# OpenTrade — PRIME+ Vault

> **状态**：✅ 已确认接入（真实解析 7 个之一）｜ 接入情况：Earn
> **调研时间**：2026-07-31 ｜ **解析类型**：A（NAV 累积，逐日计息复利）
> **调研质量**：中高（官方 docs 完整，但 PRIME+ 本身是 Curation+ 定制产品、公开细节少）

## 0. 一句话结论

OpenTrade 是**给 fintech / 新银行做"稳定币生息"后端**的英国公司；PRIME+ 是它 **Curation+（定制策展）** 线上的产品——一个**动态再平衡**的组合（RWA + 蓝筹 DeFi 策略），主打**固定利率 USD 收益 + 每日流动性**。它的价值在于"**利率稳定 + T+0/同日赎回**"，是本批协议里**赎回体验最好**的之一。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | OpenTrade |
| 链 | Ethereum |
| Supply Coins | USDC |
| Coins Integrated | PRIME+ |
| 池子 TVL | **0**（CSV，⚠️ 说明产品刚开或专为我们新开，见 9.1） |
| 收益率 | 6% |
| 类别 | Money Market Fund |
| 产品 | PRIME+ Vault |
| 产品描述 | 货币市场基金、短期国债、投资级企业债 ETF、商业票据及私人信贷等高流动性固收工具，赚取固定利息收益 |
| 池子类别 | 活期 |
| 产品网页 | https://primeplusvault.lovable.app/ ⚠️（lovable.app 是快速建站域名，**不是 opentrade.io 主域**，需确认是否为官方正式页面） |
| DD 文档 | `[W3E] OpenTrade PRIME+ DD` |
| Onboarding / 预算 | Projects' campaign info / $300,000 |
| POC | Cece ｜ 执行：产品 + 合约 ｜ 接入情况：Earn |

## 2. 协议背景

- **公司**：OpenTrade，总部伦敦。定位是**稳定币收益基础设施**——服务对象是 fintech、新银行、平台，而不是散户
- **融资**：**$17M** 战略轮（Mercury Fund + Notion Capital 领投，a16z crypto、AlbionVC、CMCC Global 参投），累计融资 **$30M+**
- **规模**：TVL **突破 $200M**；2025 年交易量超 $250M，预计 **2026 年底交易量破 $1B**
- **产品线**：
  - 标准线：USD Money Market Fund Vault、**Franklin Templeton Benji MMF Vault**、Flexible Term USDC Vault、Flexible Term EURC Vault、High Yield Corporate Bond Vault（6 个月平均 **10.59%**）
  - **Curation+（PRIME+ 所属）**：给"有特定收益目标 / 风险参数 / 复杂抵押品要求 / 混合 RWA 头寸"的客户做定制
- **协议版本**：Flexible Term 系列跑 **V4**，USD MMF 跑 **V5**（V5 支持更灵活的资金流、更简单的可升级合约、**单一 vault 类型支持多种汇率方法**）
  → ⚠️ **PRIME+ 跑哪个版本会影响 NAV 取数方式**（V5 支持"多种汇率方法"这点尤其要问清）
- **生态外延**：Sierra Protocol 的 SIERRA 流动性收益代币，底层就是 OpenTrade 策展的 vault（MMF + 商业票据 + 贸易融资）
- 📌 **交叉引用**：[Ember.md](Ember.md) 的循环贷标的里有 "PRIME"，需确认是否指本产品；[Nest-Credit.md](Nest-Credit.md) 的 FALX vault 也是"与 OpenTrade 合作部署在 Plume"

## 3. 底层资产（Underlying）

PRIME+ 是**动态再平衡组合**，官方描述为 "RWA + 蓝筹 DeFi 策略"，CSV 描述的具体标的：

1. 货币市场基金（MMF）
2. 短期美国国债（T-Bills）
3. 投资级企业债 ETF
4. 商业票据（Commercial Paper）
5. 私人信贷（Private Credit）

**参考同类 vault 的底层**（可用于推断风控严格程度）：
- Flexible Term USDC Vault：**只有**高流动性美国国债 + USD MMF，目标是"USD 无风险利率"，利率挂 **4 周国库券贴现日利率减费用**
- Franklin Templeton Benji Vault：**只用** BENJI（富兰克林邓普顿代币化 MMF）作底层

> **PRIME+ 的定位介于两者之间**：比纯 T-Bill vault 收益高（6% vs ~4%），因为掺了企业债/商票/私人信贷；比 High Yield Corporate Bond vault（10.59%）保守。

## 4. 收益来源（Yield Source）

- **固收票息**：上述五类工具的利息收入
- **机制特点**：官方对 Curation+ / PRIME+ 的核心卖点是 **"fixed-rate USD yield with daily liquidity"** —— **固定利率**（客户不承担利率波动）+ **每日流动性**
- **计息**：参考同类 vault —— **利息按日累积并复利（accrues and compounds daily）**

