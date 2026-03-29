# 数据库 ER 图

**项目名称：** 千乘坊（ride-loop）  
**文档状态：** 草稿  
**负责人：** AI 软件工厂  
**主要读者：** 架构 | 后端 | DBA | 测试  
**上游输入：** 数据设计 | 系统详细设计 | 接口与契约设计  
**下游输出：** 迁移设计 | 仓储实现 | 对账校验  
**最后更新：** 2026-03-29  

## 1. Schema 划分

- `identity`：账户、角色、司机档案、司机准入
- `commerce`：商品、二维码、归因会话、商城/司机专区订单
- `ride`：叫车订单、时间线、派单请求、派单尝试
- `finance`：佣金规则、钱包、券、退款
- `support`：工单与申诉
- `message`：消息任务与站内收件箱
- `risk`：风险事件与审计日志
- `ops`：城市配置、归因规则、RBAC
- `analytics`：指标快照与聚合视图

## 2. 账户与司机准入域

```mermaid
erDiagram
    user_account ||--o{ user_role : has
    user_account ||--o| driver_profile : extends
    driver_profile ||--o{ driver_certification : owns
    driver_profile ||--o{ driver_vehicle : owns
    driver_profile ||--o{ driver_device : binds
    driver_profile ||--o{ driver_access_application : submits

    user_account {
      uuid user_id PK
      string mobile
      string nickname
      string status
      string city_code
    }
    user_role {
      uuid user_role_id PK
      uuid user_id FK
      string role_code
      string status
    }
    driver_profile {
      uuid driver_id PK
      uuid user_id FK
      string capability_status
      string city_code
      string audit_status
    }
    driver_certification {
      uuid certification_id PK
      uuid driver_id FK
      string cert_type
      string cert_no
      string verify_status
    }
    driver_vehicle {
      uuid vehicle_id PK
      uuid driver_id FK
      string plate_no
      string vehicle_type
      string status
    }
    driver_device {
      uuid device_binding_id PK
      uuid driver_id FK
      string device_id
      string bind_status
    }
    driver_access_application {
      uuid application_id PK
      uuid driver_id FK
      string submit_status
      string review_status
    }
```

## 3. 商品、归因与商城域

```mermaid
erDiagram
    merchant_store ||--o{ product_spu : provides
    product_category ||--o{ product_spu : classifies
    product_spu ||--o{ product_sku : contains
    driver_profile ||--o{ qr_code_asset : owns
    qr_code_asset ||--o{ attribution_session : opens
    attribution_session ||--o{ mall_order : attributes
    product_spu ||--o{ mall_order_item : referenced
    mall_order ||--o{ mall_order_item : contains

    merchant_store {
      uuid store_id PK
      string store_name
      string fulfillment_type
      string city_code
    }
    product_category {
      uuid category_id PK
      string category_name
      uuid parent_id
    }
    product_spu {
      uuid spu_id PK
      uuid store_id FK
      uuid category_id FK
      string scene
      string fulfillment_type
      string status
    }
    product_sku {
      uuid sku_id PK
      uuid spu_id FK
      string sku_name
      decimal sale_price
      integer stock_qty
    }
    qr_code_asset {
      uuid qr_code_id PK
      uuid driver_id FK
      string scene
      string status
      string short_link
    }
    attribution_session {
      uuid attribution_session_id PK
      uuid qr_code_id FK
      uuid driver_id FK
      uuid passenger_user_id FK
      datetime effective_from
      datetime effective_to
    }
    mall_order {
      uuid order_id PK
      uuid attribution_session_id FK
      uuid buyer_user_id FK
      string order_scene
      string order_status
      decimal pay_amount
    }
    mall_order_item {
      uuid order_item_id PK
      uuid order_id FK
      uuid spu_id FK
      uuid sku_id FK
      integer quantity
      decimal item_amount
    }
```

## 4. 出行与调度域

```mermaid
erDiagram
    ride_order ||--o{ ride_timeline : has
    ride_order ||--o| ride_pricing_snapshot : snapshots
    ride_order ||--o{ dispatch_request : creates
    dispatch_request ||--o{ dispatch_attempt : tries
    driver_profile ||--o{ dispatch_attempt : receives

    ride_order {
      uuid ride_order_id PK
      uuid passenger_user_id FK
      uuid referral_driver_id FK
      uuid fulfillment_driver_id FK
      string city_code
      string order_status
      decimal final_amount
    }
    ride_timeline {
      uuid timeline_id PK
      uuid ride_order_id FK
      string status
      datetime occurred_at
      string operator_role
    }
    ride_pricing_snapshot {
      uuid snapshot_id PK
      uuid ride_order_id FK
      decimal estimated_amount
      decimal final_amount
      string pricing_rule_version
    }
    dispatch_request {
      uuid dispatch_request_id PK
      uuid ride_order_id FK
      string dispatch_status
      integer attempt_count
    }
    dispatch_attempt {
      uuid dispatch_attempt_id PK
      uuid dispatch_request_id FK
      uuid driver_id FK
      string response_status
      datetime responded_at
    }
```

