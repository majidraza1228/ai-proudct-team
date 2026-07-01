# Syed AI Agent Report — July 1, 2026

**Month 1, Day 1. Build the foundation artifact: Claude SDK agent + OTel + AWS IaC + Kubernetes.**

---

## What's New

**Claude Sonnet 5 — Deep Dive (released June 30, 2026)**

### What it is
Sonnet 5 is Anthropic's new mid-tier model, positioned as the workhorse for agentic workloads. It launched June 30, the day before Month 1 starts. Model ID: `claude-sonnet-5-20260630` (check platform docs for canonical ID).

### Context window and output
- **1M-token context window** — 8× larger than Sonnet 4.6's 128K. For a long-running Month 1 agent that accumulates tool call results, conversation history, and retrieved documents, this means you won't need to implement sliding window or summarization logic in Week 1. You can load the full conversation and let the model reason across all of it.
- **128K max output per response** — relevant when the agent writes large artifacts (code files, reports). You won't hit this in normal agentic loops, but know the limit.

### Pricing
| Model | Input | Output | Cached input |
|---|---|---|---|
| Sonnet 5 (promo, through Aug 31) | $2/Mtok | $10/Mtok | $0.20/Mtok |
| Sonnet 5 (after Aug 31) | $3/Mtok | $15/Mtok | $0.30/Mtok |
| Sonnet 4.6 | $3/Mtok | $15/Mtok | $0.30/Mtok |
| Opus 4.8 | $5/Mtok | $25/Mtok | $0.50/Mtok |

The promo period covers all of Month 1 (July). Sonnet 5 after August 31 costs the same as Sonnet 4.6 but performs significantly better on agentic tasks — so there's no reason to use Sonnet 4.6 after the promo ends either.

### Benchmark comparisons (why this matters for the job)
| Benchmark | Sonnet 5 | Sonnet 4.6 | Opus 4.8 |
|---|---|---|---|
| SWE-bench Pro (agentic coding) | **63.2%** | 58.1% | 69.2% |
| OSWorld-Verified (computer use) | **81.2%** | 78.5% | — |
| Terminal-Bench 2.1 (terminal use) | **80.4%** | 67.0% | — |
| Humanity's Last Exam (with tools) | **57.4%** | — | 57.9% |

The Terminal-Bench jump (+13.4 points over Sonnet 4.6) is the most relevant for this role — terminal use maps directly to K8s operations, shell tool calls, and debugging agents in production. The near-parity with Opus 4.8 on knowledge work (57.4 vs 57.9) at less than half the price is the cost-performance story to use in interviews.

### Capabilities included
All of the following are supported out of the box: tool calling, extended reasoning (thinking), prompt caching, vision input, web search, structured outputs (JSON schema), computer use, streaming.

### What "agentic reliability" means in practice
Anthropic explicitly frames Sonnet 5 around two things previous Sonnets struggled with:
1. **Longer task chains without losing context** — prior Sonnets would drift or forget earlier tool results in chains of 10+ steps. Sonnet 5 maintains coherence across longer sequences.
2. **Better self-correction when a tool call fails** — rather than getting stuck or hallucinating a result, Sonnet 5 is more likely to retry with adjusted parameters or surface the error cleanly.

Both of these matter for Month 1: you're building an agent that will make multiple tool calls in sequence (read file → process → write output → verify). Self-correction on tool failure is the behavior that makes the difference between a demo agent and a production-grade one.

### How to use it in the SDK (Python)
```python
import anthropic

client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-sonnet-5-20260630",
    max_tokens=8096,
    system=[
        {
            "type": "text",
            "text": "You are an AI infrastructure agent...",
            "cache_control": {"type": "ephemeral"}  # cache the system prompt
        }
    ],
    messages=[{"role": "user", "content": user_message}],
    tools=tools
)
```

Set `cache_control` on the system prompt block immediately — this is the single highest-ROI change you can make. With the promo pricing, cached input costs $0.20/Mtok instead of $2.00/Mtok (90% off).

### When to use Opus 4.8 instead
Opus 4.8 still leads on SWE-bench Pro (69.2% vs 63.2%). Use it when:
- The agent task requires complex multi-step reasoning where correctness matters more than cost (e.g., a one-shot architectural decision)
- You're doing extended thinking with a very long reasoning budget

For the Month 1 build, Sonnet 5 is the right choice for the agentic loop itself. Opus can be the "review" or "planning" step if you build a multi-model pipeline.

