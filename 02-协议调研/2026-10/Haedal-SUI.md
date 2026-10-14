# Haedal — SUI

> **链**：SUI（交易标识为 digest，base58） ｜ 浏览器 https://suiscan.xyz/mainnet ｜ 取数 GraphQL `https://graphql.mainnet.sui.io/graphql`
> **调研日期**：2026-08-10 本人实测，2026-10-13 补全交易类型 ｜ 本人实测钱包 `0x27408ed9752b653e009de0ce83608e7a3dd4bccc5362045978b4b38bb3159b0e`
> 本人实测与公开样本已在「来源」列标注；公开样本均为链上公开交易，非本人钱包，已逐条确认成功

## 合约

| 项 | 值 |
|---|---|
| 前端 | Stake https://www.haedal.xyz/stake ｜ Swap https://www.haedal.xyz/swap ｜ Liquidity https://www.haedal.xyz/vault ｜ Lending Vault https://www.haedal.xyz/lending ｜ veHAEDAL https://www.haedal.xyz/veHAEDAL |
| haSUI 质押包（前端当前调用版本） | `0x19e6ea7f5ced4f090e20da794cc80349a03e638940ddb95155a4e301f5f4967c`（original `0xbde4ba4c2e274a60ce15c1cfff9e5c42e41654ac8b6d906a57efa4bd3c29f47d`） |
| haSUI coinType | `0xbde4ba4c2e274a60ce15c1cfff9e5c42e41654ac8b6d906a57efa4bd3c29f47d::hasui::HASUI` |
| haWAL 质押包 | `0xec3d810a492541d4013c551b7b34d8e9d74a81ebd3642bad78611209b86c214d`（original `0x8b4d553839b219c3fd47608a0cc3d5fcc572cb25d41b7df3833208586a8d2470`） |
| 聚合 router（Swap Out / Swap 页） | `0xffd4058af7d6f6d66c335930cced8c91e38ec943e2392232aee27e8127e684e9`；haSUI / haWAL 适配器 `0xb717f942133a66d390f5114c42b79736e83decdc69d6ba6c8db106fcc1fbe6ef` |
| Liquidity Vault | `0xbf180d2cd8a9126a8b363fcd6bd8b1d57d7ac3fc6c6c11557713f0f27dc21ea7` |
| Lending Vault | `0x3777fbddf314316bb4b3618538255b09eff5460de094f5084f21edbeacbec9a8` |
| veHAEDAL | `0x2520e3de754d9785d29b34e8f15a92556fee5492d54ea7f548c44ab10a87bc4e`；奖励 `0x451682db8053eced4e8cd1aa3deb9c52f61c9b34650f536ed9c4c1a58327e329` |

## 交易类型覆盖

21 类，全部有样本：本人实测 3 类，公开样本 18 类。

