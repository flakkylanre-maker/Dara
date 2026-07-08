---
name: dara-failure-archaeology
description: >-
  Dara's incident chronicle and the protocol for keeping it: every significant
  investigation, dead end, reverted decision, and settled question, recorded
  as symptom → root cause → evidence → status so no session re-fights a
  settled battle. Load this BEFORE starting any investigation (search it for
  your symptom first), AFTER any incident that cost >30 minutes or overturned
  an assumption, and whenever you're about to conclude something that
  contradicts an existing entry. Triggers: "has this happened before",
  "postmortem", "why was this decided", "log this incident", "dead end",
  "this contradicts what the docs say".
---

# Dara Failure Archaeology

This is the project's memory of what went wrong and what got settled. Dara is
young (founded 2026-07-07), so the chronicle is short — that is honest, not
thin. The value compounds: every entry here is a battle no future session has
to re-fight. The protocol matters more than today's entry count.

## 1. Ledger protocol

**Entry format** (use exactly this shape):

```
### E<N>: <one-line title> (<date>)
- Symptom: what was observed, verbatim error text where available
- Root cause: the mechanism, or "unknown" — never a guess dressed as a cause
- Evidence: the commands/observations that establish it (reproducible)
- Status: open | settled | retired
- Consequence: what rule/skill changed because of this, if any
```

**Rules:**

1. **Entry bar:** anything that cost >30 minutes, any reverted change, any
   rejected design, any assumption later proven wrong, any incident that
   changed a rule. When in doubt, write it.
2. **Append-mostly:** status updates and new evidence are edits; deletions
   are forbidden. A settled wrong theory is still valuable — it stops the
   next session from proposing it fresh.
3. **Route through change control:** new entries and status changes are
   class B edits per `dara-change-control`.
4. **Statuses:** *open* = mechanism unconfirmed or fault still live;
   *settled* = root cause established with evidence; *retired* = the failure
   mode can no longer occur (say why).

## 2. Consultation protocol (before you investigate)

1. `rg -i '<your symptom keyword>' .claude/skills/dara-failure-archaeology/`
   — search this file before spending time on any symptom.
2. If an entry matches: follow its consequence/workaround; do not re-derive.
3. If your findings CONTRADICT a settled entry, that is significant — you may
   have found real drift. Do not silently edit the entry; raise it as a
   doctrine-adjacent change (class C gate in `dara-change-control`) with your
   new evidence attached.

## 3. The chronicle

### E1: Task brief assumed a mature codebase; repo was empty (2026-07-07/08)
- Symptom: a task described Dara as a project with a build system, tests, CI,
  history, and TODO hotspots to mine. None of it existed.
- Root cause: task framing written without checking the repo (settled).
- Evidence: `git ls-remote origin` returned exactly one ref
  (`claude/fable-5-decision-skill-hbdiak`); `find . -type f -not -path
  './.git/*'` returned a single skill file.
- Status: settled.
- Consequence: charter Invariant 1 ("ground truth only") and change-control
  non-negotiable 1 ("verify repo state before trusting any task's framing").

### E2: Interactive question tool broken — "permission stream closed" (2026-07-08)
- Symptom: `AskUserQuestion` failed twice, identically:
  `Tool permission request failed: Error: Tool permission stream closed
  before response received`.
- Root cause: unknown — infrastructure-side; not caused by question content
  (two different attempts, same failure).
- Evidence: two consecutive failures in the 2026-07-08 session; ordinary
  tools kept working throughout.
- Status: open.
- Consequence: standing workaround — retry once, then ask questions as plain
  text in the reply and proceed on labeled `ASSUMPTION`s
  (`dara-debugging-playbook` row 2). Blocks founder confirmation of E5.

### E3: GitHub MCP server flaps mid-session (2026-07-08)
- Symptom: `mcp__github__*` tools repeatedly announced as disconnected, then
  reconnected, within a single session; tool calls in the gap fail as
  "not found".
- Root cause: unknown — environmental/server-side; recurred across the
  session regardless of activity.
- Evidence: multiple disconnect/reconnect notices observed 2026-07-08.
- Status: open.
- Consequence: `dara-debugging-playbook` row 3 — re-search after reconnect;
  never conclude a capability is gone from one failed lookup.

### E4: `list_repos` unavailable for account-owned sessions (2026-07-08)
- Symptom: `list_repos` returned
  `failed to list repos: list_repos: not available for account-owned sessions`.
- Root cause: session-type limitation, not an outage (settled — the error is
  a policy message, and it reproduced deterministically).
- Evidence: the call above, 2026-07-08.
- Status: settled.
- Consequence: repo discovery beyond the scoped repo requires the founder to
  name repos explicitly (`dara-debugging-playbook` row 7).

### E5: Founding assumption — Dara is greenfield (2026-07-08)
- Symptom: not a failure but an unconfirmed load-bearing belief: that Dara is
  a brand-new project with no codebase elsewhere, and this library is its
  founding discipline layer.
- Root cause: n/a — assumption, adopted because the repo is verifiably empty
  and the founder could not be asked (see E2).
- Evidence: empty repo per E1's commands; founder confirmation pending —
  the interview questions live in `dara-bootstrap-campaign` Phase 1.
- Status: **open**.
- Consequence: charter Invariant 4 (domain content deferred); every skill
  leaning on the assumption carries a dated `ASSUMPTION` label, which doubles
  as the revisit checklist if the founder contradicts it.

### E6: Parallel authoring agents killed by account session limit (2026-07-08)
- Symptom: all 11 parallel skill-authoring subagents terminated with API
  error `You've hit your session limit · resets 1:10am (UTC)`; the lead
  session kept working.
- Root cause: account-level usage cap; N parallel agents multiply token burn
  ~N× (settled).
- Evidence: 11 simultaneous failure notifications, 2026-07-08; one agent had
  already written `dara-charter-and-state/SKILL.md` to disk — it survived and
  shipped.
- Status: settled (the cap is a fact of the account, not a bug).
- Consequence: change-control non-negotiable 8 ("push early"); playbook row 6
  (salvage-then-inline); orchestration guidance to weigh agent count against
  account limits (`dara-orchestration-workflows`).

## 4. When NOT to use this skill

- Live triage of a failure happening right now → `dara-debugging-playbook`
  (come back here afterward to log it)
- Method for NEW investigations (hypotheses, experiments) →
  `dara-research-methodology`
- The rules an entry's "consequence" created → the owning skill
  (`dara-charter-and-state`, `dara-change-control`)

## 5. Provenance and maintenance

- Authored 2026-07-08. Every entry is first-hand from the 2026-07-07/08
  sessions; all evidence commands were run live and their outputs observed
  by the author before being recorded.
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| E1 evidence still reproduces (until code lands) | `git ls-remote origin` |
| E4 limitation still holds | attempt `list_repos` once and read the error |
| E5 still open | `rg -c 'ASSUMPTION' .claude/skills/ -g 'SKILL.md'` (nonzero = still open) |
| Entry numbering intact | `rg '^### E' .claude/skills/dara-failure-archaeology/SKILL.md` |

- E2/E3/E5 are OPEN: whichever session observes them resolved (question tool
  works, MCP stable across a session, founder confirms/denies greenfield)
  must update the status with evidence — that update is exactly the kind of
  work this library exists to capture.
