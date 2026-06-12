## Summary

Describe what source data or maintainer workflow changes, and why.

## Title Format

Use one of:

- `feat(sdk): add <sdk-version>`
- `feat(source): add <package-name> manifest`
- `fix(source): correct <package-name> manifest metadata`
- `chore(source): sync package release records`
- `docs: update <area>`

## Change Type

- [ ] SDK release metadata
- [ ] First-time package manifest
- [ ] Existing package manifest metadata
- [ ] Package release-record sync refresh
- [ ] Documentation, workflow, issue template, or PR template

## Source Data Scope

Fill only the lines that apply.

- Manifest route name:
- Package repository:
- Published release tag:
- Sync notes:
- SDK version:
- Affected workflow/template:

## Validation Confirmation

- [ ] I ran `git diff --check`.
- [ ] For YAML/index-only source edits, I ran `fluoh source check . --schema-only`.
- [ ] For source data changes that add, remove, reorder, or modify SDK versions or package release records, I ran `fluoh source check .`.
- [ ] For manual release-record refreshes, I reviewed the `fluoh source sync .` output and ran `fluoh source check --skip-release-checks .`.
- [ ] For workflow changes, I reviewed triggers and permissions.

## SDK Release (if applicable)

- [ ] SDK version changes are intentional and backed by SDK repository tags.
- [ ] Added SDK versions, if any, are intended to be visible through `fluoh sdk list`.

## Source Boundaries (for source data changes)

- [ ] Package adaptation code, package release tags, and package-owned metadata remain outside this repository.
- [ ] Package release-record changes reference published `fluoh package release` tags, not in-progress package repository state.
- [ ] First-time package manifests add both the root `fluoh.yaml` manifest route entry and `manifests/<manifest-name>/fluoh.yaml`.
- [ ] Unfinished adaptations are not being added to the official source.

## Maintenance Surface (if applicable)

- [ ] README and contributing docs are updated when public behavior changes.
- [ ] Workflow, issue templates, and this PR template are updated when maintainer workflow changes.

## Local Commands

Common commands:

```sh
fluoh source check . --schema-only
fluoh source check .
git diff --check
```

For existing package manifest release-record refreshes, also run in this source repository:

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

GitHub Source CI skips package release checks so it does not clone package
repositories or install FlutterOH SDKs on hosted Linux runners. For first-time
manifest intake and manual source-data PRs, authors should still confirm they
ran local `fluoh source check .`. After a manifest is merged, normal package
release records are imported by scheduled sync without a source PR; package
validation and release evidence must be completed in the package repository
before publishing release tags.

For first-time package manifest PRs, provide the package release locator:

```text
manifest route name: <manifest-name>
package repository: <url>
published release tag: <tag>
```
