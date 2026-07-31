# Huma Finance — PST（PayFi 支付金融）

> **状态**：🟡 待确认
> **调研时间**：2026-07-31 ｜ **解析类型**：A（NAV 累积，收益型 LP 代币）
> **调研质量**：高
> 📌 **本协议在 PRD 里被点名了**：待确认事项#9「特殊协议的 Tax History 数据，需向项目方要 API（**非 Huma 通用方案**）」—— 说明 Huma 已有一套通用方案，其他协议才是特例

## 0. 一句话结论

Huma 是 **PayFi（支付金融）** 协议：把资金借给全球支付机构和实体企业，垫付跨境结算资金、提前兑现真实发票与应收账款，赚**短期融资利息**。Huma 2.0 在 **Solana** 上、**无需 KYC**、PST 是可组合的收益型 LP 代币。Master 表标 **定期 60D**，是本批**唯一的 Locked 产品**——正好用来验证 PRD 的定期产品链路（到期日、Auto Redemption、Auto Roll）。

## 1. 基础信息（Master CSV 口径）

| 字段 | 值 |
|------|-----|
| 协议 | Huma |
| 链 | **SOL（Solana）** |
| Supply Coins | USDC |
| Coins Integrated | PST |
| 池子 TVL | 200M |
| 收益率 | 10% |
| 产品上线 | **x（未上线）** |
| 类别 | Private Credit |
| 产品 | PST |
| 产品描述 | 借给全球支付机构和实体企业，用于垫付跨境结算资金或提前兑现真实发票与应收账款，赚取短期融资利息 |
| 池子类别 | **定期 60D（Locked 60 天）** ← 全表唯一定期产品之一 |
| 产品网页 | https://app.huma.finance/ |
| DD 文档 | `Huma PST DD Wallet DeFi` |
| POC | Terry ｜ 执行：产品 |

## 2. 协议背景

- **Huma 2.0（Permissionless）**：2025-04-09 上线，**只在 Solana**，是 Huma 未来的主线产品；**不要求存款方 KYC/KYB**（除受限国家或 Chainalysis 筛查命中的钱包）
- **两种模式**：
  - **Classic Mode** → 铸 **PST**（PayFi Strategy Token）：追求稳定的两位数 USDC 收益（**按月更新**）+ Huma Feathers 积分
  - **Maxi Mode** → 铸 **mPST**：更偏积分
- **锁仓机制**：**不强制锁仓**，但可**自选 3 个月 / 6 个月**期限来大幅提升 Feathers 积分倍数
  → ⚠️ **与 CSV 的「定期 60D」不完全对应**（官方是 3/6 个月的可选积分加成，不是 60 天锁定）见 9.1
- **规模与背书**：累计处理交易量 **$38 亿+**；战略伙伴含 Solana、Circle、Stellar Development Foundation、Galaxy Digital
- **组合性**：PST 可在 **Jupiter** 换回 USDC、在 **Kamino** 作抵押、在 **RateX** 交易未来收益
- ⚠️ **安全事件（必须披露）**：Huma 已废弃的 **V1 BaseCreditPool 合约（Polygon）在清退过程中被攻击，损失约 $101,400 USDC/USDC.e**；官方称 Solana 上的 PayFi V2 未受影响，Polygon 上剩余 V1 合约已全部暂停
  → 📌 **对我们的意义**：只接 Solana V2，但 DD 里要问清 V1 事件的完整复盘

## 3. 底层资产（Underlying）

- **跨境支付结算垫资**：支付机构在清算周期内的资金缺口
- **真实发票 / 应收账款提前兑现**：企业把真实发票折价提前变现
- 📌 特点：**期限极短（通常几天到几周）**，资产周转快 —— 这是 PayFi 相比其他私人信贷的核心区别（久期风险小）

## 4. 收益来源（Yield Source）

| 收益腿 | 说明 |
|--------|------|
| 短期融资利息（主） | 支付机构/企业为垫资付的利息 |
| Huma Feathers 积分 | 非现金收益，可选锁仓（3/6 月）提升倍数 |

**收益率更新频率**：官方口径 **按月更新（updated monthly）** → APY 曲线是**月度阶梯状**，不是连续曲线。

## 5. 链上机制与凭证代币

| 项 | 内容 |
|----|------|
| 凭证代币 | **PST（Solana SPL）**；Maxi 模式为 mPST |
| 合约/mint 地址 | ⚠️ 待补 |
| 收益表达 | 收益型 LP 代币（NAV 累积型） |
| 赎回 | 官方"不要求锁仓"；⚠️ 但 CSV 写定期 60D，且 **Jupiter 二级可即时换 USDC** |
| 组合性 | Jupiter / Kamino / RateX |

