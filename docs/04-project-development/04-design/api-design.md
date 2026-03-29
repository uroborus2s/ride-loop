# 接口与契约设计

**项目名称：** 千乘坊（ride-loop）  
**文档状态：** 草稿  
**负责人：** AI 软件工厂  
**主要读者：** 架构 | 后端 | 前端 | 测试  
**上游输入：** PRD | 系统架构 | 模块边界  
**下游输出：** 实现接口契约 | 集成测试 | 联调用例  
**关联 ID：** `API-001` - `API-066`, `REQ-001` - `REQ-017`  
**最后更新：** 2026-03-29  

## 1. API 总体原则

- 外部 API 统一经 `gateway-api` 暴露。
- 内部服务首版优先使用同步 HTTP，保持可联调与可观测。
- 支付回调、退款回调、派单回写、工单处理、后台人工干预必须具备幂等控制。
- 所有错误响应使用统一结构：`code`, `message`, `requestId`, `details`。
- 所有关键写接口支持 `X-Idempotency-Key`，后台高风险操作另附 `operatorId` 和 `reason`。

## 2. 外部 API 分组

| 分组 | 主要接口 | 主要调用方 |
|---|---|---|
| 身份与会话 | 登录、用户信息、司机档案、角色查询 | 乘客、司机、后台 |
| 商城与司机专区 | 商品列表、详情、下单、订单查询 | 乘客、司机轻端 |
| 出行 | 预估价、创建叫车单、订单状态、取消 | 乘客 |
| 司机轻端 | 入驻、二维码、收益、司机专区、工单 | 司机微信小程序 |
| 司机重端 | 设备绑定、在线状态、派单、行程推进 | 司机 App |
| 后台 | 审核、规则、订单、派单、台账、退款、风控、RBAC | 运营 Web |

## 3. 关键外部接口

