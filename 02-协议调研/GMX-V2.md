# GMX V2 — Arbitrum 永续合约 DEX

> **状态**：✅ 页面可点击交易类型已全覆盖（2026-08-16）｜ **调研时间**：2026-08-14 ~ 08-16
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **测试钱包**：`0x9DA44Afe…c33c`（Arbitrum）

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

### 2.1 ✅ 实测交易（2026-08-16；只记哈希+截图，解析留给解析同学）

| # | 操作（UI 视角） | tx hash | 截图 |
|:---:|---------------|---------|------|
| 1 | **开多 ETH/USD**（2x Long，Margin 0.005 ETH ≈$9.39，Size $18.75，入场价 $1,877.69，Pool WETH-WETH，Collateral WETH） | `0x1303aa1bbcf137fa8caca39ead51e76ecfbae5bc47cd125ac4f2e00da32a84a2` | [截图](截图/GMX-V2-开多ETH-20260816.png) |
| 2 | **平多 ETH/USD**（Market close 全平 $18.75，Receive 0.0049910 ETH ≈$9.37，PnL +$0.01） | `0x70b39d4b00f1234d37524f38752321d19a54c676b2be17fba3a37e33c4fa15c7` | [截图](截图/GMX-V2-平多ETH-20260816.png) |
| 3 | **Buy GM**（加流动性，GM: BTC/USD [BTC-USDC] 池，Pay 4 USDC → Receive 1.816257524361915276 GM） | `0x05a7754c09768a0fad71e482a9677897bc23e27109f7200f7faee9e39cde4526` | [截图](截图/GMX-V2-BuyGM-BTCUSD-20260816.png) |
| 4 | **Sell GM**（撤流动性，Pay 1 GM → Receive 2.197567 USDC，Selling Completed） | `0x1129448821c39146806708e33d7392f3b3488c15c767f2bdbab9c8168b3ba744` | [截图](截图/GMX-V2-SellGM-BTCUSD-20260816.png) |

### 2.2 操作覆盖（页面可点击交易）

| 交易类型 | 已测 | 哈希 |
|---------|:---:|------|
| 开仓 Long（永续） | ✅ | `0x1303aa…` |
| 平仓 Close（永续） | ✅ | `0x70b39d…` |
| Buy GM（加流动性） | ✅ | `0x05a775…` |
| Sell GM（撤流动性） | ✅ | `0x112944…` |
| 开仓 Short | ⬜ | —（与 Long 同一入口同一按钮，机制对称，未单独测） |
| Shift GM（池间转移） | ⬜ | —（Pools 页第三个 tab，未测） |
| Swap（Trade 页第三个 tab） | ⬜ | —（未测） |

**页面实际 tab**：Trade 页 = Long / Short / Swap；Pools 页 = Buy GM / Sell GM / Shift GM。

每笔操作后把 **tx hash** 和截图一起给我。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | V2 仓位是 NFT 还是账户记录 | 实测交易 |
| 2 | GM token 汇率（NAV 累积？） | Arbiscan + 实测 |
| 3 | Exchange/Router 合约地址 | 交易内确认 |