## 6. 数据接入要点（对齐 PRD）

### 6.1 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | 🟡 | PST balance × NAV | **需 Solana 解析能力** |
| 交易历史解析 | 🟡 | Solana 指令解析 | PRD#9 暗示 Huma 有"通用方案"，可能已有现成能力 |
| 池子自发现 | 🟡 | Classic / Maxi 两个池 | |
| APY | ✅ | 官方按月更新值 / NAV 差分 | 月度阶梯 |
| NAV 历史 | 🟡 | 天级打点 | |
| PNL | 🟡 | 天级快照差分 | |
| **Tax History** | 📌 | **PRD 待确认事项#9 提到「Huma 通用方案」** | 需找 Jaden 确认这个"通用方案"具体是什么，能否复用 |

### 6.2 关键取数口径

1. 🔴 **Solana 解析能力**（同 [OnRe.md](OnRe.md)）：与"EVM 为主"的基线冲突
2. 📌 **定期产品链路验证**：若确认是 Locked 60D，本协议是**唯一能验证 PRD 定期链路的产品** ——
   - 到期日（Maturity Date）展示
   - Auto Redemption 开关（PRD 4.5.2）
   - **Auto Roll 开关**（PRD 标注 *TODO: 确认是否支持*）→ Huma 的机制能不能支持自动复投，需要确认
   - 多期产品的 Earned 计算（**PRD 4.4.4 直接点名了 "Huma PST 有 2-3 期"**，每期独立计算）
3. **多期 Earned 计算**：PRD 明确要求"通过天级快照按时间段聚合查询" —— Huma 是这个逻辑的**首个适用协议**

### 6.3 需向项目方索取

1. PST mint 地址 + Classic/Maxi 两池地址
2. **NAV 取数方式**（Solana program state / oracle）
3. **锁仓机制的真实条款**：是 60 天硬锁定，还是"自选 3/6 月积分加成"？
4. 是否支持到期自动复投（Auto Roll）
5. 历史 NAV / 月度利率数据
6. **Tax History API**（PRD#9 的"Huma 通用方案"具体是什么）
7. V1 安全事件复盘报告

## 7. 风险（Risk）

1. **借款人违约风险**：支付机构 / 企业的信用风险；发票造假风险（虽标榜"真实发票"）
2. **期限错配风险**：底层资产期限短是优势，但若集中赎回仍可能挤兑
3. ⚠️ **历史安全事件**：Polygon 上废弃 V1 合约被攻击（约 $10.14 万）。官方称 Solana V2 架构独立且未受影响，但反映团队在旧合约退役管理上曾有疏漏
4. **Solana 链风险 + 组合层风险**（Jupiter/Kamino/RateX）
5. **积分（Feathers）不确定性**：Feathers 的最终价值取决于代币经济学，**不应作为收益承诺展示给用户**
6. **无 KYC 的另一面**：合规筛查仅依赖 Chainalysis 钱包筛查

## 8. 合规与准入（Compliance）

- **Huma 2.0 不要求存款方 KYC/KYB**（除受限国家 / Chainalysis 命中钱包）→ 对钱包场景友好
- 战略伙伴含 Circle、Stellar Development Foundation、Galaxy Digital
- 需以 `Huma PST DD Wallet DeFi` 为准

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | **「定期 60D」的依据**：官方是 3/6 月可选积分加成，不是 60 天硬锁定 —— 是我们定制的期限？ | Terry / 项目方（**优先**，定期 vs 活期决定整套前端链路） |
| 2 | 🔴 中台 Solana 解析能力 | Jeff / Johnson |
| 3 | **PRD#9 的「Huma 通用方案」具体指什么，能否复用到其他协议** | Jaden（**优先**，可能省下大量工作） |
| 4 | 是否支持 Auto Roll（PRD 4.5.2 TODO） | 项目方 |
| 5 | 多期产品（PRD 说 PST 有 2-3 期）的期次定义与切换机制 | Terry + Marcus |
| 6 | Feathers 积分是否要在前端展示（涉及 Campaign Bonus 字段） | Marcus |

## 10. 参考链接

- 产品页：https://app.huma.finance/
- Messari 深度报告：https://messari.io/report/understanding-huma-finance-a-comprehensive-overview
- Huma 2.0 上线 Solana（Classic/Maxi、PST/mPST、锁仓加成）：https://chainwire.org/2025/04/10/huma-finance-2-0-launches-on-solana-bringing-composable-real-yield-to-defi-users/
- PST 数据：https://www.coingecko.com/en/coins/payfi-strategy-token
- ⚠️ V1 安全事件：https://crypto.news/huma-finance-legacy-v1-contract-on-polygon-exploited-for-101400-usdc/
