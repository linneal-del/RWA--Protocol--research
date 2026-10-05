# Meteora DLMM — Solana 集中流动性 AMM

> **状态**：✅ **页面可点击交易类型已全覆盖**（加流动性 / 领手续费 / 撤流动性 / 关仓位）｜ **调研时间**：2026-08-14 ~ 08-22
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图；**链上解析由解析同学做，本页不做深度解析**

## 0. 一句话结论

Meteora 是 Solana 上的流动性协议，**DLMM = Dynamic Liquidity Market Maker**（集中流动性 + 动态费率）。用户操作 = **Add / Remove Liquidity**，收益来自交易手续费分成。**注意 Meteora 有多种池型（DLMM / DAMM v2 / ALPHA），本周只做 DLMM**。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Meteora |
| 链 | **Solana** |
| 产品 | DLMM 池（选 1-2 个主流池，如 SOL-USDC） |
| 网页入口 | https://app.meteora.ag |
| Supply Coins | 池子两边代币（如 SOL + USDC） |
| Coins Integrated | LP 份额（NFT 仓位，DLMM 仓位是 NFT 不是代币 ⚠️ 实测确认） |
| TVL / APY | 待 UI 截图 |
| 池子类别 | LP（手续费收益） |
| 主合约 | ✅ **DLMM program = `LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo`**（2026-08-22 从实测交易内确认） |

## 2. 本周操作清单（用户去做交易 + 截图）

| # | 操作 | 入口路径 | 截图要点 |
|---|------|---------|---------|
| 1 | **Add Liquidity**（小额） | app.meteora.ag → 选 DLMM 池 → Add | 池名、价格区间、两边数量、预估 APY |
| 2 | **Remove Liquidity**（全撤） | 我的仓位 → Remove | 收回两边数量 + 手续费 |
| 3 | （可选）claim fees | 仓位页 | 手续费金额 |

### 2.1 ✅ 实测交易（2026-08-14，UTC；只记哈希+截图，解析留给解析同学）

| # | 时间 | 操作（链上指令为准） | 交易签名 | 截图 |
|:---:|------|---------------|---------|------|
| 1 | 05:33:33 | **开仓 + 加流动性** —— 指令 `InitializePosition` + `AddLiquidityByStrategy2` | `27r1L31orn5tFgiy8kH2mMLLEAzgEnwV35cjLMnYbrtsCuuFRqX7XVgSUaS5woFwQ7pkf6nErqPXr9UrrPBMDQwr` | [截图](截图/Meteora-DLMM-add-20260814.png) |
| 2 | 05:34:39 | 🔴 **领取手续费（ClaimFee2）** —— **不是撤流动性**（2026-08-22 链上复核纠正） | `5dFFUCEzCGQvJZo1AtUQbh5ujdJZpgz2hxLycfKrj3ta8AQNXm4LV7LRMfLeRBHrz2fsMuiMdVd5r6uCKQMgVepR` | ⬜ 待补 |

| 3 | 08-22 14:05:41 | **领取手续费（第 2 次）** —— `ClaimFee2`，回收 wSOL 0.001231625 + USDC 0.096746 | `4mESyGvUuU3fAbsTr3sBTu7JA8AWCnYNvkBrqyyXb48YuM7JeRnn7YWQQbKFyXk3E2xkt33HKYQ7fEPPgTdRotaq` | — |
| 4 | 08-22 14:06:03 | ✅ **撤流动性 + 领费 + 关仓位（一笔三指令）** —— `RemoveLiquidityByRange2` + `ClaimFee2` + `ClosePositionIfEmpty`，回收 USDC 15.418981 | `3h5pnkjBFWUVZoLyxdWe6r635JQkzQVk8yGqXFSvjoB5RHx9krsxecPbea2ZVJDmowQuciff5rqxwhmwGGhQCeiX` | — |

🔴 **2026-08-22 纠正**：第 2 笔此前被记为 "Remove Liquidity"，链上指令实为 **`ClaimFee2`**。该笔只回收了极小额（wSOL 0.000001073 + USDC 0.000042），与"领手续费"吻合，**本金仍在池中**。真正的撤流动性是第 4 笔。

🔴 **UI 一次"Remove"= 链上三个指令**：`RemoveLiquidityByRange2`（撤本金）→ `ClaimFee2`（顺带领费）→ `ClosePositionIfEmpty`（仓位清空后自动关闭）。**解析侧不能只认第一个指令**，否则会漏掉手续费收入和仓位关闭状态。

### 2.2 操作覆盖（页面可点击交易）

| 交易类型 | 链上指令 | 已测 | 哈希 |
|---------|---------|:---:|------|
| 开仓 InitializePosition | `InitializePosition` | ✅ | `27r1L3…`（与加流动性同笔） |
| Add Liquidity | `AddLiquidityByStrategy2` | ✅ | `27r1L3…` |
| **claim fees** | `ClaimFee2` | ✅ | `5dFFUC…`、`4mESyG…`（独立成笔） |
| **Remove Liquidity** | `RemoveLiquidityByRange2` | ✅ | `3h5pnk…` |
| **Close Position** | `ClosePositionIfEmpty` | ✅ | `3h5pnk…`（与撤流动性同笔） |

**✅ 页面可点击的交易类型已全覆盖。**

每笔操作后把 **Solana 签名** 和截图一起给我。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | DLMM 仓位由 `InitializePosition` 创建为独立 position 账户（非 ERC-20 式份额），解析需按 position 账户读 | 已见指令，细节待解析同学确认 |
| 2 | ✅ **已确认：需主动 claim**，独立指令 `ClaimFee2` | 2026-08-22 链上确认 |
| 3 | ✅ **已确认** DLMM program = `LBUZKhRxPF3XUpBCjp4YzTKgLccjZhTSDM9YuVaPwxo` | 2026-08-22 |
