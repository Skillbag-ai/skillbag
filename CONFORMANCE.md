# SkillBag Conformance

This document defines how projects and implementations may describe support for
SkillBag. The normative specification is [SKILLBAG.md](./SKILLBAG.md).

## Status

SkillBag does not yet have an official certification program, trademark
license, or conformance test suite.

Until those exist, projects should avoid claiming to be "certified" or
"officially compliant." They may describe themselves as "SkillBag-compatible"
only when the relevant behavior below is implemented.

## Conformance Targets

SkillBag has three practical conformance targets.

## 1. SkillBag Workspace

A workspace is SkillBag-compatible when it contains the required entrypoint and
follows the file layout in `SKILLBAG.md`.

Minimum requirements:

- `SKILLBAG.md` exists at the workspace root.
- `.skills/` exists when installed skills exist.
- `.skills/SKILLS.md` exists when `.skills/` exists.
- `CONTEXT.md` and `USER_CONTEXT.md` are used only as defined by the standard.
- Local skill paths follow `.skills/<skill-name>/SKILL.md`.

## 2. SkillBag Source

A source is SkillBag-compatible when it can be installed by a conforming agent
or installer.

Minimum requirements:

- `AGENTS.md` identifies the repository as `SKILLBAG`.
- `AGENTS.md` states that skills live under `.skills/`.
- `.skills/` exists.
- `.skills/SKILLS.md` exists and matches installed skills.
- Each listed skill directory exists.
- Each skill has a valid `SKILL.md`.
- Skill names are canonical hyphenated names.
- Extra installation steps, if any, are documented in `AGENTS.md`.

## 3. Agent or Tool Implementation

An agent or tool is SkillBag-compatible when it implements the observable
runtime behavior required by the specification.

Minimum requirements:

- reads `SKILLBAG.md` before repository work when instructed
- reads `CONTEXT.md` and `USER_CONTEXT.md` if present
- applies the precedence order from `SKILLBAG.md`
- resolves skill inputs from conversation values, context files, skill
  defaults, and permitted discovery or inference as specified
- validates required and conditional parameters before executing a skill
- reads `.skills/SKILLS.md` before loading individual `SKILL.md` files
- resolves skill names using canonical hyphenated names
- maps hyphenated names to underscore execution identifiers only for invocation
- validates required skill metadata
- handles declared dependencies and dependency conflicts as specified
- refuses to overwrite locally modified skills without explicit user
  instruction
- applies standardized tags such as `#run/always`, `#run/last`, and
  `#use/<skill-name>`
- stops and reports before filesystem changes when source validation fails

## Claim Language

Acceptable claim language before certification exists:

- "Uses the SkillBag file layout."
- "SkillBag-compatible source."
- "Implements the SkillBag workspace-entry lifecycle."
- "Experimental SkillBag support."

Avoid:

- "SkillBag certified"
- "Official SkillBag implementation"
- "Fully compliant" without publishing tested behavior

## Future Certification

A future certification program should include:

- a public conformance test suite
- machine-checkable workspace fixtures
- installer validation tests
- lifecycle and precedence tests
- dependency conflict tests
- source validation tests
- a clear trademark or badge policy

Certification should test behavior. Sponsorship or membership should not be a
substitute for passing conformance tests.

## Compatibility Notes

The core standard intentionally avoids standardizing every possible agent
runtime detail. Implementations may add behavior as long as they do not violate
the required SkillBag semantics.

Extensions should be documented clearly and should not be presented as core
SkillBag behavior unless they are added to `SKILLBAG.md`.
