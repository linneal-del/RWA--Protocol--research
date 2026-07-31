# Ondo Finance — USDY / QYLDon / JAAAon

> **状态**：✅ 已确认接入（USDY）｜ 🟡 待确认（QYLDon、JAAAon）
> **调研时间**：2026-07-31 ｜ **解析类型**：**同一个协议里三种机制都有**（A / B 混合，取决于链）
> **调研质量**：高（官方 docs + Chainlink 文档，机制描述最清晰的一个）
> 📌 本文件覆盖 Master 表三行：**Ondo / USDY**（已接入）、**Ondo / QYLDon**、**Ondo / JAAAon**（待确认）

## 0. 一句话结论

Ondo 有两条产品线，**解析逻辑完全不同**：① **USDY** 是代币化美债票据，有 accumulating 与 **rebasing 两个版本**；② **Ondo Global Markets（QYLDon / JAAAon 等 `*on` 代币）**是代币化美股/ETF，**分红全部自动再投资、绝不发现金**，且**在 Ethereum 上靠"价格上涨"体现、在 Solana 上靠"balance 增加"体现**——这个"同一资产两种机制"是本协议接入的最大坑。

## 1. 基础信息（Master CSV 口径）

| 字段 | USDY（已接入） | QYLDon（待确认） | JAAAon（待确认） |
|------|------|------|------|
| 链 | BNB Chain | BNB Chain | BNB Chain |
| Supply Coins | USDT | USDT | USDT |
| Coins Integrated | USDY | QYLDon | JAAAon |
| 池子 TVL | 2.1B | 100k | 100k |
| 收益率 | 4% | 12% | 5% |
| 类别 | TBills | Equities | Private Credit |
| 产品 | Ondo TBill (USDY) | Global X Nasdaq 100 Covered Call ETF | Janus Henderson AAA CLO |
| 池子类别 | 活期 | 活期 | 活期 |
| 产品网页 | [app.ondo.finance/assets/usdy](https://app.ondo.finance/assets/usdy?tab=buy&fromToken=usdt&toToken=usdy) | https://app.ondo.finance/assets/qyldon | https://app.ondo.finance/assets/jaaaon |
| DD | -（无） | `[W3E] Ondo DD` | `[W3E] Ondo DD` |
| 预算 / POC | $300,000 / Terry（执行：合约） | $250,000 / Terry（执行：产品） | $250,000 / Terry（执行：产品） |

## 2. 协议背景

- **Ondo Finance** 是代币化美债的头部玩家，也是**代币化证券平台 TVL 第一**
- **Ondo Global Markets（GM）**：2025-09 从 Ethereum 起步，上线 **100+ 支美股与 ETF**；数周内 TVL 破 **$350M**、累计链上交易量 $669M+
- **BNB Chain 上线**：Ondo GM 是**首个把大规模代币化美股带到 BNB Chain 的平台**，通过 PancakeSwap 交易；**Binance Wallet 已开放** —— 用户在 App「Markets → Stocks」入口交易，手续费低至 0%
  → 📌 **这意味着我们钱包侧已经有 Ondo 资产的对接经验，可复用**
- **托管/背书**：每个代币化股票由底层证券 100% 背书；**BitGo 与 Coinbase Custody** 负责托管合规；底层由**受监管的美国券商 Alpaca** 买入并托管；按 SEC 备案，代币代表 DTC control account 中证券的**受益权**
- **USDY 规模**：2026 年初供应量超 **$740M**（Solana 占约 35%）
- **多链**：USDY —— Ethereum（原生）→ Solana → Mantle / Sui → Aptos；GM —— Ethereum / BNB Chain / Solana，可桥到 HyperEVM
- ⚠️ **USDY 在 BNB Chain 的部署本次未在公开资料中确认**（CSV 写 BNB），见 9.1

## 3. 底层资产（Underlying）

### USDY
- 每个 USDY 是**可转让数字票据**，代表对发行 LLC 资产的**优先级（senior）分级索偿权**
- 组合（2026-04 口径）：约 **92% 短期美国国债 + 8% 银行存款**（存款部分作为赎回日流动性）
- ⚠️ **重要结构变化**：**2025-12-15 起 Ondo USDY LLC 并入 Ondo Stocks 架构**；且**按发行日期不同**，USDY 的抵押可能是短期美债、**iShares Short Treasury Bond ETF 份额**、或银行活期存款 —— **背书资产并非全体一致**

### QYLDon
- 底层：**Global X Nasdaq 100 Covered Call ETF（QYLD）** —— 持有纳指 100 成分股 + 卖出备兑看涨期权
- ⚠️ **注意与 [DMZ-RWAlpha.md](DMZ-RWAlpha.md) 的策略高度重叠**（都是纳指 covered call），若两个都上线，属于**同质产品**，需要产品侧决策

### JAAAon
- 底层：**Janus Henderson AAA CLO ETF（JAAA）** —— 买入企业 CLO（贷款抵押债券）的 **AAA 级浮动利率分级**
- ⚠️ Master 表把它归为 Private Credit，但底层是**公开市场 ETF 持有的 AAA 级结构化信贷**，风险等级远低于典型私人信贷 → 分类建议调整

## 4. 收益来源（Yield Source）

| 产品 | 收益来源 | 关键机制 |
|------|---------|---------|
| USDY | 组合加权平均收益 **减 25bps 年化管理费**；利息按日计算 | 收益率 = 3 个月美债利率 − 费用。**2026-04 官方 4.65%，同期 RWA.xyz 7 日 APY 3.55%**（口径差异大，见 9.3） |
| QYLDon | QYLD 的期权权利金 + 股息，**全部净额再投资** | **总回报型（total return）**，不发现金 |
| JAAAon | JAAA 的 AAA CLO 浮动利息，**全部净额再投资** | 同上 |

### 🔴 `*on` 代币的分红机制（本文档最重要的部分）

官方明确：**持有人永远不会收到现金分派**。分红在券商账户里收到后（**扣除预扣税**）**买回更多底层股票**，然后通过两种方式之一体现：

| 实现方式 | 链 | 表现 | 我们的解析口径 |
|---------|-----|------|--------------|
| **价格累积（price-accretion）** | Ethereum | token 代表的份额增加 → **token 价格与股价背离** | 需要 NAV/multiplier，**不能拿股价当 token 价** |
| **余额累积（balance-accretion）** | **Solana、部分 CEX** | token 价格 = 每股股价 → **用户 balance 自动增加** | 直接读 balance，NAV 无意义 |

⚠️ **BNB Chain 属于哪种，本次公开资料未明确 → 必须确认（见 9.2）。这是决定 QYLDon/JAAAon 能否正确解析的第一个问题。**

- **Oracle**：Chainlink 的 Ondo feeds = 底层股价（24/5 聚合盘前/盘中/盘后/隔夜）× **`sValue` 乘数**（来自 Ondo 的 `SyntheticSharesOracle` 合约，追踪分红再投资与公司行动调整）
  → 📌 **`sValue` 就是我们要的 NAV 类数据**，且有 Chainlink feed 可用，**取数路径清晰**
  → Oracle 更新规则：**24 小时内 ≤1% 的小幅更新自动生效**，更大调整走另一条（人工/受控）路径 —— 大额特别分派时要注意

## 5. 链上机制与凭证代币

| 项 | USDY | `*on` 代币 |
|----|------|-----------|
| 两个版本 | **USDY（accumulating，价格上涨）** / **rUSDY（rebasing，价格恒 $1、balance 增长）** | 单一代币，机制按链不同（见上表） |
| 价格 | 每日更新赎回价值；2023-08 以 $1.00 发行，现约 **$1.13** | 见上 |
| ⚠️ 漏更新处理 | **若某日价格更新/rebase 漏掉，收益在下次更新时补发**；**更新前赎回不含当日收益** | — |
| 转让 | Reg S 豁免、**仅非美国人士**；初始锁定期后可**无许可二级转让**（不同于 BUIDL 等白名单制） | 有资格投资者 |
| 权利 | 优先级债权 | **仅经济敞口，无股东/投票权** |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | USDY | QYLDon / JAAAon | 说明 |
|------|:---:|:---:|------|
| 实时解析 | ✅ | 🟡 | USDY 直接 balance × price；`*on` 取决于链上机制 |
| 交易历史解析 | ✅ | 🟡 | `*on` 若是 balance-accretion，**分红会表现为 balance 变化而非交易**，历史记录需区分"申购"和"分红增发" |
| 池子自发现 | 🟡 | ❌→🟡 | GM 有 100+ 资产，**必须走注册表/Chainlink feed 列表**，不能硬编码 |
| APY | ✅ | 🟡 | USDY 用官方值；`*on` 需从 `sValue` 变化倒算，**不能用 ETF 官方分红率**（有预扣税折损） |
| NAV 历史 | ✅ / ❌ | 🟡 | **USDY accumulating 版可打点；rUSDY 是 rebasing，NAV 恒 1，不要配 NAV Tab** |
| PNL | ✅ | 🟡 | `*on` 若 balance-accretion，PNL = balance 变化 + 价格变化，两项都要 |

### 6.2 关键取数口径

1. **先确认接的是 USDY 还是 rUSDY**：
   - USDY（accumulating）→ NAV 曲线有意义，balance 不变
   - rUSDY（rebasing）→ **NAV 恒 $1，PRD 的 NAV Tab 对它无意义**，收益全在 balance 里
2. **`*on` 代币优先用 Chainlink feed**：`底层股价 × sValue`，其中 sValue 就是分红再投资乘数
3. **不要拿 QYLD 的公开分红率当 QYLDon 的收益率**：
   - 官方明确"再投资是**扣预扣税后**的"；QYLD 这种**几乎全部回报来自分派**的产品，非美籍持有人的预扣税折损会造成**显著的跟踪偏差**
   - CSV 写 12%，QYLD 官方分红率量级也是 12% 左右 —— **实际到手会低于 12%**，需要按 sValue 实测
4. **美股 5×24 交易**：周末与美股假日 sValue 不变，天级 NAV 打点会出现平段
5. **USDY 漏更新补发机制**：天级快照差分时，某天可能是 0、下一天是 2 天的量，PNL 平滑逻辑要能容忍

### 6.3 需向项目方索取

1. **BNB Chain 上 `*on` 代币是 price-accretion 还是 balance-accretion**（最高优先级）
2. USDY 在 BNB Chain 的部署确认 + 是 USDY 还是 rUSDY
3. QYLDon / JAAAon 的合约地址 + Chainlink feed 地址
4. GM 资产注册表接口（做池子自发现）
5. 历史 sValue 数据
6. 赎回时效（GM 代币赎回为稳定币的路径与到账时间）
7. 各代币的地域准入白名单

## 7. 风险（Risk）

### USDY
1. **利率风险**：收益随短期美债利率浮动，降息周期收益下降
2. **结构变化风险**：⚠️ **按发行日不同，背书资产不同**（美债 / iShares ETF 份额 / 银行存款）；2025-12 起并入 Ondo Stocks 架构
3. **银行存款交易对手风险**（约 8% 仓位）

### QYLDon
4. **上行受限**：covered call 策略，牛市跑输纳指
5. **股市回撤风险**：底层是纳指成分股
6. **预扣税折损**：几乎全部回报来自分派，非美籍持有人跟踪偏差大

### JAAAon
7. **信贷/久期风险**：AAA CLO 违约率极低但非零；浮动利率，降息时收益下降

### 共同
8. **无股东权利**：仅经济敞口，无投票权、无直接证券所有权
9. **托管/券商交易对手风险**：Alpaca（券商）+ BitGo / Coinbase Custody
10. **Oracle 风险**：价格依赖 Chainlink feed + Ondo 的 `SyntheticSharesOracle`
11. **同质化风险（产品侧）**：QYLDon 与 DMZ rAI 策略高度重叠

## 8. 合规与准入（Compliance）

- **USDY**：Reg S 豁免，**仅非美国人士**；锁定期后可无许可二级转让
- **Ondo GM**：面向**亚太、欧洲、非洲、拉美的合格投资者**；**美国、英国、EEA 居民受限**（2026-02 起通过列支敦士登牌照 passporting 向部分 EEA 用户开放）
- **托管**：BitGo、Coinbase Custody；券商 Alpaca（受监管美国 broker-dealer）
- 已按 SEC 备案（代币代表 DTC control account 证券的受益权）

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | **USDY 在 BNB Chain 是否有部署，是 USDY 还是 rUSDY** | Terry / 项目方（**最高优先**，rebasing 与否决定整套解析） |
| 2 | **BNB 上 `*on` 代币是价格累积还是余额累积** | 项目方（**最高优先**） |
| 3 | USDY 收益率官方 4.65% vs RWA.xyz 3.55% 口径差异，前端展示用哪个 | Terry + Marcus |
| 4 | QYLDon 与 DMZ rAI 策略重叠，是否都上 | Marcus（产品决策） |
| 5 | JAAAon 归类 Private Credit 是否准确（底层是公开 ETF 的 AAA CLO） | Marcus |
| 6 | GM 有 100+ 资产，池子自发现是否要做通用方案（未来可能扩到更多 `*on`） | Jeff / Johnson |
| 7 | 「TBills」映射到后台哪个枚举（建议 Bonds） | Marcus |

## 10. 参考链接

- USDY 官方机制（accumulating vs rebasing / 漏更新补发）：https://docs.ondo.finance/general-access-products/usdy/basics
- Ondo Stocks / Global Markets：https://ondo.finance/global-markets
- **Chainlink Ondo GM feeds（sValue 机制，解析必读）**：https://docs.chain.link/data-feeds/tokenized-equity-feeds/ondo
- Ondo GM 上线 BNB Chain（官方）：https://ondo.finance/blog/global-markets-live-on-bnb-chain
- BNB Chain 官方博客：https://www.bnbchain.org/en/blog/ondo-global-markets-brings-100-tokenized-u-s-stocks-etfs-to-bnb-chain
- Binance Wallet 入口 / 280M 用户：https://finance.yahoo.com/news/ondo-brings-100-tokenized-u-215754405.html
- 分红再投资机制说明（价格累积 vs 余额累积）：https://www.bitget.com/academy/ondo-global-markets-faq
- USDY 数据与结构说明：https://eco.com/support/en/articles/14798657-ondo-usdy-tokenized-treasuries-explained
