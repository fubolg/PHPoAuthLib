# PHPoAuthLib Agent Guide

## Scope and ownership

- This repository owns reusable OAuth protocol, credential, URI, HTTP, token,
  storage, signature, service-factory, and provider behavior under `src/OAuth/`.
- Generic protocol and provider fixes belong here. Helix owns tenant/channel
  settings, application factories, persistence and transport adapters, and
  Helix-only provider subclasses; do not create a workaround in either
  repository for behavior owned by the other.
- Preserve the package identity, namespace, public interfaces, constructors,
  token representations, storage contracts, service discovery, and exception
  behavior unless an explicit compatibility plan changes them.

## Sources of truth and command discovery

- Inspect `composer.json`, its lock file, PHPUnit and formatter configuration,
  CI, source, focused tests, and code-coupled documentation before making
  runtime, dependency, provider, compatibility, or validation assumptions.
- Dependency manifests define supported constraints; the lock file defines the
  resolved graph; CI and tool configuration define executable validation.
  Report disagreement with documentation, installed dependencies, or running
  services as drift.
- Use Composer scripts to discover current broad checks. The tracked commands
  include `composer validate --strict`, `composer tests`, and `composer
  check`; `composer fix` writes files and is not a validation command.
- Run a focused PHPUnit filter first for a behavioral change, then the relevant
  protocol/provider suite and broader configured checks proportional to risk.

## Protocol, provider, and security rules

- Treat authorization state, callback URIs, OAuth signatures, nonce/timestamp
  behavior, token serialization, expiry, refresh, placement, storage, and HTTP
  authorization as security-sensitive compatibility contracts.
- Provider endpoints, parameters, scopes, token parsing, and authorization
  methods MUST be based on current repository evidence or an authoritative
  provider source. A provider class or old test alone does not prove live
  compatibility.
- Behavioral fixes MUST include focused deterministic regression tests for
  relevant accepted and rejected input, signature, state, token lifecycle,
  provider payload, storage, and failure paths. Test-first sequencing applies
  only when the approved plan requires it.
- Validate affected Helix consumers when public behavior they use changes. Do
  not make live provider requests or access external OAuth accounts without
  separate explicit authorization.
- Never expose real consumer secrets, tokens, authorization codes, callback
  state, private keys, authorization headers, complete environment files, or
  sensitive provider payloads. Tests must use clearly synthetic values.

## Shared AI and SDD infrastructure

- `../helix-ai-infrastructure` owns shared SDD policy, templates, workflows,
  generated agent integrations, bootstrap tooling, and distribution metadata.
- This repository owns this guide, `.specify/memory/constitution.md`, feature
  artifacts under `specs/`, and code-coupled documentation.
- `.helix-ai.yml` pins the reviewed distribution. Do not hand-edit
  `.agents/**` or managed `.specify/**`; preview and apply only a validated
  central distribution.

## Working agreement

- Inspect Git status before editing and preserve unrelated work.
- Search implementations, extensions, tests, docs, and known consumers before
  adding an abstraction or changing a public contract.
- Make the smallest compatible owning-repository change. Define consumer
  compatibility, migration, release order, verification, recovery, rollback,
  and removal conditions for contract changes.
- Do not silently install, upgrade, or rewrite dependencies. Do not start Redis
  for an unrelated task; report an environment-dependent skipped check.
- Review for state-validation bypass, signature errors, token leakage, unsafe
  serialization or clear-all behavior, provider drift, compatibility breaks,
  and missing regression evidence.
- Commit, push, publish, release, production access, external writes, destructive
  actions, and live-provider interaction require separate explicit authorization.
- Report each relevant command as passed, failed, or not run, identify residual
  security/compatibility risk, and provide a suggested commit message.
