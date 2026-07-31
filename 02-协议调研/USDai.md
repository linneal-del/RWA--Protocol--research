# USD.AI — sUSDai（GPU / AI 算力设备抵押贷）

> **状态**：🟡 待确认
> **调研时间**：2026-07-31 ｜ **解析类型**：A（NAV 累积，汇率上升）
> **调研质量**：高（官方 docs + CoinDesk + 独立研究报告）
> ⚠️ **本协议赎回机制是全表最复杂的（30 天 epoch + FIFO 队列），是「赎回中」状态的最强测试用例**

## 0. 一句话结论

USD.AI 把 DeFi 资金变成**给 AI 算力商买 GPU 的设备融资贷**，用户存稳定币铸 USDai、再质押成 sUSDai 吃 10–17% 收益；代价是**流动性**——sUSDai 赎回走 **30 天 epoch + FIFO 队列**，高利用率时**可能跨多个 epoch**，协议明确"**不会为满足赎回而提前清算贷款**"。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | USDai（USD.AI） |
| 链 | **ARB（Arbitrum）** ⚠️ 公开资料未明确确认，见 9.1 |
| Supply Coins | USDai |
| Coins Integrated | sUSDai |
| 池子 TVL | 400M |
| 收益率 | 8%（官方 10–15% APR；CoinDesk 13–17%；Bankless 称质押最高 20%） |
| 类别 | Private Credit |
| 产品 | Staked USDai |
| 池子类别 | 活期 ⚠️ **与官方 30 天 epoch 赎回队列严重冲突，见 9.2** |
| 产品网页 | https://app.usd.ai/stake |
| DD 文档 | `[USDai] W3E DD` |
| 预算 / POC | $300,000 / Terry ｜ 执行：预算 |

## 2. 协议背景

- **三代币体系**：
  - **USDai** —— 全额背书的合成美元，**1:1 由代币化美债（M0 的 wM）抵押**，来自用户存入的 USDC/USDT，设计为近即时 1:1 可赎回
  - **sUSDai** —— 收益版（质押 USDai 得到）
  - **CHIP** —— 治理/功能代币，设定规则、风险参数、费用流与质押激励
- **借款方**：AI 基础设施运营商（neocloud、数据中心、算力供应商）申请融资买 GPU 硬件
- **贷款结构**：**无追索权（non-recourse）**，以**实体 GPU + 其合约现金流**担保，结构类似传统设备融资；**欺诈情形下对公司实体有 springing recourse**
- **放款四阶段**：托管账户注资（**Wilmington Trust** 托管）→ OEM 制造发货 → 硬件到数据中心安装测试验证 → **登记永久留置权（lien）**
- **设计哲学**：**oracleless（无预言机）** —— 用摊还计划、独立评估、结构化违约流程替代价格喂价
- **折旧口径保守**：GPU 按 **3 年折旧**（行业标准 5–7 年），因此起始 70% LTV **一年内可能降到约 40%**
- **DeFi 组合性**：**Fluid 承载 sUSDai 100% 的链上交易量**（2026-03 口径），其 Smart Debt 设计让借贷成本被交易费抵消，RWA 循环贷有效 APY 可再加 2%+
  → 📌 **交叉引用**：[Ember.md](Ember.md) 的循环贷标的之一就是 sUSDai

## 3. 底层资产（Underlying）

```
USDai 层：100% 代币化美债（M0 的 wM）→ 近即时 1:1 赎回
sUSDai 层：USDai 资金投入 GPU 抵押贷款账本（active loan book）
            + 闲置储备继续吃美债收益
```

**底层真实资产 = 英伟达 GPU 及 AI 数据中心设备 + 其合约化算力收入**。

## 4. 收益来源（Yield Source）

| 收益腿 | 说明 |
|--------|------|
| GPU 抵押贷利息（主） | 借款方（算力商）按摊还计划还本付息 |
| 闲置储备的美债收益 | 未放贷部分放美债 |

- **不靠代币激励**：CoinDesk 明确"收益来自 GPU 运营商的还款，不是排放（emissions）或杠杆循环"
- **收益表达**：**USDai / sUSDai 之间的汇率持续上升**，无需手动 claim → PRD 后台「分发方式」配 **Auto-compound**

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | sUSDai（质押 USDai 获得） |
| 合约地址 | ⚠️ 待补 |
| NAV | USDai↔sUSDai 汇率，**持续上升** |
| USDai 赎回 | 近即时 1:1（有美债背书） |
| **sUSDai 赎回** | 🔴 **30 天 epoch 周期 + FIFO 队列**；每个 epoch 结束时分配可用 USDai；**高利用率时队列可跨多个 epoch**；协议不会提前清算贷款来满足赎回 |
| 未来机制 | **QEV（Queue Extractable Value）** —— 拍卖式优先出队，付费加速，费用再分配给被动质押者 |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | ✅ | sUSDai balance × 汇率 | |
| 交易历史解析 | 🟡 | stake / unstake 事件 **+ 队列事件** | **赎回是三段式**：入队 → epoch 结算 → 到账，历史记录必须能表达"排队中" |
| 池子自发现 | 🟡 | 单一产品，可硬编码 | |
| APY | ✅ | 汇率差分自算 | 官方口径分散（8/10-15/13-17/20%），**必须自算** |
| NAV 历史 | ✅ | 天级打点汇率 | |
| PNL | ✅ | 天级快照差分 | Auto-compound，公式简单 |

