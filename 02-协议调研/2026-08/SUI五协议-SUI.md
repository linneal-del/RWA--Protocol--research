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

🔴 **Scallop 有三条独立产品线，别混为一谈**（2026-08-31 页面 + 链上双向确认）：

### 3.1 Lending（借贷）—— 存取借还

| 操作 | Move 函数 | 样本 digest |
|------|----------|------------|
| **存款** | `deposit::deposit` | `6iK1HFxA55E2KRSv21unKxYRG15AgoyWayutss5DUurw` |
| **取款（赎回存款）** | `redeem::redeem` | `G4UvsH6G8xD7prCGiNQQRWpoR5XuAuJJB1wpqXYrnMNu` |
| **存入抵押** | `deposit_collateral::deposit_collateral` | `2ceZurf2XcdZtMcFBTB79gLKAkJ7QbGxFj5DhY9RhNsu` |
| **取出抵押** | `withdraw_collateral::withdraw_collateral` | `BDUcGdyYjNvREHciDC3tAvB9mgaznWiT5PuZYQSmrkzn` |
| **借款** | `borrow::borrow` | `HdqCvK1rxTF2zaHM2SJCYWYzjffqSfqoU8yvPAUb55wW` |
| **还款** | `repay::repay` | `51W9sR4YZESMWnZnyvVGuDFmt3My5nenFFTe7zrFCvE5` |
| 铸造存款凭证 | `mint::mint` | `2XjksTm9asJKCu8rWaNaPXBFMvJmxhpdNa1gaqWxAwG9` |
| **清算**（非用户主动） | `liquidate::liquidate` | `GY495ncU6gGazwst9gNPk39rYih95EPfHPBLpZpR4KcF` |
| 闪电贷借 / 还 | `flash_loan::borrow_flash_loan` / `repay_flash_loan` | `GY495ncU6gGazwst9gNPk39rYih95EPfHPBLpZpR4KcF` |

#### ✅ 本人实测（2026-08-31，Lending 页 Supply/Withdraw）

页面：Scallop → **Lending**（截图 `Scallop-Lending-Supply-20260831.png`），弹窗有 **Supply / Withdraw** 两个页签。

| 操作 | 链上调用 | digest | 余额变化 |
|------|---------|--------|---------|
| **Supply（存款）** | `mint::mint` + **`s_coin_converter::mint_s_coin`** | `2K8JUvuCd3bcrrS3ezqySXFjZ6okJHWnQAtyJd18jdFG` | −0.028891817 scaSUI → **+0.028891817 SCALLOP_SCA_SUI**（sCoin） |
| **Withdraw（取款）** | **`s_coin_converter::burn_s_coin`** + `redeem::redeem` | `88BwiGUWVBmdVJDAJRPU1dJThsxTPFESBSQDDc9Qj6H4` | −0.00028025 sCoin → +0.00028025 scaSUI |

🔴🔴 **存款有两条路径，链上函数不一样，解析必须都认**：

| 路径 | 链上调用 | 说明 |
|------|---------|------|
| **sCoin 路径（当前 UI 走这条）** | `mint::mint` + `s_coin_converter::mint_s_coin` | 存进去后**换成 sCoin（Scallop Market Coin）**，一笔交易两个函数 |
| 直接路径 | `deposit::deposit` | 公开样本里见到的另一种，不产生 sCoin |

取款同理：UI 走 `burn_s_coin` + `redeem::redeem` 两步打包，公开样本里也有单独的 `redeem::redeem`。
→ **只认 `deposit`/`redeem` 会漏掉走 sCoin 路径的用户**（也就是现在从官网操作的绝大多数人）。

📌 **页面对 sCoin 的原文说明**：*"When you supply coins to lending pools, you will receive sCoins (Scallop Market Coins) in return. These coins represent your claim on the supplied assets and can be used to redeem your funds. **Transferring sCoins means you transfer this claim to another address.**"*
→ **sCoin 是可转让的存款凭证** —— 用户之间转 sCoin = 转移存款所有权，解析持仓时不能只看"谁存过"，要看当前谁持有 sCoin。

