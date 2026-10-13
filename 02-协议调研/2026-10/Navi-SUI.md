# Navi — SUI 借贷协议

> **状态**：✅ 前端可点击交易类型 17/18 有 digest + 交易截图（2026-10-13，借贷 / E-mode / Lending Vault / Multiply / 外部 Vault / Swap）；⬜ 1 类（Optimize 迁仓）需连钱包且持有外部仓位，未取到样本；均为公开样本，无本人实测 ｜ **调研时间**：2026-08-22，2026-10-13 补全
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**SUI**

## 交易类型覆盖（2026-10-13）

前端：Earn https://app.naviprotocol.io ｜ Borrow https://app.naviprotocol.io/borrow ｜ Portfolio https://app.naviprotocol.io/portfolio ｜ Optimize https://app.naviprotocol.io/optimize ｜ Swap / Bridge（顶部按钮，右侧面板）

| 包 | 地址 |
|---|---|
| lending（`incentive_v3` / `lending`，前端当前调用版本） | `0x512f28261c1a293f49416d8885b8d5d32bde6dd68a99a0be36fe42b248e6833a`；Storage 对象 `0xbb4e2f4b6205c2e2a2db47aeb4f830796ec7c005f88537ee775986639bc442fe` |
| navi_vault（Lending Vault） | `0x13e1e0ddcf3a76cde006d530e98a0f985c446013cfedeae6dd067a2f1ea88ff5` |
| vault（Multiply / 外部 Volo Vault 共用） | `0xdb8fca27462e39cb3cdb66c64baa498bfa037b3d8dce4046726eb4573371f02e`（旧版 `0xfba9e787…` 见下方 §2.1） |
| 聚合器（Swap） | 滑点检查 `0xdd21f177ec772046619e401c7a44eb78c233c0d53b4b2213ad83122eef4147db` |

