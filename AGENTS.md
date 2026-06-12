# Repository Guidelines

## Project Scope

`FlutterOH/source` is the official `fluoh` source repository. It contains FlutterOH SDK release metadata today and package implementation manifests when official package adaptations are ready.

Keep the repository small, predictable, and source-data focused. Do not add package adaptation code here.
Maintain and validate this repository with a released `fluoh` or the sibling `../fluoh` checkout.

## Layout

- `fluoh.yaml`: source root manifest, official SDK repository, and configured SDK versions.
- `manifests/`: future package implementation manifests, one manifest route per subdirectory.
- `.github/workflows/`: source validation and scheduled sync automation.
- `.github/ISSUE_TEMPLATE/`: maintainer triage templates.
- `.github/pull_request_template.md`: source-data PR checklist.
- `README.md` / `README.zh-CN.md`: public documentation.
- `CONTRIBUTING.md` / `CONTRIBUTING.zh-CN.md`: maintainer workflow.

## Maintenance Rules

- Use the current `fluoh.yaml` source-root schema.
- Keep repository contents limited to the current source layout, documentation, workflow files, and GitHub templates.
- Add only SDK versions that exist as SDK repository tags and should be visible through `fluoh sdk list`.
- Keep `sdk.versions` as complete installable SDK tags in ascending semantic version order.
- Use `fluoh source init <path>` only for new local source scaffolds; this official checkout is already initialized.
- Add a package manifest only after the package repository has completed its own release workflow: package-owned `fluoh.yaml`, `fluoh package status`, `fluoh package check`, and at least one published `fluoh package release` tag.
- For first-time package intake, add both the root `manifests` route entry and `manifests/<manifest-name>/fluoh.yaml`; the route name must match the manifest package name.
- Use `fluoh source sync` to refresh release records from existing package manifests; edit manifest metadata, advisory text, and maintenance notes directly in manifest YAML.
- Do not hand-edit generated package release records when `fluoh source sync` is the source of truth.
- Keep README, contributing docs, workflow files, issue templates, and PR templates aligned when the maintenance process changes.
- Do not add repo-local validation tools unless `fluoh` no longer owns source validation.
- Do not commit credentials, local caches, `.DS_Store`, generated build output, or machine-specific paths.

## Verification

Use a released or locally built `fluoh`:

```sh
fluoh source check . --schema-only
```

Add `--json` for automation. `--schema-only` is only for local YAML/index
validation; it does not read Git diffs, fetch SDK tags, clone package
repositories, verify release tags, or touch configured source snapshots and
locks. Do not combine it with diff, release, work-root, or package verification
options.

Run the full Source check before opening a pull request or when release records
must be verified:

```sh
fluoh source check .
```

Normal `fluoh source check` is read-only and diff-aware. For PR and merge-gate
work it reports `recommendation`, `changeType`/`changeTypes`,
`affectedManifests`, `checkedManifests`, `changedReleaseRecords`,
`releaseCheckPlan`, `skippedReleaseChecks`, `sdkChecks`, `errors`, and
`warnings`. Use `--all` only for explicit full audits, and use `--manifest`,
`--package`, `--shard`, `--concurrency`, or `--max-release-checks` to narrow
large manual audits when needed.

For manual refreshes after package manifests exist, validate the dirty sync
snapshot before committing:

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

GitHub workflows intentionally use `--base-ref` and `--skip-release-checks` for
PR, push, and scheduled sync checks. Keep release verification in local
maintainer checks or explicit manual audits; do not make routine CI clone and
verify every package release repository.

Package-side release gates and implementation evidence belong in the package
repository before `fluoh package release`, not in this Source repository.

Only add a local source when source-consuming commands should use this checkout.
Local sources are snapshots; add it once:

```sh
fluoh source add local .
fluoh sdk list
```

After changing source files, refresh the configured snapshot:

```sh
fluoh source update local
fluoh sdk list
```

Before committing, also run:

```sh
git status --short --ignored=matching
git diff --check
```
