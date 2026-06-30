# Month 1 Roadmap — July 2026
# AI Infrastructure Engineer Job Prep

Order: Agent Framework → AWS IaC → Deployment Pipelines → Kubernetes (last)

---

## Area 1: Agent Framework (Week 1)

**Goal:** Build one real multi-step agent using the Claude SDK. You already know the API — this week goes deeper on tool use and production patterns.

### What to build
A research agent that:
1. Takes a question
2. Calls a web search tool
3. Calls a summarize tool
4. Returns a structured answer

This covers: tool definition, multi-step loops, tool error handling, streaming — all interview-relevant patterns.

### Step-by-step

**Day 1-2: Tool use loop**
```python
import anthropic

client = anthropic.Anthropic()

tools = [
    {
        "name": "web_search",
        "description": "Search the web for current information",
        "input_schema": {
            "type": "object",
            "properties": {
                "query": {"type": "string"}
            },
            "required": ["query"]
        }
    }
]

def run_agent(question: str) -> str:
    messages = [{"role": "user", "content": question}]
    
    while True:
        response = client.messages.create(
            model="claude-sonnet-4-6",
            max_tokens=4096,
            tools=tools,
            messages=messages
        )
        
        if response.stop_reason == "end_turn":
            return response.content[0].text
        
        if response.stop_reason == "tool_use":
            tool_use = next(b for b in response.content if b.type == "tool_use")
            result = call_tool(tool_use.name, tool_use.input)
            
            messages.append({"role": "assistant", "content": response.content})
            messages.append({
                "role": "user",
                "content": [{
                    "type": "tool_result",
                    "tool_use_id": tool_use.id,
                    "content": result
                }]
            })
```

**Day 3: Add prompt caching**
- Add `cache_control: {"type": "ephemeral"}` to your system prompt
- Log cache hit rate — this is how you control cost in production

**Day 4: Streaming**
- Switch to `client.messages.stream()` 
- Handle tool use events in the stream (different from blocking)

**Day 5: Log everything**
- Log: model, input tokens, output tokens, stop_reason, tool name, tool input, tool result, latency
- This is the foundation of observability — every production agent needs this

### What to know for the interview
- Why tool results can be a prompt injection vector (agent reads untrusted web content, executes it)
- How to cap tool call loops (max_iterations guard)
- Cost structure of multi-step agents (tokens accumulate across turns)

---

## Area 2: AWS IaC — Terraform (Week 2)

**Goal:** Provision real AWS infrastructure with Terraform. Build the VPC + ECS/Lambda setup you'll later migrate to EKS in Week 4.

### What to build
Terraform config that provisions:
- VPC with public + private subnets
- ECR repository (for your agent container)
- ECS Fargate cluster + task definition (one agent container)
- IAM role for the task with least-privilege permissions
- AWS Secrets Manager secret (API key) mounted into the container
- CloudWatch log group

### Step-by-step

**Day 1: Terraform basics**
```bash
brew install terraform
terraform init
terraform plan
terraform apply
terraform destroy
```

Learn: state files, providers, resources, variables, outputs.

**Day 2: VPC + networking**
```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"

  name = "agent-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-east-1a", "us-east-1b"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24"]

  enable_nat_gateway = true
}
```

**Day 3: IAM + Secrets Manager**
```hcl
resource "aws_iam_role" "agent_task_role" {
  name = "agent-task-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Action = "sts:AssumeRole"
      Effect = "Allow"
      Principal = {
        Service = "ecs-tasks.amazonaws.com"
      }
    }]
  })
}

# Only give the agent what it needs — nothing else
resource "aws_iam_role_policy" "agent_secrets" {
  role = aws_iam_role.agent_task_role.id
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["secretsmanager:GetSecretValue"]
      Resource = [aws_secretsmanager_secret.api_key.arn]
    }]
  })
}
```

**Day 4: ECS Fargate task**
- Run your agent container from Day 1 on Fargate
- Mount the secret as an environment variable (not hardcoded)
- CloudWatch logs for every container run

**Day 5: Practice the mental model**
Answer these without docs:
- What's the blast radius if your ECS task role is compromised?
- How do you rotate a secret without redeploying?
- Why private subnets + NAT gateway instead of public subnets?

### What to know for the interview
- Least privilege IAM: never use `*` in production Action or Resource
- How Secrets Manager differs from SSM Parameter Store (cost, rotation, audit)
- OIDC federation: GitHub Actions → AWS without long-lived access keys

---

## Area 3: Agent Deployment Pipelines (Week 3)

**Goal:** Understand and implement the core insight of this job — prompts, tools, models, and context change independently, so they need independent deployment pipelines.

### The core problem
A normal service ships code. An agent system ships:
- **Model** — which version of Claude/GPT you're calling
- **System prompt** — the agent's instructions
- **Tools** — what the agent can do
- **Context** — memory, retrieved docs, session state

Any of these can change without the others. A bad prompt change can break prod with zero code changes.

