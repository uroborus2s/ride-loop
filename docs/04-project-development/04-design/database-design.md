# 数据库详细设计

**项目名称：** 千乘坊（ride-loop）  
**文档状态：** 草稿  
**负责人：** AI 软件工厂  
**主要读者：** 后端 | DBA | QA | 运维  
**上游输入：** 系统详细设计 | 接口与契约设计 | 数据库 ER 图  
**下游输出：** 数据迁移设计 | 仓储实现 | 对账方案  
**关联 ID：** `DATA-001` - `DATA-041`, `REQ-001` - `REQ-017`  
**最后更新：** 2026-03-29  

## 1. 总体策略

- 使用单一 PostgreSQL 实例，按 schema 分域，不拆库。
- Redis 承担高频缓存和短态，不作为交易真相来源。
- 所有业务主表统一使用 `uuid` 主键，统一保留：
  - `created_at`
  - `updated_at`
  - `created_by`
  - `updated_by`
  - `request_id` 或 `operator_id`（按场景）
- 状态字段统一使用枚举字符串，不使用魔法数字。

## 2. Schema 规划

| Schema | 作用 | 说明 |
|---|---|---|
| `identity` | 账户、角色、司机准入 | 承载统一用户和司机能力 |
| `commerce` | 商品、门店、归因、商城/司机专区订单 | 承载分销与本地生活交易 |
| `ride` | 叫车订单、时间线、调度引用 | 承载出行业务真相 |
| `finance` | 佣金、钱包、券、退款 | 承载资金与站内循环 |
| `support` | 工单、申诉、附件 | 承载客服和售后 |
| `message` | 消息任务、站内收件箱 | 承载通知中心 |
| `risk` | 风险事件、审计日志 | 承载风控与审计 |
| `ops` | 城市配置、规则、RBAC | 承载后台配置面 |
| `analytics` | 小时级/日级指标快照 | 承载经营看板聚合 |

## 3. Redis 规划

| 键模式 | 作用 | TTL |
|---|---|---|
| `session:{userId}` | 用户会话 | 7d |
| `idempotency:{scope}:{key}` | 幂等控制 | 24h |
| `dispatch:driver:{driverId}` | 司机在线短态 | 90s 滚动续期 |
| `dispatch:request:{requestId}` | 派单运行态 | 30m |
| `coupon:usable:{driverId}` | 司机可用券缓存 | 10m |
| `dashboard:driver:{driverId}` | 司机工作台聚合缓存 | 30s |

## 4. 统一命名与字段约定

- 表名使用 `snake_case`，主表名使用单数语义复合名，如 `driver_profile`。
- 主键统一为 `{entity}_id`。
- 外键统一引用对方主键字段名。
- 金额字段统一 `numeric(14,2)`。
- 经纬度统一 `numeric(10,6)`。
- 状态枚举字段统一 `varchar(32)` 或 `varchar(64)`。

## 5. 数据字典

### 5.1 `identity` Schema

| 数据 ID | 表名 | 主键 | 说明 |
|---|---|---|---|
| `DATA-001` | `user_account` | `user_id` | 用户主体 |
| `DATA-002` | `user_role` | `user_role_id` | 用户角色绑定 |
| `DATA-003` | `driver_profile` | `driver_id` | 司机主档 |
| `DATA-004` | `driver_certification` | `certification_id` | 司机证件与资质 |
| `DATA-005` | `driver_vehicle` | `vehicle_id` | 司机车辆信息 |
| `DATA-006` | `driver_device` | `device_binding_id` | 司机重端设备绑定 |
| `DATA-007` | `driver_access_application` | `application_id` | 司机入驻申请 |

#### `identity.user_account`

| 字段 | 类型 | 约束 | 说明 |
|---|---|---|---|
| `user_id` | `uuid` | PK | 用户主键 |
| `mobile` | `varchar(32)` | UK | 手机号 |
| `nickname` | `varchar(64)` |  | 昵称 |
| `status` | `varchar(32)` | IDX | `active`, `disabled` |
| `city_code` | `varchar(16)` | IDX | 当前城市 |

