---
name: dara-skill-authoring
description: >-
  House style and process for writing or editing skills in the Dara library —
  in this project, skills ARE the docs of record. Load this whenever you are
  about to create a new SKILL.md, restructure or substantially edit an
  existing one, write a skill description, or add scripts to a skill. Also
  load it to judge whether a draft skill meets the house bar. Triggers:
  "write a skill", "add a skill", "SKILL.md", "update the docs", "description
  isn't triggering", "where do I document this".
---

# Dara Skill Authoring

Skills are Dara's only documentation medium (charter Invariant 2), so writing
one is writing the project's memory. This skill owns HOW to write them; WHAT
each skill owns is the charter's library map; WHETHER your change may merge is
`dara-change-control` (new skill = class A, edit = class B/C).

## 1. File layout

```
.claude/skills/<kebab-case-name>/SKILL.md    # required, exactly this name
.claude/skills/<kebab-case-name>/scripts/    # optional executable helpers
```

Verify the current roster before naming anything (names must not collide and
must match the charter map): `ls /home/user/Dara/.claude/skills/`

## 2. Frontmatter: the description IS the trigger

Models choose which skill to load from the `description` alone — they do not
read the body first. A description that just summarizes content will never
fire. It must enumerate the SITUATIONS that should trigger loading: symptoms,
task types, and literal phrases.

```yaml
---
name: <kebab-name-matching-directory>
description: >-
  <What this skill is, one clause.> Load this when: <situation 1>;
  <situation 2>; <situation 3>. Triggers: "<phrase>", "<error text>",
  "<task type>".
---
```

**Bad** (never fires): `Documentation about Dara's git conventions.`
**Good** (fires when needed): `How changes are gated in Dara. Load BEFORE
branching, committing, pushing, or PRs; when a push fails and you must decide
whether to retry; ... Triggers: "commit", "push", "is this allowed".`

## 3. Mandatory sections

Every Dara skill carries, in addition to its content:

1. **Trigger-rich frontmatter** (Section 2).
2. **"When NOT to use this skill"** — names the sibling that owns each
   adjacent topic, so a wrong load costs one hop, not a wrong answer.
3. **"Provenance and maintenance"** (final section) — authorship date, what
   was verified and how, and a table of one-line re-verification commands
   for every drift-prone fact. This is what makes stale docs detectable
   instead of poisonous.

Skeleton for a new skill:

```markdown
---
name: <name>
description: >-
  <what it is>. Load this when: <...>. Triggers: "<...>".
---

# <Title>

<One paragraph: what this skill owns and which siblings border it.>

## 1. <Content sections: tables and checklists over prose>

## N-1. When NOT to use this skill
- <adjacent topic> → `<sibling-skill>`

## N. Provenance and maintenance
- Authored <date>. <What was verified, how.>
| Claim | One-line re-verification |
|---|---|
| <fact> | `<command>` |
```

## 4. Writing rules

| Rule | Rationale |
|---|---|
| Verify every command/path/flag by RUNNING it before writing it | Ground truth only (charter Inv. 1). A Sonnet-class reader executes runbooks without second-guessing; a wrong command misdirects silently. Evidence bar details: `dara-validation-and-qa` |
| Unverifiable but necessary claims get `ASSUMPTION (<date>, unconfirmed)` inline | Makes the revisit checklist greppable: `rg 'ASSUMPTION' .claude/skills/` |
| Imperative runbook voice; copy-pasteable commands; expected output stated | The reader may be a model executing, not a human browsing |
| Define each jargon term once, at first use, in italics | Audience is zero-context mid-level; undefined jargon silently forks interpretations |
| Date-stamp volatile facts ("as of <date>") | Undated facts can't be audited for drift |
| One home per fact: own your scope, cross-reference siblings by name | Duplicated facts drift independently; the reader can't tell which copy is current |
| No oversell: unproven = "open" or "candidate", never stated as working | A cheap model inherits your confidence verbatim |
| No private/user-specific data (emails, account IDs, session paths) | Skills are permanent and potentially shareable; sessions are not |
| 150–400 lines typical; tables and checklists over prose walls | Long enough to be complete, short enough to be loaded and actually read |
| Scripts in `scripts/` must be executable, self-documenting (`--help` or header comment), and verified by running them | An unrunnable script is a false promise with a filename |

## 5. Lifecycle

1. **Author** on the designated branch (never touch files outside your
   skill's directory).
2. **Verify** everything per Section 4 (self-review against
   `dara-validation-and-qa`'s three lenses).
3. **Gate** per `dara-change-control`: class A/B review; anything touching
   doctrine is class C (founder sign-off).
4. **Certify**: after passing the three-lens review, the skill is "certified
   as of <date>" (`dara-validation-and-qa` owns the concept).
5. For authoring MANY skills at once, use the multi-agent workflow in
   `dara-orchestration-workflows` (shared brief, one skill per agent).

## 6. When NOT to use this skill

- Which skill should own a piece of content → `dara-charter-and-state`
  (library map, one-home-per-fact rule)
- Whether/how your change may merge → `dara-change-control`
- What counts as verification, review checklists → `dara-validation-and-qa`
- Coordinating multiple authoring agents → `dara-orchestration-workflows`

## 7. Provenance and maintenance

- Authored 2026-07-08. The layout in Section 1 and roster command were
  verified against the live repo; the frontmatter format matches the skills
  already merged (commits `ccbe52d`..`d9dc6dc`); the writing rules encode the
  library's founding authoring brief (2026-07-08) and charter invariants.
- Re-verify before trusting:

| Claim | One-line re-verification |
|---|---|
| Layout convention holding | `find .claude/skills -name 'SKILL.md' \| sort` |
| All skills carry the 3 mandatory sections | `rg --files-without-match 'Provenance and maintenance' .claude/skills/ -g 'SKILL.md'` (only `fable-decision-style` may appear — it predates house style); same for `'When NOT to use'` |
| Descriptions are trigger-rich | `rg -A3 '^description' .claude/skills/*/SKILL.md \| head -50` and check each names load-situations |
| No stray private data | `rg -i '@gmail\|@outlook\|session_01' .claude/skills/` (empty = good) |
