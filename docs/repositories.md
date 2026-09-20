# 本地仓库与后续维护

## 已完成

- `beacon`：新建 Git 仓库，主线 `main`；包含产品方案与组件登记。
- `beacon-java`：从 GuanceCloud 旧仓库完整克隆，保留源代码、提交历史、历史标签和 `legacy/*` 分支引用；不是 ZIP 导入或浅克隆。
- 从 `guance-v2` 提交 `73a8f7edd0415f0e8651d3d1f3f295e6e6d4d1ea` 建立 Java 产品分支 `beacon`。
- 官方发布标签存入 `refs/upstream-tags/*`，避免与旧仓库 `v*` 标签混淆。
- 已检查官方 `v2.30.0` 是导入提交的祖先。下游 Gradle 版本 `2.30.2` 保留，不据此推导官方版本。
- 保留旧工作目录，不改动原有仓库。新克隆已解除本地对象缓存依赖。

## 远程配置

| 仓库 | remote | 用途 |
| --- | --- | --- |
| beacon | origin | 预配置 `https://github.com/GuanceCloud/beacon.git` |
| beacon-java | origin | 预配置 `https://github.com/GuanceCloud/beacon-java.git` |
| beacon-java | legacy | 原 `GuanceCloud/opentelemetry-java-instrumentation` |
| beacon-java | upstream | 官方 `open-telemetry/opentelemetry-java-instrumentation` |

配置 URL 不代表已创建或可访问目标 GitHub 仓库。本次没有执行远程创建、push、PR 或 Release。

## 后续顺序

1. 审计旧代码相对官方基线的差异，明确哪些增强进入首版。
2. 在 Java 仓库按 `beacon/UPSTREAM.md` 选择目标官方标签、合并并验证。抓取最新引用不等于完成升级。
3. 完成 JDK、框架、性能和 DataKit 联调，再实现 Beacon 独立版本及制品包装。
4. 审计继承的 `.github/workflows/`：旧发布流程仍指向旧分支和旧标签规则，不能直接作为 Beacon 发布流程使用。
5. 获准远程迁移后，先建立或确认目标仓库、禁用未审计 Actions，再推送产品分支并设置默认分支和保护规则。不要直接 `push --mirror` 或盲推全部标签。
6. `legacy/*` 当前是远程跟踪引用，`git push --all` 不会迁移它们。历史分支是否迁移需逐条决定并显式推送；历史对象已保存在本地。
7. 使用 `beacon-vX.Y.Z` 发布 Java；在本仓库记录实际组件版本、SHA、校验文件与验收结果。

本次初始化未修改 Java 功能代码、包名、Gradle 模块和版本号；未构建 Agent，未完成原生插桩、安全上下文或 Profiling 的新增集成。
