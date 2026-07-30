# Helix Integration

## Dependency declaration

Helix's root `composer.json` contains a VCS repository entry for
`https://github.com/fubolg/PHPoAuthLib.git` and requires
`lusitanian/oauth` as `dev-master`. Helix's `composer.lock` records the exact
source revision installed for a checkout.

This sibling source directory is not a Composer path repository. Editing it
does not alter the code already installed in Helix's `vendor/` directory.

## Verified consumers

Helix imports the `OAuth\` namespace in these areas:

- `src/Helix/OauthClientBundle`, which contains application factories,
  channel-aware token storage, HTTP clients, and Helix-specific OAuth service
  subclasses.
- `src/Helix/TransportAdapterBundle`, which uses the common service, URI, HTTP
  client, credentials, and OAuth 1 signature contracts.
- Integration facades and services under the Amazon, ChannelAdvisor,
  GoogleAds, and Walmart bundles.
- Unit tests for the OAuth client and transport adapter bundles.

The list describes verified source references and does not claim that every
external integration is currently enabled or operational.

## Ownership rule

Change this repository for:

- Generic OAuth service construction.
- Shared HTTP, URI, credential, token, and storage contracts.
- OAuth 1 signing and protocol flow.
- OAuth 2 authorization, state, expiry, refresh, and token-placement behavior.
- Reusable provider service implementations.

Change Helix for:

- Tenant- or channel-aware credential and token handling.
- Application service registration and factory chaining.
- Helix transport adapters.
- Helix-only provider subclasses and application workflows.

Public library changes can require coordinated changes in both repositories.
Validate the library first, then the affected Helix consumers against the
intended Composer revision.
