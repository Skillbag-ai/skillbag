# Contributing to SkillBag

Thanks for contributing to SkillBag.

The repository is a specification effort first. Changes should improve
precision, interoperability, and practical usability for file-based agent
workspaces.

## Before You Start

- Read [README.md](./README.md).
- Read the normative spec in [SKILLBAG.md](./SKILLBAG.md).
- Check open issues or existing proposals before starting overlapping work.

## What Good Contributions Look Like

Strong contributions usually do at least one of the following:

- remove ambiguity
- make precedence or override behavior explicit
- tighten failure conditions
- improve interoperability with the compatible per-skill format
- reduce required context while preserving semantics
- add examples only where they clarify otherwise ambiguous behavior

## What to Avoid

Avoid changes that:

- expand scope without a concrete need
- replace precise rules with broad prose
- silently change precedence or compatibility behavior
- introduce examples that behave like hidden normative text
- standardize infrastructure that the current draft does not need

## Spec Editing Guidelines

When editing the spec:

- use normative language deliberately: MUST, SHOULD, MAY
- keep terms stable once introduced
- prefer one clear rule over multiple overlapping rules
- state failure behavior explicitly
- preserve compatibility where practical
- keep the standard minimal, but not underspecified

## Proposing a Change

When proposing a non-trivial change, include:

- the problem being solved
- why the current text is insufficient
- the exact rule change
- compatibility impact
- examples only if needed to remove ambiguity

## Pull Request Guidance

Pull requests should:

- stay focused on one logical change when possible
- update documentation affected by the change
- avoid mixing editorial cleanup with semantic changes unless necessary
- call out any normative change clearly in the description

## Commit Messages

Prefer concise commit messages that state the semantic change, for example:

- `Clarify CONTEXT.md dependency precedence`
- `Require SKILLS.md sync for valid SkillBag sources`
- `Define #run/always tag behavior`

## Release Notes and History

If your change is notable for users of the draft, add a short entry to
[CHANGELOG.md](./CHANGELOG.md).

## Questions

If a proposed change is large or changes the model significantly, open a
discussion before implementing it.
