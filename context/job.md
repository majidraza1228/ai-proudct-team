# Job Target: AI Infrastructure Engineer

**Status:** Pursuing — 3-month preparation window (July–September 2026)
**Target date:** Apply by end of September 2026

## Role Summary
Build and operate production infrastructure for agentic AI systems. This is not a data science or ML role — it's platform/infra engineering where the workloads happen to be LLM agents.

## Key Requirements (from job description)

### Must-have
- Agentic deployment pipelines (prompts, tools, models, context change independently)
- Kubernetes for long-running multi-step agent jobs, autoscaling, lifecycle management
- End-to-end observability: trace every step, every tool call, every input/output
- Incident response and on-call for AI workloads in production
- Security: secrets management, least-privilege permissions, prompt injection defense, data exfiltration prevention, tool sandboxing, audit trails
- AWS + infrastructure-as-code discipline
- Network/TLS/DNS/OIDC fundamentals for debugging production failures
- Strong scripting and automation

### Bonus (worth learning)
- Hands-on agent building (tool use, multi-step workflows)
- Temporal or similar for durable workflow orchestration
- LLM evaluation harnesses, guardrails, rollback strategies
- Snowflake
- Developer platforms / internal tooling

## 3-Month Learning Plan

### Month 1 (July): Foundation
- Kubernetes fundamentals for AI workloads (not just web services)
- AWS IaC with Terraform or CDK
- Agent deployment pipeline patterns (how prompts + tools + models ship independently)
- Pick one agent framework and build something real (Claude SDK or LangGraph)

### Month 2 (August): Production Depth
- Observability: OpenTelemetry for agents, distributed tracing, tool call logging
- Security: prompt injection, OIDC, secrets management (AWS Secrets Manager or Vault)
- Temporal basics — build one durable workflow
- On-call patterns: write one runbook for an agent failure scenario

### Month 3 (September): Differentiation
- Build a shared primitive (reusable agent capability or internal tool)
- Evaluation harness for a non-deterministic system
- Document something complex enough that others can follow it
- Practice explaining infrastructure decisions with tradeoff reasoning

## User Context
- Role: AI Engineer
- Stack: Python, Flask/FastAPI, Claude API / Anthropic SDK
- Familiar with LLM app building; needs to go deeper on infra (Kubernetes, AWS, observability)
- Goal: transition into AI Infrastructure Engineer at an AI-native company
