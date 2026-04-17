# Changelog

All notable changes to this repository should be documented in this file.

The format is intentionally simple while the project remains a draft.

## v0.2.0

- Added the `Lifecycle` section defining four explicit phases: Workspace Entry,
  Installation, Refresh, and Pre-Response. Each phase specifies ordered,
  normative steps that a conforming agent MUST follow.
- Added skill conflict resolution rules to the `Resolution` section. Conflicts
  resolve through: (1) conversation context, (2) `USER_CONTEXT.md` rules,
  (3) `CONTEXT.md` rules, (4) alphabetical ascending canonical name order. If
  the alphabetical fallback is ambiguous or high-risk, the agent MUST pause and
  ask the user. Added `conflict-resolution` YAML syntax for declaring explicit
  winners in `CONTEXT.md` or `USER_CONTEXT.md`.
- Split the `Resolution` section into `Skill Conflicts` and `Dependency
  Conflicts` subsections for clarity.

## v0.1.1

- Added the `#use/<skill-name>` tag so a skill can declare another skill that
  should be considered and used in combination.
- Added the `#run/last` tag so a skill can declare that it should run after
  other applicable skills, with deterministic ordering for multiple
  `#run/last` skills.
- Clarified that user context may add or remove tags for installed skills,
  including the `#run/*` tags, without editing the skill files.
- Required installer-managed `SKILLS.md` catalogs to remain sorted in
  ascending alphabetical order by skill name.
- Documented standardized description tags in the README, including
  `#run/always`, `#run/last`, and `#use/<skill-name>`, with a long-task
  example.

## v0.1.0

- Initial release of the core SkillBag specification in `SKILLBAG.md`.
- Added repository-level OSS documentation, including README, contributing,
  license, code of conduct, security policy, and changelog.
- Added GitHub Pages publishing and documented `curl`-based installation for
  fetching `SKILLBAG.md` from the published domain.
- Added the first explicit semantic version header, `SkillBag v0.1.0`.
