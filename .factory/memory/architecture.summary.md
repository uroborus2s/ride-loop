# 架构摘要

- 架构方案：双核服务 + 共享平台库。
- 外部入口：`gateway-api`
- 主业务事实源：`platform-core (Node.js + Stratix)`
- 调度服务：`dispatch-engine (Go)`
- 主数据库：`PostgreSQL`
- 高频短态与缓存：`Redis`

## 端形态

- 乘客端：微信小程序
- 司机轻端：微信小程序
- 司机重端：独立司机 App
- 运营端：统一管理 Web

## 核心边界

- 外部流量统一进入 `gateway-api`
- 商城订单、出行订单、支付、佣金、钱包都以 `platform-core + PostgreSQL` 为准
- `dispatch-engine` 只管理司机在线态、候选匹配、派单和重派
- 返现和返现券只允许站内循环

## 模块映射

- `MOD-001` `gateway-api`
- `MOD-002` `identity-driver`
- `MOD-003` `commerce`
- `MOD-004` `ride-order`
- `MOD-005` `finance`
- `MOD-006` `dispatch-engine`
- `MOD-007` `ops-admin`
- `MOD-008` `driver-access`
- `MOD-009` `message-center`
- `MOD-010` `support-service`
- `MOD-011` `risk-compliance`
- `MOD-012` `analytics-insights`