索引建议：

- `uk_user_account_mobile (mobile)`
- `idx_user_account_city_status (city_code, status)`

#### `identity.user_role`

| 字段 | 类型 | 约束 | 说明 |
|---|---|---|---|
| `user_role_id` | `uuid` | PK | 绑定主键 |
| `user_id` | `uuid` | FK -> `user_account.user_id` | 用户 |
| `role_code` | `varchar(32)` | IDX | `passenger`, `driver`, `ops` |
| `status` | `varchar(32)` |  | `active`, `disabled` |

#### `identity.driver_profile`

| 字段 | 类型 | 约束 | 说明 |
|---|---|---|---|
| `driver_id` | `uuid` | PK | 司机主键 |
| `user_id` | `uuid` | FK | 对应用户 |
| `city_code` | `varchar(16)` | IDX | 武汉单城先用 |
| `capability_status` | `varchar(32)` | IDX | `not_applied`, `pending_review`, `approved`, `partial_suspended`, `rejected` |
| `audit_status` | `varchar(32)` | IDX | 审核状态 |
| `suspension_reason` | `varchar(255)` |  | 能力暂停原因 |

#### `identity.driver_certification`

核心字段：

- `driver_id`
- `cert_type`
- `cert_no`
- `valid_from`
- `valid_to`
- `verify_status`

#### `identity.driver_vehicle`

核心字段：

- `driver_id`
- `plate_no`
- `vehicle_type`
- `brand_name`
- `seat_count`
- `status`

#### `identity.driver_device`

核心字段：

- `driver_id`
- `device_id`
- `device_fingerprint`
- `platform`
- `bind_status`
- `last_active_at`

#### `identity.driver_access_application`

核心字段：

- `driver_id`
- `submit_status`
- `review_status`
- `review_reason`
- `submitted_at`
- `reviewed_at`

### 5.2 `commerce` Schema

| 数据 ID | 表名 | 主键 | 说明 |
|---|---|---|---|
| `DATA-008` | `merchant_store` | `store_id` | 商户/门店 |
| `DATA-009` | `product_category` | `category_id` | 商品分类 |
| `DATA-010` | `product_spu` | `spu_id` | 商品 SPU |
| `DATA-011` | `product_sku` | `sku_id` | 商品 SKU |
| `DATA-012` | `qr_code_asset` | `qr_code_id` | 司机二维码资产 |
| `DATA-013` | `attribution_session` | `attribution_session_id` | 二维码归因会话 |
| `DATA-014` | `mall_order` | `order_id` | 商城/司机专区订单 |
| `DATA-015` | `mall_order_item` | `order_item_id` | 商城/司机专区订单明细 |

#### `commerce.merchant_store`

关键字段：

- `store_id`
- `store_name`
- `store_type`
- `city_code`
- `district_code`
- `address_text`
- `lng`, `lat`
- `status`

#### `commerce.product_spu`

| 字段 | 类型 | 约束 | 说明 |
|---|---|---|---|
| `spu_id` | `uuid` | PK | SPU 主键 |
| `store_id` | `uuid` | FK | 所属门店 |
| `category_id` | `uuid` | FK | 分类 |
| `scene` | `varchar(32)` | IDX | `mall`, `driver-zone` |
| `fulfillment_type` | `varchar(32)` | IDX | `in_store_verify`, `pickup`, `delivery` |
| `status` | `varchar(32)` | IDX | `draft`, `published`, `offline` |
| `title` | `varchar(128)` |  | 商品标题 |
| `base_price` | `numeric(14,2)` |  | 基础价格 |

约束：

- 单个 `spu_id` 只允许一种 `fulfillment_type`。
- `scene = driver-zone` 的商品必须可被站内余额或券覆盖。