| 接口 ID | 方法 | 路径 | 说明 |
|---|---|---|---|
| `API-001` | `POST` | `/api/v1/auth/session` | 创建用户会话 |
| `API-002` | `GET` | `/api/v1/mall/products` | 商品列表查询，支持普通商城与司机专区场景 |
| `API-003` | `POST` | `/api/v1/ride/orders` | 出行下单入口 |
| `API-004` | `GET` | `/api/v1/admin/*` | 后台统一聚合读取入口占位 |
| `API-005` | `GET` | `/api/v1/me/profile` | 用户/司机身份与能力查询 |
| `API-007` | `POST` | `/api/v1/mall/orders` | 商城与司机专区下单 |
| `API-009` | `GET` | `/api/v1/ride/orders/{orderId}` | 行程订单详情 |
| `API-012` | `GET` | `/api/v1/driver/earnings` | 司机收益查询的兼容入口占位 |
| `API-015` | `POST` | `/api/v1/admin/ops/*` | 后台统一写入口占位 |
| `API-016` | `POST` | `/api/v1/driver/access/applications` | 司机入驻资料提交 |
| `API-018` | `POST` | `/api/v1/support/tickets` | 创建售后/申诉工单 |
| `API-020` | `GET` | `/api/v1/admin/analytics/overview` | 经营概览兼容入口占位 |
| `API-021` | `GET` | `/api/v1/driver/dashboard` | 司机轻端工作台聚合 |
| `API-022` | `GET` | `/api/v1/driver/distribution/qrcodes` | 司机二维码资产列表 |
| `API-023` | `POST` | `/api/v1/driver/distribution/qrcodes` | 创建司机二维码资产 |
| `API-024` | `GET` | `/api/v1/driver/distribution/orders` | 归因订单列表 |
| `API-025` | `GET` | `/api/v1/driver/finance/summary` | 收益、余额、券总览 |
| `API-026` | `GET` | `/api/v1/driver/finance/ledger` | 收益台账明细 |
| `API-027` | `GET` | `/api/v1/driver/finance/coupons` | 券与权益列表 |
| `API-028` | `GET` | `/api/v1/driver/access/application` | 司机申请与审核状态 |
| `API-029` | `GET` | `/api/v1/messages` | 通用消息中心 |
| `API-030` | `POST` | `/api/v1/driver-app/session/bootstrap` | 司机重端启动初始化 |
| `API-031` | `POST` | `/api/v1/driver-app/devices/bind` | 司机重端设备绑定 |
| `API-032` | `POST` | `/api/v1/driver-app/runtime/status` | 司机重端上下线 |
| `API-033` | `POST` | `/api/v1/driver-app/runtime/heartbeat` | 司机重端心跳与定位 |
| `API-034` | `GET` | `/api/v1/driver-app/dispatch/current` | 当前待响应派单 |
| `API-035` | `POST` | `/api/v1/driver-app/dispatch/{dispatchId}/actions` | 接单/拒单/超时响应 |
| `API-036` | `POST` | `/api/v1/driver-app/trips/{tripId}/status` | 行程状态推进 |
| `API-037` | `POST` | `/api/v1/driver-app/trips/{tripId}/exceptions` | 行程异常上报 |
| `API-038` | `GET` | `/api/v1/driver-app/orders` | 司机重端历史订单/详情查询 |
| `API-039` | `GET` | `/api/v1/admin/dashboard` | 运营首页总览 |
| `API-040` | `GET` | `/api/v1/admin/drivers/reviews` | 司机审核列表 |
| `API-041` | `POST` | `/api/v1/admin/drivers/{driverId}/review` | 司机审核与能力调整 |
| `API-042` | `GET` | `/api/v1/admin/distribution/rules` | 归因与活动规则查询 |
| `API-043` | `POST` | `/api/v1/admin/distribution/rules` | 归因与活动规则保存 |
| `API-044` | `GET` | `/api/v1/admin/dispatch/monitor` | 派单监控聚合数据 |
| `API-045` | `POST` | `/api/v1/admin/dispatch/interventions` | 派单人工干预 |
| `API-046` | `GET` | `/api/v1/admin/finance/ledger` | 台账与余额查询 |
| `API-047` | `GET` | `/api/v1/admin/support/tickets` | 工单列表与详情 |
| `API-048` | `POST` | `/api/v1/admin/support/tickets/{ticketId}/actions` | 工单处理动作 |
| `API-049` | `GET` | `/api/v1/admin/risk/events` | 风险事件查询 |
| `API-050` | `GET` | `/api/v1/admin/analytics/business` | 经营分析看板 |
| `API-051` | `GET` | `/api/v1/admin/mall/products` | 后台商品列表查询 |
| `API-052` | `POST` | `/api/v1/admin/mall/products` | 后台商品保存、发布、下线 |
| `API-053` | `GET` | `/api/v1/admin/orders/mall` | 商城订单查询 |
| `API-054` | `GET` | `/api/v1/admin/orders/ride` | 出行订单查询 |
| `API-055` | `GET` | `/api/v1/admin/finance/commission-rules` | 佣金规则查询 |
| `API-056` | `POST` | `/api/v1/admin/finance/commission-rules` | 佣金规则保存与发布 |
| `API-057` | `GET` | `/api/v1/admin/finance/refunds` | 退款与对账异常列表 |
| `API-058` | `POST` | `/api/v1/admin/finance/refunds/{refundId}/actions` | 退款处理动作 |
| `API-059` | `GET` | `/api/v1/admin/audit/logs` | 审计日志查询 |
| `API-060` | `GET` | `/api/v1/admin/settings/rbac` | RBAC 查询 |
| `API-061` | `POST` | `/api/v1/admin/settings/rbac` | RBAC 保存 |
| `API-062` | `GET` | `/api/v1/mall/orders` | 商城与司机专区订单列表 |
| `API-063` | `GET` | `/api/v1/mall/orders/{orderId}` | 商城与司机专区订单详情 |
| `API-064` | `GET` | `/api/v1/support/tickets` | 我的工单列表 |
| `API-065` | `GET` | `/api/v1/support/tickets/{ticketId}` | 工单详情 |
| `API-066` | `GET` | `/api/v1/mall/products/{productId}` | 商品详情查询 |

## 4. 关键内部接口

