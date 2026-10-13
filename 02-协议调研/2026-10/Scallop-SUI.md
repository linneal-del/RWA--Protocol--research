# Scallop — SUI

> **状态**：✅ 前端可点击交易类型 25/26 有 digest + 交易截图（2026-10-13，Lending / Stake / veSCA / Referral / Swap / Orderbook）；⬜ 1 类（Convert Dust）需连钱包未取到样本；Supply / Withdraw / Stake / Unstake 为 2026-08-31 本人实测，其余为公开样本
> **链**：**SUI**（交易标识为 digest，base58）｜ 样本来自链上公开交易（非本人钱包）的已注明
> **来源**：自 `2026-08/SUI五协议-SUI.md` §3 拆出（2026-10-13）

## 交易类型覆盖（2026-10-13）

前端：Lending https://app.scallop.io ｜ Stake https://app.scallop.io/stake ｜ veSCA https://app.scallop.io/vesca ｜ Referral https://app.scallop.io/referral ｜ Swap https://app.scallop.io/swap ｜ Orderbook https://app.scallop.io/spot/trade

| 包 | 地址 |
|---|---|
| protocol（借贷核心） | `0xde5c09ad171544aa3724dc67216668c80e754860f419136a68d78504eb2e2805`；Market 对象 `0xa757975255146dc9686aa823b7838b507f315d704f428cbadad2f4ea061939d9` |
| sCoin | `0x80ca577876dec91ae6d22090e56c39bc60dce9086ab0729930c6900bc4162b4c` |
| borrowIncentive（借款激励） | `0x74922703605ba0548a55188098d6ebc8fdeb9fe16993986f1b7c9a49036c7c9c` |
| scaSUI LST | `0x47c4b62aed92c5ae308ecd00253b64b3568ad97e6fb11e54b3d1ac5ed7be19ab` |
| veSCA | `0x18ff254b07856588796b3a97e447a1e504c48f58b309437cd8f2c397ecc2fc21` |
| referral | `0x73883b65a77436e7028bc41be184a7795573ceed8bb24cb289ae21680beb037c` |
| 忠诚度奖励 | veSCA 忠诚度 `0xc23deff2b10e58ad7d11b14a748a5e9673c39af6a8695e23bafddab3a65d96e5`；忠诚度 `0x21d0ba130d0ce783fd6377dc5c88aabbe60e947e40a6c42fb52761569ae9a7d8` |
| Swap | `0x782dccc2452abd5f43ea5d7de71e3fe7fe688dcae08aa0c8054624323db5e49b`；滑点检查 `0x325c1797d8d6a185ac8c5edf495f7b56b624f2e4a786e9605621d37b25df264b` |
| DeepBook v3（Orderbook） | `0x0e735f8c93a95722efd73521aca7a7652c0bb71ed1daf41b26dfd7d1ff71f748` |

