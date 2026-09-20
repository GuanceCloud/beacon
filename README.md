# Beacon

GuanceCloud 基于 OpenTelemetry 的应用探针发行体系。

本仓库维护产品方案、组件版本组合、兼容矩阵和交付规范；语言探针独立维护源码与发行。

## 当前状态

- 2026-09-20：完成本地仓库初始化，尚未创建或推送目标 GitHub 仓库。
- Java：`GuanceCloud/beacon-java`，完整源码下游发行模式，产品分支为 `beacon`。
- Java 导入基线：旧仓库 `guance-v2` / `73a8f7edd0415f0e8651d3d1f3f295e6e6d4d1ea`。
- 已核实该提交包含官方 `v2.30.0` 历史；不能据此宣称与当前上游功能完全一致。
- 目前没有 Beacon 正式版本、制品或运行验收结论；Go/Python 等语言尚未初始化。

## 文档入口

- [完整解决方案](docs/solution.md)：产品、架构、验收和实施计划。
- [Java 完整源码维护指南](docs/java-source-distribution.md)：完整方案中的操作示例，未执行部分仍是计划。
- [本地仓库与后续维护](docs/repositories.md)：本次实际状态及下一步。
- [组件登记](components.json)：源代码导入信息，不是正式发行锁文件。
- [发布记录规范](releases/README.md)。

## 仓库职责

| 仓库 | 主线 | 内容 |
| --- | --- | --- |
| `GuanceCloud/beacon` | `main` | 产品规范、语言组合、兼容矩阵、跨组件验收 |
| `GuanceCloud/beacon-java` | `beacon` | 完整 OTel Java Instrumentation 源码、自有增强、测试和 Java 发行 |

不将 Java 全量源码再复制到本仓库，也不通过子模块隐式追踪浮动版本。正式产品组合必须记录组件发布标签、提交与制品摘要。
