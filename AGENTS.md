# Repository Guidelines

## Project Scope

`FlutterOH/source` is the official `fluoh` source repository. It contains FlutterOH SDK release metadata today and package implementation manifests when official package adaptations are ready.

Keep the repository small, predictable, and source-data focused. Do not add package adaptation code here.
Maintain and validate this repository with a released `fluoh` or the sibling `../fluoh` checkout.

## Layout

- `fluoh.yaml`: source root manifest, official SDK repository, and configured SDK versions.
- `manifests/`: future package implementation manifests, one manifest name per subdirectory.
- `.github/workflows/`: source validation and scheduled sync automation.
- `.github/ISSUE_TEMPLATE/`: maintainer triage templates.
- `.github/pull_request_template.md`: source-data PR checklist.
- `README.md` / `README.zh-CN.md`: public documentation.
- `CONTRIBUTING.md` / `CONTRIBUTING.zh-CN.md`: maintainer workflow.

## Maintenance Rules

- Use the current `fluoh.yaml` source-root schema.
- Keep repository contents limited to the current source layout, documentation, workflow files, and GitHub templates.
- Add only SDK versions that exist as SDK repository tags and should be visible through `fluoh sdk list`.
- Add a package manifest only when `manifests/<manifest-name>/fluoh.yaml` exists and is ready for official consumption.
- Use `fluoh source sync` to refresh release records from existing package manifests; edit manifest metadata, advisory text, and maintenance notes directly in manifest YAML.
- Keep README, contributing docs, workflow files, issue templates, and PR templates aligned when the maintenance process changes.
- Do not add repo-local validation tools unless `fluoh` no longer owns source validation.
- Do not commit credentials, local caches, `.DS_Store`, generated build output, or machine-specific paths.

## Verification

Use a released or locally built `fluoh`:

```sh
fluoh source check .
```

Only add a local source when consumer commands should use this checkout. Local
sources are snapshots, so rerun this after changing source files:

```sh
fluoh source add local .
fluoh sdk list
```

Before committing, also run:

```sh
git status --short --ignored=matching
git diff --check
```
