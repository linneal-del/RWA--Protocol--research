# Theo — thUSD / sthUSD（黄金 delta 中性收益稳定币）

> **状态**：🟡 待确认（但 CSV「实时数据」栏标 ✅，接入情况标 "Earn"，实际进度可能更靠前，见 9.1）
> **调研时间**：2026-07-31 ｜ **解析类型**：A（NAV 累积）
> **调研质量**：高（多家主流媒体 + 专业研究报告）

## 0. 一句话结论

Theo 是**前 Optiver 交易员创立**的团队，thUSD 是"**黄金现货多头 + CME 黄金期货空头**"的 delta 中性收益稳定币——不赌金价，赚 **contango（期货升水）展期收益 + 实体黄金借贷利息 + 美债**。选黄金而不是加密基差的理由很硬：**黄金衍生品未平仓量是 BTC 的 39 倍**，规模化时不会像加密基差那样压垮资金费率。质押 thUSD 得 sthUSD，**解质押排队平均约两周**。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | Theo |
| 链 | BNB Chain |
| Supply Coins | **thUSD**（注意：申购币种是 thUSD 本身，不是 USDT） |
| Coins Integrated | sthUSD |
| 池子 TVL | 100M |
| 收益率 | 5%（第三方口径 sthUSD 基础 APR 5.40%；策略 2025 年均 8.27%；官方目标 5–12%） |
| 产品上线 | **x（未上线）** |
| 类别 | Gold ⚠️（CSV 归类 Gold，但产品本质是"美元本位收益产品"，见 9.2） |
| 产品 | Staked thUSD |
| 池子类别 | **活期 & 定期 90D**（两种都有） |
| 产品网页 | https://app.theo.xyz/ |
| DD 文档 | `2026_06_Theo` |
| Onboarding / 预算 | Projects' campaign info / **$500,000**（全表最高） |
| POC | Terry ｜ 执行：产品 ｜ 接入情况：**Earn** ｜ 实时数据：**✅** |

## 2. 协议背景

- **团队**：前 **Optiver**（顶级期权做市商）交易员创立，联创 Ari Pingle、CIO Iggy Ioppe
- **Genesis Vault**：**$100M 额度 24 小时内打满**（注意：这是**支持 thUSD 发行的存款**，不是股权融资）
- **三层产品栈**：
  - **thBILL** —— 代币化美债篮子，通过**渣打银行（Standard Chartered）旗下 Libeara** 基础设施发行，**Wellington Management** 与 **FundBridge Capital** 管理；TVL **$200M+**，累计交易量 $10 亿+，15+ 集成
  - **thGOLD** —— **MG999 链上黄金基金**的份额，该基金把实体黄金借给零售商，在金价敞口之上产生约 **2% 年化**
  - **thUSD** —— 上面两者组合 + CME 期货空头对冲后的收益型稳定币；**sthUSD** 是其质押版
- **监管角度**：**GENIUS Act 限制支付类稳定币发行人向持有人分配储备资产收益**；Theo 的论证是 thUSD 的收益来自**底层交易与资产结构**，而非发行人支付利息 → ⚠️ **这是一个法律定性论证，不是监管认可，DD 里要重点看**

## 3. 底层资产（Underlying）

```
thUSD 铸造
  ├─ 买入代币化黄金（thGOLD → MG999 链上黄金基金）
  │    └─ 实体黄金借给零售商（新加坡 Mustafa Gold 等），零售商以库存质押
  │         └─ FundBridge Capital 运营，Libeara 基础设施，20% 首损缓冲
  ├─ 同时在 CME 做空黄金期货（剥离金价方向敞口）
  └─ 短期美债（T-Bills）+ 加密 delta 中性策略作为流动性与收益缓冲
```

## 4. 收益来源（Yield Source）

| 收益腿 | 说明 | 风险性质 |
|--------|------|---------|
| **Contango 展期收益**（主） | 黄金期货通常对现货升水（反映储存与保险成本），持续滚动空头即机械捕获这一升水 | 市场型；⚠️ **若转为 backwardation（现货升水），展期收益变负** |
| **实体黄金借贷利息** | 定向放贷给 Mustafa Gold 等零售商，约 2% 年化，库存质押 + 20% 首损缓冲 | **单一交易对手信用风险** |
| **短期美债 + 加密中性策略** | 流动性与收益缓冲 | 低 |

**业绩**：官方称策略 2025 年平均年化 **~8.27%**，且**全年月度收益均为正**；目标区间 **5–12%**（视黄金市场状况）。

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | **sthUSD**（质押 thUSD 获得） |
| 合约地址 | ⚠️ 待补 |
| 收益表达 | NAV 累积（sthUSD/thUSD 汇率上升） |
| **解质押** | 🔴 **赎回队列，平均约两周** |
| 池子类别 | 活期 & **定期 90D** 两种都有 → 后台需配成**两个投资品** |
| 链 | BNB Chain（CSV） |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | ✅ | sthUSD balance × 汇率 | CSV「实时数据」已标 ✅ |
| 交易历史解析 | 🟡 | stake / unstake 事件 + **解质押队列** | 两周队列 → 需 request ID |
| 池子自发现 | 🟡 | 活期 + 定期 90D 两个池 | |
| APY | ✅ | 汇率差分自算 | 官方口径多（5 / 5.40 / 8.27 / 5-12%），**必须自算** |
| NAV 历史 | 🟡 | 天级打点 | |
| PNL | 🟡 | 天级快照差分 | **美元本位、正常情况非负**（delta 中性） |

