# Ethena Vaults

> **状态**：🟡 待确认 —— **本行在 Master CSV 里几乎全空**（只有协议名、池子类别「活期」、DD「-」、预算「-」）
> **调研时间**：2026-07-31 ｜ **解析类型**：待定（取决于最终接的是哪个产品）
> 🔴 **本文档的结论是：这一行需要先明确"到底指什么产品"，否则无法调研**

## 0. 一句话结论

**"Ethena Vaults" 不是一个明确的产品名。** 公开资料里 **Ethena 官方并没有一个叫 "Vaults" 的对外发行产品** —— 搜索到的 "vault" 指的是 ① USDe 抵押品的**冷存储托管金库**，或 ② 第三方平台（Morpho / Kamino 等）上以 Ethena 资产为底层的 **ERC-4626 策展金库**。因此这一行需要先和 POC 确认指向，才能继续调研。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | Ethena Vaults |
| 链 | **空** |
| Supply Coins | **空** |
| Coins Integrated | **空** |
| 池子 TVL / 收益率 | **空 / 空** |
| 类别 | **空** |
| DD 文档 | **-** |
| 产品 / 产品描述 / 产品网页 | **空** |
| 池子类别 | 活期 |
| 预算 / POC / 执行 / 接入情况 | - / **空** / **空** / - |

## 2. Ethena 协议背景（作为判断基础）

- **核心产品**：**USDe**（合成美元，delta 中性）、**sUSDe**（质押版，收益型）、**USDtb**（美债背书数字美元）、**ENA**（治理代币）
- **规模（2026-06 口径）**：USDe 供应约 **$44 亿**，sUSDe 约 **$31 亿**（质押率 ~70%）；USDe 从峰值 ~$140 亿降到 $50–60 亿，仍是**第三大美元计价加密资产**
- **收益趋势（关键）**：sUSDe 的 7 日年化 **2026-06 为 7.1%**，2026-04 为 9.4%；已从 20%+ 时代压缩到 **4–5% 区间**（永续资金费率与基差收窄）
- **正在做的 RWA 转向**（与本项目直接相关）：
  - 向 **Centrifuge 的代币化 JAAA 基金**配置 **$2.5 亿**（机构信贷收益）
  - 5 月新增向 **BlackRock BUIDL** 配置，储备中**美债占比首次超过 15%**
  - 计划把 sUSDe APY 靠 RWA 拉高（如 Centrifuge 的 Apollo 企业信贷基金）
  - 2026-04 治理提案：把 USDe 抵押扩展到**机构贷款与私人信贷**
- **USDtb**：由代币化美债（主要是 **BUIDL**）全额背书，托管方含 **BNY Mellon、Coinbase、Fireblocks**；2025-08 流通量已破 **$14.4 亿**
- **托管架构**：USDe 底层资产 1:1 存放在**隔离冷存储金库**，设计上 **bankruptcy-remote**（与托管方资产负债表隔离）；对冲分布在 Binance、Bybit、OKX、Deribit 及 Hyperliquid 等，通过 **Copper、Ceffu** 等场外结算方托管

## 3. 「Ethena Vaults」的三种可能指向

| 可能 | 是什么 | 是否适合我们 | 备注 |
|------|-------|------------|------|
| **A. sUSDe 本体** | 质押 USDe 得 sUSDe，NAV 累积型 | ✅ 最可能、最容易接 | 但**这不是 RWA 产品**（delta 中性基差策略），归到 RWA 理财会有分类问题 |
| **B. Ethena 的 RWA 化产品** | 如以 JAAA / BUIDL / 私人信贷为抵押的新产品 | 🟡 若有，最契合 RWA 主题 | 2026 年 4–6 月还在治理提案/配置阶段，**是否已有独立可申购产品需确认** |
| **C. 第三方平台上以 Ethena 资产为底层的策展金库** | 如 Morpho 上 sUSDe/USDe 相关 ERC-4626 vault | 🟡 | 那就应该按该平台（Morpho 等）来调研，而不是"Ethena" |
| **D. USDtb** | 美债背书数字美元 | ✅ 契合 TBills 类别 | 但 USDtb 本身**不生息**（是稳定币），需确认是否有生息版 |

> 📌 **另一个交叉线索**：本批多个协议**都把 Ethena 当底层组件**——
> - [Re.md](Re.md)：闲置资金放 sUSDe
> - [OnRe.md](OnRe.md)：抵押品就是 sUSDe，还给 ONyc 持有人 **5x Ethena Points**
> - [Ondo.md](Ondo.md) 的 JAAAon 与 Ethena 配置的 Centrifuge JAAA **是同一个底层 ETF**
>
> → **这意味着 Ethena 风险在我们的 RWA 产品池里是"系统性的"**：如果 sUSDe 出问题，Re 和 OnRe 会同时受影响。这一点值得在**组合层面**披露（见 [../03-参考/组合层风险与交叉依赖.md](../03-参考/组合层风险与交叉依赖.md)）。

