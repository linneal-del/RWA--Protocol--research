# Fragmetric —— fragSOL（Solana Restaking，底层 Jito Restaking）

> ⚠️ **范围声明**：本协议**不属于**本仓库原本的 RWA 7 协议范围，来自 **DeX 行情接链项目**（协议表条目 `SOLFragmetric`）。
> **状态**：✅ restake / unstake 双向实测均已拿到链上样本（本批 4 个协议里最完整的一个）
> **实测时间**：2026-08-10 ｜ **测试钱包**：Solana `9Gu4W2YUYCRMXiZWrD5ExF77PAS6X3Mfa6kwSmc9MhgS`（Solflare）
> **本页可信度**：链上部分为 `getTransaction` 逐笔实测；UI 部分为操作当时截图读取（原图未落盘）
> **解析类型**：A（NAV 累积，1 fragSOL = 1.13198 SOL）

## 0. 一句话结论

fragSOL 是 **Fragmetric 在 Jito Restaking 之上的 restaking 凭证**，NAV 累积型（1 fragSOL ≈ 1.132 SOL）。实测最关键的发现是 **赎回是两段式的，且 unstake 那一笔链上同时出现 `Burn` 和 `MintTo`** —— 烧掉 fragSOL、铸出一个提现凭证，**只看到 Burn 会以为用户资产凭空消失**。

## 1. 基础信息（实测口径）

| 字段 | 值 |
|------|-----|
| 协议 | Fragmetric（app.fragmetric.xyz） |
| 链 | Solana |
| 本次实测产品 | **fragSOL** |
| 底层协议 | 🔴 **Jito Restaking**（产品页 fragSOL Info 明确标注 Protocol = Jito） |
| Supply Coins | SOL（页面 Select a Token 可选，本次选 SOL） |
| Coins Integrated | **fragSOL** |
| TVL | **$4.77M**（2026-08-10 UI 快照，数字有动画模糊，需复核） |
| 收益率 | **fragSOL APY 5.55%**（2026-08-10 UI 快照） |
| fragSOL 价格 | **$85.42**（UI 快照）→ ⚠️ 注意是**每 fragSOL 约 85 美元**，不是 $1 量级 |
| 池子类别 | **定期性质**：unstake 约 1 天，最长可能 3 epochs（≈6 天） |
| 产品网页 | https://app.fragmetric.xyz |
| 接入情况 | 待定（DeX 项目侧口径） |

## 2. 协议背景

⬜ **未做**。UI 观察到的产品结构：左侧导航 **Restake / Unstake / Rewards / DeFi / Wrap / FRAG² / FragStats**，另有 **Stake $FRAG** 入口。
Rewards 面板列出两类：**Fragmetric** 自身奖励 + **(Re)staking** 奖励（图标显示 3 个来源）。

## 3. 底层资产

⬜ **未做**。已知底层是 **Jito Restaking**，具体 NCN（Node Consensus Network）清单未取（FAQ 有 "What are NCN Rewards?" 条目可展开）。

## 4. 收益来源

| 收益腿 | 来源 |
|--------|------|
| SOL 质押收益 | 经 Jito Restaking |
| **NCN / (Re)staking rewards** | UI Rewards 面板列出 3 个来源图标 |
| **Fragmetric 自身奖励** | UI Rewards 面板（可能是积分/FRAG） |

⚠️ 均为 UI 观察，未拆分占比。**注意页面有 FRAG 代币激励体系（FRAG² / Stake $FRAG）**，若含积分/空投预期，PNL 口径需单独讨论。

## 5. 链上机制与凭证代币（✅ 实测）

### 5.1 地址清单

| 项 | 地址 | 备注 |
|----|------|------|
| **fragSOL mint** | `FRAGSEthVFL7fdqM8hxfxkfCZzUvmg21cqPJVvC1qdbo` | 本次实测确认 |
| **wFRAG mint** | `WFRGSWjaz8tbAxsJitmbfRuFV2mSNwy7BMWcCwaA28U` | 治理/激励代币（wrapped FRAG），本次顺带买入 |

### 5.2 🔴 restake 与 unstake 的指令序列

