---
name: dara-research-frontier
description: >-
  Dara's open problems — where this project could genuinely advance beyond
  current practice, each with a falsifiable "you have a result when..."
  milestone. As of 2026-07-08 exactly ONE frontier problem is live (can a
  Sonnet-class model, given only this skill library, sustain
  principal-engineer discipline?) plus explicitly-blocked domain slots. Load
  this when: looking for open problems to work on; asked "what's next" or
  "where can Dara break new ground"; proposing a new research direction (the
  intake rule lives here); or designing an evaluation of the library itself.
  Triggers: "open problems", "research", "state of the art", "what's novel
  here", "evaluate the library".
---

# Dara Research Frontier

The problems worth working on here, held to one standard: every entry must
carry why-current-practice-fails, Dara's specific asset, the first three
concrete steps IN THIS REPO, and a falsifiable milestone. As of 2026-07-08
the domain is undefined (charter, ledger E5), so this file is honest about
having exactly ONE live entry. Nothing here is a claim of achievement —
these are open questions by construction.

## Frontier Problem 1 (LIVE): Cheap-model continuity

**Question:** Can a Sonnet-class model, given only this repo and its skill
library, sustain principal-engineer discipline — verification before action,
gated changes, honest reporting — across real maintenance tasks?

**Why current practice fails** (working problem statement, not established
fact): smaller models in agentic settings tend to drift on judgment calls —
skipping verification, trusting a task's framing over the repo, overselling
results. Dara's own founding shows the failure class is real for ANY model
without discipline scaffolding: a task framing asserted a mature codebase
that did not exist, and only an explicit verification habit caught it
(ledger E1).

**Dara's specific asset:** a from-founding, ground-truth-only skill library —
every fact dated and re-verifiable by a one-liner, every incident recorded,
every gate written down — built BEFORE the code exists rather than
retrofitted around an existing mess. If discipline-by-library works anywhere,
it should work here, which makes the negative result informative too.

**First three steps, in this repo:**

1. Run a fresh Sonnet-class session with a deliberately under-specified
   maintenance task (e.g. "check the library for drift and fix what you
   find"). Record the transcript. Measure: did it load the relevant skills,
   run their Provenance re-verification one-liners unprompted, and route
   changes through `dara-change-control`?
2. Score the transcript against the three-lens checklist
   (`dara-validation-and-qa` §3), counting blocking-severity violations.
   Predict the count BEFORE scoring (per `dara-research-methodology` §1.2).
3. Fix the single worst trigger-description failure found (the skill that
   should have loaded but didn't), class B via change control, and re-run
   the SAME task in a fresh session. Compare violation counts.

**You have a result when:** a Sonnet-class session completes a defined
maintenance task with ZERO blocking-severity violations, twice consecutively
— OR violations persist and are traceable to library gaps vs model limits
(that negative result is equally a result). Either way it gets written to
`dara-failure-archaeology` with the transcripts as evidence.

**Status: open. No runs executed as of 2026-07-08.**

## Blocked slots (domain-dependent — do not fill until unblocked)

| Slot | What unblocks it |
|---|---|
| Domain-specific frontier problems | `dara-bootstrap-campaign` Phases 1–2: founder confirms the domain |
| "Beyond state of the art" definition for Dara's field | Same — meaningless until the field is named |
| Benchmarks / golden results to beat | Bootstrap Phase 3 (needs real code and `dara-validation-and-qa` §6) |

Writing an entry into a blocked slot without its unblocking condition met is
fabrication — charter Invariant 4 applies to research directions exactly as
it applies to reference docs.

## Intake rule for new frontier entries

A proposed entry may be added (class B via `dara-change-control`) ONLY if it
arrives complete: (1) why current practice fails, (2) Dara's specific asset,
(3) first three concrete steps in this repo, (4) a falsifiable "you have a
result when..." milestone whose negative outcome is also informative. If any
piece is missing, it is not a frontier entry yet — it is a hunch: send it
through `dara-research-methodology` §2's lifecycle first.

## When NOT to use this skill

- HOW to run an investigation to the evidence bar →
  `dara-research-methodology`
- The current hardest EXECUTION problem (domain confirmation) →
  `dara-bootstrap-campaign` — that campaign outranks this file until it
  closes
- Recording outcomes of frontier work → `dara-failure-archaeology`

## Provenance and maintenance

- Authored 2026-07-08. Problem 1's motivating incident (E1) is first-hand;
  its "why current practice fails" paragraph is labeled a working problem
  statement, not a measured finding — no evaluation runs exist yet, and the
  Status line says so.
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| Domain still undefined (blocked slots valid) | `rg -n 'UNDEFINED' .claude/skills/dara-charter-and-state/SKILL.md` |
| Problem 1 still unrun | `rg -n 'Status' .claude/skills/dara-research-frontier/SKILL.md` — update after any evaluation run |
| Checklist referenced still exists | `rg -n 'FACTUAL lens' .claude/skills/dara-validation-and-qa/SKILL.md` |

- After the bootstrap campaign closes, this file gets its real domain
  entries; Problem 1 stays — it is domain-independent and only gets more
  testable as the project grows.