### Sources
- [Anthropic Claude Sonnet 5 launch](https://www.anthropic.com/news/claude-sonnet-5)
- [Sonnet 5 vs Sonnet 4.6 vs Opus 4.8 benchmarks — MarkTechPost](https://www.marktechpost.com/2026/06/30/anthropic-claude-sonnet-5-vs-sonnet-4-6-vs-opus-4-8-agentic-coding-benchmarks-api-pricing-and-cost-performance-tradeoffs-compared/)
- [Platform docs — models overview](https://platform.claude.com/docs/en/about-claude/models/overview)
- [TechCrunch — Sonnet 5 for agents](https://techcrunch.com/2026/06/30/anthropic-launches-claude-sonnet-5-as-a-cheaper-way-to-run-agents/)

**Programmatic Tool Calling + Tool Search Tool (Anthropic SDK)**
- What changed: Claude can now invoke tools inside a code execution environment (REPL state persists across calls). Tool Search Tool lets Claude select from thousands of tools at runtime without loading all tool schemas into the context window.
- Why it matters: Programmatic Tool Calling maps directly to the job requirement for agentic deployment pipelines — tools can version independently of the prompt. Demo this in interviews as your tool architecture pattern.
- SDK min version: `code_execution_20260120` — available in Python, TypeScript, Go, Java, Ruby, PHP, C#.

**Sub-agents now spawn sub-agents up to 5 levels deep (Managed Agents)**
- What changed: Claude Managed Agents supports sub-agent hierarchies up to 5 levels. You can also update MCP server and tool configs for an active agent session without restarting it.
- Why it matters: Lifecycle management for hierarchical agent workloads is an explicit job requirement. Know the depth limit and session update pattern — this comes up in system design interviews.

**AWS AI on EKS blueprints + MCP server for EKS**
- What changed: AWS released production-ready IaC blueprints for agentic AI on EKS (Terraform variable files per environment, reference architectures for multi-model serving and agentic pipelines). AWS Labs also shipped an MCP server for EKS.
- Why it matters: Don't design your Week 2 Terraform from scratch — start from these blueprints. The MCP server for EKS is worth knowing as a deployment debugging tool.
- Read: https://aws.amazon.com/blogs/containers/introducing-ai-on-eks-powering-scalable-ai-workloads-with-amazon-eks/

**OTel GenAI semantic conventions are now stable**
- What changed: The OpenTelemetry GenAI semantic conventions (for LLM calls, tool invocations, agent steps) landed as stable in early 2026. Auto-instrumentation packages exist for Anthropic, OpenAI, LangChain, and LlamaIndex. Datadog, Honeycomb, and New Relic all support GenAI conventions natively.
- Why it matters: Install `opentelemetry-instrumentation-anthropic` in Week 1. Wire to local Jaeger or OTEL Collector. Real trace data from day one beats explaining it in Month 2.
- Key attributes to know: `gen_ai.request.model`, `gen_ai.usage.input_tokens`, `gen_ai.usage.output_tokens`, `gen_ai.response.finish_reasons`, tool span structure.
- Read: https://opentelemetry.io/blog/2026/genai-observability/

**Prompt caching: 90% discount now standard across Claude, OpenAI, and Gemini**
- What changed: All three providers discount cached input by 90% as of mid-2026. Cache hit rate is now the correct cost metric — not token count. Claude Code itself reads 92.7% of its prompt from cache.
- Why it matters: Structure your Month 1 agent with a stable system prompt prefix (tools, instructions, context) that rides at the cached rate. This is a $0 cost optimization with a 1-hour implementation. FDE interviewers now ask about caching architecture by default.
- Read: https://galileo.ai/blog/the-2026-caching-playbook-for-agents-bigger-prompts-smaller-bills

**Evals engineering is now the #1 reason candidates fail FDE final rounds**
- What changed: At OpenAI and Anthropic, FDE candidates who can't whiteboard a regression eval suite (golden dataset, drift detection, tracked failure modes) are failing final rounds. FDE job listings are up 800%.
- Why it matters: This is Month 3 work, but plant the seed now — every agent you build in Month 1 should have at least a minimal golden dataset you can test against. Start the habit.

**EU AI Act Article 15 deadline: August 2, 2026**
- What changed: GPAI provider obligations under EU AI Act Article 15 kick in August 2, 2026 — cybersecurity requirements for high-risk AI systems (resilience against prompt injection, adversarial inputs, unauthorized alteration).
- Why it matters: FDE customers in financial services and healthcare will ask about this in Month 2 onward. Know the obligation exists and that prompt injection defense is explicitly named in the law.

**Prompt injection: OWASP LLM01 for 3rd year running; gateway-layer defense is the pattern**
- What changed: Prompt injection attacks up 340% YoY. 73% of production agent deployments remain vulnerable. Best practice in 2026: gateway-layer control point (all requests pass through, credentials live in the gateway, audit trail is uniform).
- Why it matters: Job explicitly requires prompt injection defense. The gateway pattern maps to the least-privilege requirement — build it into your Month 1 agent architecture from the start.

---

## Direction for Month 1

**This week (Week 1 — July 1–6):**
1. Start the Claude SDK agent with Sonnet 5. Use `code_execution_20260120` for tool calling.
2. Add `opentelemetry-instrumentation-anthropic` on day one — wire to local Jaeger. Log every tool call as a child span.
3. Implement prompt caching immediately: structure system prompt as a stable prefix. Target >80% cache hit rate from the first week.
4. Add basic prompt injection defense: validate inputs, define output schema, log tool calls.

**Week 2 preview (AWS IaC):**
Read the AWS AI on EKS blueprints before writing a single line of Terraform. The per-environment Terraform variable file pattern they use is interview-ready.

**Skip this week:**
- Sub-agent hierarchies (Month 2 complexity)
- KEDA (Week 3 Kubernetes work)
- Evals harness (Month 3)

---

## Flags

- **Cache hit rate > token count** — restructure how you think about cost from day one. System prompt should be static and cacheable. Dynamic content goes at the end.
- **Evals is a Month 1 habit, not a Month 3 task** — every agent you build should have a small golden dataset. FDE final rounds will test this.
- **EU AI Act August 2 deadline** — prompt injection defense in your Month 1 agent is now also a compliance talking point, not just a security pattern.
- **AWS MCP server for EKS** — file this for Month 2 when you're debugging agent deployments on EKS. It lets the agent query and troubleshoot the cluster directly.
