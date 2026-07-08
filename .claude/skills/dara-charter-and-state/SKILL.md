---
name: dara-charter-and-state
description: >-
  The architecture contract for the Dara project: what Dara is today, the
  founding invariants every session must honor, the map of the skill library,
  and the project's known weak points. Load this FIRST in any new session on
  the Dara repo, and whenever you: start a task with zero context; wonder
  "what is Dara / what does this repo contain / is there a main branch /
  where is the code"; receive a task brief that assumes Dara has a codebase,
  domain, CI, or users; need to decide whether some content is safe to write
  down or must stay deferred; or need to know which sibling skill covers a
  topic. Symptoms that should trigger it: an empty-looking repo, a task
  framing that contradicts what you see on disk, or uncertainty about the
  project's purpose.
---

# Dara Charter and State

This skill is the project's contract with every future session. Dara has no
source code yet, so its load-bearing "architecture" is the project itself:
what exists, what must stay true, and where everything else is written down.
Read this once, then jump to the sibling skill that owns your task.

## 1. What Dara is today (verified 2026-07-07)

Dara is the repository `flakkylanre-maker/Dara`. As of 2026-07-07 its ONLY
artifact is a skill library under `.claude/skills/`. There is no source code,
no README, no CI, no tests, no issues, and no `main` branch.

Verified state (all commands run from the repo root on 2026-07-07):

```
git ls-remote origin
```

returned exactly one branch ref — `refs/heads/claude/fable-5-decision-skill-hbdiak`
at commit `ccbe52d` — plus its matching `HEAD` line. One branch, no `main`.

```
find . -type f -not -path './.git/*'
```

returned exactly two files:

- `.claude/skills/fable-decision-style/SKILL.md` — the first skill (commit
  `ccbe52d`, dated 2026-07-07)
- `.claude/settings.local.json` — a small local permissions file

```
git log -1 --format='%h %ad %s' --date=short
```

returned `ccbe52d 2026-07-07 Add fable-decision-style skill documenting
answering and decision-making approach`.

**Purpose/domain: UNDEFINED as of 2026-07-07.** What Dara will actually build
or do has not been stated by the founder.

> ASSUMPTION (2026-07-07, unconfirmed): Dara is greenfield — a brand-new
> project with no prior codebase elsewhere — and this skill library is its
> founding discipline layer, written before the code so that cheaper models
> can carry the project at a principal-engineer standard. The founder has not
> confirmed this. Treat every downstream decision that leans on it as
> revisable. `dara-bootstrap-campaign` owns the process for getting it
> confirmed.

The founder (the repo owner) operates Dara through remote Claude Code
sessions. Each session runs in an ephemeral Linux container with a fresh
clone; nothing outside the pushed repo survives the session. See
`dara-environment-and-operations` for the full environment runbook.

## 2. Founding invariants

These four rules are the project's constitution. Each exists because of a
real incident or environmental fact, not taste. Breaking one is never a
local judgment call — if an invariant seems wrong for your task, stop and
raise it with the founder instead.

### Invariant 1: Ground truth only

No skill — and no other repo artifact — may state a fact, command, path, or
capability that was not verified in this repo or environment at authoring
time. Unverifiable content is either omitted or labeled
`ASSUMPTION (<date>, unconfirmed)`.

**Why.** This library exists to steer Sonnet-class models. A cheaper model
following a runbook does not second-guess it; a wrong command or invented
path misdirects it silently, and the failure surfaces far from the lie. The
motivating incident (2026-07-07): a task brief assumed a mature codebase, and
only an explicit `git ls-remote origin` check revealed the repo was empty.
Had that framing been written into a skill unverified, every later session
would have inherited the fiction. Corollary: **verify repo state before
trusting any task's framing** — run the Section 1 commands first. Evidence
standards are owned by `dara-validation-and-qa`; the incident record lives in
`dara-failure-archaeology`.

### Invariant 2: The skill library is the project's memory

Anything worth keeping — decisions, incidents, procedures, assumptions —
must be written into a skill (or another committed repo file) and pushed.
Session knowledge that is not written down does not exist.

**Why.** The environment makes this literal, not rhetorical: each session's
container is reclaimed when the session ends, and the next session starts
from a fresh clone with zero conversational context. There is no shared
scratch space, no persistent chat history the next model can rely on. The
repo is the only durable medium, and skills are its only structured content
today (verified 2026-07-07: the repo contains nothing else — see Section 1).

### Invariant 3: All repo changes route through `dara-change-control`

Any procedure, in any skill or session, that mutates repo state — branch,
commit, push, or pull request — follows the rules in `dara-change-control`.
No skill may define its own shortcut around it.

**Why.** With no CI, no tests, and no reviewers-in-the-loop (single founder,
see Section 4), the change-control rules are the ONLY gate between a
mistaken session and a corrupted project memory. Two environmental facts
raise the stakes: pushes can fail transiently (the push procedure includes
retry handling — see `dara-change-control`), and pull requests are never
created unless the founder explicitly asks. A session that improvises its
own git workflow can strand work or publish something the founder never
sanctioned.

### Invariant 4: Domain-dependent content stays deferred

Until the founder confirms Dara's domain, no one authors content that
depends on knowing it. As of 2026-07-07 three skills are explicitly deferred
for this reason: `dara-domain-reference`, `dara-config-and-flags`, and
`dara-proof-and-analysis-toolkit`. `dara-bootstrap-campaign` defines when and
how they get unblocked.

