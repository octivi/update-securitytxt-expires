# Update security.txt Expires GitHub Action

[![Marketplace](https://img.shields.io/badge/GitHub%20Marketplace-blue?logo=github)](https://github.com/marketplace/actions/update-securitytxt-expires)
[![GitHub release](https://img.shields.io/github/v/release/octivi/update-securitytxt-expires?sort=semver)](https://github.com/octivi/update-securitytxt-expires/releases)
[![license](https://img.shields.io/github/license/octivi/update-securitytxt-expires)](https://choosealicense.com/licenses/mit/)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org/)
[![semver](https://img.shields.io/badge/SemVer-2.0.0-blue)](https://semver.org/spec/v2.0.0.html)

This GitHub Action updates the Expires field in `security.txt` files to a future date so your
published security contact metadata stays current. The `security.txt` file is published at
`/.well-known/security.txt` (in repo layout: `./.well-known/security.txt`) and is specified in
[RFC 9116](https://www.rfc-editor.org/rfc/rfc9116.html).

## What the project does

This action scans target directories for files at `*/.well-known/security.txt` and updates the
`Expires:` field to a future date. By default, it sets the date to `now + 180 days`.
The path and format come from [RFC 9116](https://www.rfc-editor.org/rfc/rfc9116.html).

## Why the project is useful

`security.txt` should contain a valid future `Expires` timestamp. This action automates keeping that
value fresh, so security contact metadata does not become stale.

## Getting started

1) Add the action to your workflow (see "Example workflow" below).
2) Optionally set `targets`, `exclude_paths` and `expires_days`.
3) Run the workflow and review changes (optionally create a PR in your workflow).

## Inputs

| Input | Description | Required | Default |
| --- | --- | --- | --- |
| `targets` | Space-separated directories to scan. | No | `"."` |
| `exclude_paths` | Space-separated paths to exclude. | No | `""` |
| `expires_days` | Number of days to add to current date. | No | `"180"` |

## CLI usage

Run locally from this repo:

```bash
./update-securitytxt-expires \
  --targets ". src scripts" \
  --exclude-paths "build dist" \
  --expires-days "180"
```

Options map 1:1 to the action inputs. You can also set them via env vars:
`TARGETS`, `EXCLUDE_PATHS`, `EXPIRES_DAYS`.

For deterministic local tests you can also set `CURRENT_DATE`, for example:

```bash
CURRENT_DATE="2025-01-01" ./update-securitytxt-expires --targets "."
```

## Examples

Minimal workflow usage:

```yml
- uses: octivi/update-securitytxt-expires@v1
```

With explicit configuration:

```yml
- uses: octivi/update-securitytxt-expires@v1
  with:
    targets: ". docs"
    exclude_paths: "vendor"
    expires_days: "120"
```

## Tests

Run the lightweight test suite (no external deps):

```bash
./tests/run
```

Set `CURRENT_DATE` to make tests deterministic.

## Limitations

- Uses GNU `sed` options available on `ubuntu-latest` and `ubuntu-slim` runners.
- Uses GNU `date -d` syntax (Linux/GNU date).
- Updates only files matching `*/.well-known/security.txt`.
- Replaces an existing `Expires:` line; it does not add the field if missing.
- Exclude paths are resolved per target directory; absolute paths are matched as-is.

## Example workflow

```yml
name: Update security.txt Expires field

on:
  workflow_dispatch:

jobs:
  update-securitytxt:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: octivi/update-securitytxt-expires@v1
        with:
          targets: ". src scripts"
          exclude_paths: "build"

      # Optional: create PR in your own workflow
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v8
        with:
          branch: update-securitytxt-expires
          delete-branch: true
          commit-message: "chore: Update security.txt Expires field"
          title: Update security.txt Expires field
          body: |
            Automated update of Expires field in `security.txt`.
          labels: |
            automation
            maintenance
```

## Getting help

If you run into issues, open a GitHub issue in this repository and include a minimal reproduction
(sample file + workflow snippet).

## Maintainers and contributors

Maintained by the [Octivi DevOps team](https://octivi.com/devops). Contributions are welcome via
pull requests.