**restake（存入）**：
```
Instruction: OperatorLogMessage              ← 🔴 协议自带的 operator 日志（含 traceparent 链路追踪 ID）
CreateIdempotent / InitializeAccount3        ← 建 fragSOL 账户
Instruction: UserCreateFundAccountIdempotent ← 建用户 fund 账户
Instruction: UserCreateRewardAccountIdempotent ← 🔴 建用户 reward 账户（奖励是独立账户，不在 token balance 里）
Instruction: UserDepositSol                  ← 真正的存入
```

**unstake（赎回-请求）**：
```
Instruction: OperatorLogMessage
Instruction: UserCreateFundAccountIdempotent
Instruction: UserCreateRewardAccountIdempotent
Instruction: UserRequestWithdrawal           ← 🔴 只是「请求」，不是到账
Instruction: Burn                            ← 烧掉 fragSOL
Instruction: MintTo                          ← 🔴 铸出提现凭证（这个别漏）
```

### 5.3 汇率（三方吻合）

| 来源 | 数值 |
|------|------|
| UI restake 面板 | 1 SOL ≈ **0.8834** fragSOL |
| 链上实测反推 | 0.0001 SOL → **0.000088341** fragSOL → **0.88341** |
| UI unstake 面板（反向） | 1 fragSOL ≈ **1.13198** SOL（≈ 1/0.8834 = 1.13198 ✅） |

**→ 三者完全吻合，NAV 反推口径可靠。**

## 6. 数据接入要点

### 6.1 ✅ 实测交易全集（2026-08-10，UTC）

| # | 时间 | 操作 | 交易类型 | signature | 链上结果 |
|:---:|------|------|---------|-----------|---------|
| 1 | 08:23:28 | Restake 0.0001 SOL | 🔴 **申购（即时 mint）** | `5NhViCJEU9kDR78TCWSExh6RCWKvNpNUYoDpgaYBtuoT5KtBdPMVSg2QnFu55hboqwF84Qi21R9HCHY8rkUodCbi` | slot 438366719 ｜ fee 0.000145 SOL<br>指令 `UserDepositSol`<br>fragSOL **0 → 0.000088341** |
| 2 | 08:24:10 | Unstake 0.00001 fragSOL | 🔴 **赎回-请求（非到账）** | `4koxwRBawnRLKRHRWijnGJL9kemgSikNQTk8hasKeMAnc2EKRf4nS51etc4PBLaUvYZfV3SroUBLdEEQhUH1cpn2` | slot 438366819 ｜ fee 0.000109 SOL<br>指令 `UserRequestWithdrawal` + **`Burn`** + **`MintTo`**<br>fragSOL **0.000088341 → 0.000078341** |
| 3 | 08:39:23 | 买入 wFRAG | **二级市场 Swap（非申赎）** | `3nEtrwCyVfQrVW4xjofP8GuVDpFWUNeXSXJYhKixc9igseMrv9NjW1UFUQsycJnaK1bPmGxa4paVE4y1rSwMHwUH` | 指令 `WrapSol` → `Swap` ×2 → `Buy`（经 Raydium CLMM）<br>wFRAG **0 → 0.000935179** |

### 6.2 🔴 给解析同学的四条规格

