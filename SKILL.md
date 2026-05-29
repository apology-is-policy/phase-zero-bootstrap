---
name: phase-zero-bootstrap
description: Bootstrap a new low-level systems project (kernel, filesystem, scheduler, driver, network stack, crypto library, etc.) with a Phase 0 design-first framework. Enforces formal modeling before implementation, adversarial soundness audits, scripture-binding ARCHITECTURE.md + ROADMAP.md, and session continuity via memory + status docs. Invoke when the user wants to start a new project with production-grade discipline from day one.
---

# Phase Zero Bootstrap — Low-Level Systems Project

You are setting up the operational framework for a new low-level systems project so that all subsequent implementation proceeds with maximum rigor. This is a one-time operation per project; the artifacts you produce bind every future session.

**Your ambition is set to max.** Quality over tokens. Time over shortcuts. The user is explicitly asking for a project that will stand as a production-grade first-class peer in its domain — your job is to set the conditions under which that is possible.

## Read first

Before starting, read (if they exist):
- `CLAUDE.md` at the repo root
- `docs/VISION.md`
- Any existing design documents

If the repo already has Phase 0 artifacts, ask the user whether they want to refresh in place or bootstrap fresh. Do NOT overwrite without explicit approval.

## Philosophy

1. **Design comes before code.** Phase 0 produces written, reviewed, versioned documents. No implementation pressure.
2. **Verification rigor comes before implementation — by the user's chosen method.** Every feature that touches a load-bearing invariant is validated *before* the code is trusted, by ONE of two methods the user picks at bootstrap (Phase 4) and may revisit later: **(a) formal specification** — a TLA+ (or similar) model written first, the spec as source of truth, the code an implementation of the spec; or **(b) deep reasoning** — the invariant argued explicitly in prose (design doc + file header + commit message + reference doc) and validated by the adversarial audit + the test suite. Either way the invariant is pinned *in writing before merge*; the choice is only which form that pinning takes. (Formal modeling is the higher-assurance, higher-cost path; deep reasoning is lighter and often sufficient — many mature projects move from (a) to (b) once the team trusts its invariant discipline.)
3. **Adversarial audits gate invariant-bearing merges.** Not ceremony — each audit round has historically surfaced bugs the test suite didn't catch.
4. **ARCHITECTURE.md and ROADMAP.md are binding scripture.** Implementation deviations either update scripture first or are reverted.
5. **Session continuity is preserved.** Memory files + status docs mean a context compaction or new session instance is a no-op for quality.

## The flow

**5 phases, 3 gates. You MUST obtain explicit user approval at each gate before proceeding to the next phase. Autonomy does NOT extend to crossing gates; the user binds scripture.**

```
Phase 1: Vision interview + VISION.md
    |
    + GATE 1: Vision signoff
    |
Phase 2: COMPARISON.md + NOVEL.md
    |
    + GATE 2: Novel angles signoff
    |
Phase 3: ARCHITECTURE.md + ROADMAP.md
    |
    + GATE 3: Scripture signoff (the binding commitment)
    |
Phase 4: CLAUDE.md + repository scaffolding (memory/specs/status templates)
    |
Phase 5: Handoff — summary, next-steps, fresh session briefing
```

Implementation begins in a separate session after Phase 5, armed with the framework this bootstrap set up.

## Phase 1 — Vision interview

Goal: understand the user's vision to the point where you could explain it back to them with the same nuance.

### How to interview

- Ask open-ended questions first. Narrow on follow-ups based on their answers.
- Probe ambiguity. "What do you mean by fast?" "Which workloads?" "What does failure look like for this system?"
- Push back gently when answers sound too easy. Ambitious projects have hard choices; surface them.
- Do not robotically work through every question below. Read the user's answers and ask what's missing.
- If the user is uncertain on a question, note it as "deferred" and move on — the vision doc can mark open questions.

### Question bank

Use what's relevant; not every project touches every question.

**Mission + scope**
- What are you building? One sentence, then three.
- Why does this need to exist? What's inadequate about existing solutions?
- Who is the user? Individual, team, production operator, kernel, another system?
- What's the north star — what does "successful" look like 2-3 years out?

**Workload + scale**
- What are the target workloads? Pick 2-4 concrete ones.
- What scale are you targeting — CPUs, cores, devices, connections, nodes, data volume, ops/sec?
- What's the smallest deployment, what's the largest?
- What's the latency budget — median, p99, tail?
- What's the throughput budget?

**Properties — rank them**
Rank these in priority order for the system. Force ordering, not ties:
- Correctness (no silent data loss / no invariant violation)
- Performance (latency, throughput)
- Concurrency / parallelism
- Security (isolation, authentication, side-channel resistance)
- Reliability (MTBF, recovery speed, fault tolerance)
- Portability (architectures, OS support)
- Observability (introspection, debugging, tracing)
- Ergonomics (API surface, operator UX)
- Energy / thermal
- Real-time guarantees (bounded latency, no jitter)

**State of the art**
- Name 3-5 comparable existing systems.
- What do they do right? What do they do wrong?
- What's the state of the art, and where is it ripe for advance?
- What novel contributions could this project meaningfully make?

**Non-goals**
- What will this explicitly NOT do?
- What's tempting to include that should be excluded?
- What are the "no" answers that most comparable projects waffle on?

**Invariants (first pass)**
- What must always be true when the system is running?
- What bugs would be catastrophic — the kind where silent wrong is worse than a crash?
- What would a correctness proof look like — what would you want to prove?

**Hard problems**
- What's the hardest engineering challenge you expect?
- What's the hardest design decision?
- What decision, if made wrong, would take the longest to undo?

**Boundaries + assumptions**
- What's the target platform / kernel / language / runtime?
- What trusted computing base do you assume?
- What hardware / OS / environment primitives are assumed available?
- What's the threat model (if any)?

**Team + timeline**
- Who will work on this? One person, many, AI-assisted?
- What's the timeline — weeks, months, years?
- Are there external deadlines, release commitments, or upstream dependencies?

### Writing VISION.md

After the interview, produce `docs/VISION.md`. Template below.

```markdown
# VISION

## Mission

One paragraph. What this project is and why it needs to exist. No marketing.

## Target workloads

Enumerate the 2-4 workloads that shape every downstream decision. For each: name, description, characteristic operation, scale.

## Properties ranking

Ordered list. 1 = top priority; the tiebreaker when properties conflict. Rationale beneath each.

## Comparable systems

Table: Name | What they do right | What they do wrong | Relevance to this project.

## Novel angles

Bullet list. The things this project will uniquely contribute. Each bullet: one sentence commitment + one sentence rationale.

## Non-goals

Explicit. Each: one sentence stating what's out + one sentence on why it's out.

## Load-bearing invariants (first pass)

Enumerate. These become verification obligations in Phase 2+ (formal-spec proof obligations or deep-reasoning invariants, per the Phase-4 verification-rigor choice).

## Open questions

Things the interview didn't resolve. Tagged Q1, Q2, ... for later reference.

## Revision history

| Date | Change | Reason |
```

### Gate 1: Vision signoff

Present VISION.md to the user. Ask:
1. Does the mission capture the intent?
2. Are the target workloads right?
3. Is the properties ranking one you're willing to defend?
4. Any missing comparable systems?
5. Any invariants that should be added?

Iterate until the user explicitly says "VISION is good" or equivalent. Do not proceed without that signoff.

## Phase 2 — COMPARISON.md + NOVEL.md

These two docs crystallize what the project will uniquely contribute. They force honesty about where the project stands against existing work.

### COMPARISON.md

Three columns: MATCH (we do what they do, at parity), LEAD (we do what they do, better), DELIBERATELY-NOT (we do not do this).

Each row is a feature / property / capability. Rows reference specific competitors.

Annotate every entry with WHY. "We match X because the alternative is a moat no single project can cross alone." "We lead on Y because the state of the art rests on an assumption that's no longer true." "We deliberately skip Z because it trades more than it gives."

Template:

```markdown
# COMPARISON

Compared against: {system A}, {system B}, {system C}, ...

## MATCH — parity with the state of the art

| Feature | Competitors | Our approach | Why we match |

## LEAD — surpassing the state of the art

| Feature | Competitors | Our approach | Why we lead |

## DELIBERATELY-NOT — intentional omissions

| Feature | Competitors that have it | Why we skip |

## Overall positioning

Prose. Where in the ecosystem does this project sit? What's the elevator pitch for a skeptical engineer in this domain?
```

### NOVEL.md

Each novel angle from VISION.md becomes a full section here:
- Concrete scope (what we will do, precisely)
- What "done" looks like (the empirical/formal criterion)
- Dependencies (what other decisions this rests on)
- Risks (what could invalidate this)

Template:

```markdown
# NOVEL CONTRIBUTIONS

For each angle:

## N-{number}: {Name}

**Claim.** One sentence.

**Scope.** What's in, what's out.

**Done criterion.** Objective. Measurable.

**Dependencies.** Other decisions or components this rests on.

**Risks.** What could invalidate the claim. What we'll monitor.

**State of the art reference.** Specific papers / systems / prior attempts.
```

### Gate 2: Novel angles signoff

Ask the user:
1. Are these the novel angles the project should commit to?
2. Any angles that are actually well-solved elsewhere and should be demoted from NOVEL to MATCH?
3. Any that are overreach (infeasible at the project scope) and should be deferred?

Iterate until explicit signoff. The outputs of Phase 2 constrain Phase 3 — the architecture must serve the novel angles.

## Phase 3 — ARCHITECTURE.md + ROADMAP.md

This is the binding scripture. What lands here binds every implementation session.

### ARCHITECTURE.md

The foundational design decisions, each with written rationale. Sections are project-specific; the following is a checklist to work through, omitting what doesn't apply.

