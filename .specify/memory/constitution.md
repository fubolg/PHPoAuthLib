<!--
Sync Impact Report
- Version change: template -> 1.0.0
- Added principles:
  - I. Preserve Public OAuth Contracts
  - II. Keep Library and Helix Responsibilities Separate
  - III. Protect Credentials, Tokens, State, and Signatures
  - IV. Validate Against the Configured Toolchain
  - V. Require Evidence for Provider Contracts
- Added sections:
  - Technical and Security Constraints
  - Development and Review Workflow
- Templates updated:
  - .specify/templates/plan-template.md
  - .specify/templates/spec-template.md
  - .specify/templates/tasks-template.md
- Templates reviewed without changes:
  - .specify/templates/checklist-template.md
  - .specify/templates/constitution-template.md
- Runtime guidance reviewed:
  - AGENTS.md
  - .agents/skills/*
  - docs/*
- Follow-up TODOs: none
-->

# PHPoAuthLib Constitution

## Core Principles

### I. Preserve Public OAuth Contracts

Changes to public interfaces, abstract classes, token representations, storage
contracts, service construction, provider names, and exception behavior MUST be
treated as compatibility-sensitive. An implementation MUST first search the
existing source and tests, state the affected contract, and preserve it unless
a breaking change is an explicit requirement. Any intentional contract change
MUST include migration guidance and tests for both the intended behavior and
relevant failure paths.

### II. Keep Library and Helix Responsibilities Separate

PHPoAuthLib MUST own reusable OAuth 1, OAuth 2, HTTP, URI, credential, token,
storage, signature, and provider behavior. Helix MUST own tenant and channel
configuration, persistence adapters, application-level factory composition,
transport adapters, and Helix-specific provider subclasses. A defect MUST be
fixed in the repository that owns the behavior; neither repository may add a
workaround that conceals an owning-repository defect without an explicitly
documented compatibility reason.

### III. Protect Credentials, Tokens, State, and Signatures

Credentials, client secrets, access tokens, refresh tokens, authorization
codes, private keys, and complete environment files MUST NOT be committed,
logged, printed, or copied into documentation or fixtures. OAuth state
validation, redirect handling, token placement, token expiry, refresh behavior,
and OAuth 1 signature construction are security-sensitive. Changes to them MUST
include focused tests for successful and rejected inputs, and MUST retain secure
defaults unless an explicit, documented contract requires otherwise.

### IV. Validate Against the Configured Toolchain

The repository's Composer metadata, lock file, PHPUnit configuration, formatter
configuration, and CI configuration are the authoritative evidence for
available validation. The narrowest relevant test MUST run first, followed by
broader checks when the change affects shared protocol or provider behavior.
Validation MUST NOT silently install, upgrade, or rewrite dependencies. If the
configured environment is unavailable, the exact unrun command and reason MUST
be reported.

### V. Require Evidence for Provider Contracts

A provider class in the source tree proves only that an implementation exists;
it does not prove that the external provider's live API, endpoints, scopes, or
policies remain compatible. Specifications and documentation MUST distinguish
source-verified behavior from externally verified behavior. Missing or
time-sensitive provider facts MUST be marked `NEEDS CLARIFICATION` or verified
against an authoritative provider source before implementation; they MUST NOT
be guessed.

## Technical and Security Constraints

- The package identity, PHP constraint, autoload namespace, and dependency
  contract are defined by `composer.json`; the exact installed dependency graph
  is defined by `composer.lock`.
- Production code belongs under `src/OAuth/` and tests under `tests/Unit/`,
  following the existing namespace and directory structure.
- New abstractions require evidence that an existing interface, abstract class,
  trait, provider pattern, or storage pattern cannot express the behavior.
- Generic provider or protocol behavior belongs in this repository. Helix-only
  behavior belongs in the Helix repository even when it extends this package.
- Documentation MUST cite repository paths for architectural claims and MUST
  not claim live provider compatibility without current external verification.

## Development and Review Workflow

1. Inspect repository status and preserve unrelated work.
2. Identify the affected public contract and search its implementations,
   consumers, and tests before planning a change.
3. Record unresolved provider or consumer facts as `NEEDS CLARIFICATION`.
4. Write or update focused tests for compatibility, protocol, or security
   behavior before changing the implementation when executable dependencies
   are available.
5. Make the smallest change in the owning repository and update relevant
   documentation.
6. Review the diff, run the narrowest validation followed by the appropriate
   package check, and report any validation that could not run.
7. Code review MUST prioritize credential exposure, state validation,
   signature correctness, token lifecycle, compatibility regressions, and
   missing tests.

## Governance

This constitution governs specifications, plans, task lists, implementations,
and reviews in PHPoAuthLib. Amendments require a documented reason, an updated
Sync Impact Report, and corresponding changes to dependent templates or
guidance. Semantic versioning applies to the constitution: MAJOR for a removed
or redefined principle, MINOR for a new principle or materially expanded
requirement, and PATCH for clarifications that do not change obligations.
Reviews MUST verify compliance; any exception MUST be explicit in the plan's
Complexity Tracking section and include a reason and rejected simpler option.
Runtime repository guidance remains in `AGENTS.md`.

**Version**: 1.0.0 | **Ratified**: 2026-07-28 | **Last Amended**: 2026-07-28
