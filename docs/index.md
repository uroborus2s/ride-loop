---
title: ride-loop
mkdocs:
  home_access: public
  nav:
    - title: 入门说明
      children:
        - title: 概览
          path: 01-getting-started/index.md
          access: public
    - title: 用户指南
      children:
        - title: 概览
          path: 02-user-guide/index.md
          access: public
    - title: 项目开发文档（内）
      children:
        - title: 概览
          path: 04-project-development/index.md
          access: public
        - title: 项目治理
          children:
            - title: 概览
              path: 04-project-development/01-governance/index.md
              access: private
            - title: 项目章程
              path: 04-project-development/01-governance/project-charter.md
              access: private
        - title: 调研与决策
          children:
            - title: 概览
              path: 04-project-development/02-discovery/index.md
              access: private
            - title: 调研输入
              path: 04-project-development/02-discovery/input.md
              access: private
            - title: 头脑风暴记录
              path: 04-project-development/02-discovery/brainstorm-record.md
              access: private
        - title: 需求
          children:
            - title: 概览
              path: 04-project-development/03-requirements/index.md
              access: public
            - title: 产品需求文档（PRD）
              path: 04-project-development/03-requirements/prd.md
              access: public
            - title: 需求分析文档
              path: 04-project-development/03-requirements/requirements-analysis.md
              access: public
            - title: 需求一致性校验报告
              path: 04-project-development/03-requirements/requirements-verification.md
              access: private
            - title: 角色与业务模块需求分解
              path: 04-project-development/03-requirements/role-module-requirements.md
              access: public
            - title: 端形态与渠道策略
              path: 04-project-development/03-requirements/terminal-strategy.md
              access: private
        - title: 设计文档
          children:
            - title: 概览
              path: 04-project-development/04-design/index.md
              access: private
            - title: 技术选型与工程规则
              path: 04-project-development/04-design/technical-selection.md
              access: private
            - title: 系统架构设计
              path: 04-project-development/04-design/system-architecture.md
              access: private
            - title: 模块边界文档
              path: 04-project-development/04-design/module-boundaries.md
              access: private
            - title: 接口与契约设计
              path: 04-project-development/04-design/api-design.md
              access: private
            - title: 后端设计
              path: 04-project-development/04-design/backend-design.md
              access: private
            - title: 数据库详细设计
              path: 04-project-development/04-design/database-design.md
              access: private
            - title: UX/UI 与交互设计
              path: 04-project-development/04-design/ux-ui-design.md
              access: private
            - title: 数据库 ER 图
              path: 04-project-development/04-design/database-er-diagram.md
              access: private
            - title: 设计令牌与组件规范
              path: 04-project-development/04-design/design-token-component-spec.md
              access: private
            - title: 设计软件提示词包
              path: 04-project-development/04-design/design-tool-prompt-pack.md
              access: private
            - title: 司机重端设计
              path: 04-project-development/04-design/driver-heavy-app-spec.md
              access: private
            - title: 司机轻端设计
              path: 04-project-development/04-design/driver-light-miniapp-spec.md
              access: private
            - title: openapi
              children:
                - title: 概览
                  path: 04-project-development/04-design/openapi/index.md
                  access: private
            - title: 运营 Web 设计
              path: 04-project-development/04-design/ops-web-spec.md
              access: private
            - title: 页面逐页提示词清单
              path: 04-project-development/04-design/page-prompt-catalog.md
              access: private
            - title: 系统详细设计
              path: 04-project-development/04-design/system-detailed-design.md
              access: private
            - title: 页面级详细设计矩阵
              path: 04-project-development/04-design/ui-page-detail-matrix.md
              access: private
        - title: 开发过程文档
          children:
            - title: 概览
              path: 04-project-development/05-development-process/index.md
              access: public
            - title: 工作分解结构
              path: 04-project-development/05-development-process/wbs.md
              access: public
            - title: 实施计划
              path: 04-project-development/05-development-process/implementation-plan.md
              access: public
            - title: 任务分解文档
              path: 04-project-development/05-development-process/task-breakdown.md
              access: public
            - title: 工作量评估首页
              path: 04-project-development/05-development-process/business-overview.md
              access: public
            - title: 工作量评估说明
              path: 04-project-development/05-development-process/quotation-proposal.md
              access: public
            - title: 开发工作量评估详单
              path: 04-project-development/05-development-process/quotation-task-breakdown.md
              access: public
        - title: 测试与验证
          children:
            - title: 概览
              path: 04-project-development/06-testing-verification/index.md
              access: private
            - title: 测试计划
              path: 04-project-development/06-testing-verification/test-plan.md
              access: private
        - title: 发布与交付
          children:
            - title: 概览
              path: 04-project-development/07-release-delivery/index.md
              access: private
        - title: 运维与维护
          children:
            - title: 概览
              path: 04-project-development/08-operations-maintenance/index.md
              access: private
            - title: 部署与运行说明
              path: 04-project-development/08-operations-maintenance/deployment-guide.md
              access: private
            - title: 上线外部服务清单
              path: 04-project-development/08-operations-maintenance/external-services-register.md
              access: private
        - title: 演进复盘
          children:
            - title: 概览
              path: 04-project-development/09-evolution/index.md
              access: private
        - title: 追踪矩阵
          children:
            - title: 概览
              path: 04-project-development/10-traceability/index.md
              access: private
            - title: 需求追踪矩阵
              path: 04-project-development/10-traceability/requirements-matrix.md
              access: private
---

# 千乘坊（ride-loop）

这是 `ride-loop` 的正式项目文档入口。当前仓库按“山海工枢”阶段化工作流维护调研、需求、设计、计划、测试与部署文档，`docs-stratego` 只负责聚合展示，不反向改写源仓文档。

当前公开策略：

- 站点首页公开，用于承载全站导航。
- `01-getting-started/` 与 `02-user-guide/` 按项目 `docs_profile` 公开。
- `04-project-development/` 统一作为内部项目开发文档目录，页面默认私密。
- 新增、删除或移动 Markdown 页面后，需同步刷新根 `docs/index.md` 与对应目录概览页。

## 维护规则

- 根 `docs/index.md` 是唯一的导航声明文件，统一维护 `mkdocs.home_access` 与 `mkdocs.nav`。
- 子目录 `index.md` 只承载目录说明与推荐阅读顺序，不单独声明页面权限。
- 当前阶段以 `04-project-development/` 下的正式文档为事实源。
- 仓内 Markdown 链接统一使用相对路径，不写机器绝对路径。
- `.factory/` 保存 AI 记忆、阶段状态和执行过程，不替代正式文档。
