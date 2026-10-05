# wBETH — Wrapped Beacon ETH（币安 ETH 流动性质押）

> **状态**：✅ 链上铸造样本 + 页面结构均已确认，**UI 与链上汇率交叉验证一致**（2026-08-26）｜ **调研时间**：2026-08-26
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Ethereum**

## 0. 一句话结论

wBETH 是**币安发行的 ETH 流动性质押凭证**：1 wBETH 代表 1 BETH（1:1 锚定质押的 ETH）**加上其累积的 ETH2.0 质押收益** —— 属于 **NAV 累积型**（汇率单向上涨，不 rebase）。链上只有**一个用户动作：存 ETH 铸 wBETH**；🔴 **赎回不在链上**，需回币安站内操作。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Binance staked ETH（wBETH） |
| 链 | **Ethereum**（另有 BNB Chain 部署） |
| **合约地址** | `0xa2E3356610840701BDf5611a53974510Ae27E2e1` |
| 代币 | name **Wrapped Beacon ETH** ｜ symbol **WBETH** ｜ decimals 18 |
| 总供应 | **3,175,308.53 wBETH**（2026-08-26 链上快照） |
| **操作入口** | https://www.binance.com/en/wbeth ｜ 实际页面 = 币安 **Simple Earn → ETH Staking**（站内 CEX 操作，见 §2.4） |
| 解析类型 | **A 类（NAV 累积）** —— 汇率随质押收益上涨，余额不变 |
| 接入情况 | 待定 |

⚠️ **实测汇率参考**：样本交易中 **0.0085 ETH → 0.007691428647718743 wBETH**，反推 **1 wBETH ≈ 1.1052 ETH**。解析取值不能按 1:1。

## 2. 操作清单

| # | 操作 | 入口 | 链上有无 |
|---|------|------|:---:|
| 1 | **存入 ETH 铸 wBETH** | 合约 `deposit(address referral)`，或币安站内 | ✅ 有 |
| 2 | **赎回 wBETH → ETH** | 🔴 **币安站内（中心化）** | ❌ 链上无 |
| 3 | wBETH 转账 | 标准 ERC-20 | ✅ 有 |

## 2.1 📎 公开样本交易（**非本人钱包**，链上直接取样）

| 交易类型 | 方法 / 事件 | 样本 tx hash | 金额 |
|---------|------------|-------------|------|
| **存入铸造（deposit）** | selector `0xf340fa01`（`deposit(address)`），事件 `Transfer(from=0x0)` | `0xde97e3bc2517b8c26a5aaa563fd356c6a98ccac502387f92789bceb8dce3d2cf` | 存入 **0.0085 ETH** → 得 **0.007691428647718743 wBETH** |

**取样方法**（可复现）：对 Ethereum RPC 调 `eth_getLogs`，`address` = wBETH 合约，`topics` = `[Transfer, 0x0]`（即 mint），扫最近 2000 个区块即可。
⚠️ **可用节点**：`https://rpc.flashbots.net` ✅ ｜ `publicnode` / `llamarpc` 对 `eth_getLogs` 返回 **403**，`bsc-dataseed` 系返回 `limit exceeded` —— 换节点重试，别把节点报错当成"无数据"。

## 2.2 🔴 关键发现：链上没有赎回

扫 wBETH 合约近 **15 万个区块**（≈3 周）的 `Transfer(to=0x0)`（销毁）事件：**0 条**。

→ **wBETH 是单向铸造**：用户在链上只能存 ETH 铸 wBETH，**赎回必须回币安站内走中心化流程**（销毁不体现在链上，或由官方批量处理）。
→ **对解析同学**：不要预期存在"链上赎回"交易类型；用户 wBETH 余额减少大概率是**转账/卖出**而非赎回。

## 2.3 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| 存入铸造 deposit | ⬜ | ✅ `0xde97e3…` |
| 链上赎回 | ❌ | ❌ **协议无此链上入口** |
| ERC-20 转账 | ⬜ | ⬜ 标准，未单独取样 |

## 2.4 📷 页面结构（2026-08-26 截图确认）

截图 `wBETH-币安ETHStaking-20260826.png` —— 页面即币安 **Simple Earn → ETH Staking**（非独立 DApp）：

