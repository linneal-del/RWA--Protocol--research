# BounceBit 链 DEX 协议汇总（BitSwap V3 / BitSwap V2）

> **状态**：🔴 **链已停运**（2026-08-20 起停止出块）｜ BitSwap V3 / V2 各 6 条历史 hash 已取到，均为停链前的历史交易
> **调研时间**：2026-09-29
> **交付口径**：每个协议 = 需要调研的协议 + 页面操作截图 + 对应行为的交易 hash + 背景信息；**不做链上深度解析**
> **链**：**BounceBit**（chainId **6001**）
> **样本来源**：样本来自链上公开交易，**非本人钱包**
> **来源 Confluence**：pageId=609479858

## 0. 一句话结论

🔴 **BounceBit 链已永久关停，建议整条链标「已停运，不接入」**。2026-08-19 21:02 UTC 起链上发生授权漏洞攻击（约 2.865 亿 BB 被转走），团队于 2026-08-20 在区块 **20,702,857** 停止出块，并宣布放弃自有 L1、将 BB 以 BEP-20 形式在 BNB Chain 重发（快照区块 **20,697,260**）。官方浏览器目前最后索引到的区块也正是 20,697,260（2026-08-19 21:02 UTC）。

- 入选协议 2 / 2（BitSwap V3、BitSwap V2），按旧页口径覆盖率 100%；**OKX 不支持 / Ave 支持**
- 两个协议的 6 类交易 hash 都已从链上历史里取到（BitSwap V3 最后一笔 swap 在 2026-08-17，V2 最后一笔路由 swap 在 2025-08，最后一次减流动性在 2026-04）
- DefiLlama 上 BounceBit 链 TVL、BitSwap V3/V2 TVL 从 2026-08-19 起全部归零；GeckoTerminal 已整链下架

## 1. 链基础信息

| 字段 | 值 |
|------|-----|
| Chain ID | **6001** |
| 原生币 | **BB**（18 位） |
| RPC | `https://fullnode-mainnet.bouncebitapi.com/` → ❌ 429 Too Many Requests（2026-09-29 实测）｜ `https://rpc.ankr.com/bouncebit` → ❌ 403 需付费 key ｜ `https://bouncebit.drpc.org` → ❌ 404。**无可用公开 RPC** |
| 区块浏览器 | https://bbscan.io （Blockscout 前端，Cloudflare 拦截脚本请求）｜ 后端 API `https://explorer.bouncebit.io/api/v2/`（✅ 可直接调用，本页 hash 全部由此取得并确认） |
| DefiLlama TVL | **$0**（2026-09-29；2026-08-16 为约 $36 万，2026-08-19 起归零） |
| 链状态 | 🔴 已停运：停止出块于区块 20,702,857（2026-08-20）；BB 迁到 BNB Chain（BEP-20） |
| OKX / Ave | OKX ❌（Wallet / Explorer 列表均未列出）｜ Ave ✅ |

