# Tim Shores, AI Engineer

I design and operate agentic AI systems in production, end to end: the LLM pipeline, the evaluation gate that decides whether its output is trustworthy, the auth and data layer under it, and the deployment it runs on. Solo, and shipped.

Production code lives in private repos. This is the write-up of what I built and how.

---

## RateCon to Invoice, a production B2B AI product

Freight brokers send rate confirmations as PDFs. The carrier retypes them into an invoice by hand, several times a week, daily on short loads. I ran discovery with a working owner-operator, scoped that manual step into a product, and took it zero to one. Live on Railway and Vercel.

**The extraction pipeline.** Documents reach the system two ways: a manual upload endpoint and an inbound-email webhook with Svix signature verification. Both funnel into an LLM extraction stage on the Anthropic Claude API using structured output, landing typed freight fields (load number, rate, lane, dates) into Postgres. Email ingestion means the product meets carriers where the work already happens, in their inbox, rather than asking them to change behavior.

**The evaluation harness, and why it is the real engineering.** Extraction accuracy is scored against a hand-labeled corpus with tiered targets: **100% on money-path fields (load number, total rate), 85-90% on everything else.** That split is the whole design. Getting an LLM approximately right is easy; deciding which fields are *not allowed* to be approximately right, and building the gate that enforces it, is the job. It runs as a human-reviewed release gate. I deliberately kept live-API accuracy scoring out of CI, because a nondeterministic check does not belong in a blocking pipeline, and a flaky gate is a gate people learn to ignore.

**Production concerns, not prototype concerns.** argon2id password hashing, JWT rotation with atomic token-theft detection and family-wide revocation, a constant-time decoy hash that defeats user-enumeration timing attacks, OWASP-hardened headers. CI/CD on GitHub Actions with schema-drift and secret-scanning gates.

**Stack.** Python, FastAPI, Pydantic, SQLAlchemy 2.0, Alembic, PostgreSQL 18 on the backend. React 19, TypeScript, TanStack Router, Vite, Tailwind CSS 4, shadcn/ui on the front end. Railway, Vercel, Docker, GitHub Actions for infra.

**Verification.** 721 tests (627 pytest, 94 Vitest) against 10.2K lines of production code, a 1.33:1 test-to-code ratio, across 33 architecture decision records.

---

## Multi-agent development workflow

The product is built by a fleet of coding agents I designed and operate, not by hand. This is how I build, not a feature of the product itself.

**The architecture.** Parallel Claude Code agents run in isolated git worktrees so concurrent work cannot corrupt a shared tree. An issue-to-model classifier routes each unit of work to the right capability tier, so cheap mechanical work does not burn expensive-model budget. Custom skills encode repeatable procedures; lifecycle hooks enforce deterministic guardrails that prose instructions alone demonstrably fail to hold. **Seven orchestrated MCP servers** connect the fleet to issue tracking, version control, deployment, docs, and knowledge base.

**Matching the verification method to the claim.** A mechanical check exists (test, lint, grep) → run it, never trust a self-report. No mechanical check but it is a logic or design question → adversarial review by a fresh-context agent, because the agent that wrote something self-grades toward "pass." A claim about how the system *behaves over time* → replay it against real historical logs and count outcomes. Most setups only have the middle one.

**Gating automation on whether the answer is genuinely settled.** The system auto-applies a change only when a prior written rule or real data already decided it, with zero judgment remaining. It is structurally not permitted to conclude on its own that something is "obviously fine, ship it," which is the precise failure mode that makes self-improving agent systems compound errors.

**Backtesting governance changes against real history.** Before trusting that a new rule improves agent behavior, I mine real past sessions, apply the rule to what actually happened, and count fixes against regressions. One recent change was validated against roughly 950 historical sessions before I adopted it. Same discipline as validating a model change: measure against the real distribution, do not reason about it in the abstract.

---

## What I have not built

Stated plainly, because being caught inflating a claim costs more than volunteering a limit.

No RAG or vector store in this product. Extraction is structured-output against a known schema, not retrieval, so a vector layer would have been architecture theater. No payments integration and no human review/correction UI. The eval harness is a release gate I review personally, not a CI gate.

---

## Background

**4.5 years at OptumRx**, one of the three largest U.S. pharmacy-benefit managers, across full-stack development, QA automation, and DevOps. Built a Python/Selenium integration-test framework that cut a QA cycle from 2 days to a 15-minute automated run, with test patterns adopted across multiple Agile teams. Later owned CI/CD for core PBM services on Azure/AKS, $5M+/yr infrastructure, zero customer-impacting incidents. Ran two buy-side B2B SaaS evaluations end to end: vendor SE demos, business case, leadership recommendation, POC approval. Regulated enterprise environment, where being wrong has consequences.

**Founded and ran an independent live-events business** for six years, zero to one, $0 to $2M+ cumulative revenue across 100+ ticketed events, up to 3,000 attendees. Contracted and managed vendors across 6+ categories (AV, staging, lighting, sound, security, front-of-house) against dates that could not slip. Sole proprietor, accountable for the whole outcome.

---

## Let's talk

AI Engineer, Applied AI, Forward Deployed, or Founding Engineer roles at seed to Series B companies. Salt Lake City metro or remote US.

- LinkedIn: [linkedin.com/in/timothymshores](https://linkedin.com/in/timothymshores)
- Email: timothymshores@gmail.com

*Production code is in private repositories. Happy to walk through architecture, decisions, and trade-offs live.*