| 接口 ID | 方法 | 路径 | 调用方 -> 提供方 | 说明 |
|---|---|---|---|---|
| `API-006` | `POST` | `/internal/v1/identity/capabilities/check` | `commerce/ride-order/finance -> identity-driver` | 校验角色与司机能力 |
| `API-008` | `POST` | `/internal/v1/commerce/orders/paid` | `payment -> commerce` | 商城支付结果回写 |
| `API-010` | `POST` | `/internal/v1/ride/dispatch-tasks` | `ride-order -> dispatch-engine` | 创建派单任务 |
| `API-011` | `POST` | `/internal/v1/finance/settlements` | `commerce/ride-order -> finance` | 触发支付后结算 |
| `API-013` | `POST` | `/internal/v1/dispatch/requests` | `ride-order -> dispatch-engine` | 派单请求 |
| `API-014` | `POST` | `/internal/v1/dispatch/events` | `dispatch-engine/driver-app -> ride-order` | 司机在线、接单、派单事件回传 |
| `API-017` | `POST` | `/internal/v1/messages/tasks` | `commerce/ride-order/finance/support -> message-center` | 创建通知任务 |
| `API-019` | `GET` | `/internal/v1/risk/events` | `ops-admin -> risk-compliance` | 风险线索与审计查询 |

## 5. 端级接口分组

### 乘客与共享商城接口

- 身份：`API-001`, `API-005`
- 商城商品：`API-002`, `API-066`
- 商城订单：`API-007`, `API-062`, `API-063`
- 出行：`API-003`, `API-009`
- 工单：`API-018`, `API-064`, `API-065`

### 司机轻端接口清单

- 入驻与审核：`API-016`, `API-028`
- 工作台：`API-021`
- 分销二维码：`API-022`, `API-023`
- 归因订单：`API-024`
- 收益中心：`API-025`, `API-026`, `API-027`
- 司机专区：`API-002`, `API-066`, `API-007`, `API-062`, `API-063`
- 消息与工单：`API-029`, `API-018`, `API-064`, `API-065`

### 司机重端接口清单

- 启动与设备绑定：`API-030`, `API-031`
- 上下线与定位：`API-032`, `API-033`
- 派单响应：`API-034`, `API-035`
- 行程状态：`API-036`
- 异常上报：`API-037`, `API-018`
- 历史订单：`API-038`, `API-009`
- 消息与申诉：`API-029`, `API-064`, `API-065`

### 运营 Web 接口清单

- 首页总览：`API-039`
- 司机审核：`API-040`, `API-041`
- 商品管理：`API-051`, `API-052`
- 归因规则：`API-042`, `API-043`
- 佣金规则：`API-055`, `API-056`
- 订单中心：`API-053`, `API-054`
- 派单监控与干预：`API-044`, `API-045`
- 资金与退款：`API-046`, `API-057`, `API-058`
- 工单处理：`API-047`, `API-048`
- 风控审计：`API-049`, `API-059`
- 经营看板：`API-050`
- 权限配置：`API-060`, `API-061`

## 6. 关键请求/响应约束

### 商城下单 `API-007`

- 输入：`userId`, `scene`, `items[]`, `couponIds[]`, `walletAmount`, `attributionSessionId`, `deliveryInfo`
- 输出：`orderId`, `orderStatus`, `deductionSummary`, `paymentIntent`
- 约束：
  - 归因会话不存在时允许下单，但不生成分销收益。
  - 支付前不生成佣金分录。
  - 司机专区订单只能消耗站内余额和券，不暴露提现路径。

### 出行下单 `API-003`

- 输入：`passengerId`, `origin`, `destination`, `estimatedPrice`, `attributionSessionId`
- 输出：`rideOrderId`, `orderStatus`, `dispatchRequestId`
- 约束：
  - 非武汉服务范围直接拒绝。
  - 创建成功后才调用 `dispatch-engine`。

### 司机轻端工作台 `API-021`

- 输入：`driverId`, `cityCode`
- 输出：`capabilityStatus`, `incomeSummary`, `distributionSummary`, `driverZoneSummary`, `todoList`
- 约束：
  - 必须返回能力状态，前端据此决定入口可见性与置灰原因。
  - 所有金额由服务端聚合，前端不得本地求和。

### 司机重端启动初始化 `API-030`

- 输入：`driverId`, `deviceId`, `appVersion`, `osVersion`, `pushToken`
- 输出：`driverStatus`, `bindStatus`, `permissionChecklist`, `activeTrip`, `forceUpgrade`
- 约束：
  - 审核未通过不得返回“可上线接单”。
  - 如有在途订单，必须返回订单摘要，重端直接恢复履约态。

