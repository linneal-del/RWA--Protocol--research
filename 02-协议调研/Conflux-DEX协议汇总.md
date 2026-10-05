# Conflux eSpace — DEX 协议调研

> **链**：Conflux eSpace ｜ chainId **1030** ｜ 浏览器 https://evm.confluxscan.io ｜ RPC `https://evm.confluxrpc.com`
> **调研日期**：2026-09-29 ｜ hash 均为链上公开交易（非本人钱包），已逐条确认成功

## 协议总览

| # | 协议 | 类型 | 入选理由 | 前端 | hash |
|---|---|---|---|:-:|:-:|
| 1 | Swappi | Uniswap V2 式 | Ave + OKX 支持，交易量 84.1% | ✅ | ✅ |
| 2 | WallFreeX | Uniswap V3 式 CLMM | 交易量 15.9%（不收则覆盖率 <90%） | ✅ | ✅ |

两个协议合计覆盖 100% 交易量（2026-08-18 快照）。

---

## 1. Swappi（V2）

| 项 | 值 |
|---|---|
| 前端 | https://app.swappi.io/#/swap ｜ 流动性 https://app.swappi.io/#/pool/v2 |
| Factory | `0xe2a6f7c0ce4d5d300f97aa7e125455f5cd3342f5` |
| Router | `0x62b0873055bf896dd869e172119871ac24aea305` |
| 示例池 | `0x8fcf9c586d45ce7fcf6d714cb8b6b21a13111e0b`（WCFX/USDT） |
| LP 凭证 | ERC-20 `PPI-LP` |

| 行为 | tx hash | 截图 |
|---|---|---|
| Swap | [0xc4984d86d7472ec7d6e566cba3087c2c34c55e69727b64681b13f69863794b96](https://evm.confluxscan.io/tx/0xc4984d86d7472ec7d6e566cba3087c2c34c55e69727b64681b13f69863794b96) | [交易页](截图/Swappi-Conflux-swap交易-20260929.png) |
| Swap | [0xa69fbf57d3d11d8d9238a99302553ca1326004c57a1b6383d15701de44c3fa51](https://evm.confluxscan.io/tx/0xa69fbf57d3d11d8d9238a99302553ca1326004c57a1b6383d15701de44c3fa51) | |
| Swap | [0x67d2337cbfcbb8dec14949e62f6b474a1285f8cf16b22b6ffaf67c18418ed20b](https://evm.confluxscan.io/tx/0x67d2337cbfcbb8dec14949e62f6b474a1285f8cf16b22b6ffaf67c18418ed20b) | |
| Swap | [0x53208770bea29fda5c76442638f85048e6ea9e042540bfa5629006e5d7f96866](https://evm.confluxscan.io/tx/0x53208770bea29fda5c76442638f85048e6ea9e042540bfa5629006e5d7f96866) | |
| 加流动性 | [0x688b937f06b7d995d929c28c4b8edde9cb2d0814ef96d8712b5836c1d3a41ca0](https://evm.confluxscan.io/tx/0x688b937f06b7d995d929c28c4b8edde9cb2d0814ef96d8712b5836c1d3a41ca0) | [交易页](截图/Swappi-Conflux-加流动性交易-20260929.png) |
| 减流动性 | [0xd74f6737649bd542450d6943909eb8f08d95caf743a3cd4a1e90d2bd6aa8fd5e](https://evm.confluxscan.io/tx/0xd74f6737649bd542450d6943909eb8f08d95caf743a3cd4a1e90d2bd6aa8fd5e) | [交易页](截图/Swappi-Conflux-减流动性交易-20260929.png) |

前端截图：[swap 页](截图/Swappi-Conflux-swap页-20260929.png) ｜ [流动性页](截图/Swappi-Conflux-流动性页-20260929.png)

⚠️ 开发注意：Swap 样本多数经 OKX DexRouter 等聚合器路由进来，不走 Swappi 自己的 Router，**解析请以池子事件为准，不要按 Router 白名单过滤**。

---

## 2. WallFreeX（V3 / CLMM）

| 项 | 值 |
|---|---|
| 前端 | https://app.wallfreex.com/swap ｜ 流动性 https://app.wallfreex.com/earn |
| Factory | `0x50cADdC77c6727Bdd3c78B428c149BF110b4f595` |
| NonfungiblePositionManager | `0x5414b6ae40fb093875284e09d517190096647b10` |
| 示例池 | `0xF45c6eaC91a1f18edd72d4CCcE859036c265Ff34`（WCFX/USDT0，fee 0.3%） |
| LP 凭证 | NFT `WallFreeX-LP-POS` |

| 行为 | tx hash | 截图 |
|---|---|---|
| Swap | [0x311e32dc663393e0cf4e6af6a76e4b40bb6d0c653125dfec13b6ec90670f46af](https://evm.confluxscan.io/tx/0x311e32dc663393e0cf4e6af6a76e4b40bb6d0c653125dfec13b6ec90670f46af) | [交易页](截图/WallFreeX-Conflux-swap交易-20260929.png) |
| Swap | [0x304da94747218dd5de725bbb669fd03d845529992c2c7f0c2193f88869332ebd](https://evm.confluxscan.io/tx/0x304da94747218dd5de725bbb669fd03d845529992c2c7f0c2193f88869332ebd) | |
| Swap | [0xf1f0280e57aa908a655071ff042260a31c3258d2d449313ec8548aab881e1795](https://evm.confluxscan.io/tx/0xf1f0280e57aa908a655071ff042260a31c3258d2d449313ec8548aab881e1795) | |
| Swap | [0xac4a961a8a29d9d1e71cc519685cf9e41669a1666ed687d43442fa4ce3cd3a6a](https://evm.confluxscan.io/tx/0xac4a961a8a29d9d1e71cc519685cf9e41669a1666ed687d43442fa4ce3cd3a6a) | |
| 加流动性 | [0x5d7eff13d18bd387be8fe9da23b152a33f7940e8f79273a9ca6e44ea4f093491](https://evm.confluxscan.io/tx/0x5d7eff13d18bd387be8fe9da23b152a33f7940e8f79273a9ca6e44ea4f093491) | [交易页](截图/WallFreeX-Conflux-加流动性交易-20260929.png) |
| 减流动性 | [0x2879c1aaaae772f5c9e8a7ff546a119b2f438fe3fabef672e5d09def87ae6efd](https://evm.confluxscan.io/tx/0x2879c1aaaae772f5c9e8a7ff546a119b2f438fe3fabef672e5d09def87ae6efd) | [交易页](截图/WallFreeX-Conflux-减流动性交易-20260929.png) |

前端截图：[swap 页](截图/WallFreeX-Conflux-swap页-20260929.png) ｜ [流动性页](截图/WallFreeX-Conflux-流动性页-20260929.png)

⚠️ 开发注意：GeckoTerminal 没有索引 WallFreeX 的池子，池子发现要走 Factory 的 `PoolCreated` 事件。

---

## 待确认

- WallFreeX 是否被 OKX 收录（不影响要不要接，只影响入选理由的写法）