⚠️ **另外两条页面提示**：
1. *"Scallop's protocol supports flash loans, but only through direct contract integration — the dapp UI does not expose them."* → **闪电贷前端点不到**，只能直接调合约。链上有样本但不算"页面可点击的交易类型"。
2. *"Isolated-asset debt has to stay alone in its own obligation — open a separate one for mixed debt."* → **隔离资产的债务必须单独开一个 obligation（债仓）**，不能和其他债混在一起。解析仓位时要按 obligation 分开算。

📌 **市场参数**（截图快照）：SUI 抵押权重 **85%** ｜ Total Collateral **718.67K SUI**（≈$511.66K）｜ Borrow Weight 100% ｜ Supply APY 1.53% ｜ Total Lending Supply **$7.16M**（周环比 −10.19%）

### 3.2 Stake（流动性质押 scaSUI）—— ✅ 本人已实测

页面：Scallop → **Stake** 页签（截图 `Scallop-LiquidStake-20260831.png`）
scaSUI Price **1.0043 SUI** ｜ APR **1.316%** ｜ Total Staked **140.503K SUI** ｜ 质押费 **0.00%** ｜ **解押费 0.02%** ｜ 支持随时即时解押

| 操作 | Move 函数 | digest | 实际金额 |
|------|----------|--------|---------|
| **质押（SUI→scaSUI）** | `liquid_staking::mint` | `HV1V33YwRar6t5eFnNvgHDHpzCQtpmV6akm2Kh5sosue` | −10.011437908 SUI → +9.963060579 scaSUI |
| **解押（scaSUI→SUI）** | `liquid_staking::redeem` | `F2XaJFeGBm3tSRfgCogtMtG2mq8zMihnukDxRcRsfFwX` | −9.0 scaSUI → +9.022080398 SUI |

📌 两笔都附带 `weight::rebalance`（验证人权重再平衡），事件分别是 `MintEvent`+`StakingRequestEvent` / `RedeemEvent`+`UnstakingRequestEvent`。

### 3.3 veSCA（治理质押）

| 操作 | Move 函数 | 样本 digest |
|------|----------|------------|
| 质押 SCA | `user::stake` | `HdqCvK1rxTF2zaHM2SJCYWYzjffqSfqoU8yvPAUb55wW` |
| 质押（veSCA v2） | `user::stake_with_ve_sca_v2` | `51W9sR4YZESMWnZnyvVGuDFmt3My5nenFFTe7zrFCvE5` |
| 解押 | `user::unstake_v2` | `51W9sR4YZESMWnZnyvVGuDFmt3My5nenFFTe7zrFCvE5` |

🔴 **给解析同学的最重要一条**：Scallop 里 **"取款"和"解押"是完全不同的两件事** ——
- 借贷取款 = `redeem::redeem`（拿回存的本金）
- 流动性解押 = `liquid_staking::redeem`（scaSUI 换回 SUI）
- 治理解押 = `user::unstake_v2`（解锁 SCA）

三个都叫"redeem/unstake"但属于三条产品线，**按函数全名区分，不能只看动词**。

📌 **另外**：Scallop 交易里混了大量**跨协议再平衡**调用（`navi_entry::rebalance_deposit_to_navi`、`cetus::swap_a2b`、`bluefin::swap_b2a` 等），那些是策略金库在搬仓，不是普通用户行为，解析时要排除。

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
| ~~**Scallop**~~ | ✅ **已补齐**：取款 `redeem::redeem`、还款 `repay::repay`、存/取抵押均已找到；Stake 线你已实测两笔 | — |
| 五个协议 | 页面截图 | 逐个打开，看有没有我没列到的操作类型 |
| 五个协议 | 本人实测哈希 | 能做的操作补上你自己的交易 |

**各家入口**：
- Suilend → https://suilend.fi
- Cetus → https://app.cetus.zone
- Scallop → https://app.scallop.io
- Momentum → https://app.mmt.finance/trade
- SUI Staking → 钱包内质押 或 https://suiscan.xyz/mainnet/validators
