# 贡献和维护

[English](CONTRIBUTING.md)

本文说明官方 `FlutterOH/source` 源仓库的维护方式。这个仓库是 `fluoh` 消费的源
数据，不是包适配仓库，也不是 `fluoh` CLI 的实现仓库。

## 范围

本仓库负责：

- 在根 `fluoh.yaml` 中维护官方 FlutterOH SDK release metadata。
- 当包适配准备好并通过官方确认后，在 `manifests/` 下维护包实现 manifest。
- 保持 README、维护说明、issue 模板、PR 模板和 workflow 与当前源契约一致。

本仓库不负责：

- 实现 `fluoh`。
- 为用户安装或缓存 SDK。
- 重写用户项目依赖。
- 保存包适配代码、包 release tag 或包仓库自己的 metadata。

## 源契约

根 [`fluoh.yaml`](fluoh.yaml) 必须保持当前 source-root schema：

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

规则：

- `schema` 为 `1`，`kind` 为 `source`。
- `name` 是 Source token。官方 Source 使用 `flutteroh`。
- `repository.git.url` 是可选的 Source 自描述信息。
- `sdk.git.url` 是 FlutterOH SDK 仓库。
- `sdk.versions` 按语义化版本升序保存完整、可安装的 SDK tag。
- 未包含包 manifest 时不写 `manifests`。写入时，每个 manifest route 条目都映射到
  `manifests/<name>/fluoh.yaml`。

仓库内容应限制在当前 source layout、文档、workflow 文件和 GitHub 模板内。

## SDK Release 更新

只有同时满足以下条件时，才添加 SDK 版本：

- tag 存在于 `sdk.git.url`。
- 该版本应该通过 `fluoh sdk list` 对用户可见。
- 版本是完整 SDK tag，例如 `3.35.8-ohos-1.0.1`。
- 文档和 issue/PR 模板不需要流程变更，或已在同一个 PR 中同步更新。

版本列表应保持稳定、可重复，并按语义化版本升序排列；新增 SDK 版本追加在旧版本之后。

## Package Manifest 更新

Package manifest 是从包仓库 release 结果生成或整理出来的源数据。这里不是开发适配代码
的地方。

外部适配包只有先完成自己的包仓库发布流程后，才适合加入 `FlutterOH/source`：

- 包仓库已经有包仓库自己的 `fluoh.yaml`。
- 包 release tag 记录了 FlutterOH 仓库 URL、上游 URL、package name 和 path、SDK line、
  已适配的 upstream version、release version 和 upstream commit。
- 发布前已经通过 `fluoh package status` 查看发布就绪状态，并通过
  `fluoh package check`。
- 至少已经通过 `fluoh package release` 发布一个 release tag。

不要把包代码、包 release tag、未经发布流程确认的发布就绪声明或未发布的 package
metadata 加入本仓库。

预期 manifest 布局：

```text
manifests/
  camera/
    fluoh.yaml
```

Manifest 文件使用 `kind: manifest`。根 `fluoh.yaml` 中的 route name 必须匹配
manifest 里的 `package.name`：

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

规则：

- 只有对应 manifest 文件存在并通过校验时，才在根 `fluoh.yaml` 加入
  `manifests` entry。
- 同一个 package name 只能出现在一个官方 manifest 中。
- 每个 Source Manifest 只通过 `package` 描述一个 package。
- `package.path` 同时表示 FlutterOH 包仓库和上游仓库中的路径，默认是 `.`。
- Release records 按 `package.sdks.<sdk-line>.releases` 分组。
- Release record 必须包含 `version`、`upstream.version` 和 `upstream.commit`；
  `upstream.ref` 可选。
- 正常发布记录不需要写 release `status`。只有不应默认推荐的已发布记录才写
  `experimental` 或 `broken`。
- 使用 `maintenance` 和 `advisory` 表达包级维护说明；不要发明 `fluoh` schema
  之外的机器状态。
- 包适配代码和 release tag 留在包仓库。

当 manifest 已存在、并指向已有 release tag 的包仓库时，使用 `fluoh source sync`
刷新 release records。Manifest metadata、advisory 文案和 frozen maintenance 说明可直接
编辑 manifest 文件。
`fluoh source sync` 不会自动发现新的官方 manifest；首次接入一个包时，PR 必须同时加入
根 manifest route 条目和对应的 `manifests/<manifest-name>/fluoh.yaml`。

首次接入适配包时，通过本仓库 PR 完成：

1. Fork 或 clone `FlutterOH/source`。
2. 选择稳定的 manifest route name，通常使用包名或包组名。
3. 在根 `fluoh.yaml` 添加 manifest route 条目：

   ```yaml
   manifests:
     - name: camera
   ```

4. 创建 `manifests/<manifest-name>/fluoh.yaml`，写入 package repository、upstream、
   package name 和 path、SDK line，以及已发布 package release tag 对应的 records。
