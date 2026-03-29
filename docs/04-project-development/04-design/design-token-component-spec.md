# 设计令牌与组件规范

**项目名称：** 千乘坊（ride-loop）  
**文档状态：** 草稿  
**负责人：** AI 软件工厂  
**主要读者：** 交互设计师 | 视觉设计师 | 前端 | 产品  
**上游输入：** UX/UI 与交互设计 | 页面级详细设计矩阵  
**下游输出：** Figma/即时设计变量库 | 组件库 | 页面高保真稿  
**最后更新：** 2026-03-29  

## 1. 使用目标

- 把页面描述进一步收敛为“可复制到设计软件”的设计令牌、组件和状态规范。
- 让不同端在视觉上有区分，但令牌命名和组件行为保持同一套规则。
- 让前端在后续实现时能直接按变量和组件语义落地，而不是二次猜测样式。

## 2. 令牌命名规则

- 颜色：`color.<端>.<用途>.<层级>`
- 字体：`font.<端>.<文本类型>`
- 间距：`space.<端>.<级别>`
- 圆角：`radius.<端>.<级别>`
- 阴影：`shadow.<端>.<级别>`
- 组件：`cmp.<端>.<组件名>.<状态>`
- 状态标签：`state.<语义>`

示例：

- `color.pax.primary.600`
- `font.driverHeavy.title.lg`
- `cmp.ops.button.primary.disabled`

## 3. 全端共享基础令牌

### 3.1 中性色

| 令牌 | 值 | 说明 |
|---|---|---|
| `color.shared.neutral.0` | `#FFFFFF` | 纯白卡片底 |
| `color.shared.neutral.50` | `#F8FAFC` | 弱背景 |
| `color.shared.neutral.100` | `#F1F5F9` | 容器底 |
| `color.shared.neutral.200` | `#E2E8F0` | 分割线 |
| `color.shared.neutral.400` | `#94A3B8` | 次级说明 |
| `color.shared.neutral.600` | `#475569` | 次主文本 |
| `color.shared.neutral.800` | `#1E293B` | 主文本 |
| `color.shared.neutral.950` | `#020617` | 深色场景极深背景 |

### 3.2 语义色

| 令牌 | 值 | 用途 |
|---|---|---|
| `state.success` | `#059669` | 成功、已完成 |
| `state.warning` | `#D97706` | 待处理、临期、负余额提示 |
| `state.danger` | `#DC2626` | 驳回、失败、危险操作 |
| `state.info` | `#2563EB` | 信息提醒、系统说明 |
| `state.disabled` | `#94A3B8` | 禁用态 |

### 3.3 间距与圆角

| 令牌 | 值 |
|---|---|
| `space.shared.2` | `8px` |
| `space.shared.3` | `12px` |
| `space.shared.4` | `16px` |
| `space.shared.5` | `20px` |
| `space.shared.6` | `24px` |
| `radius.shared.sm` | `8px` |
| `radius.shared.md` | `12px` |
| `radius.shared.lg` | `16px` |
| `radius.shared.xl` | `20px` |

## 4. 端级视觉令牌

### 4.1 乘客微信小程序

| 类别 | 令牌 | 值 | 说明 |
|---|---|---|---|
| 主色 | `color.pax.primary.600` | `#0F766E` | 主 CTA、价格强调线 |
| 主色深层 | `color.pax.primary.700` | `#115E59` | 导航与重要标题 |
| 强提醒 | `color.pax.accent.500` | `#F97316` | 优惠金额、主要活动 |
| 背景 | `color.pax.bg.canvas` | `#ECFDF5` | 页面底 |
| 卡片 | `color.pax.bg.card` | `#FFFFFF` | 商品卡与订单卡 |
| 主文本 | `color.pax.text.primary` | `#134E4A` | 信任感较强的深青绿文本 |
| 次文本 | `color.pax.text.secondary` | `#52796F` | 说明文本 |

| 字体令牌 | 建议 |
|---|---|
| `font.pax.title.lg` | `22/30, 600` |
| `font.pax.title.md` | `18/26, 600` |
| `font.pax.body.md` | `14/22, 400` |
| `font.pax.caption` | `12/18, 400` |
| `font.pax.number.amount` | `24/30, 700` |

