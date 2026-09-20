# 语言项目

本页统一维护语言项目入口。各语言的实现方式、功能范围和发行节奏可以不同，不能从其他语言或上游项目推断某个 Beacon 版本的支持能力。

## Java

采用完整 OpenTelemetry Java Instrumentation 源码的下游维护方式，保留上游历史，并在对应模块开发自有增强。当前处于工程准备阶段，尚无 Beacon Java 正式发行。

以下是计划发布到 GitHub 的目标地址，使用 `beacon` 开发分支。对应仓库、分支和文件推送并开放访问后才可使用；目前不作为已上线入口或下载地址。

| 入口 | 目标地址 |
| --- | --- |
| 源码仓库 | [GuanceCloud/beacon-java](https://github.com/GuanceCloud/beacon-java) |
| 开发说明 | [Beacon Java 开发入口](https://github.com/GuanceCloud/beacon-java/blob/beacon/beacon/README.md) |
| 源码来源 | [上游基线记录](https://github.com/GuanceCloud/beacon-java/blob/beacon/beacon/upstream.lock.json) |
| 上游维护 | [OTel 同步流程](https://github.com/GuanceCloud/beacon-java/blob/beacon/beacon/UPSTREAM.md) |
| 发行开发 | [发行流程与准备项](https://github.com/GuanceCloud/beacon-java/blob/beacon/beacon/RELEASING.md) |

上述链接指向开发文档，会随开发分支变化，不代表某个正式版本的安装指南或支持承诺。首次发行后，本页再补充实际发布标签对应的使用文档与 Release 链接。

## Go

计划在 `GuanceCloud/beacon-go` 维护。工程尚待建立，先盘点现有实现并确定维护方式；暂不提供仓库或安装链接。

## Python

计划在 `GuanceCloud/beacon-python` 维护。工程尚待建立，先盘点现有实现并确定维护方式；暂不提供仓库或安装链接。

## 支持范围的维护方式

正式发行后，各语言的版本文档负责列出已验证的遥测与增强能力、运行环境、接收端兼容范围、已知限制及升级回退方法。

本仓库需要跨语言对比时，只汇总带有明确版本和证据链接的能力状态，不复制完整运行矩阵。涉及 DataKit 的接入，以实际验证的版本和协议为准。源码存在、构建成功或上游支持均不能单独作为 Beacon 已支持的依据。
