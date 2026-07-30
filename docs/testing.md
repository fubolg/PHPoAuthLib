# Testing

## Configured suite

`phpunit.xml.dist` bootstraps `vendor/autoload.php` and runs the `tests`
directory as its unit-test suite. Coverage configuration includes `src` and
excludes selected exception and token files.

The canonical Composer scripts are:

| Command | Behavior |
| --- | --- |
| `composer tests` | Runs `vendor/bin/phpunit --color=always` |
| `composer check` | Runs PHP CS Fixer in dry-run/diff mode, then PHPUnit |
| `composer fix` | Runs PHP CS Fixer in write mode |

For a focused check, call PHPUnit directly:

```bash
./vendor/bin/phpunit --filter <TestName>
```

## Redis test

`tests/Unit/Common/Storage/RedisTest.php` reads `redis_host` and `redis_port`
from the PHPUnit environment. `phpunit.xml.dist` sets them to
`127.0.0.1` and `6379`.

The test attempts to connect through Predis and marks itself skipped when a
Redis connection is unavailable. The suite does not start Redis.

## Validation boundaries

The PHPUnit suite covers local library behavior. The repository has no
configured workflow that authenticates against live OAuth providers.
Therefore:

- A passing suite does not establish that an external provider's current
  endpoints or response formats remain compatible.
- Provider-contract changes need focused unit tests and separately verified
  source requirements.
- Live OAuth credentials must not be added to tests, examples, configuration,
  logs, or documentation.

The tracked `.travis.yml` lists PHP 7.2, 7.3, 7.4, and 8.0. Treat it as
historical configuration unless an active CI run proves otherwise.