### 6.2 关键取数口径

1. ✅ **好消息：thUSD 是美元本位** —— 虽然策略用黄金，但**金价敞口已被期货空头对冲**，所以 PNL 是干净的美元收益，**不像 [Unitas.md](Unitas.md) 的 XGLD 会混入金价波动**
   → ⚠️ 因此 **CSV 把 Theo 归到「Gold」类别会误导用户**（用户会以为是黄金敞口产品），见 9.2
2. **两个池子（活期 / 定期 90D）必须分开配**：不同 APY、不同锁定期、不同赎回条款
3. **解质押两周队列** → 需 request ID + 赎回中状态
4. **Supply Coin 是 thUSD 而不是 USDT** → 用户要先拿到 thUSD。**这多了一步**：钱包侧是否需要先做 USDT→thUSD 的铸造/兑换？这会影响申购流程（PRD 4.6.1）→ 见 9.3

### 6.3 需向项目方索取

1. thUSD / sthUSD 合约地址（BNB Chain）
2. **USDT → thUSD 的铸造路径**（是否有官方 mint 合约、是否需 KYC、有无滑点）
3. 解质押队列的链上事件 + 预计等待时间查询
4. 定期 90D 产品的合约与到期机制
5. 历史汇率数据
6. contango 收益的实际历史（回撤月份表现）

## 7. 风险（Risk）

1. 🔴 **单一交易对手集中风险（最突出）**：黄金借贷腿集中在**单一零售商 Mustafa Gold**，缓释手段只有**库存质押 + 20% 首损缓冲**
2. 🔴 **Contango 不保证**：黄金可能进入 **backwardation**，此时展期收益**变为负**，直接吃掉收益
3. **赎回流动性风险**：解质押队列平均约两周
4. **基差/对冲执行风险**：期货空头需持续滚动，保证金管理失误可能造成损失；极端行情下期货与现货短暂脱钩
5. **监管定性风险**：GENIUS Act 对支付类稳定币分配收益的限制；Theo 的"收益来自资产结构而非发行人付息"是**自我论证**
6. **多层嵌套**：Libeara / FundBridge / Wellington / MG999 基金 / CME 清算 —— 链条长，任一环节出问题都传导
7. **加密中性策略腿**：作为缓冲的加密 delta 中性策略仍有资金费率风险

## 8. 合规与准入（Compliance）

- **机构化程度高**：渣打银行旗下 Libeara 基础设施、Wellington Management、FundBridge Capital（新加坡）
- ⚠️ **GENIUS Act 相关定性存在争议**（见风险 5）
- ⚠️ 具体投资者准入未在公开资料中明确 → 以 `2026_06_Theo` DD 为准

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | **CSV 里「产品上线 = x（未上线）」但「接入情况 = Earn」「实时数据 = ✅」** —— 实际进度到哪了？ | Terry（**优先**，状态自相矛盾） |
| 2 | **类别归到「Gold」是否会误导用户**（产品实为美元本位 delta 中性，无金价敞口） | Marcus（**优先**，影响资产类型标签与用户预期） |
| 3 | **申购币种是 thUSD 不是 USDT** —— 钱包侧申购流程要不要加一步兑换？ | Marcus + Terry（影响 PRD 4.6.1） |
| 4 | 活期与定期 90D 两个产品是否都上 | Terry |
| 5 | 解质押队列两周，池子类别标"活期"是否合适 | Marcus |
| 6 | 预算 $500,000 是全表最高，优先级是否相应最高 | Terry |

## 10. 参考链接

- 官网：https://theo.xyz/ ｜ 产品页：https://app.theo.xyz/
- thUSD Genesis 计划（官方）：https://theo.xyz/articles/the-thusd-genesis-program-is-live
- **OAK Research 深度分析（首个收益型黄金背书稳定币解剖）**：https://oakresearch.io/en/analyses/innovations/theo-th-usd-anatomy-first-yield-bearing-gold-backed-stablecoin
- Decrypt（黄金期货 / $100M）：https://decrypt.co/361292/theo-gold-futures-yield-bearing-stablecoin-100-million-raise
- Aleare Research（市场中性收益结构 / thBILL / thGOLD）：https://alearesearch.substack.com/p/theo-network-market-neutral-stablecoin
- 官方推文（delta 中性黄金策略说明）：https://x.com/Theo_Network/status/2031048704661029142
- 前 Optiver 团队 / Genesis Vault：https://techfundingnews.com/theo-100m-genesis-vault-thusd-gold-stablecoin/
- 价格数据：https://www.coinbase.com/en-sg/price/theo-usd
