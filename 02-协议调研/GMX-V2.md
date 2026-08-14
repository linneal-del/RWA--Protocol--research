# GMX V2 — Arbitrum 永续合约 DEX

> **状态**：⬜ 本周待实测 ｜ **调研时间**：2026-08-14 ｜ **解析类型**：待定（仓位型 + LP 型，预判 D 类仓位）
> **调研质量**：低（未实测）

## 0. 一句话结论

GMX V2 是 Arbitrum 上的永续合约 DEX。用户两类操作：**① 开/平永续仓位（long/short）② 给 GM 池加/撤流动性（LP）**。解析要哪种样本，操作清单都覆盖（各做一笔小额）。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | GMX V2 |
| 链 | **Arbitrum** |
| 产品 | 永续合约 + GM 流动性池 |
| 网页入口 | https://app.gmx.io （右上角切到 Arbitrum） |
| Supply Coins | 保证金（WETH/USDC）或 LP 存款（WETH/USDC） |
| Coins Integrated | 仓位（链上 position）/ GM token |
| TVL / APY | 待 UI 截图 |
| 池子类别 | 永续 + LP |
| 主合约 | V2 Exchange/Router ⚠️ 待链上确认（候选 `0x83518F8C44BB3F3d0f2133B06A9390975C92A8E2` 类，以交易内 to 地址为准） |

## 2. 本周操作清单（用户去做交易 + 截图）

| # | 操作 | 入口路径 | 截图要点 |
|---|------|---------|---------|
| 1 | **开多小额 ETH-PERP** | Trade → Long ETH | 保证金、杠杆、入场价 |
| 2 | **平仓** | Positions → Close | 实现 PNL |
| 3 | **加流动性**（GM 池，小额 USDC） | Earn/Pools → 选池 Add | 池名、数量 |
| 4 | **撤流动性** | 我的 LP → Remove | 收回数量 |

每笔操作后把 **tx hash** 和截图一起给我。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | V2 仓位是 NFT 还是账户记录 | 实测交易 |
| 2 | GM token 汇率（NAV 累积？） | Arbiscan + 实测 |
| 3 | Exchange/Router 合约地址 | 交易内确认 |