停链消息来源：[The Block](https://www.theblock.co/news/ecosystems/2026-08-21-bouncebit-sunset-blockchain-migrate-bnb-chain-after-3-million-exploit-412485)、[BeInCrypto](https://beincrypto.com/bouncebit-exploit-chain-sunset-bb-reissue/)

---

## 2. BitSwap V3

### 2.1 基础信息

| 字段 | 值 |
|------|-----|
| 类型 | DEX，集中流动性（Uniswap V3 fork，LP 凭证为 NFT `BIT-V3-POS`） |
| 官网 | https://app.bouncebit.io/club/1 （旧页记录）｜ DefiLlama 登记 https://portal.bouncebit.io/trade/swap |
| **操作入口** | ~~https://portal.bouncebit.io/trade/swap~~ ｜ ~~https://portal.bouncebit.io/trade/liquidity~~ 🔴 已失效：2026-09-29 打开 swap 页会跳到 Strategy 理财页，DEX 入口已从前端移除 |
| Factory | `0x30a326d09E01d7960a0A2639c8F13362e6cd304A`（示例 pool 的创建者，PoolCreated 事件由它发出） |
| NonfungiblePositionManager | `0xC2f43eA9684cf24772193218043CDF4BC1428066`（Bitswap V3 Positions NFT-V1） |
| SwapRouter | `0x6155A20d309d7Bba3106e8F572CeFfb2828355DD`（exactInputSingle / exactOutputSingle）；另有 `0xC2984d09711Db7731f6b081e616BDF5de7bA0783`（`execute` 调用，疑似 Universal Router 类） |
| 示例 pool | `0xc6f18fb0938812aba99df851f9d25c5c58395b39`（**BBTC / WBB**，fee 1%，tickSpacing 200） |
| Ave / OKX | Ave ✅ ｜ OKX ❌ |
| 交易量占比 | 100%（旧页 GT 数据：日交易 62 笔 / $4,458，TVL $480,267，停链前口径） |

### 2.2 协议背景

BitSwap 是 BounceBit 官方生态的 DEX，V3 为 Uniswap V3 分叉，主力交易对是 BBTC/WBB。池子最后一笔 swap 在 2026-08-17，两天后链因安全事件停运。**协议已随链停止运行**。

### 2.3 操作清单

| # | 操作 | 入口 |
|---|------|------|
| 1 | Swap（买 / 卖） | portal.bouncebit.io → Trade → Swap |
| 2 | 加流动性（新建仓位 / 增加） | Trade → Liquidity → Add |
| 3 | 减流动性 | Liquidity → 我的仓位 → Remove |

### 2.4 公开样本交易（非本人钱包）

token0 = BBTC，token1 = WBB。「买」= 付 WBB 得 BBTC，「卖」= 付 BBTC 得 WBB。

| 交易类型 | tx hash | 时间 (UTC) | 说明 | 截图 |
|---------|---------|-----------|------|------|
| 买（WBB→BBTC） | [0x196a15a728d13f00b69eda5ad8bfd1711b1ecfc800a202e81aee05096f960b11](https://bbscan.io/tx/0x196a15a728d13f00b69eda5ad8bfd1711b1ecfc800a202e81aee05096f960b11) | 2026-08-17 00:14 | 经 `0xC2984d09…` execute，得 0.01 BBTC | `BitSwapV3-BounceBit-swap交易-20260929.png` |
| 买（WBB→BBTC） | [0x4c9a456ef0c9190eb11e52a93838cfac53929a7ea29680d826d9139b23e30c23](https://bbscan.io/tx/0x4c9a456ef0c9190eb11e52a93838cfac53929a7ea29680d826d9139b23e30c23) | 2026-08-16 02:51 | SwapRouter exactOutputSingle | — |
| 卖（BBTC→WBB） | [0xa5996a4995eaf0bf40501a17efcc3575c243742e3c0e7f4b745d0c9d61fc0177](https://bbscan.io/tx/0xa5996a4995eaf0bf40501a17efcc3575c243742e3c0e7f4b745d0c9d61fc0177) | 2026-08-15 09:40 | SwapRouter，卖 0.001 BBTC | — |
| 卖（BBTC→WBB） | [0x8ec352e0de4eb72dbe7a044ba487ab0a93c71ee06e04b22d7771c762733223b7](https://bbscan.io/tx/0x8ec352e0de4eb72dbe7a044ba487ab0a93c71ee06e04b22d7771c762733223b7) | 2026-08-15 09:39 | SwapRouter exactInputSingle，卖 0.005 BBTC | — |
| 加流动性（Mint） | [0xc4644b42ff7063220d4a215344a634a8c81cc6ff2f0950c04aa41af41cc72668](https://bbscan.io/tx/0xc4644b42ff7063220d4a215344a634a8c81cc6ff2f0950c04aa41af41cc72668) | 2025-10-17 10:54 | NPM multicall：建池 + 首次加流动性（logs 含 Mint + IncreaseLiquidity） | `BitSwapV3-BounceBit-加流动性交易-20260929.png` |
| 减流动性（Burn） | [0xca6812ba7aa8406277df8b44a6ed236e768f2f674cd568c94fcd672a304b7dcc](https://bbscan.io/tx/0xca6812ba7aa8406277df8b44a6ed236e768f2f674cd568c94fcd672a304b7dcc) | 2026-08-17 02:16 | NPM multicall（logs 含 Burn + DecreaseLiquidity + Collect） | `BitSwapV3-BounceBit-减流动性交易-20260929.png` |

⚠️ 该池子 3000 条最新日志里 2998 条是 Swap，只有 1 次 Burn、0 次 Mint；所以加流动性样本只能用建池那笔（2025-10）。NPM 上另有独立 IncreaseLiquidity 样本（如 `0x061de6fa9557e820f9d929fee6fe1f523cb611c4091f58795103178a814ff329`，区块 16692945），未确认对应哪个池，未入表。

### 2.5 操作覆盖

| 交易类型 | hash | 截图 | 备注 |
|---------|------|:---:|------|
| Swap 买 ×2 | ✅ `0x196a15…` / `0x4c9a45…` | 见 §5 | 历史交易 |
| Swap 卖 ×2 | ✅ `0xa5996a…` / `0x8ec352…` | — | 历史交易 |
| 加流动性 | ✅ `0xc4644b…` | 见 §5 | 建池同笔 |
| 减流动性 | ✅ `0xca6812…` | 见 §5 | |
| 前端 swap 页 / 流动性页 | — | ✅ | 前端 DEX 入口已下线（见 §5） |

---

## 3. BitSwap V2

### 3.1 基础信息

| 字段 | 值 |
|------|-----|
| 类型 | DEX，恒定乘积 AMM（Uniswap V2 fork，LP 凭证 ERC-20 `BIT-V2`） |
| 官网 | 同 V3（https://app.bouncebit.io/club/1 ｜ https://portal.bouncebit.io/trade/swap） |
| **操作入口** | 🔴 已失效（⚠️ V2/V3 原本共用同一前端，无独立 URL；现已随 DEX 入口一起下线，见 §5） |
| Factory | `0x6d2Ae8505Ab39c9cF94abf69d75acc6115C2E3c0`（示例 pair 的创建者） |
| Router | `0x2307D78A37C8b730DE93681e724DC72d9585C3fC`（swapExactETHForTokens / addLiquidityETH / removeLiquidityETHWithPermit 等） |
| 示例 pair | `0xfFF0E046F1b36Cb84520c2C9773067587B491E63`（WBB / BBUSD，swap 样本）｜ `0xc3963736958EA1A7ab0A06dE618c9e12E0cD91CC`（MUBI / WBB，加流动性样本）｜ `0xdE3419e177395a16A274ae8C566b53F3694a4290`（BBDOG / WBB，减流动性样本） |
| Ave / OKX | Ave ✅ ｜ OKX ❌ |
| 交易量占比 | 0%（旧页 GT：0 量） |

### 3.2 协议背景

BitSwap V2 是同一团队的 V2 版本，与 V3 共用前端。GT 口径下早已 0 量；链上看，经官方 Router 的普通 swap 最后集中在 **2025-07～08**，2026-04 还有零星减流动性和套利机器人的跨池 swap。**近期无交易，且已随链停止运行**。

### 3.3 操作清单

| # | 操作 | 入口 |
|---|------|------|
| 1 | Swap | Trade → Swap（路由自动选 V2 池） |
| 2 | 加流动性 | Liquidity → Add（选 V2） |
| 3 | 减流动性 | Liquidity → 我的仓位 → Remove |

### 3.4 公开样本交易（非本人钱包）

| 交易类型 | tx hash | 时间 (UTC) | 说明 | 截图 |
|---------|---------|-----------|------|------|
| 买（BB→BBUSD） | [0x3453f7b65f0e86b069f0a625b2a470ae160ec942c2a5ec53b2f647f22cf7f13d](https://bbscan.io/tx/0x3453f7b65f0e86b069f0a625b2a470ae160ec942c2a5ec53b2f647f22cf7f13d) | 2025-08-05 15:56 | Router swapExactETHForTokens，pair `0xfFF0E0…` | `BitSwapV2-BounceBit-swap交易-20260929.png` |
| 买（BB→BBUSD） | [0xc83b5e4eca93a1a7b07e8fa1b81077d72cc6ef42588b3417138e7c4ad39d0dbb](https://bbscan.io/tx/0xc83b5e4eca93a1a7b07e8fa1b81077d72cc6ef42588b3417138e7c4ad39d0dbb) | 2025-08-05 15:50 | 同上 | — |
| 卖（BBUSD→BB） | [0x9a935cd22739e89df4ce5056e6161d84a25fdad474d8cea1cabfbe5a00e61ff8](https://bbscan.io/tx/0x9a935cd22739e89df4ce5056e6161d84a25fdad474d8cea1cabfbe5a00e61ff8) | 2025-08-06 05:39 | Router swapExactTokensForETH，pair `0xfFF0E0…` | — |
| 卖（BBUSD→BB） | [0x032b479140b4823661c8645fdafca985f3f41d9769ada8749b9c18be3353e046](https://bbscan.io/tx/0x032b479140b4823661c8645fdafca985f3f41d9769ada8749b9c18be3353e046) | 2025-08-06 05:26 | 同上 | — |
| 加流动性（Mint） | [0xf776c5085054f5184ceefccf0133854071c1c352381b2315d5e370a476705260](https://bbscan.io/tx/0xf776c5085054f5184ceefccf0133854071c1c352381b2315d5e370a476705260) | 2025-11-13 03:37 | Router addLiquidityETH，pair MUBI/WBB | `BitSwapV2-BounceBit-加流动性交易-20260929.png` |
| 减流动性（Burn） | [0xd7396372b696aad9cb8078b305754603e8957602f3ee28f189ae582cd035f881](https://bbscan.io/tx/0xd7396372b696aad9cb8078b305754603e8957602f3ee28f189ae582cd035f881) | 2026-04-15 10:56 | Router removeLiquidityETHWithPermit，pair BBDOG/WBB | `BitSwapV2-BounceBit-减流动性交易-20260929.png` |

📌 V2 pair 上最近的 swap（如 MUBI/WBB 池 2026-04 的 `0x1d8114c6…`、`0x640ae708…`）都是经 `0x00000000000A111F…` 的**套利机器人跨池交易**（一笔里同时打 V3 池和 V2 池），不是前端用户操作，所以没选作样本。

### 3.5 操作覆盖

| 交易类型 | hash | 截图 | 备注 |
|---------|------|:---:|------|
| Swap 买 ×2 | ✅ `0x3453f7…` / `0xc83b5e…` | 见 §5 | 2025-08 历史交易 |
| Swap 卖 ×2 | ✅ `0x9a935c…` / `0x032b47…` | — | 2025-08 历史交易 |
| 加流动性 | ✅ `0xf776c5…` | 见 §5 | |
| 减流动性 | ✅ `0xd73963…` | 见 §5 | |

---

## 4. 采集尝试记录

| 数据源 | 结果 |
|--------|------|
| chainid.network 登记的唯一 RPC `fullnode-mainnet.bouncebitapi.com` | ❌ 429 Too Many Requests |
| `rpc.ankr.com/bouncebit` | ❌ 403（需付费 key） |
| `bouncebit.drpc.org` | ❌ 404 |
| bbscan.io `/api?module=...`（etherscan 兼容）、`/api/v2/...` | ❌ Cloudflare "Just a moment" 拦截 |
| Playwright 打开 bbscan.io 页面抓网络请求 | ✅ 发现真实后端 `https://explorer.bouncebit.io/api/v2/` |
| `explorer.bouncebit.io/api/v2/addresses/<pool>/logs`、`/transactions/<hash>/logs` | ✅ **可直接 curl**，本页 12 条 hash 全部由此取得，并逐条用 tx logs 确认含对应事件（Swap / Mint+IncreaseLiquidity / Burn+DecreaseLiquidity / V2 Swap / Mint / Burn） |
| GeckoTerminal `networks/bouncebit` | ❌ 404（整链下架） |
| DefiLlama | 链 TVL 与两个协议 TVL 自 2026-08-19 起为 0 |

⚠️ 因为没有可用 RPC，本页 hash 是用浏览器后端的 tx logs 确认的，没有用 `eth_getTransactionReceipt` 复核。

## 5. 截图（2026-09-29）

| 文件 | 内容 | 状态 |
|------|------|:---:|
| `BitSwapV3-BounceBit-swap页-20260929.png` | 打开 https://portal.bouncebit.io/trade/swap 直接跳到 **Strategy（CeDeFi 理财金库）页**，导航只剩 Strategy / Stats，**已没有 Swap 入口** | ✅（证明前端已下线 DEX） |
| `BitSwapV3-BounceBit-流动性页-20260929.png` | https://portal.bouncebit.io/trade/liquidity 只显示一个带 "Funding rate" 的空白交易骨架页（加载不出数据），**没有流动性管理界面** | ✅（证明前端已下线 DEX） |
| `BitSwapV3-BounceBit-swap交易-20260929.png` | bbscan 交易详情 `0x196a15…`（Success，execute，BBTC/WBB） | ✅ |
| `BitSwapV3-BounceBit-加流动性交易-20260929.png` | bbscan 交易详情 `0xc4644b…` | ✅ |
| `BitSwapV3-BounceBit-减流动性交易-20260929.png` | bbscan 交易详情 `0xca6812…` | ✅ |
| `BitSwapV2-BounceBit-swap交易-20260929.png` | bbscan 交易详情 `0x3453f7…` | ✅ |
| `BitSwapV2-BounceBit-加流动性交易-20260929.png` | bbscan 交易详情 `0xf776c5…` | ✅ |
| `BitSwapV2-BounceBit-减流动性交易-20260929.png` | bbscan 交易详情 `0xd73963…` | ✅ |
| BitSwap V2 swap 页 / 流动性页 | V2 与 V3 共用前端，同上两张图，不单独截 | — |

⚠️ bbscan.io 对无头浏览器默认弹 Cloudflare 验证，换成普通 Chrome UA 后可正常打开交易页。

## 6. 待办 / 缺口

| # | 事项 | 状态 |
|---|------|------|
| 1 | 🔴 **业务确认：BounceBit 链已停运，是否整条链标「已停运，不接入」**（与 Dogechain 同样处理） | 📌 待确认 |
| 2 | BB 已迁到 BNB Chain（BEP-20），若业务仍要覆盖 BB，应在 BSC 侧看，不再走 6001 链 | 📌 待确认 |
| 3 | 前端 swap / 流动性页：portal 已改成 CeDeFi 理财页，DEX 入口已下线（见 §5 截图） | ✅ 已截图留证 |
| 4 | 本人实测 hash | ⬜ 链已停，**无法再做实测** |
