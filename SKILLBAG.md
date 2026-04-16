SkillBag v0.1.1

SkillBag defines how an agent discovers, installs, and applies workspace
skills. It extends the base per-skill format with dependency, installation,
and context rules. MUST, SHOULD, and MAY are normative.

## Files

- `SKILLBAG.md` is the required entrypoint.
- `.skills/` is the local skill root.
- `.skills/SKILLS.md` is required whenever `.skills/` exists.
- `CONTEXT.md` is optional project context.
- `USER_CONTEXT.md` is optional user context and SHOULD be untracked.

The effective skill set is the set of valid skill directories under `.skills/`
after dependency resolution. Agents SHOULD read `.skills/SKILLS.md` first and
load individual `SKILL.md` files only when needed.

## Precedence

Conflicts resolve in this order:

1. Explicit instructions in the current user request or active conversation.
2. `USER_CONTEXT.md`.
3. `CONTEXT.md`.
4. `SKILLBAG.md`.
5. Skill defaults.

Rules:

- Higher precedence overrides lower precedence.
- Within one file, the last explicit definition wins.
- Lower precedence MUST NOT override a higher-precedence prohibition.
- Conversation context MAY supply missing parameters but SHOULD NOT be
  persisted automatically.

## Skill Root

`.skills/SKILLS.md` rules:

- one skill per line in exact form `<name>: <description>`
- `<name>` MUST equal the directory name and the `name` in
  `.skills/<name>/SKILL.md`
- `<description>` MUST equal the `description` in
  `.skills/<name>/SKILL.md`
- each skill MUST appear exactly once
- each listed skill MUST exist at `.skills/<name>/`
- each valid skill directory under `.skills/` MUST be listed
- lines MUST be sorted by `<name>` in ascending alphabetical order

A skill lives at `.skills/<skill-name>/SKILL.md`.

`<skill-name>`:

- MUST match `[a-z0-9]+(?:-[a-z0-9]+)*`
- MUST be 1-64 characters
- MUST equal the `name` field in `SKILL.md`

Optional subdirectories: `scripts/`, `references/`, `assets/`.

## Execution Convention

The canonical skill identifier is the skill `name`, which MUST use hyphens.
The execution identifier is the canonical skill name with every `-` replaced by
`_`.

Rules:

- a user or skill MAY invoke another skill with
  `run <execution-identifier> <free-form task text>`
- `run <execution-identifier>` means execute the skill whose canonical name is
  obtained by replacing `_` with `-`
- skill paths, `SKILLS.md`, `SKILL.md`, and dependency declarations MUST use
  the canonical hyphenated name
- execution identifiers exist only for invocation
- because skill names cannot contain `_`, this mapping is one-to-one

Example: `convert-to-pdf` is executed as `run convert_to_pdf`.

## CONTEXT.md Dependencies

`CONTEXT.md` MAY define initial dependencies under a `Dependencies` heading
using a fenced `yaml` block with a top-level `dependencies` key.

Each dependency entry MUST include:

- `name`
- `version`
- `source`

Rules:

- `name` MUST use the canonical hyphenated skill name
- `source` MUST use an allowed source form
- during initial setup, the installer MUST install these dependencies before
  any other skill resolution
- if explicit skills are also requested, the `CONTEXT.md` dependencies are
  installed first

## SkillBag Source

A SkillBag source is a git repository, zip archive, or local directory root
whose root contains `AGENTS.md`, `.skills/`, and `.skills/SKILLS.md`.

Allowed `source` values:

- a git repository URL
- an HTTP(S) URL to a zip file
- a relative or absolute local path to a zip file
- a relative or absolute local path to a SkillBag source directory root

For any SkillBag source:

- `AGENTS.md` MUST explicitly identify the source as `SKILLBAG`
- `AGENTS.md` MUST state that distributed skills live under `.skills/`
- `AGENTS.md` SHOULD mention `.skills/SKILLS.md` as the skill catalog
- `AGENTS.md` MUST define any extra installation steps required before the
  skills are usable
- every skill under `.skills/` MUST satisfy this standard
- `.skills/SKILLS.md` MUST be in sync with `.skills/`

## SKILL.md

`SKILL.md` MUST be YAML frontmatter followed by Markdown instructions.

Required frontmatter:

- `name`
- `description`, which MUST be non-empty

Optional frontmatter:

- `license` for a license name or bundled license reference
- `compatibility` for environment constraints
- `metadata` for string-to-string properties
- `allowed-tools` for a space-separated list of pre-approved tools

The body is free-form but SHOULD stay short and move secondary detail into
`references/`, `scripts/`, or `assets/`.

## Tags

Skill descriptions MAY include tags in the form `#group/name`.

Rules:

- tags are part of the skill description text
- tags MAY affect runtime behavior
- user context MAY add or remove tags for any installed skill without editing
  that skill's `SKILL.md`
- such user-context tag overrides affect runtime behavior only and override the
  skill's stored description tags
- `#run/always` means the skill SHOULD always execute
- `#run/always` MAY be disabled by user context
- if not disabled, `#run/always` skills SHOULD run even when they are not
  explicitly requested
- `#run/last` means the skill SHOULD run after all other applicable non-
  `#run/last` skills
