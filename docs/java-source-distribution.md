# Beacon Java：完整源码维护、上游同步与自有发行

产品、架构、数据链路、验收与实施计划的统一入口见 [Beacon 完整解决方案](solution.md)。本文作为 Java 源码操作专题保留。

本指南替代此前“官方 Agent 制品 + 扩展”的主方案。需求基线：GuanceCloud 需要拥有完整 OpenTelemetry Java Instrumentation 源码，未来直接增强现有原生插桩和 Agent 实现。

本文是拟实施流程，没有创建、重命名或推送任何远程仓库，也没有运行完整源码构建。

## 1. 技术定位

`GuanceCloud/beacon-java` 应是 OTel Java Instrumentation 的源码级下游发行版。独立的产品名、版本号和发行节奏，与保留上游完整源码及 Git 历史并不矛盾。

```text
open-telemetry/opentelemetry-java-instrumentation
                   │ 上游完整源码、发布标签
                   ▼
         GuanceCloud/beacon-java
         ├── 原有 OTel 模块
         ├── 对原生插桩的增强和回归测试
         ├── Beacon 专有模块与配置
         └── 构建、兼容验证、发行
                   ▼
        beacon-java-agent-<版本>.jar
```

GitHub 页面是否显示 Fork 是托管关系；同步是否可靠取决于是否保留共同历史、明确上游基线并控制自有差异。

范围需精确：本指南维护的是 `opentelemetry-java-instrumentation` 完整源码。OTel Java SDK 自身位于 `open-telemetry/opentelemetry-java`，通常仍作为依赖。如果未来还要修改 SDK 内部实现，应另建受控 SDK 源码维护线并记录其制品，不能假定 instrumentation 仓库已经包含所有 OTel 项目源码。

## 2. 如何获得 beacon-java 仓库

### 推荐：沿用现有 Fork 的历史与资产

优先评估将 `GuanceCloud/opentelemetry-java-instrumentation` 重命名为 `GuanceCloud/beacon-java`。这条路径保留现有源码、分支、提交和仓库资产，适合该仓库今后主要承载 Beacon 的情况。

重命名前盘点：外部下载地址、Maven/制品发布坐标、工作流的硬编码仓库名、复用 Actions 引用、机器人配置、Webhook、文档及客户脚本。GitHub 对普通仓库链接和 Git 操作有重定向，但不会重定向对仓库所托管 Action 的引用；不应依赖所有入口都自动兼容。

若必须保留旧仓库名作为独立维护入口，则新建空的 `beacon-java`，把选定现有分支及其可达完整提交历史导入，并保留必要的历史分支和标签。不要用 ZIP 下载后重新提交的方式初始化，这会丢失共同历史；也不要盲目向有内容的目标执行镜像推送。

新建导入方式不自动迁移旧仓库的 Issues、Releases 和设置，需要单独规划。现有下游标签可能与官方同名但提交不同，导入前逐一分类。

## 3. 分支和引用规则

建议以新建的 `beacon` 分支作为产品主线，初始来源是审计后选定的现有生产维护提交，例如现有 `guance-v2` 中的已验证提交。不要直接把当前 `main` 当成产品主线；此前已发现现有发布工作流面向 `guance-v2`。

| 引用 | 用途 |
| --- | --- |
| `beacon` | 产品集成主线，可在迁移完成后设置为默认分支 |
| `feature/*` | 原生插桩增强和新功能 |
| `sync/otel-*` | 一次上游版本同步，短期分支 |
| `release/1.x` | 确实承诺维护旧产品线时创建 |
| `beacon-v1.0.0` 等 | Beacon 自有发行标签 |
| `refs/upstream-tags/*` | 本地或 CI 获取的官方标签独立命名空间 |

选择 `beacon-v*` 是为了与仓库已有的上游风格 `v2.x.y` 标签区分。现有标签不改写、不复用。独立导入且确认没有历史冲突时可以另定规则，但同一仓库应保持一致。

`upstream` remote 指向官方，`origin` 指向 GuanceCloud。官方标签的真实提交 SHA 写入版本基线文件；无必要时不把全部官方标签推送成产品发布标签。

## 4. 源码怎么组织

保留上游主要目录和构建组织，以下仅为职责示意，具体模块名以采用版本为准：

```text
beacon-java/
├── instrumentation/          # 原生插桩及其增强
├── javaagent/                # Agent 构建
├── javaagent-tooling/        # Agent 内部工具实现
├── instrumentation-api/
├── muzzle/                   # 兼容性机制
├── testing-common/
├── buildscripts/             # 上游构建逻辑
├── beacon/                   # 新增发行配置、基线、补丁台账、说明
└── .github/workflows/        # 适配 GuanceCloud 的 CI/发行
```

