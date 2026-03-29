---
title: ride-loop
mkdocs:
  home_access: private
  nav:
    - title: 用户指南
      children:
        - title: 概览
          path: 02-user-guide/index.md
          access: private
    - title: 项目开发文档（内）
      children:
        - title: 概览
          path: 04-project-development/index.md
          access: private
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
            - title: 输入与约束
              path: 04-project-development/02-discovery/input.md
              access: private
            - title: 头脑风暴记录
              path: 04-project-development/02-discovery/brainstorm-record.md
              access: private
        - title: 需求
          children:
            - title: 概览
              path: 04-project-development/03-requirements/index.md
              access: private
            - title: PRD
              path: 04-project-development/03-requirements/prd.md
              access: private
            - title: 端形态与渠道策略
              path: 04-project-development/03-requirements/terminal-strategy.md
              access: private
            - title: 角色与模块需求分解
              path: 04-project-development/03-requirements/role-module-requirements.md
              access: private
            - title: 需求分析
              path: 04-project-development/03-requirements/requirements-analysis.md
              access: private
            - title: 需求校验
              path: 04-project-development/03-requirements/requirements-verification.md
              access: private
        - title: 设计
          children:
            - title: 概览
              path: 04-project-development/04-design/index.md
              access: private
            - title: 技术选型
              path: 04-project-development/04-design/technical-selection.md
              access: private
            - title: 系统架构
              path: 04-project-development/04-design/system-architecture.md
              access: private
            - title: 模块边界
              path: 04-project-development/04-design/module-boundaries.md
              access: private
            - title: API 设计
              path: 04-project-development/04-design/api-design.md
              access: private
            - title: 后端设计
              path: 04-project-development/04-design/backend-design.md
              access: private
            - title: 数据设计
              path: 04-project-development/04-design/database-design.md
              access: private
            - title: UX/UI 设计
              path: 04-project-development/04-design/ux-ui-design.md
              access: private
            - title: 司机轻端设计
              path: 04-project-development/04-design/driver-light-miniapp-spec.md
              access: private
            - title: 司机重端设计
              path: 04-project-development/04-design/driver-heavy-app-spec.md
              access: private
            - title: 运营 Web 设计
              path: 04-project-development/04-design/ops-web-spec.md
              access: private
        - title: 开发计划
          children:
            - title: 概览
              path: 04-project-development/05-development-process/index.md
              access: private
            - title: 实施计划
              path: 04-project-development/05-development-process/implementation-plan.md
              access: private
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
            - title: 部署说明
              path: 04-project-development/08-operations-maintenance/deployment-guide.md
              access: private
        - title: 演进
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

这是 `ride-loop` 的正式项目文档入口。当前仓库按“山海工枢”阶段化工作流维护调研、需求、设计、计划、测试与部署文档，代码实现必须以后续已批准文档为准。

## 维护规则

- 根 `docs/index.md` 是唯一的导航声明文件。
- 子目录 `index.md` 只承载目录说明与推荐阅读顺序。
- 当前阶段以 `04-project-development` 目录下的文档为正式事实源。
- `.factory/` 保存 AI 记忆、阶段状态和执行过程，不替代正式文档。
