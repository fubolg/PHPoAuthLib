# PHPoAuthLib Architecture

## Package boundary

The repository declares the Composer package `lusitanian/oauth` and maps the
`OAuth\` namespace to `src/OAuth`. The package provides reusable OAuth client
primitives and concrete OAuth 1 and OAuth 2 service implementations. It does
not contain a web application, database schema, deployment configuration, or
tenant model.

The public contract is formed by the classes and interfaces under `src/OAuth`,
including constructors and methods used by consumers. The examples demonstrate
interactive authorization flows, while the PHPUnit suite exercises library
behavior.

## Component map

| Path | Verified responsibility |
| --- | --- |
| `src/OAuth/ServiceFactory.php` | Registers custom service classes, resolves built-in service class names, resolves OAuth 2 scopes, selects OAuth 1 or OAuth 2 construction, and falls back to `StreamClient` when no HTTP client was supplied |
| `src/OAuth/Common/Consumer` | Consumer identifier, secret, and callback URI contract |
| `src/OAuth/Common/Http/Client` | HTTP client contract plus cURL and PHP stream implementations |
| `src/OAuth/Common/Http/Uri` | Mutable URI implementation and factory contracts used by services and clients |
| `src/OAuth/Common/Service` | Protocol-independent service base and service-name/storage access |
| `src/OAuth/Common/Storage` | Token and authorization-state contract plus memory, PHP session, Symfony session, and Redis implementations |
| `src/OAuth/Common/Token` | Protocol-independent token contract and expiry constants |
| `src/OAuth/OAuth1` | OAuth 1 service flow, HMAC-SHA1 signing, OAuth 1 token type, and provider services |
| `src/OAuth/OAuth2` | OAuth 2 authorization, state validation, access-token exchange, refresh flow, authenticated requests, OAuth 2 token type, and provider services |
| `tests/Unit` | Unit coverage for shared components, protocol behavior, the service factory, storage adapters, and provider classes |
| `examples` | Browser-oriented examples using provider-specific configuration |

## Service construction

`OAuth\ServiceFactory` accepts a service name, consumer credentials, token
storage, optional scopes, an optional base API URI, and an optional API version.
It first resolves an OAuth 2 service class and then an OAuth 1 service class.
Consumers can override name-to-class resolution through `registerService()`.

When no HTTP client has been supplied with `setHttpClient()`, the factory
creates a `StreamClient`. OAuth 2 construction resolves symbolic scope names
against constants declared by the service class. OAuth 1 construction rejects
non-empty scopes and supplies an HMAC signature object.

## Shared contracts

Every service receives:

- `CredentialsInterface` for the consumer identifier, consumer secret, and
  callback URL.
- `ClientInterface` for sending requests and returning response bodies.
- `TokenStorageInterface` for access tokens and authorization state.

The bundled storage implementations are:

- `Memory`, which retains values only in the current PHP process.
- `Session`, which stores serialized tokens and state in `$_SESSION`.
- `SymfonySession`, which uses Symfony's `SessionInterface`.
- `Redis`, which stores serialized tokens and state in Redis hashes through
  Predis and also keeps process-local caches.

## OAuth 1 behavior

The OAuth 1 abstract service requests and stores a request token, builds an
authorization URI, exchanges a verifier for an access token, and signs
authenticated API requests. `OAuth\OAuth1\Signature\Signature` implements
HMAC-SHA1 signing and rejects unsupported hashing algorithms.

Concrete provider classes supply endpoints and response parsing. The existence
of a provider class records an implementation in this source tree; it does not
by itself establish current compatibility with the provider's external API.

## OAuth 2 behavior

The OAuth 2 abstract service builds authorization URIs, optionally stores and
validates authorization state, exchanges authorization codes, refreshes tokens,
checks token expiry, and places access tokens according to the authorization
method selected by a provider class.

Concrete provider classes supply endpoints, scopes, authorization method,
extra headers, and token-response parsing. Provider contracts are external to
this repository and can change independently.

## Helix boundary

Helix declares the `fubolg/PHPoAuthLib` repository as a Composer VCS source and
requires the package as `lusitanian/oauth` on `dev-master`. The concrete commit
used by a Helix checkout is recorded in Helix's `composer.lock`.

This repository owns generic OAuth contracts and protocol behavior. Helix owns
its channel-aware configuration, token persistence, HTTP adapters, application
factories, and Helix-specific provider subclasses. See
`docs/helix-integration.md` for the verified consumer paths.
