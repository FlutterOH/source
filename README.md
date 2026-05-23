# FlutterOH Source

[![validate source](https://github.com/FlutterOH/source/actions/workflows/validate.yml/badge.svg)](https://github.com/FlutterOH/source/actions/workflows/validate.yml)
[![sync source](https://github.com/FlutterOH/source/actions/workflows/sync-source.yml/badge.svg)](https://github.com/FlutterOH/source/actions/workflows/sync-source.yml)
[![License](https://img.shields.io/badge/license-see%20LICENSE-blue)](LICENSE)

[中文](README.zh-CN.md)

`FlutterOH/source` is the official `fluoh` source repository. It contains the
source data that `fluoh` consumes for FlutterOH SDK discovery today, and for
official package implementation manifests when package adaptations are ready.

This repository is intentionally small. The source contract starts at
[`fluoh.yaml`](fluoh.yaml); consumers should read it through `fluoh source`
commands instead of depending on internal file paths.

## Source Data

| Surface | Status | Location |
| --- | --- | --- |
| FlutterOH SDK releases | Configured | [`fluoh.yaml`](fluoh.yaml) `sdk` |
| Package implementation manifests | Empty | [`manifests/`](manifests/) |
| Maintainer workflow | Documented | [`CONTRIBUTING.md`](CONTRIBUTING.md) |

Current SDK versions:

- `3.35.8-ohos-0.0.3`
- `3.35.8-ohos-0.0.2`

No package implementation manifests are included yet. A source with SDK data and
no package data is valid.

## Use With `fluoh`

`fluoh` is configured to use this official source by default:

```sh
fluoh source update
fluoh sdk list
```

From a Flutter project, select an SDK version or series:

```sh
fluoh sdk use 3.35
```

Validate edits to this repository without changing local `fluoh` config:

```sh
fluoh source validate
```

Use an explicit local source only when you want to register this checkout or a
private mirror for consumer commands. This command stores a validated snapshot,
so rerun it after changing source files:

```sh
fluoh source add local .
fluoh sdk list
```

## Repository Layout

```text
fluoh.yaml                 # Source root manifest and SDK version list
manifests/                 # Future package implementation manifests
.github/workflows/         # Source validation and scheduled sync automation
.github/ISSUE_TEMPLATE/    # Source-data triage templates
README*.md                 # Public usage documentation
CONTRIBUTING*.md           # Maintainer workflow
AGENTS.md                  # Local agent and maintainer instructions
```

The repository should stay limited to these source files, documentation,
workflow files, and GitHub templates.

## Maintainer Workflow

Maintain this repository with an installed `fluoh` or the sibling `../fluoh`
checkout:

```sh
cd ../fluoh
dart pub global activate --source path . --overwrite
cd ../source
```

New source scaffolds are created with `fluoh source init <path>`. This official
repository has already been initialized, so routine maintenance should edit the
source data directly or refresh package release records with `fluoh source sync`.

SDK releases are edited in root [`fluoh.yaml`](fluoh.yaml). Add only complete
SDK repository tags that should be visible through `fluoh sdk list`.

Package data belongs under `manifests/<route>/fluoh.yaml`. Add a root
`manifests` route only after the corresponding manifest exists and is ready for
official consumption. Package adaptation code, package release tags, and
package-owned `fluoh.yaml` files belong in the package repositories, not here.

## Contributing a Package

Before a package can be added here, the FlutterOH package repository must
already have a package-owned `fluoh.yaml` and at least one release tag created by
`fluoh pub release`. `FlutterOH/source` only records released source metadata that
`fluoh` should make available to users.

First-time intake:

```sh
git clone https://github.com/FlutterOH/source.git
cd source
```

1. Add a `manifests` route in root `fluoh.yaml`.
2. Create the matching `manifests/<route>/fluoh.yaml`.
3. Record the package repository, upstream repository, package paths, SDK line,
   and released package records.
4. Run validation.
5. Open a pull request.

The manifest route name should normally match the package or package group, for
example `camera` or `flutter_packages`. The route in root `fluoh.yaml` must point
to an existing `manifests/<route>/fluoh.yaml` file in the same pull request.

Validation:

```sh
fluoh source validate
git status --short --ignored=matching
git diff --check
```

Use `fluoh source sync .` only after the route manifest exists. It refreshes
release records from package repository tags; it does not replace the first
manifest pull request.

After the first route pull request is merged, package maintainers do not need to
open a new `FlutterOH/source` pull request for every package release. Publish new
package release tags in the package repository; scheduled sync can keep release
records current. Maintainers can also run the `sync source` GitHub Actions
workflow manually when a release should be imported before the next schedule.
Open another `FlutterOH/source` pull request only when route metadata, package
paths, advisory text, maintenance state, or source workflow rules need to
change.

See [CONTRIBUTING.md](CONTRIBUTING.md) for source data rules, package manifest
workflow, GitHub templates, and CI expectations.

## License

See [LICENSE](LICENSE).
