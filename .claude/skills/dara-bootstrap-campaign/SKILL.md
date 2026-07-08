---
name: dara-bootstrap-campaign
description: >-
  The executable, decision-gated campaign for Dara's hardest live problem (as
  of 2026-07-08): the project has no code and an unconfirmed purpose, and the
  library's domain-dependent skills are deferred — a session must establish
  what Dara IS and ground the library in it without fabricating anything.
  Load this when: asked to "move Dara forward", define the domain, add the
  first code, unblock the deferred skills (dara-domain-reference,
  dara-config-and-flags, dara-proof-and-analysis-toolkit), or ingest a
  newly-revealed codebase. Triggers: "what should Dara be", "start building",
  "the founder answered", "add the real repo", "bootstrap".
---

# Dara Bootstrap Campaign

Numbered phases, exact commands, expected observations at every gate, and
explicit branches. Success at the end is **checklist-measured, never judged
by eye**. Every state change routes through `dara-change-control`.

**The problem, precisely:** Dara's domain is undefined and the greenfield
assumption is unconfirmed (open ledger entry `dara-failure-archaeology` E5).
Three skills are deferred because writing them now would be fabrication
(charter Invariant 4). This campaign closes E5 and unblocks them.

## Phase 0 — Verify the campaign's premise (always run first)

```bash
cd /home/user/Dara
git fetch origin 2>/dev/null; git ls-remote origin
find . -type f -not -path './.git/*'
```

**Expected as of 2026-07-08:** only `claude/*` branch refs (no `main`), and
only `.claude/**` files (skills + a local settings file). If you see that →
premise holds, continue to Phase 1.

**GATE — if you see anything else** (a `main` branch, source code, a README):
the premise has changed; parts of this campaign may already be done. STOP and
run the incoming-principal sweep (Phase 2B) on what you find BEFORE touching
anything, then update the charter's Section 1 (class B/C per
`dara-change-control`) and re-enter this campaign at Phase 3.

## Phase 1 — Founder interview

These questions are unanswered as of 2026-07-08. Every downstream branch
depends on them; do not substitute guesses.

1. **Where does the real code live?** (a) another repo — name it so it can be
   added to the session; (b) nowhere — greenfield, confirm it; (c) local and
   unpushed — push it; (d) this repo IS the product (skills-only).
2. **What is Dara's domain and purpose?** The problem it solves, intended
   stack, and what "done well" looks like.
3. **Who consumes this library** (Sonnet-class sessions / junior engineers /
   founder-as-operator), and what do they NOT know?
4. **What is the hardest live problem or next milestone** once the domain is
   set? (Becomes the next campaign skill.)
5. **What unwritten rules exist** — things never to do that no doc states?

**GATE — asking mechanics:** attempt the interactive question tool ONCE. If
it fails with a permission-stream error, that is known-open incident E2
(`dara-failure-archaeology`): ask the questions as plain text in your reply
and END THE TURN. **Do not proceed to Phase 2 on guessed answers** — labeled
assumptions were acceptable for founding scaffolding; they are NOT acceptable
for domain content (Invariant 4).

## Phase 2 — Ingest the answers

**Branch on answer 1:**

**2A. Code exists elsewhere / gets pushed** → add/fetch it, then run the
incoming-principal sweep:

```bash
# Identity and intent
cat README* 2>/dev/null; ls docs/ 2>/dev/null
# Build system and manifests
ls package.json pyproject.toml Cargo.toml Makefile go.mod 2>/dev/null
# How tests are ACTUALLY run (read CI, not just the README)
ls .github/workflows/ 2>/dev/null && cat .github/workflows/*.y*ml
# History: what changed, what got reverted, what stalled
git log --oneline --stat -20
git log -i -E --grep='revert|rollback|undo' --oneline
git branch -r --no-merged 2>/dev/null   # stalled work
# Pain hotspots
rg -in 'TODO|FIXME|HACK|XXX' --stats
```

Expected: a real picture of build/test/history/debt. Each finding feeds
Phase 3's skill updates — with every command re-run and verified in THAT
repo, not assumed from here.

**2B is the same sweep** when Phase 0's gate found code in THIS repo.

