# Tim Shores — AI Engineer

I build full-stack software by **architecting and directing AI coding agents**, not hand-authoring every line. I own the system design, the security model, the verification harness, and every judgment call; the agents produce the implementation under that direction. This is the job now, and I treat it like an engineering discipline, not a party trick.

My production code is in private repos (product work — can't open-source it). What's public: the commit density above, and the write-up below of how I actually run this.

---

## Harness engineering: building the factory, not just using the tools

Parallel agents, git worktrees, hooks, reviewer agents — that's table stakes for anyone doing serious AI-assisted development now. What I actually spend my time on is a level up from that: **treating the AI-collaboration process itself as a system to measure, verify, and improve — not just prompt better.**

**Match the verification method to the claim, don't review everything the same way.** A mechanical check exists (test, lint, grep) → run it, don't trust a self-report. No mechanical check exists but it's a logic/design question → adversarial review, argued in the abstract. It's a claim about how the system *behaves over time* → replay it against real historical logs and count actual outcomes, don't just reason about whether it sounds right. Most agent setups only have the middle one, if that.

**Automation is gated by "is this actually a foregone conclusion" — with a hard rule against deciding that for myself.** The system will only auto-apply a fix when a prior written rule (or real data) already settled the answer with zero judgment left in it. It is explicitly not allowed to decide on its own that something's "obviously fine, ship it" — that's the exact failure mode it's built to prevent. Most self-improving-agent setups don't draw a hard line between "safe to automate" and "still needs a human call"; this one does, and it's enforced structurally, not by discipline.

**I backtest governance changes against real history before trusting them, the same way you'd validate a model change.** Before trusting that a new rule actually improves behavior, I mine real past sessions, apply the new rule to what actually happened, and count fixes vs. regressions — not just reason about whether the rule sounds right in the abstract. One recent change was validated against ~950 real historical sessions before I trusted it.

---

## What that harness builds

A solo-engineered B2B SaaS (freight-logistics-adjacent), early and honest about it — days old, not years:

- **Stack** — Next.js, TypeScript, Supabase/PostgreSQL, deployed on Vercel.
- **Data pipeline** — a scheduled ingestion job pulling public federal registration data via a paginated bulk API, upserted into Postgres in batches — the kind of unglamorous, has-to-actually-work-correctly plumbing real data products run on.
- Built with the same harness and discipline described above, from day one — not retrofitted later.

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