### What to build
A pipeline where each component is independently versioned and deployed:

```
your-agent-repo/
  prompts/
    system-v1.txt
    system-v2.txt       ← new prompt, not yet live
  tools/
    web_search.py       ← tool implementation
    tool_manifest.json  ← what tools are enabled (versioned separately)
  config/
    model.json          ← { "model": "claude-sonnet-4-6", "max_tokens": 4096 }
  agent.py              ← loads config at runtime, not hardcoded
```

**Day 1-2: Config-driven agent**
```python
import json

def load_agent_config():
    with open("config/model.json") as f:
        model_config = json.load(f)
    with open("prompts/system-v1.txt") as f:
        system_prompt = f.read()
    with open("tools/tool_manifest.json") as f:
        enabled_tools = json.load(f)
    return model_config, system_prompt, enabled_tools

# Agent reads config at startup — no hardcoded values
model_config, system_prompt, tools = load_agent_config()
```

**Day 3: GitHub Actions pipeline**
```yaml
# .github/workflows/deploy-prompt.yml
name: Deploy Prompt

on:
  push:
    paths:
      - 'prompts/**'   # only fires when prompts change

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Upload prompt to S3
        run: |
          aws s3 cp prompts/system-v2.txt s3://your-agent-config/prompts/
        env:
          AWS_ROLE_ARN: ${{ secrets.AWS_ROLE_ARN }}
```

**Day 4: Canary prompt rollout**
- Route 10% of traffic to system-v2, 90% to system-v1
- Log which prompt version was used for every agent run
- This is how you do safe prompt deploys — same concept as feature flags

**Day 5: Rollback**
- Write a runbook: "prompt broke prod, how do I roll back in 5 minutes"
- Answer: point config back to previous S3 key, no code deploy needed

### What to know for the interview
- How to detect a bad prompt in production (latency spike? error rate? task completion rate?)
- What a "rollback" looks like for a non-deterministic system (you can't just revert a git commit)
- Why you need to log the exact prompt version used for every inference call

---

## Area 4: Kubernetes for Agent Workloads (Week 4)

**Goal:** Run your agent from Week 1 on Kubernetes, with autoscaling and the agent-sandbox isolation pattern. Start here last because Weeks 1-3 give you the artifact to deploy.

### What to build
Take your Week 1 agent, containerize it, and run it on a local kind cluster with:
- agent-sandbox CRD for isolation
- KEDA scaling off a Redis queue
- Structured logs for every agent run

### Step-by-step

**Day 1: Local cluster setup**
```bash
brew install kind kubectl helm

# Create cluster
kind create cluster --name agent-cluster

# Install KEDA
helm repo add kedacore https://kedacore.github.io/charts
helm install keda kedacore/keda --namespace keda --create-namespace
```

**Day 2: Containerize your agent**
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "agent.py"]
```

```bash
docker build -t agent:v1 .
kind load docker-image agent:v1 --name agent-cluster
```

**Day 3: agent-sandbox CRD**
Read: https://kubernetes.io/blog/2026/03/20/running-agents-on-kubernetes-with-agent-sandbox/

Apply the CRD and run your agent inside a sandbox. Key properties:
- Network egress restrictions (agent can only call your API, not arbitrary internet)
- Resource limits (agent can't consume unbounded CPU/memory)
- Filesystem isolation (agent can't write to host)

**Day 4: KEDA autoscaling**
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: agent-scaler
spec:
  scaleTargetRef:
    name: agent-deployment
  minReplicaCount: 0        # scale to zero when idle
  maxReplicaCount: 10       # cap burst
  triggers:
  - type: redis
    metadata:
      listName: agent-jobs
      listLength: "5"       # one pod per 5 queued jobs
```

**Day 5: Debug a failure**
Deliberately break something (wrong env var, missing secret, OOM kill) and practice:
```bash
kubectl describe pod <agent-pod>
kubectl logs <agent-pod> --previous
kubectl get events --sort-by=.lastTimestamp
```

This is the on-call muscle memory the job requires.

### What to know for the interview
- Why agent jobs are different from web service pods (stateful, long-running, bursty, need isolation)
- How KEDA scale-to-zero saves cost without losing availability
- What the agent-sandbox CRD gives you that a plain Pod doesn't
- How to trace a failure: pod logs → events → resource limits → network policy

---

## Files to Save Your Work

```
AI-Product-Team/
  notes/
    scout-2026-06-30.md     ← today's scout report
    month-1-roadmap.md      ← this file
  projects/
    week1-agent/            ← Claude SDK agent with tool use
    week2-terraform/        ← AWS IaC
    week3-pipeline/         ← agent deployment pipeline
    week4-kubernetes/       ← agent on kind + KEDA
  context/
    job.md                  ← job description + 3-month plan
```

Run `/scout` at the start of each week for updates. Run `/coordinator` when you have a direction question. Run `/claude-expert`, `/openai-expert`, or `/copilot-expert` when you need to go deep on a specific platform.
