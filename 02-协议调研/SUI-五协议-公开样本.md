# SUI 五协议公开样本汇总（Suilend / Cetus CLMM / Scallop / Momentum / SUI Staking）

> **状态**：✅ 五个协议的链上公开样本已全部取到（2026-08-29）
> **交付口径**：先给哈希打底，用户随后按页面截图补漏项和本人实测哈希
> **链**：**SUI** ｜ **样本全部来自公开地址，非本人钱包**

## 0. 一句话说明

这五个协议我都没连钱包，直接从链上扒了别人做的真实交易当样本。**每种操作类型至少一条 digest，解析同学可以直接拿去用**。你之后去页面上截图，看有没有我漏掉的操作类型，再补你自己的哈希就行。

⚠️ **SUI 取数注意**：官方公共节点的 JSON-RPC 已下线（`fullnode.mainnet.sui.io` 报 `-32601`）。本页样本全部通过 **`https://sui-rpc.publicnode.com`** 取得。

---

## 1. SUI Staking（原生质押）

**不是第三方协议，是 SUI 链自带的验证人质押。** 合约是系统包 `0x3::sui_system`，共享对象 `0x5`。

| 操作 | Move 函数 | 样本 digest |
|------|----------|------------|
| **质押** | `sui_system::request_add_stake` | `3KpHbTtGCZYbmYmrRdiePk69MJYqo3pqit5B2cWZU2eJ` |
| **质押（多币合并）** | `sui_system::request_add_stake_mul_coin` | `Gt1iHU3CwpK29gYdBtu9BYXSX4cX4JZu6wDTn4ybDioM` |
| **解除质押** | `sui_system::request_withdraw_stake` | `415XjDxcbKUMQW126hpSghAUxco3ZgS4gr1AAd3cpask` |
| **赎回可分割质押凭证** | `sui_system::redeem_fungible_staked_sui` | `FV3etqgAmwNRUcSamHb4wCTPbwDtsDKRvSET3jVFM77S` |

📌 **给解析同学**：质押有**两个入口函数**（单币 / 多币合并），别只认一个。

---

## 2. Suilend（借贷）

主市场对象：`0x84030d26d85eaa7035084a057f2f11f701b7e2e4eda87551becbc7c97505ece1`

| 操作 | Move 函数 | 样本 digest |
|------|----------|------------|
| **存款（存流动性并铸 ctoken）** | `lending_market::deposit_liquidity_and_mint_ctokens` | `2qF41gtUi9MERwB55ehchWgCUj2X4PF1syzyhizwsfys` |
| **存入抵押（ctoken 进债仓）** | `lending_market::deposit_ctokens_into_obligation` | `2qF41gtUi9MERwB55ehchWgCUj2X4PF1syzyhizwsfys` |
| **借款** | `lending_market::borrow_request` | `ELwvjPQpT7SJa6sdvWsRHx7Xs6KyitWPg4jVEoAwu16M` |
| **还款** | `lending_market::repay` | `BrXvXAVGLEVD9bRrGPniMEHTNXKioejPkSqRLBhzMiTC` |
| **取出抵押** | `lending_market::withdraw_ctokens` | `AWnBbKYbZxycctcDjnCaa2q8BTgAdtmXCJYXHTLK961X` |
| **赎回 ctoken 取回流动性** | `lending_market::redeem_ctokens_and_withdraw_liquidity_request` | `AWnBbKYbZxycctcDjnCaa2q8BTgAdtmXCJYXHTLK961X` |
| **领奖励** | `lending_market::claim_rewards` | `92F4q2b35qU8Mg1D5YVgE5S8WDnbvc6LXLLRcPLJQYpH` |
| **领奖励并复投** | `lending_market::claim_rewards_and_deposit` | `92F4q2b35qU8Mg1D5YVgE5S8WDnbvc6LXLLRcPLJQYpH` |
| 流动性质押赎回 | `liquid_staking::redeem` | `A4Z8SZC2cydszSpxKQvDCVnC9t5KGdtBkYQtvyYmWeRn` |

🔴 **两点值得注意**：
1. **一次"存款"是两步**：先 `deposit_liquidity_and_mint_ctokens`（换成 ctoken），再 `deposit_ctokens_into_obligation`（放进债仓当抵押）。**只认第一步会漏掉抵押状态**。取出时同理是反向两步。
2. **领奖励有两个版本**（直接领 / 领了直接复投），复投那个不会有资金回到钱包。

---

## 3. Scallop（借贷）

核心对象：`0xa757975255146dc9686aa823b7838b507f315d704f428cbadad2f4ea061939d9`

| 操作 | Move 函数 | 样本 digest |
|------|----------|------------|
| **存款** | `deposit::deposit` | `6LNHbDw2xQM4qxQTJMjSnwfYUu9F7dL1pk3KCmwRHERG` |
| **铸造（存款凭证）** | `mint::mint` | `2XjksTm9asJKCu8rWaNaPXBFMvJmxhpdNa1gaqWxAwG9` |
| **借款** | `borrow::borrow` | `i3nWrNAnnUvWHv1ruZBgqLtWMUpRUGciEaFzeJnkMoc` |
| **清算**（非用户主动） | `liquidate::liquidate` | `GY495ncU6gGazwst9gNPk39rYih95EPfHPBLpZpR4KcF` |
| 闪电贷借 / 还 | `flash_loan::borrow_flash_loan` / `repay_flash_loan` | `GY495ncU6gGazwst9gNPk39rYih95EPfHPBLpZpR4KcF` |