| # | 规格 | 说明 |
|:---:|------|------|
| **1** | 🔴 **unstake 里的 `MintTo` 不能漏** | 一笔 unstake 同时 `Burn` fragSOL + `MintTo` 提现凭证。**只看 Burn 会判定为「用户资产减少且无对价」**，实际用户拿到的是一张排队中的提现凭证 |
| **1b** | 🔴 **别把这套规则套到其他 Jito Restaking 协议上** | 2026-08-10 实测 [Renzo ezSOL](Renzo.md#54--solana-侧ezsoljito-restaking--赎回机制与-fragmetric-完全不同) —— **同样跑在 Jito Restaking 上，赎回却是 `EnqueueWithdrawal` + 份额转入 ticket PDA，既不销毁也不铸凭证**。<br>Fragmetric：读提现凭证代币余额即可；ezSOL：**必须枚举 Jito Vault 程序下的 ticket 账户**。**「同底层 = 同机制」的假设不成立，每个协议都要单独实测** |
| **2** | 🔴 **赎回是两段式** | UI 明确分 **Unstake / Withdraw 两个 tab**，文案：*"fragSOL unstaking usually takes 1 day, may require 3 epochs (6 days) depending on operations"*、*"Unstake at the station to withdraw principal"* → **必须有「赎回中」状态 + 预计到账时间**，且 claim 是**第二笔独立交易**（本次未做，见 §9） |
| **3** | **奖励在独立账户里** | `UserCreateRewardAccountIdempotent` 说明奖励记在**专门的 reward account**，**不在 fragSOL 的 token balance 里**。只读 token 余额会漏掉全部未领取奖励 |
| **4** | **wFRAG 买入不是申购** | 第 3 笔走 Raydium CLMM 二级市场，是 `Swap`/`Buy`，**不能算作 Fragmetric 申购**（同 Re 走 ParaSwap 的先例） |

### 6.3 ⚠️ 一个费用口径问题

| 项 | 值 |
|----|-----|
| UI 显示 restake 的 "Transaction Cost" | **≈ 0.039 SOL** |
| 链上实际 fee | **0.000145 SOL** |

**→ 差 269 倍。** 推断 UI 的 "Transaction Cost" 包含了**账户 rent（建 fund / reward / token 账户的租金押金）**，而非纯手续费。
**→ 解析注意**：给用户展示成本时，rent 是**可退押金**还是**真实支出**，口径要跟前端对齐，否则小额用户会看到「成本远大于本金」。
（UI 另标 **Unstake Fee 0.2%**，属协议费，与上面无关。）

### 6.4 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | ✅ | fragSOL balance × 1.13198 | 汇率三方吻合，可用 |
| 交易历史解析 | ✅ | 见 §6.1 三类 | 唯缺 claim（Withdraw） |
| 池子自发现 | ⬜ | 未测 | 另有 fragBTC / fragETH 等未测 |
| APY | 🟡 | UI 有 5.55% | 链上/API 取数未查 |
| NAV 历史 | ⬜ | 未测 | |
| PNL | 🟡 | 依赖 NAV 历史 + **reward account** | 🔴 别忘 §6.2 第 3 条 |

### 6.5 需向项目方索取

1. **reward account 的账户结构与读取方式**（PNL 必需）
2. **Withdraw（claim）阶段的指令与事件**
3. fragSOL/SOL 汇率的链上取数入口
4. 「3 epochs (6 days)」的触发条件（什么情况下会从 1 天变 6 天）
5. FRAG / wFRAG 激励是否计入用户收益（PNL 口径）
6. 其他 frag 系列资产（fragBTC / fragETH）是否同一套机制

## 7. 风险

⬜ **未做**。实测中直接观察到的两点：
1. **赎回时长不确定**：官方文案自己写「通常 1 天，可能需要 3 epochs（6 天），取决于运营」→ **时长由项目方运营决定，不是链上确定值**
2. **多层嵌套**：用户 → Fragmetric → Jito Restaking → NCN，任一层出问题都会传导

## 8. 合规与准入

⬜ **未做**。UI 无 KYC 环节，链上非托管。

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | 🔴 **Withdraw（claim）的链上样本** —— 本次只做到 unstake 请求 | Linnea 补做（约 1 天后回来点 Withdraw tab） |
| 2 | 🔴 **reward account 读取方式**（PNL 必需） | 项目方 / 链上账户结构分析 |
| 3 | UI "Transaction Cost 0.039 SOL" 的构成（rent 是否可退） | 项目方 / 对比 claim 后余额 |
| 4 | TVL $4.77M 复核（UI 数字有动画模糊） | 重新截图 |
| 5 | 协议背景 / 底层 NCN 清单 / 风险 / 合规四章 | 未做，需补 |

## 10. 参考链接

- 产品页：https://app.fragmetric.xyz
- 实测截图：⚠️ **原图未落盘**（会话图片缓存已被系统清理）。UI 数值已逐项抄录进本页 §5.3 / §6.2 / §6.3
