# Active Job Targets

**Currently pursuing:** AI Infrastructure Engineer + Forward Deployed Engineer (FDE)
**Full details:** See `context/jobs/ai-infra-engineer.md` and `context/jobs/fde.md`

---

## How to switch jobs

1. Save current job: `cp context/active-job.md context/jobs/[role-name].md`
2. Replace this file content with the new job description + 3-month plan
3. All skills (`/coordinator`, `/claude-expert`, `/openai-expert`, `/copilot-expert`, `/syed-ai-agent`) and the Friday routine pick it up automatically

---

## Role 1: AI Infrastructure Engineer

**Status:** Pursuing — 3-month prep window (July–September 2026)
**Apply by:** End of September 2026
**Companies:** AI-native companies, infrastructure-first startups

**Must-have for the role:**
- Kubernetes for long-running agent jobs, autoscaling, lifecycle management
- Agentic deployment pipelines (prompts, tools, models version independently)
- End-to-end observability — trace every step, every tool call
- Security: least privilege, prompt injection defense, secrets, audit trails
- AWS + infrastructure-as-code
- Incident response and on-call for AI workloads

**3-month plan:**
- Month 1 (July): Claude SDK agent → AWS IaC → deployment pipelines → Kubernetes
- Month 2 (August): Observability → security → Temporal → runbook
- Month 3 (September): Shared primitive → eval harness → documentation → interview prep

---

## Role 2: Forward Deployed Engineer (FDE)

**Status:** Pursuing — same 3-month window (July–September 2026)
**Apply by:** End of September 2026
**Companies:** Palantir, Scale AI, OpenAI, Anthropic, Sierra, Harvey, Glean, enterprise AI companies

**What FDE companies want:**
- Deploy AI products directly into enterprise customer environments
- Translate customer workflows into agent/LLM solutions — fast (days not weeks)
- Own the technical relationship: integrate, debug, and extend on-site
- Ship working prototypes under real constraints (VPCs, enterprise IT, compliance)
- Explain AI tradeoffs to non-technical stakeholders (CISOs, ops teams, executives)
- Strong breadth: Claude/OpenAI/Azure APIs, scripting, data pipelines, some infra

**Must-have for FDE:**
- Claude + OpenAI API fluency — build agents fast from scratch
- Microsoft/Azure stack — enterprise customers often run on Azure
- Ability to prototype quickly: RAG pipeline, document Q&A, workflow automation
- Communication: translate prompt injection risk, model tradeoffs, cost to non-engineers
- Security mindset: VPC, data residency, private endpoints, no-data-egress constraints
- Customer-facing delivery: scoping, building, demoing, training

**Overlapping skills (builds both targets simultaneously):**
- Claude/OpenAI/Azure API depth — required for both
- Security (prompt injection, secrets, least privilege) — required for both
- Agent architecture patterns — required for both
- Token optimization and cost engineering — required for both
- Kubernetes + AWS — strong differentiator for FDE (enterprise infra knowledge)
- Observability — differentiator for FDE (debugging agents in customer environments)

---

## Current month: Month 1 (July)

Focus: Build the foundation artifact — a Claude SDK agent with tool use, deployed to AWS, observable from day one.

This artifact serves BOTH targets:
- **AI Infra** — shows you can build and deploy agents on real infrastructure
- **FDE** — shows you can build a working agent quickly and understand how it behaves in production