- `#run/last` MAY be disabled by user context
- if multiple `#run/last` skills are applicable, they SHOULD run after all
  other applicable skills in ascending canonical skill name order
- `#use/<skill-name>` means the named skill SHOULD be considered and used in
  combination when the tagged skill is used
- `<skill-name>` in `#use/<skill-name>` MUST be a canonical hyphenated skill
  name
- `#use/<skill-name>` does not install missing skills by itself
- if the referenced skill is unavailable or disabled, ignore the tag without
  failing

## SkillBag Extensions

Reserved semantic sections in `SKILL.md` are optional. If present, they SHOULD
use fenced `yaml` blocks.

### Dependencies

Each entry MUST include `name`. It MAY include `source`, `version`, and
`required`; `required` defaults to `true`.

Rules:

- `source` MAY use any allowed `source` form above
- `version` MAY be a tag, branch, or commit
- if `source` is omitted, resolve by local name only
- dependency resolution MAY recurse
- cycles are invalid

### Parameters

The section SHOULD expose `required` and `optional`.

Rules:

- parameter names MUST be unique within the skill
- optional parameters MAY define `default`
- required parameters MUST NOT define `default`
- values resolve using the precedence rules above

## Resolution

For dependency `x`, resolve in this order:

1. an explicit higher-precedence override
2. a valid local `.skills/x/`, preferably discovered through `.skills/SKILLS.md`
3. the requesting skill's dependency declaration

Conflicts:

- same name plus different sources
- same name plus different pinned versions
- unresolved conflicts MUST stop and be reported

Local edits are authoritative. An agent MUST NOT overwrite a locally modified
skill unless the user explicitly requests reinstall, refresh, or replacement.

## Installation

Installation is lazy by default.

- On initial setup, the installer MUST materialize `.skills/skillbag-get-skills/SKILL.md` from the reserved bootstrap skill and ensure `.skills/SKILLS.md` contains `skillbag-get-skills: Install one or more skills into .skills/.`.
- If `.skills/` exists, install only missing skills unless the user explicitly
  requests reinstall.
- The agent MAY create `.skills/` on first install.
- If `.skills/` exists, `.skills/SKILLS.md` MUST stay in sync with local
  skills and remain sorted by skill name in ascending alphabetical order.
- During initial setup, the installer MUST inspect `CONTEXT.md` for
  `dependencies` and install them first when present.
- `CONTEXT.md` and `USER_CONTEXT.md` are optional and need not exist.
- The agent MAY create `CONTEXT.md` or `USER_CONTEXT.md` only when there is
  concrete content to persist.
- `USER_CONTEXT.md` SHOULD remain untracked.
- Reserved bootstrap skills MAY be defined inline as normative templates under
  `.skills/`.

## Reserved Bootstrap Skill

`skillbag-get-skills` is the reserved installation skill. By the execution
convention above, it is invoked as `run skillbag_get_skills`. During initial
setup, a conforming agent MUST materialize
`.skills/skillbag-get-skills/SKILL.md`.

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

- `skills` is a list; each item MUST include `name` and MAY include `source` and `version`.
- On initial setup, read `CONTEXT.md` dependencies first and prepend them to `skills`.
- At least one install target MUST be provided either through `skills` or `CONTEXT.md` dependencies.
- `source` MAY be a git repository URL, an HTTP(S) zip URL, a relative or absolute local zip path, or a relative or absolute local SkillBag source directory root.
- Only `git`, `curl`, `wget`, `tar`, `unzip`, `cp`, `rsync`, and `ln` are standardized acquisition tools.
- If `source` is omitted, do not guess and do not fetch from the network.
- If `version` is omitted for a git source, use the default branch head.
- For any provided `source`, resolve or extract it to a candidate root and validate it as a SkillBag source.
- Validation MUST check `AGENTS.md`, `.skills/`, `.skills/SKILLS.md`, `SKILLS.md` syntax and sync, requested skill presence in `SKILLS.md`, requested skill presence at `.skills/<name>/`, and requested skill `SKILL.md` validity.
- `AGENTS.md` MUST explicitly identify `SKILLBAG`, state that skills live under `.skills/`, and define any extra installation steps.
- The downloader MUST follow all extra installation steps in `AGENTS.md`, including installing required tools or resolving additional SkillBag sources.
- Any validation failure MUST fail the install, inform the user, and stop the entire `skillbag_get_skills` run.
- The source `.skills/` contents MUST conform to this standard.
- Install each requested skill as exactly one directory at `.skills/<name>/`.
- If `.skills/<name>/` exists and `upgrade=false`, leave it unchanged.
- If `.skills/<name>/` has local edits, do not overwrite it without explicit user instruction.
- After any successful install or upgrade, regenerate local `.skills/SKILLS.md` in ascending alphabetical order by skill name so it exactly matches local `.skills/`.
- If `persist-nonsecret-parameters=true` and no higher-precedence rule forbids it, resolved non-secret parameters MAY be written to `USER_CONTEXT.md`.
- Secrets, tokens, passwords, and private keys MUST NOT be written to `USER_CONTEXT.md` automatically.
````

Any equivalent `SKILL.md` with the same required semantics is conforming.
No other network fetch tool is standardized by this draft.
