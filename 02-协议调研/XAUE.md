# XAUE（Aurise Foundation）— 收益型代币化黄金

> **状态**：🟡 待确认（CSV 信息最不完整的一行：无 DD、无产品名、无产品页、无池子类别）
> **调研时间**：2026-07-31 ｜ **解析类型**：**A 变体（份额不变、储备增长）** ← 机制与其他协议都不同
> **调研质量**：中（有官方新闻稿，但与 CSV 口径冲突较多）

## 0. 一句话结论

XAUE 是 **Aurise Foundation** 发行的 **XAU₮（Tether Gold）"金库层（Treasury Layer）"**：存 1 XAU₮ 铸 **1,000 XAUE**（1000:1 拆细，单价降到约 $5），收益**不是发新币也不是涨价，而是"每个代币背后的黄金变多"**（供应量固定、储备增长）。预期 APR 只有 **1.5%–3%**，且**铸赎仅限 KYC/KYB 通过的白名单 Qualified Minter**。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | XAUE |
| 链 | BNB Chain ⚠️ **官方明确 XAUE 在 Ethereum，不在 BNB Chain**，见 9.1 |
| Supply Coins | USDT ⚠️ **官方是存 XAU₮ 铸 XAUE，不是 USDT**，见 9.2 |
| Coins Integrated | XAUE |
| 池子 TVL | 50M（发行时生态伙伴承诺 16,052 XAU₮ ≈ **$7,600 万**） |
| 收益率 | 3%（官方预期 APR **1.5%–3%**） |
| 产品上线 | /（已上线） |
| 类别 | Gold |
| 产品 | 空 ⚠️ |
| 产品描述 | 空 ⚠️ |
| 产品网页 | 空 ⚠️（官网 https://www.xaue.com/） |
| 池子类别 | 空 ⚠️ |
| DD 文档 | 空 ⚠️ 需补 |
| 预算 / POC | - / 空 ｜ 执行：产品 + 预算 |

> 📌 这一行是 Master 表里**填得最不完整的**：产品名、描述、产品页、池子类别、DD、POC 全空，且链与申购币种都与公开资料冲突。**建议先补齐表格再评估接入。**

## 2. 协议背景

- **发行方**：**Aurise Foundation**
- **上线时间**：**2026-04-22**（PR Newswire 口径）⚠️ 另有 KuCoin 文章称 2026-06，时间冲突
- **定位**：为 **Tether Gold（XAU₮）** 提供"**Treasury Layer**"，把传统上**不产生收益**的黄金变成有收益的资产；面向**合格机构参与者**
- **启动资金**：生态伙伴 **Aurelion** 与 **Antalpha** 联合承诺 **16,052 XAU₮**（2026-04-22 约 **$7,600 万**）；其中 Aurelion 投入 10,000 XAUT（约 $4,800 万），此前于 2025-10 完成 $1.5 亿募资
- **消费端延伸**：2026-06-09 推出 **XAUE 黄金礼品卡**，是 XAUE 上首个企业级消费应用（企业送礼 / 客户奖励场景）
- **市场背景**：2026-04 代币化黄金总市值约 **$54.5 亿**；2025 年代币化黄金增速是实体金价的 **2.6 倍**

## 3. 底层资产（Underlying）

```
XAU₮（Tether Gold，1 XAU₮ = 1 金衡盎司实物黄金）
  └─ 存入 → 按 1:1000 铸出 XAUE（固定供应模型）
       └─ 底层黄金储备被部署到 DeFi 协议：借贷、staking、提供流动性
```

⚠️ **注意与 [Unitas.md](Unitas.md)（XGLD）的高度相似性**：两者都是"XAUt + DeFi 收益"，**是同质竞品**。差异：

| | Unitas XGLD | XAUE |
|---|---|---|
| 收益机制 | 抵押 XAUt 借稳定币跑 delta 中性（**70% LTV 杠杆**） | 把黄金储备投入 DeFi 借贷/staking/LP（**无明显杠杆**） |
| 收益率 | 3% | 1.5–3% |
| 代币比例 | 1:1 | **1:1000** |
| 收益表达 | NAV 增长 | **储备增长、供应不变** |
| 铸赎准入 | 未见 KYC 要求 | **需 KYC/KYB 白名单** |
| 链 | BNB Chain | **Ethereum** |

## 4. 收益来源（Yield Source）

把底层黄金储备部署到 **DeFi 借贷、staking、提供流动性**赚取收益。预期 **APR 1.5%–3%**（随市场波动）。

**参考市场水位**：DefiLlama 的黄金代币收益表显示 XAUT/PAXG 在 Lista Lending、YO Protocol、Uniswap 等池的 30 日平均 APY **2.08%–11.12%**，最高为 Lista Lending 上的 XAUT。
→ 📌 说明 1.5–3% 是**偏保守**的水位，也侧面印证它确实没加杠杆。

## 5. 链上机制与凭证代币 —— ⚠️ 机制特殊，重点看

