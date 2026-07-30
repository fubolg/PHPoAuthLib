# PHPoAuthLib Agent Guide

## Scope and ownership

- This repository provides the `lusitanian/oauth` PHP library under the
  `OAuth\` namespace. It contains protocol-independent credentials, URI, HTTP
  client, token, and storage contracts; OAuth 1 signing and service behavior;
  OAuth 2 authorization, token, refresh, and request behavior; and concrete
  provider service classes.
- `src/OAuth/ServiceFactory.php` owns generic service discovery, custom service
  registration, scope resolution, default HTTP-client selection, and OAuth 1
  versus OAuth 2 construction.
- `src/OAuth/Common` owns shared credentials, HTTP, URI, service, storage, and
  token abstractions and their bundled implementations.
- `src/OAuth/OAuth1` owns OAuth 1 service flow, HMAC-SHA1 signing, OAuth 1
  tokens, and OAuth 1 provider classes.
- `src/OAuth/OAuth2` owns OAuth 2 service flow, authorization state, token
  refresh, token placement, OAuth 2 tokens, and OAuth 2 provider classes.
- `../helix` selects this repository as the VCS source for the
  `lusitanian/oauth` Composer package. Helix-specific factories, token
  persistence, transport adapters, and provider subclasses remain in Helix.
- Change generic OAuth behavior and public library contracts here. Change
  tenant-aware storage, channel settings, application orchestration, and
  Helix-only provider behavior in `../helix`.
- See `docs/architecture.md` for verified component boundaries and
  `docs/helix-integration.md` for the consumer boundary.

## Shared AI and SDD infrastructure

- `../helix-ai-infrastructure` owns shared policy and generated Spec Kit assets.
- This repository owns this guide, `.specify/memory/constitution.md`, local
  feature artifacts under `specs/`, and code-coupled documentation.
- `.helix-ai.yml` pins the reviewed distribution. Do not hand-edit generated
  `.agents/` or managed `.specify/` files; change shared sources centrally.

## Runtime and compatibility

- `composer.json` declares PHP `^7.2 || 8.*`.
- Production autoloading maps `OAuth\` to `src/OAuth`.
- Development dependencies declare PHPUnit `^8.5` and PHP CS Fixer `^3.0`.
- Development dependency installation requires the DOM, cURL, and JSON
  extensions according to `composer.json`.
- The Symfony session and Redis storage implementations depend on the optional
  Symfony HttpFoundation and Predis packages declared in `require-dev` and
  `suggest`.
- The historical Travis configuration lists PHP 7.2, 7.3, 7.4, and 8.0. Do
  not represent that file as evidence of validation on newer PHP versions.

## Working agreement

- Inspect `git status` before editing and preserve unrelated user changes.
- Search the current implementation and focused unit tests before adding an
  abstraction or changing a public method.
- Preserve the `OAuth\` namespace, Composer package identity, public
  interfaces, constructor contracts, token serialization behavior, and service
  factory behavior unless the requested change explicitly alters them.
- Make the smallest change that addresses the verified requirement.
- Treat OAuth provider endpoint, parameter, scope, token parsing, and
  authorization-method changes as provider-contract changes. Add or update
  focused tests and document the evidence for the new contract.
- Do not infer that a provider is currently compatible merely because a
  provider class or unit test exists. No live-provider validation workflow is
  configured in this repository.
- Validate affected consumers in `../helix` after changing public behavior used
  by Helix.
- Do not commit, push, publish, access external OAuth accounts, or make live
  provider requests unless the user explicitly requests that action.
- Never print or store real consumer secrets, access tokens, refresh tokens,
  authorization codes, callback state, or complete environment files.
- After editing, review the complete diff, run the narrowest relevant
  validation, report checks that passed, failed, or could not run, and provide
  a suggested commit message.

## Canonical validation

- `composer validate --strict`
- `composer tests`
- `composer check`
- `./vendor/bin/phpunit --filter <TestName>`

`composer tests` runs the PHPUnit suite. `composer check` runs PHP CS Fixer in
dry-run mode and then PHPUnit. `composer fix` modifies files and is not a
validation command.

The Redis unit test connects to `127.0.0.1:6379` by default and marks itself
skipped when Redis is unavailable. Do not start Redis implicitly for an
unrelated task. If `vendor/` is missing, dependency installation requires a
separate Composer install step and may require network access.

## Security-sensitive changes

- Treat credential handling, callback URIs, authorization state, token
  serialization, token expiry, refresh tokens, OAuth 1 signature construction,
  and HTTP authorization headers as security-sensitive contracts.
- Preserve authorization-state validation unless a verified provider contract
  requires a scoped change.
- Keep secrets out of fixtures and examples; use obvious non-secret values in
  tests.
- Review storage changes for cross-service token collisions, unsafe
  serialization behavior, unintended clear-all operations, and state/token
  namespace changes.
- Review HTTP client changes for credential leakage through query strings,
  headers, redirects, logs, and exception messages.

## Code reviews

- Prioritize authentication correctness, secret or token exposure, signature
  construction, state validation, backward compatibility, provider regressions,
  and missing tests.
- Report actionable findings ordered by severity with file and line references.
- Avoid a change summary unless the user asks for one.
