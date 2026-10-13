# Tori Finance — trUSD / strUSD（机构策略稳定币）· Ethereum

> **状态**：✅ 兑换/质押/解押(发起冷却) 本人实测已覆盖（2026-09-01）；冷却后提取待 9/8 补
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Ethereum**

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
