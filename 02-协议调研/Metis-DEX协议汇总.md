# Metis Andromeda — DEX 协议调研

> **链**：Metis Andromeda（以太坊 L2） ｜ chainId **1088** ｜ 浏览器 https://andromeda-explorer.metis.io ｜ RPC `https://andromeda.metis.io/?owner=1088`
> **调研日期**：2026-09-29 ｜ hash 均为链上公开交易（非本人钱包），已逐条确认成功

## 协议总览

| # | 协议 | 类型 | 入选理由 | 前端 | hash |
|---|---|---|---|:-:|:-:|
| 1 | Hercules V3 | Algebra 式 CLMM | 交易量 50.0% | 🔴 503 | 🟡 |
| 2 | Netswap | Uniswap V2 式 | Ave 支持，交易量 42.4% | ✅ | ✅ |
| 3 | WAGMI | Uniswap V3 式 CLMM | OKX 支持，交易量 4.0% | ✅ | 🟡 |
| 4 | Tethys | Uniswap V2 式 | OKX 支持，交易量 3.4% | 🔴 域名待售 | ✅ |

四个协议合计覆盖 99.9% 交易量；Hercules V3、WAGMI 的 Confluence LP 样本类型登记有误，已补采（见各节）。

---

## 1. Hercules V3（Algebra CLMM）

| 项 | 值 |
|---|---|
| 前端 | 🔴 https://app.hercules.exchange 返回 503，无可用前端 |
| Factory | `0xc5bfa92f27df36d268422ee314a1387bb5ffb06a` |
| ALM 金库（Hypervisor） | `0x015b8a7698148271dc95635e32a6a76d723dbaff`（份额代币 `aWMETIS-WETH`）；rebalance 调用方 Admin `0x2ffaced56c4366115b65adbb8703a5541a27973d` |
| 示例池 | `0xbd718c67cd1e2f7fbe22d47be21036cd647c7714`（WETH/WMETIS，AlgebraPool 动态费率） |
| LP 凭证 | ALM 金库份额 ERC-20 `aWMETIS-WETH` |

