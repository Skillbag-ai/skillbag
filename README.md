# SkillBag

SkillBag is a draft standard for defining, composing, installing, versioning,
and running agent skills from files in a local workspace.

It is loosely based on the per-skill format defined by
[agentskills.io](https://agentskills.io/specification), but it is intended as
an extension of that format rather than a replacement. The base skill format
describes an individual skill. SkillBag adds the workspace-level structure
needed to manage many skills together.

The standard is centered on a single entrypoint file, `SKILLBAG.md`. An agent
that reads that file should be able to determine:

- which skills are available
- where those skills come from
- how skills depend on each other
- which context files modify behavior
- how missing skills are installed
- how skills are invoked at runtime

In practice, SkillBag is meant to play a role similar to a package manager for
skills. It does not try to be a general-purpose software package manager, but
it does define a standardized architecture for an AI agent working directory:
where skills live, how they are discovered, how they are versioned, how they
are composed, and how they are installed.

This exists because single-skill formats are not enough for real workspaces.
AI agents operating inside a local directory need project-level rules:

- Which skills are installed here?
- Which skills are required for initial setup?
- Where do skills come from?
- How do local overrides interact with downloaded skills?
- Which context files take precedence?
- How can an agent discover skills cheaply without loading everything?

SkillBag adds those workspace-level rules while staying compatible with a
per-skill `SKILL.md` format.

It is especially useful for local-directory agent workflows, where the
workspace itself should carry the instructions, structure, and dependency
rules needed for reliable operation.

It is also designed to keep the workspace provider-agnostic. Instead of
relying only on proprietary skill systems defined inside a specific UI or app,
the workspace can define its own portable skill architecture in files that
live with the project. That makes skills easier to:

- version
- review
- compose
- reuse
- move between agent providers
- keep under source control

The underlying idea is simple: agentic instructions and skills are a
higher-level programming language. As in any programming language, structure
and architecture matter. Once skills become reusable building blocks, the
workspace needs explicit rules for layout, composition, installation, and
override behavior.

## Status

SkillBag is an active draft. The goal of this repository is to make the
standard precise, minimal, and practical for file-based agent environments.

The normative specification lives in [SKILLBAG.md](./SKILLBAG.md).

## What SkillBag Standardizes

At a high level, SkillBag standardizes:

- a required `SKILLBAG.md` entrypoint
- a `.skills/` directory containing installed skills
- a required `.skills/SKILLS.md` catalog when `.skills/` exists
- dependency declaration and resolution rules
- installation and validation rules for SkillBag sources
- precedence across conversation context, user context, project context, and
  skill defaults
- a canonical skill naming convention and a runtime invocation convention
- bootstrap installation through the reserved `skillbag-get-skills` skill

Taken together, these rules define a stable architecture for local AI-agent
workspaces, not just a single skill file format.

## Core Concepts

### Entrypoint

`SKILLBAG.md` is the project entrypoint. Agents should read it to understand
the effective skill environment for a workspace.

### Skill Root

Installed skills live under `.skills/`. Each skill is a directory containing at
least `SKILL.md`.

### Skill Catalog

`.skills/SKILLS.md` is the low-cost discovery surface for installed skills.
Agents should read it first, then load only the specific `SKILL.md` files they
need.

### Context Files

SkillBag supports:

- `CONTEXT.md` for project-level context
- `USER_CONTEXT.md` for user-local context

These files can influence dependency installation and runtime behavior.

### SkillBag Sources

A SkillBag source can be:

- a git repository URL
- an HTTP(S) URL to a zip file
- a local path to a zip file
- a local path to a SkillBag directory root

Valid sources must contain `AGENTS.md`, `.skills/`, and `.skills/SKILLS.md`,
and their skills must conform to the standard.

### Versioning, Composability, and Reuse

SkillBag is designed so skills can be treated as durable building blocks:

- versioned through explicit `version` fields and source references
- composed through dependency declarations
- reused across multiple workspaces
- adapted locally without losing the distinction between downloaded and local
  behavior

This is one of the main advantages of a file-based, provider-agnostic approach.

## How SkillBag Is Used

The expected workflow looks like this:

1. Add `SKILLBAG.md` to the workspace.
2. Optionally add `CONTEXT.md` with initial dependencies.
3. Add instruction to execute `SKILLBAG.md` in your agent's context (in `AGENTS.md`, `CLAUDE.md`, or any other way defined by your agent).
4. If needed, explicitly ask your agent to run installation instructions from `SKILLBAG.md`

This model is intended for local-directory agent workflows, where the agent is
working against a real filesystem and the workspace itself should be the source
of truth.

## Installation

Simply copy the `SKILLBAG.md` into your project's root directory. Alternatively, get the latest by running this command from within your directory:

```bash
curl https://skillbag.md -o SKILLBAG.md
```

## Runtime Invocation

Skill names are canonical and hyphenated, for example:

- `convert-to-pdf`
- `skillbag-get-skills`

The runtime execution identifier replaces `-` with `_`. For example:

- `run convert_to_pdf ...`
- `run skillbag_get_skills ...`

The canonical hyphenated name is still used in:

- directory names
- `SKILLS.md`
- `SKILL.md`
- dependency declarations

## Repository Layout

This repository contains the core specification and repository-level docs:

- [SKILLBAG.md](./SKILLBAG.md): normative specification
- [README.md](./README.md): project overview
- [CONTRIBUTING.md](./CONTRIBUTING.md): contribution guide
- [CODE_OF_CONDUCT.md](./CODE_OF_CONDUCT.md): community standards
- [SECURITY.md](./SECURITY.md): security disclosure guidance
- [CHANGELOG.md](./CHANGELOG.md): human-readable change log
- [LICENSE.md](./LICENSE.md): MIT license

## Non-Goals

SkillBag does not attempt to standardize:

- a general-purpose package manager for arbitrary software
- execution sandboxes or trust models for all agents
- a universal skill body language beyond the compatible `SKILL.md` shape
- hosted registries, publishing APIs, or release infrastructure

## Design Priorities

When evaluating changes to the standard, prefer:

- precise semantics over vague guidance
- explicit precedence and override rules
- compatibility with the base skill format
- low-cost discovery through `SKILLS.md`
- minimal required context
- fail-fast validation for downloaded sources
- portability across AI-agent providers
- versionability, composability, and reusability of skills

## Versioning

This repository hosts a draft standard. Until a stable process is defined,
changes may refine semantics substantially. Proposed changes should still aim
for compatibility whenever practical.

## Contributing

Contributions are welcome. Start with [CONTRIBUTING.md](./CONTRIBUTING.md).

## Security

If you believe the specification enables a meaningful security issue, see
[SECURITY.md](./SECURITY.md).

## License

This repository is licensed under the MIT License. See
[LICENSE.md](./LICENSE.md).
