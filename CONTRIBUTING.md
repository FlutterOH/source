# Contributing and Maintenance

[中文](CONTRIBUTING.zh-CN.md)

This guide covers maintenance for the official `FlutterOH/source` source
repository. It is source data for `fluoh`, not a package adaptation repository
and not the `fluoh` CLI implementation.

## Scope

This repository is responsible for:

- Maintaining official FlutterOH SDK release metadata in root `fluoh.yaml`.
- Maintaining official package implementation manifests under `manifests/` when
  package adaptations are ready and approved.
- Keeping README files, maintainer guidance, issue templates, pull request
  templates, and workflows aligned with the current source contract.

This repository is not responsible for:

- Implementing `fluoh`.
- Installing or caching SDKs for users.
- Rewriting user project dependencies.
- Hosting package adaptation code, package release tags, or package-owned
  metadata.

## Source Contract

Root [`fluoh.yaml`](fluoh.yaml) must stay in the current source-root schema:

```yaml
schema: 1
kind: source
name: FlutterOH official source
description: Official FlutterOH SDK releases and package implementation metadata.

repository:
  git:
    url: https://github.com/FlutterOH/source.git

environment:
  fluoh: '>=0.1.0'

sdk:
  git:
    url: https://gitcode.com/CPF-Flutter/flutter_flutter.git
  versions:
    - 3.35.8-ohos-1.0.1
    - 3.35.8-ohos-0.0.3
```

Rules:

- `schema` is `1`, and `kind` is `source`.
- `repository.git.url` is the public official source repository URL.
- `environment.fluoh` records the expected `fluoh` CLI constraint.
- `sdk.git.url` is the FlutterOH SDK repository.
- `sdk.versions` contains complete installable SDK tags.
- `manifests` is absent while no package manifests are included. When present,
  each manifest entry maps to `manifests/<name>/fluoh.yaml`.

Keep repository contents limited to the current source layout, documentation,
workflow files, and GitHub templates.

## SDK Release Updates

Only add SDK versions that satisfy all of the following:

- The tag exists in `sdk.git.url`.
- The version is intended to be visible in `fluoh sdk list`.
- The version is a complete SDK tag, for example `3.35.8-ohos-1.0.1`.
- Documentation and issue/PR templates do not need workflow changes, or they
  are updated in the same pull request.

Keep the list deterministic. Prefer newest versions first if the source starts
to include more than one patch in a line.

## Package Manifest Updates

Package manifests are source data generated or curated from package repositories
after package releases exist. They are not a place to develop the adaptation.

An external package contribution is ready for `FlutterOH/source` only after the
package repository has completed its own release workflow:

- The package repository has a package-owned `fluoh.yaml`.
- The package repository records the FlutterOH repository URL, upstream URL and
  branch, package paths, SDK version, adapted upstream version, and release
  version.
- Release readiness has been reviewed with `fluoh package status`, and
  `fluoh package check` passed before release.
- At least one release tag has been published with `fluoh package release`.

Do not add package code, package release tags, unverified readiness claims, or
unreleased package metadata to this repository.

Expected manifest layout:

```text
manifests/
  camera/
    fluoh.yaml
```

Manifest files use `kind: manifest`, and the manifest `name` must match the
root manifest entry:

```yaml
schema: 1
kind: manifest
name: camera

repository:
  git:
    url: https://github.com/FlutterOH/camera.git

upstream:
  git:
    url: https://github.com/flutter/packages.git
    branch: main

packages:
  camera:
    repository:
      path: packages/camera/camera
    upstream:
      path: packages/camera/camera
    sdks:
      "3.35":
        releases:
          - version: "0.1.0"
            upstreamVersion: "0.11.0"
```

Rules:

- Add a root `manifests` entry only when the manifest file exists and validates.
- A package name must appear in only one official manifest.
- Omit release `status` for normal published records. Use `experimental` or
  `broken` only for published records that should not be recommended by default.
- Use `maintenance` and `advisory` for package-level guidance; do not invent
  machine statuses outside the `fluoh` schema.