→ PRD 后台「分发方式」配 **Auto-compound**；「赎回处理时间」可填**同日 / 约 6 小时**（见下）

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | PRIME+（⚠️ 实际 symbol 待确认，可能是 vault share token） |
| 合约地址 | ⚠️ 待补 |
| 收益表达 | 按日复利 → **share 汇率上涨**（NAV 累积型） |
| 赎回 | 参考 Flexible Term USDC Vault：**当日截止时间前提交的赎回，当日到账，通常 6 小时内** ⚠️ PRIME+ 具体条款待确认 |
| 协议版本 | V4 / V5 待确认（影响汇率方法） |
| 链 | Ethereum（CSV）；OpenTrade 也在 permissioned 与 permissionless 系统上都有部署 |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | ✅ | share balance × 汇率 | 已在真实解析清单内 |
| 交易历史解析 | ✅ | 存入 / 赎回事件 | 赎回是**同日结算**，两段式事件较简单 |
| 池子自发现 | 🟡 | OpenTrade 有多个 vault，是否有 Factory 待确认 | |
| APY | ✅ | **固定利率产品**，可直接取公示利率；或按 NAV 差分自算 | 固定利率 → **APY 曲线会很平**，前端 1W 视图几乎是直线，属正常 |
| NAV 历史 | ✅ | 天级打点汇率 | **按日复利 → 天级颗粒度天然匹配** |
| PNL | ✅ | 天级快照差分 | 固收产品，**基本不会负值**（除底层信用事件） |

### 6.2 关键取数口径

- **V5 "单一 vault 类型支持多种汇率方法"是风险点**：必须确认 PRIME+ 用的是哪种汇率方法（是 ERC-4626 `convertToAssets`？还是链下推送的 exchange rate？），否则解析可能取到错的字段
- **固定利率的含义要确认**：是"利率锁定一段时间"还是"完全固定"？如果是"每个周期重定价"，APY 曲线会是阶梯状
- **TVL 为 0**：说明池子可能是**为我们新开的**，意味着 ① 无历史 NAV 可回溯 ② 上线初期 NAV=1，PNL 从零开始（对我们其实是好事，见 9.1）

### 6.3 需向项目方索取

1. PRIME+ vault 合约地址 + 协议版本（V4/V5）+ **汇率取数方法**
2. 是否标准 ERC-4626
3. 赎回条款：截止时间（cutoff）、到账时效、是否有额度限制
4. 固定利率的重定价周期
5. 费用结构（管理费 / performance fee 是否已扣在 NAV 里）
6. 产品页正式域名（primeplusvault.lovable.app 是否官方）
7. 底层组合权重与再平衡频率（详情页 Underlying Tab 要写）

## 7. 风险（Risk）

1. **信用风险**：底层含**投资级企业债、商业票据、私人信贷**——比纯国债 vault 风险高，企业违约会影响本金
2. **私人信贷流动性风险**：私人信贷本身不流动，若赎回集中，"每日流动性"承诺依赖 vault 的流动性层设计
3. **动态再平衡 = 策展人风险**：组合权重由 OpenTrade 决定，用户无法控制
4. **固定利率的另一面**：利率上行周期，用户拿不到上涨的市场利率
5. **智能合约风险**
6. **规模风险**：CSV TVL 为 0，新池子无历史表现记录
7. **交易对手风险**：OpenTrade 作为策展/运营方

## 8. 合规与准入（Compliance）

- ⚠️ 公开信息不足。OpenTrade 的客户是 fintech / 新银行（B2B2C），**产品原本不是面向散户直接开放**
- 在 **permissioned 与 permissionless 系统上都有部署** —— 需确认我们接的这个 vault 是哪种（permissioned 会有白名单）
- 需以 `[W3E] OpenTrade PRIME+ DD` 为准

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | **TVL=0：这个 vault 是否为 Binance 专门新开？** 若是，则无历史 NAV 需要回溯（**能直接消掉 PRD 待确认事项#1 的一部分压力**） | Cece（**优先，对 Scott/Linnea 的 NAV 回溯工作量有直接影响**） |
| 2 | vault 是 permissioned 还是 permissionless（是否白名单） | Cece / 项目方 |
| 3 | V4/V5 + 汇率取数方法 | 项目方 |
| 4 | 「Money Market Fund」映射到后台哪个枚举（建议 Bonds 或申请新增） | Marcus |
| 5 | primeplusvault.lovable.app 是否官方正式页面 | Cece |
| 6 | Ember 循环贷里的 "PRIME" 是否就是本产品 | Cece（同时问 Ember 与 OpenTrade） |

## 10. 参考链接

- 官方文档 - 平台总览：https://docs.opentrade.io/welcome-to-opentrade/platform-overview
- 官方文档 - 稳定币收益金库：https://docs.opentrade.io/stablecoin-yield/stablecoin-yield-vaults
- 官方文档 - MMF 金库：https://docs.opentrade.io/stablecoin-yield/stablecoin-yield-vaults/money-market-fund-vaults
- 官方文档 - Flexible Term USDC Vault（赎回时效 / 计息机制参考）：https://docs.opentrade.io/stablecoin-yield/stablecoin-yield-vaults/money-market-fund-vaults/flexible-term-usdc-vault
- 官方文档 - 利率与费用：https://docs.opentrade.io/stablecoin-yield/stablecoin-yield-vaults/money-market-fund-vaults/flexible-term-usdc-vault/interest-rate-and-fees
- 官网：https://www.opentrade.io/
- 产品页（待确认官方性）：https://primeplusvault.lovable.app/
- $17M 融资 / TVL $200M：https://www.theblock.co/post/400187/opentrade-raises-17m-to-expand-stablecoin-yield-infrastructure-after-topping-200m-tvl
