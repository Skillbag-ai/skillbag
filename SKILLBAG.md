SkillBag v0.2.0

SkillBag defines how an agent discovers, installs, and applies workspace
skills. MUST, SHOULD, and MAY are normative.

## Files

| File                | Required               | Role                                                                              |
| ------------------- | ---------------------- | --------------------------------------------------------------------------------- |
| `SKILLBAG.md`       | yes                    | Entrypoint.                                                                       |
| `.skills/`          | when skills exist      | Skill root.                                                                       |
| `.skills/SKILLS.md` | when `.skills/` exists | Discovery catalog; read first, load individual `SKILL.md` files only when needed. |
| `CONTEXT.md`        | no                     | Project context and initial dependencies.                                         |
| `USER_CONTEXT.md`   | no                     | User-local context; SHOULD be untracked.                                          |

## Precedence

Highest to lowest: (1) current conversation, (2) `USER_CONTEXT.md`,
(3) `CONTEXT.md`, (4) `SKILLBAG.md`, (5) skill defaults. Within one file,
last explicit definition wins. Lower precedence MUST NOT override a
higher-precedence prohibition. Conversation context MAY supply missing
parameters but SHOULD NOT persist them automatically.

## Skill Root

`.skills/SKILLS.md`: one line per skill, `<name>: <description>`, sorted
ascending. Each `<name>` MUST equal the directory name and `SKILL.md` `name`.
Each `<description>` MUST equal `SKILL.md` `description`. Every valid skill
directory MUST be listed; every listed skill MUST exist.

Skill path: `.skills/<skill-name>/SKILL.md`. Optional subdirs: `scripts/`,
`references/`, `assets/`. `<skill-name>` MUST match
`[a-z0-9]+(?:-[a-z0-9]+)*` (1–64 chars) and equal `SKILL.md` `name`.

## Naming and Invocation

Canonical names use hyphens (`convert-to-pdf`). The execution identifier
replaces `-` with `_` for invocation only (`run convert_to_pdf`). All skill
paths, catalog entries, and dependency declarations MUST use the canonical
hyphenated name.

## SKILL.md

YAML frontmatter followed by Markdown instructions.

Required: `name`, `description` (non-empty).

Optional: `license`, `compatibility`, `metadata` (string-to-string),
`allowed-tools` (space-separated list).

Body SHOULD stay short; move secondary detail into `references/`, `scripts/`,
or `assets/`.

### Parameters

```yaml
required:
  - name: input-path
optional:
  - name: format
    default: json
```

Names MUST be unique within the skill. Required parameters MUST NOT define
`default`. Values resolve using the precedence rules above.

### Dependencies

```yaml
dependencies:
  - name: other-skill # required; canonical hyphenated name
    source: https://... # optional; omit to resolve by local name only
    version: v1.0.0 # optional; tag, branch, or commit
    required: true # optional; default true
```

Resolution MAY recurse; cycles are invalid.

## Tags

Tags appear in skill descriptions as `#group/name`. User context MAY add or
remove tags for any skill without editing `SKILL.md`.

The agent MUST comply with tag's behaviour specs.

| Tag                 | Behavior                                                                                                                                                                              |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `#run/always`       | Runs at workspace entry and before every response, unless disabled by user context.                                                                                                   |
| `#run/last`         | Runs in the pre-response phase, after all non-`#run/last` skills. Multiple `#run/last` skills run in ascending canonical name order. MAY be disabled by user context.                 |
| `#use/<skill-name>` | When this skill runs, the named companion SHOULD also run if available. Does not install missing skills; ignore silently if unavailable. `<skill-name>` MUST be canonical hyphenated. |

## CONTEXT.md Dependencies

```yaml
dependencies:
  - name: skillbag-create-skill # canonical hyphenated name
    version: main # tag, branch, or commit
    source: git@github.com:Org/skillbag-utils.git
```

`name`, `version`, and `source` are required. Installed before any other skill
resolution during initial setup.

## SkillBag Source

