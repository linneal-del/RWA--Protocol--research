# Midas — mfONE（Fasanara 私人信贷）

> **状态**：🟡 待确认
> **调研时间**：2026-07-31 ｜ **解析类型**：A（NAV 累积）
> **调研质量**：中高
> ⚠️ **CSV 写 BNB Chain，但公开资料明确 mF-ONE 是 Ethereum 上的 ERC-20** —— 见 9.1

## 0. 一句话结论

Midas 是**代币化发行平台**，mF-ONE 是它把 **Fasanara Capital 的 F-ONE 私人信贷基金**代币化的产品；它最值得注意的设计是**三层流动性结构**（美债即时层 + 中间层 + 基金核心层），其中**即时流动性层占 TVL 约 10%，支持即时用 USDC 赎回**——这在私人信贷类产品里算流动性设计做得好的。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | Midas |
| 链 | BNB Chain ⚠️（公开资料：mF-ONE 在 **Ethereum**，见 9.1） |
| Supply Coins | USDT |
| Coins Integrated | mfONE（官方写法 **mF-ONE**） |
| 池子 TVL | 70M |
| 收益率 | 11% |
| 类别 | Private Credit |
| 产品 | Fasanara's Tokenized Investment Strategy |
| 产品描述 | Fasanara Capital 的 SME 贷款、房地产信贷、fintech 应收账款 |
| 池子类别 | 活期 |
| 产品网页 | https://midas.app/mfone |
| DD 文档 | 空 ⚠️ 需补 |
| Onboarding / 预算 | Projects' campaign info / - |
| POC | Terry ｜ 执行：预算 |

## 2. 协议背景

- **Midas**：代币化发行商，把机构资管策略包成"链上投资凭证（certificate）"，专门为 **DeFi 集成**设计
- **融资**：**$50M A 轮**，RRE 与 Creandum 领投
- **mF-ONE 上线**：2025-06 与 **Fasanara Capital**（机构资管）+ **Morpho** + **Steakhouse Financial** 联合推出
- **DeFi 玩法（repo 策略）**：合格投资者可把 mF-ONE 抵押进 **mF-ONE/USDC Morpho 市场**，借出 Steakhouse 策展金库供给的 USDC；白名单 LP 可借 USDC 买更多 mF-ONE 加杠杆 —— 设计上利用 Morpho **$20 亿+ USDC 流动性**，规模化时不显著推高借贷利率
- **Midas Staked Liquidity（MSL）**：初始额度最高 **$4,000 万**的即时赎回便利，无交易对手/结算风险
- **其他产品**：mTBILL（代币化美债）、mHYPER、**mXRP**（与 Axelar + Lista DAO 合作，**在 BNB Chain 上**）
  → 📌 **CSV 的 BNB 可能是把 mXRP 的链搞混了**，见 9.1

## 3. 底层资产（Underlying）

**Fasanara Capital F-ONE 基金**的四类配置：
1. **Fintech 原生应收账款**（fintech-originated receivables）
2. **SME 贷款**（中小企业贷）
3. **房地产抵押信贷**
4. **delta 中性数字资产策略**

**三层流动性结构**（对我们的赎回体验最重要）：

| 层 | 内容 | 作用 |
|----|------|------|
| 即时流动性层 | 链上美债（**mTBILL**），目标 **占 mF-ONE TVL 的 10%** | **支持即时 USDC 赎回** |
| 中间流动性管理层 | — | 缓冲 |
| 核心配置层 | F-ONE 基金 | 主收益来源 |

## 4. 收益来源（Yield Source）

- **私人信贷票息**：SME 贷款利息、应收账款贴现、房地产信贷利息
- **delta 中性数字资产策略**收益
- **即时层美债收益**（mTBILL）