| # | 交易类型 | 前端入口 | 模块 · 函数 | digest | 来源 |
|---|---|---|---|---|---|
| 1 | haSUI 质押（Automated） | Stake → SUI → Stake | 质押包 · `interface::request_stake` | [8wToiZwttDRZnAXc5X5xf9boD9Njee77qumFokExup18](https://suiscan.xyz/mainnet/tx/8wToiZwttDRZnAXc5X5xf9boD9Njee77qumFokExup18) | 本人实测 |
| 2 | haSUI 质押（Manual 自选节点） | Stake → Manual → Choose a Validator | 质押包 · `interface::request_stake`（validator 参数非 `0x0`） | [GGA2CSZq1ZrvKPpwbCCtskjAMCb1WXreYtKpN8TiZ34U](https://suiscan.xyz/mainnet/tx/GGA2CSZq1ZrvKPpwbCCtskjAMCb1WXreYtKpN8TiZ34U) | 公开样本 |
| 3 | haSUI 普通解押（等 epoch） | Withdraw → Unstake | 质押包 · `interface::request_unstake_delay` | [Cx9XQ1BsUuKmmd9bJ76CBT2TypvKxs3w96ZBf5JYUSGy](https://suiscan.xyz/mainnet/tx/Cx9XQ1BsUuKmmd9bJ76CBT2TypvKxs3w96ZBf5JYUSGy) | 本人实测 |
| 4 | haSUI 即时解押（费 0.02%） | Withdraw → Instant Unstake | 质押包 · `interface::request_unstake_instant_v2` | [AvKhsPnSWSJXjgWzTJ2aBJSj4wh3L9yuttnYknonhzin](https://suiscan.xyz/mainnet/tx/AvKhsPnSWSJXjgWzTJ2aBJSj4wh3L9yuttnYknonhzin) | 公开样本 |
| 5 | haSUI Swap Out | Withdraw → Swap Out | 聚合 router · `new_swap_context` → 适配器 `hasui::swap` → router `confirm_swap` | [BDw1Khj8SCcMRGP1mPTmofFK7j4b3NtMUZgqwVfqhd8C](https://suiscan.xyz/mainnet/tx/BDw1Khj8SCcMRGP1mPTmofFK7j4b3NtMUZgqwVfqhd8C) | 本人实测 |
| 6 | haSUI 领取（Unstake 到期后） | Claim | 质押包 · `interface::claim_v2` | [HCmjgVXVMDx4n5ADEx7dv4fVW5ah758KACCdorrSEKH1](https://suiscan.xyz/mainnet/tx/HCmjgVXVMDx4n5ADEx7dv4fVW5ah758KACCdorrSEKH1) | 公开样本 |
| 7 | haWAL 质押 | Stake → WAL → Stake | haWAL 包 · `interface::request_stake` | [Gt1uj66EkdsfRrYSzA9TzZdcUnUdbpqoRf7uPjdUeBnn](https://suiscan.xyz/mainnet/tx/Gt1uj66EkdsfRrYSzA9TzZdcUnUdbpqoRf7uPjdUeBnn) | 公开样本 |
| 8 | haWAL 普通解押（等 epoch，约 14 天） | WAL → Withdraw → Unstake | haWAL 包 · `interface::request_unstake_delay` | [6q4dyBwbfUWoHZMGKikN9ZG4vtpsnW9Z3hb24UUKY3QQ](https://suiscan.xyz/mainnet/tx/6q4dyBwbfUWoHZMGKikN9ZG4vtpsnW9Z3hb24UUKY3QQ) | 公开样本 |
| 9 | haWAL 即时解押 | WAL → Withdraw → Instant Unstake | haWAL 包 · `interface::request_unstake_instant` | [6a9YEysXBkcfQQG9FTcueMBnaTsSw1dZ6Ac2tczDoeu1](https://suiscan.xyz/mainnet/tx/6a9YEysXBkcfQQG9FTcueMBnaTsSw1dZ6Ac2tczDoeu1) | 公开样本 |
| 10 | haWAL Swap Out | WAL → Withdraw → Swap Out | 聚合 router（旧版 `0xde5d696a…`）· `new_swap_context` → 适配器 `hawal::swap` → `confirm_swap` | [7FeUu6mMxy7C9aWFRSBuLMGUbHb8c9WrVNrzVLnnvKT7](https://suiscan.xyz/mainnet/tx/7FeUu6mMxy7C9aWFRSBuLMGUbHb8c9WrVNrzVLnnvKT7) | 公开样本（2026-03） |
| 11 | haWAL 领取 | WAL → Claim | haWAL 包 · `interface::claim` | [2ULiJP28WcrfXrh39p5jUfjk2zzxGs52EYmtrYhcJfdw](https://suiscan.xyz/mainnet/tx/2ULiJP28WcrfXrh39p5jUfjk2zzxGs52EYmtrYhcJfdw) | 公开样本 |
| 12 | Swap（任意币对） | Swap 页（Powered by Cetus） | 聚合 router · `new_swap_context_v3` → `<DEX>::swap` → `confirm_swap_v3` | [EKidm4d4yR28Eq7Mo7Kf9hptrRQRoVq48Z4jNEFWW116](https://suiscan.xyz/mainnet/tx/EKidm4d4yR28Eq7Mo7Kf9hptrRQRoVq48Z4jNEFWW116) | 公开样本（聚合器通用交易，链上无法区分是否经 Haedal 前端发起） |
| 13 | Liquidity Vault 存入 | Liquidity → Deposit（可开 Zap） | Liquidity Vault 包 · `vault::deposit` | [J2LwFFV7a7WoV413gPRV84pVVu7wirvTZeWSd5JbaY6Q](https://suiscan.xyz/mainnet/tx/J2LwFFV7a7WoV413gPRV84pVVu7wirvTZeWSd5JbaY6Q) | 公开样本 |
| 14 | Liquidity Vault 取出 | Liquidity → 详情 → Withdraw | Liquidity Vault 包 · `vault::new_withdraw_cert` → `withdraw_clmm` → `withdraw_asset` → `destroy_withdraw_cert` | [G2e1een6cpfdLA58daHShRXehR6msDfn2hRXhd2CQ7SY](https://suiscan.xyz/mainnet/tx/G2e1een6cpfdLA58daHShRXehR6msDfn2hRXhd2CQ7SY) | 公开样本 |
| 15 | Lending Vault 存入 | Lending Vault → Deposit | Lending Vault 包 · `vault_entry::begin_deposit` + `complete_deposit_return` | [HRwHcdybLtgKTRGVoVSx26hxqAiRuw4nSqUT6cdafXod](https://suiscan.xyz/mainnet/tx/HRwHcdybLtgKTRGVoVSx26hxqAiRuw4nSqUT6cdafXod) | 公开样本 |
| 16 | Lending Vault 取出 | Lending Vault → 详情 → Withdraw | Lending Vault 包 · `vault_entry::begin_withdraw_idle_only` + `complete_withdraw`（开 Auto Swap 时为 `complete_withdraw_return` + 聚合器 swap） | [D1NTw3nuBZTpVNc6yxBzA6vPGdhAt8HQ6SYrzstxEzcL](https://suiscan.xyz/mainnet/tx/D1NTw3nuBZTpVNc6yxBzA6vPGdhAt8HQ6SYrzstxEzcL) | 公开样本 |
| 17 | veHAEDAL 锁仓 | veHAEDAL → Lock HAEDAL | veHAEDAL 包 · `staking::add_stake` | [7gAAmZarkP5h4mwuxciHZe4Hg2uZ1meCuzvLuo71fXSQ](https://suiscan.xyz/mainnet/tx/7gAAmZarkP5h4mwuxciHZe4Hg2uZ1meCuzvLuo71fXSQ) | 公开样本 |
| 18 | veHAEDAL 追加锁仓数量 | veHAEDAL（已有锁仓，需连钱包） | veHAEDAL 包 · `staking::add_to_existing_stake` | [3juBPvWS3pTBP6rZqMiUob4K7rtQAkKPbjZSmCA9Ht46](https://suiscan.xyz/mainnet/tx/3juBPvWS3pTBP6rZqMiUob4K7rtQAkKPbjZSmCA9Ht46) | 公开样本 |
| 19 | veHAEDAL 延长锁期 | veHAEDAL（已有锁仓，需连钱包） | veHAEDAL 包 · `staking::extend_existing_lock` | [EF7PPDyuETXMSe75NmRgVqyR7v4N8CTnDZbYqsWq4n4S](https://suiscan.xyz/mainnet/tx/EF7PPDyuETXMSe75NmRgVqyR7v4N8CTnDZbYqsWq4n4S) | 公开样本 |
| 20 | veHAEDAL 到期解锁 | veHAEDAL（到期后，需连钱包） | veHAEDAL 包 · `staking::unstake_and_claim` | [7NpfeheAB11vy27wWtEkp5T6aXA8QzvX5euUPTiP98ij](https://suiscan.xyz/mainnet/tx/7NpfeheAB11vy27wWtEkp5T6aXA8QzvX5euUPTiP98ij) | 公开样本 |
| 21 | veHAEDAL 领奖励 | veHAEDAL → Your Rewards → Claim | veHAEDAL 奖励包 · `reward_distribution::claim_rewards_v2` | [BZw7NGiD95aydMbkBW5gpJGCBHG4Fpcyb8jdARhHNvPv](https://suiscan.xyz/mainnet/tx/BZw7NGiD95aydMbkBW5gpJGCBHG4Fpcyb8jdARhHNvPv) | 公开样本 |

页面有入口但**不产生 Haedal 交易**：Stake 菜单下的 DeFi（第三方协议策略列表，点击跳外链）、Stats（数据页）、Nodes（菜单项，`/nodes` 直达为 404）；Lending Vault 的 Risk Disclosure 勾选（仅本地确认）。

## 截图

**前端页面**

| Stake（SUI / Automated，本人实测时截图） | Stake（Manual 自选节点） |
|:-:|:-:|
| <img src="截图/Haedal-haSUI-stake-20260810.png" width="460"> | <img src="截图/Haedal-SUI-Stake页Manual选节点-20261013.png" width="460"> |
| **Withdraw → Unstake（本人实测时截图）** | **Withdraw → Instant Unstake** |
| <img src="截图/Haedal-haSUI-unstake-20260810.png" width="460"> | <img src="截图/Haedal-SUI-Withdraw页InstantUnstake-20261013.png" width="460"> |
| **Withdraw → Swap Out（本人实测时截图）** | **Claim** |
| <img src="截图/Haedal-haSUI-swapout-20260810.png" width="460"> | <img src="截图/Haedal-SUI-Claim页-20261013.png" width="460"> |
| **Stake（WAL）** | **Withdraw（WAL）** |
| <img src="截图/Haedal-SUI-Stake页WAL-20261013.png" width="460"> | <img src="截图/Haedal-SUI-Withdraw页WAL-20261013.png" width="460"> |
| **Swap** | **Liquidity Vault 列表** |
| <img src="截图/Haedal-SUI-Swap页-20261013.png" width="460"> | <img src="截图/Haedal-SUI-LiquidityVault页-20261013.png" width="460"> |
| **Liquidity Vault 详情（Deposit / Withdraw / Zap）** | **Lending Vault 列表** |
| <img src="截图/Haedal-SUI-LiquidityVault详情页-20261013.png" width="460"> | <img src="截图/Haedal-SUI-LendingVault页-20261013.png" width="460"> |
| **Lending Vault 详情（Deposit / Withdraw / Auto Swap）** | **veHAEDAL** |
| <img src="截图/Haedal-SUI-LendingVault详情页-20261013.png" width="460"> | <img src="截图/Haedal-SUI-veHAEDAL页-20261013.png" width="460"> |
| **DeFi（第三方策略列表，无链上交易）** | **Stats（无链上交易）** |
| <img src="截图/Haedal-SUI-DeFi页-20261013.png" width="460"> | <img src="截图/Haedal-SUI-Stats页-20261013.png" width="460"> |

**交易详情（每类一张，均为 suiscan）**

| 1 haSUI 质押 · 本人实测（`8wToiZ…`） | 2 haSUI 手动选节点质押（`GGA2CS…`） |
|:-:|:-:|
| <img src="截图/Haedal-SUI-haSUI质押本人交易-20261014.png" width="460"> | <img src="截图/Haedal-SUI-haSUI手动选节点质押交易-20261013.png" width="460"> |
| **3 haSUI 普通解押 · 本人实测（`Cx9XQ1…`）** | **4 haSUI 即时解押（`AvKhsP…`）** |
| <img src="截图/Haedal-SUI-haSUI普通解押本人交易-20261014.png" width="460"> | <img src="截图/Haedal-SUI-haSUI即时解押交易-20261013.png" width="460"> |
| **5 haSUI Swap Out · 本人实测（`BDw1Kh…`）** | **6 haSUI 领取（`HCmjgV…`）** |
| <img src="截图/Haedal-SUI-haSUI-SwapOut本人交易-20261014.png" width="460"> | <img src="截图/Haedal-SUI-haSUI领取交易-20261013.png" width="460"> |
| **7 haWAL 质押（`Gt1uj6…`）** | **8 haWAL 普通解押（`6q4dyB…`）** |
| <img src="截图/Haedal-SUI-haWAL质押交易-20261013.png" width="460"> | <img src="截图/Haedal-SUI-haWAL普通解押交易-20261013.png" width="460"> |
| **9 haWAL 即时解押（`6a9YEy…`）** | **10 haWAL Swap Out（`7FeUu6…`）** |
| <img src="截图/Haedal-SUI-haWAL即时解押交易-20261013.png" width="460"> | <img src="截图/Haedal-SUI-haWAL-SwapOut交易-20261013.png" width="460"> |
| **11 haWAL 领取（`2ULiJP…`）** | **12 Swap（`EKidm4…`）** |
| <img src="截图/Haedal-SUI-haWAL领取交易-20261013.png" width="460"> | <img src="截图/Haedal-SUI-Swap交易-20261013.png" width="460"> |
| **13 Liquidity Vault 存入（`J2LwFF…`）** | **14 Liquidity Vault 取出（`G2e1ee…`）** |
| <img src="截图/Haedal-SUI-流动性金库存入交易-20261013.png" width="460"> | <img src="截图/Haedal-SUI-流动性金库取出交易-20261013.png" width="460"> |
| **15 Lending Vault 存入（`HRwHcd…`）** | **16 Lending Vault 取出（`D1NTw3…`）** |
| <img src="截图/Haedal-SUI-借贷金库存入交易-20261013.png" width="460"> | <img src="截图/Haedal-SUI-借贷金库取出交易-20261013.png" width="460"> |
| **17 veHAEDAL 锁仓（`7gAAmZ…`）** | **18 veHAEDAL 追加锁仓（`3juBPv…`）** |
| <img src="截图/Haedal-SUI-veHAEDAL锁仓交易-20261013.png" width="460"> | <img src="截图/Haedal-SUI-veHAEDAL追加锁仓交易-20261013.png" width="460"> |
| **19 veHAEDAL 延长锁期（`EF7PPD…`）** | **20 veHAEDAL 到期解锁（`7Npfeh…`）** |
| <img src="截图/Haedal-SUI-veHAEDAL延长锁期交易-20261013.png" width="460"> | <img src="截图/Haedal-SUI-veHAEDAL解锁交易-20261013.png" width="460"> |
| **21 veHAEDAL 领奖励（`BZw7NG…`）** |  |
| <img src="截图/Haedal-SUI-veHAEDAL领奖励交易-20261013.png" width="460"> |  |

## 开发注意

- **三条赎回路径是三种交易，按函数 / 事件区分，不能只看余额**：普通 Unstake（#3，`request_unstake_delay`，事件 `UserNormalUnstaked`）当笔 haSUI 扣除但 SUI 不到账，需等 epoch 后再发 Claim（#6）；Instant Unstake（#4，`request_unstake_instant_v2`，收 0.02% 费）是协议自身的即时赎回；Swap Out（#5）走聚合 router，同笔同时出现 `UserInstantUnstaked` 和 router 的 `SwapEvent`，对价来自 DEX 池子，含滑点。
- **按 original package 识别，不要写死版本地址**：haSUI 包共 8 个版本，前端当前调用 v7 `0x19e6ea7f…`，v8 `0x126e4cfb…` 也有用户调用；haWAL 同理（5 个版本）。按 MoveCall 过滤需覆盖全部版本。
- **两个 Vault 的存取交易里混有大量 keeper 调用**（Pyth 喂价、`calculate_aum`、聚合器 swap 等，一笔 15～20 个 MoveCall），用户动作只认 `vault::deposit` / `vault::new_withdraw_cert`（Liquidity）和 `vault_entry::begin_deposit` / `begin_withdraw_*`（Lending）。

## 待确认

- Swap Out（#5 / #10）是否计为「赎回」：协议事件与 DEX 事件同笔出现，需产品定义
- haSUI / SUI 汇率的链上取数入口（哪个对象的哪个字段）
- Swap 页（#12）为聚合器通用交易，链上无法归因到 Haedal 前端，是否纳入解析范围
