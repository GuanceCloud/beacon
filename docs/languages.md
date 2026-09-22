# 语言项目

本页统一维护语言项目入口。各语言的实现方式、功能范围和发行节奏可以不同，不能从其他语言或上游项目推断某个 Beacon 版本的支持能力。

## Java

采用完整 OpenTelemetry Java Instrumentation 源码的下游维护方式，保留上游历史，并在对应模块开发自有增强。当前处于工程准备阶段，尚无 Beacon Java 正式发行。

以下开发入口已推送到 GitHub，使用 `main` 开发分支。开发文档会随分支更新，不作为正式版本的安装或支持承诺。

| 入口 | 开发地址 |
| --- | --- |
| 源码仓库 | [GuanceCloud/beacon-java](https://github.com/GuanceCloud/beacon-java) |
| 开发说明 | [Beacon Java 开发入口](https://github.com/GuanceCloud/beacon-java/blob/main/beacon/README.md) |
| 源码来源 | [上游基线记录](https://github.com/GuanceCloud/beacon-java/blob/main/beacon/upstream.lock.json) |
| 上游维护 | [OTel 同步流程](https://github.com/GuanceCloud/beacon-java/blob/main/beacon/UPSTREAM.md) |
| 发行开发 | [发行流程与准备项](https://github.com/GuanceCloud/beacon-java/blob/main/beacon/RELEASING.md) |

上述链接指向开发文档，会随开发分支变化，不代表某个正式版本的安装指南或支持承诺。首次发行后，本页再补充实际发布标签对应的使用文档与 Release 链接。

## Go

计划在 `GuanceCloud/beacon-go` 维护。工程尚待建立，先盘点现有实现并确定维护方式；暂不提供仓库或安装链接。

## Python

采用完整 OpenTelemetry Python Contrib 源码的独立下游维护方式，不使用 GitHub Fork。开发工程已从旧 `gtrace` 分支保留自有增强与提交历史，并合入官方 `v0.65b0` 发布基线；配套 Python Core 开发依赖固定到 `v1.44.0`。目前仅完成自有发行包与 Profiling 测试，尚未完成完整上游矩阵、DataKit 接收端和正式制品验收，因此没有 Beacon Python 正式发行或安装入口。

以下开发入口已推送到 GitHub，使用 `main` 开发分支。开发文档会随分支更新，不作为正式版本的安装或支持承诺。

| 入口 | 开发地址 |
| --- | --- |
| 源码仓库 | [GuanceCloud/beacon-python](https://github.com/GuanceCloud/beacon-python) |
| 开发说明 | [Beacon Python 开发入口](https://github.com/GuanceCloud/beacon-python/blob/main/beacon/README.md) |
| 源码来源 | [上游基线记录](https://github.com/GuanceCloud/beacon-python/blob/main/beacon/upstream.lock.json) |
| 上游维护 | [OTel 同步流程](https://github.com/GuanceCloud/beacon-python/blob/main/beacon/UPSTREAM.md) |
| 发行准备 | [发行准备项](https://github.com/GuanceCloud/beacon-python/blob/main/beacon/RELEASING.md) |

现有自有实现的历史来源可在[旧 `gtrace` 分支](https://github.com/GuanceCloud/opentelemetry-python-contrib/tree/gtrace)追溯；已有的 Guance PyPI 包不等于 Beacon Python 发行。首次发行后，本页再补充固定版本的安装与 Release 链接。

## 支持范围的维护方式

正式发行后，各语言的版本文档负责列出已验证的遥测与增强能力、运行环境、接收端兼容范围、已知限制及升级回退方法。

本仓库需要跨语言对比时，只汇总带有明确版本和证据链接的能力状态，不复制完整运行矩阵。涉及 DataKit 的接入，以实际验证的版本和协议为准。源码存在、构建成功或上游支持均不能单独作为 Beacon 已支持的依据。