对于现有 JDBC、HTTP 或框架插桩增强，直接在对应原生模块中修改并补测试。不要同时启用一份相同目标的复制模块，否则可能重复插桩。新增独立框架支持时遵守上游模块约定及构建注册方式。

避免全仓库包名、目录名、格式和版权头替换。已有 `io.opentelemetry.*` 保持原名；Beacon 新增独立代码可使用自有命名空间，必要时遵守该模块的类加载约束。

每个功能 PR 同时包含实现、行为测试、框架兼容范围和用户影响说明；品牌修改和大范围机械改动独立提交。保留许可证与 NOTICE。

## 5. 基线和自有改动台账

建议 `beacon/upstream.yaml` 记录：

- 官方仓库、已采用发布标签、该标签真实 commit。
- 对应 SDK、Instrumentation API、构建工具版本。
- 当前 Beacon 产品版本及兼容策略。

建议 `beacon/changes.yaml` 或同类清单记录每项下游改动：

| 字段 | 作用 |
| --- | --- |
| 功能 ID、负责人 | 找到维护人 |
| 涉及模块和关键提交 | 定位冲突与变更范围 |
| 原因与预期行为 | 判断升级后是否保留语义 |
| 回归用例 | 防止无冲突合并后功能退化 |
| 上游 Issue/PR | 跟踪通用修复是否已合入 |
| 移除或重新评估条件 | 避免长期重复实现 |

台账用于追溯，不表示每次升级都重新应用全部补丁；正常同步通过 Git 合并保留现有修改。

## 6. 每次上游同步的实际步骤

### 6.1 自动发现

每天检查官方稳定 Release，与 `beacon/upstream.yaml` 比较。以目标标签去重，生成同步任务。稳定产品线优先合并发布标签，官方 main 可用于独立预兼容任务，不直接进入正式发行。

### 6.2 在独立同步分支合并完整源码

以下命令是迁移完成后的操作模板；`beacon-java` 远程仓库及 `beacon` 分支需先按迁移计划建立。目标 `v2.31.1` 只是具体语法示例，应替换为评审选定且尚未采用的官方版本。

```bash
git clone https://github.com/GuanceCloud/beacon-java.git
cd beacon-java
git remote add upstream https://github.com/open-telemetry/opentelemetry-java-instrumentation.git
git fetch origin
git fetch --no-tags upstream 'refs/tags/*:refs/upstream-tags/*'
git switch -c sync/otel-2.31.1 origin/beacon
git rev-parse 'refs/upstream-tags/v2.31.1^{commit}'
git merge --no-ff --no-commit refs/upstream-tags/v2.31.1
```

合并前核对标签来源和目标提交，确保它是预期的官方发布。首次迁移还要验证产品分支与上游具有共同历史，发现异常基线时先调查，不使用 `--allow-unrelated-histories` 掩盖问题。

若发生冲突，处理后 `git add` 并运行相关测试，最终提交合并。不要使用全局 `ours/theirs` 策略自动取一边；原生插桩冲突需要理解 Advice、matcher、helper、上下文和异常处理语义。

即使无文本冲突，也要审查所有自有改动涉及的模块是否发生上游行为变化。

### 6.3 合并后的适配

1. 对照台账检查全部 GuanceCloud 增强是否仍有效。
2. 删除被上游等价实现替代的本地重复逻辑，并保留回归用例。
3. 使用目标上游的 SDK 和构建依赖基线，重新验证 SecurityContext/Profiler。
4. 检查配置默认值、框架范围、语义字段、类加载及性能变化。
5. 更新基线文件到本次实际合并的官方 tag 和 commit。

### 6.4 提交同步 PR

同步分支完成提交后推送到 origin，PR 目标为 `beacon`。PR 包含版本变化、冲突解决说明、自有功能回归结果和已知限制。

同步 PR 应使用保留 ancestry 的合并方式。不要将整次上游同步 squash 为一个提交，否则会丢掉已采用上游提交的祖先关系，后续合并难以正确识别已同步历史。普通自有功能 PR 可以遵循团队的提交整理规则。

## 7. 原生插桩增强的开发流程