| # | 交易类型 | 前端入口 | 模块 · 函数 | digest |
|---|---|---|---|---|
| 1 | Supply 存款（Main Market） | Borrow 页 → Main Market → Supply | lending 包 · `incentive_v3::entry_deposit`（2026-08 样本 `PnLKm4…` 为 `deposit_with_account_cap`） | [8pMtBtAq3MbikZumvF8PenuTVRFXkzD2R9o2sBL9faM9](https://suiscan.xyz/mainnet/tx/8pMtBtAq3MbikZumvF8PenuTVRFXkzD2R9o2sBL9faM9)（公开样本） |
| 2 | Withdraw 取款 | Portfolio → Withdraw | lending 包 · `incentive_v3::withdraw_v2` | [C7Pm64cW1T31Lh9QtoDrEKksrzr1TYpJnTV1pZ4oZt8T](https://suiscan.xyz/mainnet/tx/C7Pm64cW1T31Lh9QtoDrEKksrzr1TYpJnTV1pZ4oZt8T)（公开样本） |
| 3 | Borrow 借款（费 0.3%） | Borrow 页 → Borrow | lending 包 · `incentive_v3::borrow_v2` | [6R9rW2gVKNckCnH7QcvoeTr7f1EEsJN1zDC1Mz5qSHps](https://suiscan.xyz/mainnet/tx/6R9rW2gVKNckCnH7QcvoeTr7f1EEsJN1zDC1Mz5qSHps)（公开样本） |
| 4 | Repay 还款 | Borrow 弹窗 → Repay tab / Portfolio → Repay | lending 包 · `incentive_v3::entry_repay` | [2xYhsHwgLfJU1c16Dxo5Fmv4NyGdV6x92zJDYkyZLpjB](https://suiscan.xyz/mainnet/tx/2xYhsHwgLfJU1c16Dxo5Fmv4NyGdV6x92zJDYkyZLpjB)（公开样本） |
| 5 | 领借贷奖励 | Portfolio → Rewards → Claim | lending 包 · `incentive_v3::claim_reward_entry` | [8ENYNYTBUSn2rrMyrSVTnyCmWGrbGe6eDSqF4RDv2QFA](https://suiscan.xyz/mainnet/tx/8ENYNYTBUSn2rrMyrSVTnyCmWGrbGe6eDSqF4RDv2QFA)（公开样本） |
| 6 | 进入 E-mode 市场（建子账户） | Borrow 页 → 非 Main Market 的市场，首次操作 | lending 包 · `lending::create_account` + `lending::enter_emode_with_account_cap` | [FHPiCkYwsomj1ed2UmKvGZ4ChqxQATeABSJq886EM82S](https://suiscan.xyz/mainnet/tx/FHPiCkYwsomj1ed2UmKvGZ4ChqxQATeABSJq886EM82S)（公开样本） |
| 7 | E-mode 市场借款（存 / 取 / 还同理） | Borrow 页 → E-mode 市场 → Borrow | lending 包 · `incentive_v3::borrow_with_account_cap_v2`（存 `deposit_with_account_cap`、取 `withdraw_with_account_cap_v2`、还 `repay_with_account_cap`） | [C6ujXro2ZTQmoEa35YLAFgZD9n5ckY3xPDnbx43oyw4P](https://suiscan.xyz/mainnet/tx/C6ujXro2ZTQmoEa35YLAFgZD9n5ckY3xPDnbx43oyw4P)（公开样本） |
| 8 | Lending Vault 存入 | Earn → USDC High Yield / Prime 等 → Deposit | navi_vault 包 · `navi_vault::deposit`（前置 `sync_market_balance` ×N + `collect_reward`） | [5BP3MP1371nchxLufVdPiUFugX1ApnqxC4sMB9nmJqgD](https://suiscan.xyz/mainnet/tx/5BP3MP1371nchxLufVdPiUFugX1ApnqxC4sMB9nmJqgD)（公开样本） |
| 9 | Lending Vault 取出 | Earn → Vault 详情 → Withdraw | navi_vault 包 · `navi_vault::withdraw` | [DdQH29VrfjC1SY4KeRYp2nDZHAxWnGsvfqym3sUb4kAH](https://suiscan.xyz/mainnet/tx/DdQH29VrfjC1SY4KeRYp2nDZHAxWnGsvfqym3sUb4kAH)（公开样本） |
| 10 | Lending Vault 领奖励 | Earn → Vault 详情（需连钱包） | navi_vault 包 · `navi_vault::collect_reward` + `claim_reward` | [HMrMCjiScircCKgQ9UoncWnXHnJv7T9wo3tUm9PXFzok](https://suiscan.xyz/mainnet/tx/HMrMCjiScircCKgQ9UoncWnXHnJv7T9wo3tUm9PXFzok)（公开样本） |
| 11 | Multiply 存入申请 | Earn → Sui/vSui Multiply → Multiply | vault 包 · `user_entry::deposit_with_auto_transfer` | [E1abEBwnHcJchyfmQyJysEsUuTDJNqxhiqmyp1QD2Tp6](https://suiscan.xyz/mainnet/tx/E1abEBwnHcJchyfmQyJysEsUuTDJNqxhiqmyp1QD2Tp6)（公开样本） |
| 12 | Multiply 取出申请 | Multiply 详情 → Withdraw（需连钱包，每日 14:00 UTC 执行） | vault 包 · `user_entry::withdraw_with_auto_transfer` | [F7imhxa2tmHZj1jDKkxLzjDtMyf8bV6cWGwgWDrdGPTg](https://suiscan.xyz/mainnet/tx/F7imhxa2tmHZj1jDKkxLzjDtMyf8bV6cWGwgWDrdGPTg)（公开样本） |
| 13 | 外部 Vault（Volo）存入申请 | Earn → More Vaults → Volo SUI Prime 等 → Deposit | vault 包 · `user_entry::deposit_with_auto_transfer` | [6EpSTyofxc24RhmjacTc93Vyjy9bKWWrWfk2CMhzctkM](https://suiscan.xyz/mainnet/tx/6EpSTyofxc24RhmjacTc93Vyjy9bKWWrWfk2CMhzctkM)（公开样本） |
| 14 | 外部 Vault 取出申请 | 外部 Vault 详情 → Withdraw（需连钱包，按周执行） | vault 包 · `user_entry::withdraw_with_auto_transfer` | [5kL5mqNRyWa9mrhjQex4pxgLBFhUovUZB3b9vn48BSyC](https://suiscan.xyz/mainnet/tx/5kL5mqNRyWa9mrhjQex4pxgLBFhUovUZB3b9vn48BSyC)（公开样本） |
| 15 | Vault 撤销存入申请 | Vault 详情 → 待执行申请（需连钱包） | vault 包 · `user_entry::cancel_deposit_with_auto_transfer` | [BUgLoqPoQsUhkKT7an2qepgFMhPAHqEnAdQCWWyyLzDx](https://suiscan.xyz/mainnet/tx/BUgLoqPoQsUhkKT7an2qepgFMhPAHqEnAdQCWWyyLzDx)（公开样本） |
| 16 | Vault 撤销取出申请 | Vault 详情 → 待执行申请（需连钱包） | vault 包 · `user_entry::cancel_withdraw` | [7cL3MEtD8zycAtsohZ2sgDqnpHPNkvjYnMWoS9d4A6En](https://suiscan.xyz/mainnet/tx/7cL3MEtD8zycAtsohZ2sgDqnpHPNkvjYnMWoS9d4A6En)（公开样本） |
| 17 | Swap | 顶部 Swap（右侧面板） | Navi 聚合器 · `<DEX>::swap`（本例为 vSUI `stake_pool::stake`）+ `slippage::check_slippage_v3` | [3FawchG9ReKTidtgjFWUebmfTwYTLvNWpCv1RNRjbFrz](https://suiscan.xyz/mainnet/tx/3FawchG9ReKTidtgjFWUebmfTwYTLvNWpCv1RNRjbFrz)（公开样本；聚合器通用交易，链上无法区分是否经 Navi 前端发起） |
| 18 | Optimize（跨协议迁仓） | Optimize | — | ⬜ 需连钱包且持有 Suilend / Scallop / AlphaLend 仓位才出现迁移按钮；链上为外部协议取出 + Navi 存入的组合，无专用函数，未取到可确认样本 |

页面有入口但**不产生 Navi 交易**：Reward Hub（积分与兑换，链下）、Bridge 面板（外部跨链）、Copilot（多钱包看板，需登录；其 Claim 按钮未登录时无法确认调用）、Security 页（展示）。Portfolio 上的 Withdraw / Repay / Claim 复用 #2 / #4 / #5 的函数。闪电贷、清算非页面可点击操作，不计入。

⚠️ 开发注意：
- **lending 包已升级**：前端当前调用 `0x512f2826…`，下方 §1 记录的 `0xd899cf7d…` 为早期版本，解析需覆盖全部版本。**E-mode 市场的操作全部走 `*_with_account_cap` 变体**，仓位挂在 AccountCap 子账户下，不在用户地址名下。
- **Multiply 与外部 Volo Vault 共用一个 vault 包**（suiscan 标注 `@volosui/volo-vault`）：用户只发「申请」（`deposit/withdraw_with_auto_transfer`），资金由运营方按日 / 周批量执行（下方 §2.1 的 `9kdSs9…` / `6DorEA…` 即批量执行，signer 是运营方），解析需把申请与执行两笔交易关联。

### 截图

**前端页面**

| Earn（Vault 列表） | Borrow（Main Market，Supply / Borrow） |
|:-:|:-:|
| <img src="截图/Navi-SUI-Earn页-20261013.png" width="460"> | <img src="截图/Navi-SUI-Borrow页-20261013.png" width="460"> |
| **Borrow / Repay 弹窗（2026-08-22 截图）** | **Lending Vault 详情（USDC High Yield）** |
| <img src="截图/Navi-borrow-repay弹窗-20260822.png" width="460"> | <img src="截图/Navi-SUI-LendingVault详情页-20261013.png" width="460"> |
| **Multiply 详情（Sui/vSui）** | **外部 Vault 详情（Volo SUI Prime）** |
| <img src="截图/Navi-SUI-Multiply详情页-20261013.png" width="460"> | <img src="截图/Navi-SUI-外部Vault详情页-20261013.png" width="460"> |
| **Swap 面板** | **Bridge 面板（外部跨链）** |
| <img src="截图/Navi-SUI-Swap面板-20261013.png" width="460"> | <img src="截图/Navi-SUI-Bridge面板-20261013.png" width="460"> |
| **Optimize（需连钱包）** | **Reward Hub（积分，链下）** |
| <img src="截图/Navi-SUI-Optimize页-20261013.png" width="460"> | <img src="截图/Navi-SUI-RewardHub页-20261013.png" width="460"> |
| **Copilot（多钱包看板）** |  |
| <img src="截图/Navi-SUI-Copilot页-20261013.png" width="460"> |  |

**交易详情（每类一张，均为 suiscan）**

| 1 Supply（`8pMtBt…`） | 2 Withdraw（`C7Pm64…`） |
|:-:|:-:|
| <img src="截图/Navi-SUI-Supply存款交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-Withdraw取款交易-20261013.png" width="460"> |
| **3 Borrow（`6R9rW2…`）** | **4 Repay（`2xYhsH…`）** |
| <img src="截图/Navi-SUI-Borrow借款交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-Repay还款交易-20261013.png" width="460"> |
| **5 领借贷奖励（`8ENYNY…`）** | **6 进入 E-mode（`FHPiCk…`）** |
| <img src="截图/Navi-SUI-领奖励交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-进入Emode交易-20261013.png" width="460"> |
| **7 E-mode 借款（`C6ujXr…`）** | **8 Lending Vault 存入（`5BP3MP…`）** |
| <img src="截图/Navi-SUI-Emode借款交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-LendingVault存入交易-20261013.png" width="460"> |
| **9 Lending Vault 取出（`DdQH29…`）** | **10 Lending Vault 领奖励（`HMrMCj…`）** |
| <img src="截图/Navi-SUI-LendingVault取出交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-LendingVault领奖励交易-20261013.png" width="460"> |
| **11 Multiply 存入申请（`E1abEB…`）** | **12 Multiply 取出申请（`F7imhx…`）** |
| <img src="截图/Navi-SUI-Multiply存入申请交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-Multiply取出申请交易-20261013.png" width="460"> |
| **13 外部 Vault 存入申请（`6EpSTy…`）** | **14 外部 Vault 取出申请（`5kL5mq…`）** |
| <img src="截图/Navi-SUI-外部Vault存入申请交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-外部Vault取出申请交易-20261013.png" width="460"> |
| **15 撤销存入申请（`BUgLoq…`）** | **16 撤销取出申请（`7cL3ME…`）** |
| <img src="截图/Navi-SUI-Vault撤销存入交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-Vault撤销取出交易-20261013.png" width="460"> |
| **17 Swap（`3Fawch…`）** | **参考：运营方批量执行存入（`9kdSs9…`，非用户操作）** |
| <img src="截图/Navi-SUI-Swap交易-20261013.png" width="460"> | <img src="截图/Navi-SUI-Vault批量执行存入交易-20261013.png" width="460"> |

---

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
| **清算**（非用户主动） | `incentive_v3::liquidation_v2` | `EcsxeoZyLnJdGum5UqjbQRmXBafxSms8QjqMhDLNV8um` ⚠️ **链上失败交易（status=FAILURE，2026-10-13 复核），不可作样本** | — |
| 闪电贷还款（套利机器人） | `lending::flash_repay_with_ctx` | `3oF2CaCeAXrCZxFAbYhCaNuTnFNAnBYdnxzEwjosJSzB` ⚠️ **链上失败交易（status=FAILURE，2026-10-13 复核），不可作样本** | 无事件 |

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
