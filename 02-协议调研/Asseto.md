# Asseto — NGI+（Partners Group 私募基建股权）

> **状态**：🟡 待确认（CSV「产品上线 = x」，接入情况 "WK"）
> **调研时间**：2026-07-31 ｜ **解析类型**：A（NAV 挂钩，链下基金 NAV 驱动）
> **调研质量**：中（只有 2026-07-07 官方新闻稿，产品细节极少）
> 🔴 **本批协议里流动性最差、透明度最低的一个**：底层是**私募基建股权基金**，NAV 通常按季度估值

## 0. 一句话结论

NGI+ 是把 **Partners Group Next Generation Infrastructure**（全球最大私募市场管理人之一的私募基建股权策略）代币化的产品，由**香港 Asseto** 平台发行——这是 **Asseto 平台上第一个私募基建股权资产**，从"标准化金融产品"扩展到"高度非标的另类资产"。**私募股权型资产的估值频率（季度）和退出流动性（通常有锁定期）与我们"活期 + 天级 NAV"的产品形态存在结构性冲突**。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | Asseto <> Plume（CSV 写法）⚠️ 公开资料未见 NGI+ 与 Plume 的关联，见 9.1 |
| 链 | ETH（Ethereum）⚠️ 官方只说"多链"，未指明 NGI+ 的链 |
| Supply Coins | USDC |
| Coins Integrated | NGI+ |
| 池子 TVL | 4M（本批最小） |
| 收益率 | 15%（本批最高之一）⚠️ 私募股权的"收益率"通常指 IRR/目标回报，**不是可派发的年化利息**，见 6.2 |
| 产品上线 | **x（未上线）** |
| 类别 | Private Credit ⚠️ **实为 Private Equity（私募股权），分类错误，见 9.2** |
| 产品 | The Partners Group Next Generation Infrastructure - Class PC USD ACC |
| 池子类别 | 活期 ⚠️ **私募基建基金标"活期"存疑，见 9.3** |
| 产品网页 | https://reale.finance/distribution/NGIPlus_ETH ⚠️ 该域名与 Asseto（asseto.finance）不一致，见 9.4 |
| DD 文档 | -（无）⚠️ 需补 |
| Onboarding / 预算 | - / **积分** |
| POC | Cece ｜ 执行：产品 ｜ 接入情况：**WK** |

## 2. 协议背景

- **发行平台**：**Asseto**（asseto.finance），自称"亚洲领先的机构级 RWA 代币化平台"
  - **战略投资方**：**HashKey Group**
  - 入选 **BNB Chain MVB 孵化计划**
  - 香港 **Cyberport** 支持的 9 个重点区块链项目之一
  - 已落地**香港首个房地产 RWA 项目**
  - 已在多条链部署 **13 个 RWA 产品**，代币化资产规模 **$5 亿+**
  - 一站式服务：合规顾问 + 资产代币化 + 链上分发 + 结算
- **NGI+ 上线**：**2026-07-07（香港）**，官方定位是"平台首个私募基建股权资产"
- **底层管理人**：**Partners Group**（全球最大私募市场管理人之一）
- 🔴 **免责声明（必须转述给用户）**：官方明确 **Partners Group 及底层基金管理人不是 NGI+ 的发行人**，不对 NGI+ 的发行、技术系统、链上转让安排、投资者服务负责；**提及其名称不构成任何背书、推荐或担保**
  → 📌 **这条对详情页 Compliance / Risk Tab 文案是硬要求**，不能让用户以为"Partners Group 发的产品"

## 3. 底层资产（Underlying）

- **资产**：**私募基建股权基金**的权益份额（Partners Group Next Generation Infrastructure，Class PC USD ACC）
  - 「ACC」= accumulating（累积型份额，收益不分派、滚入 NAV）
- **代币持有人拿到什么**：满足投资者资格与产品准入要求后，获得**链上经济敞口**，其价值**挂钩底层基金份额的 NAV 表现**（受相关产品文件条款约束）
- **底层真实资产**：下一代基础设施资产（通常包括数字基建、能源转型、交通等长周期实物资产）

## 4. 收益来源（Yield Source）

**私募基建股权的资本增值 + 项目现金流**，通过底层基金 NAV 增长体现（ACC 份额不分派）。

⚠️ **关键概念差异**：
- 其他协议的"收益率"= 票息/保费/利差 → **可持续、按期产生**
- 私募股权的"回报"= **IRR / 目标回报** → 分布不均匀，**可能前期为负（J 曲线效应）**，退出时才实现

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | NGI+ |
| 链 | ⚠️ CSV 写 Ethereum，官方只说"多链" |
| 合约地址 | ⚠️ 待补 |
| 收益表达 | 挂钩底层基金 NAV（ACC 累积型） |
| NAV 来源 | 🔴 **链下基金估值 → 链上推送**（私募基建通常**季度估值**） |
| 申购 / 赎回 | ⚠️ **完全不明**。私募基建基金一级市场通常有认购期、锁定期、有限赎回窗口 |
| 准入 | 🔴 官方措辞"满足适用的投资者资格与产品准入要求" → **有准入门槛** |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | 🟡 | NGI+ balance × NAV | 需确认链与合约 |
| 交易历史解析 | 🟡 | 申赎事件 | |
| 池子自发现 | ❌→🟡 | 单一产品，硬编码 | |
| APY | ❌ | **私募股权无 APY 概念** | 见 6.2 |
| NAV 历史 | ⚠️ | 链下推送 | 🔴 **若季度估值，"天级 NAV 曲线"根本不成立** |
| PNL | ⚠️ | 天级快照差分 | 🔴 **季度跳变，昨日收益 99% 的天数为 0** |