```text
从 beacon 创建 feature 分支
    ↓
修改对应 instrumentation 模块
    ↓
实现与测试同 PR：正常调用、异常、异步、边界框架版本
    ↓
模块测试 + Muzzle + Agent smoke test
    ↓
负责人评审、合并到 beacon
    ↓
记录自有差异，进入下一次 Beacon 发行
```

特别检查同一请求是否重复产生 Span，Advice 是否引用了不可见的应用类，目标类匹配是否过宽，异常是否影响业务。官方开发文档中的模块、SPI、类加载与 Muzzle 约定应跟随所采用的上游版本。

## 8. 如何验证同步后保留官方能力和自身增强

源码级增强后不能再承诺 JAR 内官方条目完全相同；验证重点改为保留上游测试体系、回归语义和明确差异。

每次同步至少验证：

- 目标上游版本要求的构建环境和相关上游检查。
- 被修改模块的测试及目标依赖版本范围，包含适用的 Muzzle 检查。
- 声明支持的 JDK、框架 smoke tests 和跨服务上下文。
- GuanceCloud 自有增强的回归用例。
- SecurityContext、Profiling、DataKit 与平台联调。
- 与官方同基线 Agent 对照；增强引入的预期差异独立列出。
- CPU、内存、吞吐和延迟对比，以及导出失败和关闭行为。

不必在每次小改动时跑全仓库所有组合，但上游整版同步和正式发行必须执行定义好的必需矩阵，不能只运行 `shadowJar` 就发布。

构建任务沿用采用版本的 Wrapper 和 Agent 任务。现有项目使用的示例任务为：

```bash
./gradlew --no-daemon -Potel.stable=true :javaagent:shadowJar
```

该命令仅用于构建说明，不代表全部测试或最终 Beacon 品牌打包已经配置。

## 9. 产品版本和上游版本分开

维护两套语义：

- 上游基线：例如 OTel Java Instrumentation 2.31.1 与其 SDK 组合。
- 产品版本：例如 Beacon Java 1.0.0，包含该基线及 GuanceCloud 的改动。

上述 Beacon 版本是规划示例，不代表已发布。

不要盲目把全仓库上游版本常量替换成 Beacon 1.0.0；上游版本值可能参与插件、依赖和 BOM 解析。建议新增独立的 `beaconVersion`，用于自有 tag、最终制品名、发行元数据；需要发布修改过的库模块时，设计自有 Maven 坐标与版本，不冒充官方制品。

最终清单记录 Beacon tag/commit、官方基线 tag/commit、外部组件、构建环境、实际依赖和制品摘要。

## 10. 发行流程

```text
自有功能 PR / 上游同步 PR
            ↓
          beacon
            ↓
Release PR：Beacon 版本、CHANGELOG、支持矩阵
            ↓
确定提交构建完整 Agent 与发行包
            ↓
必需矩阵 + 灰度 + 最终制品验证
            ↓
beacon-v<版本> tag 与 GitHub Release
            ↓
更新 GuanceCloud/beacon 产品总入口
```

不在最终发布时再合并上游、更新依赖或改源码。候选制品与测试证据绑定到确定提交和摘要；RC 转正式版若修改内部版本信息，需要重新构建并验证。

发布失败或线上回归时保留问题版本的追溯信息，恢复上一固定发行包和配置。不得覆盖已发布 tag 或同版本资产。维护旧版本时在 `release/*` 回补必要修复，不要求强行升级整个上游。

## 11. 近期实施顺序

1. 审计现有 `guance-v2`、`guance`、`async-profile` 的生产用途与差异，确认代码权威来源。
2. 确定重命名现有仓库或保留历史导入新仓库，形成迁移清单后再实施。
3. 建立 `beacon` 产品分支、官方基线记录和自有改动台账。
4. 从选定完整源码构建首个 Beacon 候选，跑通现有功能。
5. 演练一次官方发布标签的合并，验证原生增强的回归与冲突处理。
6. 固化同步 PR、必需检查、产品版本和发行流水线。

## 参考

- [GitHub 仓库重命名](https://docs.github.com/en/repositories/creating-and-managing-repositories/renaming-a-repository)
- [GitHub 仓库复制](https://docs.github.com/en/repositories/creating-and-managing-repositories/duplicating-a-repository)
- [官方 instrumentation 开发指南](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/docs/contributing/writing-instrumentation.md)
- [官方 Agent 插桩模块指南](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/docs/contributing/writing-instrumentation-module.md)
- [官方 Agent 结构](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/docs/contributing/javaagent-structure.md)

开发实施时应将这些开发文档切换到实际采用的上游标签阅读；main 文档仅用于理解当前总体机制。
