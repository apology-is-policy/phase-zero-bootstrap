# phase-zero-bootstrap

A Claude Code skill that bootstraps a new low-level systems project (kernel, filesystem, scheduler, driver, network stack, crypto library, distributed protocol, anything where correctness is load-bearing) with a Phase 0 design-first framework.

The skill is a single `SKILL.md`. Drop it into `~/.claude/skills/phase-zero-bootstrap/` (or your project's skills directory), then invoke it at the start of a new project. The skill conducts a structured design interview, produces a binding architecture, and scaffolds the operational artifacts that keep every subsequent session disciplined.

This is not a template generator. It is an **operational discipline contract**. What it produces — the documents, the gates, the per-chunk rituals, the memory layout — is the contract under which the model will operate on the project for its entire lifetime.

---

## What the skill is for

Ambitious low-level projects fail in a small number of recognizable ways:

- A foundational decision (concurrency model, on-disk format, fault model) gets made implicitly during early implementation, and by the time anyone notices, thousands of lines depend on it.
- A "we'll add tests / specs / audits later" intention never converts, because the project never has a less busy moment than today.
- Sessions lose state across context compactions, so the next instance of the model re-derives context that the previous instance already had — and silently drops invariants that lived only in the conversation log.
- Audit findings get lost across session boundaries. The same bug class comes back three rounds later.
- The architecture document drifts away from the code, the code drifts away from the spec, the spec is never written. Everyone is busy; nobody is aligned.

The Phase Zero Bootstrap is the antidote. It refuses to let the project start until the foundational decisions are written down, reviewed, signed off, and converted into operational machinery that every future session is bound by.

It is opinionated. The opinions exist because each one represents a class of failure that has happened repeatedly on real projects.

---

## The flow at a glance

```
Phase 1: Vision interview + VISION.md
    + GATE 1: Vision signoff
Phase 2: COMPARISON.md + NOVEL.md
    + GATE 2: Novel angles signoff
Phase 3: ARCHITECTURE.md + ROADMAP.md
    + GATE 3: Scripture signoff (the binding commitment)
Phase 4: CLAUDE.md + repository scaffolding (specs/, docs/, memory/)
Phase 5: Handoff — summary, next-steps, fresh session briefing
```

Five phases, three gates. The gates require **explicit user signoff** — the model has no authority to cross them. Autonomy granted later does not extend backwards through a gate.

Implementation begins in a separate session after Phase 5, armed with the scaffolding this bootstrap built.

---

## Section-by-section walkthrough

### Phase 1 — Vision interview

The model conducts an open-ended interview to understand the user's vision well enough to explain it back with the same nuance. It probes ambiguity, pushes back on too-easy answers, and surfaces hard tradeoffs.

The question bank covers:

- **Mission + scope** — what is being built, why, for whom, what success looks like 2–3 years out.
- **Workload + scale** — concrete target workloads, scale targets, latency budgets, throughput budgets.
- **Properties ranking** — a forced ranking (no ties) of correctness, performance, concurrency, security, reliability, portability, observability, ergonomics, energy, real-time. The ranking becomes the **tiebreaker** when properties conflict later.
- **State of the art** — comparable systems, what they do right, what they do wrong, where the field is ripe for advance.
- **Non-goals** — explicit exclusions. What's tempting to include that should be excluded.
- **Invariants (first pass)** — what must always be true. What bugs would be catastrophic.
- **Hard problems** — the hardest engineering challenge, the hardest design decision, the decision that would take longest to undo if made wrong.
- **Boundaries + assumptions** — target platform, trusted computing base, threat model.
- **Team + timeline** — who, how many, AI-assisted or not, deadlines.

Output: `docs/VISION.md` — mission, target workloads, properties ranking, comparable systems, novel angles, non-goals, load-bearing invariants (first pass), open questions.

**Gate 1** requires the user to explicitly confirm: mission captures intent, target workloads correct, properties ranking defensible, invariants accounted for.

### Phase 2 — COMPARISON.md and NOVEL.md

These two documents force honesty about where the project stands against existing work.

**COMPARISON.md** has three columns: **MATCH** (parity with state of the art), **LEAD** (surpasses state of the art), **DELIBERATELY-NOT** (intentional omissions). Every entry is annotated with WHY. "We match X because the alternative is a moat no single project can cross alone." "We lead on Y because the state of the art rests on an assumption that's no longer true." "We deliberately skip Z because it trades more than it gives."

**NOVEL.md** expands each novel angle into a full section: concrete scope (what we will do, precisely), what "done" looks like (objective, measurable), dependencies, risks.

**Gate 2** asks: are these the novel angles the project should commit to? Anything that's actually well-solved elsewhere and should be demoted from NOVEL to MATCH? Anything that's overreach and should be deferred?

The outputs of Phase 2 constrain Phase 3 — the architecture must serve the novel angles.

### Phase 3 — ARCHITECTURE.md and ROADMAP.md (the scripture)

This is the binding commitment. What lands here constrains every implementation session for the project's lifetime.

**ARCHITECTURE.md** works through a checklist (omitting what doesn't apply) of foundational decisions:

- Concurrency model
- Memory model (ownership, lifetime, allocator, GC presence)
- Resource model (whatever resources the system manages)
- Namespace model (identifiers, lookup, inheritance)
- Interface model (APIs, syscalls, ABIs, wire protocols, on-disk formats)
- Safety model (invariants, how they're enforced — types, dynamic checks, formal proofs)
- Performance model (latency/throughput bounds, where they come from, how measured)
- Fault model (what faults the system handles, what it doesn't)
- Security model (threat model, trust boundaries, capabilities)
- Observability model (logging, tracing, metrics, debugging primitives)
- Test/verification strategy (unit, integration, property, fuzz, formal)
- Build/deploy/runtime story
- Upgrade / compatibility story (versioning, migration, feature flags)

For every decision: chosen option, alternatives considered, rationale, tradeoffs accepted, reference to the novel angle or invariant it serves.

Every on-disk, on-wire, or ABI-exposed format gets a byte-precise layout, a version tag, compat / ro-compat / incompat feature-flag tiers, and compile-time invariants.

The document ends with an enumerated list of **load-bearing invariants** that must hold. These become TLA+ proof obligations and audit invariants — the same list, used in three places.

**ROADMAP.md** is a phased plan from Phase 1 (first implementation phase) onward. Each phase has scope, non-scope, deliverables, **objective measurable exit criteria** (a test suite passing, a spec TLC-clean, a benchmark hitting a target), risks, dependencies.

**Gate 3** is the big one. After signoff, ARCHITECTURE.md + ROADMAP.md become scripture: deviations either update scripture first (with user approval) or get reverted. No silent normalization.

### Phase 4 — CLAUDE.md and repository scaffolding

After Gate 3, the bootstrap sets up the operational artifacts that enforce the framework at every session.

**CLAUDE.md** — the central operating contract. The skill instantiates a long, detailed template tailored to the project's specifics. Sections include:

- **Mission** — copied from VISION.md.
- **Design-first policy** — what Phase 0 means, when it's active.
- **Spec-first policy** — if a feature touches a load-bearing invariant, the TLA+ model comes BEFORE the implementation. Concrete six-step pattern. Includes TLA+ tools installation instructions.
- **Audit-triggering changes** — a project-specific trigger table listing which subsystems require an adversarial soundness audit before merge. Includes the prosecutor agent prompt template.
- **Invariants that must hold** — copied verbatim from ARCHITECTURE.md §N, kept in sync.
- **Regression testing** — every audit finding that can be made to fail without the fix MUST land a regression test. Test matrix baseline (default + ASan + TSan, extend with ubsan/msan/fuzzers).
- **Implementation patterns** — idempotency on retry, compile-time invariants, split-big-chunks-into-sub-chunks, crash-injection + fault-injection testing.
- **Autonomy + escalation** — default stance and always-escalate cases (format breaks, destructive ops, ARCH deviations, scope pivots).
- **Git + commit discipline** — detailed commit messages with prose rationale; per-chunk commits; `Co-Authored-By` footer; prefer new commits over `--amend`; never skip hooks; never force-push to main.
- **Memory + session continuity** — protocol for `MEMORY.md`, `project_active.md`, `project_next_session.md`, `audit_rN_closed_list.md`. Frontmatter format. Handoff protocol.
- **Phase status docs** — per-phase pickup guide with TL;DR, landed chunks table, remaining work, exit criteria status, build/verify commands, trip hazards.
- **End-of-iteration summary** — a structured format with seven required blocks (this iteration landed / what's new / posture / memory + scripture / roadmap visualization / cache window / candidate next chunks). The **posture** block requires verbatim boot-log evidence — proof the system actually demonstrated the new behavior, not just compiled.
- **Roadmap visualization** — a three-row ASCII visualization at the end of every iteration summary so the user can orient without flipping to ROADMAP.md.
- **When to recommend `/compact`** — soft vs STRONG recommendation, with explicit criteria for each.
- **Autonomy modes (stopping signals)** — common modes and how to surface them.
- **Self-audit before formal audit** — 30–60 second self-review pass for known-hazard categories before spawning the prosecutor agent.
- **Per-chunk commit anatomy** — substantive commit + hash-fixup follow-up. Commit message structure with mechanism block, "alternative considered" rationale, trade-offs section, files annotation, audit posture, "what does this chunk close vs. defer" precision.
- **Reference doc tip-snapshot pattern** — `docs/REFERENCE.md` accumulates a back-traceable narrative chain at top; each chunk advances the tip with full detail; predecessors demoted to one-liners; ~10-chunk back-window.
- **Deferred-finding discipline** — silent drops are forbidden; the close commit explicitly enumerates deferred items with priority + finding number + named successor chunk.
- **Forward-progress guarantee discipline** — every wait/sleep primitive documents the wake source AND a forward-progress fallback so interactive runs AND CI both terminate cleanly. Composing pattern: kernel pre-fire + iteration cap + CI injection + post-boot grep enforcement.
- **Memory file size pressure** — `MEMORY.md` ≤ ~24 KB; top entries one-line; predecessor entries trim as new milestones land.
- **Style policies** — comments explain non-obvious WHY, never WHAT. No multi-paragraph docstrings. Terse responses. No backwards-compat shims without explicit need.

**Repository scaffolding** — the directory layout:

```
{repo-root}/
├── CLAUDE.md
├── docs/
│   ├── VISION.md
│   ├── COMPARISON.md
│   ├── NOVEL.md
│   ├── ARCHITECTURE.md
│   ├── ROADMAP.md
│   ├── phase1-status.md
│   ├── REFERENCE.md
│   └── reference/
│       └── 00-overview.md
├── specs/
│   ├── README.md
│   └── (spec files appear during impl)
└── (language-specific dirs appear per ARCH decision)
```

**The bootstrap deliberately does NOT initialize build / test / source directories.** Those depend on the language, build system, and project shape chosen in ARCHITECTURE.md. They are left for the implementation session.

**Memory scaffolding** — seeds `MEMORY.md`, `project_active.md`, `project_next_session.md`, optionally `user_profile.md` in the auto-memory directory.

### Phase 5 — Handoff

The bootstrap closes with:

1. Confirmation that VISION, COMPARISON, NOVEL, ARCHITECTURE, ROADMAP, CLAUDE.md are all in place.
2. Confirmation that specs/, docs/, memory/ are scaffolded.
3. Confirmation of git state: everything committed, no untracked artifacts.
4. A statement of the next step: "Next session starts Phase 1 per ROADMAP.md. Read CLAUDE.md first, then docs/phase1-status.md, then the relevant ARCHITECTURE sections."
5. A reminder: implementation sessions are bound by ARCH + ROADMAP as scripture. Deviations update scripture first.

Each phase commits as a separate git commit so the bootstrap itself is auditable.

---

## The operational discipline contract

This is the heart of what the skill establishes. The artifacts are not documentation; they are the **contract under which the model operates**.

### Design comes before code

Phase 0 produces written, reviewed, versioned documents. No implementation pressure. Architectural decisions compound — a wrong choice in the concurrency model, the on-disk format, or the fault model becomes irrevocable once thousands of lines depend on it. Phase 0 is the window during which those decisions are still cheap.

### Formal specifications come before implementation

Any feature that touches a load-bearing invariant gets its TLA+ (or equivalent) model first. The spec is the source of truth; code is an implementation of the spec. When the spec catches a bug, the bug costs minutes at the spec level instead of commits at the runtime level.

The pattern is six explicit steps: propose in prose → model in TLA+ → iterate until TLC is green → write a `_buggy.cfg` that demonstrates the failure mode → implement against the model with SPEC-TO-CODE references → extend spec FIRST when new mechanisms appear.

**Slogan: if you cannot articulate the invariant formally, you don't understand it well enough to implement it.**

### Adversarial audits gate invariant-bearing merges

Not ceremony. Each round has historically surfaced bugs the test suite didn't catch, and the pattern is that regressions in invariant-bearing surfaces are not caught by tests. The CLAUDE.md template ships a prosecutor-agent prompt that instructs the audit agent to **prosecute, not defend**: brutal but grounded, quote code don't paraphrase, prosecution chains with state → step → step → violation.

Findings are catalogued by severity (P0 active break / P1 latent break / P2 hazard / P3 nice-to-have). Every P0/P1/P2 is fixed before merge. Every audit finding that can be made to fail without the fix MUST land a regression test.

### ARCHITECTURE.md and ROADMAP.md are binding scripture

After Gate 3, deviations either update scripture first (with user approval) or get reverted. There is no silent normalization. The CLAUDE.md template includes explicit "always escalate" cases that autonomy does NOT cover: format breaks, destructive ops, ARCH deviations, cross-phase scope pivots.

### Session continuity is preserved

Memory files + status docs + the tip-snapshot pattern in REFERENCE.md mean that a context compaction or a new session instance is a **no-op for quality**. The next instance opens, reads `CLAUDE.md` + `docs/phaseN-status.md` + `project_next_session.md`, and is fully oriented.

The per-chunk discipline supports this: every chunk updates `project_active.md`, `project_next_session.md`, the phase status doc, and the reference tip-snapshot. Every chunk's tip-snapshot ends with `Predecessor: <chunk-name> (<substantive hash>/<fixup hash>).` so the chain is back-traceable. A reviewer reading just the Snapshot block in REFERENCE.md can reconstruct the last ~10 chunks of work without opening any other file.

### Per-chunk rituals

Every chunk that adds a row to a status doc OR a tip-snapshot entry to REFERENCE.md uses a **two-commit pattern**: a substantive commit with full prose rationale, followed by a trivial hash-fixup commit that backfills the substantive SHA into status docs and REFERENCE.md. After both commits, memory files refresh with actual SHAs.

The end-of-iteration summary has seven required blocks. The **posture** block requires verbatim boot-log evidence — load-bearing evidence the system actually demonstrated the new behavior. The **cache window** block backs the user's decision on whether to continue iterating or invoke `/compact`.

### Forward-progress guarantee discipline

Every wait/sleep primitive (IRQ wait, semaphore, condition variable, busy-poll) must document the wake source AND a forward-progress fallback. The composing pattern — kernel-side pre-fire for interactive runs, iteration cap on busy-poll, CI-side injection via control socket, post-boot grep enforcement — is encoded from day one rather than retrofitted after the first hang.

---

## Why this is efficient for large ambitious projects

Phase 0 looks like overhead. It is not overhead. Here is the math.

**Foundational decisions are 10–100× more expensive to revisit after implementation.** A concurrency model swap a year in means rewriting every locking site. A wire-format change means coordinated upgrades, deprecation periods, and compat shims. A namespace model rethink touches every API. Phase 0 spends a session (or a few) making these decisions cheaply. That session is repaid 10× the first time the project would otherwise have hit a foundational rewrite — and 100× by the third time.

**Spec-first compounds in the same direction.** A bug caught in TLA+ costs minutes. The same bug caught at runtime costs commits — and the same bug caught in production costs incidents. The cost ratio is similar to the design-first ratio. Spec-first applies only to load-bearing invariants, not pure computation or test helpers; the skill is explicit about when to apply it.

**Session continuity eliminates re-derivation cost.** Without it, every context compaction re-runs the "understand the project" preamble — sometimes incorrectly, because the conversation log doesn't fully reconstruct invariants. With it, the next session opens already oriented. On long-arc projects, this is the single biggest efficiency multiplier; it converts session boundaries from a cost into a no-op.

**Audits catch what tests miss, and the regression-test rule converts each finding into permanent coverage.** Without an adversarial framing, audits become defensive ("explain why the code is fine"). With the prosecutor framing, audits surface real bugs. The regression-test rule ensures the same bug class can't return.

**The structured end-of-iteration summary is decision-grade communication.** Seven blocks, three of them load-bearing for user decisions (posture / cache window / candidate next chunks). The user can choose what comes next in under 30 seconds without asking for state.

**Operational gates protect the user's binding authority.** The model has no authority to cross Gate 1, 2, or 3. Autonomy granted later does not extend backwards. Whatever discipline the user is willing to commit to, the framework upholds; whatever the user wants to revisit, the framework surfaces.

**Anti-patterns are named explicitly.** Moving past Gate 1 with an unclear vision; moving past Gate 3 with an ARCH that hasn't decided the hardest open questions; writing CLAUDE.md before ARCH is signed off; starting implementation during the bootstrap; accepting "no invariants apply." Naming the failure modes is the first step to not committing them.

---

## When to invoke this skill

Invoke at the **start** of a new low-level systems project where:

- Correctness is load-bearing (silent wrong is worse than crash).
- The project will outlive a single session, and probably outlive a single quarter.
- Foundational decisions exist that compound across the codebase (concurrency model, on-disk format, fault model, ABI surface).
- The user is willing to invest a design session (or a few) up front for the option of building a production-grade peer in the project's domain.

Do **not** invoke for:

- One-off scripts.
- Throwaway prototypes.
- Projects where the foundational decisions are already settled by the choice of language or framework (a typical CRUD web app).
- Projects where you don't yet know whether you want to commit; the bootstrap is binding by design.

If the repo already has Phase 0 artifacts, the skill asks before overwriting.

---

## Installation

```
mkdir -p ~/.claude/skills/phase-zero-bootstrap
cd ~/.claude/skills/phase-zero-bootstrap
git clone https://github.com/apology-is-policy/phase-zero-bootstrap.git .
```

Or copy `SKILL.md` into `~/.claude/skills/phase-zero-bootstrap/` directly.

Invoke from Claude Code with `/phase-zero-bootstrap` or by asking the model to start a new project with Phase 0 discipline.

---

## License

See repository.
