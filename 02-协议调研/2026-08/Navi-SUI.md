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

#### 📎 Earn Vault（Multiply）样本 —— 独立 package

**Vault package**：`0xfba9e78742d8f3edeb405561b954846ce3e60cab64dac00e600d50bb4923be0f`
（另有记录器包 `0x7007493dab46ed15f0d28b41b39b71f9314c520cd8349e01cb3bf46f7cb5fba5`）

| 交易类型 | Move 调用 | 样本 digest |
|---------|----------|------------|
| **Vault 存款** | `operation::batch_execute_deposit` | `9kdSs9LBmYq6EzAvJScx2Xno12zgqc2QAjk52CRXq5Zt`（08-19 03:38） |
| **Vault 取款** | `operation::batch_execute_withdraw` | `6DorEAhaYmipxVojtRbF4BZvAUtSN9RbiXFDeTFzfRxY`（08-19 03:42） |
| 运营方代存 | `operation::deposit_by_operator` | 同上 |
| 存取手续费归集 | `vault::deposit_withdraw_fee_collected` + `vault_manage::retrieve_deposit_withdraw_fee_by_operator` | 同上 |

🔴🔴 **链上与 UI 互相印证的关键机制**：Vault 存取的函数名是 **`batch_execute_*`** 和 **`deposit_by_operator`** —— 说明**用户的存取请求由运营方批量执行**，而不是用户自己直接发起。这正好解释了 UI 上那句 **"Withdraw Period 14:00 UTC Daily"**（每日固定窗口批量处理）。
→ **解析要点**：Vault 交易的 **signer 是 operator，不是用户**；按 signer 归属仓位会全错，必须解析交易内的受益人参数。

**Vault 其余函数**（keeper 记账，非用户操作，应排除）：`vault::update_coin_type_asset_value` / `vault::update_free_principal_value` / `vault_oracle::update_price` / `curator_position::update_curator_position_value` / `curator_position::validate_curator_position_value` / `receipt_adaptor::update_receipt_value` / `zo_adaptor::update_zo_position_value` / `vault_event_recorder::record_vault_status` / `vault_operation_recorder::record_operation`

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
| **Earn Vaults / Multiply 存款** | ⬜ | ✅ `9kdSs9…`（`batch_execute_deposit`） |
| **Earn Vaults / Multiply 取款** | ⬜ | ✅ `6DorEA…`（`batch_execute_withdraw`） |
| **Optimize（迁仓）** | ⬜ | ⬜ **未捕获**：按 Storage 对象扫 10 页未见专用函数，疑与 withdraw+swap+deposit 组合在同一笔内，需本人实测定位 |
| Swap | ⬜ | 🟡 链上可见大量 `pool::swap` / `router::swap` / `swap_router::swap_exact_input`，但**多为套利机器人借闪电贷所发**，非 Navi Swap 页用户行为，未单独归因 |

### 2.2.1 📷 页面结构（2026-08-22 截图确认，钱包 `0x274…9b0e`）

**顶部导航**：Earn🔥 / Borrow / Portfolio / Swap / **Optimize** / Copilot / Reward Hub🔥 / …

| 页面 | 关键内容 | 截图 |
|------|---------|------|
| **Earn** | 金库列表 | [截图](截图/Navi-earn-20260822.png) |
| **Earn Vault 详情（Multiply）** | 🔴 **独立的「Multiply」循环杠杆入口**：TVL **$2.33M**（2.81M SUI）｜ Base APR **5.61%**（另有 Supply APR / Borrow APR）｜ **Min Investment 3 SUI** ｜ **Withdraw Fee 0.16%** ｜ 🔴 **Withdraw Period 14:00 UTC Daily**（每日固定窗口才能取）｜ 说明文字：该循环策略**免除 borrow / performance / flash loan 三项费用** | [截图](截图/Navi-EarnVault-Multiply-20260822.png) |
| **Borrow（USDC 市场）** | **Borrow Position 弹窗含 Borrow / Repay 两个 tab**；Borrow from = **Main Market**（可下拉切市场）｜ **Borrow Fee 0.3%** ｜ Borrow APR 6.938% ｜ 显示 Main Market HF（健康因子）｜ 无抵押时按钮为 *Insufficient collateral*。市场数据：Total Supply $26.21M / Total Borrow $21.07M | [截图](截图/Navi-borrow-repay弹窗-20260822.png) |
| **Optimize** | ✅ **会产生链上交易**：Supply / Borrow 两个 tab，Before(SUI) → After(**suiUSDT APR 16.409%**)，带 **Max Slippage 0.5%**，本质是**跨协议/跨资产的仓位迁移**（非纯展示页） | [截图](截图/Navi-optimize-20260822.png) |

🔴 **链上看不出的三件事**：
1. **Earn Vault 的 Multiply 是独立产品**（循环杠杆），有 **每日 14:00 UTC 的取款窗口** 和 3 SUI 起投门槛 —— 链上只看到存取，看不到"只能在某时段取"
2. **Borrow 与 Repay 在同一个弹窗的两个 tab**，且借款收 **0.3% Borrow Fee**（对应链上 `BorrowFeeDeposited` 事件）
3. **Optimize 是一次"迁仓"操作**，UI 上是一个按钮，链上很可能拆成 withdraw + swap + deposit 多步 —— 解析时容易误判成三笔独立行为

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | ✅ **已确认**：Vault 走独立 package `0xfba9e787…`，函数 `operation::batch_execute_deposit/withdraw` | 2026-08-24 链上取样 |
| 2 | ✅ **已确认：Optimize 会产生链上交易**（带滑点设置的迁仓）；Copilot 待确认 | 2026-08-22 截图 |
| 3 | `entry_deposit` 与 `deposit_with_account_cap` 的使用场景差异 | 文档 / 实测 |
| 4 | Swap 走的是 Navi 自有还是聚合器 | 实测交易 |