CSV 标 11%，属私人信贷合理区间。

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | **mF-ONE**，公开资料明确为 **Ethereum ERC-20** |
| 合约地址 | ⚠️ 待补 |
| 收益表达 | NAV 累积（代币化投资凭证） |
| 赎回 | **即时层（约 10% TVL）支持即时 USDC 赎回**；超出即时层额度时 ⚠️ 需确认排队机制 |
| 组合性 | Morpho（mF-ONE/USDC 市场）+ Steakhouse 策展金库 |
| 准入 | ⚠️ Morpho repo 玩法明确限"**qualified investors / 白名单 LP**"，**主产品申购是否需白名单待确认** |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | ✅ | mF-ONE balance × NAV | Ethereum ERC-20，标准 |
| 交易历史解析 | ✅ | mint / redeem 事件 | |
| 池子自发现 | 🟡 | Midas 有多个 m* 产品（mTBILL / mHYPER / mXRP / mF-ONE），可考虑做通用方案 | |
| APY | 🟡 | NAV 差分自算 | 基金 NAV 更新频率待确认（私人信贷基金常按月估值） |
| NAV 历史 | 🟡 | 天级打点 | ⚠️ **若基金按月估值，天级打点会出现长平段 + 月末跳变** |
| PNL | 🟡 | 天级快照差分 | 月末跳变会让"昨日收益"某天特别大、其他天为 0 |

### 6.2 关键取数口径

🔴 **NAV 更新频率是本协议最关键的问题**：
- 私人信贷基金通常**按月（甚至按季）估值**
- 若 mF-ONE 的 NAV 是月度更新，则：
  - **NAV 曲线是阶梯而非曲线**
  - **Yesterday Earnings 会大部分时间为 0，月末跳一次** → PRD 4.5.2 的"昨日收益"字段体验会很差
  - **建议**：对这类产品在前端说明"收益按月结算"，或用**线性摊平**（需与 Jackson/Marcus 对齐口径）

### 6.3 需向项目方索取

1. **链部署确认**（Ethereum？是否有 BNB 部署？）+ 合约地址
2. 🔴 **NAV 更新频率**（每日 / 每月 / 每季）
3. 即时赎回额度上限与超额后的排队机制
4. 主产品申购是否需要白名单 / 合格投资者
5. 历史 NAV 数据
6. 补 DD 文档

## 7. 风险（Risk）

1. **私人信贷违约风险**：SME 贷款、fintech 应收账款、房地产信贷都有违约敞口
2. **流动性分层风险**：即时赎回只覆盖约 10% TVL，**集中赎回时会打穿即时层**，剩余需等基金层变现
3. **估值透明度风险**：私人信贷基金 NAV 由管理人估值，非市价（mark-to-model 而非 mark-to-market）
4. **杠杆传导风险**：Morpho repo 策略允许白名单 LP 加杠杆，若集中去杠杆会冲击 mF-ONE 二级价格
5. **交易对手风险**：Fasanara Capital（基金管理人）+ Midas（发行人）
6. **delta 中性腿风险**：基金内含加密中性策略，资金费率风险传导

## 8. 合规与准入（Compliance）

- Midas 为代币化发行平台，产品是"投资凭证"
- ⚠️ Morpho 侧玩法明确限合格投资者/白名单；**主产品准入待确认**
- 需补 DD

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | **CSV 写 BNB Chain，公开资料是 Ethereum ERC-20** —— 是否混淆了 Midas 的 mXRP（确实在 BNB）？ | Terry / 项目方（**优先**） |
| 2 | 🔴 **NAV 更新频率**（决定 NAV 曲线与昨日收益的体验） | 项目方（**优先**） |
| 3 | 申购是否需白名单 / 合格投资者 | Terry |
| 4 | 补 DD 文档（CSV 该栏为空） | Terry |
| 5 | 即时赎回额度上限 | 项目方 |

## 10. 参考链接

- 产品页：https://midas.app/mfone
- The Block（mF-ONE 上线 / Fasanara 策略构成）：https://www.theblock.co/post/360182/midas-tokenizes-fasanaras-f-one-fund-for-onchain-exposure-to-private-credit-digital-asset-strategies
- CoinDesk（Midas + Fasanara + Morpho + Steakhouse）：https://www.coindesk.com/business/2025/06/27/tokenization-firm-midas-introduces-private-credit-product-with-fasanara-morpho-and-steakhouse
- Steakhouse 拆解（三层流动性 / repo 机制）：https://kitchen.steakhouse.financial/p/midas-fasanara-mf-one-in-morpho-onchain
- Morpho 案例页：https://morpho.org/stories/midas/
- 数据：https://app.rwa.xyz/assets/mF-ONE