| 行为 | tx hash | 截图 |
|---|---|---|
| Swap | [0x838817e92deb68055d8e094299c65cc0e1db309095c7f8f3efa42a91b7050e96](https://andromeda-explorer.metis.io/tx/0x838817e92deb68055d8e094299c65cc0e1db309095c7f8f3efa42a91b7050e96) | [交易页](截图/HerculesV3-Metis-swap交易-20260929.png) |
| Swap | [0x6b2582f28f2d7ce515f1e2ad5229a28e0c408aa8b2b1ce4253dc049f3ada2539](https://andromeda-explorer.metis.io/tx/0x6b2582f28f2d7ce515f1e2ad5229a28e0c408aa8b2b1ce4253dc049f3ada2539) |  |
| Swap | [0xc84c5303f41ee81a9081b2de723f94aa0952987d9c09e3c0908bfd390d268237](https://andromeda-explorer.metis.io/tx/0xc84c5303f41ee81a9081b2de723f94aa0952987d9c09e3c0908bfd390d268237) |  |
| Swap | [0xbda912a1e2c6b63061b226d781d147363d8e58d3b92289fa048c28c9e83300ee](https://andromeda-explorer.metis.io/tx/0xbda912a1e2c6b63061b226d781d147363d8e58d3b92289fa048c28c9e83300ee) |  |
| 加流动性（ALM 金库存入，补采候选；池子无 Mint） | [0x95451160ca41411b1d37d1ce207b0f731d625272dd95599a330e8f7e59840809](https://andromeda-explorer.metis.io/tx/0x95451160ca41411b1d37d1ce207b0f731d625272dd95599a330e8f7e59840809) |  |
| 减流动性（用户赎回 ALM 金库，补采） | [0x9f950722d202aeafd1945a52920eae909efa3d86ed97fc7dbf27b3940b959090](https://andromeda-explorer.metis.io/tx/0x9f950722d202aeafd1945a52920eae909efa3d86ed97fc7dbf27b3940b959090) |  |
| （Confluence 原登记为加流动性，实为 ALM rebalance，勿用） | [0x53e0a308d7752fdf47170cee8e2c565d8dce712fcdb0cd7528a8ceaa9ee96d69](https://andromeda-explorer.metis.io/tx/0x53e0a308d7752fdf47170cee8e2c565d8dce712fcdb0cd7528a8ceaa9ee96d69) | [交易页](截图/HerculesV3-Metis-加流动性交易-20260929.png) |
| （Confluence 原登记为减流动性，实为 ALM rebalance，勿用） | [0xf16875aa44b15dc1f041056ec4cd0f367f12fd715625d840cf238b036572ea55](https://andromeda-explorer.metis.io/tx/0xf16875aa44b15dc1f041056ec4cd0f367f12fd715625d840cf238b036572ea55) | [交易页](截图/HerculesV3-Metis-减流动性交易-20260929.png) |

前端截图：无（前端 503，存证见 [503 页](截图/HerculesV3-Metis-前端503-20260929.png)）

⚠️ 开发注意：
- 池子的 Mint / Burn 几乎全部来自 Admin 合约的 `rebalance`（同笔 Burn + Collect + Mint），用户侧 LP 行为要按 **ALM 金库份额铸造/销毁** 识别。
- Swap 均由聚合器（如 `0xf613e798…0fe67`）路由进来，**解析以池子事件为准**。

---

## 2. Netswap（V2）

| 项 | 值 |
|---|---|
| 前端 | https://netswap.io/#/swap ｜ 流动性 https://netswap.io/#/pool |
| Factory | `0x70f51d68d16e8f9e418441280342bd43ac9dff9f` |
| Router | `0x1e876cce41b7b844fde09e38fa1cf00f213bff56`（NetswapRouter，`addLiquidityMetis` / `removeLiquidity`） |
| 示例池 | `0x3d60afecf67e6ba950b499137a72478b2ca7c5a1`（m.USDT/Metis）；LP 样本在 `0x5ae3ee7f…5091`（m.USDC/Metis） |
| LP 凭证 | ERC-20 `NLP` |

| 行为 | tx hash | 截图 |
|---|---|---|
| Swap | [0x360cc32c952cefb9458ece8d5cbb298534184d29f34e16d4d2f3a00d911fc994](https://andromeda-explorer.metis.io/tx/0x360cc32c952cefb9458ece8d5cbb298534184d29f34e16d4d2f3a00d911fc994) | [交易页](截图/Netswap-Metis-swap交易-20260929.png) |
| Swap | [0xa2563707a14a12235cea0faf1c73564609968847ca7c9b1fc3280cd7aef15133](https://andromeda-explorer.metis.io/tx/0xa2563707a14a12235cea0faf1c73564609968847ca7c9b1fc3280cd7aef15133) |  |
| Swap | [0x1c94f8d06b2662b376c9067fe3109aa51cfe47ba89e56e49ab5b07b18fdd6359](https://andromeda-explorer.metis.io/tx/0x1c94f8d06b2662b376c9067fe3109aa51cfe47ba89e56e49ab5b07b18fdd6359) |  |
| Swap | [0xe5f1766e30db38c5ef510f625b235804d127a5f3643bfa2c6670cdd55c2d9df7](https://andromeda-explorer.metis.io/tx/0xe5f1766e30db38c5ef510f625b235804d127a5f3643bfa2c6670cdd55c2d9df7) |  |
| 加流动性 | [0xc0a63070b3c1e0296ab6dd8a4504b2629f505b0a598b89fd98f4b57eb88e8f11](https://andromeda-explorer.metis.io/tx/0xc0a63070b3c1e0296ab6dd8a4504b2629f505b0a598b89fd98f4b57eb88e8f11) | [交易页](截图/Netswap-Metis-加流动性交易-20260929.png) |
| 减流动性 | [0x19e4d78ed661aeb4fdf1e6c391b63b879f0f6a1d5be7f6444b087410374b7b9f](https://andromeda-explorer.metis.io/tx/0x19e4d78ed661aeb4fdf1e6c391b63b879f0f6a1d5be7f6444b087410374b7b9f) | [交易页](截图/Netswap-Metis-减流动性交易-20260929.png) |

前端截图：[swap 页](截图/Netswap-Metis-swap页-20260929.png) ｜ [流动性页](截图/Netswap-Metis-流动性页-20260929.png)

---

## 3. WAGMI（V3 / CLMM）

| 项 | 值 |
|---|---|
| 前端 | https://app.wagmi.com/trade/swap?chain=1088 ｜ 流动性 https://app.wagmi.com/liquidity/pools?chain=1088（不带 `?chain=1088` 默认落 Sonic；部分地区 451） |
| Factory | `0x8112e18a34b63964388a3b2984037d6a2efe5b8a` |
| NonfungiblePositionManager | `0xa7e119cf6c8f5be29ca82611752463f0ffcb1b02` |
| 其他合约 | GMI `0x19eab1a88328da0fb9471f36582a3c107e740776`；杠杆 LiquidityBorrowingManager `0xca95290e0079ae61f4c433819607f53f9feced53` |
| 示例池 | `0xd0c5ecbb9e363531ea4cbf5807837c656d308eb0`（WETH/WMETIS，fee 0.15%） |
| LP 凭证 | NFT `Uniswap V3 Positions NFT-V1` |

| 行为 | tx hash | 截图 |
|---|---|---|
| Swap | [0x2e60845c6478281aedf102ee998207157c8dfc4c8f61da15e67b84bd4794574d](https://andromeda-explorer.metis.io/tx/0x2e60845c6478281aedf102ee998207157c8dfc4c8f61da15e67b84bd4794574d) | [交易页](截图/WAGMI-Metis-swap交易-20260929.png) |
| Swap | [0x26b5f044e5bf100368d1a75fe86a64c85298a930ff2ec1205c6273c4ddd4ea2c](https://andromeda-explorer.metis.io/tx/0x26b5f044e5bf100368d1a75fe86a64c85298a930ff2ec1205c6273c4ddd4ea2c) |  |
| Swap | [0x1859114ac67c7192239a46ee73a6d99148de518703cfcf2a190959feb2a2a2de](https://andromeda-explorer.metis.io/tx/0x1859114ac67c7192239a46ee73a6d99148de518703cfcf2a190959feb2a2a2de) |  |
| Swap | [0x8d2e87b3df02ae1a0eee37454deff4e855a2682fc9954c830ad6ea2ea9d9f96a](https://andromeda-explorer.metis.io/tx/0x8d2e87b3df02ae1a0eee37454deff4e855a2682fc9954c830ad6ea2ea9d9f96a) |  |
| 加流动性（IncreaseLiquidity，经第三方合约调用，补采候选） | [0xfec2598b5689db4f1f8425d0c36be4b6328752dd4eab3dd98996d6cff3894d61](https://andromeda-explorer.metis.io/tx/0xfec2598b5689db4f1f8425d0c36be4b6328752dd4eab3dd98996d6cff3894d61) |  |
| 减流动性（PositionManager multicall，补采） | [0xd6dabf9e8f8b1c6f6fdac4e6192226a63367d7d6c8b77c8c67740392e6e00809](https://andromeda-explorer.metis.io/tx/0xd6dabf9e8f8b1c6f6fdac4e6192226a63367d7d6c8b77c8c67740392e6e00809) |  |
| （Confluence 原登记为加流动性，实为杠杆 repayBorrow，勿用） | [0x3a8b4d7a0dd692561b42fc643fd06df956c632ad77049cd03a5a8c9d3da172b9](https://andromeda-explorer.metis.io/tx/0x3a8b4d7a0dd692561b42fc643fd06df956c632ad77049cd03a5a8c9d3da172b9) | [交易页](截图/WAGMI-Metis-加流动性交易-20260929.png) |
| （Confluence 原登记为减流动性，实为 GMI 金库操作，勿用） | [0xf31fa92997553b89ce1b853c4f5a671bfdf3d9534fa33bbcf5d19195fa67063d](https://andromeda-explorer.metis.io/tx/0xf31fa92997553b89ce1b853c4f5a671bfdf3d9534fa33bbcf5d19195fa67063d) | [交易页](截图/WAGMI-Metis-减流动性交易-20260929.png) |

前端截图：[swap 页](截图/WAGMI-Metis-swap页-20260929.png) ｜ [流动性页](截图/WAGMI-Metis-流动性页-20260929.png)

⚠️ 开发注意：
- 池子的 Mint / Burn 还会来自杠杆（LiquidityBorrowingManager）和 GMI 合约，**只按 PositionManager 识别 LP 会漏，按池子事件识别会混入杠杆/GMI**，需要按调用方区分。
- Swap 样本均经聚合器 `0xf613e798…` 路由，解析以池子事件为准。

---

## 4. Tethys（V2）

| 项 | 值 |
|---|---|
| 前端 | 🔴 tethys.finance 域名待售，无可用前端 |
| Factory | `0x2cdfb20205701ff01689461610c9f321d1d00f80` |
| Router | `0x81b9fa50d5f5155ee17817c21702c3ae4780ad09`（UniswapV2Router02，`addLiquidityETH` / `removeLiquidityETH`） |
| 示例池 | `0xee5adb5b0dfc51029aca5ad4bc684ad676b307f7`（WETH/Metis） |
| LP 凭证 | ERC-20 `TETHYSLP` |

| 行为 | tx hash | 截图 |
|---|---|---|
| Swap | [0x416b7da7587064329789676b96a87712ee256d14b847f0fece8f8c131d0e2dfa](https://andromeda-explorer.metis.io/tx/0x416b7da7587064329789676b96a87712ee256d14b847f0fece8f8c131d0e2dfa) | [交易页](截图/Tethys-Metis-swap交易-20260929.png) |
| Swap | [0x861c250c04681282faab69017271731596e37059ea8e569c63967e6483684957](https://andromeda-explorer.metis.io/tx/0x861c250c04681282faab69017271731596e37059ea8e569c63967e6483684957) |  |
| Swap | [0xd722e6957f621ac4caa5c8ef07622067871db93569db9d1f2602900e0d76ac30](https://andromeda-explorer.metis.io/tx/0xd722e6957f621ac4caa5c8ef07622067871db93569db9d1f2602900e0d76ac30) |  |
| Swap | [0xa1bcff6a23130c19dfb16a0a4c08c62f201a9dc593ea7d624e0b0533d918290e](https://andromeda-explorer.metis.io/tx/0xa1bcff6a23130c19dfb16a0a4c08c62f201a9dc593ea7d624e0b0533d918290e) |  |
| 加流动性 | [0xc3ddb2ec1dd39c685820f45fb4295ecfba6ec3b3583e9d7fefabb90e97c04f68](https://andromeda-explorer.metis.io/tx/0xc3ddb2ec1dd39c685820f45fb4295ecfba6ec3b3583e9d7fefabb90e97c04f68) | [交易页](截图/Tethys-Metis-加流动性交易-20260929.png) |
| 减流动性 | [0xe54ec04e1adbe96e67c44c1568906aee0cb6ee953e6c1524d80838a744bcb7d2](https://andromeda-explorer.metis.io/tx/0xe54ec04e1adbe96e67c44c1568906aee0cb6ee953e6c1524d80838a744bcb7d2) | [交易页](截图/Tethys-Metis-减流动性交易-20260929.png) |

前端截图：无（域名待售，存证见 [域名待售页](截图/Tethys-Metis-域名待售-20260929.png)）

⚠️ 开发注意：Swap 基本由聚合器路由进来，解析以池子事件为准。

---

## 待确认

- Hercules V3、Tethys 前端已死、只剩聚合器流量，是否仍按常规口径接入
- Hercules V3 / WAGMI 的补采 LP 样本是否采用，并是否同步修正 Confluence 原登记
