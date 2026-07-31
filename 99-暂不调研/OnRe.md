# OnRe — ONyc（百慕大持牌链上再保险）

> **状态**：🟡 待确认
> **调研时间**：2026-07-31 ｜ **解析类型**：A（NAV 累积，非 rebasing）
> **调研质量**：高（官方 docs 完整）
> ⚠️ **链在 Solana（SPL 代币），是本批唯一非 EVM 的重点候选** —— 与"约 8 个协议，EVM 为主"的项目基线冲突

## 0. 一句话结论

OnRe 是**百慕大双牌照持牌再保险公司**（保险 + 数字资产），ONyc 是它的收益型美元资产：用户存 sUSDe / USDG 进百慕大**受监管的分隔账户（Segregated Account）**，赚"再保险保费（8–10%）+ 抵押品收益（sUSDe 的 Ethena 收益）"的**叠加收益**。接入难点有三个：**Solana 链**、**季度赎回 + 30 天通知期**、**赎回需 KYC 且要求"成熟投资者"**。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | OnRe <> Plume（CSV 写法）⚠️ 公开资料**未见 OnRe 与 Plume 的关联**，官方只在 Solana，见 9.1 |
| 链 | **SOL（Solana）** |
| Supply Coins | USDC（官方：**sUSDe 或 USDG**） |
| Coins Integrated | ONyc |
| 池子 TVL | 120M（官方：2026-02-17 AUM 破 $100M，提前 4 个月达标；Kamino 借贷市场规模破 $150M） |
| 收益率 | 12%（官方：基础 8–10% 保费，混合目标 9–15%；第三方称均值 11.97%） |
| 类别 | Reinsurance |
| 产品 | OnRe tokenized reinsurance asset |
| 产品描述 | 将 DeFi 稳定币抵押品（sUSDe）用于承保传统再保险合同，收益叠加 DeFi 基础利率与真实保费 |
| 池子类别 | 活期 ⚠️ **与官方「季度赎回 + 30 天通知期」冲突，见 9.2** |
| 产品网页 | https://app.onre.finance/earn |
| DD / 预算 | -（无 DD）/ - |
| POC | Cece ｜ 执行：产品 |

## 2. 协议背景

- **牌照（本批协议里最硬的合规资质之一）**：百慕大金融管理局（BMA）**双牌照** —— **Innovative Insurer General Business** 牌照 + **Class F 数字资产业务法（DABA）** 牌照。公司自称是**首个同时持有两类牌照的再保险公司**，因此可以**直接接受数字资产作为抵押品**
- **业务**：从原保险公司买入风险（飓风、地震等巨灾），以及专业险种（D&O、Cyber、Tech E&O）
- **法律结构**：资本装在**分隔账户（Segregated Account, SA）**里 —— ONyc Pool 有自己独立的 SA，资产负债与其他业务**法律隔离（ring-fenced）**
- **融资**：**$5M A 轮**，Forward Industries（NASDAQ: FWDI）与 RockawayX 联合领投；Forward Industries（最大 SOL 企业持有方，700 万+ SOL）计划向该平台发行的 Solana 收益代币投入最高 **$2,500 万**
- **产品演进**：从早期单一抵押品的 **ONe** 演进为多抵押品的 **ONyc**
- **审计**：Quantstamp
- **DeFi 组合性（Solana 生态很深）**：Kamino（借贷市场 $150M+，**Solana 上最大链上再保险市场**）、Loopscale、Orca（ONyc/sUSDe 池）、Exponent、Drift
- **激励**：ONyc 持有人有 **5x Ethena Points 加成**（合格 sUSDe 存款）；另有 $ONRE 空投预期

## 3. 底层资产（Underlying）

```
用户 sUSDe（或 USDG）
  └─ 百慕大受监管资金池（ONyc Pool，独立 SA）
       ├─ 抵押品本身继续吃 Ethena sUSDe 收益
       └─ OnRe 精算团队把抵押品部署进真实再保险合同
            ├─ 财产巨灾（飓风 / 地震）
            └─ 专业险种（D&O / Cyber / Tech E&O）
```

**两层收益叠加**是它的核心卖点，也是**风险叠加**的来源（既有保险赔付风险，又有 Ethena delta 中性策略风险）。

## 4. 收益来源（Yield Source）

| 收益腿 | 量级 |
|--------|------|
| 再保险保费（承保业绩） | 基础 **8–10% APY**（官方 FAQ：10–12%） |
| 抵押品收益（sUSDe / Ethena） | 随资金费率浮动 |
| **合计目标** | **约 9–15% APY** |

**收益表达**：**非 rebasing** —— 收益体现在 **NAV 变化**上，**赎回时或通过二级流动性实现**。每个 ONyc 代表对分隔账户的部分索偿权，保费赚取导致账户增长或收缩时 ONyc 随之调整。

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | **ONyc（Solana SPL 代币）** |
| **Mint 地址** | `5Y8NV33Vv7WbnLfq3zBcKSdYPrk7g2KoiQoe7M2tcxp5` ✅（官方文档给出，本批少数有确切地址的） |
| 收益表达 | 非 rebasing，NAV 累积 |
| 申购 | 存 sUSDe 或 USDG 铸 ONyc |
| **赎回** | 🔴 **季度周期**；每季度初有 **30 天通知窗口**，期间用户把 ONyc 换成 **redemption token**；**赎回需 KYC 且需满足"成熟投资者（sophisticated investor）"资格** |
| 二级退出 | Orca 上有 ONyc/sUSDe 池（不想等季度可二级卖出，但有滑点） |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | 🟡 | ONyc balance × NAV | **需 Solana 解析能力**（非 EVM） |
| 交易历史解析 | 🟡 | Solana 指令解析；**赎回三段式**（换 redemption token → 季度执行 → 到账） | redemption token 是独立代币，持仓解析要不要计入？见 6.2 |
| 池子自发现 | 🟡 | 单一产品 | |
| APY | 🟡 | NAV 差分自算 | 官方口径多（8-10 / 10-12 / 9-15 / 11.97%） |
| NAV 历史 | 🟡 | 天级打点 | ⚠️ NAV 更新频率待确认（保险业绩通常按月/季结算，可能不是每日更新） |
| PNL | 🟡 | 天级快照差分 | 巨灾年份可能负值 |

