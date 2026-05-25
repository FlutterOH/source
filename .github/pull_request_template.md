## Summary

-

## Source Data (if applicable)

- [ ] Source data changes pass `fluoh source validate`.
- [ ] SDK version changes are intentional and backed by SDK repository tags.
- [ ] Package manifest changes reference published package release metadata.
- [ ] New package manifest routes include package `status` and `check` results.
- [ ] Package adaptation code, package release tags, and package-owned metadata remain outside this repository.

## Maintenance Surface (if applicable)

- [ ] README and contributing docs are updated when public behavior changes.
- [ ] Workflow, issue templates, and this PR template are updated when maintainer workflow changes.

## Validation

For source data changes:

```sh
fluoh source validate
```

For new package manifest routes, include package repository checks:

```sh
fluoh package status
fluoh package check
```

Validation result:

-
