# Solayer —— sSOL（Solana LST / AVS Restaking）

> ⚠️ **范围声明**：本协议**不属于**本仓库原本的 RWA 7 协议范围，来自 **DeX 行情接链项目**（协议表条目 `SOLSolayer`）。
> **状态**：🟡 stake 已实测；**unstake 操作失败，未拿到样本**
> **实测时间**：2026-08-10 ｜ **测试钱包**：Solana `BpexTtBqxhgvGWeipAE7SowyCp5KSihv8s5YZ91gXbFp`（Binance Web3 MPC 钱包）
> **本页可信度**：链上部分为 `getTransaction` 逐笔实测；UI 部分为操作当时截图读取（原图未落盘）

## 0. 一句话结论

Solayer 的 sSOL 是 **SOL 的流动质押凭证，汇率 1 sSOL = 1.1653 SOL（NAV 累积型）**；实测 stake 一笔链上同时出现 **`DepositSol` + `Restake` 两个指令** —— 这是它跟普通 LST 的区别（多了 AVS restaking 那一腿）。**unstake 本次在钱包侧执行失败，没有产生链上交易**，这条路径仍是空白。

## 1. 基础信息（实测口径）

| 字段 | 值 |
|------|-----|
| 协议 | Solayer（app.solayer.org） |
| 链 | Solana |
| 本次实测产品 | **sSOL**（另有 sUSD 未测） |
| Supply Coins | SOL（原生币） |
| Coins Integrated | **sSOL** |
| TVL | Total **$10.5M** ｜ sSOL **$7.4M** ｜ sUSD **$745.1K**（2026-08-10 UI 快照） |
| 收益率 | sSOL **5.15% APY** ｜ sUSD **3.34% APY**（2026-08-10 UI 快照） |
| 存款人数 | **304.5K**（UI 快照） |
| 池子类别 | 待确认（unstake 未测通，不清楚是否有 epoch 等待） |
| 产品网页 | https://app.solayer.org |
| 接入情况 | 待定（DeX 项目侧口径） |

## 2. 协议背景

⬜ **未做**。页面自述：*"Stake with the best performing Solana validator, enhanced with dedicated hardware, software optimization, MEV rewards, and AVS rewards."*
→ **收益自述为四腿**：优质验证人 + 专用硬件 + 软件优化 + **MEV rewards** + **AVS rewards**。

## 3. 底层资产

⬜ **未做**。底层为 Solana 原生质押 + AVS restaking，具体 AVS 清单未取。

## 4. 收益来源

| 收益腿 | 来源 |
|--------|------|
| Solana 原生质押收益 | 页面自述 |
| MEV rewards | 页面自述 |
| AVS rewards | 页面自述（对应链上的 `Restake` 指令） |

⚠️ 均为页面自述，未交叉验证，未拆分各腿占比。

## 5. 链上机制与凭证代币（✅ stake 部分实测）

### 5.1 地址清单

| 项 | 地址 |
|----|------|
| **sSOL mint** | `sSo14endRuUbvQaJS3dq36Q829a3A6BEfoeeRGJywEh` |
| sUSD mint（未测） | `sUSDLMVGaEHXTHXBjPWZaGrDLA5FDvzuKvVWSPuxWXo` |

### 5.2 🔴 一笔 stake = `DepositSol` + `Restake` 两个指令

实测 stake 交易的指令序列：

```
Create                              ← 建 sSOL 的 associated token account
Initialize the associated token account
Instruction: DepositSol             ← 存 SOL，铸 sSOL（LST 那一腿）
Instruction: Restake                ← 🔴 再把它 restake 进 AVS（Solayer 特有的那一腿）
Create
Initialize the associated token account
```

**→ 解析规格**：只识别 `DepositSol` 会把 Solayer 当成普通 LST，**漏掉 `Restake` 这一腿**（AVS 敞口 / AVS 奖励归属都在这里）。

### 5.3 汇率

| 来源 | 数值 |
|------|------|
| UI（Unstake 面板） | **1 sSOL = 1.1653 SOL** |
| 性质 | NAV 累积型（sSOL 相对 SOL 升值） |