| # | 交易类型 | 前端入口 | 模块 · 函数 | digest |
|---|---|---|---|---|
| 1 | 存款 Supply（换 sCoin） | Lending → Lending Pools → Supply | protocol · `mint::mint` + sCoin · `s_coin_converter::mint_s_coin` | [2K8JUvuCd3bcrrS3ezqySXFjZ6okJHWnQAtyJd18jdFG](https://suiscan.xyz/mainnet/tx/2K8JUvuCd3bcrrS3ezqySXFjZ6okJHWnQAtyJd18jdFG)（本人实测） |
| 2 | 取款 Withdraw | Lending → Lending Pools → Withdraw | sCoin · `s_coin_converter::burn_s_coin` + protocol · `redeem::redeem` | [88BwiGUWVBmdVJDAJRPU1dJThsxTPFESBSQDDc9Qj6H4](https://suiscan.xyz/mainnet/tx/88BwiGUWVBmdVJDAJRPU1dJThsxTPFESBSQDDc9Qj6H4)（本人实测） |
| 3 | 开债仓（首次存抵押时） | Lending → Collateral Pools → Deposit（首次） | protocol · `open_obligation::open_obligation` + `return_obligation`（同笔附带借款激励 `user::stake`） | [HADvqk2tpYByKugB4K4GBqHwMi7DhofoEjYqrR88U8Kp](https://suiscan.xyz/mainnet/tx/HADvqk2tpYByKugB4K4GBqHwMi7DhofoEjYqrR88U8Kp)（公开样本） |
| 4 | 存入抵押 | Lending → Collateral Pools → Deposit | protocol · `deposit_collateral::deposit_collateral` | [2ceZurf2XcdZtMcFBTB79gLKAkJ7QbGxFj5DhY9RhNsu](https://suiscan.xyz/mainnet/tx/2ceZurf2XcdZtMcFBTB79gLKAkJ7QbGxFj5DhY9RhNsu)（公开样本） |
| 5 | 取出抵押 | Lending → Collateral Pools → Withdraw（需连钱包） | protocol · `withdraw_collateral::withdraw_collateral` | [BDUcGdyYjNvREHciDC3tAvB9mgaznWiT5PuZYQSmrkzn](https://suiscan.xyz/mainnet/tx/BDUcGdyYjNvREHciDC3tAvB9mgaznWiT5PuZYQSmrkzn)（公开样本） |
| 6 | 借款 | Lending → Borrowing Pools → Borrow | protocol · `borrow::borrow`（前后包 `user::unstake_v2` / `user::stake`） | [HdqCvK1rxTF2zaHM2SJCYWYzjffqSfqoU8yvPAUb55wW](https://suiscan.xyz/mainnet/tx/HdqCvK1rxTF2zaHM2SJCYWYzjffqSfqoU8yvPAUb55wW)（公开样本） |
| 7 | 借款（已绑推荐人，享借款费折扣） | 同上（绑定推荐人后自动走此路径） | referral · `claim_ve_sca_referral_ticket` → protocol · `borrow::borrow_with_referral` → `burn_ve_sca_referral_ticket` | [CZV8k1gPGYxZKapP2HWnFdMHcqPC4KjsUB5xdGjXcmq8](https://suiscan.xyz/mainnet/tx/CZV8k1gPGYxZKapP2HWnFdMHcqPC4KjsUB5xdGjXcmq8)（公开样本） |
| 8 | 还款 | Lending → Borrowing Pools → Repay | protocol · `repay::repay`（前后包 `user::unstake_v2` / `user::stake_with_ve_sca_v2`） | [51W9sR4YZESMWnZnyvVGuDFmt3My5nenFFTe7zrFCvE5](https://suiscan.xyz/mainnet/tx/51W9sR4YZESMWnZnyvVGuDFmt3My5nenFFTe7zrFCvE5)（公开样本） |
| 9 | 领借款激励 | Lending → REWARD → Harvest All | borrowIncentive · `user::redeem_rewards` | [ACs44pi7PyKtsAiFLFdKWkfo19MgEHeaghgiMcpPmpBL](https://suiscan.xyz/mainnet/tx/ACs44pi7PyKtsAiFLFdKWkfo19MgEHeaghgiMcpPmpBL)（公开样本） |
| 10 | scaSUI 质押 | Stake | LST · `liquid_staking::mint`（+ `weight::rebalance`） | [HV1V33YwRar6t5eFnNvgHDHpzCQtpmV6akm2Kh5sosue](https://suiscan.xyz/mainnet/tx/HV1V33YwRar6t5eFnNvgHDHpzCQtpmV6akm2Kh5sosue)（本人实测） |
| 11 | scaSUI 解押（费 0.02%） | Stake（切换方向） | LST · `liquid_staking::redeem`（+ `weight::rebalance`） | [F2XaJFeGBm3tSRfgCogtMtG2mq8zMihnukDxRcRsfFwX](https://suiscan.xyz/mainnet/tx/F2XaJFeGBm3tSRfgCogtMtG2mq8zMihnukDxRcRsfFwX)（本人实测） |
| 12 | veSCA 新建锁仓 | veSCA → LOCK SCA（首次） | veSCA · `ve_sca::mint_ve_sca_key` | [EfNSJTHJ3f4S7ic1iP1XPYyF72Ne4uj7wFL8ekXWUJae](https://suiscan.xyz/mainnet/tx/EfNSJTHJ3f4S7ic1iP1XPYyF72Ne4uj7wFL8ekXWUJae)（公开样本，2026-02） |
| 13 | veSCA 追加锁仓（+延期） | veSCA → LOCK SCA（已有 veSCA） | veSCA · `ve_sca::lock_more_sca`（同笔可带 `extend_lock_period`） | [5faW8iBkVUDzoh2vxV7FNBLrUmJ9gVcM4b3JRncEQVx3](https://suiscan.xyz/mainnet/tx/5faW8iBkVUDzoh2vxV7FNBLrUmJ9gVcM4b3JRncEQVx3)（公开样本） |
| 14 | veSCA 延长锁期 | veSCA → LOCK SCA → Lock Duration | veSCA · `ve_sca::extend_lock_period` | [8XicvDGPhTRhJXQMuXawY7JhnBA612M4qCPTuofAvv3b](https://suiscan.xyz/mainnet/tx/8XicvDGPhTRhJXQMuXawY7JhnBA612M4qCPTuofAvv3b)（公开样本） |
| 15 | veSCA 过期后续锁 | veSCA（已到期，需连钱包） | veSCA · `ve_sca::renew_expired_ve_sca` | [DKzw5WgRKPe8CQ1B5VhevkXAFamLwRY8Gq2X9FA7cduA](https://suiscan.xyz/mainnet/tx/DKzw5WgRKPe8CQ1B5VhevkXAFamLwRY8Gq2X9FA7cduA)（公开样本） |
| 16 | veSCA 解锁赎回 | veSCA → UNLOCK SCA | veSCA · `ve_sca::redeem` | [FCULdHc8KRmD2N1EREJVuLH6psRTn2zEFAciqETfh1RG](https://suiscan.xyz/mainnet/tx/FCULdHc8KRmD2N1EREJVuLH6psRTn2zEFAciqETfh1RG)（公开样本） |
| 17 | veSCA 合并 | MY VESCA → Merge（需连钱包） | veSCA · `ve_sca::merge`（同笔先 `user::deactivate_boost_v2`） | [899pyxYQFDSuUyzuG1XV1HmF2FwqwPtWWFg6FTU29yN7](https://suiscan.xyz/mainnet/tx/899pyxYQFDSuUyzuG1XV1HmF2FwqwPtWWFg6FTU29yN7)（公开样本） |
| 18 | veSCA 拆分 | MY VESCA → Split（需连钱包） | veSCA · `ve_sca::split` | [DJoiP8CZhKGDLvA9UvkWEtb6Qk4kP2BtP7vb1ZhWd7vk](https://suiscan.xyz/mainnet/tx/DJoiP8CZhKGDLvA9UvkWEtb6Qk4kP2BtP7vb1ZhWd7vk)（公开样本） |
| 19 | veSCA 忠诚度奖励领取 | veSCA → Loyalty Program（需连钱包） | veSCA 忠诚度 · `ve_sca_reward::redeem_reward` | [98ktkpAjQGKc6toWXyEt4nJkzMYo4XuS6jLaFFqBLSvt](https://suiscan.xyz/mainnet/tx/98ktkpAjQGKc6toWXyEt4nJkzMYo4XuS6jLaFFqBLSvt)（公开样本，2025-08） |
| 20 | 忠诚度奖励领取 | veSCA → Loyalty Program（需连钱包） | 忠诚度 · `reward_pool::redeem_reward` | [F3Eu34Z4TyDap1TgfBsR7VczTHvB3WDVcU6iyoWKeFg2](https://suiscan.xyz/mainnet/tx/F3Eu34Z4TyDap1TgfBsR7VczTHvB3WDVcU6iyoWKeFg2)（公开样本，2026-06） |
| 21 | 绑定推荐人 | Referral（打开推荐链接后，需连钱包） | veSCA · `mint_ve_sca_placeholder_key` + referral · `referral_bindings::bind_ve_sca_referrer` | [ERUkPWdqFqon8xZBqcGxQC24xvXhyT17XuEMSEunhSk1](https://suiscan.xyz/mainnet/tx/ERUkPWdqFqon8xZBqcGxQC24xvXhyT17XuEMSEunhSk1)（公开样本） |
| 22 | 解绑推荐人 | Referral → MY REFERRER（需连钱包） | referral · `referral_bindings::unbind_ve_sca_referrer` | [Yu9QeaxzUdvsM7nQLiUcqdpVqPo5CKnR5AHaTKU75N4](https://suiscan.xyz/mainnet/tx/Yu9QeaxzUdvsM7nQLiUcqdpVqPo5CKnR5AHaTKU75N4)（公开样本） |
| 23 | 领推荐收益 | Referral → Current Pending Reward（需连钱包） | referral · `referral_revenue_pool::claim_revenue_with_ve_sca_key`（同笔可接 swap） | [yQzMjzfhrxzzZctnWJcx7azPagUy3AC5HhJNzaAeWoS](https://suiscan.xyz/mainnet/tx/yQzMjzfhrxzzZctnWJcx7azPagUy3AC5HhJNzaAeWoS)（公开样本） |
| 24 | Swap | Spot → Swap | swap · `swap::create_swap_hot_potato` → 聚合 router + `<DEX>::swap` → `swap::emit_swap_event`（前后 `scallop_swap_slippage::check_slippage`） | [2c6tir1uFE7aP64LHbtfuKLKNeRsAeoCgQwnx3z1FGqF](https://suiscan.xyz/mainnet/tx/2c6tir1uFE7aP64LHbtfuKLKNeRsAeoCgQwnx3z1FGqF)（公开样本） |
| 25 | Orderbook 挂单 / 撤单 | Spot → Orderbook（DeepBook） | DeepBook v3 · `pool::place_limit_order` / `pool::cancel_order`（不经 Scallop 合约） | [7M8Q25vobnWhXvysnT7psWyVpbG8KHxprSy7TuC5Tks5](https://suiscan.xyz/mainnet/tx/7M8Q25vobnWhXvysnT7psWyVpbG8KHxprSy7TuC5Tks5)（公开样本；DeepBook 通用交易，链上无法区分是否经 Scallop 前端发起） |
| 26 | Convert Dust（钱包内小额币一键兑换） | 钱包侧栏 → Convert Dust | — | ⬜ 需连钱包才可操作，链上与普通 Swap 无法区分，未取到可确认样本 |

页面有入口但**不产生 Scallop 交易**：Tools（PTB 交易构建工具，用户自拼任意交易）、Bridge → Wormhole Connect / Portal / Sui Bridge、CEX On-Ramp / Alchemy Pay / Li.Fi（外部服务）、More → DefiLlama / Sentio（外链）、Ecosystem / Leaderboard（展示）、首页 Banner（跳 Cetus / Bucket / Kraken）。闪电贷前端不暴露（见下方 §1 页面提示），清算非用户主动操作，均不计入。

⚠️ 开发注意：
- **`user::stake` / `user::unstake_v2` / `user::stake_with_ve_sca_v2` 属于借款激励包（borrowIncentive），不是 veSCA**：借款、还款、开债仓时前端会先把 obligation 从激励池取出、操作后再放回，同笔出现；下方 §3 表中这几个函数的归类以此为准。veSCA 的真实操作在 `ve_sca` 模块（#12～#18）。
- **Swap 由 Scallop 自有 swap 包包裹聚合 router**，用户行为以 `swap::emit_swap_event` 为准；Orderbook 直接调 DeepBook v3，链上没有 Scallop 包的痕迹。

### 截图

**前端页面**

| Lending（Lending / Collateral / Borrowing Pools） | Lending → REWARD（Harvest All） |
|:-:|:-:|
| <img src="截图/Scallop-SUI-Lending页-20261013.png" width="460"> | <img src="截图/Scallop-SUI-Lending页REWARD-20261013.png" width="460"> |
| **Lending Supply 弹窗（2026-08-31 实测时截图）** | **Stake（2026-08-31 实测时截图）** |
| <img src="截图/Scallop-Lending-Supply-20260831.png" width="460"> | <img src="截图/Scallop-LiquidStake-20260831.png" width="460"> |
| **Stake** | **veSCA（LOCK / UNLOCK / Loyalty Program）** |
| <img src="截图/Scallop-SUI-Stake页-20261013.png" width="460"> | <img src="截图/Scallop-SUI-veSCA页-20261013.png" width="460"> |
| **Referral** | **Spot → Swap** |
| <img src="截图/Scallop-SUI-Referral页-20261013.png" width="460"> | <img src="截图/Scallop-SUI-Swap页-20261013.png" width="460"> |
| **Spot → Orderbook（DeepBook）** | **Bridge（Wormhole，无 Scallop 交易）** |
| <img src="截图/Scallop-SUI-Orderbook页-20261013.png" width="460"> | <img src="截图/Scallop-SUI-Bridge页-20261013.png" width="460"> |

**交易详情（每类一张，均为 suiscan）**

| 1 存款 Supply（`2K8JUv…`） | 2 取款 Withdraw（`88BwiG…`） |
|:-:|:-:|
| <img src="截图/Scallop-SUI-存款Supply交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-取款Withdraw交易-20261013.png" width="460"> |
| **3 开债仓（`HADvqk…`）** | **4 存入抵押（`2ceZur…`）** |
| <img src="截图/Scallop-SUI-开债仓交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-存入抵押交易-20261013.png" width="460"> |
| **5 取出抵押（`BDUcGd…`）** | **6 借款（`HdqCvK…`）** |
| <img src="截图/Scallop-SUI-取出抵押交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-借款交易-20261013.png" width="460"> |
| **7 带推荐人借款（`CZV8k1…`）** | **8 还款（`51W9sR…`）** |
| <img src="截图/Scallop-SUI-带推荐人借款交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-还款交易-20261013.png" width="460"> |
| **9 领借款激励（`ACs44p…`）** | **10 scaSUI 质押（`HV1V33…`）** |
| <img src="截图/Scallop-SUI-领借款激励交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-scaSUI质押交易-20261013.png" width="460"> |
| **11 scaSUI 解押（`F2XaJF…`）** | **12 veSCA 新建锁仓（`EfNSJT…`）** |
| <img src="截图/Scallop-SUI-scaSUI解押交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-veSCA新建锁仓交易-20261013.png" width="460"> |
| **13 veSCA 追加锁仓（`5faW8i…`）** | **14 veSCA 延长锁期（`8XicvD…`）** |
| <img src="截图/Scallop-SUI-veSCA追加锁仓交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-veSCA延长锁期交易-20261013.png" width="460"> |
| **15 veSCA 过期续锁（`DKzw5W…`）** | **16 veSCA 解锁赎回（`FCULdH…`）** |
| <img src="截图/Scallop-SUI-veSCA过期续锁交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-veSCA解锁赎回交易-20261013.png" width="460"> |
| **17 veSCA 合并（`899pyx…`）** | **18 veSCA 拆分（`DJoiP8…`）** |
| <img src="截图/Scallop-SUI-veSCA合并交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-veSCA拆分交易-20261013.png" width="460"> |
| **19 veSCA 忠诚度奖励（`98ktkp…`）** | **20 忠诚度奖励（`F3Eu34…`）** |
| <img src="截图/Scallop-SUI-veSCA忠诚度奖励领取交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-忠诚度奖励领取交易-20261013.png" width="460"> |
| **21 绑定推荐人（`ERUkPW…`）** | **22 解绑推荐人（`Yu9Qea…`）** |
| <img src="截图/Scallop-SUI-绑定推荐人交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-解绑推荐人交易-20261013.png" width="460"> |
| **23 领推荐收益（`yQzMjz…`）** | **24 Swap（`2c6tir…`）** |
| <img src="截图/Scallop-SUI-领推荐收益交易-20261013.png" width="460"> | <img src="截图/Scallop-SUI-Swap交易-20261013.png" width="460"> |
| **25 Orderbook 限价单（`7M8Q25…`）** |  |
| <img src="截图/Scallop-SUI-Orderbook限价单交易-20261013.png" width="460"> |  |

---

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
| **清算**（非用户主动） | `liquidate::liquidate` | `GY495ncU6gGazwst9gNPk39rYih95EPfHPBLpZpR4KcF` ⚠️ **链上失败交易（status=FAILURE，2026-10-13 复核），不可作样本** |
| 闪电贷借 / 还 | `flash_loan::borrow_flash_loan` / `repay_flash_loan` | `GY495ncU6gGazwst9gNPk39rYih95EPfHPBLpZpR4KcF` ⚠️ **链上失败交易（status=FAILURE，2026-10-13 复核），不可作样本** |

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
