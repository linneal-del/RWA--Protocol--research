# Uniswap V2 — Robinhood Chain

> **状态**：🟡 三类核心交易已取到链上样本（2026-08-22）｜ **调研时间**：2026-08-22
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Robinhood Chain**（chainId **4663**）

## 0. 一句话结论

Robinhood Chain 上部署的是**标准 Uniswap V2**（LP 凭证为 `UNI-V2` ERC-20，Factory/Router/Pair 三件套，事件签名与以太坊主网完全一致）。用户操作三类：**Swap / 加流动性(Mint) / 减流动性(Burn)**。链很活跃——近 2000 个区块内就有 **240 笔 Swap**。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Uniswap V2（标准部署） |
| 链 | **Robinhood Chain**，chainId **4663** |
| 原生币 | **ETH**（18 位） |
| 公共 RPC | `https://rpc.mainnet.chain.robinhood.com` ｜ 备用 `https://rpc.arrowrpc.com` |
| 区块浏览器 | **https://robinhoodchain.blockscout.com** ｜ 另有 https://hoodscan.ai |
| 链 TVL | **$580,341,467**（DeFiLlama，2026-08-22） |
| **操作入口** | https://app.uniswap.org/ （切换网络到 Robinhood Chain） |
| LP 凭证 | **`UNI-V2` ERC-20 份额代币**（非 NFT，与 V3 不同） |
| 接入情况 | 待定 |

### 已定位合约（Robinhood Chain）

| 类型 | 地址 |
|------|------|
| UniswapV2Factory | `0xeeFAE6560808fa90Cd1d251917Ad2b27cCc01Ed9`、`0x95Ef745F698c7431EB61355436Df4E87B8b7fbF3`、`0x85039B2e95558aDdCCf4379728b8433C447E37bE`、`0xc34eF180F0c04AB02D04dD82dFf2B5D223542ACA` |
| UniswapV2Router02 | `0x95bE739Fe6C12548B3CdF9081fed6bc20d1568Bb`、`0x6248FeEb4E43D2398D3bb678A7Ca60B2b8D00abe`、`0xd2541D19f560F234754b9Cc4a688Ac4f30b35B77` |
| UniversalRouter | `0x40d6bdac60c0810fC3ed30a988A4c3ac890fdd43`、`0x74C7Cffa2c1669808747De8be9164Bb61726f7AE`、`0x37E076a3Fe61d6ef3459C1d4f583bD3e1B988334`、`0x8F8224DFc6a73F9E4A91aFf23152343790c63f67` |

⚠️ **实际成交走的 Router 不止官方那几个**：下方 3 笔样本的 `to` 分别是 `0x89e5db8b…`、`0x5bbb332a…`、`0x87b2f7d6…`，均不在上表内（疑为聚合器/自定义 Router）。**解析不能只按 Router 白名单认交易，应以 Pair 合约的事件为准。**

## 2. 操作清单

| # | 操作 | 入口路径 |
|---|------|---------|
| 1 | **Swap** | app.uniswap.org → 切 Robinhood Chain → Swap |
| 2 | **加流动性** | Pool → New position（选 v2） |
| 3 | **减流动性** | Pool → 我的仓位 → Remove |

### 2.1 📎 公开样本交易（**非本人钱包**，链上直接取样）

均取自 **2026-08-22 13:33~13:34 UTC** 的最新区块，Pair 的 LP 代币均为标准 `UNI-V2`。

| 交易类型 | 事件 topic0 | 样本 tx hash | Pair / 交易对 |
|---------|------------|-------------|--------------|
| **Swap** | `0xd78ad95f…59d822` | `0x275c2322e0e9ed8a35896746932c08a794c8ee87c399a3b13cebe4daf2fae1fd` | `0x43521210…6c05` **WETH/NSLD** |
| **加流动性（Mint）** | `0x4c209b5f…21c4f` | `0xf0b57927ca22220d2974cafb31fe9b9dd5f05352715e8bacf8ad2a6cfed6a0c4` | `0x2667c951…456e` **WETH/CATFERDON** |
| **减流动性（Burn）** | `0xdccd412f…36496` | `0xb05b1bf986fb0e988e0b03e5e936189a313a45bf567075aecd8e44ef8fbcb0a7` | `0xdb1b2bd5…5e5e` **WETH/ROHM** |

**取样方法**（可复现）：对 RPC `https://rpc.mainnet.chain.robinhood.com` 调 `eth_getLogs`，按上表 topic0 过滤最近 2000 个区块即可 —— Swap 240 条 / Mint 25 条 / Burn 3 条。

### 2.2 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| Swap | ⬜ | ✅ `0x275c23…` |
| 加流动性 Mint | ⬜ | ✅ `0xf0b579…` |
| 减流动性 Burn | ⬜ | ✅ `0xb05b1b…` |
| approve（前置授权） | ⬜ | ⬜ 标准 ERC-20，未单独取样 |

### 2.1.1 📷 前端确认（2026-08-22 截图）

✅ **app.uniswap.org 的网络选择器里能选到 Robinhood Chain**，且标记为「**新**」（截图 `UniswapV2-Robinhood链可选-20260822.png`）。

- 网络列表分组：**有余额**（Base / Arbitrum）+ **其他网络**（**Robinhood Chain 新** / Unichain / …）
- 该链已有代币在前端可选，如 **USDG**（`0x5fc5…d168`）
- 前端导航：探索 / 发行(Beta) / 流动性池 / 投资组合；交易面板支持**跨链兑换**

→ **结论：Robinhood Chain 在 Uniswap 官方前端已正式支持，用户可直接在 app.uniswap.org 上操作，无需直连合约。**

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | ✅ **已确认支持**，网络选择器内标「新」 | 2026-08-22 截图 |
| 2 | 4 套 Factory / 3 套 Router02 哪套是官方主用 | 比对 Pair 的 factory() |
| 3 | 样本里 3 个非标 Router 是什么（聚合器？） | 读合约 |
| 4 | Robinhood Chain 上是否也有 Uniswap V3/V4 | Factory 搜索 |