⚠️ 本次 stake 金额过小（换得 0.000858133 sSOL），**不足以反推汇率做交叉验证**，UI 数值待下次较大额操作时复核。

## 6. 数据接入要点

### 6.1 ✅ 实测交易（2026-08-10，UTC）

| # | 操作 | 交易类型 | signature | 结果 |
|:---:|------|---------|-----------|------|
| 1 | Stake SOL | 🔴 **质押（LST + Restake 复合）** | `24fg3SDThyztNEbju2yh3Q6WFQVNKfCjVVhEdJMvSWEL8tiwvTsX2HFCY4szYPBjcFUUQw5yiqY6npP8tTSUKnW1` | 08:18:47 ｜ slot 438366068 ｜ fee **0.00041 SOL**<br>sSOL **0 → 0.000858133** |
| 2 | Unstake 0.00008 sSOL | ❌ **失败，无链上交易** | —— | 钱包弹窗：**「交易执行失败」**，网络费显示 `--`，「确认」按钮置灰 |

**交叉验证**：交易后 sSOL 余额 **0.000858133**；Unstake 面板显示可用余额 **0.000858** ✅ 吻合。

### 6.2 🔴 unstake 失败这件事本身的价值

| 项 | 观察 |
|----|------|
| 钱包 | Binance Web3 MPC 钱包（UI 显示 `BpexTtBqxhgvGWeipAE7SowyCp5KSihv8s5YZ91gXbFp` / "My Wallet"） |
| 弹窗类型 | 「合约调用」→ **交易执行失败** |
| 网络费 | 显示 `--`（模拟阶段就失败，**未上链**） |
| UI 状态 | 面板卡在 "Loading…"，"0 Pending transactions" |
| 尝试金额 | 0.00008 sSOL（余额 0.000858） |

**→ 推断**（未证实）：模拟阶段即失败，常见原因是**金额过小触发协议最小赎回额**、或 MPC 钱包对该指令的兼容问题。
**→ ⬜ 待补**：换更大金额重试，或换 Phantom/Solflare 类普通钱包重试，以区分「协议限制」还是「钱包兼容」。**这个区分很重要** —— 如果是协议有最小赎回额，那是 PRD 需要的字段；如果是钱包问题，则与解析无关。

### 6.3 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | 🟡 | sSOL balance × 汇率 | 汇率链上取数入口未查 |
| 交易历史解析 | 🟡 | stake 已有样本 | 🔴 **unstake 无样本** |
| 池子自发现 | ⬜ | 未测 | |
| APY | 🟡 | UI 有 5.15% | 链上/API 取数方式未查 |
| NAV 历史 | ⬜ | 未测 | |
| PNL | ⬜ | 未测 | |

### 6.4 需向项目方索取

1. **sSOL/SOL 汇率的链上取数方式**（stake pool 账户里的哪个字段）
2. **最小赎回额是否存在**（直接关系到 §6.2 的失败原因）
3. unstake 是否有 epoch 等待 / 是否两段式（request + claim）
4. sSOL 的 stake pool 账户地址、Restake 涉及的 AVS 程序清单
5. sUSD 产品线是否同一套机制

## 7. 风险

⬜ **未做**。实测中直接观察到的一点：**赎回路径未验证通** —— 在给用户上线前，赎回能否走通必须先确认。

## 8. 合规与准入

⬜ **未做**。UI 无 KYC 环节，链上非托管。

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | 🔴 **unstake 失败原因：协议最小赎回额 or MPC 钱包兼容** | Linnea 换金额/换钱包重试 |
| 2 | 🔴 **unstake 链上样本**（含是否两段式、是否等 epoch） | Linnea 补做 |
| 3 | sSOL 汇率链上取数入口 | 项目方 / 读 stake pool 账户 |
| 4 | `Restake` 指令对应的 AVS 程序与奖励归属 | 项目方 |
| 5 | Native Staking 入口（UI 上有）与 sSOL 是否两条不同链路 | 浏览器确认 |
| 6 | 协议背景 / 底层 / 风险 / 合规四章 | 未做，需补 |

## 10. 参考链接

- 产品页：https://app.solayer.org
- 实测截图：⚠️ **原图未落盘**（会话图片缓存已被系统清理）。UI 数值已逐项抄录进本页
