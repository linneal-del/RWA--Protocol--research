# Scallop — SUI

> **状态**：🟡 2026-08-31 已有公开样本 digest + 部分本人实测；2026-10 按「前端所有可点击交易类型」标准补全中
> **链**：**SUI**（交易标识为 digest，base58）｜ 样本来自链上公开交易（非本人钱包）的已注明
> **来源**：自 `2026-08/SUI五协议-SUI.md` §3 拆出（2026-10-13）

## Scallop（借贷 + 流动性质押 + veSCA）

核心对象：`0xa757975255146dc9686aa823b7838b507f315d704f428cbadad2f4ea061939d9`

🔴 **Scallop 有三条独立产品线，别混为一谈**（2026-08-31 页面 + 链上双向确认）：

### 1 Lending（借贷）—— 存取借还

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

### 2 Stake（流动性质押 scaSUI）—— ✅ 本人已实测

页面：Scallop → **Stake** 页签（截图 `Scallop-LiquidStake-20260831.png`）
scaSUI Price **1.0043 SUI** ｜ APR **1.316%** ｜ Total Staked **140.503K SUI** ｜ 质押费 **0.00%** ｜ **解押费 0.02%** ｜ 支持随时即时解押

| 操作 | Move 函数 | digest | 实际金额 |
|------|----------|--------|---------|
| **质押（SUI→scaSUI）** | `liquid_staking::mint` | `HV1V33YwRar6t5eFnNvgHDHpzCQtpmV6akm2Kh5sosue` | −10.011437908 SUI → +9.963060579 scaSUI |
| **解押（scaSUI→SUI）** | `liquid_staking::redeem` | `F2XaJFeGBm3tSRfgCogtMtG2mq8zMihnukDxRcRsfFwX` | −9.0 scaSUI → +9.022080398 SUI |

📌 两笔都附带 `weight::rebalance`（验证人权重再平衡），事件分别是 `MintEvent`+`StakingRequestEvent` / `RedeemEvent`+`UnstakingRequestEvent`。

### 3 veSCA（治理质押）

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