5. 运行 `fluoh source check .` 和 `git diff --check`。
6. 提交 PR，说明 manifest route name、适配仓库、已发布 release tag，并确认已在本地运行
   source check。上游路径、SDK line 和 release records 应能从 manifest diff 和已发布
   package release tag 中复核。

manifest 合并后，`.github/workflows/sync.yml` 会根据包仓库 tags 继续更新
release records。manifest 已存在后如需手动刷新，运行：

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

这一步校验 sync 生成的 dirty source snapshot。如果这次刷新要作为人工
source-data PR 提交，release-record verification 需要基于已提交 diff；提交 sync
生成的改动后，打开 PR 前应在本地运行 `fluoh source check --base-ref <base> .`。
常规定时 sync commit 没有 PR 作者；它信任已经在包仓库完成验证后发布的 package
release tags。

维护者也可以在 GitHub Actions 中手动触发 `sync source` workflow，用于下一次定时任务
之前的临时仓库侧刷新。

首次 manifest PR 合并后，包维护者不需要每发布一个包版本都向 `FlutterOH/source` 提 PR。
新包版本应在包仓库通过 `fluoh package status` 和 `fluoh package check` 后，再用
`fluoh package release` 发布；定时 sync workflow 可以导入这些 tags。只有 release tag
无法表达的 metadata 变化才需要再次提交 source-data PR，例如 manifest route name 调整、
repository 或 package path 修正、advisory 文案、maintenance 状态或流程变化。

## 本地维护环境

使用已安装的 `fluoh`，或在同时开发两个仓库时激活同级源码 checkout：

```sh
cd ../fluoh
dart pub global activate --source path . --overwrite
cd ../source
fluoh --version
```

`fluoh source init <path>` 用于创建新的 source scaffold。本官方仓库已经初始化；
日常维护直接编辑源文件。

日常本地校验：

```sh
fluoh source check . --schema-only
```

只有需要让 consumer commands 使用当前 checkout 时，才添加本地源。本地源是快照；
源文件变更后需要重新添加：

```sh
fluoh source add local .
fluoh sdk list
```

提交 PR 前运行：

```sh
fluoh source check .
git status --short --ignored=matching
git diff --check
```

包发布就绪检查、包测试和应用兼容性检查属于包仓库，应在 `fluoh package release` 前
完成；本仓库通过 `fluoh source check` 校验已发布的 source metadata。

## GitHub Workflow

`.github/workflows/validate.yml` 使用 `fluoh` 检查 checkout。它应聚焦源检查：

- 优先从 pub.dev 安装已发布的 `fluoh`；如果 package 不可用，再回退到
  `FlutterOH/fluoh` 默认分支。
- PR 通过 `fluoh source check --base-ref <base> --skip-release-checks .`
  检查，让 CI 校验 source metadata 和变更的 SDK tags，但不 clone package
  release 仓库，也不安装 FlutterOH SDK。
- push 到 `main` 时通过
  `fluoh source check --base-ref <before> --skip-release-checks .` 检查，直接
  push 的源数据变更仍会校验变更的 SDK tags，但不会 clone package release 仓库。
- 手动 `workflow_dispatch` 通过 `fluoh source check --skip-release-checks .`
  作为 source snapshot check。

Source checking 由 `fluoh` CLI 负责。
首次 manifest 接入和人工 source-data PR 的作者仍应确认已在本地运行
`fluoh source check .`。GitHub CI 有意使用 `--skip-release-checks`，避免在托管
Linux runner 上执行 package 仓库。首次 manifest 接入后的常规 package release 由定时
sync 导入，不需要 source PR；包侧验证由 `fluoh package release` 前的包仓库流程负责。

`.github/workflows/sync.yml` 每天运行一次，并通过 `workflow_dispatch` 支持在
GitHub Actions 中临时手动触发。它会先检查根 `fluoh.yaml` 是否声明 manifests。
没有 manifest 时，workflow 成功退出且不改文件；有 manifest 后执行：

```sh
fluoh source sync .
fluoh source check --skip-release-checks .
git diff --check
```

如果 sync 产生源数据变更，workflow 会先在生成的 commit 上运行
`fluoh source check --base-ref origin/main --skip-release-checks .`，再直接
提交到 `main`。Package release verification、OHOS build、OHOS run 和设备验证
属于 package 仓库，应在 `fluoh package release` 前完成，不属于 Source CI。sync
workflow 只导入已发布的 source metadata。
`fluoh source check --all .` 只用于明确的人工全量审计，不作为常规 CI 路径，
因为官方 source 可能包含大量 package manifests。

## Pull Requests

PR 应说明：

- 用户可见的源数据变化。
- 新增或删除的 SDK 版本。
- 新增、删除或修改的包 manifest。
- 校验确认项。
- 对维护流程、issue 模板、PR 模板或 CI 的影响。

提交应保持聚焦。建议使用 Conventional Commits：

```text
docs: refresh official source maintenance guide
ci: validate source with fluoh
feat(sdk): add 3.35.8-ohos-0.0.4
feat(source): add camera manifest
```

不要提交 credentials、本地 cache、`.DS_Store`、生成的 build output 或机器相关路径。
