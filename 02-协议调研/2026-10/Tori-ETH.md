# Tori Finance — trUSD / strUSD（机构策略稳定币）· Ethereum

> **状态**：✅ 交易类型覆盖已补全（2026-10-13）：12 类上链交易，11 类有 hash + 交易截图（含本人 9/1 实测 3 类），⬜ 2 处（Monad 金库跨链存入；兑换选 USDT/AUSD/strUSD 时的路由）；前端 10 张页面截图
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Ethereum**

## 交易类型覆盖（2026-10-13）

前端导航：赚取（兑换 / 质押〔质押·解除质押·WITHDRAW〕/ YT 锁定 / 两个生态金库）/ 跨链 / 活动 / DeFi / 透明度。共 13 类会上链，12 类有 hash（2026-10-13 复核补 `claimPendleYield`）（已逐条确认成功且日志里有对应事件），另有兑换的其他币种路由和 Monad 金库跨链存入标 ⬜。

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash |
|---|---|---|---|---|
| 1 | 兑换获取 trUSD（USDC→trUSD） | 赚取 → 兑换 | Curve trUSD/USDC 池 `0xb723a224c9ACF3891B20437B4d55dd45600F5FA3` · `exchange` | [0xece57d0b3b9a430f172e77382e0c1d4df30e2cd84f9fcc497d18d579f8efc7ca](https://etherscan.io/tx/0xece57d0b3b9a430f172e77382e0c1d4df30e2cd84f9fcc497d18d579f8efc7ca)（本人实测） |
|  | 兑换（USDT / AUSD / strUSD → trUSD） | 赚取 → 兑换（下拉选币） | ⬜ 未连钱包不出报价，路由合约无法确认 | ⬜ |
| 2 | 质押（trUSD→strUSD） | 赚取 → 质押 → 质押 | strUSD `0x280839980a7eD0D7717F64125fE241012E5F5815` · `deposit` | [0x58d57d11a4b199bdd5808d5995aa9c935fdbf5837803847001cc50cce79fea0b](https://etherscan.io/tx/0x58d57d11a4b199bdd5808d5995aa9c935fdbf5837803847001cc50cce79fea0b)（本人实测） |
| 3 | 解除质押（发起 7 天冷却，按份额） | 质押 → 解除质押 | strUSD · `cooldownShares` | [0xbdbf713ed1e9b9264aa2746a813a585e971b02789261b71b1923bd4bda5c9434](https://etherscan.io/tx/0xbdbf713ed1e9b9264aa2746a813a585e971b02789261b71b1923bd4bda5c9434)（本人实测） |
|  | 解除质押（发起 7 天冷却，按数量） |  | strUSD · `cooldownAssets` | [0x5c9e169160a136d1ccabbb42b4270a8ae38b9d4d229999240c7f818d72465c22](https://etherscan.io/tx/0x5c9e169160a136d1ccabbb42b4270a8ae38b9d4d229999240c7f818d72465c22)（公开样本） |
| 4 | 冷却期满提取 trUSD | 质押 → WITHDRAW（需连钱包） | strUSD · `unstake` | [0x31b5fec2f8f23e398a722e34fed42cc43de9ed74f045eff274d98a52d9dc876e](https://etherscan.io/tx/0x31b5fec2f8f23e398a722e34fed42cc43de9ed74f045eff274d98a52d9dc876e)（公开样本） |
| 5 | 取消冷却（重新质押） | 质押 → WITHDRAW（需连钱包） | strUSD · `cancelCooldown` | [0x873e23e451263f4d1beff035079d4579c1c1bf2150802459e10483b25908f413](https://etherscan.io/tx/0x873e23e451263f4d1beff035079d4579c1c1bf2150802459e10483b25908f413)（公开样本） |
| 6 | YT 锁定（YT-strUSD→lYT-strUSD） | 赚取 → YT 锁定 | PendleYTLock `0x6569C554cBf71Dfcc32aEc48B0eDd2582f5C4a96` · `deposit` | [0xd49d57d103e4c589539fef66e76c2af7d6b2dea9eae4cc64331da0b9b1c994ef](https://etherscan.io/tx/0xd49d57d103e4c589539fef66e76c2af7d6b2dea9eae4cc64331da0b9b1c994ef)（公开样本） |
| 7 | 领取 lYT 收益（strUSD） | YT 锁定（需连钱包） | PendleYTLock · `claimYield` | [0xb71f8293c6086e595a516dcf882ed4436a402bc5fbecd189bc19e6bfc524c73b](https://etherscan.io/tx/0xb71f8293c6086e595a516dcf882ed4436a402bc5fbecd189bc19e6bfc524c73b)（公开样本） |
|  | Pendle YT 收益归集（SY-strUSD 收进锁仓合约，非领给调用者） | ⚠️ 前端入口待确认（任何地址可调用，近期 11 个不同调用方） | PendleYTLock · `claimPendleYield` | [0xa7d4fd088a65c046f19027820e87ff49a15cea814416899fa962056720d04c55](https://etherscan.io/tx/0xa7d4fd088a65c046f19027820e87ff49a15cea814416899fa962056720d04c55) |
| 8 | 跨链 trUSD / strUSD（Ethereum→Monad） | 跨链 | Chainlink CCIP Router `0x80226fc0Ee2b096224EeAc085Bb9a8cba1146f7D` · `ccipSend`（trUSD 锁入 LockRelease 池 `0x558d77ff…`） | [0xd94697354f6d7269cf2fdb87da767c54af3303f7fffca932e8cfa68804ba1093](https://etherscan.io/tx/0xd94697354f6d7269cf2fdb87da767c54af3303f7fffca932e8cfa68804ba1093)（公开样本） |
| 9 | 生态金库存入（trUSD） | 赚取 → 生态金库 → DEPOSIT | Upshift TokenizedVault `0xcd69123b3FBBfC666E1f6a501da27B564C00De54` · `deposit`（份额 ETRUSD `0x6f20aE2C…`） | [0x744263d59e642a9b47221fa6464b19ee5111a9c249015eed17d7e7620ee4b421](https://etherscan.io/tx/0x744263d59e642a9b47221fa6464b19ee5111a9c249015eed17d7e7620ee4b421)（公开样本） |
| 10 | 生态金库申请提取（7 天） | 生态金库 → WITHDRAW | 同上 · `requestRedeem` | [0x3da4af13db5194edbdca5a3bf5e7f10b3bc2ad8219f617648e476575730911cd](https://etherscan.io/tx/0x3da4af13db5194edbdca5a3bf5e7f10b3bc2ad8219f617648e476575730911cd)（公开样本） |
| 11 | 生态金库到期领取 | 生态金库 → WITHDRAW（需连钱包） | 同上 · `claim` | [0x103fd6db2170ff1679b71bf043f4505122447bbd82f92ede317679e49ec2397a](https://etherscan.io/tx/0x103fd6db2170ff1679b71bf043f4505122447bbd82f92ede317679e49ec2397a)（公开样本） |
| 12 | Monad 生态金库从 Ethereum 存入（USDC/USDT→AUSD 并跨链） | 赚取 → Ecosystem Vault（Monad）→ 存入 → ETHEREUM | ⬜ 页面写“一笔交易完成兑换与跨链”，Ethereum 侧调用的合约要连钱包才显示，未找到能确认属于 Tori 的样本 | ⬜ |

页面有入口但**不产生 Tori 交易**：活动（Cores 积分、推荐，链下）、DeFi（Morpho / Pendle / Curve / Fluid 等第三方外链）、透明度（数据页）、YT 锁定里的“买入”（跳 Pendle）。

### 截图

**前端页面**

| 兑换 | 质押 |
|:-:|:-:|
| <img src="截图/Tori-ETH-兑换页-20261013.png" width="460"> | <img src="截图/Tori-ETH-质押页-20261013.png" width="460"> |
| **解除质押** | **WITHDRAW（冷却提取）** |
| <img src="截图/Tori-ETH-解除质押页-20261013.png" width="460"> | <img src="截图/Tori-ETH-冷却提取页-20261013.png" width="460"> |
| **YT 锁定** | **跨链** |
| <img src="截图/Tori-ETH-YT锁定页-20261013.png" width="460"> | <img src="截图/Tori-ETH-跨链页-20261013.png" width="460"> |
| **生态金库（Upshift）** | **Ecosystem Vault（Monad）** |
| <img src="截图/Tori-ETH-生态金库页-20261013.png" width="460"> | <img src="截图/Tori-ETH-Monad金库页-20261013.png" width="460"> |
| **DeFi（外链，无 Tori 交易）** | **活动（Cores 积分，链下）** |
| <img src="截图/Tori-ETH-DeFi页-20261013.png" width="460"> | <img src="截图/Tori-ETH-活动页-20261013.png" width="460"> |

**交易详情（每类一张，均为 Blockscout）**

| 1 兑换（`0xece57d…`） | 2 质押（`0x58d57d…`） |
|:-:|:-:|
| <img src="截图/Tori-ETH-兑换交易-20261013.png" width="460"> | <img src="截图/Tori-ETH-质押交易-20261013.png" width="460"> |
| **3 发起冷却（`0xbdbf71…`）** | **4 冷却后提取（`0x31b5fe…`）** |
| <img src="截图/Tori-ETH-发起冷却交易-20261013.png" width="460"> | <img src="截图/Tori-ETH-冷却后提取交易-20261013.png" width="460"> |
| **5 取消冷却（`0x873e23…`）** | **6 YT 锁定（`0xd49d57…`）** |
| <img src="截图/Tori-ETH-取消冷却交易-20261013.png" width="460"> | <img src="截图/Tori-ETH-YT锁定交易-20261013.png" width="460"> |
| **7 领取 lYT 收益（`0xb71f82…`）** | **8 跨链（`0xd94697…`）** |
| <img src="截图/Tori-ETH-领取YT收益交易-20261013.png" width="460"> | <img src="截图/Tori-ETH-跨链交易-20261013.png" width="460"> |
| **9 金库存入（`0x744263…`）** | **10 金库申请提取（`0x3da4af…`）** |
| <img src="截图/Tori-ETH-金库存入交易-20261013.png" width="460"> | <img src="截图/Tori-ETH-金库申请提取交易-20261013.png" width="460"> |
| **11 金库到期领取（`0x103fd6…`）** | **7b Pendle YT 收益归集（`0xa7d4fd…`）** |
| <img src="截图/Tori-ETH-金库领取交易-20261013.png" width="460"> | <img src="截图/Tori-ETH-Pendle收益归集交易-20261013.png" width="460"> |

⚠️ 开发注意：
- **兑换不是 Tori 自有铸币合约**，是 Curve trUSD/USDC 池 `0xb723a224…` 的 `exchange`（看 `TokenExchange` 事件 + 池地址）；聚合器（1inch / LiFi / CoW 等）也会路过这个池子，trUSD 合约另有机构直铸的 `mint`/`redeem`，前端不走。
- **strUSD 解押分两段**：`cooldownShares`/`cooldownAssets` 时 strUSD 立即销毁，trUSD 转入 TrUsdSilo `0xF7c0d8853E69DCD37ee7599c6280d2632F3360B4`；7 天后 `unstake` 才从 Silo 转给用户；`cancelCooldown` 则把 Silo 里的 trUSD 重新铸回 strUSD。

## 0. 一句话说明

Tori 把机构级算法交易策略（delta 中性）包装成稳定币产品：用户拿稳定币铸 **trUSD**（1:1 稳定币），再把 trUSD **质押成 strUSD** 赚 delta 中性策略收益。跟 Ethena(USDe/sUSDe)、Unitas(USDu/sUSDu) 是同一类"合成美元 + 收益版"结构。当前 APY **11.2%**。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Tori Finance |
| 链 | **Ethereum** |
| **操作入口** | https://app.tori.finance |
| 官网 | https://tori.finance ｜ 当前 APY **11.2%** |
| **兑换合约（USDC→trUSD）** | `0xb723a224c9acf3891b20437b4d55dd45600f5fa3`（sel `0x3df02124`） |
| trUSD（基础稳定币） | `0xd0580192E98eA6CEB9c7b6191Ed2E27560911697`（symbol TRUSD） |
| strUSD（质押收益版） | `0x280839980a7eD0D7717F64125fE241012E5F5815`（symbol STRUSD） |
| Ecosystem Vault | `0x6f20aE2C98c2D34e6A57f3411f2C5Af92E32592d`（symbol ETRUSD） |
| 解析类型 | trUSD = 稳定币(1:1)；strUSD = **A 类 NAV 累积**（质押收益体现在汇率） |
| 接入情况 | 待定 |

## 2. 操作清单（页面可点击）

| # | 操作 | 说明 |
|---|------|------|
| 1 | **Mint trUSD** | 用稳定币（USDC 等）铸 trUSD |
| 2 | **Redeem trUSD** | trUSD 换回稳定币 |
| 3 | **Stake（trUSD→strUSD）** | 质押赚 delta 中性策略收益 |
| 4 | **Unstake（strUSD→trUSD）** | 解押 |
| 5 | **Ecosystem Vault 存入** | USDC 存入生态金库（页面另有此入口） |

页面说明原文：*"Stake trUSD for strUSD, and automatically earn rewards from our delta-neutral trading strategies."* / *"Earn Cores just by holding trUSD or using it on DeFi protocols."*（trUSD 持有本身也攒积分 Cores）

## 2.1 ✅ 本人实测（2026-09-01）

页面截图：兑换 `Tori-兑换trUSD-20260901.png` ｜ 质押 `Tori-质押strUSD-20260901.png` ｜ 提取冷却 `Tori-解押冷却7天-20260901.png`。当前 TVL $66.3M，APY 10.67%。

🔴 **关键：兑换/质押的合约不是 trUSD/strUSD 代币本身，而是各自的操作合约**：
- 兑换合约（USDC→trUSD）：`0xb723a224c9acf3891b20437b4d55dd45600f5fa3`，selector `0x3df02124`
- 质押/解押合约就是 strUSD 代币 `0x280839…`（stake `0x6e553f65` / unstake `0x9343d9e1`）

| # | 时间(UTC) | 操作 | 合约 | tx hash | 结果 |
|:---:|------|------|------|---------|------|
| 1 | 15:27:59 | **兑换 trUSD**（USDC→trUSD） | `0xb723a224…` | `0xece57d0b3b9a430f172e77382e0c1d4df30e2cd84f9fcc497d18d579f8efc7ca` | 1 USDC → 0.9999618 trUSD |
| 2 | 15:31:23 | **兑换 trUSD**（第 2 笔，5 USDC） | `0xb723a224…` | `0x64e7059603f249b307b40f41af00f3eae7a7842f209ad4e2c02b7152225ffed0` | 5 USDC → 4.9998091 trUSD |
| 3 | 15:29:35 | **approve**（授权 trUSD 给质押合约） | trUSD `0xd05801…` | `0x9a7ede727487d109cec5ee355e166e61d0baf32f02a7a4d909404bf563207d5f` | 前置授权 |
| 4 | 15:32:35 | **质押（trUSD→strUSD）** | strUSD `0x280839…` sel `0x6e553f65` | `0x58d57d11a4b199bdd5808d5995aa9c935fdbf5837803847001cc50cce79fea0b` | 5 trUSD → **4.908140766 strUSD**（汇率 1 strUSD≈1.0187 trUSD） |
| 5 | 15:34:23 | **解除质押（strUSD→trUSD）** | strUSD `0x280839…` sel `0x9343d9e1` | `0xbdbf713ed1e9b9264aa2746a813a585e971b02789261b71b1923bd4bda5c9434` | 销毁 3 strUSD → 3.0561481 trUSD |

🔴🔴 **strUSD 解押是两段式，有 7 天冷却**（截图 `Tori-解押冷却7天-20260901.png`）：
- 第 5 笔"解除质押"只是**发起冷却**，trUSD 不会立刻到账
- 页面 WITHDRAW 页显示：**冷却期 7 天**，3.06 trUSD 处于"冷却中"，就绪时间 9/8，剩 6d23h
- 冷却结束后要再点"提取已锁定"才真正拿到 trUSD；期间可"取消冷却"
→ **对解析同学**：解押要拆成 **发起冷却 → (等7天) → 提取** 两笔，中间用户 strUSD 已烧但 trUSD 未到，必须有"冷却中"状态。这和 Renzo/Native 的排队赎回同类。

## 2.2 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| **兑换 trUSD（USDC→trUSD）** | ✅ `0xece57d…` `0x64e705…` | — |
| **质押（trUSD→strUSD）** | ✅ `0x58d57d…` | ✅ `0x70e945…` |
| **解押-发起冷却（strUSD→trUSD）** | ✅ `0xbdbf71…` | — |
| approve | ✅ `0x9a7ede…` | — |
| **解押-冷却后提取** | ⬜ 等 9/8 冷却结束再做 | — |
| Ecosystem Vault 存入（RockawayX 管理） | ⬜ | ⬜ 待补 |
| YT 锁定 | ⬜ | ⬜ 页面有此 tab，未测 |

**页面实际 tab**：兑换 / 质押 / YT 锁定；质押页下又有 质押 / 解除质押 / **WITHDRAW** 三个子 tab。

⚠️ 还差：**冷却期满后的"提取已锁定"**（要等到 9/8）、Ecosystem Vault 存入、YT 锁定。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | ✅ **已确认**：兑换 trUSD 走合约 `0xb723a224…`（不是 mint 而是 USDC↔trUSD 兑换） | 2026-09-01 实测 |
| 2 | ✅ **已确认**：strUSD 解押有 **7 天冷却**，两段式（发起→提取） | 2026-09-01 实测 |
| 3 | Ecosystem Vault 与 strUSD 的区别 | 页面/文档 |
