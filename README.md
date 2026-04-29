# version-matrix-php83-composer27

## Feature exercised

Minimal Composer probe pinning PHP 8.3 and Composer 2.7 to verify that Mend SCA correctly detects all Packagist registry dependencies when run against a specific toolchain version matrix.

## Toolchain pin

| Component | Version |
|---|---|
| PHP | 8.3 |
| Composer | 2.7 |
| Docker image | `composer:2.7-php8.3` |

## Expected dependency tree

### Direct dependencies (`require`)

| Package | Version | Source |
|---|---|---|
| `monolog/monolog` | 3.10.0 | registry (Packagist) |

### Transitive dependencies

| Package | Version | Source | Parent |
|---|---|---|---|
| `psr/log` | 3.0.2 | registry | monolog/monolog |

### Platform packages (NOT in lockfile)

| Constraint | Type |
|---|---|
| `php >=8.3` | platform — runtime constraint only |

### Summary

- Total packages in lockfile: **2**
- Direct (`require`): **1**
- Transitive: **1**
- Dev packages (`require-dev`): **0**
- All sources: **Packagist registry**

## Expected Mend `.whitesource` versioning

```json
{
    "php": "8.3",
    "composer": "2.7"
}
```

## How to reproduce with Docker

```bash
docker run --rm -v "$(pwd):/app" composer:2.7-php8.3 composer install --no-dev --prefer-dist
```

Note: Do NOT commit `/vendor`.

## Mend failure modes exercised

- **Missing transitive deps**: Mend must not drop `psr/log` (the sole transitive of `monolog/monolog`).
- **Wrong resolved version**: Mend must report lockfile version `3.10.0`, not the constraint `^3.0`.
- **Platform package incorrectly included**: `php` is a platform constraint; it must not appear as an installable node in the dependency tree.
- **Toolchain version regression**: Validates that Mend handles Composer 2.7 `plugin-api-version` without misreading the lockfile format.

## Probe metadata

```
pattern:          version-matrix
pm:               composer
pm_version_tested: 2.7
lockfile:         composer.lock
php_minimum:      8.3
composer_version: 2.7
docker_image:     composer:2.7-php8.3
generated:        2026-04-29
target:           remote
repo:             https://github.com/mend-detection-qa/composer-version-matrix-php83-composer27-probe
```