索引建议：

- `idx_product_spu_scene_status (scene, status)`
- `idx_product_spu_store_category (store_id, category_id)`

#### `commerce.product_sku`

核心字段：

- `spu_id`
- `sku_name`
- `sale_price`
- `stock_qty`
- `status`

#### `commerce.qr_code_asset`

核心字段：

- `driver_id`
- `scene`
- `product_id` 或 `campaign_id`
- `status`
- `short_link`
- `poster_url`
- `template_version`

#### `commerce.attribution_session`

| 字段 | 类型 | 约束 | 说明 |
|---|---|---|---|
| `attribution_session_id` | `uuid` | PK | 归因会话主键 |
| `qr_code_id` | `uuid` | FK | 来源二维码 |
| `driver_id` | `uuid` | FK | 引流司机 |
| `passenger_user_id` | `uuid` | FK | 乘客 |
| `effective_from` | `timestamp` | IDX | 生效开始 |
| `effective_to` | `timestamp` | IDX | 生效结束 |
| `status` | `varchar(32)` | IDX | `active`, `expired`, `closed` |

#### `commerce.mall_order`

| 字段 | 类型 | 约束 | 说明 |
|---|---|---|---|
| `order_id` | `uuid` | PK | 订单主键 |
| `buyer_user_id` | `uuid` | FK | 购买用户 |
| `attribution_session_id` | `uuid` | FK | 可为空 |
| `order_scene` | `varchar(32)` | IDX | `mall`, `driver-zone` |
| `fulfillment_type` | `varchar(32)` | IDX | 到店核销/自提/配送 |
| `order_status` | `varchar(32)` | IDX | `pending_pay`, `paid`, `fulfilling`, `completed`, `refunding`, `refunded` |
| `item_amount` | `numeric(14,2)` |  | 商品金额 |
| `coupon_deduction` | `numeric(14,2)` |  | 券抵扣 |
| `wallet_deduction` | `numeric(14,2)` |  | 站内余额抵扣 |
| `pay_amount` | `numeric(14,2)` |  | 实付金额 |

索引建议：

- `idx_mall_order_buyer_scene_status (buyer_user_id, order_scene, order_status)`
- `idx_mall_order_attr_session (attribution_session_id)`

### 5.3 `ride` Schema

| 数据 ID | 表名 | 主键 | 说明 |
|---|---|---|---|
| `DATA-016` | `ride_order` | `ride_order_id` | 行程订单 |
| `DATA-017` | `ride_timeline` | `timeline_id` | 行程时间线 |
| `DATA-018` | `ride_pricing_snapshot` | `snapshot_id` | 价格快照 |
| `DATA-019` | `dispatch_request` | `dispatch_request_id` | 派单请求 |
| `DATA-020` | `dispatch_attempt` | `dispatch_attempt_id` | 派单尝试与响应 |

#### `ride.ride_order`

关键字段：

- `ride_order_id`
- `passenger_user_id`
- `referral_driver_id`
- `fulfillment_driver_id`
- `city_code`
- `origin_lng`, `origin_lat`, `origin_address`
- `destination_lng`, `destination_lat`, `destination_address`
- `order_status`
- `estimated_amount`
- `final_amount`
- `cancel_reason`

索引建议：

- `idx_ride_order_passenger_status (passenger_user_id, order_status)`
- `idx_ride_order_driver_status (fulfillment_driver_id, order_status)`
- `idx_ride_order_referral_driver (referral_driver_id)`

#### `ride.ride_timeline`

核心字段：

- `ride_order_id`
- `status`
- `occurred_at`
- `operator_role`
- `note`

#### `ride.dispatch_request`

核心字段：

- `ride_order_id`
- `dispatch_status`
- `candidate_count`
- `attempt_count`
- `first_dispatched_at`
- `last_dispatched_at`

#### `ride.dispatch_attempt`

核心字段：

