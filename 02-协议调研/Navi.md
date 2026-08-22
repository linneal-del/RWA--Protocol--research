# Navi — SUI 借贷协议

> **状态**：🟡 六类用户操作已取到链上样本（2026-08-22）｜ **调研时间**：2026-08-22
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**SUI**

## 0. 一句话结论

Navi 是 SUI 上的头部借贷协议（Protocol TVL **$143.24M**），用户操作围绕**存 / 取 / 借 / 还 / 领奖励**五类，全部通过 `incentive_v3` 模块进入。另有 Earn Vaults、Swap、Optimize（自动优化）等衍生入口。协议同时开放 **flash loan**（`lending::flash_loan_with_ctx`），链上被套利机器人高频使用 —— **解析时要把套利机器人的闪电贷交易和真实用户操作区分开**。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | NAVI Protocol（naviprotocol.io） |
| 链 | **SUI** |
| 产品 | 借贷池（Earn / Borrow）+ Earn Vaults + Swap + Optimize |
| **操作入口** | **Earn（存款）**：https://app.naviprotocol.io ｜ **Borrow**：https://app.naviprotocol.io/borrow ｜ **Portfolio**：https://app.naviprotocol.io/portfolio ｜ **Optimize**：https://app.naviprotocol.io/optimize ｜ **Copilot**：https://app.naviprotocol.io/copilot ｜ **Reward Hub**：https://app.naviprotocol.io/leaderboard ｜ Swap（导航内） |
| Protocol TVL | **$143.24M**（2026-08-21 UI） |
| Vaults Deposits | **$12.27M** |
| 安全门户 | https://app.naviprotocol.io/security |
| 接入情况 | 待定 |

### 链上关键对象

| 类型 | 值 |
|------|-----|
| **Package** | `0xd899cf7d2b5db716bd2cf55599fb0d5ee38a3061e7b6bb6eebf73fa5bc4c81ca` |
| **Storage 对象** | `0xbb4e2f4b6205c2e2a2db47aeb4f830796ec7c005f88537ee775986639bc442fe`（类型 `…::storage::Storage`） |
| 用户操作模块 | **`incentive_v3`** |
| 闪电贷模块 | `lending`（`flash_loan_with_ctx` / `flash_repay_with_ctx`） |

⚠️ **SUI 取数注意**：官方公共 fullnode 的 **JSON-RPC 已下线**（`fullnode.mainnet.sui.io` 返回 `-32601 Method not found`）。实测可用端点：**`https://sui-rpc.publicnode.com`**（本页样本即由此取得），或改用 GraphQL。

## 2. 操作清单

| # | 操作 | 入口路径 |
|---|------|---------|
| 1 | **Supply / 存款** | Earn 页 → 选资产 → Supply |
| 2 | **Withdraw / 取款** | Earn 或 Portfolio → Withdraw |
| 3 | **Borrow / 借款** | Borrow 页 |
| 4 | **Repay / 还款** | Borrow 页或 Portfolio → Repay |
| 5 | **Claim 奖励** | Reward Hub |
| 6 | Vaults / Swap / Optimize | 对应导航页 |

### 2.1 📎 公开样本交易（**非本人钱包**，链上直接取样）

均取自 **2026-08-22 13:19~13:37 UTC**，通过 `suix_queryTransactionBlocks` 按 Storage 对象过滤得到。SUI 用 **digest**（base58）而非 EVM 式 hash。

| 交易类型 | Move 调用 | 样本 digest | 关键事件 |
|---------|----------|------------|---------|
| **Supply / 存款** | `incentive_v3::deposit_with_account_cap` | `PnLKm4jv1Rm3jVzvN6MVHkk3xLxS3VTQEAwjq6XGroL` | `DepositEvent` / `PoolDeposit` / `StateUpdated` |
| **Withdraw / 取款** | `incentive_v3::withdraw_v2` | `9Q64ut7kaCfFUYNPwe4Z3Nj3yyBb4tZADFfTYzHAaAY5` | `PoolWithdraw` / `NaviHealthFactorVerified` / `StateUpdated` |
| **Borrow / 借款** | `incentive_v3::borrow_v2` | `HrKxM1qgMg2oSKF824ksWbhhHtWbqhDLEiBe7jA6RnsX` | `BorrowEvent` / **`BorrowFeeDeposited`** / `PoolWithdraw` |
| **Repay / 还款** | `incentive_v3::entry_repay` | `GNZVeN2CQvBxfGZMPAEdsQJXVbq7PdBkfwLayXhrmWAA` | `PoolDeposit` / `PoolWithdraw` / `NaviHealthFactorVerified` |
| **Claim 奖励** | `incentive_v3::claim_reward_entry` | `4xnpkQhs4Z6sGGdyLVSUXVBDxhAmjvhq8fzvDRrPwKWE` | `RewardClaimed` |
| **清算**（非用户主动） | `incentive_v3::liquidation_v2` | `EcsxeoZyLnJdGum5UqjbQRmXBafxSms8QjqMhDLNV8um` | — |
| 闪电贷还款（套利机器人） | `lending::flash_repay_with_ctx` | `3oF2CaCeAXrCZxFAbYhCaNuTnFNAnBYdnxzEwjosJSzB` | 无事件 |

**`incentive_v3` 模块出现过的全部函数**：`entry_deposit` / `deposit_with_account_cap` / `withdraw_v2` / `borrow_v2` / `entry_repay` / `claim_reward` / `claim_reward_entry` / `liquidation_v2`

🔴 **解析要点（观察，非深度解析）**：
1. **存款有两个入口函数** —— `entry_deposit`（普通）与 `deposit_with_account_cap`（带账户凭证），**别只认一个**
2. **借款会额外抛 `BorrowFeeDeposited`** —— 借款费单独计，不要算进本金
3. **`PoolDeposit` / `PoolWithdraw` 会在多种操作里出现**（还款同时出现两者），**不能只凭这两个事件判定操作类型**，要看 MoveCall 函数名
4. 链上大量 `arb_flash::*` / `lending::flash_loan_with_ctx` 是**套利机器人**，非用户行为，应排除

### 2.2 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| Supply | ⬜ | ✅ `PnLKm4…` |
| Withdraw | ⬜ | ✅ `9Q64ut…` |
| Borrow | ⬜ | ✅ `HrKxM1…` |
| Repay | ⬜ | ✅ `GNZVeN…` |
| Claim 奖励 | ⬜ | ✅ `4xnpkQ…` |
| Earn Vaults 存取 | ⬜ | ⬜ 待取样 |
| Swap / Optimize | ⬜ | ⬜ 待取样 |

⚠️ **页面截图待补**：操作清单来自导航结构，**尚未实地截图确认各页可点击按钮**（如 Vaults 存取是否独立入口、Optimize 是否产生链上交易）。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | Earn Vaults 的存取是否走不同模块 | 截图 + 链上取样 |
| 2 | Optimize / Copilot 是否产生链上交易 | 页面确认 |
| 3 | `entry_deposit` 与 `deposit_with_account_cap` 的使用场景差异 | 文档 / 实测 |
| 4 | Swap 走的是 Navi 自有还是聚合器 | 实测交易 |