### 6.2 关键取数口径 —— 🔴 与 PRD 的结构性冲突

| PRD 要求 | NGI+ 的现实 | 冲突程度 |
|---------|-----------|---------|
| NAV **天级打点** + 1W/1M/3M/1Y 曲线 | 季度估值 → **1W 视图是一条直线，1Y 视图只有 4 个点** | 🔴 高 |
| **Yesterday Earnings** | 季度跳变 → 绝大多数天数为 0，某一天跳一大截 | 🔴 高 |
| 列表页卡片 **APY 降序排列** | 私募股权无 APY，只有目标 IRR 15% | 🔴 高（且若用 15% 参与排序，会排在最前面） |
| **活期赎回** | 私募基建基金通常有锁定期 | 🔴 高 |

📌 **结论建议**：**NGI+ 不适合放在一期**。若必须上，需要 PRD 层面为"低频估值资产"设计单独的展示形态（如：只展示季度 NAV 表点、不展示昨日收益、APY 位置改为"目标回报"并加免责说明）。

### 6.3 需向项目方索取

1. 链部署 + 合约地址
2. 🔴 **NAV 估值频率与推送机制**
3. 🔴 **申赎条款**（认购期、锁定期、赎回窗口、最低申购额）
4. 🔴 **投资者准入的具体要求**
5. 「收益率 15%」的确切定义（目标 IRR？历史回报？）
6. 与 Plume 的关系；「reale.finance」域名的归属
7. 补 DD 文档

## 7. 风险（Risk）

1. 🔴 **流动性风险（首要）**：私募基建股权是**最不流动的资产类别之一**，退出依赖基金的赎回安排或二级转让
2. 🔴 **估值风险**：季度 mark-to-model 估值，**账面 NAV 与可实现价值可能有显著差距**
3. **J 曲线风险**：私募股权投资早期可能账面为负（管理费先扣、资产未增值）
4. **发行人 ≠ 管理人风险**：**Partners Group 不承担任何 NGI+ 相关责任**（官方明确），用户实际的交易对手是 Asseto 及发行实体
5. **长周期资产风险**：基建资产周期长（通常 7–10 年+），受利率、政策、建设周期影响
6. **规模风险**：TVL 仅 4M
7. **结构复杂度风险**：代币 → 链上敞口 → 挂钩基金份额 NAV → 底层基建资产，**四层嵌套**

## 8. 合规与准入（Compliance）

- **发行平台**：Asseto（香港），HashKey 投资，Cyberport 支持
- **底层**：Partners Group Next Generation Infrastructure - Class PC USD ACC
- 🔴 **有投资者资格与产品准入要求**（官方措辞，具体门槛未公开）
- 🔴 **必须披露**：Partners Group 及底层管理人非发行人、不负责、不背书
- **无 DD 文档** → 需补

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | CSV 写 "Asseto <> Plume"，公开资料未见 Plume 关联 | Cece / 项目方 |
| 2 | **类别标 Private Credit，实为 Private Equity（私募股权）** | Marcus（**优先**，分类直接影响风险标签） |
| 3 | 🔴 **私募基建基金标「活期」是否成立** | Cece + Marcus（**最高优先**） |
| 4 | 「reale.finance」是什么平台，与 asseto.finance 什么关系 | Cece |
| 5 | 🔴 **NAV 估值频率** —— 若为季度，建议不进一期 | 项目方 + Marcus |
| 6 | 「收益率 15%」的定义 | Cece |
| 7 | 接入情况标 "WK" 是什么意思（已有人做过？） | WK / Jeff |
| 8 | 补 DD 文档 | Cece |

## 10. 参考链接

- 官方新闻稿（NGI+ 上线，含免责声明原文）：https://www.streetinsider.com/Globe+Newswire/Asseto+Expands+Into+Private+Infrastructure+With+NGI+,+Backed+by+a+Partners+Group+Strategy/26737262.html
- 同一新闻稿（另一媒体）：https://www.manilatimes.net/2026/07/07/tmt-newswire/globenewswire/asseto-expands-into-private-infrastructure-with-ngi-backed-by-a-partners-group-strategy/2379450
- Asseto 平台数据：https://app.rwa.xyz/platforms/asseto
- 产品页（403，需浏览器打开）：https://reale.finance/distribution/NGIPlus_ETH
- Plume 全球 RWA 联盟（CSV 里 Plume 关联的可能来源）：https://plume.org/blog/plume-launches-the-global-rwa-alliance