- `dispatch_request_id`
- `driver_id`
- `attempt_no`
- `response_status`
- `responded_at`
- `timeout_at`

### 5.4 `finance` Schema

| 数据 ID | 表名 | 主键 | 说明 |
|---|---|---|---|
| `DATA-021` | `commission_rule` | `commission_rule_id` | 佣金规则 |
| `DATA-022` | `commission_ledger` | `commission_ledger_id` | 佣金台账 |
| `DATA-023` | `wallet_account` | `wallet_account_id` | 司机钱包账户 |
| `DATA-024` | `wallet_entry` | `wallet_entry_id` | 钱包分录 |
| `DATA-025` | `coupon_template` | `coupon_template_id` | 券模板 |
| `DATA-026` | `coupon_record` | `coupon_record_id` | 券实例 |
| `DATA-027` | `refund_record` | `refund_record_id` | 退款记录 |

#### `finance.commission_rule`

核心字段：

- `business_type`
- `city_code`
- `version_no`
- `status`
- `effective_from`
- `effective_to`
- `rule_snapshot`

#### `finance.commission_ledger`

| 字段 | 类型 | 约束 | 说明 |
|---|---|---|---|
| `commission_ledger_id` | `uuid` | PK | 台账主键 |
| `commission_rule_id` | `uuid` | FK | 命中规则 |
| `driver_id` | `uuid` | FK | 收益司机 |
| `source_type` | `varchar(32)` | IDX | `mall-order`, `ride-order` |
| `source_id` | `uuid` | IDX | 来源业务单 |
| `ledger_status` | `varchar(32)` | IDX | `pending`, `posted`, `reversed` |
| `amount` | `numeric(14,2)` |  | 金额 |
| `rule_snapshot` | `jsonb` |  | 规则快照 |

#### `finance.wallet_account`

关键字段：

- `driver_id`
- `balance_status`
- `current_balance`
- `negative_balance_amount`
- `last_reconciled_at`

说明：

- `current_balance` 为查询缓存字段。
- 账务真相以 `wallet_entry` 聚合为准。

#### `finance.wallet_entry`

关键字段：

- `wallet_account_id`
- `commission_ledger_id`
- `refund_record_id`
- `entry_type`
- `direction`
- `amount`
- `source_type`
- `source_id`
- `idempotency_key`

索引建议：

- `uk_wallet_entry_idempotency (idempotency_key)`
- `idx_wallet_entry_account_created (wallet_account_id, created_at desc)`

#### `finance.coupon_record`

关键字段：

- `coupon_template_id`
- `driver_id`
- `coupon_status`
- `issued_from_source_type`
- `issued_from_source_id`
- `face_value`
- `used_order_id`
- `expires_at`

#### `finance.refund_record`

关键字段：

- `biz_type`
- `biz_id`
- `refund_status`
- `refund_amount`
- `operator_id`
- `reason`
- `settlement_snapshot`

### 5.5 `support` Schema

| 数据 ID | 表名 | 主键 | 说明 |
|---|---|---|---|
| `DATA-028` | `support_ticket` | `ticket_id` | 工单 |
| `DATA-029` | `support_ticket_timeline` | `ticket_timeline_id` | 工单流转 |
| `DATA-030` | `support_ticket_attachment` | `attachment_id` | 工单附件 |

#### `support.support_ticket`

核心字段：

- `biz_type`
- `biz_id`
- `ticket_type`
- `ticket_status`
- `creator_user_id`
- `creator_role`
- `assignee_role`
- `assignee_user_id`
- `resolution_summary`

#### `support.support_ticket_timeline`

核心字段：

- `ticket_id`
- `action_type`
- `operator_role`
- `operator_id`
- `note`
- `occurred_at`

### 5.6 `message` Schema

| 数据 ID | 表名 | 主键 | 说明 |
|---|---|---|---|
| `DATA-031` | `message_task` | `message_task_id` | 消息发送任务 |
| `DATA-032` | `message_inbox` | `inbox_message_id` | 站内消息 |