**For every foundational decision:**
- State the decision (chosen option).
- State the alternatives considered.
- State the rationale (why chosen over alternatives).
- State the tradeoffs accepted.
- Reference the novel angle or invariant it serves.

**Checklist of decisions** (not all apply; extend as domain requires):

- Concurrency model (lock-based / lock-free / STM / message-passing / actor / data-parallel)
- Memory model (ownership, lifetime, allocator, GC presence)
- Resource model (whatever resources the system manages — CPUs, memory, disk, connections, pools)
- Namespace model (if applicable — identifiers, lookup, inheritance)
- Interface model (APIs, syscalls, ABIs, wire protocols, on-disk formats)
- Safety model (invariants, how they're enforced — types, dynamic checks, formal proofs)
- Performance model (latency/throughput bounds, where they come from, how measured)
- Fault model (what faults the system handles, what it doesn't)
- Security model (threat model, trust boundaries, capabilities)
- Observability model (logging, tracing, metrics, debugging primitives)
- Test/verification strategy (unit, integration, property, fuzz, formal)
- Build/deploy/runtime story
- Upgrade / compatibility story (versioning, migration, feature flags)

**Format discipline.** Every on-disk, on-wire, or ABI-exposed format gets:
- A struct / record / wire layout, byte-precise.
- Version tag.
- Compat / ro-compat / incompat feature-flag tiers (or domain equivalent).
- Compile-time or build-time invariants (struct size, alignment, enum range).

**Example prose skeleton** (adapt per domain):

```markdown
# ARCHITECTURE

## §1 Scope

What this document decides. What it leaves to later phases. Version + date.

## §2 Terminology

Precise definitions. Every term the rest of the document depends on.

## §3 Concurrency model

The decision. The alternatives. The rationale.
Invariants: (list — these become verification obligations: formal-spec proof obligations or deep-reasoning invariants).

## §4 Resource model

...

(continue for every section identified in the checklist)

## §N Invariants that must hold

An enumerated list. Each invariant: one sentence stating it, one paragraph explaining why it's load-bearing, one sentence on how it's enforced (type system, runtime check, formal proof, review policy).

## §N+1 Open design questions

Deferred to later phases. Tagged DQ-1, DQ-2, ... for reference.
```

### ROADMAP.md

A phased plan from Phase 1 (first implementation phase) onward, with exit criteria for each phase.

Each phase has:
- Scope (what's in)
- Non-scope (what's explicitly out)
- Deliverables (artifacts produced)
- Exit criteria (objective, measurable — a test suite passing, a TLA+ spec TLC-clean, a benchmark hitting a target)
- Risks + dependencies

Template:

```markdown
# ROADMAP

## Phase 1: {Name}

**Scope.** ...

**Non-scope.** ...

**Deliverables.**
- ...

**Exit criteria.**
- [ ] Item 1 (objective, measurable).
- [ ] Item 2.

**Risks.** ...

## Phase 2: {Name}

...

## Open phases (sketched)

Later phases at lower resolution — sketched, not binding, revised when reached.
```

### Gate 3: Scripture signoff

Ask the user to explicitly approve ARCH + ROADMAP as binding. Once signed off, these documents constrain every implementation session. Deviations must update scripture first, not after the fact.

Iterate until explicit signoff.

## Phase 4 — CLAUDE.md + scaffolding

After Gate 3, set up the operational artifacts that enforce the framework at every session.

### Decision: verification-rigor method (ask the user)

Before writing CLAUDE.md, **ask the user which verification method this project will use** for invariant-bearing features (Philosophy #2) — it is optional, not assumed:

- **(a) Formal specification (spec-to-code / spec-first)** — TLA+ (or similar) model written + TLC-checked BEFORE the implementation, with buggy-config counterexamples and a SPEC-TO-CODE mapping. Highest assurance; highest cost; needs the TLA+ toolchain + the modeling skill. Best when the invariants are concurrency/ordering-heavy and the cost of a runtime bug is very high.
- **(b) Deep reasoning** — the invariant is argued explicitly in prose (design doc + file-header + commit message + reference doc) and validated by the adversarial audit round + the test suite. Lighter; faster; no toolchain. Best when the team trusts its invariant discipline and the audit+test floor is strong.

Present it as a clear choice (offer a recommendation if the project's risk profile points one way), get the user's pick, and **write the CHOSEN variant into CLAUDE.md's verification-rigor policy** (below) — keep the chosen one, drop or footnote the other. The choice is revisitable: a project may start with (a) and later move to (b) (a common path once invariant discipline is proven), or suspend (a) per-chunk — record any such change in CLAUDE.md with the rationale + user signoff. If the user has no preference, default to (a) for a from-scratch low-level project (formal modeling earns its cost early), but say so.

### CLAUDE.md

Write `CLAUDE.md` at the repo root. It encodes the operational framework. Template below — substitute project-specific details (project name, build commands, invariants) but preserve every section's substance.

```markdown
# CLAUDE.md

Operating notes for Claude when working on {PROJECT_NAME}.

## Mission

{One paragraph — copy from VISION.md's Mission section.}

## Design-first policy (Phase 0)

Architectural decisions compound. A wrong choice in {list the foundational decisions from ARCH} becomes irrevocable once thousands of lines of code depend on it. Phase 0 — active at project start until ARCHITECTURE.md is signed off — pauses implementation of new features until major architectural decisions are specified in writing.

Phase 0 outputs live in `docs/`: VISION.md, COMPARISON.md, NOVEL.md, ARCHITECTURE.md, ROADMAP.md. All written, reviewed, versioned.

Only after ARCHITECTURE.md is signed off does implementation resume.

**Process notes during Phase 0:**
- Every design decision gets a written rationale.
- Every decision assumes it will be challenged in a year when context has faded.
- Design sessions produce documents, not code.
- Load-bearing invariants identified during design become candidates for formal specification. The spec is the source of truth; code is an implementation of the spec.

## Verification-rigor policy (applies to every invariant-bearing feature)

This project uses **{FILL IN AT PHASE 4: "formal specification (spec-first)" OR "deep reasoning"}** as its verification method — the user's choice (Phase 4 "Decision: verification-rigor method"). Keep the matching variant below; the other is the documented alternative the project can switch to (or suspend per-chunk) with user signoff.

**The trigger (both methods):** a feature touches a load-bearing invariant — concurrency, commit ordering, resource accounting, cache coherence, fault recovery, crypto key derivation, scheduling fairness, priority inversion, deadlock freedom, anything in the §N Invariants list in ARCHITECTURE.md. Such a feature must have its invariant **pinned in writing and validated before merge.** The methods differ only in *how*.

### Method (a) — formal specification (spec-first)

**The TLA+ (or similar) model comes BEFORE the implementation.** Write the spec, let TLC chew on it, let invariant violations surface at the spec level where they cost minutes, not at runtime where they cost commits.
1. Propose the feature in prose (problem + shape).
2. Model the mechanism in TLA+ — state, actions, invariants. TLC with small bounds.
3. Iterate until TLC is green under the invariants the implementation must uphold. If a bug shows up, fix the DESIGN before writing code.
4. Where a spec captures a specific bug, also write a `{spec}_buggy.cfg` that fails the invariant under the buggy assumption. Executable documentation of "this is the bug, this is the fix."
5. Implement against the model. Cross-reference each impl step to the corresponding spec action in comments. Keep the SPEC-TO-CODE mapping current.
6. When the impl surfaces a new mechanism the spec didn't cover, extend the spec FIRST, then update the impl.

**If you cannot articulate the invariant formally, you don't understand it well enough to implement it.**

### Method (b) — deep reasoning

**No formal model; the invariant is validated by careful prose reasoning backed by the rigor floor.**
1. Propose the feature in prose (problem + shape).
2. Articulate the invariant explicitly — in the implementation's file header + the commit message + the reference doc. Walk the interleavings / failure modes in prose: name the serializing event, the lock order, the lifetime, the ordering guarantee.
3. The **adversarial audit round + the test suite are the rigor floor** — they stand in for TLC. The audit prosecutes the articulated invariant; regression tests pin the behavior (every demonstrated bug gets a test that fails pre-fix, passes post-fix).
4. **If you cannot articulate the invariant clearly in prose, you don't understand it well enough to implement it.**

Method (b) is the lighter, toolchain-free path. A project on (a) may suspend it per-chunk, or switch wholesale to (b), once invariant discipline is proven — record the change + the user signoff here.

### Both methods

Features that clearly benefit from the rigor: multi-{device,node,thread} coordination, quorum protocols, commit protocols, key rotation, fault recovery, refcount graphs, MVCC readers vs writers, anything that persists a reservation-then-commit or multi-phase write, anything that uses crypto nonces over a shared key, anything with more than one state machine in play.

Features that usually don't (pure computation, test helpers, config parsing, CLI glue): skip the heavy treatment; just write + test. Use judgment.

### TLA+ setup

Install OpenJDK (`/opt/homebrew/opt/openjdk/bin` on macOS, apt-get install default-jdk on Linux).

Download TLA+ tools:
```bash
curl -sL -o /tmp/tla2tools.jar \
  https://github.com/tlaplus/tlaplus/releases/download/v1.8.0/tla2tools.jar
```

Run every spec in `specs/`:
```bash
export PATH="/opt/homebrew/opt/openjdk/bin:$PATH"
cd specs
for s in $(ls *.tla | sed 's/\.tla$//'); do
    echo "== $s =="
    java -cp /tmp/tla2tools.jar tlc2.TLC -workers auto -deadlock \
        -config $s.cfg $s.tla 2>&1 | tail -3
done
```

Pre-commit for invariant-bearing features: spec clean + buggy-config counterexample confirmed + all tests pass.

## Audit-triggering changes

**Any change to the surfaces below MUST spawn a focused adversarial soundness audit before merge.** Not as ceremony — each round has historically surfaced bugs the test suite didn't catch, and the pattern is that regressions in these areas are not caught by tests.

**Trigger list.** (Refresh after each ARCH change.)

| Surface | Representative files | Why |
|---|---|---|
| {subsystem 1, e.g. crypto nonce / key handling} | {files} | {reason — tied to an invariant from ARCH §N} |
| {subsystem 2} | {files} | {reason} |
| {subsystem 3} | {files} | {reason} |
| ... | ... | ... |

**How to run an audit round:**

1. Spawn a soundness-prosecutor agent (general-purpose subagent, `run_in_background: true`). Use the most capable model available.
2. In the prompt, include `memory/audit_rN_closed_list.md` contents as the "already fixed — do not re-report" preamble.
3. Scope the prompt to the surface you changed.
4. Tell the agent explicitly to prosecute, not defend. Brutal but grounded.
5. Wait for the completion notification. Do not poll.
6. Trust but verify: the agent's summary describes intent; validate quoted file:line references.
7. Fix every P0/P1/P2 finding before merge. P3 findings get tracked or closed with explicit justification.

### Prosecutor agent prompt template

```
You are an adversarial soundness prosecutor auditing {scope} against
the invariants listed in ARCHITECTURE.md §N.

# Scope

Commits: {SHA1}, {SHA2}, ...
Files in scope: {list}

# Invariants that MUST hold

{enumerate from ARCH §N, briefly}

# Adversarial categories to prosecute

- {category 1 — e.g. nonce reuse in crash-recovery paths}
- {category 2 — e.g. tamper vectors}
- {category 3 — e.g. integer edge cases}
- {category 4 — e.g. lifecycle hazards}
- {category 5 — e.g. concurrent mutation}
- (extend per domain)

# Procedure

1. Read memory/audit_rN_closed_list.md to know the do-not-report set.
2. For each file in scope, read fully. Do NOT skim.
3. Catalog findings by severity:
   - P0: actively-broken (reproducible correctness / security / safety violation).
   - P1: latent-broken (correct today under exact test coverage, wrong under realistic deviation).
   - P2: hazard + should-land-before-merge.
   - P3: nice-to-have.
4. For each finding: file + line + prosecution chain (state → step → step → violation) + suggested fix.
5. Withdraw findings guarded by existing code. Don't re-report closed items.

Report format per finding:
## Finding F<NUM> [P<severity>]: <title>
**File**: path:line
**Invariant**: <which>
**Prosecution**:
1. state that <X>.
2. attacker/crash/retry does <Y>.
3. observes <violation>.
**Suggested fix**: <1-2 sentences>

At the end: Summary with counts by severity + confidence notes on
areas you couldn't audit as deeply as you wanted.

Be brutal but grounded. Quote code; don't paraphrase it.
```

## Invariants that must hold

Copy the enumerated list from ARCHITECTURE.md §N verbatim here. These are the verification obligations (formal-spec proof obligations or deep-reasoning invariants, per the Phase-4 choice) AND the audit invariants. Keep in sync with ARCH.

## Regression testing

- Every audit finding that can be made to fail without the fix MUST land a regression test. The test fails on the pre-fix code; passes on the post-fix code.
- Every spec-level bug demonstrated by a `{spec}_buggy.cfg` must have a corresponding runtime regression test (when feasible — some concurrency bugs are hard to trigger deterministically; in those cases the buggy-config serves as the durable regression).
- Test matrix baseline: default build + address sanitizer + thread sanitizer. Extend with ubsan/msan/fuzzers when practical.
- Pre-commit for every substantive change: full test suite on the default build. Pre-merge for invariant-bearing changes: all three matrices + all specs.

Example commands (adapt per build system):

```bash
# Default build + tests
{build command} && {test command}

# ASan
{build command with ASan}
{test command}

# TSan
{build command with TSan}
{test command}

# All specs
export PATH="/opt/homebrew/opt/openjdk/bin:$PATH"
cd specs && for s in *.tla; do
  java -cp /tmp/tla2tools.jar tlc2.TLC -workers auto -deadlock \
    -config "${s%.tla}.cfg" "$s" 2>&1 | tail -3
done
```

## Implementation patterns

### Idempotency on retry

Any function that writes durable state MUST short-circuit on clean state. If the function's contract is "on success, durable state X is recorded," then calling it twice with the same inputs and no intervening mutations must produce byte-identical durable state.

**Why:** distributed commit protocols (quorum, Paxos-like, 2PC) rely on byte-identical retries to preserve content-agreement invariants. A function that allocates fresh resources on every call produces divergent content across retries, violating the quorum content invariant and bricking the system.

**Pattern:** carry a dirty flag. Mutations set dirty. Commits check dirty; if clean AND a durable result already exists, return cached result. If dirty, do the work + clear dirty. Analogous for any structure that produces durable artifacts.

### Compile-time invariants

Every on-disk, on-wire, or ABI-exposed format gets:
- `_Static_assert` (C/C++) / `static_assert` / equivalent on struct size, alignment, and discriminant ranges.
- Explicit version constants (STM_UB_VERSION-style).
- Compat / ro-compat / incompat feature-flag tiers.

Catches format drift at build time, not at mount / connect / boot time.

### Split big chunks into sub-chunks

When an implementation chunk exceeds one commit's reasonable scope, split into sub-chunks named Xa / Xb / Xc. Each sub-chunk lands independently with its own status-doc row, commit message, and tests. Handoff points between sub-chunks mean a context compaction at any boundary is recoverable.

### Chunk completeness — pull dependencies forward; deferral needs signoff

If the current chunk's **proper and complete** implementation depends on an item that is later on the roadmap, or on an item deferred in an earlier chunk, **strongly prefer pulling that item into the current chunk** — complete the chunk to the fullest specification possible rather than shipping a half-version built against a missing dependency. The pull-forward is the **default**, not a deviation: note it in the chunk's commit message + status row and proceed (it is the act of *finishing the chunk*, so it does not by itself need signoff).

**Deferral is the exception, and it needs the user's signoff.** If deferring the dependency genuinely makes more sense (truly separable, large enough to be its own chunk, or better audited on its own), do not silently ship the half-version — surface it as a structured choice and get the user's vote first.

**Why this is binding:** too many quiet deferrals compound into **silent omissions** — the system ends up not actually doing what scripture says it does, and nobody decided that on purpose. Bias the default toward completeness; put the burden of proof on *deferring*, not on *building*. (This is the chunk-scoped form of the build-vs-defer judgment: a dependency of the current chunk defaults to build-now; only a genuinely-separable foreseeable-but-not-yet item is deferred, and turning a real current-chunk dependency into a deferral is what needs signoff.)

### Crash-injection + fault-injection testing

For torn-write-sensitive paths (persistent state machines, multi-phase commits), wire fault-injection hooks at every durable write. Test that recovery from each injection point produces a valid state. Same pattern applies to interrupt injection in schedulers, fault injection in fault-tolerant networking, and partial-failure injection in distributed systems.

## Autonomy + escalation

**Default stance:** When the user grants autonomy ("you can proceed autonomously," etc.), proceed on implementation, testing, formal modeling, audit triage, commit, and push to your own branch.

**Always escalate (autonomy does NOT cover these):**
- Format breaks (on-disk version bumps, wire-protocol ABI changes, syscall interface changes).
- Destructive operations (`git push --force`, branch/tag deletion, hard reset of shared branches, database drops).
- Architectural deviations from ARCHITECTURE.md — either update ARCH first (with user approval) or revert the deviation.
- Cross-phase scope pivots — pulling *unrelated* future scope into the current phase, OR **deferring an item the current chunk depends on** (see "Chunk completeness — pull dependencies forward"), must be confirmed. Pulling a genuine *dependency* forward to complete the current chunk to its fullest spec is preferred and does NOT need confirmation — note it and proceed.
- Anything unclear in ARCH / ROADMAP / NOVEL / VISION.
- Anything visible to others (pushes to shared branches, PR creation, external API calls, Slack/email posting).
- Spending significant compute or external budget.

**Deviation tracking:** If implementation diverges from ARCH / ROADMAP, surface it explicitly:
- In the commit message (the WHY of the deviation).
- In the affected phase status doc.
- If the deviation is load-bearing, propose an ARCH update; do not silently normalize the deviation.

## Git + commit discipline

- **Detailed commit messages** with prose rationale. Each commit message explains WHAT changed, WHY, and what the alternative was (if the decision was non-obvious). First line under ~70 chars; body has the reasoning.
- **Per-chunk commits**, not per-day. A chunk is a coherent, testable, revertable unit.
- **`Co-Authored-By` footer** on AI-assisted commits. Stratum uses: `Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>`. Adapt per Anthropic guidance.
- **Prefer new commits over `--amend`.** Amending rewrites history; hook failures may hide in the prior commit. Exception: if the user explicitly asks for an amend.
- **Never force-push to main.** Never force-push shared branches without explicit user approval.
- **Never skip hooks** (`--no-verify`, `--no-gpg-sign`) unless the user explicitly requests it. If a pre-commit hook fails, diagnose and fix the underlying issue.
- **Before committing**, run the full test suite on the default build. For invariant-bearing changes, run the full matrix + specs.

## Memory + session continuity

This project uses Claude Code's auto-memory (`~/.claude/projects/<project-hash>/memory/`). Maintain these files:

- `MEMORY.md` — one-line index, ~150 chars per entry.
- `project_active.md` — current state. Brief description of what's landed, what's in progress, what's next. Update per commit.
- `project_next_session.md` — pickup pointer for the next session. Detailed. Written at every handoff.
- `audit_rN_closed_list.md` — cumulative do-not-report preamble for audit rounds. Append after every round.
- `user_profile.md` (optional) — user's role, preferences, preferred style.
- `feedback_*.md` — durable feedback (do and don't rules) that should survive context compaction.

Every memory file has frontmatter:
```
---
name: {short name}
description: {one-line description used to decide relevance in future conversations}
type: {user, feedback, project, reference}
originSessionId: {session id where this was first written; for traceability}
---
```

### Handoff protocol

At every session boundary (compaction, explicit handoff, completing a phase/sub-chunk, any point where a new instance might pick up):

1. Update `project_active.md` with current state.
2. Update `project_next_session.md` with the pickup pointer: current tip SHA, what's landed, what's next, any invariants or traps the next session needs to know.
3. Update the affected phase status doc.
4. If audit findings remain open, summarize in memory.
5. Commit the memory + status updates.

### Handoff mode under budget pressure

When token/time budget is low:
- Stop at a clean commit boundary.
- Update memory + status docs thoroughly.
- Summarize to the user: what landed, what's queued, what the next session picks up.
- Do NOT land partial work just to close a chunk.

## Phase status docs

Every phase has a status doc at `docs/phase{N}-status.md`. It's the authoritative pickup guide for that phase.

Sections:

- **TL;DR** — one paragraph.
- **Landed chunks table** — rows of `| Commit SHA | What | Tests |`. One row per landed sub-chunk. Add immediately when the chunk commits.
- **Remaining work** — outstanding sub-chunks with scope notes.
- **Exit criteria status** — checklist from ROADMAP, ticked as deliverables complete.
- **Build + verify commands** — exact invocations.
- **Trip hazards** — invariants carrying into this phase, gotchas for subsequent sub-chunks.
- **Known deltas from ARCH** — owed follow-ups (things the impl needs but ARCH hasn't specified yet).
- **References** — pointers to relevant ARCH sections, specs, prior-phase docs.

Update status docs per chunk, not per phase.

## End-of-iteration summary

After every landed chunk (feature chunk, audit close, doc-only chunk), produce a structured summary so the user — or a future session reading the conversation log — can reconstruct state in under 30 seconds. The format is load-bearing: it surfaces the next decision point cleanly.

```
**This iteration landed (N new commits, tip `<HASH>`)**:
- `<hash1>` — <one-line scope>
- `<hash2>` — <one-line scope (hash fixup if applicable)>

**What's new**:
- <highlight 1> — one paragraph max; key mechanisms not a redo of commit message
- <highlight 2>

**Posture**:
- N/N PASS × default (~Xms) + sanitizer (~Yms). M specs clean (if any moved).
- Working tree clean; K commits ahead of origin/<branch>.
- Boot-log evidence (verbatim quotes of the key new behavior — load-bearing; proves the system actually demonstrated the new behavior, not just compiled):
  ```
  <key boot-log lines>
  ```
- Audit-bearing classification: <audit-bearing at layer X / non-audit-bearing at per-syscall, audit-bearing at userspace-driver discipline layer>.
- ROADMAP exit criteria touched: <closes §X.Y "..." / advances §X.Y substrate-only — bridge to /dev/cons surface still open / leaves open §Z.W with successor pointer>.
- <Deferred audits / open findings carried forward, if any>.

**Memory + scripture**: MEMORY.md (top entry + latest-milestone + tip ref), project_active.md (description + session-window + cumulative commit list), project_next_session.md (current state + verify-pickup tip extended), docs/phaseN-status.md (landed rows), docs/REFERENCE.md (tip + earlier-tip).

**Roadmap visualization**: (see Roadmap visualization section)

**Cache window**: <cache stays warm — feasible to continue / cumulative tokens substantial — soft recommendation / STRONG recommendation for /compact>. Rationale: <clean tree at X; tests green; N chunks landed in this session; next chunk Y is fresh-vs-cached subsystem>.

**Candidate next chunks**: <brief; with deps if non-obvious>.
```

The Posture's boot-log quote is load-bearing — it's evidence, not narration. The Cache window line backs the user's decision on whether to continue iterating or compact. Memory + scripture enumerates exactly which files moved (a reviewer can spot-check).

## Roadmap visualization

At the end of each iteration summary, include a small ASCII roadmap viz so the user can orient without flipping to ROADMAP.md. Three rows:

```
ROADMAP            P1 ████ P2 ████ P3 ████ P4 ████░░░░ P5 ░░░░ P6 ░░░░ P7 ░░░░ P8 ░░░░
                                          ↑ here
PHASE 4 CHUNKS     ✅ A B C D E F G H Ia1 Ia2 Ib Ic1-7 Ja Jb Jc K K-events L L-scanout N O ...
                   ⬜ Id  M  Z
CURRENT CHUNK      <chunk-name> — <one-line scope> ✓ landed (or ⏳ in flight)
```

Update per iteration. The `↑ here` arrow points at the active phase. `████░░░░` bars are eyeballed against chunks-landed vs chunks-queued — they don't need precision; their job is a visual sense of "deep into N" vs "early in N". The chunks line uses alphabetic / chunk-name shorthand consistent with the phase status doc. The empty-boxes line enumerates outstanding sub-chunks by short name.

When approaching a phase boundary (last few chunks of Phase N), add a fourth line listing the **queued exit criteria** for the active phase (one-liner each, with which sub-chunk closes which).

Refresh the visualization in `project_active.md` at every session boundary so a fresh session opens with the picture already drawn.

## When to recommend `/compact`

Two grades:

**Soft recommendation** ("you could compact here"): clean-tree boundary, tests green, but cache window still has 1-2 chunks of headroom. Mention as an option; default to continuing if the user is actively iterating.

**STRONG recommendation** ("strong recommendation for `/compact` here"): all of:
- Working tree clean (everything committed).
- Test matrix green (default + sanitizers).
- Recent audit (if any) is closed; no open P0/P1/P2.

AND at least one of:
- Cumulative tokens consumed exceed ~60-70% of the model's context budget.
- The next chunk involves a fresh subsystem (different from what's cached).
- 4+ chunks have landed in this session window (cache churn coming anyway).
- An audit roundtrip + fix loop is queued (audit output is dense — fresh context absorbs it better).
- The user is about to dictate a context shift (different feature, different module).

The recommendation includes the rationale verbatim: "Clean tree at `<hash>`; tests + specs green; cumulative cache is stretching (N chunks landed this session); next chunk Y is a fresh subsystem. **Strong recommendation for `/compact` here.** Handoff doc updated for clean pickup."

**Do NOT recommend mid-chunk or with uncommitted state.** Mid-chunk compaction loses inflight reasoning.

## Autonomy modes (stopping signals)

The user can grant autonomy with explicit stopping signals. Common modes:

- **"You have autonomy. Stop on STRONG `/compact` recommendation"** — the most common mode for long-arc sessions. Proceed iteration-by-iteration; produce end-of-iteration summary after each chunk lands; continue if cache window allows; explicitly stop and surface the STRONG recommendation when the criteria above are met.
- **"Autonomy until `/effort max` would help"** — proceed but pause when a chunk would benefit from `/effort max` (multi-step audit, format break, spec-first invariant work).
- **"Autonomy for this one chunk"** — bounded by the chunk; come back with end-of-iteration summary; do not start the next chunk without explicit signal.
- **"Stop after N chunks"** — explicit chunk count.

When a stopping signal fires, do NOT silently continue. Surface clearly: "Per autonomy mode, stopping here. <reason>."

## When to recommend `/effort max`

For sessions involving:

- Multi-step audit roundtrips with triage + fixes + re-audit.
- Composition-heavy chunks crossing 3+ modules.
- Format-break work (on-disk version bumps, ABI changes, syscall interface changes).
- Spec-first work where the spec needs careful invariant design.
- Recovery from an audit P0/P1 that requires deep tracing.

Suggest the user run `/effort max` if not already set. Quality over speed in these contexts is non-negotiable.

## Self-audit before formal audit

Before spawning the formal audit agent — and on every chunk regardless of whether it's formally audit-bearing — do a 30-60 second self-review pass for known-hazard categories:

- **Lock ordering**: every multi-lock acquire matches the global rule (mark "N/A — single-thread userspace" if applicable; flag otherwise).
- **Lifetimes**: borrowed pointers documented; UAF surfaces traced; scope-to-process boundaries explicit.
- **Error-path cleanups**: every early-return path releases acquired resources.
- **Idempotency on retry**: dirty-flag short-circuits where applicable.
- **State-machine guards**: every transition matches its spec action; defensive range-checks reject malformed external input.
- **Compile-time invariants**: format changes have `_Static_assert` / equivalent.
- **Boundary conditions**: integer overflow, empty inputs, max bounds, wraparound semantics.
- **DSB / memory-order discipline**: barriers before/after publish-points; fences match the spec mandate (e.g. VIRTIO §2.7.13.1).
- **IRQ / wake-source discipline**: ACK device-side interrupt registers before each batch; wake mechanism documented; forward-progress guarantee in place.
- **Endianness**: explicit LE/BE per spec; cross-arch behavior.

State the result in the commit message: "Self-audit clean across <N> categories: <list>" or "Self-found before audit: <finding> — fixed in same chunk".

Findings either land as a fix-in-the-same-chunk OR as an explicit "self-found before audit" addendum commit (so the audit's closed-list preamble accounts for them).

## Audit-in-flight parallel work

When the focused audit prosecutor is running in the background, do NOT idle and do NOT poll for completion (the runtime delivers a notification on completion). Two activities happen in parallel — both required, in this order:

1. **Useful non-colliding work first.** Identify work that doesn't touch the audit's file scope. Examples: documentation updates, status-doc refresh, memory-file maintenance, scripture renumbering, prep notes for the next chunk, a separate-subsystem refactor, sibling-test additions. The agent's prompt scoped its file list explicitly — treat that list as off-limits while the agent runs (don't risk creating a merge conflict with the agent's deductions).

2. **Then a self-audit on the same surface as the agent.** Prosecute the audited code adversarially yourself. Re-read every modified file. Trace each invariant. Find what the agent might miss. Two independent prosecutors catch different issues — the agent and you bias toward different categories. Treat your findings with the same authority as the agent's.

When the agent completes:
- **Merge findings**: combine its report with your self-found ones. Disposition together; do not segregate "agent findings" vs "self findings" — they're all findings with the same severity rigor.
- **Cross-check**: if the agent missed something you found (or vice versa), the gap itself is signal about audit coverage. Note it for the next prosecutor prompt's "focus areas."

This discipline is **non-optional** for any audit-bearing chunk. The cost is small (the self-audit is anyway a refinement of the pre-audit self-review per "Self-audit before formal audit" above); the value is real — round 2 prosecutors and self-audits running concurrently with round 1 have caught real P0/P1s the single-pass formal audit missed.

## Re-audit on dirty close

A close is **dirty** if any of:
- Any P0 returned.
- (P1 + P2 count) ≥ 6.
- The fixes themselves were structurally invasive (restructured a load-bearing mechanism, lifted a lock-order rule, changed a wait/wake protocol, removed a primitive).

On a dirty close, the fixes themselves may introduce new bugs — **schedule a follow-up audit round on the audit-close state**. The follow-up:

1. Treats the round-N closed list as do-not-re-report preamble (just like any audit).
2. Focuses prosecutor attention on **the fixes themselves**, named explicitly in a "round N+1 focus areas" section. Invasive restructures often introduce new lock-order issues, lifecycle hazards, or memory-ordering gaps.
3. Runs the audit-in-flight parallel-work discipline (above): useful non-colliding work + self-audit on the same surface.
4. Repeats until the round returns clean (0 P0, 0 P1, only documented-as-deferred P3s).

A clean close that completed via N > 1 rounds is still clean. Multiple rounds aren't a defect; they're the discipline doing its job. Each round's findings + dispositions get appended to the cumulative closed-list memory file.

The pattern catches real bugs: a fix that restructures a wait/wake mechanism (e.g. from single-waiter Rendez to multi-waiter poll_waiter_list to break an ABBA deadlock) may introduce a new pop-and-copy race window that loses notes under contention — a defect the round-1 fixes create that round-1 review didn't see.

## Design conversation -> scripture commit (mid-project pattern)

When an implementation chunk surfaces a non-trivial design question -- a new mechanism, a load-bearing decision, an invariant not yet in scripture -- the workflow is:

1. **Stop the implementation.** Don't try to design-while-coding. Stop, surface to the user.
2. **Surface as a structured option set.** Not a yes/no; lay out 2-4 options with their consequences. Auto-mode bias is "make the call" -- but scripture-altering decisions are explicitly outside auto-mode and warrant the user's vote.
3. **Have the conversation in-session.** Iterate to user signoff in one round-trip where possible.
4. **Land the design as a SCRIPTURE COMMIT FIRST -- no code.** The commit updates `ARCHITECTURE.md` / `NOVEL.md` / phase-design docs / `CLAUDE.md` / `ROADMAP.md` as needed, and adds a memory-file index entry. The commit message names the design decision, the rationale, the alternatives considered, and the open questions resolved.
5. **THEN implement** in a subsequent commit that references the scripture commit's SHA in its message.
6. **THEN audit** (the standard pattern for audit-bearing implementations).

The pattern is "scripture before code, every time the code would otherwise determine the scripture." It produces audit-traceable design history and makes the implementation auditable against a fixed reference. The scripture commit is short, focused, and reversible if implementation surfaces a flaw in the design.

## Plain ASCII commit messages

Commit message bodies (and the first line) use plain ASCII. Specifically:
- **No em-dashes** (the Unicode em-dash character). Use `--` instead.
- **No Unicode arrows** (`→`, `←`, `↑`, `↓`). Use `->`, `<-`, `^`, `v`.
- **No section signs** (`§`). Use `section` or just the number.
- **No Unicode quotes** (the typographic forms). Use ASCII `"..."`, `'...'`.
- **No comparison glyphs** (`≥`, `≤`, `≠`). Use `>=`, `<=`, `!=`.
- **No emoji** unless the user explicitly requests them in the message.

Why: clean diff against `git log`, clean grep over the log, consistent rendering across terminals and CI dashboards, and one fewer thing for a future maintainer's editor / pager to mishandle. Doc files (`docs/*.md`, `CLAUDE.md`) and code comments may use Unicode freely; **commit messages stay ASCII**.

Pass commit message bodies via a HEREDOC for the same robustness reason:
```bash
git commit -m "$(cat <<'EOF'
Title line under 70 chars.

Body paragraphs use plain ASCII (-- not em-dash; -> not arrow; etc.).

Co-Authored-By: <model identifier>
EOF
)"
```

## Per-chunk commit anatomy (substantive + hash fixup)

Every substantive chunk that adds a row to a status doc OR a tip-snapshot entry to `REFERENCE.md` uses a **two-commit** pattern:

**1. Substantive commit.** Code + tests + reference doc + status-doc row with `*(pending)*` placeholder + REFERENCE.md tip with "substantive pending / hash fixup pending" placeholder. Commit message structure:

```
<scope>: <short summary> [(P<count> P<sev> if audit-close)]

<paragraph: WHAT changed and WHY>

Alternative considered: <name>. Rejected because <reason>.  ← when non-obvious

<paragraph(s): mechanism block(s) — numbered list of new mechanisms>

Does NOT close ROADMAP §X.Y "<criterion>" — <reason; named successor>.
What this chunk DOES close: <substrate / surface / contract>.

No new kernel surface. <or: New syscall X; audit-bearing per CLAUDE.md trigger surfaces>.

Tests added: <count>; <what they cover>. <N>/<N> PASS x default (~Xms) + UBSan (~Yms).

Audit posture: <audit-bearing at layer / non-audit-bearing at per-syscall, audit-bearing at userspace-driver discipline layer>. Self-audit clean across <N> adversarial categories: <list>.

Trade-offs:
  - <accepted cost 1> — rationale.
  - <accepted cost 2>.

Files:
  - <path> — <one-line annotation per file>

<If audit-close: per-finding section (Fixed / Deferred), one paragraph each>

Phase status row updated: docs/phaseN-status.md.
Reference doc updated: docs/reference/NN-...md.
REFERENCE.md tip promoted; <predecessor> demoted to earlier-tip.

Co-Authored-By: <model identifier>
```

**2. Hash fixup commit.** Trivial commit replacing `*(pending)*` with the substantive commit's hash in:
- `docs/phaseN-status.md` landed-chunks row
- `docs/REFERENCE.md` tip snapshot
- Any other status doc that referenced the pending hash

Commit message:
```
<scope>: hash fixup

Backfill substantive SHA <HASH> in:
  - docs/phaseN-status.md landed-chunks table row
  - docs/REFERENCE.md tip snapshot

Co-Authored-By: <model identifier>
```

This pattern makes chunks immediately greppable in `git log` and keeps status docs hash-accurate without temporal lag. Applies to feature chunks, audit closes, doc-only chunks — anything that lands a row.

After both commits, refresh memory files (MEMORY.md description + latest-milestone + tip ref; `project_active.md` description + session-window + cumulative commit list; `project_next_session.md` current state) with the actual SHAs. Memory files live outside the repo and are updated after the hash fixup.

## Reference doc tip-snapshot pattern

`docs/REFERENCE.md` has a `## Snapshot` block at top whose first bullet is the active **Tip**. Every chunk advances the tip:

- The new chunk's tip narrative goes at the top, with full multi-paragraph detail (what landed, mechanisms, ROADMAP impact, audit posture).
- The previous tip is demoted to an `**Earlier tip:**` entry; its content is **preserved verbatim**. Each predecessor becomes another `**Earlier tip:**` entry — the snapshot block accumulates a back-traceable narrative chain.
- Tip narratives end with `Predecessor: <name> (<substantive hash>/<fixup hash>).` so the chain is explicit.

A reviewer reading just the Snapshot block in REFERENCE.md should reconstruct the last ~10 chunks of work without opening any other file. Trim aggressively past the 10-chunk back-window if the snapshot grows past ~10 entries; older history lives in the phase status docs.

## Deferred-finding discipline

When an audit surfaces findings that genuinely belong in a future chunk:

- The close commit message MUST explicitly enumerate the deferred items by priority + finding number + brief rationale.
- The future chunk is named (e.g., "deferred to P5-N replace-in-flight flag" / "deferred to R12-vaddr unified upper-bound check").
- If the finding is purely doc/cosmetic and can be deferred indefinitely, it goes into the relevant reference doc's "Known caveats" section with a reference number.
- Silent drops are forbidden — if a finding is dropped, the close commit must state "withdrawn: <reason>".
- The next-session handoff doc lists any open deferred findings under a "Deferred audits" section.

This protects against audit findings being lost across session boundaries.

## Forward-progress guarantee discipline

When a chunk adds a wait/sleep mechanism (IRQ wait, semaphore, condition variable, busy-poll), document the wake source AND a forward-progress fallback so interactive runs AND CI both terminate cleanly.

Patterns that compose:

- **Kernel-side pre-fire** (interactive forward-progress): for tests waiting on hardware IRQs that may not fire in interactive mode, the kernel test pre-fires the SPI via `gic_set_pending_spi(intid)` (or equivalent) before spawning the child. Per ARM IHI 0069 §12.9.6, pending-bit is orthogonal to enable, so the latched pending delivers the moment the child enables. Child's first wait returns without sleeping; then transitions to a bounded mechanism that won't hang.
- **Iteration cap on busy-poll**: bounded loop with an explicit cap (e.g., `MAX_POLL_ITER = 200_000_000`). Wall-clock budget documented per emulator/host (~3-5 sec on QEMU TCG). On cap exhaustion: SKIP-exit with clear log line; don't hang.
- **CI-side injection via tooling**: test harness wires the emulator's control socket (e.g. QMP `-qmp unix:...,server,nowait`) and `tools/test.sh` spawns a background injector that polls the boot log for a known sentinel (e.g. `<subsystem>: AWAITING_<EVENT>`) then triggers the wake-source (`send-key`, `human-monitor-command`, etc.).
- **Post-boot enforcement**: `tools/test.sh` greps the boot log for a success marker after boot completes; absence (when the wake was expected per env config) fails the run with a clear diagnostic naming likely causes.

The pattern composes: kernel pre-fire gives one guaranteed wake; CI injection delivers the actual event; iteration cap bounds the worst case; post-boot grep enforces the success signal. Each layer has a single responsibility.

Sentinel + success-marker strings are part of the test-harness ABI — they don't change without coordinating tooling updates in the same commit.

## Memory file size pressure

`MEMORY.md` is loaded into every conversation as context (up to ~24 KB before truncation). Keep it tight:

- **Top entries are one-line each** (~150-200 chars max). The linked file carries the detail; the index entry is just a pointer + one-line hook.
- **Latest milestone gets full detail** (one paragraph at the bottom of the top block). When superseded, demote to one-liner predecessor.
- **Predecessor entries trim to one-liner** as new milestones land. Full detail lives in `project_active.md` session-window blocks + the phase status doc.
- **Size check**: if `MEMORY.md` exceeds ~24 KB on a chunk close, trim the most-recent verbose predecessor down to one line before committing memory updates.

`project_active.md` accumulates per-session `## This session window — <chunk> landed` blocks — these can be verbose, since `project_active.md` is read selectively rather than loaded as default context. When a session compacts, ensure both `project_active.md` and `project_next_session.md` carry the full handoff narrative; `MEMORY.md` just points to them.

## Style policies

- **Comments explain non-obvious WHY, never WHAT.** A well-named identifier already tells you WHAT. Never reference the current task / fix / PR ("used by X", "added for Y flow", "issue #123") — those belong in the PR description and rot.
- **No multi-paragraph docstrings.** One short line max where needed.
- **Terse responses, direct statements.** State results and decisions; don't narrate deliberation.
- **No backwards-compat shims** without explicit need. Delete dead code; don't leave re-exports with `// removed` comments.
- **Avoid comments that reference the author's intent** ("I chose X because..."). The reason goes in the commit message; the code stands on its own.

## Session-state files

- Built artifacts deploy to `{path}` and must be {any post-deploy steps, e.g. codesign --force --sign -}.
- TLA+ tools at `/tmp/tla2tools.jar`. Install instructions above.

## Build + test commands

```bash
# Default build
{command}

# Sanitizer builds
{command}

# Test suite
{command}

# Specs
{as above}
```
```

### Repository scaffolding

Create the following directories and files:

```
{repo-root}/
├── CLAUDE.md                             # from template above
├── docs/
│   ├── VISION.md                         # from Phase 1
│   ├── COMPARISON.md                     # from Phase 2
│   ├── NOVEL.md                          # from Phase 2
│   ├── ARCHITECTURE.md                   # from Phase 3
│   ├── ROADMAP.md                        # from Phase 3
│   ├── phase1-status.md                  # template (first impl phase)
│   ├── REFERENCE.md                      # as-built top-level index (template below)
│   └── reference/
│       └── 00-overview.md                # as-built overview template (below)
├── specs/
│   ├── README.md                         # TLA+ setup + run instructions
│   └── (spec files appear during impl)
└── (language-specific dirs appear per ARCH decision)
```

The REFERENCE.md + reference/00-overview.md are seeded as templates;
per-subsystem files (`reference/01-...md`, etc.) appear during
implementation as their subsystems land. The reference is intentionally
distinct from ARCHITECTURE.md — see "As-built REFERENCE doc" in the
Sustaining discipline section above.

Do NOT initialize build / test / source directories — those depend on the language, build system, and project shape chosen in ARCHITECTURE.md. Leave them for the implementation session.

#### REFERENCE.md template

```markdown
# {PROJECT_NAME} — technical reference

This document is the **as-built** reference for {PROJECT_NAME}. It
describes what exists in the tree today, where the relevant code
lives, which formal specs pin which invariants, and how the
subsystems compose. It is NOT a roadmap and NOT a design document —
see `docs/ARCHITECTURE.md` for design intent and `docs/ROADMAP.md`
for phased scope.

## How to read this

The reference is split by subsystem, one file per layer of the stack.
Each file follows the same template:

- **Purpose** — one paragraph on what the layer does and where it
  sits in the stack.
- **Public API** — every exported function with its contract.
- **Implementation** — structure + invariants + known caveats.
- **Spec cross-reference** — formal modules that pin invariants for
  this layer.
- **Tests** — which suites exercise the layer and what they cover.
- **Status** — what's implemented today vs. what's stubbed or
  deferred. Commit hashes cite the landing points.

When a section describes a detail enforced by a spec, the spec's
action / invariant name is in `backticks`. When a section cites a
file, the form is `path/to/file.c:line` so editors can jump there.

## Snapshot

- **Tip**: {commit hash}
- **Phases**: {current phase status}
- **Tests**: {suite count} × ({sanitizer matrix}) green.
- **Specs**: {N} formal modules clean.
- **LOC**: ~{N} KLOC across {M} src/ modules + {K} public headers.

For phase-level status see `docs/phase{N}-status.md`. The reference
below covers the as-built layers in bottom-up order.

## Contents

| File | Layer | Size guide |
|---|---|---|
| [00-overview.md](reference/00-overview.md) | Layer cake + cross-cutting concerns | medium |
| 01-{layer}.md (planned) | {description} | {size} |
| ...

(Per-subsystem files appear as their subsystems land.)

## Document maintenance

When a chunk lands (bug fix, refactor, new module), the author is
responsible for:

1. Updating the relevant reference/NN-*.md section(s).
2. Refreshing the [Snapshot](#snapshot) figures.
3. If the chunk introduces or refutes an invariant, updating the
   spec catalog entry.
4. If a new term or acronym enters the lexicon, updating the
   glossary.

Reference sections are PR-first like any code change; the audit
policy in `CLAUDE.md` extends here: a change to a documented
invariant updates the spec FIRST, then the reference, then the code.
If the three disagree, the spec wins.
```

#### reference/00-overview.md template

```markdown
# 00 — Overview

## Layer cake (bottom-up)

(Layered ASCII diagram of the stack as built.  Update per chunk.)

## Cross-cutting concerns

### {Invariant name 1}

(One paragraph describing the invariant + which specs pin it +
which code paths uphold it.)

### {Invariant name 2}

...

### Lock order (global) {if applicable}

(The global lock-acquisition order with any reversal-warnings.)

## Versioning

| Version | Bump reason |
|---|---|
| {N → N+1} | {reason from ARCH version-bump notes} |

## Phase status (as-built)

| Phase | Status | Highlights | See |
|---|---|---|---|
| 1 | ✅ complete | ... | phase1-status.md |
| 2 | 🚧 in progress | ... | phase2-status.md |

## Test posture

(Test suite count, sanitizer configurations, spec count.)

## Build + CI

(Local invocation + CI workflow link.)
```

### Memory scaffolding

Seed the auto-memory directory at `~/.claude/projects/<project-hash>/memory/`:

```
MEMORY.md                      # index
project_active.md              # "Phase 0 in progress" or "Phase 0 complete, ready for Phase 1"
project_next_session.md        # pickup pointer
user_profile.md                # from the Phase 1 interview (role, style)
```

`MEMORY.md` template:
```markdown
- [Project active state](project_active.md) — {one-line description}
- [Next-session pickup](project_next_session.md) — {one-line pickup hint}
- [User profile](user_profile.md) — {role + preferences}
```

`project_active.md` template:
```markdown
---
name: {PROJECT_NAME} — active state
description: {one-line}
type: project
originSessionId: {session id}
---

Phase 0 {complete / in progress}. ARCHITECTURE.md + ROADMAP.md at {tip SHA}. {N} novel angles committed. {M} load-bearing invariants identified. Next: Phase 1 implementation — {first phase name} per ROADMAP.md.
```

`project_next_session.md` template:
```markdown
---
name: {PROJECT_NAME} — next-session pickup
description: Session pickup pointer. {status}
type: project
originSessionId: {session id}
---

READ FIRST, in this order:
1. CLAUDE.md (root)
2. docs/VISION.md, docs/ARCHITECTURE.md, docs/ROADMAP.md
3. docs/phase1-status.md

Current state: {description}

Next: {first implementation chunk}.
```

### Phase status doc template (first phase)

`docs/phase1-status.md`:

```markdown
# Phase 1 — status and pickup guide

Authoritative pickup guide for {Phase 1 name}.

## TL;DR

{One paragraph from ROADMAP.md Phase 1 scope.}

## Landed chunks

| Commit SHA | What | Tests |
|---|---|---|
| (empty — phase not started) |

## Remaining work

{Enumerate sub-chunks per the chunking plan in ROADMAP.md Phase 1.}

## Exit criteria status

(Copy from ROADMAP.md Phase 1 exit criteria.)

## Build + verify commands

{TBD — filled in when the build system lands in an early sub-chunk.}

## Trip hazards

{Invariants carrying into Phase 1 from ARCH. Gotchas.}

## References

- docs/ARCHITECTURE.md §{relevant sections}
- docs/ROADMAP.md §{relevant sections}
```

### TLA+ specs dir scaffolding

`specs/README.md`:

```markdown
# Specs

TLA+ formal specifications for load-bearing invariants.

## Setup

Install OpenJDK (`/opt/homebrew/opt/openjdk/bin` on macOS).

Download TLA+ tools:
```
curl -sL -o /tmp/tla2tools.jar \
  https://github.com/tlaplus/tlaplus/releases/download/v1.8.0/tla2tools.jar
```

## Run all specs

```
export PATH="/opt/homebrew/opt/openjdk/bin:$PATH"
for s in *.tla; do
    echo "== $s =="
    java -cp /tmp/tla2tools.jar tlc2.TLC -workers auto -deadlock \
        -config "${s%.tla}.cfg" "$s" 2>&1 | tail -3
done
```

## Structure

Every spec has:
- `{name}.tla` — the model.
- `{name}.cfg` — the primary config; TLC should find no violations.
- `{name}_buggy.cfg` (optional) — demonstrates a specific bug at the spec level. TLC should produce a counterexample.

Two configs per spec is the executable-documentation pattern: the primary config says "this is how it should work," the buggy config says "this is the specific way it could fail." When a runtime bug is caught that a spec could have caught, add the retroactive spec AND the buggy-config counterexample — future refactors are bound by the test.
```

## Phase 5 — Handoff

Final step. Summarize the bootstrap to the user:

1. Confirm VISION, COMPARISON, NOVEL, ARCHITECTURE, ROADMAP, CLAUDE.md all in place.
2. Confirm specs/ + docs/ + memory/ scaffolded.
3. Confirm git state: everything committed; no untracked artifacts.
4. State the next step: "Next session starts Phase 1 per ROADMAP.md. Read CLAUDE.md first, then docs/phase1-status.md, then the relevant ARCHITECTURE sections."
5. Remind the user: implementation sessions are bound by ARCH + ROADMAP as scripture. Deviations update scripture first.

## Commit strategy for the bootstrap itself

Create separate commits for each phase:
1. `Phase 0: VISION.md` (after Gate 1 signoff).
2. `Phase 0: COMPARISON.md + NOVEL.md` (after Gate 2).
3. `Phase 0: ARCHITECTURE.md + ROADMAP.md` (after Gate 3 — the big one).
4. `Phase 0: CLAUDE.md + framework scaffolding` (after Phase 4).

Each commit message explains what was decided and why, not just what was written.

## Interview principles

Throughout the bootstrap:
- **Press on ambiguity.** Vague answers produce vague architecture.
- **Force tradeoffs.** Ambitious systems accept costs; surface the costs.
- **Don't accept "we'll figure it out later" on foundational decisions.** Those are exactly the decisions Phase 0 exists to lock down.
- **Never propose a design that's not obviously defensible.** If you can't write the rationale, you don't understand the decision.
- **Refuse to proceed past a gate without explicit signoff.** Gates are not speed bumps; they're the mechanism by which the user binds scripture.

## Sustaining discipline (mid-flight)

The bootstrap establishes the framework; this section codifies the patterns
that keep long-arc work disciplined session over session. These are recommendations
the model should make to the user proactively, and patterns the model should
follow when granted autonomy.

### Recommend compaction at clean boundaries

Two grades of recommendation. **Codify both into the CLAUDE.md template** (see the "When to recommend `/compact`" section in the template above) and apply during the bootstrap itself when its own context window starts stretching.

**Soft recommendation** ("you could compact here if you want"): clean-tree boundary, tests green, but cache window still has 1-2 chunks of headroom. Mention as an option; default to continuing if the user is actively iterating.

**STRONG recommendation** ("strong recommendation for `/compact` here"): all of working-tree-clean + tests-green + recent-audit-closed, AND at least one of:
- Cumulative tokens consumed exceed ~60-70% of the model's context budget.
- The next chunk involves a fresh subsystem (different from what's cached).
- 4+ chunks have landed in this session window (cache churn coming anyway).
- An audit roundtrip + fix loop is queued (audit output is dense — fresh context absorbs it better).
- The user is about to dictate a context shift (different feature, different module).

The recommendation includes the rationale verbatim: "Clean tree at `<hash>`; tests + specs green; cumulative cache is stretching (N chunks landed this session); next chunk Y is a fresh subsystem. **Strong recommendation for `/compact` here.** Handoff doc updated for clean pickup."

**Do NOT recommend compaction mid-chunk or with uncommitted state.** Mid-chunk compaction risks losing critical inflight reasoning.

### Autonomy modes (stopping signals)

The user can grant autonomy with explicit stopping signals (codified in the CLAUDE.md template under "Autonomy modes"). The most common mode for long-arc sessions is:

> "You have autonomy. Stop on STRONG `/compact` recommendation."

Apply this also during the bootstrap itself: if the bootstrap turns into a multi-iteration design conversation, treat each gate signoff as a clean boundary and apply the same STRONG/soft distinction for compaction recommendations.

When a stopping signal fires, do NOT silently continue. Surface clearly: "Per autonomy mode, stopping here. <reason>."

### Recommend `/effort max` for high-stakes sessions

For sessions involving:
- Multi-step audit roundtrips with triage + fixes + re-audit.
- Composition-heavy chunks crossing 3+ modules.
- Format-break work (on-disk version bumps, ABI changes).
- Spec-first work where the spec needs careful invariant design.
- Recovery from an audit P0/P1 that requires deep tracing.

Suggest the user run `/effort max` if not already set. Quality over speed in
these contexts is non-negotiable.

### Sanitizer matrix as parallel-background-task pattern

The default + ASan + TSan matrix is the baseline for every code-changing chunk.
Pattern:

1. Default suite runs synchronously after build (~1-2 min).
2. ASan + TSan launch in parallel as background tasks (`run_in_background: true`).
3. Schedule a check-back wakeup tuned to expected runtime (~3-5 min for sanitizers).
4. Continue with non-overlapping work (doc updates, commit prep) while waiting.
5. Process completion notifications as they fire.

If both sanitizers timeout under contention with concurrent work, re-run
serially as a regression check before declaring the failure real.

Codify the cmake invocations for the project once during bootstrap; reference
them in CLAUDE.md so each session knows the exact commands.

### Audit-in-flight parallel work + re-audit on dirty close

Two related patterns codified in the CLAUDE.md template ("Audit-in-flight parallel work" + "Re-audit on dirty close" sections):

**Audit-in-flight parallel work**: when the focused audit prosecutor is running in the background, do not idle and do not poll. Two activities in parallel — both required:
1. Useful non-colliding work (docs, status, prep for next chunk, sibling-subsystem refactors). The agent's prompt scoped its file list; respect that scope.
2. Self-audit on the SAME surface as the agent. Two independent prosecutors catch different bugs.

When the agent completes, merge findings (don't segregate agent-found vs self-found — they're all findings with equal severity rigor).

**Re-audit on dirty close**: when an audit returns dirty (any P0, OR (P1 + P2) >= 6, OR structurally invasive fixes), schedule a follow-up audit round on the close commit. The follow-up:
1. Treats the round-N closed list as do-not-re-report preamble.
2. Focuses on the fixes themselves (named in a "round N+1 focus areas" section).
3. Runs the audit-in-flight parallel-work discipline again.
4. Repeats until clean.

Invasive fixes (restructured wait/wake mechanisms, lifted lock-order rules, changed primitives) genuinely introduce new bugs. Round-2 routinely finds defects round-1 missed because round-1 didn't see the fixes yet. The discipline is non-optional for audit-bearing chunks.

Codify both sections into the CLAUDE.md template so the discipline is encoded from day one.

### Design conversation -> scripture commit (mid-project pattern)

Codified in the CLAUDE.md template ("Design conversation -> scripture commit"). When an implementation chunk surfaces a non-trivial design question mid-stream:

1. Stop the implementation (don't design-while-coding).
2. Surface as a structured option set (2-4 options with consequences).
3. Have the conversation in-session to user signoff.
4. Land the design as a SCRIPTURE COMMIT FIRST (no code).
5. THEN implement (referencing the scripture commit's SHA).
6. THEN audit.

The pattern is "scripture before code, every time the code would otherwise determine the scripture." It produces audit-traceable design history and decouples the design decision from implementation risk.

### Plain ASCII commit messages

Codified in the CLAUDE.md template ("Plain ASCII commit messages"). Commit message bodies (first line + body) use plain ASCII: no em-dashes, no Unicode arrows, no section signs, no Unicode quotes, no comparison glyphs, no emoji unless explicitly requested. Doc files and code comments may use Unicode freely; commit messages stay ASCII for diff/grep/render consistency.

Always pass the message body via HEREDOC for robustness.

### As-built REFERENCE doc, distinct from ARCHITECTURE.md

`docs/ARCHITECTURE.md` is design intent (including unimplemented work).
`docs/REFERENCE.md` (+ `docs/reference/NN-*.md` per subsystem) is the as-built
state — what exists in the tree right now, with `file:line` citations, public
API tables, spec/code/test cross-refs, status checklists, and known caveats.

Per-chunk discipline (codified in CLAUDE.md):
- New module / new public API → add or extend the corresponding subsystem section.
- Change to a documented invariant → update the section after the spec.
- New TLA+ spec or buggy config → catalog entry.
- New term / acronym / state name → glossary entry.
- Format version bump → version table entry.
- Snapshot drift (test counts, spec counts, tip hash) → refresh on close.

The reference's `Snapshot` block at top-of-index is the canonical "where are
we right now" — refresh on every audit close + every chunk that changes the
counts. A reviewer reading just the snapshot should know within 30 seconds
which phase, which chunk, what tests, what specs, what tip.

The bootstrap should scaffold `docs/REFERENCE.md` + `docs/reference/00-overview.md`
as part of Phase 4. Per-subsystem files appear as their subsystems land.

### Multi-session continuity

Beyond the existing handoff protocol:

1. **Per-chunk status row pattern**: every status doc row uses the
   `*(pending)*` placeholder for its commit hash, with a follow-up trivial
   "fill in hash" commit immediately after the substantive commit lands.
   Solves the chicken-and-egg problem where the close commit message wants
   to reference its own hash. Pattern looks like:

   ```
   git commit -m "<substantive close>"          # produces hash X
   sed -i 's/(pending)/<hash X>/' status.md
   git commit -m "fill in <chunk> close hash"
   ```

2. **Reference snapshot refresh**: a separate fixup commit alongside the
   hash-fill, when test counts or specs counts moved.

3. **Memory file lifecycle**:
   - `project_active.md` — refreshed after every commit that changes posture.
   - `project_next_session.md` — fully rewritten at every handoff with
     concrete next-step decision tree (option A / B / C with deps).
   - Audit closed-list — appended after every audit close.

4. **Handoff content discipline**: the next-session pickup MUST include:
   - Current tip hash + `git log --oneline -N` verification command.
   - Test + spec posture (counts).
   - What's working, what's deferred (with reasons).
   - Decision tree for the next chunk (multiple options with deps).
   - Operational notes (autonomy level, lock-order, format versions in flight).
   - Quick-reference commands (build / test / spec / audit-spawn).

### Self-audit before formal audit

Before spawning the formal audit agent, do a 30-60 second self-review pass on
the impl + tests for known-hazard categories:

- **Lock ordering**: every multi-lock acquire matches the global rule.
- **Lifetime**: borrowed pointers documented; UAF surfaces traced.
- **Error-path cleanups**: every early-return path releases acquired resources.
- **Idempotency on retry**: dirty-flag short-circuits where applicable.
- **State-machine guards**: every transition matches its spec action.
- **Compile-time invariants**: format changes have static_asserts.
- **Boundary conditions**: integer overflow, empty inputs, max bounds.

Findings from self-review either land as a fix-in-the-same-chunk OR as an
explicit "self-found before audit" addendum commit (so the audit's closed-list
preamble accounts for them). Self-audit is not redundant with the formal audit;
it absorbs class P1s that would otherwise be embarrassing for the formal round
to find.

### Per-chunk commit anatomy (substantive + hash fixup)

The two-commit pattern applies to **every chunk that adds a row to a status doc OR a tip-snapshot entry to `REFERENCE.md`** — not just audit closes. See the "Per-chunk commit anatomy" section in the CLAUDE.md template above for the full structure (commit message template, hash-fixup follow-up, memory-file refresh after).

For an **audit close** specifically:
- First-line format: `Phase N RXX (<chunk> scope) audit close: <P0> P0 + <P1> P1 + <P2> P2 + <P3> P3`
- Body has explicit per-finding sections (Fixed / Deferred), one paragraph each.
- Tests section enumerates new regression tests + their failure-without-fix proof.

For a **feature chunk**:
- First-line format: `<chunk-name>: <short summary>`.
- Body has a numbered list of new mechanisms.
- "Does NOT close X — DOES close Y" precision when advancing a ROADMAP exit criterion without ticking the box (name the successor chunk).
- "Alternative considered" rationale when the chosen design isn't obvious.
- Trade-offs section listing accepted costs with rationale.
- Files section with per-file one-line annotation.

Both forms get the `Co-Authored-By` footer + the trivial hash-fixup follow-up commit + the post-commit memory-file refresh with actual SHAs.

### Predecessor chain discipline

Every chunk's tip-snapshot narrative, every status-doc row, and every `project_active.md` session-window block ends with an explicit predecessor pointer:

```
Predecessor: <chunk-name> (<substantive hash>/<fixup hash>).
```

This builds a back-traceable chain across sessions. A future session reading `docs/REFERENCE.md`'s Snapshot block can walk backwards through the chain without opening any other file. The chain also surfaces when a chunk lifts an open deferred finding from an earlier chunk — name the originating chunk explicitly.

### Deferred-finding discipline

When an audit surfaces findings that genuinely belong in a future chunk:

- The close commit message MUST explicitly enumerate the deferred items by
  priority + finding number + brief rationale.
- The future chunk is named (e.g., "deferred to P5-N replace-in-flight flag").
- If the finding is purely doc/cosmetic and can be deferred indefinitely, it
  goes into the relevant reference doc's "Known caveats" section with a
  reference number.
- Silent drops are forbidden — if a finding is dropped, the close commit must
  state "withdrawn: <reason>".

This protects against audit findings being lost across session boundaries.
The next-session handoff doc should list any open deferred findings under a
"Deferred audits" section.

### Forward-progress guarantee discipline

Every wait/sleep primitive a chunk introduces (IRQ wait, semaphore, condition variable, busy-poll) must document the wake source AND a forward-progress fallback so interactive runs AND CI both terminate cleanly. The composing pattern — kernel-side pre-fire + iteration cap + CI-side injection via control socket + post-boot grep enforcement — is codified in the CLAUDE.md template's "Forward-progress guarantee discipline" section.

Apply this proactively during the bootstrap when discussing Phase 1+ test-harness design: surface the discipline before the first hardware-wait test gets implemented, so the pattern is encoded from day one rather than retrofitted after a hang.

Sentinel + success-marker strings are part of the test-harness ABI — they don't change without coordinating tooling updates in the same commit.

### Memory file size pressure

`MEMORY.md` is loaded into every conversation as context (up to ~24 KB before truncation). Codified in the CLAUDE.md template ("Memory file size pressure" section). Operational rules during sustaining mode:

- Top entries one-line each (~150-200 chars max). Linked file carries the detail.
- Latest milestone gets full paragraph at the bottom of the top block. When superseded, demote to one-liner.
- Predecessor entries trim as new milestones land.
- If `MEMORY.md` exceeds ~24 KB at chunk close, trim the most-recent verbose predecessor to one line before committing memory updates.

`project_active.md` accumulates session-window blocks (these can be verbose; they're read selectively, not loaded as default context). Ensure both `project_active.md` and `project_next_session.md` carry the full handoff narrative; `MEMORY.md` just points to them.

### Operational summary patterns

End-of-iteration summaries follow the structured format codified in the CLAUDE.md template (see "End-of-iteration summary" section above). Every chunk close, audit close, and feature chunk produces a summary with these blocks (in order):

1. **This iteration landed** — per-commit one-liner with hashes.
2. **What's new** — bulleted highlights (key mechanisms, not a redo of commit message).
3. **Posture** — test counts + sanitizer matrix + clean-tree state + commits-ahead-of-origin + **boot-log evidence quoted verbatim** + audit-bearing classification + ROADMAP exit criteria touched + open deferred findings.
4. **Memory + scripture** — enumeration of every memory file + doc that was touched.
5. **Roadmap visualization** — three-row ASCII viz (see next subsection).
6. **Cache window** — soft / STRONG `/compact` recommendation with rationale.
7. **Candidate next chunks** — brief; with deps if non-obvious.

The boot-log evidence quote is **load-bearing**: it proves the system actually demonstrated the new behavior, not just compiled clean. The cache-window line gives the user the data to decide whether to continue or compact without having to ask the model.

This structure lets the user (or a future session reading the conversation log) reconstruct state in under 30 seconds.

### Roadmap visualization

At the end of every iteration summary, include a small ASCII roadmap viz so the user can orient without flipping to ROADMAP.md. Three required rows + an optional fourth:

```
ROADMAP            P1 ████ P2 ████ P3 ████ P4 ████░░░░ P5 ░░░░ P6 ░░░░ P7 ░░░░ P8 ░░░░
                                          ↑ here
PHASE 4 CHUNKS     ✅ A B C D E F G H Ia1 Ia2 Ib Ic1-7 Ja Jb Jc K K-events L L-scanout N O ...
                   ⬜ Id  M  Z
CURRENT CHUNK      <chunk-name> — <one-line scope> ✓ landed (or ⏳ in flight)
[optional]
EXIT CRITERIA      ✅ <criterion 1>  ⬜ <criterion 2 — awaits <successor chunk>>  ...
```

The `↑ here` arrow points at the active phase. `████░░░░` bars are eyeballed against chunks-landed vs queued — they don't need precision; their job is a visual sense of "deep into Phase N" vs "early in Phase N". The chunks line uses alphabetic / chunk-name shorthand consistent with the phase status doc.

Refresh the visualization in `project_active.md` at every session boundary so a fresh session opens with the picture already drawn. **Codify this section into the CLAUDE.md template too** — every project's CLAUDE.md should contain its own roadmap-viz example so the conventions don't drift across sessions.

## Anti-patterns

- Moving past Gate 1 with an unclear vision.
- Moving past Gate 2 with novel angles that could be demoted to MATCH.
- Moving past Gate 3 with an ARCH that hasn't decided the hardest open questions.
- Writing CLAUDE.md before ARCH is signed off (the framework depends on what's being framed).
- Skipping the audit policy because "we'll add it later" — it has to be in place from day one.
- Starting implementation during the bootstrap.
- Accepting "no invariants apply" — every low-level system has load-bearing invariants; if none are obvious, the design hasn't gone deep enough.

## Success criteria for the bootstrap

- [ ] VISION.md signed off.
- [ ] COMPARISON.md + NOVEL.md signed off.
- [ ] ARCHITECTURE.md + ROADMAP.md signed off — binding.
- [ ] CLAUDE.md in place, tailored to this project's invariants + commands + audit surfaces.
- [ ] specs/ + docs/ + memory/ scaffolded.
- [ ] phase1-status.md template present.
- [ ] TLA+ tools installed + runnable.
- [ ] Initial commit(s) landed with per-phase detail.
- [ ] Memory + next-session pickup written.
- [ ] User confirms readiness for Phase 1 implementation.
