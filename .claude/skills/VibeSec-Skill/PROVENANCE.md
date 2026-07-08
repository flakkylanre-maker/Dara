# Provenance — VibeSec-Skill (vendored, third-party)

This is a **vendored, unmodified third-party skill**, not Dara doctrine. It is
kept here so the founder owns a durable copy that survives even if the upstream
repository is deleted, renamed, or made private.

| Field | Value |
|---|---|
| Upstream repository | https://github.com/BehiSecc/VibeSec-Skill |
| Author | BehiSecc |
| License | Apache License 2.0 (see `LICENSE` in this directory) |
| Vendored from commit | `0590993b35ad51961f65a4d01cf1196dfead05bb` |
| Upstream commit date | 2026-02-17 |
| Vendored on | 2026-07-08 |
| `SKILL.md` modified from upstream? | No — byte-identical copy |

## What it is

A content-only security skill: `SKILL.md` is a ~750-line secure-coding guide
(access control, XSS/CSRF, SSRF, SQLi, XXE, path traversal, auth, JWT, API
security) written from a bug-bounty perspective. It ships **no scripts, no
hooks, and no executable code** — it only adds knowledge to the model's context.

## Audit note (2026-07-08)

Reviewed before vendoring: the repo contains only `SKILL.md`, `README.md`, and
`LICENSE`. No hidden instructions, no exfiltration, no network calls — every URL
in `SKILL.md` is legitimate teaching material (SSRF bypass tables, cloud
metadata endpoints, etc.). Technical guidance spot-checked and found accurate.
Upstream self-describes as covering "60-70% of common vulnerabilities" — treat
it as a strong first-pass guardrail, not a substitute for real security review.

## Relationship to Dara doctrine

This skill is intentionally **outside** the `dara-*` doctrine set. It is not
governed by Dara's charter and does not carry Dara's certified-inventory
guarantees. If it is ever edited away from the upstream copy, it stops being a
clean vendored artifact — update this file and record the change.
