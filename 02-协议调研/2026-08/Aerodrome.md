# Aerodrome（V2）— Base

> **链**：Base（chainId 8453） ｜ 浏览器 https://basescan.org ｜ https://base.blockscout.com
> **调研日期**：2026-08-12，2026-11-03 补全交易类型
> #1 Swap、#2 加流动性（volatile）为本人实测（钱包 `0x9da44afe5ba28aa42301c626155ea66eb544c33c`）；其余为公开样本，均为链上公开交易，非本人钱包，已逐条用 RPC 确认 status=0x1 并核对方法选择器
> V3（Slipstream 集中流动性）另见 [Aerodrome-V3.md](Aerodrome-V3.md)

## 合约

| 项 | 值 |
|---|---|
| 前端 | Dashboard https://aerodrome.finance/dash ｜ Swap https://aerodrome.finance/swap ｜ Liquidity https://aerodrome.finance/liquidity ｜ Launch pool https://aerodrome.finance/launch ｜ Vote https://aerodrome.finance/vote ｜ Lock https://aerodrome.finance/locks ｜ Create Lock https://aerodrome.finance/lock ｜ Incentivize https://aerodrome.finance/incentivize ｜ Upgrade https://aerodrome.finance/upgrade |
| PoolFactory | `0x420DD381b31aEf6683db6B902084cB0FFECe40Da` |
| Router（加 / 减流动性） | `0xcF77a3Ba9A5CA399B7c97c74d54e5b1Beb874E43` |
| UniversalRouter（前端 Swap） | `0xcAF22ce31298cf2bf1d152862f80216478ad7c67` |
| Voter | `0x16613524e02ad97eDfeF371bC883F2F5d6C480A5` |
| VotingEscrow（veAERO，ERC-721） | `0xeBf418Fe2512e7E6bd9b87a8F0f294aCDC67e6B4` |
| RewardsDistributor（rebase） | `0x227f65131A261548b057215bB1D5Ab2997964C7d` |
| AERO | `0x940181a94A35A4569E4529A3CDfB74e38FD98631` |
| AerodromeMigration（Upgrade 到 Aero） | `0x22a2f7340a3fb4c7c389d91ee954766b12476721` |
| 示例池 | volatile `0x6cdcb1c4a4d1c3c6d054b27ac5b77e89eafb971d`（USDC/AERO）；stable `0x27a8afa3bd49406e48a074350fb7b2020c43b2bd`（USDC/USDbC） |
| 示例池 Gauge / 激励合约 | Gauge `0x4f09bab2f0e15e2a078a227fe1537665f55b8360`；BribeVotingReward `0xae507c81432219593591534667729bef116cfd5d`（均为 USDC/AERO 池，由 `Voter.gauges(pool)` / `gaugeToBribe(gauge)` 查得） |
| LP 凭证 | ERC-20，由池合约本身铸造（`Volatile AMM - USDC/AERO`、`Stable AMM - USDC/USDbC`） |

## 交易类型覆盖

