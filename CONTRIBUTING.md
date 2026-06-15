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
name: flutteroh
description: Official FlutterOH SDK releases and package implementation metadata.

repository:
  git:
    url: https://github.com/FlutterOH/source.git

sdk:
  git:
    url: https://gitcode.com/CPF-Flutter/flutter_flutter.git
  versions:
    - 3.35.8-ohos-0.0.2
    - 3.35.8-ohos-0.0.3
    - 3.35.8-ohos-1.0.1
```

Rules:

- `schema` is `1`, and `kind` is `source`.
- `name` is the Source token. The official Source uses `flutteroh`.
- `repository.git.url` is optional Source self-description.
- `sdk.git.url` is the FlutterOH SDK repository.
- `sdk.versions` contains complete installable SDK tags in ascending semantic
  version order.
- `manifests` is absent while no package manifests are included. When present,
  each manifest route entry maps to `manifests/<name>/fluoh.yaml`.

Keep repository contents limited to the current source layout, documentation,
workflow files, and GitHub templates.

## SDK Release Updates

Only add SDK versions that satisfy all of the following:

- The tag exists in `sdk.git.url`.
- The version is intended to be visible in `fluoh sdk list`.
- The version is a complete SDK tag, for example `3.35.8-ohos-1.0.1`.
- Documentation and issue/PR templates do not need workflow changes, or they
  are updated in the same pull request.

Keep the list deterministic. Sort SDK versions in ascending semantic version
order and append newer SDK versions after older versions.

## Package Manifest Updates

Package manifests are source data generated or curated from package repositories
after package releases exist. They are not a place to develop the adaptation.

An external package contribution is ready for `FlutterOH/source` only after the
package repository has completed its own release workflow:

- The package repository has a package-owned `fluoh.yaml`.
- The package release tag records the FlutterOH repository URL, upstream URL,
  package name and path, SDK line, adapted upstream version, release version,
  and upstream commit.
- Release readiness has been reviewed with `fluoh package status`.
- The package repository has completed the release-gate outputs required by its
  current package workflow, such as the canonical report, report check,
  independent review, and `fluoh package check --report <report-path>` when
  those gates apply.
- At least one release tag has been published with `fluoh package release`.

Do not add package code, package release tags, unverified readiness claims, or
unreleased package metadata to this repository.

Expected manifest layout:

```text
manifests/
  camera/
    fluoh.yaml
```

Manifest files use `kind: manifest`. The route name in root `fluoh.yaml` must
match `package.name` in the manifest:

```yaml
schema: 1
kind: manifest

repository:
  git:
    url: https://github.com/FlutterOH/camera.git

upstream:
  git:
    url: https://github.com/flutter/packages.git

package:
  name: camera
  path: packages/camera/camera
  sdks:
    "3.35":
      releases:
        - version: 0.1.0
          upstream:
            version: 0.11.0
            ref: camera-v0.11.0
            commit: "0123456789abcdef0123456789abcdef01234567"
```

Rules:

- Add a root `manifests` entry only when the manifest file exists and validates.
- A package name must appear in only one official manifest.
- Each Source Manifest describes exactly one package under `package`.
- `package.path` is the path in both the FlutterOH package repository and the
  upstream repository, and defaults to `.`.
- Release records are grouped under `package.sdks.<sdk-line>.releases`.
- Release records must include `version`, `upstream.version`, and
  `upstream.commit`; `upstream.ref` is optional.
- Omit release `status` for normal published records. Use `experimental` or
  `broken` only for published records that should not be recommended by default.
- Use `maintenance` and `advisory` for package-level guidance; do not invent
  machine statuses outside the `fluoh` schema.
- Package adaptation code and release tags remain in the package repository.

Use `fluoh source sync` to refresh release records from package repositories
when manifests already exist and point at released package repos. Edit manifest
files directly for manifest metadata, advisory text, and frozen maintenance
notes. `fluoh source sync` imports only released package tags whose SDK line is
covered by root `sdk.versions`; review any tags it skips before committing
generated records. It does not discover new official manifests by itself; the
first pull request for a package must add both the root manifest route entry and
the matching `manifests/<manifest-name>/fluoh.yaml`.

First-time package intake uses a pull request to this repository:

1. Fork or clone `FlutterOH/source`.
2. Choose a stable manifest route name, normally the package name or package
   group name.
3. Add the manifest route entry to root `fluoh.yaml`:

   ```yaml
   manifests:
     - name: camera
   ```

4. Create `manifests/<manifest-name>/fluoh.yaml` with repository, upstream,
   package name and path, SDK line, and records from published package release
   tags whose package-side release gates are complete.
5. Run `fluoh source check .` and `git diff --check`.
6. Open a pull request that lists the manifest route name, package repository,
   published release tag, optional package release verification notes, and
   confirms the local source check was run. Upstream paths, SDK lines, release
   records, and package-side gates should be verifiable from the manifest diff
   and the published package release tag; do not rely on user-provided form
   text alone.

After the manifest is merged, `.github/workflows/sync.yml` can keep release
records current from package repository tags. For manual refreshes after a
manifest exists, run:

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

This validates the dirty source snapshot produced by sync. If the refresh will
be submitted as a manual source-data pull request, release-record verification
requires a committed diff; after committing the generated changes, run
`fluoh source check --base-ref <base> .` locally before opening the pull request.
Routine scheduled sync commits do not have a PR author; they trust package
repository release tags that were produced after package-side release gates.

Maintainers can also trigger the `sync source` workflow from GitHub Actions for
an immediate repository-side refresh before the next scheduled run.

Package maintainers do not need a new `FlutterOH/source` pull request for every
package release after the first manifest pull request is merged. New package
versions should be released in the package repository with
`fluoh package release` after the package-side release gates required by the
current package workflow are complete; the scheduled sync workflow can import
those tags.
Open another source-data pull request only for metadata changes that cannot be
derived from release tags, such as manifest route name changes, repository or
package path corrections, advisory text, maintenance state, or workflow changes.

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
fluoh source check . --schema-only
```