## 5. 资金、工单、消息与风控域

```mermaid
erDiagram
    commission_rule ||--o{ commission_ledger : applies
    wallet_account ||--o{ wallet_entry : has
    coupon_template ||--o{ coupon_record : issues
    coupon_record ||--o{ coupon_transfer_record : transfers
    commission_ledger ||--o{ wallet_entry : settles
    refund_record ||--o{ wallet_entry : reverses
    support_ticket ||--o{ support_ticket_timeline : logs
    support_ticket ||--o{ support_ticket_attachment : owns
    message_task ||--o{ message_inbox : delivers
    risk_event ||--o{ audit_log : references

    commission_rule {
      uuid commission_rule_id PK
      string business_type
      string city_code
      string status
      string version_no
    }
    commission_ledger {
      uuid commission_ledger_id PK
      uuid commission_rule_id FK
      string source_type
      uuid source_id
      uuid driver_id FK
      string ledger_status
      decimal amount
    }
    wallet_account {
      uuid wallet_account_id PK
      uuid driver_id FK
      string balance_status
      decimal current_balance
    }
    wallet_entry {
      uuid wallet_entry_id PK
      uuid wallet_account_id FK
      uuid commission_ledger_id FK
      uuid refund_record_id FK
      string entry_type
      string direction
      decimal amount
    }
    coupon_template {
      uuid coupon_template_id PK
      string scene
      decimal face_value
      string status
    }
    coupon_record {
      uuid coupon_record_id PK
      uuid coupon_template_id FK
      uuid driver_id FK
      uuid original_driver_id FK
      string coupon_status
      string transfer_status
      datetime expires_at
    }
    coupon_transfer_record {
      uuid coupon_transfer_id PK
      uuid coupon_record_id FK
      uuid sender_driver_id FK
      uuid recipient_driver_id FK
      string transfer_method
      string transfer_status
      datetime lock_expires_at
    }
    refund_record {
      uuid refund_record_id PK
      string biz_type
      uuid biz_id
      string refund_status
      decimal refund_amount
    }
    support_ticket {
      uuid ticket_id PK
      string biz_type
      uuid biz_id
      string ticket_status
      string creator_role
    }
    support_ticket_timeline {
      uuid ticket_timeline_id PK
      uuid ticket_id FK
      string action_type
      datetime occurred_at
      string operator_role
    }
    support_ticket_attachment {
      uuid attachment_id PK
      uuid ticket_id FK
      string attachment_url
    }
    message_task {
      uuid message_task_id PK
      string template_code
      string channel
      string send_status
    }
    message_inbox {
      uuid inbox_message_id PK
      uuid message_task_id FK
      uuid receiver_user_id FK
      boolean is_read
    }
    risk_event {
      uuid risk_event_id PK
      string event_type
      string risk_level
      string event_status
    }
    audit_log {
      uuid audit_log_id PK
      uuid risk_event_id FK
      string operator_id
      string action_type
      datetime created_at
    }
```

## 6. 运营与分析域

```mermaid
erDiagram
    city_config ||--o{ attribution_rule : owns
    rbac_role ||--o{ rbac_permission : grants
    rbac_role ||--o{ rbac_binding : binds
    metric_snapshot_hourly ||--o{ metric_snapshot_daily : rolls_up

    city_config {
      uuid city_config_id PK
      string city_code
      string city_name
      string status
    }
    attribution_rule {
      uuid attribution_rule_id PK
      string city_code
      string rule_type
      string version_no
      string status
    }
    rbac_role {
      uuid role_id PK
      string role_code
      string role_name
      string status
    }
    rbac_permission {
      uuid permission_id PK
      uuid role_id FK
      string permission_code
      string scope_level
    }
    rbac_binding {
      uuid binding_id PK
      uuid role_id FK
      uuid user_id FK
      string status
    }
    metric_snapshot_hourly {
      uuid metric_snapshot_hourly_id PK
      string city_code
      string metric_code
      datetime snapshot_at
      decimal metric_value
    }
    metric_snapshot_daily {
      uuid metric_snapshot_daily_id PK
      string city_code
      string metric_code
      date snapshot_date
      decimal metric_value
    }
```

## 7. 说明

- `wallet_account.current_balance` 是缓存字段，真实余额以 `wallet_entry` 聚合为准。
- `mall_order.order_scene` 区分普通商城与司机专区。
- `ride_order.referral_driver_id` 和 `ride_order.fulfillment_driver_id` 允许不同。
- `audit_log` 既用于合规审计，也为后台动作级权限操作提供责任链。

## 8. 变更记录

| 日期 | 变更内容 | 变更人 |
|---|---|---|
| 2026-03-29 | 初始版本 | AI 软件工厂 |
| 2026-03-29 | 补充多域 ER 图与跨域关系 | AI 软件工厂 |
| 2026-03-29 | 补充返现券转赠实体与资金域关系 | AI 软件工厂 |
