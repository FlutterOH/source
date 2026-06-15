<h1 align="center">FlutterOH Source</h1>

<p align="center">
  Official source data consumed by <a href="https://github.com/FlutterOH/fluoh">fluoh</a>.
</p>

<p align="center">
  <a href="https://github.com/FlutterOH/source/actions/workflows/validate.yml"><img src="https://github.com/FlutterOH/source/actions/workflows/validate.yml/badge.svg" alt="validate source"></a>
  <a href="https://github.com/FlutterOH/source/actions/workflows/sync.yml"><img src="https://github.com/FlutterOH/source/actions/workflows/sync.yml/badge.svg" alt="sync source"></a>
  <img src="https://img.shields.io/badge/source%20schema-1-blue" alt="source schema 1">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue" alt="License"></a>
</p>

<p align="center">
  <a href="#quick-start">Quick start</a> ·
  <a href="#source-data">Source data</a> ·
  <a href="#maintenance">Maintenance</a> ·
  <a href="CONTRIBUTING.md">Contributing</a> ·
  <a href="README.zh-CN.md">简体中文</a>
</p>

## Quick Start

`fluoh` uses this official source by default, so most users do not need to clone
this repository.

Refresh source metadata and list available FlutterOH SDK versions:

```sh
fluoh source update
fluoh sdk list
```

Select a FlutterOH SDK line from a Flutter project:

```sh
fluoh sdk use 3.35 --pub-get
```

`fluoh sdk use` initializes `ohos/` by default when the project does not already
have one. Use `--no-init-ohos` only if the OHOS platform files are managed
separately.

Run Flutter through the selected FlutterOH SDK:

```sh
fluohf pub get
fluohf run
fluohf build hap
```

If your goal is to adapt an app or package, start with the
[`fluoh`](https://github.com/FlutterOH/fluoh) CLI and AI skill. This repository
only publishes the source records that `fluoh` reads.

## Source Data

The source contract starts at [`fluoh.yaml`](fluoh.yaml).

| Data | Current status | Used by |
| --- | --- | --- |
| SDK repository | `https://gitcode.com/CPF-Flutter/flutter_flutter.git` | `fluoh sdk` commands |
| SDK versions | `3.35.8-ohos-0.0.2`, `3.35.8-ohos-0.0.3`, `3.35.8-ohos-1.0.1` | `fluoh sdk list` and `fluoh sdk use` |
| Package manifests | None yet | Future official package implementation lookup |

This repository is deliberately source-data focused. It does not contain SDK
binaries, package adaptation code, package release tags, local caches, or the
`fluoh` CLI implementation.

## Repository Layout

- [`fluoh.yaml`](fluoh.yaml): source root manifest, SDK repository, and official
  SDK versions.
- `manifests/`: future package implementation manifests, created as
  `manifests/<manifest-name>/fluoh.yaml` when official package adaptations are
  ready.
- `.github/workflows/`: source validation and scheduled sync automation.
- `.github/ISSUE_TEMPLATE/`: SDK release and package manifest triage templates.
- [`CONTRIBUTING.md`](CONTRIBUTING.md): maintainer workflow, source schema rules,
  package manifest intake, and CI expectations.

## Maintenance

Use a released `fluoh`, or activate the sibling checkout while developing both
repositories:

```sh
cd ../fluoh
dart pub global activate --source path . --overwrite
cd ../source
fluoh --version
```

Validate this checkout after changing source files:

```sh
fluoh source check . --schema-only
```

Add `--json` when a script or CI job needs the machine-readable contract. The
schema-only check is local YAML/index validation; it does not read Git diffs,
fetch SDK tags, clone package repositories, or verify package release tags.

Register this checkout only when local `fluoh` commands should read it. The
registration stores a snapshot:

```sh
fluoh source add local .
fluoh sdk list
```

After changing source files, refresh that snapshot before validating consumer
commands:

```sh
fluoh source update local
fluoh sdk list
```

When package manifests exist, maintainers can refresh released package metadata
from package repositories:

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

`fluoh source sync` imports records from published package release tags only.
Package-side release gates are completed in package repositories before
`fluoh package release`; this Source repository imports and validates the
published metadata.

Before opening a pull request, also run:

```sh
fluoh source check .
git status --short --ignored=matching
git diff --check
```

For large explicit audits, use `fluoh source check . --all` and narrow with
`--manifest`, `--package`, `--shard`, `--concurrency`, or
`--max-release-checks` when needed. Routine CI uses `--skip-release-checks` so
hosted runners validate source YAML and changed-route selection without cloning
package repositories.

## Links

- [`fluoh` CLI and AI skill](https://github.com/FlutterOH/fluoh)
- [`fluoh` command reference](https://github.com/FlutterOH/fluoh/blob/main/doc/commands.md)
- [`fluoh` source schema](https://github.com/FlutterOH/fluoh/blob/main/doc/schema.md)
- [Contributing and maintenance workflow](CONTRIBUTING.md)

## License

MIT
