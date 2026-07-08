---
name: dara-orchestration-workflows
description: >-
  Dara's multi-agent workflow patterns: when to spawn parallel subagents vs
  work inline, the shared-brief pattern that keeps N agents consistent, the
  anatomy of a good agent prompt, the parallel-authoring and
  three-reviewer-plus-fixer review workflows, and orchestration failure modes
  (including agents dying to account session limits). Load this when: a task
  has N independent same-shaped work items; you're asked to author or review
  many skills/documents at once; designing agent prompts; or subagents are
  failing and you must decide whether to respawn or go inline. Triggers:
  "spawn agents", "parallel", "subagent", "orchestrate", "review with
  multiple reviewers", "agents failed".
---

# Dara Orchestration Workflows

How a lead session coordinates subagents in Dara. Grounded in the workflow
that built this very library (2026-07-08): a parallel-authoring wave, a
mid-flight failure (see Section 5), and a three-lens review. *Subagent* = a
separately-spawned model instance that starts with ZERO conversation context
and returns one report; that zero-context start is the central design
constraint for everything below.

## 1. Orchestrate vs work inline

| Situation | Choice | Why |
|---|---|---|
| N independent, same-shaped items (N skills to author, N docs to review) | Orchestrate | Parallelism wins; items don't share mutable state |
| Sequential or interdependent steps | Inline | Agents can't see each other; you'd serialize anyway plus pay context-transfer cost |
| Small task (< ~30 min of work) | Inline | Spawn overhead + cold-start re-derivation exceeds the work |
| Needs conversation context or founder intent | Inline | An agent won't have it and will guess — guessing violates charter Invariant 1 |
| Account near its usage limit | Inline | N agents ≈ N× token burn; see Section 5, failure F3 |

## 2. The shared-brief pattern

Before spawning ANY wave of agents, write ONE brief file they all read first.
The brief is what keeps N parallel agents consistent and stops each from
re-deriving (or inventing) context. It must contain:

1. **Verified ground truth** — the facts of the repo/environment, each
   checkable, so agents inherit truth instead of guessing.
2. **Non-negotiable rules** — verification obligations, prohibitions,
   format requirements (for skills: the rules in `dara-skill-authoring`).
3. **The work-item roster with ownership boundaries** — who owns which
   fact/file ("one home per fact"), with exact cross-reference names.
4. **A required report-back format** — what each agent must return, so a
   missing or malformed report is detectable as a failure.

Write the brief to the session scratchpad (find its path in your session's
system prompt — never hardcode it) and start every agent prompt with:
"Read <brief path> in full and follow every rule in it."

## 3. Anatomy of a good agent prompt (checklist)

- [ ] Brief path, with the read-it-first instruction
- [ ] Exactly ONE output artifact, by absolute path
- [ ] What this agent OWNS vs must cross-reference by name
- [ ] Verification obligations: "run every command you print before
  writing it" (charter Invariant 1 applies to agents too)
- [ ] Explicit prohibitions: never run mutating git commands; never touch
  files outside the assigned artifact; read-only inspection is fine
- [ ] The report-back format from the brief
- [ ] Enough content direction (outline, required sections) that the agent
  spends its effort on verification and writing, not on guessing scope

## 4. The two standard workflows

**Parallel authoring** (used to build this library):
1. Lead writes the shared brief (Section 2) and the roster.
2. Spawn one agent per artifact, all in parallel, all writes confined to
   `.claude/skills/`.
3. Lead collects reports; a missing report = failed item (Section 5, F2).
4. Lead — never the agents — runs all git operations, batched, per
   `dara-change-control` (including its push-early rule).

**Three-reviewer + fixer** (runs only after ALL artifacts exist):
1. Spawn three parallel reviewers with the disjoint lenses from
   `dara-validation-and-qa` §3 (factual / doctrine / usability), each
   reviewing the COMPLETE set, each writing a findings report with
   severities to the scratchpad.
2. Spawn ONE fixer agent (or fix inline) applying blocking + important
   findings; the fixer must re-verify each fix, not just apply the
   reviewer's wording.
3. Lead spot-checks the fixes, then commits via `dara-change-control`.
   Certification is recorded per `dara-validation-and-qa` §5.

## 5. Orchestration failure modes (all observed or designed-against here)

| # | Failure | Mitigation |
|---|---|---|
| F1 | Agents invent facts when the brief under-specifies | Verification obligations in every prompt + the factual review lens catches leaks |
| F2 | Agent fails silently / returns nothing | Required report-back format; treat a missing report as a failed item and re-run it (inline if cheap) |
| F3 | Agents die to account session limits mid-wave | OBSERVED 2026-07-08: all 11 authoring agents were killed at once by the account cap; one artifact already on disk survived. Mitigations: check disk for partial output before assuming total loss (`git status --short`); push completed work immediately; continue inline rather than respawning into the same cap; weigh wave size against account limits before spawning. Full record: `dara-failure-archaeology` E6 |
| F4 | Overlapping ownership → contradictory artifacts | The roster's ownership boundaries; the doctrine lens catches residuals |
| F5 | Agent mutates state it shouldn't | Explicit prohibitions in the prompt; lead owns all git; diff review before commit (`git status --short` then `git diff --stat`) |

## 6. When NOT to use this skill

- The review CONTENT (lens checklists, severities) → `dara-validation-and-qa`
- Skill format the authoring agents must follow → `dara-skill-authoring`
- Git mechanics and gating for the lead's commits → `dara-change-control`
- Single-artifact work → just do it inline; this skill adds nothing

## 7. Provenance and maintenance

- Authored 2026-07-08, from the library's own construction: the shared-brief
  pattern, roster, prompts, and F3 are first-hand from that run; the
  three-reviewer + fixer design is the library's founding review protocol,
  first executed in the initial certification pass (2026-07-08).
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| Lens checklists still match | Open `dara-validation-and-qa` §3 |
| F3 stays accurate | `rg -n 'E6' .claude/skills/dara-failure-archaeology/SKILL.md` |
| Lead-owns-git still doctrine | `rg -n 'never run mutating git' .claude/skills/dara-orchestration-workflows/SKILL.md` and `rg -n 'force-push' .claude/skills/dara-change-control/SKILL.md` — both must hit |

- If the platform's agent semantics change (context sharing, persistence),
  Section 1's table is the first thing to re-derive — it encodes the
  zero-context constraint.