### 6.2 关键取数口径

1. 🔴 **Solana 解析能力**：项目基线是"约 8 个协议，**EVM 为主**"。本协议是 SPL 代币，**需要 Solana 侧解析能力**。若中台暂不支持，这是硬阻塞（Huma PST 同样在 Solana，见 [Huma.md](Huma.md)）
2. 🔴 **redemption token 的持仓归属**：用户在通知窗口把 ONyc 换成 redemption token 后，**手里已经不是 ONyc 了**。此时：
   - 持仓解析若只认 ONyc，用户持仓会**归零**（严重 bug）
   - 必须把 redemption token 识别为"**赎回中持仓**" → 正好对应 PRD 的「赎回中状态」
3. **NAV 更新频率**：若非每日更新，天级打点会出现长平段，APY 曲线不平滑

### 6.3 需向项目方索取

1. **NAV 的链上取数方式与更新频率**（Solana 上是 oracle account 还是 program state）
2. **redemption token 的 mint 地址与生命周期**
3. 季度赎回窗口日历
4. **KYC / 成熟投资者要求的具体门槛**（决定我们的用户能不能赎回！）
5. 历史 NAV 数据
6. 与 Plume 的关系（CSV 写 "OnRe <> Plume"）

## 7. 风险（Risk）

1. **巨灾承保风险（首要）**：飓风、地震等巨灾赔付超预期时，分隔账户缩水，ONyc NAV 下跌、本金亏损
2. 🔴 **赎回流动性风险**：**季度赎回 + 30 天通知窗口**；错过窗口需等下一季度；二级市场退出有滑点
3. 🔴 **赎回准入风险**：**赎回需 KYC + 成熟投资者资格** —— 如果我们的钱包用户不满足，**买得进赎不出**，这是产品级红线问题
4. **嵌套风险**：抵押品是 sUSDe → 叠加 Ethena 的 delta 中性策略风险（资金费率转负、脱锚）
5. **专业险种长尾风险**：D&O / Cyber / Tech E&O 的赔付有长尾特性，损失可能滞后数年才体现
6. **单一实体集中风险**：所有承保由 OnRe 一家精算团队决策
7. **Solana 生态风险**：链本身 + Kamino/Orca 等组合层

## 8. 合规与准入（Compliance）

- **发行/承保主体**：OnRe，百慕大注册，**BMA 双牌照**（Innovative Insurer General Business + Class F DABA）
- **法律隔离**：ONyc Pool 独立分隔账户（SA），资产负债 ring-fenced
- 🔴 **赎回需 KYC + 成熟投资者资格**（申购是否需要 KYC 待确认）
- 审计：Quantstamp
- **无 DD 文档**（CSV 里 DD 栏为 "-"）→ 需补

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | CSV 写 "OnRe <> Plume"，但官方只在 Solana —— 是否有 Plume 部署计划？ | Cece / 项目方 |
| 2 | 🔴 **CSV 标「活期」，官方是季度赎回 + 30 天通知期** | Marcus + Cece（**最高优先**） |
| 3 | 🔴 **赎回的 KYC / 成熟投资者门槛，我们的用户是否满足** | Cece（**红线问题，先问清再评估接入**） |
| 4 | 🔴 **中台是否具备 Solana 解析能力**（本协议 + Huma 都在 Solana） | Jeff / Johnson |
| 5 | redemption token 如何识别与计入持仓 | 项目方 + Scott/Linnea |
| 6 | NAV 更新频率 | 项目方 |
| 7 | 补 DD 文档 | Cece |

## 10. 参考链接

- 官方文档 - ONyc 介绍（含 mint 地址）：https://docs.onre.finance/introduction/onre-tokenized-reinsurance-onyc
- 官方文档 - ONyc 白皮书：https://docs.onre.finance/getting-started/the-onchain-yield-coin-onyc/the-onyc-whitepaper
- 官方 FAQ（赎回 / KYC / 成熟投资者）：https://docs.onre.finance/technical-resources/frequently-asked-questions
- 官网：https://www.onre.finance/ ｜ 产品页：https://app.onre.finance/earn
- ONe → ONyc 演进：https://www.onre.finance/blog/onre-evolves-one-into-onyc-to-power-stablecoin-adoption-across-defi-and-traditional-markets
- Kamino 市场破 $150M：https://solanacompass.com/news/onres-onyc-lending-market-on-kamino-crosses-150m-as-institutional-capital-backs-on-chain-reinsurance
- 保险业媒体报道（牌照 / 结构）：https://www.insurancebusinessmag.com/reinsurance/news/breaking-news/onre-launches-onyc-to-turn-stablecoins-into-yieldgenerating-reinsurance-collateral-541497.aspx
- $5M A 轮：https://coinalertnews.com/news/2026/05/06/onre-funding-solana-reinsurance
- 数据：https://www.coingecko.com/en/coins/onyc
