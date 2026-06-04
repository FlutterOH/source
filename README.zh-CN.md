<h1 align="center">FlutterOH Source</h1>

<p align="center">
  <a href="https://github.com/FlutterOH/fluoh">fluoh</a> 消费的官方 source 数据。
</p>

<p align="center">
  <a href="https://github.com/FlutterOH/source/actions/workflows/validate.yml"><img src="https://github.com/FlutterOH/source/actions/workflows/validate.yml/badge.svg" alt="validate source"></a>
  <a href="https://github.com/FlutterOH/source/actions/workflows/sync.yml"><img src="https://github.com/FlutterOH/source/actions/workflows/sync.yml/badge.svg" alt="sync source"></a>
  <img src="https://img.shields.io/badge/source%20schema-1-blue" alt="source schema 1">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue" alt="License"></a>
</p>

<p align="center">
  <a href="#快速开始">快速开始</a> ·
  <a href="#source-数据">Source 数据</a> ·
  <a href="#维护">维护</a> ·
  <a href="CONTRIBUTING.zh-CN.md">贡献指南</a> ·
  <a href="README.md">English</a>
</p>

## 快速开始

`fluoh` 默认使用这个官方 source，大多数用户不需要 clone 本仓库。

刷新 source metadata 并查看可用 FlutterOH SDK 版本：

```sh
fluoh source update
fluoh sdk list
```

在 Flutter 项目中选择 FlutterOH SDK line：

```sh
fluoh sdk use 3.35 --pub-get --init-ohos
```

通过已选择的 FlutterOH SDK 运行 Flutter：

```sh
fluohf pub get
fluohf run
fluohf build hap
```

如果目标是适配 App 或 Package，请从
[`fluoh`](https://github.com/FlutterOH/fluoh) CLI 和 AI skill 开始。本仓库只发布
`fluoh` 读取的 source records。

## Source 数据

源契约从 [`fluoh.yaml`](fluoh.yaml) 开始。

| 数据 | 当前状态 | 使用方 |
| --- | --- | --- |
| SDK 仓库 | `https://gitcode.com/CPF-Flutter/flutter_flutter.git` | `fluoh sdk` 命令 |
| SDK 版本 | `3.35.8-ohos-0.0.2`、`3.35.8-ohos-0.0.3`、`3.35.8-ohos-1.0.1` | `fluoh sdk list` 和 `fluoh sdk use` |
| Package manifests | 暂无 | 后续官方 package implementation lookup |

本仓库刻意保持 source-data focused。这里不保存 SDK 二进制、包适配代码、包 release
tags、本地 caches，也不实现 `fluoh` CLI。

## 仓库结构

- [`fluoh.yaml`](fluoh.yaml)：source root manifest、SDK 仓库和官方 SDK 版本。
- `manifests/`：后续官方包适配准备好后，以 `manifests/<manifest-name>/fluoh.yaml`
  形式保存 package implementation manifests。
- `.github/workflows/`：source validation 和定时 sync automation。
- `.github/ISSUE_TEMPLATE/`：SDK release 和 package manifest triage templates。
- [`CONTRIBUTING.zh-CN.md`](CONTRIBUTING.zh-CN.md)：维护流程、source schema 规则、
  package manifest 接入和 CI 期望。

## 维护

使用已发布的 `fluoh`，或在同时开发两个仓库时激活同级源码 checkout：

```sh
cd ../fluoh
dart pub global activate --source path . --overwrite
cd ../source
fluoh --version
```

修改 source 文件后校验当前 checkout：

```sh
fluoh source check . --schema-only
```

只有需要让 consumer commands 使用当前 checkout 时，才添加本地 source。本地 source
是快照，每次 source 变更后都需要重新添加：

```sh
fluoh source add local .
fluoh sdk list
```

当 package manifests 存在后，维护者可以从包仓库刷新已发布 package records：

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

提交 PR 前还需要运行：

```sh
fluoh source check .
git status --short --ignored=matching
git diff --check
```

## 链接

- [`fluoh` CLI 和 AI skill](https://github.com/FlutterOH/fluoh)
- [`fluoh` 命令参考](https://github.com/FlutterOH/fluoh/blob/main/doc/commands.zh-CN.md)
- [`fluoh` source schema](https://github.com/FlutterOH/fluoh/blob/main/doc/schema.zh-CN.md)
- [贡献和维护流程](CONTRIBUTING.zh-CN.md)

## 许可证

MIT