核心字段：

- `message_task.template_code`
- `message_task.channel`
- `message_task.send_status`
- `message_inbox.receiver_user_id`
- `message_inbox.receiver_role`
- `message_inbox.is_read`

### 5.7 `risk` Schema

| 数据 ID | 表名 | 主键 | 说明 |
|---|---|---|---|
| `DATA-033` | `risk_event` | `risk_event_id` | 风险事件 |
| `DATA-034` | `audit_log` | `audit_log_id` | 审计日志 |

核心字段：

- `risk_event.event_type`
- `risk_event.risk_level`
- `risk_event.event_status`
- `audit_log.operator_id`
- `audit_log.action_type`
- `audit_log.biz_object_type`
- `audit_log.biz_object_id`
- `audit_log.before_snapshot`
- `audit_log.after_snapshot`

### 5.8 `ops` 与 `analytics` Schema

| 数据 ID | 表名 | 主键 | 说明 |
|---|---|---|---|
| `DATA-035` | `city_config` | `city_config_id` | 城市配置 |
| `DATA-036` | `attribution_rule` | `attribution_rule_id` | 归因规则 |
| `DATA-037` | `rbac_role` | `role_id` | 角色 |
| `DATA-038` | `rbac_permission` | `permission_id` | 权限 |
| `DATA-039` | `rbac_binding` | `binding_id` | 账号与角色绑定 |
| `DATA-040` | `metric_snapshot_hourly` | `metric_snapshot_hourly_id` | 小时指标 |
| `DATA-041` | `metric_snapshot_daily` | `metric_snapshot_daily_id` | 日指标 |

核心字段：

- `attribution_rule.rule_type`
- `attribution_rule.version_no`
- `rbac_permission.scope_level`
- `metric_snapshot_hourly.metric_code`
- `metric_snapshot_hourly.snapshot_at`

## 6. 核心关系约束

- `user_account 1 -> n user_role`
- `user_account 1 -> 1 driver_profile`
- `driver_profile 1 -> n qr_code_asset`
- `attribution_session 1 -> n mall_order`
- `attribution_session 1 -> n ride_order`
- `ride_order 1 -> n ride_timeline`
- `ride_order 1 -> n dispatch_request`
- `dispatch_request 1 -> n dispatch_attempt`
- `wallet_account 1 -> n wallet_entry`
- `commission_ledger 1 -> n wallet_entry`
- `support_ticket 1 -> n support_ticket_timeline`

## 7. 状态与一致性规则

- 商城订单和行程订单都只有支付成功后才能触发资金入账。
- 退款必须先生成 `refund_record`，再由 `wallet_entry` 和 `commission_ledger` 做逆向分录。
- 负余额状态由 `wallet_account.balance_status` 标记，但金额以 `wallet_entry` 聚合校验。
- 司机专区订单的履约模式一旦下单，不允许后台直接改成另一种履约模式。

## 8. 分区、归档与保留策略

- `ride_timeline`, `dispatch_attempt`, `audit_log` 可按月分区。
- `metric_snapshot_hourly` 保留 180 天，汇总后沉到 `metric_snapshot_daily`。
- `message_task` 和 `message_inbox` 可按 90 天归档。
- 资金、退款、审计类表不做物理删除，只做逻辑状态变更。

## 9. 数据质量与审计要求

- 所有账务表必须保存 `request_id`、`idempotency_key`、`operator_id`。
- 所有后台高风险动作必须写 `risk.audit_log`。
- 退款与工单结论必须可追溯到原订单、原台账和操作人。

## 10. 变更记录

| 日期 | 变更内容 | 变更人 |
|---|---|---|
| 2026-03-29 | 初始版本 | AI 软件工厂 |
| 2026-03-29 | 扩展为多 schema 详细设计，补充表结构、索引、状态与保留策略 | AI 软件工厂 |