### 4.2 司机轻端微信小程序

| 类别 | 令牌 | 值 | 说明 |
|---|---|---|---|
| 主色 | `color.driverLight.primary.600` | `#047857` | 收益、主按钮 |
| 主色深层 | `color.driverLight.primary.700` | `#065F46` | 重点卡片标题 |
| 辅助强调 | `color.driverLight.accent.500` | `#F59E0B` | 临期券、待办、负余额提示 |
| 背景 | `color.driverLight.bg.canvas` | `#F7FEE7` | 工作台弱底色 |
| 区块底 | `color.driverLight.bg.block` | `#ECFCCB` | 区块型卡片底 |
| 主文本 | `color.driverLight.text.primary` | `#14532D` | 收益视图主文本 |
| 次文本 | `color.driverLight.text.secondary` | `#4D7C0F` | 规则与辅助说明 |

| 字体令牌 | 建议 |
|---|---|
| `font.driverLight.title.lg` | `22/30, 600` |
| `font.driverLight.title.md` | `18/26, 600` |
| `font.driverLight.body.md` | `14/22, 400` |
| `font.driverLight.caption` | `12/18, 400` |
| `font.driverLight.number.amount` | `26/32, 700` |

### 4.3 司机重端 Android App

| 类别 | 令牌 | 值 | 说明 |
|---|---|---|---|
| 背景 | `color.driverHeavy.bg.canvas` | `#0B1220` | 主背景 |
| 次级面板 | `color.driverHeavy.bg.panel` | `#111827` | 浮层与卡片 |
| 主动作 | `color.driverHeavy.primary.500` | `#14B8A6` | 上线、接单、确认类动作 |
| 强提醒 | `color.driverHeavy.accent.500` | `#F97316` | 倒计时、提示 |
| 危险色 | `color.driverHeavy.danger.500` | `#E11D48` | 拒单、异常、取消 |
| 主文本 | `color.driverHeavy.text.primary` | `#E5E7EB` | 深色模式主文本 |
| 次文本 | `color.driverHeavy.text.secondary` | `#94A3B8` | 说明与标签 |

| 字体令牌 | 建议 |
|---|---|
| `font.driverHeavy.title.lg` | `24/30, 700` |
| `font.driverHeavy.title.md` | `18/24, 600` |
| `font.driverHeavy.body.md` | `14/20, 500` |
| `font.driverHeavy.caption` | `12/18, 400` |
| `font.driverHeavy.number.amount` | `28/34, 700` |

### 4.4 运营 Web

| 类别 | 令牌 | 值 | 说明 |
|---|---|---|---|
| 主色 | `color.ops.primary.700` | `#1E3A8A` | 导航、主按钮、KPI 标题 |
| 主色浅层 | `color.ops.primary.100` | `#DBEAFE` | 筛选标签、信息底色 |
| 辅助强调 | `color.ops.accent.500` | `#F59E0B` | 待办、预警 |
| 背景 | `color.ops.bg.canvas` | `#F8FAFC` | 页面底 |
| 卡片 | `color.ops.bg.card` | `#FFFFFF` | 表格、抽屉、图表卡 |
| 主文本 | `color.ops.text.primary` | `#0F172A` | 标题与正文 |
| 次文本 | `color.ops.text.secondary` | `#475569` | 次级说明 |

| 字体令牌 | 建议 |
|---|---|
| `font.ops.title.lg` | `24/32, 600` |
| `font.ops.title.md` | `18/26, 600` |
| `font.ops.body.md` | `14/22, 400` |
| `font.ops.caption` | `12/18, 400` |
| `font.ops.number.amount` | `28/34, 700` |

## 5. 共享组件规范

### 5.1 按钮

| 组件 | 必备状态 | 设计规则 |
|---|---|---|
| 主按钮 | 默认、按下、加载、禁用 | 主按钮宽度优先占满可操作区域；司机重端按钮高度不低于 `52dp` |
| 次按钮 | 默认、按下、禁用 | 用于取消、查看详情、次优路径 |
| 危险按钮 | 默认、确认中、禁用 | 只用于驳回、取消、退款、拒单等动作 |
| 权限受控按钮 | 可见可点、可见禁用、不可见 | 运营 Web 高风险动作必须支持按钮级权限表现 |

