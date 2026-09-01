<!--
Sync Impact Report
- Version change: 1.0.0 -> 1.1.0
- Renamed and materially expanded principles:
  - I. Preserve Public OAuth and Extension Contracts
  - II. Keep Library and Helix Responsibilities Separate
  - III. Protect State, Signatures, Credentials, and Tokens
  - IV. Require Evidence for Provider Contracts
  - V. Evolve Token and Storage Behavior Compatibly
- Added principle:
  - VI. Require Risk-Proportionate Regression Evidence
- Removed test-first wording as a universal obligation; sequencing now follows
  the approved feature plan.
- Removed runtime and dependency versions; tracked executable configuration is
  now the sole source of truth.
- Dependent guidance:
  - UPDATED AGENTS.md
  - REVIEWED current managed templates and skills from helix-ai-infrastructure
  - PENDING central SDD quality-gate update, distribution validation, bootstrap
    regeneration, and pin adoption because `helix-ai-infrastructure` is
    read-only in this session
- Extension hooks: none configured
- Follow-up actions: none local.
-->
# PHPoAuthLib Constitution

## Core Principles

### I. Preserve Public OAuth and Extension Contracts

Public interfaces, abstract classes, constructors, token representations,
storage contracts, service construction and registration, provider names,
extension points, and exception behavior are compatibility-sensitive. They
MUST evolve additively or through explicit versioning. An intentional contract
change MUST define affected consumers, migration, release order, verification,
rollback, removal conditions, and regression coverage before implementation.

### II. Keep Library and Helix Responsibilities Separate

PHPoAuthLib owns reusable OAuth protocol, HTTP, URI, credential, token, storage,
signature, factory, and provider behavior. Helix owns tenant and channel
configuration, application persistence and transport adapters, application
factory composition, and Helix-only provider subclasses. Behavior MUST be fixed
in its owning repository; neither repository may create a competing source of
truth or conceal an owning-repository defect with an undocumented workaround.

### III. Protect State, Signatures, Credentials, and Tokens

Authorization state validation, redirect handling, signature construction,
nonce and timestamp behavior, token placement, serialization, expiry, refresh,
storage, and HTTP authorization are security-sensitive contracts. Secure
validation MUST reject mismatched or malformed state and signature inputs unless
a verified protocol or provider contract explicitly requires different
behavior.

Consumer secrets, tokens, authorization codes, callback state, private keys,
authorization headers, complete environment files, connection URIs, and
sensitive provider payloads MUST NOT appear in code, logs, documentation,
fixtures, prompts, or generated artifacts. Tests MUST use clearly synthetic
values, and exception behavior MUST NOT leak secrets.

### IV. Require Evidence for Provider Contracts

A provider class or historical test proves only repository implementation, not
the current live provider contract. Endpoints, parameters, scopes,
authorization methods, token parsing, signature requirements, and policies MUST
come from current code-coupled evidence or an authoritative provider source.
Time-sensitive or unresolved facts MUST be recorded as unresolved and MUST NOT
be guessed. Live-provider access or mutation requires separate explicit
authorization.

### V. Evolve Token and Storage Behavior Compatibly

Token serialization, expiry interpretation, refresh behavior, storage keys,
retrieval, deletion, and clear operations MUST preserve existing consumer data
and namespace behavior across compatible releases. Retried writes or refreshes
MUST avoid duplicate or destructive effects where the contract permits retry.
A format or namespace change MUST define detection, migration, partial rollout,
consumer release order, recovery, rollback, and removal conditions.

### VI. Require Risk-Proportionate Regression Evidence

Every behavioral change MUST include focused deterministic regression tests at
the smallest level capable of proving the affected protocol, provider, token,
storage, HTTP, or extension contract. Applicable accepted, rejected, boundary,
signature, state, expiry, refresh, serialization, transport, and provider
failure paths MUST be covered. Integration tests MUST be used when storage,
HTTP-client, framework, or consumer behavior cannot be proved in isolation.
Affected Helix consumers MUST be validated when their public contract changes.

Documentation-only or other non-behavioral changes MAY omit application tests
with a stated reason. Test-first sequencing applies only when the approved
feature plan requires it. Configured checks that cannot run MUST be reported
with the command and reason and MUST NOT be represented as passed.

## Technical and Security Constraints

- Dependency manifests define supported constraints; the lock file defines
  resolved dependencies; CI, PHPUnit, formatter, and other tool configuration
  define executable validation. These tracked files MUST be inspected before
  runtime, compatibility, or command assumptions are made.
- Production code and tests MUST remain in their tracked namespace and
  directory structure unless an explicit compatible reorganization is in scope.
- Disagreement between tracked configuration, documentation, installed
  dependencies, running services, or consumer assumptions is drift and MUST be
  reported.
- Feature artifacts live under `specs/`. Shared SDD policy and generated
  assets are owned by `helix-ai-infrastructure`, pinned by `.helix-ai.yml`,
  and MUST be updated only through the validated central distribution.

## Development and Review Workflow

1. Identify the affected protocol, provider, public extension, token, storage,
   security, and consumer contracts from source and focused tests.
2. Verify time-sensitive provider facts from authoritative evidence and record
   unresolved facts rather than guessing.
3. Specify successful, rejected, boundary, failure, retry, migration, recovery,
   rollback, and consumer outcomes as applicable.
4. Define focused regression tests; order them before implementation only when
   the approved plan requires test-first delivery.
5. Implement the smallest compatible owning-repository change and keep
   Helix-only policy in Helix.
6. Run focused validation before broader configured and affected-consumer
   checks. Review for secret exposure, state bypass, signature errors, token
   loss, provider drift, and compatibility regressions, and report every check
   as passed, failed, or not run.

## Governance

This constitution governs PHPoAuthLib specifications, plans, tasks,
implementations, and reviews. `AGENTS.md` operationalizes these principles.
Amendments require an evidence-based rationale, an accurate Sync Impact Report,
preservation of the ratification date, a semantic constitution version change,
and updates to affected repository-owned guidance.

Shared SDD behavior MUST change first in `helix-ai-infrastructure`, pass its
owned validation and temporary bootstrap checks, and then be adopted through
the reviewed bootstrap workflow. Any conflict with a MUST principle blocks
implementation unless a reviewed, explicitly scoped exception records its
risk, duration, and removal condition.

**Version**: 1.1.0 | **Ratified**: 2026-07-28 | **Last Amended**: 2026-09-01
