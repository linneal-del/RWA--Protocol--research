# PRD 要点（数据侧视角）

> 来源：Confluence pageId=594744395「[PRD] RWA 理财」v81（2026-07-31 快照）
> 本页只抽取**与协议调研 / 数据接入相关**的部分。前端交互细节（卡片样式、按钮态、键盘）请看 PRD 原文。

## 一、协议调研需要产出什么（→ 决定本仓库调研模版）

RWA 详情页 **Overview Tab** 有四个子 Tab，内容全部由**管理后台配置**，也就是需要 PM 侧提供文案：

| Tab | 内容 | 本仓库对应章节 |
|-----|------|--------------|
| **Underlying** | 底层资产信息 | 各协议文档「3. 底层资产」 |
| **Yield Source** | 收益来源 | 各协议文档「4. 收益来源」 |
| **Risk** | 风险披露（默认上下结构列表） | 各协议文档「7. 风险」 |
| **Compliance** | 合规信息 | 各协议文档「8. 合规与准入」 |

此外 **Basic Info** 需要：协议、池子 TVL（实时 & 历史）、质押代币 & 凭证代币（含**合约地址**，弹窗展示）、Transaction Rules（锁仓期 / 赎回等待期 / 质押总上限 / 最小质押额 / 各项费用）。

> ⚠️ PRD 明确标注 TODO：**动态字段（基金经理、持仓明细）不适合配置化，可能需按协议单独开发** —— 调研时要判断该协议是否有此类字段。

## 二、后台配置字段（RWA 专属）

- **Is RWA**（关联投资品 ID，多选）：条件是 Receipt Token 只有一个（共用）、From Token 不同、Underlying Token 相同
  → **调研含义**：一个协议若支持多种申购币种（如 USDT / USDC 都能进同一 vault），要标清 From Token 列表
- **底层资产类型**（单选）：Private Credit / Equities / Bonds / Structured Products
  → ⚠️ 后台枚举只有 4 类，但 Master 表里出现了 **Reinsurance / Gold / TBills / Money Market Fund / Stocks**，映射关系需和 Marcus 对齐（见各协议文档「9. 待确认」）
- **期限类型**：Locked（+锁定天数 N）/ Flexible
- **产品参数**：赎回处理时间、申购费、赎回费、管理费、分发方式（Auto-compound / At maturity / Continuous）、到期日
  → **调研含义**：分发方式直接决定 NAV 还是 balance 变化，是解析口径的关键

## 三、六个数据维度（本仓库每个协议都要自评）

来自 `8月待冲刺RWA协议列表.md`「项目管理」表：

| 维度 | 定义 | 依赖的链上/链下能力 |
|------|------|------------------|
| **实时解析** | 链上实时持仓数据解析 | 凭证代币 balance + NAV 换算 |
| **交易历史解析** | 历史交易记录（Stake / Redeem） | 事件日志（Deposit/Withdraw/RedeemRequest） |
| **池子自发现** | 池子自动发现能力 | Factory 合约 / 统一注册表 |
| **APY** | 收益率数据接入 | 链上算 or 项目方 API |
| **NAV 历史** | NAV 天级快照 + 历史回溯 | `convertToAssets` 天级打点；历史需回溯 |
| **PNL** | 持仓盈亏 | 天级快照聚合（Jackson 侧算，中台供原始数据） |

## 四、解析口径的三类协议（调研时必须分类）

这是本次调研中总结出的**最影响接入成本**的分类：

| 类型 | 机制 | 持仓解析 | NAV 曲线 | 典型协议 |
|------|------|---------|---------|---------|
| **A. NAV 累积型**（ERC-4626 / share 价格上涨） | balance 不变，`convertToAssets` 上升 | balance × NAV | 直接从合约取，天级打点 | Ember、Nest nOPAL、Unitas XGLD、sUSDai、ONyc、Theo sthUSD、Midas mfONE、XAUE |
| **B. Rebasing / balance 增长型** | share 价格恒定，balance 增长 | 直接读 balance | **NAV 恒为 1，曲线无意义** | Ondo rUSDY、Ondo GM 在 Solana 侧 |
| **C. 现金流分发型**（定期派息） | 周期性发 USDT 到钱包 | balance + 派息记录 | NAV 平，收益在派息流水里 | DMZ rAI（每周 USDT 派息） |

> **对 PRD 的直接影响**：PRD 核心指标栏 Tab 是「NAV / APY / 派息信息」三选，正好对应上面三类。B 类不要配 NAV Tab，C 类必须配「派息信息」Tab。

## 五、赎回链路（决定「赎回中」状态能不能做）

PRD 4.6.2 要求返回 **request ID + 交易结果状态**。调研发现协议赎回机制差异极大：

| 赎回模式 | 说明 | 影响 |
|---------|------|------|
| 即时赎回 | 池内有流动性即到账 | 无需 request ID，无「赎回中」态 |
| T+N 结算 | 如 OpenTrade 同日 / T+4 | 需要 request ID 跟踪 |
| **Epoch / 队列** | 如 sUSDai 30 天 epoch + FIFO 队列 | 队列可能跨多个 epoch，「赎回中」可能持续数月 |
| **季度赎回窗口** | 如 Re reUSDe、OnRe ONyc（季度 + 30 天通知期） | 前端需展示「下一个赎回窗口」，PRD 目前无此字段 ⚠️ |

> Scott 的结论是「只返回交易成功/失败状态可行，关联最终赎回结果需重构系统」——上面这张表说明：**季度赎回窗口类协议若不关联最终结果，用户体验会很差**，建议一期优先选即时/T+N 类协议。

## 六、PRD 待确认事项（数据侧相关）

| # | 事项 | 负责人 |
|---|------|--------|
| 1 | NAV 历史数据回溯方案（天级打点能跑多久历史） | Scott / Linnea |
| 2 | Total Earnings 聚合计算（RWA + 普通投资品共用） | Jackson / Calvin |
| 3 | Unstake 状态粒度（只返回成功/失败 vs 关联最终赎回结果） | Scott + Johnson |
| 4 | Invested 取值（周期内最高 balance + tooltip） | Marcus |
| 5 | 多期产品 Earned 计算（按时间段从天级快照聚合） | Marcus |
| 6 | 字段对接清单表格（页面字段 × 数据来源 × 解析方式） | Marcus |
| 7 | Tech Design 会议逐字段过 | Selina |
| 8 | 提前给投资品 ID 列表用于代币价格配置 | Marcus → Scott |
| 9 | 特殊协议 Tax History 需向项目方要 API（非 Huma 通用方案） | Jaden |
| 10 | Portfolio PnL 未启动，时间段 PnL 依赖天级快照 | Jackson |