**2C. Greenfield confirmed** → the founder's answer to question 2 becomes the
charter's domain statement (next phase). No sweep to run; do not invent one.

## Phase 3 — Update the library through change control

1. **Charter update** (domain statement, resolved assumption): doctrine
   change → **class C, founder sign-off** per `dara-change-control`.
2. **Close the ledger:** update `dara-failure-archaeology` E5 to settled,
   with the founder's answer as evidence (class B).
3. **Author the unblocked skills** via the parallel-authoring workflow
   (`dara-orchestration-workflows`), format per `dara-skill-authoring`:
   - `dara-domain-reference` — the domain theory a mid-level person lacks,
     as it applies HERE (from answer 2 + sweep findings)
   - `dara-config-and-flags` — every real configuration axis found in 2A/2B
     (only if code exists; a greenfield project has none yet — skip and note)
   - `dara-proof-and-analysis-toolkit` — the domain's first-principles
     analysis methods, each with a worked example from the actual repo
   - New rows in `dara-debugging-playbook` for code-level failure modes, and
     `dara-validation-and-qa` §6 (what testing means here) gets written
4. **Review:** every new/updated skill passes the three-lens review
   (`dara-validation-and-qa` §3) before merge. Expected output: zero
   blocking findings.

## Phase 4 — Promotion: measurable completion checklist

The campaign is DONE when every box checks (run the commands; do not eyeball):

- [ ] Charter contains a founder-confirmed domain statement:
      `rg -n 'UNDEFINED' .claude/skills/dara-charter-and-state/SKILL.md`
      returns nothing
- [ ] E5 settled: `rg -A6 '^### E5' .claude/skills/dara-failure-archaeology/SKILL.md | rg 'Status: settled'` returns a match
- [ ] No load-bearing unconfirmed assumptions remain:
      `rg -n 'ASSUMPTION' .claude/skills/ -g 'SKILL.md'` returns only entries
      that are dated AND non-load-bearing (each survivor justified in the
      campaign's closing report)
- [ ] The charter's deferred list is empty or each remaining entry
      re-justified in writing
- [ ] A fresh Sonnet-class session, given only the repo, can run THIS skill's
      Phase 0 and correctly conclude the campaign is complete
- [ ] The founder has named the next hardest problem (Phase 1 q4) and it is
      recorded as the seed of the next campaign skill

## Known wrong paths — fenced off

1. **Writing domain skills before founder confirmation.** Fabrication; the
   founding failure mode this library exists to prevent (E1). No exceptions
   for "obvious" domains.
2. **Treating a task description's framing as ground truth.** E1 again.
   Phase 0 exists precisely because framings lie.
3. **Force-pushing or rewriting history to "clean up" the bootstrap.**
   Forbidden by `dara-change-control` rule 3; the messy founding history IS
   the archaeology.
4. **Proceeding past Phase 1 on assumed answers** because the question tool
   is broken. The workaround (ask inline, end turn) costs one turn; a wrong
   domain costs the library.

## When NOT to use this skill

- Day-to-day work that doesn't touch the domain question → the relevant
  sibling (`dara-change-control`, `dara-debugging-playbook`, ...)
- The general method for turning hunches into results →
  `dara-research-methodology`
- Looking for open research problems → `dara-research-frontier`

## Provenance and maintenance

- Authored 2026-07-08. Phase 0's commands and expected outputs were run live
  that day; Phase 1's questions are the real unanswered founder questions
  from the founding session (blocked by E2); Phase 2A's sweep commands are
  standard git/rg inspection verified for syntax in this environment
  (`git log --grep`, `rg --stats` exercised 2026-07-08).
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| Premise (empty repo, no main) | `git ls-remote origin && find . -type f -not -path './.git/*'` |
| E5 still open | `rg -A6 '^### E5' .claude/skills/dara-failure-archaeology/SKILL.md \| rg 'Status'` |
| E2 still open (question tool) | attempt one interactive question; note result in the ledger |
| Deferred list unchanged | `rg -n 'deferred' .claude/skills/dara-charter-and-state/SKILL.md` |

- This skill RETIRES when Phase 4 passes: mark it retired in the frontmatter
  description (class C — it is doctrine), keep the file as history, and seed
  the next campaign from Phase 1 question 4's answer.