- Package adaptation code and release tags remain in the package repository.

Use `fluoh source sync` to refresh release records from package repositories
when manifests already exist and point at released package repos. Edit manifest
files directly for manifest metadata, advisory text, and frozen maintenance
notes. `fluoh source sync` does not discover new official manifests by itself;
the first pull request for a package must add both the root manifest entry and
the matching `manifests/<manifest-name>/fluoh.yaml`.

First-time package intake uses a pull request to this repository:

1. Fork or clone `FlutterOH/source`.
2. Choose a stable manifest name, normally the package name or package group
   name.
3. Add the manifest entry to root `fluoh.yaml`:

   ```yaml
   manifests:
     - name: camera
   ```

4. Create `manifests/<manifest-name>/fluoh.yaml` with repository, upstream, package
   paths, SDK line, and records from published package release tags.
5. Run `fluoh source validate` and `git diff --check`.
6. Open a pull request that lists the manifest name, package repository,
   published release tag, and source validation result. Upstream paths, SDK
   lines, and release records should be verifiable from the manifest diff and
   the published package release tag.

After the manifest is merged, `.github/workflows/sync.yml` can keep release
records current from package repository tags. For manual refreshes after a
manifest exists, run:

```sh
fluoh source sync .
fluoh source validate
git diff --check
```

Maintainers can also trigger the `sync source` workflow from GitHub Actions for
an immediate repository-side refresh before the next scheduled run.

Package maintainers do not need a new `FlutterOH/source` pull request for every
package release after the first manifest pull request is merged. New package
versions should be released in the package repository with
`fluoh package release` after `fluoh package status` and
`fluoh package check`; the scheduled sync workflow can import those tags.
Open another source-data pull request only for metadata changes that cannot be
derived from release tags, such as manifest name changes, repository or package
path corrections, advisory text, maintenance state, or workflow changes.

## Local Maintenance Setup

Use an installed `fluoh`, or activate the sibling checkout while developing both
repositories:

```sh
cd ../fluoh
dart pub global activate --source path . --overwrite
cd ../source
fluoh --version
```

`fluoh source init <path>` creates new source scaffolds. This official
repository has already been initialized; routine maintenance edits the source
files directly.

For routine local validation from this repository:

```sh
fluoh source validate
```

Only add a local source when consumer commands should use this checkout. Local
sources are snapshots, so add a fresh snapshot after changing source files:

```sh
fluoh source add local .
fluoh sdk list
```

Before opening a pull request, run:

```sh
fluoh source validate
git diff --check
```

Package readiness checks, package tests, and app compatibility checks belong in
the package repository before `fluoh package release`; this repository validates
only released source metadata.

## GitHub Workflow

`.github/workflows/validate.yml` validates the checked-out source with `fluoh`.
It should remain focused on source validation:

- Install the released `fluoh` from pub.dev, falling back to the default
  `FlutterOH/fluoh` branch if the package is not available.
- Validate the checkout through `fluoh source validate`.

Source validation belongs to the `fluoh` CLI.

`.github/workflows/sync.yml` runs daily and also supports
`workflow_dispatch` for temporary manual runs from GitHub Actions. It checks
whether root `fluoh.yaml` declares manifests. If no manifests exist, the
workflow exits successfully without changing files. Once manifests exist, it runs:

```sh
fluoh source sync .
fluoh source validate
git diff --check
```

When sync changes source data, the workflow commits directly to `main`.

## Pull Requests

Pull requests should describe:

- User-visible source data changes.
- Added or removed SDK versions.
- Added, removed, or changed package manifests.
- Validation commands and results.
- Any maintainer workflow, issue template, PR template, or CI impact.

Keep commits focused. Conventional Commits are preferred:

```text
docs: refresh official source maintenance guide
ci: validate source with fluoh
feat(sdk): add 3.35.8-ohos-0.0.4
feat(source): add camera manifest
```

Do not commit credentials, local caches, `.DS_Store`, generated build output,
or machine-specific paths.