## 4. 收益来源（若接 sUSDe）

| 收益腿 | 说明 |
|--------|------|
| 永续合约资金费率 / 基差（主，历史） | delta 中性：现货多头 + 永续空头，赚资金费率 |
| 美债 / RWA 收益（占比上升中） | BUIDL、JAAA 等，储备中美债已超 15% |
| **趋势** | 从"纯加密基差"向"加密基差 + RWA"混合演进，收益率从 20%+ 压缩到 4–7% |

## 5. 数据接入要点（若接 sUSDe）

| 维度 | 可行性 | 说明 |
|------|:---:|------|
| 实时解析 | ✅ | sUSDe 是标准 ERC-4626，`convertToAssets` 直接可用 |
| 交易历史解析 | ✅ | 标准 Deposit/Withdraw 事件 |
| 池子自发现 | ✅ | 单一合约 |
| APY | ✅ | 官方有公示 7 日 APY；也可 NAV 差分自算 |
| NAV 历史 | ✅ | 天级打点 `convertToAssets`，可 archive node 回溯（sUSDe 上线久，历史数据丰富） |
| PNL | ✅ | 差分即可 |
| **冷却期** | ⚠️ | sUSDe 解质押历史上有 **7 天 cooldown**（需确认当前是否仍有），影响「赎回中」状态 |

→ **如果接的是 sUSDe，它大概率是本批最容易接的协议**（标准 4626 + 历史数据充足 + 无 KYC）。

## 6. 风险（若接 sUSDe）

1. **资金费率风险**：资金费率转负时收益为负甚至侵蚀本金
2. **收益压缩趋势**：已从 20%+ 降到 4–7%，且**继续下行的结构性压力明确**（杠杆离场）
3. **CEX 交易对手风险**：对冲头寸分布在 Binance/Bybit/OKX/Deribit/Hyperliquid，依赖场外结算方（Copper、Ceffu）
4. **脱锚风险**：USDe 历史规模从 $140 亿降至 $50–60 亿，赎回压力下的脱锚风险
5. **RWA 转向带来的新风险**：抵押品扩展到机构贷款与私人信贷后，**新增信用风险与流动性风险**
6. 🔴 **系统性风险（对我们特别重要）**：Re / OnRe 都以 sUSDe 为底层，**Ethena 出问题会同时影响多个 RWA 产品**
7. **不是 RWA**：如果最终接的是 sUSDe，把它放在 "RWA Earn" 列表里存在**产品定性问题**（它是加密原生策略，不是真实世界资产）

## 7. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | 🔴 **"Ethena Vaults" 到底指哪个产品**（sUSDe？USDtb？某个 RWA 化新产品？第三方金库？） | **CSV 该行 POC 为空，需先找 Marcus / Cece / Terry 认领**（**最高优先，其余问题都依赖这个**） |
| 2 | 若是 sUSDe：把 delta 中性策略放进 "RWA Earn" 是否合适 | Marcus（产品定性） |
| 3 | sUSDe 当前是否仍有 7 天 cooldown | 项目方 / 官方文档 |
| 4 | 是否有 Ethena 的 RWA 化独立产品可申购 | Cece / 项目方 |
| 5 | 补齐 CSV 全部空白字段 + DD 文档 | 待认领的 POC |
| 6 | **组合层面的 Ethena 敞口**：Re + OnRe + Ethena 若同时上线，用户实际是三重 sUSDe 敞口，是否需要在产品层面控制 | Marcus + 风控 |

## 8. 参考链接

- Ethena 官方（USDe / sUSDe / USDtb 机制）：https://www.datawallet.com/crypto/what-is-ethena
- sUSDe 收益机制：https://eco.com/support/en/articles/14798653-susde-explained-ethena-s-yield-bearing-stablecoin
- 2026 delta 中性收益现状（7.1% / 4.4B 供应）：https://eco.com/support/en/articles/15254002-ethena-usde-and-susde-2026-delta-neutral-yield
- **RWA 转向（Centrifuge JAAA $250M / BUIDL / 治理提案）**：https://earlystagealpha.substack.com/p/a-dollar-a-network-a-token-the-ethena
- Q1 2026 报告：https://stablecoininsider.org/ethena-usde-q1-2026-report/
- 金库/托管架构与 vault 语义辨析：https://coinmetrics.substack.com/p/state-of-the-network-issue-359
