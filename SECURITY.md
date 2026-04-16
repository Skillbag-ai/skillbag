# Security Policy

SkillBag is a specification repository, not a production service. Even so,
security-relevant problems can still exist, especially when the specification
changes agent behavior around:

- fetching remote sources
- validating downloaded repositories or archives
- executing installed skills
- handling local overrides and user context

## Reporting

If you find a security issue in the specification or examples, please report it
privately to the maintainers before opening a public issue.

A useful report should include:

- the affected section or rule
- the security impact
- a concrete abuse scenario
- any suggested mitigation

## Scope

Examples of in-scope issues:

- a rule that would enable unsafe remote fetching by default
- ambiguous validation behavior that could lead to unsafe installation
- precedence rules that could bypass explicit safety controls
- documentation that materially misstates a security-sensitive requirement

Examples of out-of-scope issues:

- generic concerns without a concrete spec impact
- third-party tool vulnerabilities unrelated to the standard text itself

## Disclosure

Please allow maintainers time to evaluate and address the report before public
disclosure.
