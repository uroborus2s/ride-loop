# 部署与运行说明

**项目名称：** 千乘坊（ride-loop）  
**文档状态：** 草稿  
**负责人：** AI 软件工厂  
**主要读者：** 开发 | 运维 | QA  
**上游输入：** 技术选型 | 系统架构 | 后端设计  
**下游输出：** 本地联调环境 | 测试环境部署 | 运维基线  
**关联 ID：** `DEP-001` - `DEP-005`, `OPS-001` - `OPS-004`  
**最后更新：** 2026-03-29  

## 1. 部署目标

- 为编码后的本地联调和测试环境准备统一的基础设施与服务启动方式。
- 确保 `gateway-api`、`platform-core`、`dispatch-engine`、`PostgreSQL`、`Redis` 能组成最小联调拓扑。

## 2. 目标运行单元

| 组件 | 说明 |
|---|---|
| `gateway-api` | 外部 API 接入层 |
| `platform-core` | 主业务服务 |
| `dispatch-engine` | 调度服务 |
| `PostgreSQL` | 主业务数据库 |
| `Redis` | 缓存、幂等和调度短态 |

## 3. 本地开发建议

- 使用 `Docker Compose` 管理 `PostgreSQL` 和 `Redis`。
- 由于当前环境没有本地 Go 工具链，调度服务优先采用容器化运行。
- Node 主业务服务在编码阶段根据最终锁定的 Stratix 版本启动。

## 4. 环境变量分类

- 通用：
  - `APP_ENV`
  - `CITY_CODE`
- 数据库：
  - `DB_HOST`
  - `DB_PORT`
  - `DB_NAME`
  - `DB_USERNAME`
  - `DB_PASSWORD`
- Redis：
  - `REDIS_HOST`
  - `REDIS_PORT`
- 内部服务：
  - `PLATFORM_CORE_BASE_URL`
  - `DISPATCH_ENGINE_BASE_URL`
- 第三方：
  - `PAYMENT_PROVIDER_*`
  - `MAP_PROVIDER_*`

## 5. 部署约束

- `platform-core` 是订单与台账真相源，数据库备份优先级最高。
- `dispatch-engine` 可横向扩容，但不得直接写主业务数据库订单事实。
- 后台服务与接入层必须启用权限控制。

## 6. 故障处理原则

- PostgreSQL 不可用：停止订单写入，禁止伪成功。
- Redis 不可用：禁用在线态和幂等缓存能力，并进入降级模式。
- 调度服务不可用：出行订单转为待人工处理，不影响商城链路。

## 7. 当前阻塞项

- 本机未安装 Go 工具链。
- 实际可安装的 Stratix 版本需在进入编码前锁定。
- 支付和地图服务接入方式仍待最终确认。

## 8. 变更记录

| 日期 | 变更内容 | 变更人 |
|---|---|---|
| 2026-03-29 | 初始版本 | AI 软件工厂 |

