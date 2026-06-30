# Syed AI Agent Report — June 30, 2026

## What's New

**OTel auto-instrumentation for Anthropic SDK**
- What changed: Auto-instrumentation packages now exist for the Anthropic SDK, emitting OTel-compliant spans for every LLM call, tool invocation, and agent step with no manual code.
- Why it matters: Add to Week 1 agent build now — instrument from day one instead of retrofitting in Month 2. The job requires end-to-end trace visibility.
- Read: https://opentelemetry.io/blog/2026/genai-observability/

**AgentCore VPC egress + private EKS integration**
- What changed: Bedrock AgentCore Gateway now supports secure VPC egress, letting agents call private EKS-hosted MCP servers without public exposure.
- Why it matters: AWS-native answer to the job's "secrets and permissioning model" requirement — know this before Week 2 Terraform work.
- Read: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/release-notes.html

**AgentCore A/B testing across EKS/Lambda/non-AWS**
- What changed: AgentCore splits live production traffic between agent versions regardless of where they run (EKS, Lambda, or off-AWS).
- Why it matters: Maps to "rollback strategies for non-deterministic systems" bonus requirement. The canary pattern from month-1-roadmap.md Week 3 is now AWS-managed.
- Read: https://aws.amazon.com/blogs/machine-learning/build-and-deploy-scalable-ai-agents-with-nvidia-nemo-amazon-bedrock-agentcore-and-strands-agents/

**Bedrock Guardrails in AgentCore for prompt injection**
- What changed: Guardrails now evaluates both agent outputs and gateway inputs for prompt injection, harmful content, and sensitive data.
- Why it matters: Job explicitly requires prompt injection defense — reference this architecture in interviews.
- Read: https://aws.amazon.com/blogs/aws/aws-weekly-roundup-ny-summit-recap-local-zone-in-hanoi-grok-4-3-in-bedrock-price-reductions-and-more-june-22-2026/

**Claude Code background agent auto-resume**
- What changed: Background Claude Code workers killed by daemon restart now automatically resume.
- Why it matters: Maps to "lifecycle management these workloads demand" — the production ops behavior to build for K8s agent workloads.
- Read: https://releasebot.io/updates/anthropic/claude-code

## Direction for This Month

**This week (Week 1):** Add OTel instrumentation from day one. Install `opentelemetry-instrumentation-anthropic`, wire to local Jaeger or OTEL Collector, log every tool call. One week of real trace data beats explaining the concept in an interview.

**Week 2 preview:** Before writing Terraform, read the AgentCore VPC egress architecture. Design your VPC to match: agents in private subnets, tools/MCP accessible via gateway, no direct internet exposure.

**Skip this week:** AgentCore Managed Knowledge Base and Web Search — RAG infrastructure is not on the job requirements list.

## Flags

- **OTel is table stakes for this role** — Datadog, Honeycomb, New Relic all support GenAI semantic conventions. Know `gen_ai.usage.input_tokens`, `gen_ai.response.finish_reasons`, and tool span structure before any interview.
- **AgentCore → EKS is the AWS interview story** — AgentCore Identity + VPC Gateway + EKS lets you answer "how would you secure agents in production on AWS" with a concrete architecture.
