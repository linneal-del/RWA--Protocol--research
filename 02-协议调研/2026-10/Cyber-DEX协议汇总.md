# Cyber — DEX 协议调研

> **链**：Cyber（OP Stack L2） ｜ chainId **7560** ｜ 浏览器 https://cyberscan.co ｜ RPC `https://rpc.cyber.co/`（`eth_getLogs` 单次 ≤1000 区块）
> **调研日期**：2026-09-29，2026-10-05 补全交易类型 ｜ hash 均为链上公开交易（非本人钱包），已逐条确认成功

## 协议总览

| # | 协议 | 类型 | 入选理由 | 前端 | 交易类型覆盖 |
|---|---|---|---|:-:|:-:|
| 1 | CyberSwap | iZiSwap 式（点位 CLMM + 限价单） | 链上唯一有量 DEX，交易量 100% | ✅ | 14 / 14 有样本 |

单协议覆盖 100% 交易量；但链上 DEX 极度萎缩（TVL 约 $3K，主池日均 1～7 笔 swap）。

**交易类型怎么找全的**：① 前端逐页点（Swap / Limit Order / Pools / Farm / Points / Bridge）；② 链上把 Router、LiquidityManager、限价单、Farm 四个合约的近期交易按方法名归类，补上必须连钱包才看得到的操作（减流动性、领手续费、Farm 解押/领奖等）。两边交叉核对后共 14 类。

---

## 1. CyberSwap（iZiSwap fork）

| 项 | 值 |
|---|---|
| 前端 | https://cyberswap.cc/trade/swap ｜ 限价单 https://cyberswap.cc/trade/limit ｜ 流动性 https://cyberswap.cc/trade/pools ｜ Farm https://cyberswap.cc/farm/dynamic |
| Factory | `0x8c7d3063579BdB0b90997e18A770eaE32E1eBb08` |
| Swap 路由 | `0x3EF68D3f7664b2805D4E88381b64868a56f88bC4` |
| LiquidityManager | `0x19b683A2F45012318d9B2aE1280d68d3eC54D663`（LP 仓位 NFT `CYBERSWAP-LIQUIDITY-NFT`） |
| 限价单合约 | `0x02F55D53DcE23B4AA962CC68b0f685f26143Bdb2` |
| Farm 合约 | `0x8981c60ff02CDBbF2A6AC1a9F150814F9cF68f62`（未开源，部署地址与 LiquidityManager 不同，由链上行为推定为前端 Farm 页所用合约） |
| 示例池 | `0xa3c6ef565ab5cf989b1fb1ada2e89473ec06299f`（CYBER/WETH；tokenX = CYBER `0x14778860E937f509e651192a90589dE711Fb88a9`，tokenY = WETH `0x4200000000000000000000000000000000000006`）｜ Farm 池 `0x7FcA2959…5425`（CYBER/cCYBER） |

