# USDai — Arbitrum 合成美元（AI 信贷收益）

> **状态**：⬜ 本周待实测 ｜ **调研时间**：2026-08-14 ｜ **解析类型**：待定（预判 A 类 NAV 累积，sUSDai）
> **调研质量**：中（官网 + Arbiscan 合约已确认，未连钱包实测）

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

每笔操作后把 **tx hash** 和截图一起给我。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | sUSDai 合约地址 + decimals + 汇率函数 | Arbiscan + 实测 |
| 2 | redeem 是否即时（还是排队） | 实测 |
| 3 | Buy 接受的 supply coins | UI |
