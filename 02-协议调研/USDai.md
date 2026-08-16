# USDai — Arbitrum 合成美元（AI 信贷收益）

> **状态**：🟡 Swap 已实测（2026-08-14）｜ **调研时间**：2026-08-14
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> 页面实际 Tab：**Swap / Redeem / Stake / Bridge**（Buy 走 Swap；Swap 走 LI.FI 路由，可跨链）

## 0. 一句话结论

USD.AI = **合成美元协议**：USDai 1:1 由稳定币储备（PYUSD 为核心）背书，储备部署给 AI 公司做 GPU 抵押信贷赚收益；收益版 **sUSDai**（stake USDai 得，NAV 累积，官网显示 **8.90% gross / 7.67% net**）。TVL $430.3M（2026-08-14 官网）。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | USD.AI（USDai Foundation） |
| 链 | **Arbitrum**（Ethereum 等多链部署） |
| 产品 | USDai（合成美元）/ sUSDai（stake 版） |
| 网页入口 | https://app.usd.ai （Buy / Earn / Loans / Positions） |
| Supply Coins | USDC / PYUSD（⚠️ Buy 页实测确认） |
| Coins Integrated | USDai `0x0A1a1A107E45b7Ced86833863f482BC5f4ed82EF`（Arbiscan 已确认）；sUSDai 地址 ⚠️ 待确认 |
| TVL | $430.3M（官网 2026-08-14） |
| 收益率 | 8.90% gross / 7.67% net |
| 池子类别 | 活期（stake） |
| 参考 | [usd.ai/usdai](https://usd.ai/usdai) ｜ [Arbiscan token](https://arbiscan.io/token/0x0A1a1A107E45b7Ced86833863f482BC5f4ed82EF) ｜ [DeepWiki 合约](https://deepwiki.com/usdai-foundation/usdai-contracts/1.3-deployed-contracts) |

## 2. 本周操作清单（用户去做交易 + 截图）

| # | 操作 | 入口路径 | 截图要点 |
|---|------|---------|---------|
| 1 | **Buy / Mint USDai**（小额 USDC） | app.usd.ai → Buy | 汇率、数量 |
| 2 | **Earn / Stake → sUSDai** | Earn | APY、份额 |
| 3 | **Redeem / Unstake** | Positions → redeem | 收回数量、是否排队 |

### 2.1 ✅ 实测交易（2026-08-14；只记哈希+截图，解析留给解析同学）

| # | 操作（UI 视角） | tx hash | 截图 |
|:---:|---------------|---------|------|
| 1 | 08-14 · **Swap** 1 USDC（Ethereum）→ 1.0003 USDai（Arbitrum，跨链；路由 OKX Dex Aggregator / LI.FI；步骤：Swap → Approve PYUSD → Buy USDai） | `0xdd19f67e5872e6a63cdc978b7b825be5458daa5aa4c09ab9c480133d779cd1cf` | [截图](截图/USDai-swap-20260814.png) |
| 2 | 08-16 10:22:59 · **Swap**（Ethereum，USDC 3.0 → PYUSD 3.000111，走 LI.FI `Permit2Proxy`，方法 `callDiamondWithEIP2612Signature`） | `0xb0ffa4ce7642e963919ea8441e29d1671bf4f7b6c72a2a20724b95d6d433cb5d` | [截图](截图/USDai-swap-3USDC-20260816.png) |
| 3 | 08-16 10:24:23 · **Bridge 发送 / Buy USDai**（Ethereum，方法 `send` → **OFTWrapper**（LayerZero OFT），销毁 PYUSD 4.0） | `0x4b4f139f59dcdb26b50495ae58e9e5443847cb410420a8499cfd073e0260ff87` | 同上 |
| 4 | 08-16 10:27:43 · **Bridge 到账**（Arbitrum，收到 **USDAI 4.0**，from `0x24a92E28…`） | `0x894d2f828619d8fd8236ab5bc40c81207273a8ed3df0eb1fcb6a478478bd3900` | — |

⚠️ **UI 上一次 "Swap" 在链上是三笔**：LI.FI swap（USDC→PYUSD，Ethereum）→ OFT send（PYUSD 销毁，Ethereum）→ USDai 到账（Arbitrum）。**Buy USDai 与 Bridge 是同一条 OFT 链路，不是两个独立入口**。

### 2.2 操作覆盖（页面可点击交易）

| 交易类型 | 已测 | 哈希 |
|---------|:---:|------|
| Swap（USDC→PYUSD） | ✅ | `0xdd19f6…`（08-14）、`0xb0ffa4…`（08-16） |
| Bridge（OFT 跨链，Buy USDai 同链路） | ✅ | 发送 `0x4b4f13…` + 到账 `0x894d2f…` |
| **Stake（USDai → sUSDai，APY 8.9%）** | ⬜ | **链上未找到**（2026-08-16 全量核查钱包 `0x9da44afe…` 的 Ethereum / Arbitrum 交易与代币流水，无 sUSDai 记录）—— 未做，待补 |
| **Redeem** | ⬜ | **链上未找到**（同上）—— 未做，待补 |

> 🔎 **核查说明**（2026-08-16）：Ethereum 上的 `depositWithPermit`(StakingRouter) 与 `initiateRedeem` 两笔属于 **Theo**（sthUSD/thUSD），不是 USDai。Solana 钱包 `BpexTtBq…` 无 USDai 记录（USDai 为 EVM 资产）。

每笔操作后把 **tx hash** 和截图一起给我。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | sUSDai 合约地址 + decimals + 汇率函数 | Arbiscan + 实测 |
| 2 | redeem 是否即时（还是排队） | 实测 |
| 3 | Buy 接受的 supply coins | UI |
