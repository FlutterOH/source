# FlutterOH Source

[![validate source](https://github.com/FlutterOH/source/actions/workflows/validate.yml/badge.svg)](https://github.com/FlutterOH/source/actions/workflows/validate.yml)
[![sync source](https://github.com/FlutterOH/source/actions/workflows/sync.yml/badge.svg)](https://github.com/FlutterOH/source/actions/workflows/sync.yml)
[![License](https://img.shields.io/badge/license-see%20LICENSE-blue)](LICENSE)

[English](README.md)

`fluoh` 的官方源数据仓库。

本仓库当前发布 FlutterOH SDK metadata；后续在包适配准备好后发布官方包实现
manifest。这里不是包适配仓库，也不保存 SDK 二进制、包代码或 `fluoh` CLI 实现。

## 使用

`fluoh` 默认使用这个官方源：

```sh
fluoh source update
fluoh sdk list
```

在 Flutter 项目中选择 SDK：

```sh
fluoh sdk use 3.35 --pub-get --init-ohos
```

校验当前 checkout：

```sh
fluoh source validate
```

## 当前数据

- SDK 仓库：`https://gitcode.com/openharmony-tpc/flutter_flutter.git`
- SDK 版本：
  - `3.35.8-ohos-0.0.3`
  - `3.35.8-ohos-0.0.2`
- 包 manifest：暂无

源契约从 [`fluoh.yaml`](fluoh.yaml) 开始。使用者应通过 `fluoh source` 命令读取，
不应依赖仓库内部路径。

## 贡献

SDK 更新规则、包 manifest 接入、本地维护环境和 CI 期望请见
[CONTRIBUTING.zh-CN.md](CONTRIBUTING.zh-CN.md)。

## License

See [LICENSE](LICENSE).
