# 🎒 SkillBag

SkillBag is an open standard for portable, composable AI agent behavioral
specifications. Think of it as `.editorconfig` for agent skills: a file-based
convention that makes agent behaviors version-controlled, reusable, and movable
across providers and projects.

It is loosely based on the per-skill format defined by
[agentskills.io](https://agentskills.io/specification), extended to cover
workspace-level concerns that individual skill files do not address.

The standard is centered on a single entrypoint file, `SKILLBAG.md`. An agent
that reads that file can determine:

- which skills are available
- where those skills come from
- how skills depend on each other
- which context files modify behavior
- how missing skills are installed
- how skills are invoked at runtime

## Why This Exists

Single-skill formats describe one skill. Real workspaces need answers to
questions that span many skills:

- Which skills are installed here?
- Which skills are required for initial setup?
- Where do skills come from?
- How do local overrides interact with downloaded skills?
- Which context files take precedence?
- How can an agent discover skills without loading everything?

SkillBag adds those workspace-level rules while staying compatible with the
per-skill `SKILL.md` format and staying provider-agnostic.

Skills are behavioral specifications, not guaranteed execution contracts.
Execution quality depends on the agent interpreting them. SkillBag accepts this
and provides conventions for detecting and correcting drift (see
`skillbag-supervisor` in [skillbag-utils](https://github.com/Skillbag-ai/skillbag-utils))
rather than pretending it does not happen.

## Status

SkillBag is an active draft. The normative specification lives in
[SKILLBAG.md](./SKILLBAG.md).

## What SkillBag Standardizes

- a required `SKILLBAG.md` entrypoint
- a `.skills/` directory containing installed skills
- a required `.skills/SKILLS.md` catalog when `.skills/` exists
- dependency declaration and resolution rules
- installation and validation rules for SkillBag sources
- precedence across conversation context, user context, project context, and
  skill defaults
- a canonical skill naming convention and a runtime invocation convention
- bootstrap installation through the reserved `skillbag-get-skills` skill

## Core Concepts

### Entrypoint

`SKILLBAG.md` is the workspace entrypoint. Agents read it to understand the
effective skill environment.

### Skill Root

Installed skills live under `.skills/`. Each skill is a directory containing at
least `SKILL.md`.

### Skill Catalog

`.skills/SKILLS.md` is the low-cost discovery surface for installed skills.
Agents read it first, then load only the specific `SKILL.md` files they need.

### Context Files

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

Skills can be:

- versioned through explicit `version` fields and source references
- composed through dependency declarations
- reused across multiple workspaces
- adapted locally without losing the distinction between downloaded and local behavior

## Provider Shim Files

SkillBag is provider-agnostic. The specification and skills live in ordinary
files that work with any agent. However, different agents use different
filenames to discover workspace instructions:

| File | Agent |
|---|---|
| `AGENTS.md` | OpenAI Codex CLI, and others adopting this convention |
| `CLAUDE.md` | Claude Code |
| `.cursorrules` | Cursor |
| `.github/copilot-instructions.md` | GitHub Copilot |

SkillBag uses `AGENTS.md` as its primary instruction file. Each other file is a
one-line shim that redirects to it:

```
Read and follow instructions in AGENTS.md
```

This keeps provider-specific files trivially thin. All actual workspace rules
live in `AGENTS.md`, which points agents to `SKILLBAG.md`. The skills
themselves are fully provider-agnostic.

## Installation

**1.** Fetch `SKILLBAG.md` into your project root:

```bash
curl -fsSL https://skillbag.md -o SKILLBAG.md
```

**2.** Add one line to your `AGENTS.md`:

```
Read and execute SKILLBAG.md before any other work.
```

That's it. On the next agent session the agent reads the spec, installs
`skillbag-get-skills`, processes any dependencies in `CONTEXT.md`, and keeps
`.skills/SKILLS.md` in sync — all without further setup.

Optionally declare skill dependencies in `CONTEXT.md`:

```yaml
dependencies:
  - name: skillbag-supervisor
    version: main
    source: git@github.com:Skillbag-ai/skillbag-utils.git
```

## Runtime Invocation

Skill names are canonical and hyphenated (`convert-to-pdf`,
`skillbag-get-skills`). The runtime execution identifier replaces `-` with `_`
(`run convert_to_pdf`, `run skillbag_get_skills`). The canonical hyphenated
name is still used in directory names, `SKILLS.md`, `SKILL.md`, and dependency
declarations.

## Tags

Skill descriptions may include standardized tags directly in the description
text.

| Tag | Behavior |
|---|---|
| `#run/always` | Skill runs even when not explicitly requested, unless disabled by user context |
| `#run/last` | Skill runs after all other applicable non-`#run/last` skills |
| `#use/<skill-name>` | When this skill is used, the named companion skill should also be considered |

If multiple `#run/last` skills apply, they run in ascending canonical name
order.

User context may add or remove tags for installed skills without editing skill
files, making it possible to disable supervision globally or enable it only for
specific tasks.

## Repository Layout

- [SKILLBAG.md](./SKILLBAG.md): normative specification
- [AGENTS.md](./AGENTS.md): agent instructions for this repository
- [README.md](./README.md): project overview
- [CONTRIBUTING.md](./CONTRIBUTING.md): contribution guide
- [CODE_OF_CONDUCT.md](./CODE_OF_CONDUCT.md): community standards
- [SECURITY.md](./SECURITY.md): security disclosure guidance
- [CHANGELOG.md](./CHANGELOG.md): human-readable change log
- [LICENSE.md](./LICENSE.md): MIT license

## Non-Goals

SkillBag does not attempt to standardize:

- a general-purpose software package manager
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

## Contributing

Contributions are welcome. Start with [CONTRIBUTING.md](./CONTRIBUTING.md).

## Security

See [SECURITY.md](./SECURITY.md).

## License

MIT. See [LICENSE.md](./LICENSE.md).
