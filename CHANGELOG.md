# Changelog

All notable changes to this repository should be documented in this file.

The format is intentionally simple while the project remains a draft.

## v0.1.1

- Added the `#use/<skill-name>` tag so a skill can declare another skill that
  should be considered and used in combination.
- Added the `#run/last` tag so a skill can declare that it should run after
  other applicable skills, with deterministic ordering for multiple
  `#run/last` skills.
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
