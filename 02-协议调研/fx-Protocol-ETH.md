# f(x) Protocol — fxUSD（杠杆 + 稳定币）· Ethereum

> **状态**：✅ 四条产品线本人实测已覆盖（fxMINT/Earn/fxSAVE + 铸造，2026-09-01）；杠杆开仓、赎回待补
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Ethereum**

## 0. 一句话说明

f(x) Protocol（Aladdin DAO 旗下）是链上交易平台：一边给 ETH/WBTC 提供**无爆仓压力的杠杆**，一边给稳定币持有人**可持续收益**。核心稳定币是 **fxUSD**，另有稳定池 fxSP。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | f(x) Protocol（Aladdin DAO / aladdin.club） |
| 链 | **Ethereum** |
| **操作入口** | https://fx.aladdin.club |
| **fxUSD** | `0x085780639CC2cACd35E474e71f4d000e2405d8f6`（symbol fxUSD / name f(x) USD） |
| fxSP（稳定池） | `0x65C9A641afCEB9C0E6034e558A319488FA0FA3be`（symbol fxSP / f(x) Stability Pool） |
| 底层敞口 | ETH / WBTC |
| 接入情况 | 待定 |

## 2. 操作清单（页面可点击）

| # | 操作 | 说明 |
|---|------|------|
| 1 | **Mint fxUSD** | 存抵押铸稳定币 |
| 2 | **Redeem fxUSD** | 赎回 |
| 3 | **开杠杆头寸** | ETH/WBTC 杠杆（无爆仓压力） |
| 4 | **稳定池 fxSP 存/取** | 稳定币赚收益 |

## 2.1 ✅ 本人实测（2026-09-01）—— 四条产品线全覆盖

f(x) 页面导航：**Trade（杠杆）/ fxSAVE / fxMINT / Earn / fxUSD Bridge / Lock / …**。本次实测覆盖 Earn、fxSAVE、Trade 杠杆、fxMINT 四条。

| # | 时间(UTC) | 产品线 | 操作 | tx hash | 结果 |
|:---:|------|------|------|---------|------|
| 1 | 15:37:47 | Earn | **approve USDC** | `0x0849c865d7dd554ed287f3f182ea528e6b1688481c9a4c523a4f0acfeb9a7bb6` | 前置 |
| 2 | 15:38:11 | Earn | **质押到 fxUSD Stability Pool Gauge**（Approve&Deposit&Stake，sel `0xcd20b95e`） | `0xd0b2db450f4903f06b09a3adbcb4985cbdfe92773443d60f9e437c4c3ba19416` | 10 USDC → 9.8982 fxSP + 铸 Gauge 份额，选 wstETH 收益 |
| 3 | 15:38:47 | Earn | **解押+提取**（Unstake&Withdraw，sel `0x00f714ce`） | `0x53f3a8c14871d2b87ec6ba9c80ee9b3237aca8197a836a7f166e4b15223994ee` | 烧 5 fxSP-Gauge → 退 5 fxSP |
| 4 | 15:39:11 | fxSAVE | **approve** | `0xd00239241edd6bf0fd8de7e9e0ddd089fa5d8c84930c79dbea3c56d1455819ca` | 前置 |
| 5 | 15:41:59 | fxSAVE | **存入 fxSAVE**（sel `0x3ea34dc0`，`depositToFxSave`） | `0x1f3ccd1d471165c2e2de204cab4cb7627a558b57e222a7eb8eddc27f61e4d353` | 5 USDC → 4.4825 fxSAVE（APY 7.49%，自动复投的 delta 中性稳定币金库） |
| 6 | 15:42:59 | fxMINT | **铸 fxUSD（USDC 直铸）**（sel `0xef9e1aa7`，48 条 log） | `0x9a2c7ec339f1a624909f6186b0fa81c4a0513a2dbbc2891900b6fb98daaf7d72` | 铸 9.9985 fxUSD |
| 7 | 15:43:59 | Trade | **approve xWBTC** | `0xce0438d6d3d1b1fbd595ef3ae3a91e740b61fb774421ff05d46fd0a0dcc62f30` | 前置 |
| 8 | 15:44:11 | fxMINT | **存 WBTC 铸 fxUSD**（sel `0x216d5108`，Deposit&Mint） | `0xc918919b5642b2fda9bb627a918374a67323ceddf771ed97c493d73a1ecfd8b4` | WBTC 抵押 → 铸 1.99 fxUSD，LTV 67%→80% |

