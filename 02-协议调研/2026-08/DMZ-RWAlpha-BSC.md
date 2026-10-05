# DMZ / RWAlpha — rAI 美股期权收益篮子

> **状态**：✅ 已确认接入（真实解析 7 个之一）｜ 接入情况：Earn 已接
> **调研时间**：2026-07-31 ｜ **解析类型**：**C（现金流分发型，每周派 USDT）** ← 全表唯一
> ⚠️ 「DMZ」是 Master 表里的内部叫法，公开品牌是 **RWAlpha**（sp.rwalpha.ai）

## 0. 一句话结论

RWAlpha 的 rAI 不是"代币化股票"，而是**把美股科技 ETF 的期权权利金变成每周 USDT 现金流**的结构化产品——**收益靠派息而不是 NAV 上涨**，这让它成为本批协议里唯一必须走 PRD「派息信息」Tab 而不是「NAV」Tab 的产品，解析逻辑与其他协议完全不同。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | DMZ（公开品牌：RWAlpha） |
| 链 | BNB Chain |
| Supply Coins | USDT |
| Coins Integrated | rAI |
| 池子 TVL | 100k |
| 收益率 | 14%（官方口径：净分红率约 14%、年化约 17%） |
| 类别 | Stocks |
| 产品 | rAI Stocks basket |
| 产品描述 | 资金投向美股科技 ETF 及 STRC 永续优先股，赚取底层固定股息 + 卖出备兑看涨期权（covered call）的权利金。US Tech ETFs (QQQI, NVDY, etc.) executing covered calls, and STRC perpetual preferred shares |
| 池子类别 | 活期 |
| 产品网页 | https://sp.rwalpha.ai/vault/AI |
| DD 文档 | `DMZ DD (rAI & rSTR)` |
| Onboarding / 预算 | —— / 积分 |
| POC | Terry ｜ 执行：预算 + 合约 ｜ 接入情况：Earn |

## 2. 协议背景

- **平台**：RWAlpha，在 **BNB Chain** 上做 RWA 收益基础设施，主打两个旗舰产品 **rAI** 和 **rAIX**
- **基础设施**：自建 tokenization engine + 多签托管 + 合规工具；与券商 **MooMoo（富途旗下）API 直连**，保证代币 1:1 对应底层资产；NAV 计算、铸造、销毁通过 **Fireblocks** 控制
- **多链扩张**：已与 **Mantle** 合作在 Mantle 链上线联合 RWA vault，Mantle 作为独立验证方参与全流程监督
- **产品矩阵**：
  - **rAI**：锚定纳斯达克 100 期权 ETF + 英伟达期权 ETF，目标净分红率 ~14%、年化 ~17%，**每周 USDT 派息**
  - **rAIX**（rAI Max）：在 rAI 基础上加存储/大科技成长资产，目标净分红率 ~11%、年化 ~37%
  - **rSTR**：见 DD 文档（Master 表 DD 名为 `rAI & rSTR`，本次未接）

## 3. 底层资产（Underlying）

1. **美股科技/AI 主题期权 ETF**：QQQI（Nasdaq-100 covered call）、NVDY（NVIDIA 期权收益）等
2. **STRC 永续优先股**（Strategy 发行的浮动利率优先股）
3. 底层由券商（MooMoo）持有，Fireblocks 控制铸销，Mantle 侧另有独立验证

> **对用户的关键披露**：用户拿的不是股票，也没有股东权利，本质是**期权卖方策略的现金流份额**。

## 4. 收益来源（Yield Source）

| 收益腿 | 说明 |
|--------|------|
| **备兑看涨期权权利金**（主） | 卖出 covered call 收权利金，把标的波动率变成现金流 |
| **底层固定股息** | ETF 分红 + STRC 优先股股息 |

**分发方式**：**每周 USDT 派息**（官方口径 "real returns, weekly dividends"）→ PRD 后台「分发方式」应配 **Continuous / 定期派息**，**不是 Auto-compound**。

> **策略天花板**：covered call 的代价是**放弃标的上涨空间**。牛市里 rAI 会明显跑输直接持有 QQQ，这点必须写进 Risk Tab（否则用户会问"为什么科技股涨了我的收益没变"）。

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | rAI（BNB Chain，ERC-20） |
| 合约地址 | ⚠️ 待补（本次未验证） |
| 收益表达 | **不靠 NAV 上涨**：收益以 **USDT 直接打到用户钱包**（每周） |
| NAV | 有 NAV（Fireblocks 控制的链下计算 + 1:1 对应底层），但**NAV 主要反映标的价格波动，不反映收益** |
| 申购 / 赎回 | USDT 申购；活期赎回（⚠️ 具体是否 T+N、是否需等美股交易日待确认） |