| 项 | 内容 |
|----|------|
| 凭证代币 | **XAUE**（Ethereum，官方口径） |
| 合约地址 | ⚠️ 待补 |
| 铸造比例 | **1 XAU₮ → 1,000 XAUE**（固定供应模型），单价降到约 **$5** |
| 🔴 **收益表达** | **供应量不变，储备增长**：例：存 1 XAU₮ 得 1,000 XAUE；2% 年化后储备变 1.02 XAU₮，**供应仍是 1,000 XAUE**，此时 1,000 XAUE 可赎回 **1.02 XAU₮** |
| 铸赎准入 | 🔴 **仅 KYC/KYB + AML 通过的 "Qualified Minter" 白名单可直接铸造/赎回**；零售用户只能在**二级 DEX/CEX 市场交易** |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | 🟡 | XAUE balance × **每 XAUE 对应的 XAU₮ 数量** × XAU₮ 价格 | **三段换算**，比其他协议多一层 |
| 交易历史解析 | 🟡 | mint / redeem 事件（**但零售是二级市场买卖，不是 mint**） | 🔴 见 6.2 |
| 池子自发现 | 🟡 | 单一产品 | |
| APY | 🟡 | 由"储备/供应"比率差分算 | 不能用价格差分（价格含金价） |
| NAV 历史 | 🟡 | 天级打点"储备/供应"比率 | |
| PNL | ⚠️ | **与 XGLD 同样的双计价问题** | 见 6.2 |

### 6.2 关键取数口径 —— 两个特殊问题

**① 三段换算 + 双计价（同 [Unitas.md](Unitas.md) 的问题）**

```
XAUE 的美元价值 = XAUE 数量 × (储备 XAU₮ / 总供应 XAUE) × XAU₮/USD 价格
                              └── 这才是收益（缓慢上升）──┘   └── 金价波动 ──┘
```
- **USD 本位 PNL 会混入金价涨跌**，1.5–3% 的收益很容易被金价波动完全盖住
- 必须**分开存**"储备比率"与"金价"两个序列才能做归因

**② 🔴 白名单铸造 → 用户拿币路径完全不同**

| | 白名单 Qualified Minter | 我们的钱包用户 |
|---|---|---|
| 获取方式 | mint（存 XAU₮） | **二级市场买入（DEX/CEX）** |
| 链上表现 | Mint 事件 | **Swap / Transfer** |
| 成本基准 | 铸造时的储备比率 | **买入时的市场价（可能溢价/折价）** |

→ 🔴 **对我们的直接影响**：如果用户是二级市场买入，**"申购成本"无法从 mint 事件推断**，PNL 的成本基准必须用**买入时的实际成交价**。而 PRD 的申购流程（4.6.1）是"Stake → 铸造凭证"模型，**与本产品不匹配**。

### 6.3 需向项目方索取

1. **链部署确认**（Ethereum？是否有 BNB 部署计划？）+ 合约地址
2. 🔴 **我们的用户如何申购**：能否成为 Qualified Minter，还是只能走二级市场？
3. **储备量与供应量的链上查询接口**（算 NAV 的关键）
4. 历史储备比率数据
5. 收益部署的具体 DeFi 协议清单（风险披露需要）
6. 补齐 CSV 空白字段 + DD 文档

## 7. 风险（Risk）

1. **金价风险**：完整黄金价格敞口，金价下跌时美元本位亏损（且 1.5–3% 收益极易被金价波动盖过）
2. 🔴 **准入/流动性风险**：铸赎需白名单，零售只能走二级市场 → **二级流动性不足时可能有显著溢价/折价，且赎回路径不通**
3. **DeFi 部署风险**：储备被投入借贷/staking/LP，承担这些协议的智能合约与清算风险；**LP 还有无常损失风险**
4. **交易对手层叠**：Tether（XAU₮ 的实物黄金背书）+ Aurise（储备管理）+ 各 DeFi 协议
5. **集中持仓风险**：启动资金主要来自 Aurelion 与 Antalpha 两家，**大额赎回会显著影响储备**
6. **信息透明度风险**：本批协议里公开资料最少的之一，无官方 docs 站可查机制细节

## 8. 合规与准入（Compliance）

- **发行方**：Aurise Foundation
- 🔴 **直接铸造/赎回仅限 KYC/KYB + AML 通过的 Qualified Minter**（面向合格机构参与者）
- 零售用户只能二级市场交易
- **无 DD 文档** → 需补

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | **CSV 写 BNB Chain，官方明确在 Ethereum**（XAU₮ 本身 2026-03 才上 BNB Chain，可能是被混淆了） | 需定 POC（CSV 该栏为空）（**优先**） |
| 2 | **CSV 写 Supply Coins = USDT，官方是存 XAU₮ 铸造** | 同上（**优先**） |
| 3 | 🔴 **白名单铸造 → 我们的用户怎么申购？**（决定这个产品能否按 PRD 的 Stake 流程接入） | 项目方（**最高优先**） |
| 4 | 补齐 CSV 空白字段（产品名/描述/产品页/池子类别/DD/POC） | 需先定 POC |
| 5 | **与 Unitas XGLD 同质** —— 两个黄金产品是否都上 | Marcus（产品决策） |
| 6 | 上线时间口径冲突（2026-04 vs 2026-06） | 项目方 |

## 10. 参考链接

- 官网：https://www.xaue.com/
- 官方发布（机制 / 1000:1 / 储备增长模型 / Qualified Minter）：https://www.prnewswire.com/news-releases/aurise-foundation-launches-xaue-unlocking-yield-for-rwa-gold-302750318.html
- KuCoin 解读（⚠️ 时间口径与上文冲突）：https://www.kucoin.com/blog/Aurise-launches-xaue
- Aurelion 投入 $48M：https://grafa.com/en/news/crypto/aurelion-48m-tokenised-gold-xaue
- XAUE 黄金礼品卡：https://www.prnewswire.com/apac/news-releases/xaue-launches-gold-gift-card-to-bring-tokenized-gold-into-corporate-gifting-and-everyday-use-302795090.html
- Tether Gold（XAU₮）：https://www.aurelion.com/xaut
- 代币化黄金市场与收益水位：https://coinpaprika.com/education/defi-tokenized-commodities-lending-collateral-and-yield/
