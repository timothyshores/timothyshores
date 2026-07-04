# Tim Shores — AI Engineer

I build full-stack software by **architecting and directing AI coding agents**, not hand-authoring every line. I own the system design, the security model, the verification harness, and every judgment call; the agents produce the implementation under that direction. This is the job now, and I treat it like an engineering discipline, not a party trick.

My production code is in private repos (client/product work — can't open-source it). What's public: the commit density above, my open-source activity on **Anthropic's own `claude-code` repo**, and the write-up below of how I actually run this.

---

## How I direct the agentic harness

The interesting part isn't a single feature — it's the **system I built to ship reliably with AI agents**. Treating an LLM as a fast junior engineer only works if you build the rails that keep it honest.

**Parallel agents in isolated git worktrees.** Multiple coding agents work concurrently, each in its own worktree, so independent work streams never collide. I orchestrate the fan-out and own the merge.

**Deterministic governance via hooks.** Some steps must fire every time, not "when the model remembers." I wrap those as harness hooks (pre/post tool-call, session-start, stop) so policy is enforced mechanically — secret-scan gates, lint gates, session-close rituals — instead of relying on the model's discretion.

**Tool-restricted reviewer agents.** I run read-only reviewer agents (Read/Grep/Glob only, no write access) for a fresh-context adversarial pass before anything merges. The reviewer can't "fix" what it finds — it can only report, which keeps the review honest.

**Custom skills.** Repeatable procedures (design reviews, issue decomposition, close-out rituals) are codified as skills the agents invoke, so the same task is done the same way every time.

**Issue-to-model routing.** Work is matched to the cheapest model that can do it well — mechanical edits to a small model, judgment-heavy design and review to a larger one — so cost tracks actual task difficulty.

**The non-negotiable: never accept model output blind.** Every agent-produced change is suspect until proven — I read the diff, run the suite, and adversarially review the load-bearing parts. The actual skill here is catching the mistakes the agent introduces: a plausible-but-wrong refactor, a test that passes for the wrong reason, a security regression that looks fine. That judgment doesn't transfer to the agent. It's the job.

---

## What that harness builds

A solo-engineered B2B SaaS (freight-logistics-adjacent), currently in active development, data model through CI/CD:

- **Backend** — FastAPI, Pydantic v2, SQLAlchemy 2.0, Alembic migrations, PostgreSQL.
- **Frontend** — React 19, TypeScript, Vite, Tailwind, React Hook Form + Zod.
- **Auth, done properly, not "a login form"** — argon2id hashing, JWT access tokens with refresh-token rotation, token-theft detection via atomic compare-and-swap, JTI-based revocation, httpOnly same-origin cookies, OWASP security headers.
- **CI/CD** — GitHub Actions with branch protection: lint/format/security-scan/test on every push, schema-drift and ERD-drift checks, `gitleaks` on every commit, ~150 tests across backend and frontend.
- **28 ADRs** documenting the *why* behind the load-bearing decisions, not just the *what*.

Also building a second, industry-agnostic project in the AI-assisted document-generation space — same harness, same discipline, earlier stage. I've prototyped LLM document-extraction (field-level schema generation, OCR-vs-semantic-extraction trade-offs) — that's at the prototype stage and I'm precise about that distinction, in writing and in interviews.

---

## Background

- **4.5 years at OptumRx** (one of the three largest U.S. pharmacy-benefit managers), across full-stack development, QA automation, and DevOps. Built a Python/Selenium integration-test framework that cut a QA cycle from ~2 days to a 15-minute automated run; later owned CI/CD for core PBM services on Azure/AKS ($5M+/yr infrastructure, zero customer-impacting incidents). Shipped in a regulated, enterprise environment where mistakes have real consequences.
- **Founded and ran an independent live-events company** for six years — scaled it from a standing start to $2M+ in cumulative revenue across 100+ produced events, as the person accountable for the whole outcome: budget, staffing, vendors, the show going right on the night it mattered. That's where I learned what ownership actually costs.

---

## Let's talk

Looking for founding / applied-AI / full-stack engineering roles at seed–Series-B companies, remote-US.

- LinkedIn: [linkedin.com/in/timothymshores](https://linkedin.com/in/timothymshores)

*Production code is in private repositories — happy to walk through architecture, decisions, and trade-offs live.*
