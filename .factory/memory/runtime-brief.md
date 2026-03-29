# 项目压缩运行卡

- 生成时间：2026-03-29 22:30:03
- 负责人：项目协调者
- 项目：ride-loop
- 当前阶段：PLAN
- 当前模式：未知
- 技术画像：千乘坊双核服务画像
- 技术栈：未设置
- 活跃工作项：0
- 阻塞项：0
- 开放风险：0
- 最近发布包：无
- 最近交接包：无
- 最近快照：无
- 备注：补齐半初始化项目规则入口

## AI 最小读取顺序

1. 先读本文件 `/.factory/memory/runtime-brief.md`
2. 再读 `/.factory/memory/role-charter.project.md`
3. 再读 `/.factory/project.json`
4. 再读 `/.factory/memory/motivation-state.md`、`/.factory/memory/autonomy-rules.md`、`/.factory/memory/evolution-baseline.md`
5. 再读当前阶段核心文档
6. 只有需要背景解释时，才读人类长文档

## 当前阶段核心文档

- `docs/04-project-development/05-development-process/wbs.md`
- `docs/04-project-development/05-development-process/task-breakdown.md`
- `docs/04-project-development/05-development-process/implementation-plan.md`

## 必守规则

- 不跳阶段。
- 代码类工作必须走 PR 闭环后再关单。
- 任何已接受变更都要同步代码、文档、测试、`.factory/memory/`。
- 遇到阻塞、空转或质量漂移时，优先执行 `factory-dispatch recovery`。
- 发现问题时优先做模式级修复，再把有效做法沉淀到 `evolution-baseline.md`。
- 实现前优先读取 `docs/04-project-development/04-design/technical-selection.md`。
- 任务单位是人天，最小精度 0.5，但不是默认拆分步长。

## 当前推荐动作

- `python3 ../../AiProject/shanforge/scripts/factory-dispatch session --project "." --owner "项目协调者"`
- `python3 ../../AiProject/shanforge/scripts/factory-dispatch board --project "." --owner "项目协调者" --focus "当前协作焦点"`
- `python3 ../../AiProject/shanforge/scripts/factory-dispatch doctor --project "." --owner "项目协调者" --scope full`
