# DigiFT — pEAK（Peakwater 波动率 Alpha 基金）

> **状态**：🟡 待确认
> **调研时间**：2026-07-31 ｜ **解析类型**：A（NAV 累积，基金 NAV 驱动）
> **调研质量**：中高（有官方新闻稿 + RWA.xyz，但链部署与赎回条款不明）
> 📌 **本批唯一的"代币化对冲基金策略（tokenized alpha）"产品** —— 不是固收，是主动管理策略

## 0. 一句话结论

DigiFT 是**新加坡 MAS 持牌**的机构级 RWA 交易所，pEAK 是它把 **Peakwater Volatility Alpha Fund**（Pilgrim Partners Asia 管理的跨资产波动率长短仓套利基金）代币化的产品，**目标绝对年化 20%**。它和其他协议的本质区别是：**收益来自主动交易策略的 alpha，不是票息**——所以**收益率不稳定、可能为负**，且**仅面向合格投资者（qualified investors only）**，这是接入的最大门槛。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | DigiFT <> Plume（CSV 写法）⚠️ 公开资料未见 pEAK 与 Plume 的关联，见 9.1 |
| 链 | ETH（Ethereum）⚠️ 待确认 |
| Supply Coins | USDC |
| Coins Integrated | pEAK |
| 池子 TVL | 80M |
| 收益率 | 12%（基金**目标绝对年化 20%**） |
| 产品上线 | **x（未上线）** |
| 类别 | **Equities** |
| 产品 | Peakwater Volatility Alpha Fund Token |
| 产品描述 | 投向量化波动率 alpha 基金，跨传统股票、VIX、固收及数字资产衍生品执行多空波动率套利 |
| 池子类别 | 活期 |
| 产品网页 | https://www.digift.io/solutions/investDetail?tokenCode=pEAK |
| DD 文档 | -（无）⚠️ 需补 |
| POC | Cece ｜ 执行：产品 |

## 2. 协议背景

- **DigiFT**：**新加坡持牌**的机构级 RWA 代币化交易所；累计融资 **$25M**（战略轮）
- **pEAK 上线**：**2025-06-19** 与 **Pilgrim Partners Asia** 合作，宣称是**首个机构级代币化波动率策略**
- **基金结构**：Peakwater Volatility Alpha Fund 是 **Pilgrim Investment VCC**（新加坡可变资本公司 umbrella 结构）下的**注册子基金**
- **管理人**：**Pilgrim Partners Asia**，持 **MAS 资本市场服务（CMS）基金管理牌照**，**仅面向合格投资者**；16 年+ 投资管理经验，AUM **$10 亿+**
- **产品线定位**："**tokenized alpha**" —— 主动管理策略上链，与 DigiFT 的另两个产品并列：**uMINT**（UBS 资管货币市场基金代币）、**iSNR**（代币化私人信贷票据）
- **后续产品**：2025-10-22 推出 **pEAK.BTC**（Peakwater BTC Enhance Fund）—— IBIT 等受监管工具持有的 BTC 敞口 + Peakwater 波动率 alpha 策略

## 3. 底层资产（Underlying）

- **策略**：系统化**相对价值与套利**策略，跨资产捕获**波动率风险溢价**
- **使用工具**：上市工具，如 **COIN（Coinbase 股票）**、**BTC ETF**；覆盖传统股票、**VIX**、固收、数字资产衍生品
- **风控**：严格的**回撤与杠杆控制**
- **目标**：**绝对年化 20%**（absolute return，不是相对基准）

> ⚠️ **CSV 归类 Equities 有些勉强**：底层是波动率衍生品套利，只是**用股票/ETF 作工具**。归 **Structured Products** 更准确。

## 4. 收益来源（Yield Source）

**唯一一条腿：主动交易策略的 alpha**（多空波动率套利）。

这与本批其他协议有本质区别：

| | 其他协议 | pEAK |
|---|---------|------|
| 收益性质 | 票息 / 保费 / 利差（**可预期**） | **交易 alpha（不可预期）** |
| 收益为负的概率 | 低（除信用事件） | **正常业务范围内就可能为负** |
| APY 曲线形态 | 平稳或阶梯 | **波动，可能下行** |