## 6. 数据接入要点（对齐 PRD）— ⚠️ 本协议是特例，请重点看

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | ✅ | rAI balance × NAV | 已接（Earn 侧） |
| 交易历史解析 | 🟡 | mint / redeem 事件 **+ 每周派息转账记录** | **派息流水必须单独解析**，否则历史不完整 |
| 池子自发现 | 🟡 | rAI / rAIX / rSTR 多产品，建议走注册表或硬编码 | |
| APY | 🟡 | **不能用 NAV 差分算**！必须用「派息金额 / 持仓本金」年化 | 与其他协议公式不同 |
| NAV 历史 | 🟡 | 可打点，但 **NAV 曲线 ≈ 美股波动曲线**，对用户意义不同 | 建议详情页**不主推 NAV Tab**，改推「派息信息」Tab |
| PNL | ⚠️ | **必须 = NAV 变化 + 累计派息**，只算 NAV 会严重低估收益 | **本协议最大的 PNL 口径坑** |

### 6.2 关键取数口径（务必与 Jackson / Calvin 对齐）

```
Total Earnings（rAI）= (当前 balance × 当前 NAV − 申购成本) + Σ 历史 USDT 派息
                        └── NAV 部分（可能为负）──┘   └── 派息部分（累加）──┘
```

- **只按 NAV 差分算 PNL 会漏掉全部收益**（因为收益是派出去的，不留在 NAV 里）
- 派息的 USDT 已经进用户钱包、可能已被转走/花掉，所以**必须按历史转账事件累计，不能按当前余额推**
- 美股是 5×24（非 7×24），**周末/美股休市日 NAV 不变**，天级打点会出现连续相同值，PNL 计算要能容忍

### 6.3 需向项目方索取

1. rAI 合约地址 + 派息合约/分发地址（识别派息转账用）
2. 派息事件的链上标识（是标准 Transfer 还是自定义 Distribution 事件）
3. 历史派息记录（金额 + 时间 + 每份额分红）
4. NAV 计算与更新频率（美股收盘后更新？时区？）
5. 官方 APY 口径定义（14% 净分红率 vs 17% 年化 的差别）
6. 赎回时效（是否受美股交易日限制）

## 7. 风险（Risk）

1. **上行受限（Covered Call 固有）**：标的大涨时收益被期权行权截断，会明显跑输直接持有
2. **股票市场风险**：底层是科技股/AI 主题 ETF，回撤时 NAV 直接下跌，**派息不能弥补本金损失**
3. **STRC 永续优先股风险**：无到期日、利率敏感、发行方信用风险
4. **交易对手/托管风险**：底层由券商（MooMoo）持有，依赖 Fireblocks 与多签
5. **流动性/赎回风险**：底层是美股，受交易日与结算周期限制，池子 TVL 仅 100k 量级
6. **收益不保证**：派息率随期权市场波动率变化，波动率下降时权利金收入下降

## 8. 合规与准入（Compliance）

- ⚠️ 公开信息不足。BNB Chain 上的产品页无明显 KYC 门槛，但底层是美股衍生品，**大概率有地域限制（非美国用户）**
- 需以 `DMZ DD (rAI & rSTR)` 为准

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | **PNL 口径必须含派息**（见 6.2） | Jackson / Calvin + Marcus（**本文档最重要的一条**） |
| 2 | 详情页核心指标 Tab 是否为该协议改成「派息信息」而非 NAV | Marcus（PRD 4.5.1 支持多 Tab，正好用得上） |
| 3 | 派息事件的链上识别方式 | 项目方 |
| 4 | 「Stocks」映射到后台哪个资产类型枚举（建议 Equities） | Marcus |
| 5 | 是否同时接 rAIX / rSTR | Terry |
| 6 | 池子 TVL 仅 100k，是否有最低规模要求 | Terry |

## 10. 参考链接

- 产品页：https://sp.rwalpha.ai/vault/AI
- BNB Chain 上线（结构化产品 / rAI & rAIX 收益口径）：https://www.kucoin.com/news/flash/rwalpha-launches-rwa-yield-infrastructure-with-structured-products-on-bnb-chain
- Mantle 联合 vault（MooMoo API / Fireblocks / NAV 机制）：https://www.kucoin.com/news/flash/rwalpha-and-mantle-launch-first-joint-rwa-vault-on-mantle-chain