Use `--schema-only` for local YAML/index validation only. It checks the Source
root, SDK metadata, Manifest routes, route/name consistency, release records,
and package index construction without reading Git diffs, fetching SDK tags,
cloning package repositories, verifying release tags, or touching configured
source snapshots and locks. It cannot be combined with diff, release,
work-root, or package verification options.

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

Running `fluoh source sync .` in this maintainer checkout edits repository files
only. It does not refresh a previously registered local source. If local `fluoh`
commands should see the new data, run `fluoh source update <name>` after syncing
or editing.

Before opening a pull request, run:

```sh
fluoh source check .
git status --short --ignored=matching
git diff --check
```

Normal `fluoh source check` is read-only and diff-aware. For PR and merge-gate
checks it validates changed Source files, compares Manifest routes and release
records against the selected base ref, checks added SDK tags, and verifies only
the release records that need package-side validation. Advisory-only,
maintenance-only, and deleted release-record changes are YAML-only checks.
Machine-readable output includes `recommendation`, `changeType` or
`changeTypes`, `affectedManifests`, `checkedManifests`,
`changedReleaseRecords`, `releaseCheckPlan`, `skippedReleaseChecks`,
`sdkChecks`, `changedFiles`, `errors`, and `warnings`.

Use `fluoh source check . --all` only for explicit full audits. Large manual
audits can be narrowed with `--manifest <name>`, `--package <name>`,
`--shard <index>/<total>`, `--concurrency <n>`, and
`--max-release-checks <n>`. Use `--skip-release-checks` when the intent is to
validate Source YAML and changed-route selection without cloning package
repositories.

Package-side validation and release gates belong in the package repository
before `fluoh package release`; this repository validates only released source
metadata through `fluoh source check`.

## GitHub Workflow

`.github/workflows/validate.yml` checks the checked-out source with `fluoh`.
It should remain focused on source checking:

- Install the released `fluoh` from pub.dev, falling back to the default
  `FlutterOH/fluoh` branch if the package is not available.
- Check pull requests through
  `fluoh source check --base-ref <base> --skip-release-checks .` so CI validates
  source metadata and changed SDK tags without cloning package release
  repositories or installing FlutterOH SDKs.
- Check pushes to `main` through
  `fluoh source check --base-ref <before> --skip-release-checks .` so direct
  source-data pushes still validate changed SDK tags without cloning package
  release repositories.
- Check manual `workflow_dispatch` runs through
  `fluoh source check --skip-release-checks .` as a source snapshot check.

Source checking belongs to the `fluoh` CLI.
For first-time manifest intake and manual source-data PRs, authors should still
confirm they ran local `fluoh source check .`. GitHub CI intentionally uses
`--skip-release-checks` to avoid running package repositories on hosted Linux
runners. Its step summary is derived from the command's JSON fields, including
changed files, affected/checked manifests, SDK checks, release-check plans,
skipped release checks, warnings, and errors. Normal package releases after
manifest intake are imported by scheduled sync without a source PR;
package-side release gates are enforced before `fluoh package release`.

`.github/workflows/sync.yml` runs daily and also supports
`workflow_dispatch` for temporary manual runs from GitHub Actions. It checks
whether root `fluoh.yaml` declares manifests. If no manifests exist, the
workflow exits successfully without changing files. Once manifests exist, it runs:

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

When sync changes source data, the workflow commits directly to `main` after
running `fluoh source check --base-ref origin/main --skip-release-checks .` on
the generated commit. Package-side validation and release gates belong to
the package repository before `fluoh package release`, not to this Source CI.
The sync workflow imports published source metadata only.
Use `fluoh source check . --all` only for explicit manual audits, not routine
CI, because official sources can contain many package manifests.

## Pull Requests

Pull requests should describe:

- User-visible source data changes.
- Added or removed SDK versions.
- Added, removed, or changed package manifests.
- Validation confirmations.
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