⬜ **还没抓到的**：`withdraw`（取款）、`repay`（还款）—— 近期交易里没出现，需要翻更多页或你实测补。

📌 **给解析同学**：Scallop 交易里混了大量**跨协议再平衡**调用（`navi_entry::rebalance_deposit_to_navi`、`cetus::swap_a2b`、`bluefin::swap_b2a` 等），那些是策略金库在搬仓，不是普通用户行为，解析时要排除。

---

## 4. Cetus CLMM（集中流动性 DEX）

**当前包**：`0x1eabed72c53feb3805120a081dc15963c204dc8d091542592abaf7a35689b2fb`
样本按 `pool` 模块的原生事件取，干净不混聚合器。

| 操作 | 事件 | 样本 digest |
|------|------|------------|
| **兑换** | `pool::SwapEvent` | `ANLGZ8zJ6fe6s93u2dFPpKUwabBASQNhUrXCsMxFY5v2` |
| **开仓位** | `pool::OpenPositionEvent` | `6rcqqkjWWPFH7HPGhgFQW8izEU26d2aEE6dcPoEipWhS` |
| **加流动性** | `pool::AddLiquidityEvent` | `3Yq3fLHbcy2CTiYC8eznEAUTpHr62EP99PfWVxzn3trc` |
| **撤流动性** | `pool::RemoveLiquidityEvent` | `ECpZ2fSgUFLAgboX9BExkNqY77GKmw9eg4i3YAxut68M` |
| **领手续费** | `pool::CollectFeeEvent` | `EDoHeD285N4PifrapCUqbcDLuPodHCYZL8gwykh7Jzic` |
| **关仓位** | `pool::ClosePositionEvent` | `EDoHeD285N4PifrapCUqbcDLuPodHCYZL8gwykh7Jzic` |

🔴 **和 Meteora DLMM 一样的坑**：**关仓位和领手续费在同一笔交易里**（样本 digest 相同）。UI 上点一次"关闭仓位"，链上会同时出现领费 + 关仓两个事件，**只认一个会漏账**。

⚠️ **不要用 Cetus 的 GlobalConfig 对象取样**：那个对象被大量聚合器（bluefin / bolt / 各种 router）调用，捞出来一堆别家的 swap，会误判成 Cetus 自己的交易。**要按 pool 模块的事件筛**。

---

## 5. Momentum（集中流动性 DEX）

**当前包**：`0x70285592c97965e811e0c6f98dccc3a9c2b4ad854b3594faab9597ada267b860`
用户操作分散在 **4 个模块**，不在同一个：

| 操作 | 事件（模块::事件名） | 样本 digest |
|------|-------------------|------------|
| **兑换** | `trade::SwapEvent` | `CaTvLsKNSFXpGGNV1rVbRKPKJwpXu1eKyMVMAX9RGcPz` |
| **开仓位** | `liquidity::OpenPositionEvent` | `CY1B8zVvUQUqoSXBpk3k6HgM7sEi2A3kYvm3DyCiw9Z6` |
| **加流动性** | `liquidity::AddLiquidityEvent` | `CY1B8zVvUQUqoSXBpk3k6HgM7sEi2A3kYvm3DyCiw9Z6` |
| **撤流动性** | `liquidity::RemoveLiquidityEvent` | `37qhxLCvwfRJ1eTEMokdpur113EiQZsHbuidDf5rqypy` |
| **关仓位** | `liquidity::ClosePositionEvent` | `37qhxLCvwfRJ1eTEMokdpur113EiQZsHbuidDf5rqypy` |
| **领手续费** | `collect::FeeCollectedEvent` | `37qhxLCvwfRJ1eTEMokdpur113EiQZsHbuidDf5rqypy` |
| **领池子奖励** | `collect::CollectPoolRewardEvent` | `37qhxLCvwfRJ1eTEMokdpur113EiQZsHbuidDf5rqypy` |
| 闪电贷还款 | `trade::RepayFlashSwapEvent` | `CaTvLsKNSFXpGGNV1rVbRKPKJwpXu1eKyMVMAX9RGcPz` |
| 建池 | `create_pool::PoolCreatedEvent` | `7iczwVfGebendmL7EzKeGBrVH8jENf3i4Habtq5WfjSw` |

🔴 **一笔交易干四件事**：digest `37qhxLCv…` 里同时有 **撤流动性 + 关仓位 + 领手续费 + 领奖励** 四个事件。撤仓时这几样是打包发生的。
📌 加流动性那笔（`CY1B8zVv…`）同时有 **开仓位 + 加流动性** 两个事件。

---

## 6. 待办（需要你去页面上确认）

| 协议 | 我这边缺的 | 你要做的 |
|------|-----------|---------|
| **Scallop** | `withdraw`（取款）、`repay`（还款）样本 | 页面上确认这两个入口在哪 |
| 五个协议 | 页面截图 | 逐个打开，看有没有我没列到的操作类型 |
| 五个协议 | 本人实测哈希 | 能做的操作补上你自己的交易 |

**各家入口**：
- Suilend → https://suilend.fi
- Cetus → https://app.cetus.zone
- Scallop → https://app.scallop.io
- Momentum → https://app.mmt.finance/trade
- SUI Staking → 钱包内质押 或 https://suiscan.xyz/mainnet/validators