📌 **这决定了前端表达方式**：pEAK 不应该展示"APY"（暗示稳定收益），而应展示 **NAV 走势 + 历史业绩**。PRD 4.5.1 的核心指标栏支持多 Tab（NAV / APY / 派息），**pEAK 应主推 NAV Tab**。

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | pEAK |
| 链 | ⚠️ CSV 写 Ethereum，公开资料未明确 |
| 合约地址 | ⚠️ 待补 |
| 收益表达 | 基金 NAV 驱动（NAV 累积型） |
| 申购 / 赎回 | ⚠️ **完全不明**。对冲基金型产品通常有**申购日/赎回日（月度或季度）+ 通知期 + 锁定期**，需确认 |
| 准入 | 🔴 **仅合格投资者（qualified investors only）** |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | 🟡 | pEAK balance × NAV | 需确认链与合约 |
| 交易历史解析 | 🟡 | 申赎事件 | 若有申购日/赎回日机制，事件会是"申请→执行"两段 |
| 池子自发现 | 🟡 | DigiFT 多产品（pEAK / pEAK.BTC / uMINT / iSNR） | |
| APY | ⚠️ | **不建议展示 APY** | 见 4. 收益来源 |
| NAV 历史 | 🟡 | 关键指标，**必须接** | ⚠️ NAV 更新频率待确认（基金常按周/月估值） |
| PNL | 🟡 | 天级快照差分 | **常态可能为负**，PRD 允许负值这点在这里最常触发 |

### 6.2 关键取数口径

1. 🔴 **NAV 更新频率**：对冲基金常按**周或月**公布 NAV。若非每日，"昨日收益"字段基本没意义（同 [Midas.md](Midas.md) 的问题）
2. 🔴 **合格投资者限制**：如果只有合格投资者能申购，**这个产品能不能进 Binance Wallet 的公开列表**本身就是问题 → 见 9.2
3. **APY 字段处理**：PRD 各处（列表页卡片、DeFi 首页"Up to {max_apy}% APY"）都强依赖 APY。若 pEAK 无稳定 APY：
   - 用什么填列表页卡片的 APY？（用"目标 20%"会有合规风险）
   - 会不会因为 pEAK 的目标收益最高而被选进首页"Up to X% APY"？→ **这是合规隐患**

### 6.3 需向项目方索取

1. 链部署 + 合约地址
2. 🔴 **NAV 更新频率与公布方式**
3. 🔴 **申赎机制**（申购日/赎回日、通知期、锁定期、最低申购额）
4. 🔴 **投资者准入的具体要求**（能否向零售用户开放）
5. 历史 NAV / 业绩数据（含最大回撤）
6. 与 Plume 的关系
7. 补 DD 文档

## 7. 风险（Risk）

1. 🔴 **策略风险（首要）**：主动波动率套利策略，**亏损属正常业务范围**。相对价值策略在市场结构突变（如波动率骤升）时可能大幅回撤
2. **杠杆风险**：策略使用杠杆（官方称有严格控制，但未披露上限）
3. **数字资产敞口**：使用 COIN 股票与 BTC ETF 作工具，间接承担加密市场风险
4. **流动性/赎回风险**：对冲基金型产品赎回条款通常严格，可能有 gate 条款
5. **估值风险**：衍生品头寸估值依赖模型，非市价
6. **准入风险**：合格投资者限制
7. **管理人集中风险**：单一管理人（Pilgrim Partners Asia）

## 8. 合规与准入（Compliance）

- **发行/交易平台**：DigiFT，**新加坡持牌**
- **基金结构**：Pilgrim Investment VCC 下注册子基金（新加坡）
- **管理人**：Pilgrim Partners Asia，持 **MAS CMS 基金管理牌照**
- 🔴 **仅面向合格投资者（qualified investors only）**
- **无 DD 文档** → 需补

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | 链部署（CSV 写 ETH，公开资料未确认）+ 与 Plume 的关系 | Cece / 项目方 |
| 2 | 🔴 **仅合格投资者可投 —— 是否能进 Binance Wallet 公开列表？** | Cece + 法务（**红线问题，建议先定性再投入接入资源**） |
| 3 | 🔴 NAV 更新频率 | 项目方 |
| 4 | 🔴 申赎机制（是否真"活期"） | 项目方 |
| 5 | **APY 字段怎么填**（无稳定 APY，但 PRD 列表页/首页强依赖 APY） | Marcus（**优先**，涉及首页"Up to X% APY"的合规表述） |
| 6 | 类别归 Equities 是否改为 Structured Products | Marcus |
| 7 | 补 DD 文档 | Cece |

## 10. 参考链接

- 产品页：https://www.digift.io/solutions/investDetail?tokenCode=pEAK
- DigiFT 官方（首个机构级代币化波动率策略上线）：https://insights.digift.io/the-first-institutional-grade-tokenized-volatility-strategy-goes-live-on-digift/
- Pilgrim Partners Asia 新闻稿：https://www.pilgrimpartnersasia.com/in-the-news/press-release-digift-and-pilgrim-partners-asia-launch-peakwater
- pEAK.BTC（后续产品）：https://insights.digift.io/digift-pilgrim-partners-launch-peakwater-btc-focused-volatility-fund/
- Peakwater 官网：https://www.peakwatervolatility.com/
- DigiFT $25M 融资：https://www.prnewswire.com/apac/news-releases/digift-closes-strategic-round-to-scale-institutional-grade-rwa-infrastructure-securing-us25-million-to-date-302534433.html
- 数据：https://app.rwa.xyz/assets/pEAK
