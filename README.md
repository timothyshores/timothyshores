# Tim Shores, AI Engineer

I architect and ship production AI systems end to end, directing agentic coding harnesses rather than hand-authoring every line. I own the system design, the security model, the verification harness, and every judgment call; the agents produce the implementation under that direction.

Production code lives in private repos (product work). What's public is the write-up below of how I actually build.

---

## What I'm building: RateCon to Invoice

A B2B AI SaaS for freight carriers, solo, live on Railway and Vercel. A broker sends a rate confirmation as a PDF and the carrier retypes it into an invoice by hand, several times a week. I ran discovery with a working owner-operator, scoped that manual step into a product, and took it zero to one.

**LLM document-extraction pipeline (Anthropic Claude API), live in production**, reached by two paths: a manual upload endpoint and a Resend inbound-email webhook with Svix signature verification, pulling freight-document fields through the structured-output API into typed persistence.

**An evaluation harness is what makes it trustworthy.** Extraction is scored against a hand-labeled corpus with tiered targets: 100% on money-path fields (load number, total rate) and 85-90% elsewhere, run as a human-reviewed release gate. Getting an LLM approximately right is easy. Deciding which fields are *not allowed* to be approximately right is the engineering.

**Senior-grade auth**: argon2id hashing, JWT rotation with atomic token-theft detection and family-wide revocation, a constant-time decoy hash defeating user-enumeration timing attacks, OWASP-hardened headers.

**721 tests (627 backend pytest, 94 frontend Vitest) against 9.6K lines of production code, a 1.3:1 test-to-code ratio**, plus CI/CD on GitHub Actions with schema-drift and secret-scanning gates, and 33 architecture decision records across 335 commits.

**Stack** , Backend: Python, FastAPI, Pydantic, SQLAlchemy 2.0, Alembic, PostgreSQL 18. Frontend: React 19, TypeScript, TanStack Router, Vite, Tailwind CSS 4, shadcn/ui. Infra: Railway, Vercel, Docker, GitHub Actions.

**What I have not built**, and say so plainly in interviews: no RAG or vector store in this product (extraction is structured-output, not retrieval), no payments integration, no human review/correction UI. The eval harness is a release gate I review, deliberately not a CI gate, because live-API accuracy scoring does not belong in a blocking pipeline.

---

## Harness engineering: building the factory, not just using the tools

Parallel agents, git worktrees, hooks, reviewer agents are table stakes now. What I actually spend time on is a level up: treating the AI-collaboration process itself as a system to measure, verify, and improve.

**Match the verification method to the claim.** A mechanical check exists (test, lint, grep) → run it, don't trust a self-report. No mechanical check but it's a logic or design question → adversarial review by a fresh-context agent. A claim about how the system *behaves over time* → replay it against real historical logs and count actual outcomes. Most agent setups only have the middle one, if that.

**Automation is gated on whether something is genuinely a foregone conclusion, with a hard rule against deciding that for myself.** The system auto-applies a fix only when a prior written rule or real data already settled the answer with zero judgment left. It is explicitly not allowed to conclude on its own that something is "obviously fine, ship it," which is the exact failure mode it exists to prevent.

**I backtest governance changes against real history before trusting them.** Before believing a new rule improves behavior, I mine real past sessions, apply the rule to what actually happened, and count fixes against regressions. One recent change was validated against roughly 950 historical sessions before I trusted it.

**Seven orchestrated MCP servers** spanning issue tracking, version control, docs, and knowledge base, with custom skills and lifecycle hooks enforcing deterministic governance across parallel agents in isolated git worktrees.

---

## Background

**4.5 years at OptumRx**, one of the three largest U.S. pharmacy-benefit managers, across full-stack development, QA automation, and DevOps. Built a Python/Selenium integration-test framework that cut a QA cycle from 2 days to a 15-minute automated run. Later owned CI/CD for core PBM services on Azure/AKS, $5M+/yr infrastructure, zero customer-impacting incidents. Also ran two buy-side B2B SaaS evaluations end to end: vendor SE demos, business case, leadership recommendation, POC approval.

**Founded and ran an independent live-events business** for six years, zero to one, $0 to $2M+ cumulative revenue across 100+ ticketed events, up to 3,000 attendees. Contracted and managed vendors across 6+ categories (AV, staging, lighting, sound, security, front-of-house) against dates that could not slip. That is where I learned what owning the whole outcome actually costs.

---

## Let's talk

Looking for AI Engineer, Applied AI, Forward Deployed, or Founding Engineer roles at seed to Series B companies. Salt Lake City metro or remote US.

- LinkedIn: [linkedin.com/in/timothymshores](https://linkedin.com/in/timothymshores)
- Email: timothymshores@gmail.com

*Production code is in private repositories. Happy to walk through architecture, decisions, and trade-offs live.*