A git repository, HTTP(S) zip, local zip, or local directory. A valid source
MUST contain `AGENTS.md`, `.skills/`, and `.skills/SKILLS.md`. `AGENTS.md`
MUST identify the repo as `SKILLBAG`, state that skills live under `.skills/`,
and define any extra installation steps.

## Resolution

For dependency `x`: (1) explicit higher-precedence override, (2) local
`.skills/x/`, (3) requesting skill's declaration.

**Dependency conflicts** (same name, different source or pinned version): MUST
stop and report before making any changes.

**Skill conflicts** (two installed skills contradict on the same action):
resolve in order — conversation, `USER_CONTEXT.md` rule, `CONTEXT.md` rule,
alphabetical ascending canonical name (first wins). If the fallback is
ambiguous or high-risk, MUST pause and ask the user.

Explicit overrides in `CONTEXT.md` or `USER_CONTEXT.md`:

```yaml
conflict-resolution:
  - skills: [skill-a, skill-b]
    winner: skill-a
```

Local edits are authoritative. MUST NOT overwrite a locally modified skill
without explicit user instruction.

## Lifecycle

### Workspace Entry (once per session)

Execute in order before any other workspace work:

1. Read `SKILLBAG.md`.
2. Read `CONTEXT.md` and `USER_CONTEXT.md` if present.
3. Read `.skills/SKILLS.md` if present.
4. Apply precedence rules.
5. Run all `#run/always` skills not disabled by user context.

If declared dependencies are missing from `.skills/`, prompt the user to
initialize before continuing skill-dependent work.

### Installation

Triggered by user request or by missing declared dependencies at workspace
entry. Any source validation failure MUST stop the entire run before touching
the filesystem:

1. Materialize `.skills/skillbag-get-skills/SKILL.md` if absent.
2. Prepend `CONTEXT.md` dependencies to the install list.
3. Validate each source as a SkillBag source.
4. Install each skill at `.skills/<name>/`; skip if present and `upgrade=false`.
5. Regenerate `.skills/SKILLS.md` in ascending alphabetical order.

### Refresh (mid-session)

Triggered when `SKILLBAG.md`, `.skills/SKILLS.md`, or a loaded `SKILL.md`
changes. Re-read only changed files that are loaded or newly selected; do not
reload all `SKILL.md` files eagerly. Complete before relying on changed state.

## Reserved Bootstrap Skill

`skillbag-get-skills` MUST be materialized at
`.skills/skillbag-get-skills/SKILL.md` during initial setup. Invoke as
`run skillbag_get_skills`.

Canonical template:

````md
---
name: skillbag-get-skills
description: Install one or more skills into .skills/.
allowed-tools: git curl wget tar unzip cp rsync ln
---

## Parameters

```yaml
optional:
  - name: skills
    default: []
  - name: destination
    default: .skills/
  - name: upgrade
    default: false
  - name: persist-nonsecret-parameters
    default: true
```

## Instructions

- `skills`: list of `{name, source?, version?}` items.
- Prepend `CONTEXT.md` dependencies to `skills` on initial setup. At least
  one install target MUST be provided via `skills` or `CONTEXT.md`.
- Allowed acquisition tools: `git`, `curl`, `wget`, `tar`, `unzip`, `cp`,
  `rsync`, `ln`. No other network tools are standardized.
- If `source` is omitted, resolve by local name only; do not fetch.
- If `version` is omitted for a git source, use the default branch head.
- Validate each source: check `AGENTS.md` identifies `SKILLBAG` and defines
  extra steps; check `.skills/`, `.skills/SKILLS.md` sync, requested skill
  presence, and `SKILL.md` validity. Follow all extra steps in `AGENTS.md`.
  Any failure MUST stop the entire run and report before making changes.
- Install each skill at `.skills/<name>/`. Skip if present and `upgrade=false`.
  Do not overwrite local edits without explicit user instruction.
- After all installs, regenerate `.skills/SKILLS.md` in ascending alphabetical
  order.
- If `persist-nonsecret-parameters=true`, resolved non-secret parameters MAY
  be written to `USER_CONTEXT.md`. Secrets MUST NOT be written automatically.
````

Any equivalent `SKILL.md` with the same required semantics is conforming.
