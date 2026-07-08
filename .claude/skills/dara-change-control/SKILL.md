---
name: dara-change-control
description: >-
  How changes are classified, gated, and executed in the Dara repo. Load this
  BEFORE doing anything that changes repo state: creating a branch, staging
  files, committing, pushing, creating or updating a pull request, or editing
  any existing skill. Also load it when: a task asks you to "clean up" history,
  force-push, or merge; you need to know who must approve a doctrine change;
  a push fails and you must decide whether to retry; or you are unsure whether
  a change needs review before merge. Triggers: "commit", "push", "branch",
  "PR", "merge", "revert", "edit a skill", "is this allowed".
---

# Dara Change Control

This skill owns the rules for changing anything in `flakkylanre-maker/Dara`.
Today the repo's only product is the skill library (see
`dara-charter-and-state`), so change control is skill-library change control,
plus placeholders for the day code arrives. Nothing in any other skill may
route around this one — that is charter Invariant 3.

## 1. Change classification

Classify every change BEFORE touching the repo. The class determines the gate.

| Class | Example | Gate before merge/push |
|---|---|---|
| A. New skill | Adding `.claude/skills/<new>/SKILL.md` | Author verification (every command run) + three-lens review (see `dara-validation-and-qa`) |
| B. Factual edit to an existing skill | Updating a stale command, re-dating a verified fact | Author verification of the changed facts; note the edit in the commit message |
| C. Doctrine change | Editing charter invariants, this skill's rules, evidence standards in `dara-validation-and-qa`, or retiring/renaming a skill | FOUNDER SIGN-OFF required. Propose in your reply, wait for explicit approval. Never self-approve. |
| D. Code change | Any future source code | UNDEFINED as of 2026-07-08 — to be defined when code exists, via `dara-bootstrap-campaign` Phase 3. Until then, treat any code-authoring task as a doctrine question (class C). |

If a change straddles classes, the strictest class wins.

## 2. Non-negotiables

Each rule below carries its rationale and, where one exists, the real incident
behind it. Breaking one is never a local judgment call.

1. **Verify repo state before trusting any task's framing.**
   Run `git ls-remote origin` and `find . -type f -not -path './.git/*'`
   before building on what a task description says the repo contains.
   *Incident (2026-07-07/08):* a task brief assumed a mature codebase; the repo
   was actually empty. Full record: `dara-failure-archaeology`.
2. **Work only on your designated `claude/*` branch.**
   Remote sessions are assigned a branch. Never push to a different branch
   without explicit founder permission. There is no `main` branch yet
   (verified 2026-07-08) — do not create one on your own initiative; that is a
   class C change.
3. **Never force-push or rewrite pushed history.**
   Pushed history is the project's memory (charter Invariant 2). A rewrite can
   silently destroy the only durable record. The sole exception is the
   documented merged-PR restart pattern, and only with `--force-with-lease`
   on history that is already merged elsewhere.
4. **Stage specific paths; never `git add -A` or `git add .` blindly.**
   Sessions accumulate stray local files (e.g. `.claude/settings.local.json`
   exists locally and is NOT part of the library). Blind staging publishes
   them. Run `git status --short` first; add only the files you intended to
   change: `git add .claude/skills/<name>/SKILL.md`.
5. **Push with retry ONLY on network failures.**
   `git push -u origin <branch>`; if it fails with a network error, retry up
   to 4 times with exponential backoff (2s, 4s, 8s, 16s). If it fails with an
   authorization, policy, or non-fast-forward error, do NOT retry — diagnose
   via `dara-debugging-playbook` and report.
6. **Never create a pull request unless the founder explicitly asks.**
   PRs are outward-facing artifacts of founder intent. `gh` is not installed
   in this environment; PR operations go through the GitHub MCP tools — see
   `dara-environment-and-operations`.
7. **Commits explain WHY, not just what.**
   The commit log is part of project memory. A future session must be able to
   reconstruct intent from it. One logical change per commit.
8. **Push early during long work.**
   The container is ephemeral; unpushed work can be lost to a session limit or
   reclaim. *Incident (2026-07-08):* eleven parallel authoring agents were
   terminated mid-work by an account session limit; only work already written
   to disk survived. Commit and push in batches as soon as a unit of work is
   verifiably complete.

## 3. Standard flow (copy-paste anatomy)

Terms: *staging* = telling git which file changes go into the next commit;
*fast-forward* = a push the remote can accept without merging.

```bash
# 0. Orient (always)
git status --short
git ls-remote origin

# 1. Confirm you are on the designated branch (create it only if truly absent)
git branch --show-current

# 2. Stage ONLY the intended files
git add .claude/skills/<name>/SKILL.md

# 3. Commit with a why-bearing message
git commit -m "<what and why, one logical change>"

# 4. Push with -u; on NETWORK failure retry with backoff (2s/4s/8s/16s, max 4)
git push -u origin <designated-branch>
```

Expected push output ends with the branch tracking line, e.g.
`branch '<branch>' set up to track 'origin/<branch>'.` Anything else → stop
and triage (`dara-debugging-playbook`).

## 4. Review requirement for skills

Every class A skill, and any class B edit that changes more than dates, passes
the three-lens review before it is considered certified: FACTUAL (commands and
paths re-verified), DOCTRINE (no contradiction with charter or siblings, no
missing gate), USABILITY (trigger quality, one home per fact, self-contained).
The full checklists and severity thresholds are owned by
`dara-validation-and-qa`; the multi-agent way to run the review is owned by
`dara-orchestration-workflows`.

## 5. When NOT to use this skill

- How to verify a fact or what counts as evidence → `dara-validation-and-qa`
- Environment mechanics (proxy, MCP tools, what's installed) →
  `dara-environment-and-operations`
- A push or tool is failing and you need triage → `dara-debugging-playbook`
- Writing the content of a skill → `dara-skill-authoring`
- What the invariants are and why → `dara-charter-and-state`

## 6. Provenance and maintenance

- Authored 2026-07-08 against the live repo (branch
  `claude/fable-5-decision-skill-hbdiak`, then at commit `1b320e9`). Command
  syntax in Section 3 was exercised in the authoring session (the same
  add/commit/push flow produced commits `ccbe52d` and `1b320e9`). The
  git-workflow conventions (designated branches, retry backoff, no-PR rule)
  come from the remote-session operating rules in force on 2026-07-08.
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| No `main` branch yet | `git ls-remote origin` |
| Stray local files exist | `git status --short` and `ls .claude/` |
| Designated branch | `git branch --show-current` |
| Commit log conventions holding | `git log --oneline -10` |

- If the repo gains a `main` branch, CI, or code, Sections 1–3 need a class C
  revision — do not patch them piecemeal.
