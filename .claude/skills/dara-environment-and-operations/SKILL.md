---
name: dara-environment-and-operations
description: >-
  The Dara working environment: what the remote session container provides,
  which tools are installed (and which are NOT, e.g. gh), how outbound HTTPS
  and the agent proxy work, how GitHub is reached via MCP tools, and where
  artifacts must land to survive the session. Load this when: starting work in
  a fresh session; a CLI tool is missing or fails TLS verification; you need
  to call GitHub (PRs, issues, CI) and gh is absent; you wonder what survives
  the session; or you must recreate working state from scratch. Triggers:
  "command not found", "TLS/certificate error", "proxy", "where do files go",
  "gh: ABSENT", "MCP", "ephemeral container".
---

# Dara Environment and Operations

Dara is operated entirely through remote Claude Code sessions. Each session is
an isolated, ephemeral Linux container: the repo is cloned fresh to
`/home/user/Dara` when the container starts, and the container is reclaimed
after inactivity or session end. **Consequence: only content committed and
pushed to the repo survives.** That fact drives charter Invariant 2 and the
push-early rule in `dara-change-control`.

## 1. Fresh-session orientation checklist

Run these in order at the start of any session (all verified 2026-07-08):

```bash
git -C /home/user/Dara status --short        # expect: clean or known strays
git -C /home/user/Dara branch --show-current # your designated claude/* branch
git -C /home/user/Dara ls-remote origin      # the real remote state
find /home/user/Dara -type f -not -path '*/.git/*'   # what actually exists
ls /home/user/Dara/.claude/skills/           # the library roster on disk
```

As of 2026-07-08 the repo contains only `.claude/skills/**` (the library) and
a local-only `.claude/settings.local.json` that must never be committed (see
`dara-change-control` rule 4). Then read `dara-charter-and-state` before
acting on any task framing.

## 2. Tool inventory (verified 2026-07-08 via `command -v`)

| Tool | Status | Path |
|---|---|---|
| `git` | present | `/usr/bin/git` |
| `gh` (GitHub CLI) | **ABSENT** — use GitHub MCP tools instead (Section 4) | — |
| `node` / `npm` | present | `/opt/node22/bin/node`, `/opt/node22/bin/npm` |
| `python3` | present | `/usr/local/bin/python3` |
| `rg` (ripgrep) | present | `/usr/bin/rg` |
| `jq` | present | `/usr/bin/jq` |
| `curl` | present | `/usr/bin/curl` |

Chromium + Playwright are pre-installed (`PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers`);
never run `playwright install`. Re-verify any of these with
`command -v <tool>` before writing them into a runbook — inventories drift.

## 3. Network: the agent proxy

*Agent proxy* = a local HTTPS proxy all outbound traffic must traverse; it
does TLS interception, so tools must trust its CA (certificate authority)
bundle.

- CA bundle: `/root/.ccr/ca-bundle.crt` (verified present 2026-07-08).
- Health/diagnostic: `curl -sS "$HTTPS_PROXY/__agentproxy/status"` — verified
  working 2026-07-08; returns JSON with `"enabled": true`, the CA bundle path,
  and a `noProxy` list (localhost, anthropic.com, npm/pypi/crates/golang
  registries, private ranges are exempted).
- If a tool fails TLS verification or gets 403/405/407 through the proxy:
  read `/root/.ccr/README.md` and check the status endpoint for per-tool
  fixes. **Never disable TLS verification and never unset `HTTPS_PROXY`** —
  that is a hard rule, not a preference.

## 4. GitHub without `gh`

All GitHub operations (viewing/creating PRs, comments, CI status, branches)
go through the GitHub MCP tools, named `mcp__github__*`. Operational notes,
all observed in real sessions on 2026-07-08:

- The GitHub MCP server **disconnects and reconnects intermittently**. Tools
  vanish and return. Never conclude a capability is gone without re-searching
  for the tool after a reconnect (triage detail: `dara-debugging-playbook`).
- Session GitHub access is scoped to `flakkylanre-maker/Dara` unless a repo
  is explicitly added. Do not use unscoped search tools to reach beyond it.
- `git push`/`fetch` to `origin` work over a local authenticated remote and
  do not need the MCP tools.
- Creating a PR remains gated by `dara-change-control` rule 6 (founder must
  ask explicitly).

## 5. Where artifacts land

| Location | Lifetime | Use for |
|---|---|---|
| `/home/user/Dara` (repo), committed + pushed | Permanent | Everything worth keeping — skills, docs, scripts |
| Repo working tree, uncommitted | Until container reclaim | Work in progress only; push in batches |
| Session scratchpad directory | Until container reclaim | Temp files, agent briefs, intermediate outputs. Find its exact path in your own session's system prompt ("Scratchpad Directory") — it is session-specific; never hardcode it into a skill or script |
| Anywhere else on the filesystem | Until container reclaim | Avoid |

## 6. Known environment failure modes (summary)

One line each; full symptom→triage table lives in `dara-debugging-playbook`:

| Symptom | One-liner |
|---|---|
| MCP tools disappeared | Server flap; re-search after reconnect |
| Interactive question tool errors ("permission stream closed") | Known open incident; ask inline in reply text instead |
| TLS/certificate failure in a CLI tool | Proxy CA issue; check status endpoint (Section 3) |
| Subagents die with "session limit" API errors | Account usage cap; work inline, push what exists, resume after reset |

## 7. When NOT to use this skill

- Rules for committing/pushing/branching → `dara-change-control`
- Step-by-step triage of a live failure → `dara-debugging-playbook`
- What the project is and its invariants → `dara-charter-and-state`
- Multi-agent coordination mechanics → `dara-orchestration-workflows`

## 8. Provenance and maintenance

- Authored 2026-07-08 in a live remote session. Every command and path above
  was executed/inspected in that session: tool paths via `command -v`, CA
  bundle via `ls -la`, proxy status via the curl above, repo contents via
  `find`. MCP flap and session-limit incidents were observed first-hand the
  same day (`dara-failure-archaeology` is the system of record).
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| Tool inventory | `for t in git gh node python3 rg jq curl npm; do printf '%s: ' $t; command -v $t \|\| echo ABSENT; done` |
| CA bundle present | `ls -la /root/.ccr/ca-bundle.crt` |
| Proxy healthy | `curl -sS "$HTTPS_PROXY/__agentproxy/status" \| head -c 300` |
| Repo contents | `find /home/user/Dara -type f -not -path '*/.git/*'` |

- Environment images change without notice; treat Section 2's paths as
  volatile and re-run the inventory loop whenever a runbook depends on them.