### 交易类型覆盖

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash |
|---|---|---|---|---|
| 1 | Swap 买（WETH→CYBER） | Swap | 路由 · `swapAmount` | [0xcbab557ebd995728c6fb9e0f34f054a1b8045519f608b1abe20561553f192c9f](https://cyberscan.co/tx/0xcbab557ebd995728c6fb9e0f34f054a1b8045519f608b1abe20561553f192c9f) |
| | Swap 买（WETH→CYBER） | | | [0x3e58f50c37854a1affac3f4470b3f803f2baf9600d0966def33e72376542fd77](https://cyberscan.co/tx/0x3e58f50c37854a1affac3f4470b3f803f2baf9600d0966def33e72376542fd77) |
| | Swap 卖（CYBER→WETH） | | | [0xacc8b6da7243d0a6f38fa5778210b67ede1bbd5a4b5b979de2d927cbecef468d](https://cyberscan.co/tx/0xacc8b6da7243d0a6f38fa5778210b67ede1bbd5a4b5b979de2d927cbecef468d) |
| | Swap 卖（CYBER→WETH） | | | [0x49040374da6f65cd756c431ad9ac78580d751729561fe53475b1a88d690b4978](https://cyberscan.co/tx/0x49040374da6f65cd756c431ad9ac78580d751729561fe53475b1a88d690b4978) |
| | Swap 卖（多跳，同笔还经池子 `0x0dA461…`） | | | [0x0eb55bf68be9cf25337162154df3a9e4344fe2fb0cfa339e52622402d558cacf](https://cyberscan.co/tx/0x0eb55bf68be9cf25337162154df3a9e4344fe2fb0cfa339e52622402d558cacf) |
| 2 | Swap 指定输出金额 | Swap（输入目标数量） | 路由 · `swapDesire` | [0x5ccf08245a7758611771ebb39a9ab1dec3b586f12ec34d44c7c2c097777cec1b](https://cyberscan.co/tx/0x5ccf08245a7758611771ebb39a9ab1dec3b586f12ec34d44c7c2c097777cec1b) |
| 3 | 加流动性（开新仓位） | Pools → Add Liquidity | LiquidityManager · `mint` | [0xd780db089dd106a2c081010092a0634b8ec49b3d3417ce0c144084f5444e5d27](https://cyberscan.co/tx/0xd780db089dd106a2c081010092a0634b8ec49b3d3417ce0c144084f5444e5d27) |
| 4 | 追加流动性（已有仓位） | Pools → Manage Liquidity（需连钱包） | LiquidityManager · `addLiquidity` | [0xd414a462035d4e59f6afe7b5cc924deb4b0e27d074b91bb82864e24013403604](https://cyberscan.co/tx/0xd414a462035d4e59f6afe7b5cc924deb4b0e27d074b91bb82864e24013403604) |
| 5 | 减流动性 | Manage Liquidity（需连钱包） | LiquidityManager · `decLiquidity` + `collect` | [0xb1622996d2c1fa15362b6f025b5e9be51ac6c62db88088672a2a5069a138822d](https://cyberscan.co/tx/0xb1622996d2c1fa15362b6f025b5e9be51ac6c62db88088672a2a5069a138822d) |
| 6 | 只领手续费 | Manage Liquidity（需连钱包） | LiquidityManager · `collect` | [0x717bdd5e765bd4d2f0c0db4bc6027a762181f9f316944a75b766c8a7ce0c769c](https://cyberscan.co/tx/0x717bdd5e765bd4d2f0c0db4bc6027a762181f9f316944a75b766c8a7ce0c769c) |
| 7 | 关闭仓位（销毁 LP NFT） | Manage Liquidity（需连钱包） | LiquidityManager · `burn` | [0x47b2521079ac232794b769a0f8eb316660614f6ebd93126ff468d73304813e22](https://cyberscan.co/tx/0x47b2521079ac232794b769a0f8eb316660614f6ebd93126ff468d73304813e22) |
| 8 | 限价单挂单 | Swap → Limit Order | 限价单合约 · `newLimOrder` | [0x1badf9ddf1f8a84da34bbbb2a0946047a1375d60d6eb380ed454a95d699da15a](https://cyberscan.co/tx/0x1badf9ddf1f8a84da34bbbb2a0946047a1375d60d6eb380ed454a95d699da15a) |
| | 限价单挂单 | | | [0xf5bcb86f91a52b4cf7662af4e7b4896acddfe7a2144ff882804edc481ca99f24](https://cyberscan.co/tx/0xf5bcb86f91a52b4cf7662af4e7b4896acddfe7a2144ff882804edc481ca99f24) |
| 9 | 限价单撤单 | Limit Order → 我的订单（需连钱包） | 限价单合约 · `decLimOrder` + `collectLimOrder` | [0x9457b51dc97a1b0b6cf0b0d5c469c0936ceb2153dc02598b57cada33155b4770](https://cyberscan.co/tx/0x9457b51dc97a1b0b6cf0b0d5c469c0936ceb2153dc02598b57cada33155b4770) |
| 10 | 限价单成交后领取 | Limit Order → 我的订单（需连钱包） | 限价单合约 · `collectLimOrder` | [0x7f87c2849785b801dc314bd6f650d155ff186982bbd38ed57dd949b394923e4f](https://cyberscan.co/tx/0x7f87c2849785b801dc314bd6f650d155ff186982bbd38ed57dd949b394923e4f) |
| 11 | Farm 质押 | Farm → CYBER/cCYBER（需连钱包） | Farm · `deposit` | [0xd72e91be16ec6aee39a7c279f868005c70ba51e9c3a2ca92897a579294d06320](https://cyberscan.co/tx/0xd72e91be16ec6aee39a7c279f868005c70ba51e9c3a2ca92897a579294d06320) |
| 12 | Farm 解押 | Farm（需连钱包） | Farm · `withdraw` | [0x175f07ee0d438a0d16c10a4e1dd1581de38306e875955dd199f51f253bdbfaa3](https://cyberscan.co/tx/0x175f07ee0d438a0d16c10a4e1dd1581de38306e875955dd199f51f253bdbfaa3) |
| 13 | Farm 领全部奖励 | Farm（需连钱包） | Farm · `collectAllTokens` | [0x90eb5dc1fb7465e8100f445c7f8454c1a300d6c3c299a697bc52d92b75c9aedb](https://cyberscan.co/tx/0x90eb5dc1fb7465e8100f445c7f8454c1a300d6c3c299a697bc52d92b75c9aedb) |
| 14 | Farm 领单个仓位奖励 | Farm（需连钱包） | Farm · `collect` | [0x561a5138101cc05e4370389a58d92f9856dbafc3044d468684d4ee3350651ddf](https://cyberscan.co/tx/0x561a5138101cc05e4370389a58d92f9856dbafc3044d468684d4ee3350651ddf) |

页面有入口但**不产生 CyberSwap 交易**：Points（链下积分）、Bridge（跳转 Cyber Bridge / Orbiter / Owlto 等外部桥）、Analytics（数据页）。

### 截图

**前端页面**

| Swap | Limit Order |
|:-:|:-:|
| <img src="截图/CyberSwap-Cyber-swap页-20260929.png" width="460"> | <img src="截图/CyberSwap-Cyber-限价单页-20261005.png" width="460"> |
| **Pools（Add / Manage Liquidity）** | **Farm** |
| <img src="截图/CyberSwap-Cyber-Pools页-20261005.png" width="460"> | <img src="截图/CyberSwap-Cyber-Farm页-20261005.png" width="460"> |
| **Liquidity（未连钱包）** | **Points / Bridge（无链上交易）** |
| <img src="截图/CyberSwap-Cyber-流动性页-20260929.png" width="460"> | <img src="截图/CyberSwap-Cyber-Points页-20261005.png" width="225"> <img src="截图/CyberSwap-Cyber-Bridge页-20261005.png" width="225"> |

**交易详情（每类一张，均为 cyberscan）**

| 1 Swap（`0x3e58f5…`） | 2 Swap 指定输出（`0x5ccf08…`） |
|:-:|:-:|
| <img src="截图/CyberSwap-Cyber-swap交易-20260929.png" width="460"> | <img src="截图/CyberSwap-Cyber-指定输出swap交易-20261005.png" width="460"> |
| **3 加流动性（`0xd780db…`）** | **4 追加流动性（`0xd414a4…`）** |
| <img src="截图/CyberSwap-Cyber-加流动性交易-20260929.png" width="460"> | <img src="截图/CyberSwap-Cyber-追加流动性交易-20261005.png" width="460"> |
| **5 减流动性（`0xb16229…`）** | **6 只领手续费（`0x717bdd…`）** |
| <img src="截图/CyberSwap-Cyber-减流动性交易-20260929.png" width="460"> | <img src="截图/CyberSwap-Cyber-领手续费交易-20261005.png" width="460"> |
| **7 关闭仓位（`0x47b252…`）** | **8 限价挂单（`0x1badf9…`）** |
| <img src="截图/CyberSwap-Cyber-关闭仓位交易-20261005.png" width="460"> | <img src="截图/CyberSwap-Cyber-限价挂单交易-20261005.png" width="460"> |
| **9 限价撤单（`0x9457b5…`）** | **10 限价领取（`0x7f87c2…`）** |
| <img src="截图/CyberSwap-Cyber-限价撤单交易-20261005.png" width="460"> | <img src="截图/CyberSwap-Cyber-限价领取交易-20261005.png" width="460"> |
| **11 Farm 质押（`0xd72e91…`）** | **12 Farm 解押（`0x175f07…`）** |
| <img src="截图/CyberSwap-Cyber-Farm质押交易-20261005.png" width="460"> | <img src="截图/CyberSwap-Cyber-Farm解押交易-20261005.png" width="460"> |
| **13 Farm 领全部奖励（`0x90eb5d…`）** | **14 Farm 领单仓位奖励（`0x561a51…`）** |
| <img src="截图/CyberSwap-Cyber-Farm领全部奖励交易-20261005.png" width="460"> | <img src="截图/CyberSwap-Cyber-Farm领单仓位奖励交易-20261005.png" width="460"> |

⚠️ 开发注意：
- **不是 Uniswap V3 fork，是 iZiSwap 架构**：事件用 `tokenX/tokenY`、`leftPoint/rightPoint`，Swap topic0 为 `0x0fe977d6…`（非 V3 的 `0xc42079f9…`），方向看 `sellXEarnY`（false=买 CYBER，true=卖）；限价单另有 AddLimitOrder / DecLimitOrder / CollectLimitOrder 事件。池子里不少 `Burn` 的 liquidity = 0（只结算手续费），不能算减流动性。
- **Farm 质押不是"把已有 NFT 转进去"**：用户直接把 CYBER + cCYBER 转给 Farm 合约，由 Farm 代为加流动性并铸出 LP NFT 自己托管（见 #11 截图 Tokens minted → `0x89…8f62`），NFT 的 owner 是 Farm，不是用户。

---

## 待确认

- 链上量极小（TVL 约 $3K），业务上是否仍值得接入
- 限价单（#8～10）、Farm（#11～14）是否纳入解析范围
- Farm 合约 `0x8981c60f…` 未开源，需连钱包在前端 Farm 页确认调用的就是这个合约
