# AI Engineer Squad

A personal AI team built in Claude Code to help pursue an **AI Infrastructure Engineer** role in 3 months (July–September 2026).

Each skill represents a specialist on the team — one per major AI platform, plus a coordinator and a live research agent.

---

## The Team

| Skill | Specialist | What they cover |
|-------|-----------|-----------------|
| `/coordinator` | Team Lead | Synthesizes all platforms into one unified answer. Start here for direction questions. |
| `/claude-expert` | Anthropic | Claude API, tool use, MCP, agent orchestration, production deployment of Claude agents |
| `/openai-expert` | OpenAI | GPT-4o, Assistants API, Codex, Agents SDK, function calling, production patterns |
| `/copilot-expert` | Microsoft / GitHub | GitHub Copilot, GitHub Actions, Azure, AKS, Microsoft identity (OIDC, Entra) |
| `/scout` | Research | Live web search across all platforms + Kubernetes/AWS, filtered to the job goal |

---

## Repo Structure

```
AI-Product-Team/
├── CLAUDE.md                        # Claude Code project config
├── README.md                        # this file
├── context/
│   └── job.md                       # job description + 3-month plan (all skills read this)
├── notes/
│   ├── scout-2026-06-30.md          # scout report: what's new, what to focus on
│   └── month-1-roadmap.md           # week-by-week instructions for July
└── .claude/
    └── skills/
        ├── coordinator/SKILL.md
        ├── claude-expert/SKILL.md
        ├── openai-expert/SKILL.md
        ├── copilot-expert/SKILL.md
        └── scout/SKILL.md
```

---

## How to Use

### Setup

1. Clone the repo
   ```bash
   git clone https://github.com/majidraza1228/ai-proudct-team.git
   cd ai-proudct-team
   ```

2. Copy skills to your global Claude Code skills folder
   ```bash
   cp -r .claude/skills/* ~/.claude/skills/
   ```

3. Open Claude Code from this directory
   ```bash
   claude
   ```

4. Skills are now available as `/coordinator`, `/claude-expert`, `/openai-expert`, `/copilot-expert`, `/scout`

### Daily workflow

| What you want | Command |
|--------------|---------|
| Direction for the week | `/scout` |
| Cross-platform question | `/coordinator` |
| Deep dive on Claude/Anthropic | `/claude-expert` |
| Deep dive on OpenAI | `/openai-expert` |
| GitHub Actions, Azure, AKS | `/copilot-expert` |

---

## 3-Month Plan

### Month 1 — July: Foundation
| Week | Focus |
|------|-------|
| 1 | Claude SDK agent with tool use, streaming, structured logging |
| 2 | AWS IaC with Terraform (VPC, ECS, IAM, Secrets Manager) |
| 3 | Agent deployment pipelines — prompts, tools, models versioned independently |
| 4 | Kubernetes — kind cluster, agent-sandbox CRD, KEDA autoscaling |

### Month 2 — August: Production Depth
- OpenTelemetry for agents — trace every step, every tool call
- Security: prompt injection defense, OIDC, secrets management
- Temporal — build one durable workflow
- Write one incident runbook for an agent failure scenario

### Month 3 — September: Differentiation
- Build a shared primitive (reusable agent capability or internal tool)
- Evaluation harness for a non-deterministic system
- Document something complex enough that others can follow it
- Practice explaining infrastructure decisions with tradeoff reasoning

---

## Job Target

**Role:** AI Infrastructure Engineer at an AI-native company

**Core requirements:**
- Agentic deployment pipelines
- Kubernetes for long-running agent workloads, autoscaling, lifecycle management
- End-to-end observability (every step, every tool call)
- Security: least privilege, prompt injection defense, audit trails
- AWS + infrastructure-as-code
- Incident response and on-call for AI workloads

See `context/job.md` for the full job description and gap analysis.

---

## Notes

Scout reports are saved to `notes/` after each `/scout` run. Week-by-week instructions are in `notes/month-1-roadmap.md`. These persist across Claude sessions — nothing is lost when you close the chat.
