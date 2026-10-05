# Cyber — DEX 协议调研

> **链**：Cyber（OP Stack L2） ｜ chainId **7560** ｜ 浏览器 https://cyberscan.co ｜ RPC `https://rpc.cyber.co/`（`eth_getLogs` 单次 ≤1000 区块）
> **调研日期**：2026-09-29 ｜ hash 均为链上公开交易（非本人钱包），已逐条确认成功

## 协议总览

| # | 协议 | 类型 | 入选理由 | 前端 | hash |
|---|---|---|---|:-:|:-:|
| 1 | CyberSwap | iZiSwap 式（点位 CLMM + 限价单） | 链上唯一有量 DEX，交易量 100% | ✅ | ✅ |

单协议覆盖 100% 交易量；但链上 DEX 极度萎缩（TVL 约 $3K，主池日均 1～7 笔 swap）。

---

## 1. CyberSwap（iZiSwap fork）

| 项 | 值 |
|---|---|
| 前端 | https://cyberswap.cc/trade/swap ｜ 流动性 https://cyberswap.cc/trade/liquidity |
| Factory | `0x8c7d3063579BdB0b90997e18A770eaE32E1eBb08` |
| Swap 路由 | `0x3EF68D3f7664b2805D4E88381b64868a56f88bC4`（multicall） |
| LiquidityManager | `0x19b683A2F45012318d9B2aE1280d68d3eC54D663`（加/减流动性均经其 multicall） |
| 示例池 | `0xa3c6ef565ab5cf989b1fb1ada2e89473ec06299f`（CYBER/WETH；tokenX = CYBER `0x14778860E937f509e651192a90589dE711Fb88a9`，tokenY = WETH `0x4200000000000000000000000000000000000006`） |
| LP 凭证 | NFT（LiquidityManager） |

| 行为 | tx hash | 截图 |
|---|---|---|
| Swap 买（WETH→CYBER） | [0xcbab557ebd995728c6fb9e0f34f054a1b8045519f608b1abe20561553f192c9f](https://cyberscan.co/tx/0xcbab557ebd995728c6fb9e0f34f054a1b8045519f608b1abe20561553f192c9f) | |
| Swap 买（WETH→CYBER） | [0x3e58f50c37854a1affac3f4470b3f803f2baf9600d0966def33e72376542fd77](https://cyberscan.co/tx/0x3e58f50c37854a1affac3f4470b3f803f2baf9600d0966def33e72376542fd77) | <img src="截图/CyberSwap-Cyber-swap交易-20260929.png" width="320" alt="交易页"> |
| Swap 卖（CYBER→WETH） | [0xacc8b6da7243d0a6f38fa5778210b67ede1bbd5a4b5b979de2d927cbecef468d](https://cyberscan.co/tx/0xacc8b6da7243d0a6f38fa5778210b67ede1bbd5a4b5b979de2d927cbecef468d) | |
| Swap 卖（CYBER→WETH） | [0x49040374da6f65cd756c431ad9ac78580d751729561fe53475b1a88d690b4978](https://cyberscan.co/tx/0x49040374da6f65cd756c431ad9ac78580d751729561fe53475b1a88d690b4978) | |
| Swap 卖（多跳，Confluence 原样本，同笔还经池子 `0x0dA461…`） | [0x0eb55bf68be9cf25337162154df3a9e4344fe2fb0cfa339e52622402d558cacf](https://cyberscan.co/tx/0x0eb55bf68be9cf25337162154df3a9e4344fe2fb0cfa339e52622402d558cacf) | |
| 加流动性（Mint） | [0xd780db089dd106a2c081010092a0634b8ec49b3d3417ce0c144084f5444e5d27](https://cyberscan.co/tx/0xd780db089dd106a2c081010092a0634b8ec49b3d3417ce0c144084f5444e5d27) | <img src="截图/CyberSwap-Cyber-加流动性交易-20260929.png" width="320" alt="交易页"> |
| 减流动性（Burn） | [0xb1622996d2c1fa15362b6f025b5e9be51ac6c62db88088672a2a5069a138822d](https://cyberscan.co/tx/0xb1622996d2c1fa15362b6f025b5e9be51ac6c62db88088672a2a5069a138822d) | <img src="截图/CyberSwap-Cyber-减流动性交易-20260929.png" width="320" alt="交易页"> |
| 限价单挂单（AddLimitOrder，参考） | [0xf5bcb86f91a52b4cf7662af4e7b4896acddfe7a2144ff882804edc481ca99f24](https://cyberscan.co/tx/0xf5bcb86f91a52b4cf7662af4e7b4896acddfe7a2144ff882804edc481ca99f24) | |

前端截图：<img src="截图/CyberSwap-Cyber-swap页-20260929.png" width="320" alt="swap 页"> ｜ <img src="截图/CyberSwap-Cyber-流动性页-20260929.png" width="320" alt="流动性页">

⚠️ 开发注意：
- **不是 Uniswap V3 fork，是 iZiSwap 架构**：事件用 `tokenX/tokenY`、`leftPoint/rightPoint`，Swap topic0 为 `0x0fe977d6…`（非 V3 的 `0xc42079f9…`），方向看 `sellXEarnY`（false=买 CYBER，true=卖）；另有 AddLimitOrder / DecLimitOrder / CollectLimitOrder 限价单事件。
- 池子里不少 `Burn` 的 liquidity = 0（只结算手续费），不能算减流动性。

---

## 待确认

- 链上量极小（TVL 约 $3K），业务上是否仍值得接入
- 限价单是否纳入解析范围
