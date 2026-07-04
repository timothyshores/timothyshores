# Agentic Engineering — Architecture Overview

> A reference write-up of how I design, direct, and ship production software using an agentic development harness. The implementation code lives in private repositories; this repo documents the architecture, the engineering rigor, and the way I work.

I'm an AI engineer who builds full-stack software by **architecting and directing AI coding agents** rather than hand-authoring every line. I own the system design, the security model, the verification harness, and the judgment calls; the agents produce the implementation under that direction. This document walks through a representative full-stack B2B SaaS I built solo this way, and the harness I use to do it.

---

## What I shipped (representative project)

A full-stack B2B SaaS, deployed to production, built end to end as the sole engineer — from data model to live deployment.

**Backend** — FastAPI (Python 3), Pydantic v2, SQLAlchemy 2.0, Alembic migrations, PostgreSQL. Deployed on Railway with a pre-deploy `alembic upgrade head` and a health-checked container.

**Frontend** — React 19, TypeScript, Vite, Tailwind, headless accessible UI primitives, React Hook Form + Zod validation. Deployed on Vercel, proxied same-origin to the backend.

**Authentication (the part I'm most deliberate about)** — this is not "a login form":
- argon2id password hashing
- short-lived JWT access tokens with refresh-token **rotation**
- **token-theft detection** via atomic compare-and-swap on the refresh token (distinguishes a stolen-and-reused token from a benign concurrent request)
- JTI-based access-token revocation (logout actually invalidates the token, not just the cookie)
- httpOnly same-origin cookie strategy, OWASP security-headers middleware, timing-attack-resistant verification

**CI/CD** — GitHub Actions on every PR and push, with branch protection:
- backend: `ruff` lint + format, `bandit` security scan, `pytest`, Alembic schema-drift check, ERD-drift check
- frontend: ESLint, `knip` dead-code report, Prettier, `vitest`, TypeScript build
- `gitleaks` secret scanning on every commit, unconditionally
- path-filtered jobs so unchanged surfaces skip

**Testing** — a test-driven suite of ~150 tests (backend `pytest` + frontend `vitest`) covering auth flows, token rotation, revocation, schema validation, security headers, and transaction isolation.

**Documented decisions** — 28 architecture decision records (ADRs) capturing the *why* behind the load-bearing choices, not just the *what*.

**Currently building a second, industry-agnostic B2B SaaS** in the review-aggregation / AI-assisted document-generation space — same harness, same verification discipline, earlier stage.

---

## How I direct the agentic harness

The interesting part isn't any single feature — it's the **system I built to ship reliably with AI agents**. Treating an LLM as a junior engineer that writes a lot of code fast only works if you build the rails that keep it honest. Mine:

**Parallel agents in isolated git worktrees.** Multiple coding agents work concurrently, each in its own worktree, so independent work streams never collide. I orchestrate the fan-out and own the merge.

**Deterministic governance via hooks.** Some steps must fire every time, not "when the model remembers." I wrap those as harness hooks (pre/post tool-call, session-start, stop) so policy is enforced mechanically — secret-scan gates, lint gates, session-close rituals — rather than relying on the model's discretion.

**Custom skills.** Repeatable procedures (design reviews, issue decomposition, close-out rituals) are codified as skills the agents invoke, so the same task is done the same way every time.

**Tool-restricted reviewer agents.** I run read-only reviewer agents (Read/Grep/Glob only — no write access) to get a fresh-context adversarial pass on work before it merges. The reviewer can't "fix" what it finds; it can only report, which keeps the review honest.

**Orchestrated MCP servers.** The harness connects several Model Context Protocol servers (issue tracking, documentation lookup, version control, knowledge base) so agents act against real systems with real context instead of guessing.

**Issue-to-model routing.** Work is matched to the cheapest model that can do it well — mechanical edits to a small model, judgment-heavy design and review to a larger one — so cost tracks the actual difficulty of the task.

---

## How I think about verification (the non-negotiable)

Directing agents only produces good software if you **never accept model output blind**. The whole point of the harness above is that velocity and quality can coexist *because* the verification is strong: types, tests, evals, end-to-end checks, fresh-context review, and contracts I own.

Concretely, I treat every agent-produced change as suspect until proven: I read the diff, run the suite, and adversarially review the load-bearing parts. The most important skill in this way of working is catching the mistakes the agent introduces — a plausible-but-wrong refactor, a test that passes for the wrong reason, a security regression that looks fine. That judgment is the engineer's job, and it doesn't transfer to the agent.

**On the AI itself:** I've prototyped LLM document-extraction (running real-world documents through a model to produce a field-level schema and an OCR-vs-semantic-extraction architectural rationale). That work is at the prototype stage — a designed approach, not yet a production pipeline — and I'm precise about that distinction, in writing and in interviews.

---

## Background

- **4.5 years at OptumRx** (one of the three largest U.S. pharmacy-benefit managers) across QA automation, a DevOps rotation, and a full-stack development rotation. Built a Python/Selenium integration-test framework that cut a QA cycle from ~2 days to 15-minute automated runs; later owned CI/CD for core PBM services on Azure/AKS ($5M+/yr infrastructure, zero customer-impacting incidents).
- **Founder** of an independent live-events company — scaled $1K → $2M+ cumulative revenue across 100+ events. I know what it means to own an outcome end to end.

---

## Let's talk

I'm looking for founding / applied-AI / full-stack engineering roles at seed–Series-B companies, remote-US.

- LinkedIn: [linkedin.com/in/timothymshores](https://linkedin.com/in/timothymshores)

*Production code is in private repositories — happy to walk through architecture, decisions, and trade-offs live.*
