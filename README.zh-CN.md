# FlutterOH Source

[![validate source](https://github.com/FlutterOH/source/actions/workflows/validate.yml/badge.svg)](https://github.com/FlutterOH/source/actions/workflows/validate.yml)
[![sync source](https://github.com/FlutterOH/source/actions/workflows/sync-source.yml/badge.svg)](https://github.com/FlutterOH/source/actions/workflows/sync-source.yml)
[![License](https://img.shields.io/badge/license-see%20LICENSE-blue)](LICENSE)

[English](README.md)

`FlutterOH/source` 是 `fluoh` 的官方源仓库。它保存 `fluoh` 消费的源数据：
当前用于 FlutterOH SDK 发现，后续在官方包适配准备好后保存包实现
manifest。

本仓库会保持小而稳定。源契约从 [`fluoh.yaml`](fluoh.yaml) 开始；使用者
应通过 `fluoh source` 命令读取，不应依赖仓库内部临时路径。

## 源数据

| 数据面 | 状态 | 位置 |
| --- | --- | --- |
| FlutterOH SDK 版本 | 已配置 | [`fluoh.yaml`](fluoh.yaml) 的 `sdk` |
| 包实现 manifest | 为空 | [`manifests/`](manifests/) |
| 维护流程 | 已文档化 | [`CONTRIBUTING.zh-CN.md`](CONTRIBUTING.zh-CN.md) |

当前 SDK 版本：

- `3.35.8-ohos-0.0.3`
- `3.35.8-ohos-0.0.2`

目前没有包含包实现 manifest。只包含 SDK 数据、不包含包数据的源是有效状态。

## 配合 `fluoh` 使用

`fluoh` 默认配置会使用这个官方源：

```sh
fluoh source update
fluoh sdk list
```

在 Flutter 项目目录中选择 SDK 版本或版本线：

```sh
fluoh sdk use 3.35
```

校验本仓库变更时，不需要改变本地 `fluoh` 配置：

```sh
fluoh source validate
```

只有需要让 consumer commands 使用当前 checkout 或私有镜像时，才显式添加本地源。
该命令会保存一个已校验快照；源文件变更后需要重新运行：

```sh
fluoh source add local .
fluoh sdk list
```

## 仓库结构

```text
fluoh.yaml                 # Source root manifest 和 SDK 版本列表
manifests/                 # 后续包实现 manifest
.github/workflows/         # 源校验和定时 sync 自动化
.github/ISSUE_TEMPLATE/    # 源数据分诊模板
README*.md                 # 公开使用说明
CONTRIBUTING*.md           # 维护流程
AGENTS.md                  # 本地 agent 和维护者说明
```

仓库内容应限制在这些源文件、文档、workflow 文件和 GitHub 模板内。

## 维护流程

使用已安装的 `fluoh`，或同级源码仓库中的 `fluoh`，维护本仓库：

```sh
cd ../fluoh
dart pub global activate --source path . --overwrite
cd ../source
```

新的 source scaffold 由 `fluoh source init <path>` 创建。本官方仓库已经初始化，
日常维护应直接编辑源数据，或用 `fluoh source sync` 刷新包 release records。

SDK release 在根 [`fluoh.yaml`](fluoh.yaml) 中维护。只添加 SDK 仓库中真实存在、
且应该通过 `fluoh sdk list` 对外可见的完整 SDK tag。

包数据放在 `manifests/<route>/fluoh.yaml`。只有对应 manifest 已存在、并准备好
作为官方数据消费时，才在根 `fluoh.yaml` 中加入 `manifests` route。包适配代码、
包 release tag 和包仓库自己的 `fluoh.yaml` 都应留在对应包仓库，不放在这里。

## 贡献适配包

包加入本仓库之前，FlutterOH 包仓库必须已经包含包仓库自己的 `fluoh.yaml`，并且
至少有一个通过 `fluoh pub release` 创建的 release tag。`FlutterOH/source` 只记录
`fluoh` 应该提供给用户的已发布 source metadata。

首次接入流程：

```sh
git clone https://github.com/FlutterOH/source.git
cd source
```

1. 在根 `fluoh.yaml` 添加 `manifests` route。
2. 创建对应的 `manifests/<route>/fluoh.yaml`。
3. 记录 package repository、upstream repository、package path、SDK line 和已发布
   的 package release records。
4. 运行校验。
5. 提交 pull request。

manifest route 名称通常使用包名或包组名，例如 `camera` 或 `flutter_packages`。
根 `fluoh.yaml` 中的 route 必须在同一个 PR 中对应一个已经存在的
`manifests/<route>/fluoh.yaml` 文件。

校验命令：

```sh
fluoh source validate
git status --short --ignored=matching
git diff --check
```

`fluoh source sync .` 只在 route manifest 已经存在后使用。它从包仓库 tag 刷新
release records，不能替代首次 manifest PR。

首次 route PR 合并后，包维护者不需要每发布一个包版本都向 `FlutterOH/source` 提 PR。
新版本只需要在包仓库发布新的 release tag；定时 sync 会继续更新 release records。
如果需要在下一次定时任务前导入 release，维护者也可以在 GitHub Actions 中手动运行
`sync source` workflow。只有 route metadata、package path、advisory 文案、
maintenance 状态或 source 流程规则需要变化时，才需要再次向 `FlutterOH/source` 提 PR。

源数据规则、包 manifest 流程、GitHub 模板和 CI 期望请见
[CONTRIBUTING.zh-CN.md](CONTRIBUTING.zh-CN.md)。

## License

See [LICENSE](LICENSE).
