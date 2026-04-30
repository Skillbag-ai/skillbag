# SkillBag Governance

SkillBag is a specification-first open source project. Governance should keep
the standard precise, provider-neutral, and practical for agent implementers.

## Scope

This document governs changes to the SkillBag standard and the public process
around it. The normative specification remains [SKILLBAG.md](./SKILLBAG.md).
If this document conflicts with `SKILLBAG.md`, the specification controls for
runtime behavior.

## Current Phase

SkillBag is currently in a founder-led draft phase.

During this phase:

- BackupDev UG maintains the repositories and release process.
- Pull requests are reviewed for precision, compatibility, and implementability.
- Normative changes require public review before merge unless they are
  editorial fixes with no semantic effect.
- The project should collect adopter feedback before freezing major semantics.

## Future Working Group

If multiple independent vendors or implementers want formal participation,
SkillBag should form a public working group.

The working group should:

- publish meeting notes or written decisions
- maintain a public proposal process
- separate technical consensus from sponsorship
- require compatibility impact notes for normative changes
- keep conformance claims tied to observable behavior

## Foundation Hosting

If several serious vendors need formal neutrality, SkillBag may move the
standard to a neutral host such as a Linux Foundation, Joint Development
Foundation, or Agentic AI Foundation style structure.

Foundation hosting should be considered when it provides clear value:

- neutral trademark and IP stewardship
- a durable legal home for the specification
- multi-vendor participation without one vendor controlling the standard
- formal membership, sponsorship, and conformance programs

Foundation hosting should not be used merely for appearance if the project has
not yet attracted real implementers.

## Roles

### Maintainers

Maintainers review and merge changes, prepare releases, triage issues, and keep
the specification internally consistent.

Maintainers must not merge a normative change without considering:

- the exact behavior being standardized
- compatibility with existing SkillBag workspaces
- failure behavior and edge cases
- whether the change belongs in the core standard or in utility skills

### Contributors

Contributors propose issues, pull requests, examples, tests, and implementation
feedback.

Non-trivial proposals should include:

- problem statement
- proposed rule text
- compatibility impact
- examples only where they remove ambiguity

### Implementers

Implementers build agent support, validators, package tooling, or hosted
services around SkillBag.

Implementers are encouraged to report ambiguous behavior before shipping
divergent interpretations.

### Sponsors

Sponsors fund shared project work. Sponsorship does not grant private control
over the specification, special conformance status, or the ability to bypass
the public proposal process.

## Decision Process

Changes fall into three categories.

### Editorial Changes

Editorial changes do not alter behavior. They may be merged by maintainers
after normal review.

Examples:

- typo fixes
- clearer wording with no semantic change
- link updates

### Normative Changes

Normative changes alter required, recommended, or allowed behavior.

Normative changes should:

- be proposed publicly
- identify changed MUST, SHOULD, or MAY behavior
- include compatibility impact
- update related documentation and examples
- receive maintainer approval after review

### Governance Changes

Governance changes affect project control, participation, sponsorship, or
conformance claims.

Governance changes should have a longer public review period than ordinary
technical changes and should not be bundled with unrelated specification
changes.

## Releases

Release notes should identify normative changes separately from editorial and
documentation changes.

Versioning should communicate compatibility:

- patch releases clarify or fix without intended semantic breakage
- minor releases add compatible behavior or tighten undefined behavior
- major releases may contain incompatible changes

## Conflicts of Interest

Participants should disclose material conflicts when proposing or deciding on
changes that advantage their own implementation, hosted service, or commercial
offering.

Disclosure does not disqualify participation. It gives other participants the
context needed to evaluate the proposal.

## Neutrality Principles

SkillBag should remain:

- provider-agnostic
- file-based and inspectable
- compatible with the base skill format where practical
- precise about precedence, installation, and failure behavior
- open to independent implementations

