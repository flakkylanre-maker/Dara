---
name: dara-validation-and-qa
description: >-
  What counts as evidence in Dara and how work gets validated: the
  VERIFIED-vs-ASSUMPTION standard, the skill validation procedure (run every
  command, check every cross-reference), the three-lens review checklists
  (factual / doctrine / usability) with severity thresholds, and the
  certified-inventory concept. Load this when: deciding whether a claim may
  be written down; reviewing a skill or any deliverable; asked "is this
  verified?"; before declaring any work done; or when defining acceptance for
  new work. Triggers: "review this", "verify", "is this true", "evidence",
  "acceptance criteria", "certified", "QA".
---

# Dara Validation and QA

This skill owns Dara's evidence bar and review machinery. As of 2026-07-08
the project's only validated artifact class is skills — there is no code and
no test suite (see `dara-charter-and-state`). The procedures below ARE the
project's test suite until code arrives.

## 1. The evidence bar

Every claim in this repo is exactly one of:

| Grade | Meaning | Obligation |
|---|---|---|
| **VERIFIED** | A command was run and its output observed by the author | The command must be reproducible by the reader; date-stamp it |
| **ASSUMPTION** | Necessary but unconfirmed | Label inline: `ASSUMPTION (<date>, unconfirmed)` + what would confirm it |
| (neither) | — | Not written. There is no third grade |

Corollaries: "It should work" is not evidence. Success is **measured against
a stated expectation**, never judged by eye. A claim whose verification you
inherited (from another skill or session) is only VERIFIED if you can point
at its re-verification one-liner.

## 2. Skill validation procedure

Run this against any skill under review (new or edited):

1. **Execute every command in it, top to bottom**, in a fresh shell from the
   repo root. Compare observed output against any stated expectation.
   Mutating git commands: validate syntax and gating (against
   `dara-change-control`) rather than executing on the live branch.
2. **Check every cross-referenced sibling exists.** Verified working
   2026-07-08 — from the repo root:

   ```bash
   for ref in $(rg -o 'dara-[a-z-]+' .claude/skills/<name>/SKILL.md | sort -u); do
     [ -d ".claude/skills/$ref" ] && echo "OK  $ref" || echo "MISSING  $ref"
   done
   ```

   `MISSING` is acceptable ONLY for the explicitly deferred skills named in
   the charter (`dara-domain-reference`, `dara-config-and-flags`,
   `dara-proof-and-analysis-toolkit`) — and only where the text says they are
   deferred.
3. **Run the skill's own Provenance re-verification one-liners** and confirm
   they still hold.
4. **Grep for unlabeled confidence:** every `ASSUMPTION` is dated; nothing
   asserts a capability without a command behind it.

## 3. The three-lens review

Used before any class A/B skill change merges (`dara-change-control` owns
WHEN it is required; this section owns WHAT it checks). Lenses are disjoint
by design so three reviewers can run in parallel
(`dara-orchestration-workflows` owns the multi-agent mechanics).

**FACTUAL lens** — is it true?
- [ ] Every command/path/flag re-verified against the live repo (Section 2.1)
- [ ] Every date accurate; every "verified" claim actually reproducible
- [ ] Nothing invented or stale. Severity question: *would this send an
  engineer (or a Sonnet-class model) down a wrong path?* If yes → blocking

**DOCTRINE lens** — is it allowed and consistent?
- [ ] No contradiction with charter invariants or between sibling skills
- [ ] No overstated claims (unproven things labeled open/candidate)
- [ ] Anything that changes repo state routes through `dara-change-control`
  — a procedure that bypasses gating is blocking regardless of quality
- [ ] One home per fact: substantive duplication with a sibling is a finding

**USABILITY lens** — will it work for a zero-context reader?
- [ ] Description is trigger-rich (situations + phrases, per
  `dara-skill-authoring` §2)
- [ ] Self-contained: jargon defined, no reliance on session context or
  private paths
- [ ] Scannable: tables/checklists where content enumerates
- [ ] Has "When NOT to use" + correct sibling pointers, and a Provenance
  section with runnable one-liners

## 4. Severity thresholds

| Severity | Definition | Disposition |
|---|---|---|
| **Blocking** | Factual error in a command/path/claim; doctrine contradiction; missing change-control gate | Must be fixed before merge, no exceptions |
| **Important** | Weak/misleading trigger description; substantive duplication; missing mandatory section; undated volatile fact | Fix before merge when feasible; otherwise fix in the next class B pass, tracked in the review record |
| **Minor** | Style, phrasing, ordering | Log; fix opportunistically |

## 5. Certified inventory

A skill is **"certified as of <date>"** when it last passed a full three-lens
review. Certification is recorded in the review record (review summary in the
session + the commit that applied fixes), not by editing every skill. The
initial certification pass for the founding library is dated 2026-07-08.
A certified skill whose re-verification one-liners fail is NO LONGER
certified — fix it (class B) and note the drift in `dara-failure-archaeology`
if it cost real time.

## 6. When code arrives

Deliberately undefined as of 2026-07-08: test frameworks, coverage
expectations, and golden/acceptance data for code do not exist and must not
be invented here. `dara-bootstrap-campaign` Phase 3 defines how this section
gets written once the domain and stack are real. Until then, any code-shaped
task is a class C question for the founder.

## 7. When NOT to use this skill

- HOW to write a skill (format, template, style) → `dara-skill-authoring`
- WHEN a review is required, and merge mechanics → `dara-change-control`
- Running reviewers as parallel agents → `dara-orchestration-workflows`
- Evidence discipline for experiments/research claims →
  `dara-research-methodology` (it extends this bar with
  prediction-before-run and adversarial refutation)

## 8. Provenance and maintenance

- Authored 2026-07-08. The cross-reference loop in Section 2.2 was executed
  against the live library before being written down. The lens checklists
  encode the founding authoring brief's review design (2026-07-08); the
  severity model was applied in the library's own initial certification pass.
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| Cross-reference loop works | Run Section 2.2's loop against any skill |
| Deferred-skill list current | `rg -n 'deferred' .claude/skills/dara-charter-and-state/SKILL.md` |
| No unlabeled assumptions | `rg -n 'ASSUMPTION' .claude/skills/ -g 'SKILL.md'` — each hit must carry a date |
| No code/tests yet (Section 6 premise) | `find . -type f -not -path './.git/*' -not -name 'SKILL.md' -not -name '*.local.json'` (empty = premise holds) |