| 项 | 内容 |
|---|---|
| 站内导航 | Overview / **Simple Earn** / Advanced Earn / Loan / VIP Earn |
| 弹窗页签 | **ETH Staking** / Product Rules |
| 唯一操作按钮 | **Subscribe（申购）** |
| **Conversion Ratio** | **1 ETH ≈ 0.90487395 WBETH** |
| Reference APR | **2.2%** |
| 计息规则 | Stake Date 08/26 22:52 → **Rewards Start Accruing 08/27 08:00 AM**（🔴 **次日 8 点才起息**，非即时） |
| 需勾选 | Binance ETH Staking Service Agreement |
| 池子规模 | Total Value Staked **250,916.42 ETH** |

🔴 **申购是站内（CEX）操作，不产生用户钱包的链上交易** —— 用户在 binance.com 点 Subscribe，链上不会出现该用户地址的交易。§2.1 那笔链上 `deposit` 是**别人直接与合约交互**产生的，两条路径并存。

### ✅ 汇率交叉验证（UI 与链上完全吻合）

| 来源 | 数值 |
|------|------|
| UI Conversion Ratio | 1 ETH = 0.90487395 WBETH → **1 WBETH = 1.105126 ETH** |
| 链上样本反推 | 0.0085 ETH ÷ 0.007691428647718743 wBETH = **1.105126 ETH** |

→ **小数点后 6 位完全一致**，NAV 反推口径可靠。🔴 **绝不能按 1 wBETH = 1 ETH 处理。**

⚠️ **赎回入口本次未截到** —— 用户反馈页面上"没有别的"操作，赎回可能在 Simple Earn 的持仓页（My Staked ETH）而非本页。待补。

## 2.5 🔴 BSC 侧结论：页面上没有入口，链上也没有本地铸造（2026-09-01 确认）

### 页面确认（用户实地查看）

⚠️ 原图未落盘（会话缓存已清），以下为逐项抄录：

Binance ETH Staking 弹窗的 **Stake Amount 下拉框只有两个选项：`ETH` 和 `BETH`** —— **没有 BSC / BNB Chain 网络选项**。整个申购流程绑定在以太坊资产上。

| 当次快照 | 值 |
|---|---|
| Conversion Ratio | 1 ETH ≈ **0.9046016 WBETH** → 1 WBETH ≈ **1.105457 ETH** |
| Reference APR | **2.21%** |
| 最小申购 | **0.0001 ETH** |
| 计息规则 | Stake Date 08/31 23:48 → Rewards Start Accruing **09/01 08:00 AM**（仍是次日 8 点起息） |
| Total Value Staked | **251,573.04 ETH** |

### 链上确认

| 检查项 | 结果 |
|--------|------|
| BSC 上的 wBETH 合约地址 | 与以太坊**同地址** `0xa2E3356610840701BDf5611a53974510Ae27E2e1`，symbol `wBETH` / name `Wrapped Binance Beacon ETH` ✅ 合约存在 |
| BSC 上的**本地铸造**（`Transfer from 0x0`） | 🔴 **近 15 万个区块 0 条** |
| BSC 上的转账活跃度 | 近 2000 块仅 **1 笔**（`0x7d5a67c9886978af99e3b145794cfd4f8351d3a7916f6ba52cab65ac29b3dc96`），非常冷清 |

### 🔴 结论

**wBETH 在 BSC 上不构成一个可调研的协议入口**：
1. **页面无 BSC 选项** —— 用户无法在 BSC 上申购或赎回
2. **链上无本地铸造** —— BSC 上的 wBETH 是**从以太坊桥过去的**，不是在 BSC 铸的
3. BSC 侧唯一能看到的动作是**普通 ERC-20 转账**，而且量极小

→ 按交付口径记为：**BSC 链页面无该协议入口，无法提供交易哈希**。若需覆盖 wBETH，应以 **Ethereum** 为准（ETH 侧的 deposit 样本见 §2.1）。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | ✅ **已确认**：页面唯一操作是 **Subscribe（申购）**，属站内 CEX 行为不上链 | 2026-08-26 截图 |
| 2 | `deposit` 的 referral 参数用途 | 合约 / 文档 |
| 3 | 汇率链上读取函数（`exchangeRate()` 未命中标准签名）—— 已可用「存入量÷得到量」反推，与 UI 一致 | 读合约 ABI 补正式取数口径 |
| 5 | **赎回入口在哪个页面**（本次未截到，疑在 Simple Earn 持仓页） | 浏览器补截图 |
| 4 | BNB Chain 侧部署是否同一套逻辑 | 链上比对 |
