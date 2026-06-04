## Summary

-

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

## Intake Info (if applicable)

- Manifest route name:
- Package repository:
- Published release tag:

## Source Check

- [ ] Source data changes pass `fluoh source check .`.
- [ ] Whitespace and patch formatting pass `git diff --check`.

## SDK Release (if applicable)

- [ ] SDK version changes are intentional and backed by SDK repository tags.
- [ ] Added SDK versions are intended to be visible through `fluoh sdk list`.

## Package Manifest (if applicable)

- [ ] Package adaptation code, package release tags, and package-owned metadata remain outside this repository.
- [ ] Package release-record changes reference published FlutterOH package release tags, not in-progress package repository state.
- [ ] Referenced package release tags were created by `fluoh package release` and contain package-owned `fluoh.yaml` metadata.
- [ ] Existing manifest release-record refreshes were generated with `fluoh source sync .` when applicable.
- [ ] First-time package manifests add both the root `fluoh.yaml` manifest route entry and `manifests/<manifest-name>/fluoh.yaml`.
- [ ] First-time package manifests provide enough intake info for reviewers or automation to fetch and verify the published package release tag.
- [ ] Unfinished adaptations are not being added to the official source.

## Maintenance Surface (if applicable)

- [ ] README and contributing docs are updated when public behavior changes.
- [ ] Workflow, issue templates, and this PR template are updated when maintainer workflow changes.

## Validation

Run in this source repository:

```sh
fluoh source check .
git diff --check
```

For existing package manifest release-record refreshes, also run in this source repository:

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

Release-record verification runs from the committed PR diff in CI with
`fluoh source check --base-ref <base> .`.

For first-time package manifest PRs, provide the package release locator:

```text
manifest route name: <manifest-name>
package repository: <url>
published release tag: <tag>
```

Validation result:

-
