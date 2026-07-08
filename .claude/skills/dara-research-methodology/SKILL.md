---
name: dara-research-methodology
description: >-
  The discipline that turns a hunch into an accepted result in Dara: the
  evidence bar for mechanisms (one mechanism must explain ALL observations
  including negatives, survive adversarial refutation), the
  predict-numbers-before-running rule, the idea lifecycle from hunch to
  adopted change or documented retirement, and claims/no-oversell discipline
  for anything outward-facing. Load this when: forming or testing a
  hypothesis; designing an experiment; deciding whether a result is "proven";
  writing any claim of novelty or capability; or an investigation is drifting
  into guesswork. Triggers: "I think the cause is", "let's try", "prove it",
  "is this ready to claim", "experiment", "hypothesis", "retire this idea".
---

# Dara Research Methodology

The method for making DURABLE knowledge here — extending the everyday
evidence bar of `dara-validation-and-qa` with the stricter discipline that
investigations and claims need. **Honest status (2026-07-08):** this doctrine
is adopted at founding, not yet battle-tested in Dara; its first real
application was the construction of this library itself (Section 4 walks that
case). Treat it as the standard to hold, and log where it fails you
(`dara-failure-archaeology`).

## 1. The evidence bar for mechanisms

A proposed explanation ("the root cause is X", "this design works because Y")
is ACCEPTED only when all three hold:

1. **One mechanism explains ALL observations — including the negatives.**
   If your theory explains the failure but not why the failure skipped case
   Z, it is not yet the mechanism; it is a correlation with a story.
2. **The hypothesis predicted specifics BEFORE the test ran.** Write down
   what you expect to observe — numbers, exact outputs, which cases pass and
   fail — before running the experiment. Post-hoc "as expected" is worthless
   as evidence because any outcome can be narrated as expected.
3. **It survived an assigned adversarial refutation.** Someone — a second
   agent, a reviewer, or you in an explicit devil's-advocate pass with its
   own written output — whose stated job is to BREAK the conclusion: propose
   rival mechanisms and find the observation that discriminates. Reuse the
   reviewer machinery in `dara-orchestration-workflows` §4 for this.

*Negative observation* = something that did NOT fail, or did NOT change, when
the theory says it should have. Collect these deliberately; they kill more
wrong theories than positive evidence confirms right ones.

## 2. The idea lifecycle

No idea may sit unlabeled between states. The states and the only legal
transitions:

| State | Meaning | Exit |
|---|---|---|
| **Hunch** | Unwritten intuition | Write it as a hypothesis, or drop it |
| **Hypothesis** | Written: mechanism + predicted observations/numbers + the experiment that would falsify it | Run the experiment |
| **Experiment** | Isolated, reversible, on a branch, gated by `dara-change-control` | Outcome recorded — no silent abandonment |
| **Adopted** | Survived Section 1's bar; change merged via change control; the OWNING skill updated so the knowledge has a home | (Re-opened only by contradicting evidence — class C discussion) |
| **Retired** | Falsified or abandoned WITH the evidence written to `dara-failure-archaeology` | Permanent record. Retirement is a result, not a failure |

The graveyard is load-bearing: an undocumented dead idea WILL be re-proposed
by a future session that cannot know it died.

## 3. Claims discipline (no oversell)

For anything outward-facing — READMEs, papers, release notes, even confident
statements in skills:

1. Three grades, never blurred: **demonstrated here** (reproducible from
   this repo alone, with the commands), **reported elsewhere** (cite it;
   you did not verify it), **conjecture** (labeled as such).
2. Nothing is claimed as *novel* without a stated comparison point, nor as
   *working* beyond exactly what a written verification showed.
3. A public claim must be reproducible from the repo alone — if the evidence
   lives only in a session transcript, it does not support a public claim.
4. Every claim carries its date; capabilities drift.

## 4. Worked example — the founding investigation (2026-07-08)

The library's own founding, run through the lifecycle:

- **Hunch → Hypothesis:** a task brief implied "Dara is a mature codebase."
  Written prediction if true: `git ls-remote origin` shows multiple
  branches incl. an integration branch; the tree contains README, manifests,
  tests; history has depth.
- **Experiment:** run the checks (read-only, no gating needed).
- **Observation:** ONE ref (`claude/fable-5-decision-skill-hbdiak`); the tree
  held a single skill file. Every prediction of the hypothesis failed.
- **Negative observation honored:** nothing about the session contradicted
  emptiness (no hidden branches via `git log --all`) — a rival mechanism
  ("wrong clone/branch") was checked and excluded, which is Section 1.3 in
  miniature.
- **Outcome:** hypothesis RETIRED with evidence
  (`dara-failure-archaeology` E1); consequence ADOPTED through doctrine:
  charter Invariant 1 and change-control rule 1 ("verify repo state before
  trusting any framing").

Total cost of the method: two commands. Cost of skipping it: a library
fabricated against an imaginary codebase.

## 5. Where good ideas come from (scoped honestly for a young project)

Ranked by hit-rate observed so far: (1) contradictions between what a skill
says and what a session observes — each is a free, pre-validated research
lead; (2) recurring friction in sessions (anything you worked around twice);
(3) the deferred-skill list and open ledger entries (E2, E3, E5); (4) the
open problems in `dara-research-frontier`, which each arrive pre-formed with
a falsifiable milestone.

## 6. When NOT to use this skill

- Everyday claim verification and reviews → `dara-validation-and-qa` (this
  skill is for investigations and claims that must survive challenge)
- Live incident triage → `dara-debugging-playbook`
- Reading/writing the historical record → `dara-failure-archaeology`
- Picking WHICH problem to work on → `dara-research-frontier`

## 7. Provenance and maintenance

- Authored 2026-07-08. Section 4's example is first-hand and its commands
  were run live (system of record: `dara-failure-archaeology` E1). The
  doctrine itself is founding-adopted (stated in the header) — its
  effectiveness in Dara is an open question by its own standards.
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| E1 record matches Section 4 | `rg -A8 '^### E1' .claude/skills/dara-failure-archaeology/SKILL.md` |
| Lifecycle gating intact | `rg -n 'class' .claude/skills/dara-change-control/SKILL.md \| head -5` |
| Open leads still open | `rg -n 'Status: open' .claude/skills/dara-failure-archaeology/SKILL.md` |

- First session that ADOPTS or RETIRES an idea through this lifecycle should
  add it as a second worked example (class B edit) — one real case per state
  transition is the maintenance goal.
