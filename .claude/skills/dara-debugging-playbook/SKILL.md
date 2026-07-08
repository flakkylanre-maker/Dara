---
name: dara-debugging-playbook
description: >-
  Symptom-to-triage playbook for Dara's real, observed failure modes: empty or
  unexpected repo state, "tool permission stream closed" errors, MCP tools
  disappearing mid-session, git push failures, TLS/proxy errors, subagents
  dying with session-limit API errors, and skill commands that no longer work
  as documented. Load this the moment something fails or looks wrong
  in-session and you need discriminating checks before acting. Triggers: any
  error message, "this doesn't match the task description", "tool not found",
  "push failed", "certificate", "agent failed", "command in a skill is wrong".
---

# Dara Debugging Playbook

Dara has no application code yet (see `dara-charter-and-state`), so this
playbook honestly covers the project's actual operating surface: the remote
session environment, the git/GitHub workflow, and the skill library itself.
Every row below is grounded in an incident that really happened or a check
that was really run — nothing is hypothetical. When code arrives, code-level
rows get added via `dara-bootstrap-campaign` Phase 3.

*Discriminating check* = the single cheapest observation that splits the
candidate causes, run BEFORE attempting any fix.

## 1. Symptom → triage table

| # | Symptom | First discriminating check | Likely cause | Fix / workaround | Trap to avoid |
|---|---|---|---|---|---|
| 1 | Repo looks empty, or files a task mentions don't exist | `git ls-remote origin` then `git log --all --oneline` then `find . -type f -not -path './.git/*'` | Task framing is wrong (it happened here), OR wrong branch, OR you're in the wrong directory | Believe the repo, not the task. Re-read `dara-charter-and-state`; ask the founder inline if the gap is load-bearing | Building on the task's framing anyway. Story: on 2026-07-07/08 a task assumed a mature codebase; `ls-remote` showed a single skill branch. Hours of fabricated work were avoided by one command |
| 2 | `Tool permission request failed: ... stream closed before response received` on an interactive tool | Retry ONCE. Same error? | Known open infrastructure fault in the interactive question UI (observed twice, 2026-07-08) | Ask the question as plain text in your reply, end the turn or proceed on a documented `ASSUMPTION (<date>)` label | Retrying in a loop, or silently guessing the answer without labeling the assumption |
| 3 | An MCP tool that existed is suddenly "not found" | Search for it again (ToolSearch) and wait — servers announce reconnects | GitHub MCP server flap (observed repeatedly, 2026-07-08); tools vanish and return | Re-load the tool after reconnect; sequence work that doesn't need it meanwhile | Concluding the capability is gone and rearchitecting around its absence |
| 4 | `git push` fails | Read the error class: network timeout vs auth/policy denial vs non-fast-forward | Transient network → retry; policy → you're pushing somewhere you shouldn't; non-fast-forward → remote moved | Network: retry with the backoff schedule in `dara-change-control` rule 5. Policy: stop, check the designated branch (its rule 2). Non-fast-forward: fetch and reconcile — NEVER force-push (its rule 3) | Treating every failure as retryable. Auth errors don't heal with backoff |
| 5 | CLI tool fails TLS verification or gets 403/405/407 | `curl -sS "$HTTPS_PROXY/__agentproxy/status"` (verified working 2026-07-08) | Tool not using the agent proxy's CA bundle (`/root/.ccr/ca-bundle.crt`) | Per-tool fixes in `/root/.ccr/README.md`; point the tool at the CA bundle | Disabling TLS verification or unsetting `HTTPS_PROXY` — hard-forbidden (`dara-environment-and-operations` §3) |
| 6 | Subagents fail with API error "You've hit your session limit" | Note the reset time in the error text; check whether YOUR session still responds | Account usage cap; parallel agents multiply burn | Salvage: check disk for partial outputs (`git status --short`), commit+push what's complete, continue inline, resume orchestration after reset. Story: 2026-07-08, eleven authoring agents died at once; one had written its file first — it was recovered and shipped | Respawning agents immediately (they die the same way), or letting finished-but-unpushed work sit until reclaim |
| 7 | `list_repos: not available for account-owned sessions` | None needed — the message is definitive | Session-type limitation, not an outage (confirmed 2026-07-08) | Use what's in scope; ask the founder to name any other repo explicitly | Retrying, or reporting it as a transient failure |
| 8 | A command documented in a skill doesn't work | Run that skill's "Provenance and maintenance" re-verification one-liners | Skill drift: the environment or repo changed under the doc | Fix the skill (class B edit per `dara-change-control`); log the drift in `dara-failure-archaeology` if it cost real time | Working around the stale doc silently — the next session hits the same wall |

## 2. Triage discipline (applies to every row)

1. **Reproduce before fixing.** One clean reproduction beats a theory.
2. **One discriminating experiment beats ten guesses.** Pick the check that
   splits the hypothesis space, not the one that's easiest to run.
3. **Evidence must support the SPECIFIC action.** A symptom that
   pattern-matches a known failure may have a different cause; confirm the
   cause, not the resemblance, before state-changing fixes.
4. **Write it down.** Anything that cost more than ~30 minutes, changed your
   understanding, or ended in a dead end goes to `dara-failure-archaeology`
   as a ledger entry — that's the entry bar, not a suggestion.

## 3. When NOT to use this skill

- Reading or appending the historical incident record →
  `dara-failure-archaeology` (this skill is for LIVE triage)
- Environment reference (what's installed, proxy details) →
  `dara-environment-and-operations`
- Whether a fix you want to make is allowed → `dara-change-control`
- Designing a new investigation or experiment → `dara-research-methodology`

## 4. Provenance and maintenance

- Authored 2026-07-08. Rows 1–3 and 5–7 are first-hand incidents from the
  2026-07-07/08 sessions (system of record: `dara-failure-archaeology`);
  row 4's policy comes from the operating rules in `dara-change-control`;
  row 5's check and row 8's mechanism were executed live before writing.
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| Repo-state checks work | `git ls-remote origin && find . -type f -not -path './.git/*'` |
| Proxy status endpoint | `curl -sS "$HTTPS_PROXY/__agentproxy/status" \| head -c 200` |
| CA bundle path | `ls /root/.ccr/ca-bundle.crt` |
| Skill re-verification pattern | Open any sibling skill's final section and run its one-liners |

- Add a row whenever a NEW failure mode is observed (class B edit via
  `dara-change-control`); never delete a row whose failure mode was fixed —
  mark it "retired (<date>)" so the history stays legible.