28 类：26 类有样本（本人实测 2 类、公开样本 24 类），2 类 ⬜。

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash | 来源 |
|---|---|---|---|---|---|
| 1 | Swap（多跳 ETH→WETH→wBLT→AERO） | Swap 页 | UniversalRouter · `execute` `0x3593564c` | [0x12457f9bf19697daf7888e5b8f8ca64d5e2486792549540cab5af6cbd40c8e75](https://basescan.org/tx/0x12457f9bf19697daf7888e5b8f8ca64d5e2486792549540cab5af6cbd40c8e75) | 本人实测 |
| 2 | 加流动性（volatile，USDC/AERO） | Liquidity → 池子 New deposit → Deposit | Router · `addLiquidity` `0x5a47ddc3`（stable=false） | [0x81f502b3061dcc7f661cf68817db7b786ee65532957bb7fe613524d1bd167ad5](https://basescan.org/tx/0x81f502b3061dcc7f661cf68817db7b786ee65532957bb7fe613524d1bd167ad5) | 本人实测 |
| 3 | 加流动性（stable，USDC/USDbC） | 同上，池型 Basic Stable | Router · `addLiquidity` `0x5a47ddc3`（stable=true） | [0xa6a515d795171c3e5289b2c51297a385d35976d9bb5c6e07030b2a5300ec4f1d](https://basescan.org/tx/0xa6a515d795171c3e5289b2c51297a385d35976d9bb5c6e07030b2a5300ec4f1d) | 公开样本 |
| 4 | 减流动性 | Dashboard → Liquidity Rewards → 仓位 Withdraw | Router · `removeLiquidity` `0x0dede6c4` | [0x7bb949273e44bbbcbaf141b21215d591898a91142c3cf39872ba9c6a5846216f](https://basescan.org/tx/0x7bb949273e44bbbcbaf141b21215d591898a91142c3cf39872ba9c6a5846216f) | 公开样本 |
| 5 | 质押 LP 到 Gauge | Dashboard → 仓位 Stake | Gauge · `deposit(uint256)` `0xb6b55f25` | [0x54e520c60395578a9b4a1a498f273cd120247afa94b82ed1a3eb490e4a9092c6](https://basescan.org/tx/0x54e520c60395578a9b4a1a498f273cd120247afa94b82ed1a3eb490e4a9092c6) | 公开样本 |
| 6 | 取消质押 | Dashboard → 仓位 Unstake | Gauge · `withdraw` `0x2e1a7d4d` | [0xdc8ca7513085db84978f9557cf924963866de3eadb966536fb87699979264176](https://basescan.org/tx/0xdc8ca7513085db84978f9557cf924963866de3eadb966536fb87699979264176) | 公开样本 |
| 7 | 领 AERO 排放 | Dashboard → Liquidity Rewards → Claim | Gauge · `getReward(address)` `0xc00007b0` | [0x5b2510582aa8bbb6293345982db6e600f62b02439989402d03acfdde70b12a21](https://basescan.org/tx/0x5b2510582aa8bbb6293345982db6e600f62b02439989402d03acfdde70b12a21) | 公开样本 |
| 8 | 领 LP 交易手续费（未质押仓位） | Dashboard → 仓位 Claim fees | Pool · `claimFees()` `0xd294f093` | [0x87835064c079c25b658aa812c91e2e779aeea4b02b9c6c4bdef9d71cd9f8a0f4](https://basescan.org/tx/0x87835064c079c25b658aa812c91e2e779aeea4b02b9c6c4bdef9d71cd9f8a0f4) | 公开样本 |
| 9 | 创建池（Basic volatile） | Liquidity → Launch pool | PoolFactory · `createPool(address,address,bool)` `0x36bf95a0` | [0x688fa9c8c0738167085a6fd1c2e4eeaec12a2d5b79f05df3e63b208c101fc301](https://basescan.org/tx/0x688fa9c8c0738167085a6fd1c2e4eeaec12a2d5b79f05df3e63b208c101fc301) | 公开样本 |
| 10 | 创建锁仓（AERO → veAERO） | Lock → Create Lock | VotingEscrow · `createLock` `0xb52c05fe` | [0x46accea014169e07388be4e829a45c038c8758bf323622a050ed012f223197ae](https://basescan.org/tx/0x46accea014169e07388be4e829a45c038c8758bf323622a050ed012f223197ae) | 公开样本 |
| 11 | 追加锁仓数量 | Dashboard → Locks → Increase | VotingEscrow · `increaseAmount` `0xb2383e55` | [0xec2e073257961ec15fb9081bbb372b65bfaad7e0b112c731bf9a138cb0fff34a](https://basescan.org/tx/0xec2e073257961ec15fb9081bbb372b65bfaad7e0b112c731bf9a138cb0fff34a) | 公开样本 |
| 12 | 延长锁仓期限 | Dashboard → Locks → Extend | VotingEscrow · `increaseUnlockTime` `0x9d507b8b` | [0xde70265f6097f01a454a528c7c3665fdb969a3663839c898bbef6c52f810374c](https://basescan.org/tx/0xde70265f6097f01a454a528c7c3665fdb969a3663839c898bbef6c52f810374c) | 公开样本 |
| 13 | 开启 Auto-Max Lock | Dashboard → Locks → Extend → Auto-Max | VotingEscrow · `lockPermanent` `0xe75b1c2e` | [0x1eb80dec3a68833c95af9d42b8b88b3e3c14f79d34d09c8db4a0e0cca46806a8](https://basescan.org/tx/0x1eb80dec3a68833c95af9d42b8b88b3e3c14f79d34d09c8db4a0e0cca46806a8) | 公开样本 |
| 14 | 关闭 Auto-Max Lock | 同上，关闭开关 | VotingEscrow · `unlockPermanent` `0x35b0f6bd` | [0x3c910a01e4bbe5474097d43cc6d8661153528724518d41c7c37b24686c57f791](https://basescan.org/tx/0x3c910a01e4bbe5474097d43cc6d8661153528724518d41c7c37b24686c57f791) | 公开样本 |
| 15 | 合并锁仓 | Dashboard → Locks → Merge | VotingEscrow · `merge` `0xd1c2babb` | [0x0d8350ef5d5ff4f5dd3084cf9c40b9e8aebdf3dc857e966143916bf386e97649](https://basescan.org/tx/0x0d8350ef5d5ff4f5dd3084cf9c40b9e8aebdf3dc857e966143916bf386e97649) | 公开样本 |
| 16 | 拆分锁仓 | Dashboard → Locks → Split | VotingEscrow · `split` `0x4b19becc` | ⬜ 无样本 | `canSplit(address(0))` 返回 false，仅白名单地址可拆分，普通钱包调用会 revert；Split 事件按 topic 检索无结果 |
| 17 | 转移 veNFT | Dashboard → Locks → Transfer | VotingEscrow · `transferFrom` `0x23b872dd` | [0xc32559eaeb2f6efcca106d59b72de87b4999b6e7202916eb7f103a6725e45a24](https://basescan.org/tx/0xc32559eaeb2f6efcca106d59b72de87b4999b6e7202916eb7f103a6725e45a24) | 公开样本 |
| 18 | 到期取回 AERO | Dashboard → Locks → Withdraw（锁仓到期后） | VotingEscrow · `withdraw` `0x2e1a7d4d` | [0x84de63620b63fa194b14bbdbe8a5c9cefabdb1bcd5a396c0de6b5efe3ace3c2b](https://basescan.org/tx/0x84de63620b63fa194b14bbdbe8a5c9cefabdb1bcd5a396c0de6b5efe3ace3c2b) | 公开样本 |
| 19 | 投票 | Vote 页（2026-10-22 起已关闭） | Voter · `vote` `0x7ac09bf7` | [0x9d487be17183e06833339128253a1f5946537a5566f89c8ebfcb20dea08f7982](https://basescan.org/tx/0x9d487be17183e06833339128253a1f5946537a5566f89c8ebfcb20dea08f7982) | 公开样本 |
| 20 | 重置投票 | Dashboard → Locks → Reset；Upgrade 流程第 4 步 | Voter · `reset` `0x310bd74b` | [0x175e18764f44960eb524164db77a735607b52b0bbc375be8cf71bc8325e320c5](https://basescan.org/tx/0x175e18764f44960eb524164db77a735607b52b0bbc375be8cf71bc8325e320c5) | 公开样本 |
| 21 | 领投票奖励（手续费 + 激励） | Dashboard → Voting Rewards → Claim | Voter · `claimBribes` `0x7715ee75`（同实现的 `claimFees` `0x666256aa`） | [0x77e00c3718f6c7394ecdafd67330e9f5ad0393746fb16be134460852a88aefad](https://basescan.org/tx/0x77e00c3718f6c7394ecdafd67330e9f5ad0393746fb16be134460852a88aefad) | 公开样本 |
| 22 | 领 rebase | Dashboard → Locks → Claim rebase | RewardsDistributor · `claim` `0x379607f5` | [0x15ad1db3b97e9654a19ffb515cd7a40b6a8a19d7d7801f9243d7a6bad444eb7f](https://basescan.org/tx/0x15ad1db3b97e9654a19ffb515cd7a40b6a8a19d7d7801f9243d7a6bad444eb7f) | 公开样本 |
| 23 | 锁仓存入 Relay | Lock → Relays → Deposit Lock | Voter · `depositManaged` `0xe0c11f9a` | [0x096b76474007b5d95144142b1fff7ca596a956bbd07af6ea6ea8dbc360f9a3ea](https://basescan.org/tx/0x096b76474007b5d95144142b1fff7ca596a956bbd07af6ea6ea8dbc360f9a3ea) | 公开样本 |
| 24 | 从 Relay 取出锁仓 | Dashboard → Locks → Withdraw from Relay；Upgrade 流程第 3 步 | Voter · `withdrawManaged` `0x370fb5fa` | [0x4afc4a962bcca024fdf1f337d650a8c5eb7eebf57e4623a1585af8b83008a7da](https://basescan.org/tx/0x4afc4a962bcca024fdf1f337d650a8c5eb7eebf57e4623a1585af8b83008a7da) | 公开样本 |
| 25 | 添加激励（bribe） | Incentivize 页 | BribeVotingReward · `notifyRewardAmount` `0xb66503cf` | [0xfacc01e89977ff6f4836979aff6eeb452b3e7431f0068ec7a8bf11ea806bfc4b](https://basescan.org/tx/0xfacc01e89977ff6f4836979aff6eeb452b3e7431f0068ec7a8bf11ea806bfc4b) | 公开样本 |
| 26 | 升级 AERO 到 Aero | Upgrade → AERO tokens → Upgrade | AerodromeMigration · `depositLiquid` `0x781ba9b8` | [0x9418363bce803848da21128a0655ddb48cc7eeb62c4412d166020428b765367f](https://basescan.org/tx/0x9418363bce803848da21128a0655ddb48cc7eeb62c4412d166020428b765367f) | 公开样本 |
| 27 | 升级 veAERO 到 sAERO | Upgrade → Locks → Upgrade | AerodromeMigration · `depositVeNFT` `0xc0ddab40` | [0x71dd23efccdb852ea782bcb383fe138ca93de76a476b30a6e6323f8f2f005580](https://basescan.org/tx/0x71dd23efccdb852ea782bcb383fe138ca93de76a476b30a6e6323f8f2f005580) | 公开样本 |
| 28 | 升级 LP 仓位 | Upgrade → Deposits → Upgrade | — | ⬜ 无样本 | 迁移合约近 150 笔只有 `depositLiquid` / `depositVeNFT`，测试钱包无 LP 仓位，看不到这一步实际调用哪个合约 |

Router 另有 `addLiquidityETH` `0xb7e0d4c0` / `removeLiquidityETH` `0xd7b0e0a5`（池子一侧为 ETH 时使用），以及 `swapExactTokensForTokens` 等直连 swap 方法（多为聚合器调用），解析口径同 #2～#4。Upgrade 页对无仓位钱包直接显示 “Upgrade complete”，页面不展开。

## 截图

**前端页面**

| 本人实测：Swap 成功（#1） | 本人实测：New deposit USDC/AERO（#2） |
|:-:|:-:|
| <img src="截图/Aerodrome-swap-20260812.png" width="460"> | <img src="截图/Aerodrome-swap2-20260812.png" width="460"> |
| **Swap 页** | **Liquidity 池列表（含 Launch pool）** |
| <img src="截图/Aerodrome-Swap页-20261103.png" width="460"> | <img src="截图/Aerodrome-Liquidity池列表页-20261103.png" width="460"> |
| **New deposit（Basic Volatile）** | **Launch pool** |
| <img src="截图/Aerodrome-NewDeposit页-20261103.png" width="460"> | <img src="截图/Aerodrome-LaunchPool页-20261103.png" width="460"> |
| **Dashboard（Upgrade 横幅 / Liquidity Rewards / Locks / Voting Rewards）** | **Lock 页（Create Lock / Relays）** |
| <img src="截图/Aerodrome-Dashboard页-20261103.png" width="460"> | <img src="截图/Aerodrome-Lock页-20261103.png" width="460"> |
| **Create new lock** | **Relay deposit** |
| <img src="截图/Aerodrome-CreateLock页-20261103.png" width="460"> | <img src="截图/Aerodrome-Relay存入页-20261103.png" width="460"> |
| **Vote 页（迁移后已关闭）** | **Incentivize 页** |
| <img src="截图/Aerodrome-Vote页-20261103.png" width="460"> | <img src="截图/Aerodrome-Incentivize页-20261103.png" width="460"> |

**交易详情（公开样本，均为 blockscout）**

| 3 加流动性 stable（`0xa6a515…`） | 4 减流动性（`0x7bb949…`） |
|:-:|:-:|
| <img src="截图/Aerodrome-加流动性stable交易-20261103.png" width="460"> | <img src="截图/Aerodrome-减流动性交易-20261103.png" width="460"> |
| **5 质押 LP（`0x54e520…`）** | **6 取消质押（`0xdc8ca7…`）** |
| <img src="截图/Aerodrome-质押LP交易-20261103.png" width="460"> | <img src="截图/Aerodrome-取消质押交易-20261103.png" width="460"> |
| **7 领 AERO 排放（`0x5b2510…`）** | **8 领 LP 手续费（`0x878350…`）** |
| <img src="截图/Aerodrome-领AERO排放交易-20261103.png" width="460"> | <img src="截图/Aerodrome-领LP手续费交易-20261103.png" width="460"> |
| **9 创建池（`0x688fa9…`）** | **10 创建锁仓（`0x46acce…`）** |
| <img src="截图/Aerodrome-创建池交易-20261103.png" width="460"> | <img src="截图/Aerodrome-创建锁仓交易-20261103.png" width="460"> |
| **11 追加锁仓（`0xec2e07…`）** | **12 延长锁仓（`0xde7026…`）** |
| <img src="截图/Aerodrome-追加锁仓交易-20261103.png" width="460"> | <img src="截图/Aerodrome-延长锁仓交易-20261103.png" width="460"> |
| **13 开启 Auto-Max（`0x1eb80d…`）** | **14 关闭 Auto-Max（`0x3c910a…`）** |
| <img src="截图/Aerodrome-开启AutoMax交易-20261103.png" width="460"> | <img src="截图/Aerodrome-关闭AutoMax交易-20261103.png" width="460"> |
| **15 合并锁仓（`0x0d8350…`）** | **17 转移 veNFT（`0xc32559…`）** |
| <img src="截图/Aerodrome-合并锁仓交易-20261103.png" width="460"> | <img src="截图/Aerodrome-转移veNFT交易-20261103.png" width="460"> |
| **18 到期取回（`0x84de63…`）** | **19 投票（`0x9d487b…`）** |
| <img src="截图/Aerodrome-到期取回交易-20261103.png" width="460"> | <img src="截图/Aerodrome-投票交易-20261103.png" width="460"> |
| **20 重置投票（`0x175e18…`）** | **21 领投票奖励（`0x77e00c…`）** |
| <img src="截图/Aerodrome-重置投票交易-20261103.png" width="460"> | <img src="截图/Aerodrome-领投票奖励交易-20261103.png" width="460"> |
| **22 领 rebase（`0x15ad1d…`）** | **23 存入 Relay（`0x096b76…`）** |
| <img src="截图/Aerodrome-领Rebase交易-20261103.png" width="460"> | <img src="截图/Aerodrome-存入Relay交易-20261103.png" width="460"> |
| **24 取出 Relay（`0x4afc4a…`）** | **25 添加激励（`0xfacc01…`）** |
| <img src="截图/Aerodrome-取出Relay交易-20261103.png" width="460"> | <img src="截图/Aerodrome-添加激励交易-20261103.png" width="460"> |
| **26 升级 AERO（`0x941836…`）** | **27 升级 veAERO（`0x71dd23…`）** |
| <img src="截图/Aerodrome-升级AERO交易-20261103.png" width="460"> | <img src="截图/Aerodrome-升级veAERO交易-20261103.png" width="460"> |

## 开发注意

- **Swap 金额以池级 `Swap` 事件为准，不按 Router 归属**：前端 Swap 走 UniversalRouter（#1），每个池的 `Swap(sender, to, amount0In, amount1In, amount0Out, amount1Out)` 里 `sender` 是 Router 而不是用户，`to` 是下一跳；多跳要逐池解析。Router 最后的聚合事件不带金额。大量 swap 由聚合器直接打到池子，也不要按 Router 白名单过滤。
- **LP 是 ERC-20（池合约自己 mint / burn），质押后在 Gauge 里**：#5 质押后 LP 会转进 Gauge，用户钱包里的 `balanceOf` 变成 0，所以仓位要加上 `Gauge.balanceOf(user)`。池型看 `pool.stable()`，或看 LP 名称前缀是 `Volatile AMM` 还是 `Stable AMM`。#2、#3 都调用 `addLiquidity`，靠第 3 个参数 `stable` 区分。
- **veAERO 是 ERC-721，锁仓类操作看 VotingEscrow `Deposit` 事件的 `depositType`（topic3）**：1 = 创建（#10），2 = 追加（#11），3 = 延长（#12）。2026-10-22 起 Aerodrome 并入 Aero：Vote 页已关闭，示例 Gauge 的 `isAlive` 为 false，Liquidity 页排放 APR 全部显示 N/A。Upgrade 交易（#26 / #27）里也会出现 VotingEscrow 的 Deposit、Merge、Transfer 事件，要识别成迁移转出，不能当成普通锁仓。

## 待确认

- 拆分锁仓（#16）要白名单，普通用户实际用不了，是否还需要覆盖
- 升级 LP 仓位（#28）的链上形态：需要一个持有 LP 仓位的钱包实测一笔
- Upgrade 类交易（#26～#28）是否纳入 Aerodrome V2 的解析范围；Launch pool 前端是直接调 `PoolFactory.createPool`（#9 样本就是直调），还是在首次 `addLiquidity` 时由 Router 自动建池
