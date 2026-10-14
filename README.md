# Pogon Release — GitHub Action

Links the issues and pull requests of the commits you ship to a release in
[Pogon](https://pogon.me). It runs the Pogon Release CLI: a single JavaScript
file downloaded from `downloads.pogon.me` and checked against the SHA-256
pinned in this action, so a changed file fails the step.

## Continuous pipeline — a release on every deploy

```yaml
on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0          # the CLI reads the commits since the last release
      - uses: krafter-dev/pogon-release@v0
        with:
          key: ${{ secrets.POGON_RELEASE_KEY }}
```

## Scheduled pipeline — collect, then complete

```yaml
      - uses: krafter-dev/pogon-release@v0
        with:
          key: ${{ secrets.POGON_RELEASE_KEY }}
          release-version: ${{ github.ref_name }}
      # when the version ships:
      - uses: krafter-dev/pogon-release@v0
        with:
          key: ${{ secrets.POGON_RELEASE_KEY }}
          command: complete
          release-version: ${{ github.ref_name }}
```

## Inputs

| Input | Meaning |
|---|---|
| `key` | pipeline access key (required, from a secret) |
| `command` | `sync` (default), `complete`, `update` |
| `release-version`, `name`, `description` | target version, release name, description (`" "` clears it) |
| `stage` | stage name for `update` |
| `include-paths` | comma-separated path globs, `!` excludes |
| `include-subjects`, `issue-pattern` | subject filter / identifier regex (group 1) |
| `base-ref` | scan `<base-ref>..HEAD` |
| `links` | one per line, `https://…` or `Label=https://…` |
| `documents` | one file per line, `path` or `Title=path` |
| `release-notes-file` | release notes for this release |
| `no-branch-ref-detection`, `dry-run`, `verbose` | `true` to enable |
| `timeout` | seconds |
| `api-url` | Pogon server, default `https://app.pogon.me` |

Outputs: `release-id`, `release-url` (empty when there was nothing to sync).

The CLI, its documentation and its source live in the Pogon repository; this
repository only carries the action. License notices: `NOTICE`.