🔴 **几个链上看不出的机制**：
1. **Earn 质押是"三合一"**：approve + deposit + stake to gauge 一笔完成（sel `0xcd20b95e`），且存款时**要选收益币种**（wstETH / FXN / USD 三选一，APR 各不同）。
2. **Earn 解押/提取有 60 分钟延迟**（截图 `fx-Earn解押提取-20260901.png`）：默认排队 60 分钟，或勾选 **"付 1% 费用立即到账"**。解析要区分这两种。
3. **杠杆开仓**（Trade 页，截图 `fx-杠杆开多BTC-20260901.png`）：Long/Short BTC，杠杆 1.2x~7x，有 Liquidation Brake、Funding Rate —— 但**本次杠杆开仓没提交**（截图停在 Preview），无哈希；第 8 笔是 fxMINT 存 WBTC 铸 fxUSD，不是杠杆。
4. **fxMINT 有两种铸法**：USDC 直铸（第 6 笔）/ 存 WBTC 抵押铸（第 8 笔），后者会显示 LTV 和 Liquidation Brake。

## 2.2 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| **fxMINT 铸 fxUSD（USDC 直铸）** | ✅ `0x9a2c7e…` | ✅ `0x49e16d…` |
| **fxMINT 存 WBTC 铸 fxUSD** | ✅ `0xc91891…` | — |
| Redeem fxUSD | ⬜ | ✅ `0x9fb6f4…` |
| **Earn 质押（Stability Pool Gauge）** | ✅ `0xd0b2db…` | — |
| **Earn 解押+提取** | ✅ `0x53f3a8…` | — |
| **fxSAVE 存入** | ✅ `0x1f3ccd…` | — |
| fxSAVE 取出 | ⬜ | ⬜ 待补 |
| **杠杆开多（Trade，xPOSITION）** | ✅ `0x24acba…` | — |
| approve ×4 | ✅ | — |

### 追加实测（2026-09-01 16:03）
| # | 操作 | tx hash | 结果 |
|:---:|------|---------|------|
| 9 | **approve USDC**（给杠杆合约 `0x33636d49…`） | `0x400031419b0be5c6dda93c9077026eab9f6755b996ec30aad580f5bac4086314` | 前置 |
| 10 | **杠杆开多 BTC**（Trade 页 Long，5 USDC 保证金 @ 5.12x，sel `0xef9e1aa7`） | `0x24acba5528caa8d0507bf7a82f132e22c7c3cdd89cc646bd3a5c4724804ad91e` | 铸 20.619 fxUSD 建 xPOSITION 多头 |

🔴 **杠杆开多和 fxMINT 铸币是同一个 selector `0xef9e1aa7`**（都是 mint fxUSD）—— 区别在**目标合约**：fxMINT 走 `0x33…cc708`（fxSAVE/mint 合约），杠杆走另一个 position 合约。截图 `fx-杠杆开多BTC-5x-20260901.png` 显示持仓面板有 **Repay / Add Collateral / TP/SL / Adjust Leverage** 四个管理按钮，说明杠杆头寸是可管理的独立仓位。

**页面实际导航**：Trade / fxSAVE / fxMINT / Earn / fxUSD Bridge / Lock / Stats / Leaderboard。

⚠️ 还差：**杠杆平仓 / Repay / Add Collateral**、fxSAVE 取出、fxUSD 赎回。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | 杠杆头寸是 NFT 还是账户记录 | 实测 |
| 2 | fxUSD 铸造接受哪些抵押（ETH/WBTC/稳定币） | 页面 |
| 3 | fxSP 稳定池凭证机制 | 实测 |
