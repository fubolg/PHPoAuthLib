# Development Workflow

## Prerequisites

The package manifest declares:

- PHP `^7.2 || 8.*`.
- Composer for dependency installation and scripts.
- DOM, cURL, and JSON extensions for development dependency installation.
- PHPUnit `^8.5`.
- PHP CS Fixer `^3.0`.

The repository does not contain a Dockerfile or Compose configuration.

## Setup

Install the versions recorded in `composer.lock`:

```bash
composer install
```

Do not update `composer.lock` merely to restore a missing `vendor/` directory.
Dependency installation may require network access.

## Source layout

Production code is under `src/OAuth` and is autoloaded through the `OAuth\`
PSR-4 namespace. Tests are under `tests`, with unit-test classes autoloaded from
`tests/Unit` and mocks from `tests/Mocks`.

Add generic protocol behavior, public contracts, and reusable provider
implementations to this repository. Keep tenant-aware storage, channel
settings, application orchestration, and Helix-only provider behavior in
`../helix`.

## Change workflow

Before editing:

```bash
git status --short
```

Search the implementation and its tests before changing public behavior.
Provider-specific changes require evidence from the requested provider
contract; do not update endpoints, scopes, parameters, or parsing based on an
assumption.

Run the smallest focused test first:

```bash
./vendor/bin/phpunit --filter ServiceFactoryTest
```

Then run the repository scripts appropriate to the change:

```bash
composer validate --strict
composer tests
composer check
```

`composer check` runs PHP CS Fixer as a dry run before PHPUnit. `composer fix`
changes files and should be used only when formatting changes are intended.

After editing, inspect the complete diff and run:

```bash
git diff --check
```

## Cross-repository changes

Helix consumes this package through a VCS repository entry and the
`lusitanian/oauth` requirement. A source checkout beside Helix does not replace
the version installed under Helix's `vendor/`; `composer.lock` remains
authoritative for the installed commit.

When a change affects a public class, interface, constructor, factory,
serialization format, or exception used by Helix:

1. Validate the focused PHPoAuthLib behavior here.
2. Identify the affected Helix imports and factories.
3. Validate the affected Helix tests against the intended package revision.
4. Coordinate the library and consumer release order.

Do not patch Helix to conceal a generic defect owned by this library.