### 司机重端行程状态推进 `API-036`

- 输入：`tripId`, `action`, `location`, `occurredAt`
- 输出：`tripStatus`, `timeline[]`, `nextAllowedActions[]`
- 约束：
  - 只允许司机重端调用。
  - 非法状态迁移返回 `RIDE-409`。

### 商品管理 `API-052`

- 输入：`productDraft`, `action`, `operatorId`, `reason`
- 输出：`productId`, `status`, `publishedAt`
- 约束：
  - 商品保存、发布、下线都必须有版本号。
  - 已发布商品变更不得直接覆盖历史审计记录。

### 佣金规则发布 `API-056`

- 输入：`ruleSet`, `publishAt`, `changeReason`, `operatorId`
- 输出：`version`, `status`
- 约束：
  - 同一业务线与同一生效时段不允许规则重叠。
  - 变更必须保留发布前后差异。

### 退款处理 `API-058`

- 输入：`refundId`, `action`, `amount`, `reason`, `operatorId`
- 输出：`refundStatus`, `ledgerRefs[]`
- 约束：
  - 通过退款必须生成逆向台账或冲销引用。
  - 已完成退款不可重复处理。

### RBAC 变更 `API-061`

- 输入：`action`, `payload`, `reason`, `operatorId`
- 输出：`roleStatus`, `version`
- 约束：
  - 高危权限变更必须二次确认。
  - 必须可追溯到变更前后权限矩阵。

## 7. 错误码与幂等策略

| 代码 | 场景 | 说明 |
|---|---|---|
| `AUTH-401` | 未登录或令牌无效 | 需要重新鉴权 |
| `ROLE-403` | 角色无权限 | 司机/运营功能越权 |
| `MALL-404` | 商品或订单不存在 | 商品下架或订单无权限 |
| `MALL-409` | 商城订单状态冲突 | 重复支付或重复下单 |
| `RIDE-409` | 出行订单状态冲突 | 非法状态迁移 |
| `FIN-409` | 资金分录冲突 | 重复回调或重复冲销 |
| `DISPATCH-503` | 调度不可用 | 可重试或人工处理 |
| `SUPPORT-409` | 工单状态冲突 | 重复关闭或重复处理 |
| `DRIVER-423` | 司机能力受限 | 审核未通过或能力被暂停 |
| `OPS-409` | 后台干预冲突 | 重复干预或状态已变化 |
| `RBAC-409` | 权限版本冲突 | 角色权限被其他管理员更新 |

- 幂等键使用场景：
  - 商城下单
  - 支付回调
  - 退款回调
  - 派单事件回传
  - 工单处理回写
  - 后台人工干预
  - 司机重端状态推进

## 8. 兼容与版本策略

- 外部 API 路径包含 `/api/v1/` 版本前缀。
- 内部接口路径包含 `/internal/v1/` 版本前缀。
- 字段变更遵循“新增兼容、删除延后”的原则。
- 兼容入口 `API-004`, `API-012`, `API-015`, `API-020` 只作为聚合/迁移占位，不作为首版主实现优先目标。
- 任一接口变更必须先更新本文件和追踪矩阵。

## 9. OpenAPI 对应文件

- 乘客端：`openapi/passenger-miniapp.openapi.yaml`
- 司机轻端：`openapi/driver-light-miniapp.openapi.yaml`
- 司机重端：`openapi/driver-heavy-app.openapi.yaml`
- 运营 Web：`openapi/ops-web.openapi.yaml`

## 10. 未决项

- 地图供应商相关字段和估价接口待第三方适配层确定后补齐。
- 支付回调签名字段与退款回调字段待支付渠道确认后补齐。
- 司机重端推送/语音播报的技术选型仍需定稿。

## 11. 变更记录

| 日期 | 变更内容 | 变更人 |
|---|---|---|
| 2026-03-29 | 重构接口编号并补齐司机重端、消息、工单和看板接口 | AI 软件工厂 |
| 2026-03-29 | 补充三类端的端级接口清单 | AI 软件工厂 |
| 2026-03-29 | 补充后台商品、退款、RBAC 接口以及司机专区共享接口 | AI 软件工厂 |
