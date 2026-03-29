# 项目状态诊断报告

- 诊断时间：2026-03-29 23:41:50
- 诊断负责人：项目医生
- 诊断范围：full
- 当前阶段：PLAN
- 结果：未通过
- 备注：发布策略文件补充后复检

## 锁状态

- 锁文件：`/Users/uroborus/NodeProject/ride-loop/.factory/project.lock`
- 检测开始时是否占用：否
- 最近锁原因：无
- 最近锁时间：无

## 规则入口状态

- `AGENTS.md` / `GEMINI.md` 当前更接近稳定协作入口，未发现明显的现状快照污染。

## 文档状态

- `docs/04-project-development/04-design/technical-selection.md`：就绪，已具备实质内容
- `docs/04-project-development/04-design/module-boundaries.md`：就绪，已具备实质内容
- `docs/04-project-development/05-development-process/wbs.md`：就绪，已具备实质内容
- `docs/04-project-development/05-development-process/task-breakdown.md`：就绪，已具备实质内容
- `docs/04-project-development/05-development-process/implementation-plan.md`：就绪，已具备实质内容

## docs-stratego 源文档状态

- `docs/index.md` 中 `01-getting-started/index.md` 的访问级别应为 `public`，当前为 `private`。
- `docs/index.md` 中 `02-user-guide/index.md` 的访问级别应为 `public`，当前为 `private`。
- `docs/index.md` 中 `04-project-development/03-requirements/index.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/03-requirements/prd.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/03-requirements/requirements-analysis.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/03-requirements/requirements-verification.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/03-requirements/role-module-requirements.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/03-requirements/terminal-strategy.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/05-development-process/index.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/05-development-process/wbs.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/05-development-process/implementation-plan.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/05-development-process/task-breakdown.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/05-development-process/business-overview.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/05-development-process/quotation-proposal.md` 的访问级别应为 `private`，当前为 `public`。
- `docs/index.md` 中 `04-project-development/05-development-process/quotation-task-breakdown.md` 的访问级别应为 `private`，当前为 `public`。

## AI 记忆状态

- AI 记忆晚于源文件更新时间不足，建议刷新 `factory-refresh-memory`。

## 追踪状态

- 当前追踪关系数：27。

## 任务拆解状态

- 当前无活跃实施任务。

## 阻塞与风险

- 当前无阻塞工作项。
- 当前无开放风险。

## 建议动作

- 执行 `factory-dispatch docs-index-refresh --project <项目路径>` 刷新目录 `index.md`，并清理文档中的机器绝对路径。
- 执行 `factory-dispatch memory --project <项目路径>` 刷新 AI 记忆。
