# FlutterOH Source

[![validate source](https://github.com/FlutterOH/source/actions/workflows/validate.yml/badge.svg)](https://github.com/FlutterOH/source/actions/workflows/validate.yml)
[![sync source](https://github.com/FlutterOH/source/actions/workflows/sync.yml/badge.svg)](https://github.com/FlutterOH/source/actions/workflows/sync.yml)
[![License](https://img.shields.io/badge/license-see%20LICENSE-blue)](LICENSE)

[中文](README.zh-CN.md)

Official source data for `fluoh`.

This repository publishes FlutterOH SDK metadata today and will publish official
package implementation manifests when package adaptations are ready. It is not a
package adaptation repository and does not contain SDK binaries, package code, or
the `fluoh` CLI implementation.

## Use

`fluoh` uses this official source by default:

```sh
fluoh source update
fluoh sdk list
```

Select an SDK from a Flutter project:

```sh
fluoh sdk use 3.35 --pub-get --init-ohos
```

Validate this checkout:

```sh
fluoh source validate
```

## Current Data

- SDK repository: `https://gitcode.com/openharmony-tpc/flutter_flutter.git`
- SDK versions:
  - `3.35.8-ohos-0.0.3`
  - `3.35.8-ohos-0.0.2`
- Package manifests: none yet

The source contract starts at [`fluoh.yaml`](fluoh.yaml). Consumers should use
`fluoh source` commands instead of depending on internal repository paths.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for SDK update rules, package manifest
intake, local maintenance setup, validation, and CI expectations.

## License

See [LICENSE](LICENSE).