### 6.2 关键取数口径 —— 🔴 赎回状态是重点

PRD 4.6.2 要求返回 **request ID + 交易结果状态**。本协议是**最需要 request ID 的协议**：

| 用户看到的状态 | 链上事实 | 我们要提供的字段 |
|--------------|---------|--------------|
| 已提交赎回 | 入队事件，拿到队列位置 | request ID + 队列位置 |
| 排队中 | 仍在 FIFO 队列（**可能 1–3+ 个月**） | 队列位置 + 预计 epoch |
| 部分到账 | epoch 结算时按可用额度**部分分配** | **部分成交金额** ⚠️ PRD 目前无部分赎回字段 |
| 全部到账 | 结算完成 | 完成状态 |

🔴 **这里暴露了 PRD 的一个缺口**：Scott 的结论是"只返回交易成功/失败状态"，但本协议赎回可能**部分成交 + 跨月排队**。若只返回"交易成功"，用户会以为钱到了。→ **建议一期不上 sUSDai，或前端明确展示队列信息**（见 9.2）

### 6.3 需向项目方索取

1. **链部署确认**（Arbitrum？Ethereum？）+ 合约地址
2. **赎回队列的链上事件定义**（入队 / 结算 / 部分成交）+ 队列位置查询接口
3. 当前 epoch 时间表与历史赎回等待时长统计
4. 历史汇率数据
5. QEV 上线时间（会改变赎回逻辑）
6. 贷款账本披露频率（详情页 Underlying Tab 若要展示持仓明细，属 PRD 标注的"动态字段"）

## 7. 风险（Risk）

1. 🔴 **流动性/赎回风险（首要）**：资金投入**不流动、摊还型**的 GPU 贷款，赎回走 30 天 epoch + FIFO；高利用率时**跨多个 epoch**；协议明确不会提前清算贷款
2. **GPU 资产快速折旧**：3 年折旧，LTV 从 70% 一年内可能降到 40%；GPU 技术迭代快（新一代发布会打击旧卡残值）
3. **借款人违约风险**：无追索权贷款，违约时只能处置 GPU；GPU 二手处置市场深度有限
4. **AI 资本开支周期风险**：AI 算力需求若转弱，借款人现金流与 GPU 残值双重恶化
5. **无预言机设计的双面性**：避免了喂价被操纵，但**估值依赖独立评估与摊还表**，透明度依赖协议自身披露
6. **sUSDai 明确承担协议核心资产负债风险**（官方原文）
7. **两层结构风险**：USDai 层安全（美债背书），sUSDai 层承担全部信贷风险 —— 文案上要区分清楚，不能让用户以为 sUSDai 也是"美债背书"

## 8. 合规与准入（Compliance）

- ⚠️ 公开信息不足。托管方 **Wilmington Trust**（美国信托机构）参与放款托管，说明有一定机构化架构
- 需以 `[USDai] W3E DD` 为准

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | 链部署（CSV 写 ARB，公开资料未确认） | Terry / 项目方 |
| 2 | 🔴 **CSV 标「活期」，但官方是 30 天 epoch + FIFO 队列（可跨月）** —— 池子类别定性、以及一期是否上线 | Marcus + Terry（**最高优先**） |
| 3 | 是否支持**部分赎回成交**，PRD 需不需要新增字段 | Marcus + Scott/Linnea |
| 4 | 收益率口径（8% vs 10–17%） | Terry |
| 5 | 队列位置能否链上查询（决定前端能否展示"预计到账"） | 项目方 |

## 10. 参考链接

- 官方文档：https://docs.usd.ai/ ｜ 运作机制：https://docs.usd.ai/usdai/how-it-works
- sUSDai 产品页：https://usd.ai/susdai ｜ 质押页：https://app.usd.ai/stake
- CoinDesk（收益来源 / 13-17%）：https://www.coindesk.com/markets/2025/10/24/usdai-bridges-defi-and-ai-by-turning-stablecoins-into-loans-for-nvidia-gpus
- 独立深度研究（风险 / epoch 队列 / 折旧）：https://www.stablewatch.io/research/usd-ai-deep-dive
- Bankless 解读：https://www.bankless.com/read/earning-from-gpus-with-usdai
- CHIP / 三代币体系：https://www.coinex.com/en/academy/detail/4307-what-is-usdai-chip-powering-ai-infrastructure-financing-with-tokenized-gpu-collateral