**Why.** This is Invariant 1 applied at the scale of a whole document: with
the domain undefined, a domain skill could only be fabricated, and a
fabricated reference is worse than an empty slot because future sessions
would build on it with full confidence. The deferral is cheap; unwinding a
library built on an invented domain is not.

## 3. Library map

The roster below is the complete planned library as of 2026-07-07. Most
entries are being authored in parallel with this one — check what actually
exists on disk with `ls .claude/skills/` (on 2026-07-07 that returned only
`fable-decision-style`; expect more after the authoring branches merge).

| Skill | Load it when... |
|---|---|
| `dara-charter-and-state` (this skill) | Starting any session; orienting with zero context; checking an invariant; finding which sibling owns a topic |
| `dara-change-control` | Doing ANYTHING that changes repo state: branching, committing, pushing, PRs, review gates |
| `dara-environment-and-operations` | Working with the session environment: container, tooling, proxy, GitHub MCP tools, where artifacts land |
| `dara-debugging-playbook` | Hitting a failure or odd symptom in-session and needing symptom-to-triage steps |
| `dara-failure-archaeology` | Recording a new incident, or reading the real incident ledger to date |
| `dara-skill-authoring` | Writing or editing any skill in this library (house format and template) |
| `dara-validation-and-qa` | Deciding what counts as evidence; verifying claims; running review checklists |
| `dara-orchestration-workflows` | Coordinating multiple agents on authoring or review work |
| `dara-bootstrap-campaign` | Driving the project forward: confirming the domain, unblocking deferred skills, grounding the library |
| `dara-research-methodology` | Forming hypotheses, managing an idea's lifecycle, making or auditing claims |
| `dara-research-frontier` | Looking for open problems to work on in this repo |
| `fable-decision-style` | Answering questions, choosing between approaches, deciding act-vs-ask, reporting results |

Deferred until the domain is confirmed (do NOT author; see Invariant 4):
`dara-domain-reference`, `dara-config-and-flags`,
`dara-proof-and-analysis-toolkit`.

One home per fact: each skill owns its row's scope. If you find the same
fact stated in two skills, that is a bug — fix it via `dara-change-control`
so one skill owns it and the other points there.

## 4. Known weak points (as of 2026-07-07)

State these plainly to yourself before planning work; several of them
invalidate common defaults.

1. **Domain undefined.** Dara's purpose is not stated anywhere. Any plan
   that presumes a product, user, or tech stack is speculation.
2. **Founder's intent unconfirmed, and the confirmation channel is broken.**
   The interactive question tool (`AskUserQuestion`) failed twice on
   2026-07-07 with "Tool permission stream closed before response received"
   — status open. Workaround: ask questions as plain text in your reply,
   proceed on explicitly documented assumptions, and label them per
   Invariant 1. Full incident record: `dara-failure-archaeology`.
3. **No `main` branch.** Verified 2026-07-07 via `git ls-remote origin`:
   the only branch is `claude/fable-5-decision-skill-hbdiak`. There is no
   integration target yet; do not assume one exists or invent one — branch
   strategy is owned by `dara-change-control`.
4. **Single-founder bus factor.** One person holds all intent and all merge
   authority. If they are unavailable, nothing can be confirmed or merged.
   This makes Invariant 2 (write everything down) the mitigation, not a
   nicety.
5. **The greenfield assumption is an assumption.** Section 1's working
   assumption could be wrong — Dara might be a rewrite, a fork-to-be, or a
   placeholder. Every skill leaning on it carries the dated label; if the
   founder contradicts it, those labels are the checklist of what to revisit.
6. **The library is self-referential and unproven.** As of 2026-07-07 no
   skill here has been exercised by a later cold-start session. Treat the
   library itself as a candidate system, not a validated one, until
   `dara-validation-and-qa` procedures have been run against it in anger.

## 5. When NOT to use this skill

This skill tells you what is true and what must stay true. It does not tell
you how to do anything. For procedures, go to the owner:

- Making any repo change (branch/commit/push/PR) → `dara-change-control`
- Environment mechanics: proxy, MCP tools, container layout, artifacts →
  `dara-environment-and-operations`
- Triaging a live failure → `dara-debugging-playbook`
- Reading or appending incident history → `dara-failure-archaeology`
- Writing a new skill → `dara-skill-authoring`
- Verifying a claim or reviewing work → `dara-validation-and-qa`
- Running multi-agent work → `dara-orchestration-workflows`
- Confirming the domain / unblocking deferred skills →
  `dara-bootstrap-campaign`
- Research and claims discipline → `dara-research-methodology`;
  open problems → `dara-research-frontier`
- Judgment and communication style → `fable-decision-style`

If your question is "how", you are in the wrong skill.

## 6. Provenance and maintenance

- Authored 2026-07-07 in a remote Claude Code session, against the live repo
  at commit `ccbe52d` (the repo's only commit at the time). Every command in
  this file was run in that session and its output confirmed before being
  written down. Incidents cited are from the 2026-07-07 session history;
  `dara-failure-archaeology` is their system of record.
- This skill goes stale the moment the repo gains a domain, a `main` branch,
  or source code. Re-verify before trusting Sections 1, 3, and 4:

| Claim | One-line re-verification (run from repo root) |
|---|---|
| Branch list / no `main` | `git ls-remote origin` |
| Repo contents (files) | `find . -type f -not -path './.git/*'` |
| Latest commit and date | `git log -1 --format='%h %ad %s' --date=short` |
| Which skills exist on disk | `ls .claude/skills/` |
| Local branch state | `git branch -a` |

- If any re-verification contradicts this file, the file is wrong: update it
  (Section 1 and 4 first) via `dara-change-control`, and re-date every claim
  you touch.