### 5.2 卡片

| 组件 | 使用端 | 设计规则 |
|---|---|---|
| 优惠卡 | 乘客端 | 金额、有效期、规则入口必须同屏可见 |
| 收益卡 | 司机轻端 | 今日收益、待入账、可用余额固定顺序 |
| 派单卡 | 司机重端 | 倒计时、起点、预估收入、主操作按钮必须一屏可见 |
| KPI 卡 | 运营 Web | 数值、同比/环比、更新时间三层结构固定 |

### 5.3 列表与表格

| 组件 | 使用端 | 设计规则 |
|---|---|---|
| 商品列表卡 | 乘客端、司机轻端 | 商品图、标题、价格、履约方式、状态标签固定顺序 |
| 订单时间线 | 乘客端、司机重端、运营 Web | 当前状态节点高亮，历史节点弱化 |
| 后台表格 | 运营 Web | 顶部筛选栏、批量区、表格、分页器四段式结构固定 |

### 5.4 表单

| 表单类型 | 设计规则 |
|---|---|
| 入驻资料表单 | 必须按步骤分段，不用超长整页 |
| 司机专区结算表单 | 随履约方式动态切换字段组 |
| 审核/退款/干预弹窗 | 原因输入框必显，且原因区不允许被折叠 |
| 申诉表单 | 异常类型优先单选，说明为补充项，附件可选但应强提醒 |

## 6. 状态与反馈规范

### 6.1 页面状态

- 默认态
- 加载态
- 空态
- 无结果态
- 异常态
- 禁用态
- 无权限态

### 6.2 负余额展示规范

| 端 | 组件建议 | 展示原则 |
|---|---|---|
| 司机轻端 | 顶部说明卡 + 台账解释卡 | 强调“待后续收益冲抵”，不用系统错误样式 |
| 运营 Web | 余额侧卡 + 列表高亮标签 | 要展示缺口金额、最近冲抵时间、关联逆分录 |

### 6.3 高风险动作反馈

| 动作类型 | 反馈规则 |
|---|---|
| 退款审核 | 二次确认弹窗 + 原因输入 + 影响摘要 |
| 调度干预 | 二次确认弹窗 + 目标司机/区域确认 |
| 驳回审核 | 理由模板 + 手填原因 |
| 权限变更 | 高亮警示条 + 二次确认 + 审计提示 |

## 7. 设计软件出图约束

- 所有页面稿必须携带页面 ID，例如 `UI-DRV-H-004`。
- 所有关键组件必须在设计稿里标注状态名称，而不是只靠颜色暗示。
- 需要出图的弹窗、抽屉、全屏态视为独立稿件，不作为“备注说明”处理。
- 司机重端要同时给出日间强光可见性检查稿和夜间主稿。
- 运营 Web 列表页至少输出：默认列表、筛选后无结果、无权限按钮、抽屉展开四个视图。

## 8. 设计软件负向提示词

以下内容应明确排除：

- 不要使用紫色主色和 AI 常见霓虹渐变风格。
- 不要把司机轻端做成接单应用。
- 不要在司机重端加入商城运营横幅、收益大卡或复杂动效。
- 不要把运营 Web 做成大屏可视化海报。
- 不要让高风险按钮与普通按钮拥有同等视觉权重。

## 9. 交付清单

- 变量库：颜色、字体、间距、圆角、阴影。
- 组件库：按钮、卡片、列表项、时间线、表格、弹窗、状态标签。
- 页面稿：按 [页面级详细设计矩阵](./ui-page-detail-matrix.md) 输出。
- 审稿清单：按 [设计软件提示词包](./design-tool-prompt-pack.md) 和逐页提示词执行回查。

## 10. 变更记录

| 日期 | 变更内容 | 变更人 |
|---|---|---|
| 2026-03-29 | 初始版本 | AI 软件工厂 |
