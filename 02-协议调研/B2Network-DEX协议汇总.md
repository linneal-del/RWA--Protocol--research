# B² Network 链 DEX 协议汇总（GlowSwap / MagicSwap / DYORSwap）

> **状态**：✅ 三个协议各 6 条 hash 已取到（GlowSwap 为近期交易；MagicSwap、DYORSwap 只有较早的历史交易）｜ 截图：GlowSwap 齐，DYORSwap 前端未切到 B²，MagicSwap 前端域名已无法解析
> **调研时间**：2026-09-29
> **交付口径**：每个协议 = 需要调研的协议 + 页面操作截图 + 对应行为的交易 hash + 背景信息；**不做链上深度解析**
> **链**：**B² Network**（chainId **223**，OP 架构 Bitcoin L2）
> **样本来源**：样本来自链上公开交易，**非本人钱包**
> **来源 Confluence**：pageId=609479855

## 0. 一句话结论

入选 3 / 3 个协议，交易量覆盖率 100%；**三个都是 OKX 支持 / Ave 不支持（协议级）**。链还活着，但 DEX 的实际量几乎全在 GlowSwap 一家：

- **GlowSwap**（PancakeSwap V3 fork）：🟢 还有交易，最新一笔 router swap 在 2026-09-27；DefiLlama TVL 约 **$249 万**，但前端 Pools 页 24h 交易量显示 **$0**，量非常小
- **MagicSwap**（V2 fork）：🔴 **前端域名 `swap.magicswap.cc` / `magicswap.cc` 已无法解析（DNS 无记录）**，链上最近一笔 swap 在 2026-07-18
- **DYORSwap**（V2 fork，多链部署）：🔴 **B² 上近乎停用**，抽查的 11 个 pair 最后一笔交易在 **2024-06-03**

## 1. 链基础信息

| 字段 | 值 |
|------|-----|
| Chain ID | **223** |
| 原生币 | **BTC**（18 位；wrapped 版为 WBTC `0x4200000000000000000000000000000000000006`） |
| RPC | `https://rpc.bsquared.network` ✅ ｜ `https://b2-mainnet.alt.technology` ✅（`eth_getLogs` 限 1000 区块/次）｜ `https://mainnet.b2-rpc.com` ❌ 401 tenant disabled ｜ `https://b2-mainnet-public.s.chainbase.com` ❌ 超时 ｜ `https://rpc.ankr.com/b2` ❌ 403 需付费 key |
| 区块浏览器 | https://explorer.bsquared.network （Blockscout 前端）｜ 后端 API **`https://backend-blockscout.bsquared.network/api/v2/`**（✅ 可直接调用；前端域名下 `/api` 是 404，这就是旧结论"浏览器是 Next.js、没 API"的原因） |
| DefiLlama TVL | 链 **$2,762,314**（2026-09-29） |
| 链状态 | 🟢 活着，最新区块 38,746,879（2026-09-29 04:52 UTC） |
| OKX / Ave | 链级：OKX ✅ ｜ Ave ✅；协议级（旧页）：三个协议都是 Ave ❌ / OKX ✅ |

---

## 2. GlowSwap

### 2.1 基础信息

| 字段 | 值 |
|------|-----|
| 类型 | DEX，集中流动性（**PancakeSwap V3 fork**：LP NFT 名 `Glow V3 Positions NFT-V1`、符号 `PCS-V3-POS`，Swap 事件带 protocolFees） |
| 官网 | https://glowswap.io |
| **操作入口** | https://glowswap.io/swap ｜ https://glowswap.io/pools （2026-09-29 截图确认，swap 页底部显示 **B² Mainnet**；`/liquidity` 返回 404） |
| Factory | `0x02eAFbE9dE030f69aF02B7D3F2f69B28016f3C83`（GlowV3Factory，共 84 条 PoolCreated） |
| SwapRouter | `0x96C565B2285842921beD78c8f3a1403a9E277070`（multicall） |
| NonfungiblePositionManager | `0x00e1C41497B8F2df3Ec32143Cd675F0af8Cf00F9` |
| 其他 | Quoter `0xaaDE1302a1b6d28949b630C873Fe2AB937D12BC6` ｜ MasterChefV3 `0x8E10abC4B8662dcbc1fE46aD6CEBC8e4ac27F4de` |
| 示例 pool | `0x7655b5ec615131d3fb56f9470a79f3499ea4a8f2`（**WBTC / USDT**，swap 主样本）｜ `0x3838bc0de097bc8553d8270eeb3fff0f5e35e9cc`（WBTC / USDT，另一费率档）｜ `0xc1ae36ba0c671f4ebb4cc94f6aa0d5d127deda70`（加流动性样本所在池） |
| Ave / OKX | Ave ❌ ｜ OKX ✅ |
| 交易量占比 | 100%（GT：日交易 5 笔 / $112.06） |

### 2.2 协议背景

GlowSwap 自称"B² 网络上的先驱 DEX"，DefiLlama 归类 Dexs，是 B² 链上 TVL 最大的 DEX（约 $249 万，前端 Pools 页显示 $400 万）。合约是 PancakeSwap V3 分叉，另有 Farms（MasterChefV3）。主要交易对是 WBTC/USDT、WBTC/USDC、USDT/USDC 等。

### 2.3 操作清单

| # | 操作 | 入口 |
|---|------|------|
| 1 | Swap（买 / 卖） | glowswap.io → Swap |
| 2 | 加流动性 | Pools → Add Liquidity（或池子行的 "+"） |
| 3 | 减流动性 | Pools → My Positions → Remove |
| 4 | Farm 质押 LP NFT | Farms（不在 6 条交付口径内） |

### 2.4 公开样本交易（非本人钱包）

示例池 token0 = WBTC、token1 = USDT。「买」= 付 USDT 得 WBTC，「卖」= 付 WBTC 得 USDT。

| 交易类型 | tx hash | 时间 (UTC) | 说明 | 截图 |
|---------|---------|-----------|------|------|
| 买（USDT→WBTC） | [0x3dc8c8eea8947bc35897b8a0667331ba94cf94e9ed2a2f62feed90fa1097e3ee](https://explorer.bsquared.network/tx/0x3dc8c8eea8947bc35897b8a0667331ba94cf94e9ed2a2f62feed90fa1097e3ee) | 2026-09-10 03:56 | SwapRouter，池 `0x7655b5…`，付 1 USDT | — |
| 买（USDT→WBTC） | [0x2731be97a14122d7e5ff18ab27808168194aeb78e1d2722dbf3b1f737a1d021a](https://explorer.bsquared.network/tx/0x2731be97a14122d7e5ff18ab27808168194aeb78e1d2722dbf3b1f737a1d021a) | 2026-09-21 09:44 | SwapRouter，池 `0x3838bc…`（WBTC/USDT 另一档） | — |
| 卖（WBTC→USDT） | [0x183c326e955ee017e4b19d8996e9ae6755762f1325639e34d4bb5ab171225381](https://explorer.bsquared.network/tx/0x183c326e955ee017e4b19d8996e9ae6755762f1325639e34d4bb5ab171225381) | 2026-09-27 08:41 | SwapRouter，池 `0x7655b5…` | `GlowSwap-B2-swap交易-20260929.png` |
| 卖（WBTC→USDT） | [0xb42cd98e8a17b86bddf03043c47e0cd0e075685c53446a34599f079a7c408fd7](https://explorer.bsquared.network/tx/0xb42cd98e8a17b86bddf03043c47e0cd0e075685c53446a34599f079a7c408fd7) | 2026-09-27 08:17 | SwapRouter，池 `0x7655b5…` | — |
| 加流动性（Mint） | [0xc4f6e6282a5c17ecacd6d543dc9932ed002cbe638f15cfa0bbef3566a3247170](https://explorer.bsquared.network/tx/0xc4f6e6282a5c17ecacd6d543dc9932ed002cbe638f15cfa0bbef3566a3247170) | 2026-04-29 18:01 | NPM，logs 含 Mint + IncreaseLiquidity，池 `0xc1ae36…` | `GlowSwap-B2-加流动性交易-20260929.png` |
| 减流动性（Burn） | [0x8b13091fab3013faae163926a41f0fab79ed4e41578884a7b71072a329f0f32e](https://explorer.bsquared.network/tx/0x8b13091fab3013faae163926a41f0fab79ed4e41578884a7b71072a329f0f32e) | 2026-08-24 11:16 | NPM multicall，logs 含 Burn + DecreaseLiquidity + Collect，池 `0x3838bc…` | `GlowSwap-B2-减流动性交易-20260929.png` |

📌 加流动性很少：NPM 最新 500 条日志里只有 1 次 IncreaseLiquidity（就是上表这笔，2026-04）。
📌 池子里最新的 swap 有一部分来自 `0x00000000001220099542D41a8c31C80DaB68EbDB`（非官方 Router，疑似机器人），样本只选了走官方 SwapRouter 的交易。

### 2.5 操作覆盖

| 交易类型 | hash | 截图 | 备注 |
|---------|------|:---:|------|
| Swap 买 ×2 | ✅ `0x3dc8c8…` / `0x2731be…` | — | |
| Swap 卖 ×2 | ✅ `0x183c32…` / `0xb42cd9…` | ✅ | |
| 加流动性 | ✅ `0xc4f6e6…` | ✅ | |
| 减流动性 | ✅ `0x8b1309…` | ✅ | |
| 前端 swap 页 / 流动性页 | — | ✅ | 均显示 B² Mainnet |

---

## 3. MagicSwap

### 3.1 基础信息

| 字段 | 值 |
|------|-----|
| 类型 | DEX，恒定乘积 AMM（Uniswap V2 fork，LP 代币名 `Magicswap LP Token` / `MLP`，事件 topic0 与标准 V2 一致） |
| 官网 | https://swap.magicswap.cc （旧页记录） |
| **操作入口** | ⚠️ 操作入口已失效：`https://swap.magicswap.cc/swap` 与 `https://magicswap.cc/` 在 2026-09-29 均 **DNS 无法解析**，前端截不到 |
| Factory | `0xdb8d3e993fa1d3085e99c3c20a4f844a6c6866df`（示例 pair 的 `factory()` 返回值） |
| Router | `0xF80fFcf0C54a1A3e62010b1428913721260af2B6`（卖出 / 加流动性 / 减流动性样本都走它）；买入样本走 `0x0bf56B5d21036B2F9345F3e59AfE3c6359BCBb96`（方法 `0x7a3082c9`，⚠️ 身份待查，疑似聚合器或另一版 Router） |
| 示例 pair | `0x3d5ACB96122bd133fe7d1bD6e674854F8d32Ba16`（**B2Baby / WBTC**，swap 样本）｜ `0x1567A5153048509792c9aDBa30eaBC728928D89c`（B2BTC / WBTC，加/减流动性样本） |
| 平台代币 | MAGIC `0xE6e454889E4496F70Fbe0b73DB39675B90CA67E3` |
| Ave / OKX | Ave ❌ ｜ OKX ✅ |
| 交易量占比 | 0%（GT：0 量） |

### 3.2 协议背景

MagicSwap 是 B² 上的一个 V2 分叉 DEX，发行了平台币 MAGIC。浏览器搜索到至少 11 个 MLP 交易对，绝大多数只在 2024 年上线初期有交易。**前端域名已失效，链上近两个多月无交易，基本可视为停运**。

### 3.3 操作清单

| # | 操作 | 入口 |
|---|------|------|
| 1 | Swap | ⚠️ 前端已失效，无法确认页面结构（按 V2 fork 常规应为 Swap / Pool 两页） |
| 2 | 加流动性 | 同上 |
| 3 | 减流动性 | 同上 |

### 3.4 公开样本交易（非本人钱包）

pair `0x3d5ACB…` token0 = B2Baby、token1 = WBTC。「买」= 付 WBTC 得 B2Baby，「卖」= 付 B2Baby 得 WBTC。

| 交易类型 | tx hash | 时间 (UTC) | 说明 | 截图 |
|---------|---------|-----------|------|------|
| 买（WBTC→B2Baby） | [0xd2e781b47772b8464001f2dec115822176394de7a274c3fa88d8c636d78171f9](https://explorer.bsquared.network/tx/0xd2e781b47772b8464001f2dec115822176394de7a274c3fa88d8c636d78171f9) | 2025-03-26 05:28 | 经 `0x0bf56B…` | — |
| 买（WBTC→B2Baby） | [0xc73502c7fb8297ecfb60ad9cddc72536dc354d182e099c69adb61802fa4be468](https://explorer.bsquared.network/tx/0xc73502c7fb8297ecfb60ad9cddc72536dc354d182e099c69adb61802fa4be468) | 2025-03-02 17:40 | 经 `0x0bf56B…` | — |
| 卖（B2Baby→WBTC） | [0xf41e6c8f4a98b27ff6b542175b3e21bcd8e9476a6aa874010fe1f2cce51d7f6f](https://explorer.bsquared.network/tx/0xf41e6c8f4a98b27ff6b542175b3e21bcd8e9476a6aa874010fe1f2cce51d7f6f) | 2026-07-18 00:03 | Router swapExactTokensForTokens | `MagicSwap-B2-swap交易-20260929.png` |
| 卖（B2Baby→WBTC） | [0x8ee073f04f376fb938860e114d04b8adc9f8bbe03def028751fb76b81c67c18d](https://explorer.bsquared.network/tx/0x8ee073f04f376fb938860e114d04b8adc9f8bbe03def028751fb76b81c67c18d) | 2026-07-18 00:03 | 同上 | — |
| 加流动性（Mint） | [0xbd925cdece770614841baae1205a3763dfb9fc9face10be28d6bb7ac14d92341](https://explorer.bsquared.network/tx/0xbd925cdece770614841baae1205a3763dfb9fc9face10be28d6bb7ac14d92341) | 2024-04-26 11:09 | Router addLiquidityETH，pair `0x1567A5…` | `MagicSwap-B2-加流动性交易-20260929.png` |
| 减流动性（Burn） | [0x0389cbbf589174162982908843d9852f8cd104337a897e00566bc9cbbbc7853a](https://explorer.bsquared.network/tx/0x0389cbbf589174162982908843d9852f8cd104337a897e00566bc9cbbbc7853a) | 2024-04-29 06:45 | Router removeLiquidityETHWithPermit，pair `0x1567A5…` | `MagicSwap-B2-减流动性交易-20260929.png` |

### 3.5 操作覆盖

| 交易类型 | hash | 截图 | 备注 |
|---------|------|:---:|------|
| Swap 买 ×2 | ✅ `0xd2e781…` / `0xc73502…` | — | 2025-03 历史交易 |
| Swap 卖 ×2 | ✅ `0xf41e6c…` / `0x8ee073…` | ✅ | 2026-07 |
| 加流动性 | ✅ `0xbd925c…` | ✅ | 2024-04 历史交易 |
| 减流动性 | ✅ `0x0389cb…` | ✅ | 2024-04 历史交易 |
| 前端 swap 页 / 流动性页 | — | ⬜ | 🔴 域名 DNS 无法解析，截不到 |

---

## 4. DYORSwap

### 4.1 基础信息

| 字段 | 值 |
|------|-----|
| 类型 | DEX，多链部署；B² 上找到的是 V2 fork（LP 代币 `DYOR LPs` / `DYOR-LP`，事件 topic0 与标准 V2 一致）。前端另有 Positions（V3）页签，B² 上是否有 V3 部署 ⚠️ 待查 |
| 官网 | https://dyorswap.finance |
| **操作入口** | https://dyorswap.finance/swap ｜ https://dyorswap.finance/liquidity （⚠️ 2026-09-29 截图时默认网络是 **X Layer（显示 OKB）**，未连钱包无法切到 B²） |
| Factory | `0x2ccadb1e437aa9cdc741574bda154686b1f04c09`（示例 pair 的 `factory()` 返回值） |
| Router | `0x5F6cC7a76c15eEb976c60abdc71A7D349e02D763` |
| 示例 pair | `0xAa282723ca21776F427A5f2c8A4FdcE0027E4c7F`（**Bitleaf / WBTC**，swap + 减流动性样本）｜ `0xD35366F95D309a2ED7C66CF63E4A262E67d560a8`（加流动性样本所在 pair） |
| 平台代币 | dyor `0x75151B386C00e78E621C9c2bDD72CDda287c6Ab9` |
| Ave / OKX | Ave ❌ ｜ OKX ✅ |
| 交易量占比 | 0%（GT：0 量） |

### 4.2 协议背景

DYORSwap 是多链部署的 DEX（前端默认 X Layer），导航有 Trade / Lock / Farms / IDO / Bridge / Launch。B² 上的部署在 2024-04 上线，浏览器能搜到至少 11 个 DYOR-LP 交易对，但**抽查的 11 个 pair 最后一笔交易都在 2024-06 之前**，B² 部署已基本停用。

### 4.3 操作清单

| # | 操作 | 入口 |
|---|------|------|
| 1 | Swap | dyorswap.finance → Trade → Swap（需先切到 B²） |
| 2 | 加流动性 | Trade → V2 → Add Liquidity |
| 3 | 减流动性 | Trade → V2 → Your Liquidity → Remove |

### 4.4 公开样本交易（非本人钱包）

pair `0xAa2827…` token0 = Bitleaf、token1 = WBTC。「买」= 付 BTC 得 Bitleaf，「卖」= 付 Bitleaf 得 BTC。

| 交易类型 | tx hash | 时间 (UTC) | 说明 | 截图 |
|---------|---------|-----------|------|------|
| 买（BTC→Bitleaf） | [0x43757adb7135a52bb14de7265f5351c016759e2654c7e4073f622e288ea7206f](https://explorer.bsquared.network/tx/0x43757adb7135a52bb14de7265f5351c016759e2654c7e4073f622e288ea7206f) | 2024-05-24 04:39 | Router swapExactETHForTokens | `DYORSwap-B2-swap交易-20260929.png` |
| 买（BTC→Bitleaf） | [0xc0c6e8e22b901c2676caf55acc39d67a73c5ac84bcfa376901aac09825d4c612](https://explorer.bsquared.network/tx/0xc0c6e8e22b901c2676caf55acc39d67a73c5ac84bcfa376901aac09825d4c612) | 2024-05-09 05:34 | 同上 | — |
| 卖（Bitleaf→BTC） | [0x8d5dd46e17a2765f84177c41c48925da8c63ba3c28e426715626ad77674aeeed](https://explorer.bsquared.network/tx/0x8d5dd46e17a2765f84177c41c48925da8c63ba3c28e426715626ad77674aeeed) | 2024-05-04 13:40 | Router swapExactTokensForETH | — |
| 卖（Bitleaf→BTC） | [0x98755f8b4dfd852ade72ab672c0fec694323ab3d7e21c749fab5a8b5606f7ad1](https://explorer.bsquared.network/tx/0x98755f8b4dfd852ade72ab672c0fec694323ab3d7e21c749fab5a8b5606f7ad1) | 2024-05-03 17:30 | 同上 | — |
| 加流动性（Mint） | [0x46cd4fbf6bcefccd0195e961617f1d13ab3cd4796ab8979d27320aff6ff45f78](https://explorer.bsquared.network/tx/0x46cd4fbf6bcefccd0195e961617f1d13ab3cd4796ab8979d27320aff6ff45f78) | 2024-04-17 12:33 | Router addLiquidityETH，pair `0xD35366…` | `DYORSwap-B2-加流动性交易-20260929.png` |
| 减流动性（Burn） | [0xdd8cc59647fc90320da4d1f3657f7de0686d9f4f5ce4c4daf47920a1a60847ef](https://explorer.bsquared.network/tx/0xdd8cc59647fc90320da4d1f3657f7de0686d9f4f5ce4c4daf47920a1a60847ef) | 2024-04-30 08:23 | Router（方法 `0x5b0d5984`），pair `0xAa2827…` | `DYORSwap-B2-减流动性交易-20260929.png` |

🔴 **坑**：pair `0xAa2827…` 最新两笔带 Mint 的交易（`0xffb543b9…` 2024-06-03、`0xc9f5e57c…` 2024-05-31）其实是 **swapExactTokensForETHSupportingFeeOnTransferTokens**——Bitleaf 是带税代币，卖出时合约自动把税加进池子，所以一笔卖出里同时出现 Swap + Mint + Swap。**这类 Mint 不是用户主动加流动性**，没作为加流动性样本。

### 4.5 操作覆盖

| 交易类型 | hash | 截图 | 备注 |
|---------|------|:---:|------|
| Swap 买 ×2 | ✅ `0x43757a…` / `0xc0c6e8…` | ✅ | 2024-05 历史交易 |
| Swap 卖 ×2 | ✅ `0x8d5dd4…` / `0x98755f…` | — | 2024-05 历史交易 |
| 加流动性 | ✅ `0x46cd4f…` | ✅ | 2024-04 历史交易 |
| 减流动性 | ✅ `0xdd8cc5…` | ✅ | 2024-04 历史交易 |
| 前端 swap 页 / 流动性页 | — | 🟡 | 已截图，但页面默认 X Layer，**未显示 B²** |

---

## 5. 截图（2026-09-29）

| 文件 | 内容 | 状态 |
|------|------|:---:|
| `GlowSwap-B2-swap页-20260929.png` | glowswap.io/swap，面板底部显示 **B² Mainnet** | ✅ |
| `GlowSwap-B2-流动性页-20260929.png` | glowswap.io/pools，Concentrated Pools，TVL $4,000,138，24h 量 $0，有 Add Liquidity / Create Pool | ✅ |
| `GlowSwap-B2-swap交易-20260929.png` / `-加流动性交易-` / `-减流动性交易-` | 浏览器交易详情 `0x183c32…` / `0xc4f6e6…` / `0x8b1309…` | ✅ |
| MagicSwap swap 页 / 流动性页 | 域名 DNS 无法解析，截图为空白，已删除 | ⬜ |
| `MagicSwap-B2-swap交易-20260929.png` / `-加流动性交易-` / `-减流动性交易-` | 浏览器交易详情 `0xf41e6c…` / `0xbd925c…` / `0x0389cb…` | ✅ |
| `DYORSwap-B2-swap页-20260929.png` | dyorswap.finance/swap，Swap / Positions / V2 三个页签，**默认代币 OKB（X Layer），未显示 B²** | 🟡 |
| `DYORSwap-B2-流动性页-20260929.png` | dyorswap.finance/liquidity（V2 页签，Your Liquidity + Add Liquidity），同样未显示 B² | 🟡 |
| `DYORSwap-B2-swap交易-20260929.png` / `-加流动性交易-` / `-减流动性交易-` | 浏览器交易详情 `0x43757a…` / `0x46cd4f…` / `0xdd8cc5…` | ✅ |

⚠️ dyorswap.finance 对无头浏览器默认弹 Cloudflare 验证，换成普通 Chrome UA 后才打开。

## 6. 采集尝试记录

| 数据源 | 结果 |
|--------|------|
| RPC `rpc.bsquared.network` / `b2-mainnet.alt.technology` | ✅ 可用（后者需带 User-Agent，否则 403）；`eth_getLogs` 超过 1000 区块报 `block range greater than 1000 max`；用来做 `eth_getTransactionReceipt` 复核 GlowSwap 样本、`eth_call` 读 pair 的 token0/token1/factory |
| 其余 3 个公开 RPC | ❌ 401 / 超时 / 403 |
| `explorer.bsquared.network/api/v2/...` | ❌ 404（前端域名没有 API） |
| 读前端 `/assets/envs.js` 找到 `NEXT_PUBLIC_API_HOST` | ✅ 后端是 `backend-blockscout.bsquared.network` |
| `backend-blockscout.bsquared.network/api/v2/addresses/<factory>/logs` | ✅ 拿到 GlowSwap 84 个池子（PoolCreated），再按池子拉 logs |
| `…/api/v2/search?q=Magic` / `q=DYOR` | ✅ 找到 MagicSwap / DYORSwap 的 LP 代币合约（即 pair 地址），再按 pair 拉 logs |
| `…/api/v2/transactions/<hash>` + `/logs` | ✅ 18 条样本逐条确认 status=ok，且 logs 里含对应事件（Swap / Mint / Burn / IncreaseLiquidity / DecreaseLiquidity） |
| GeckoTerminal / DexScreener | 旧结论：GT 流动性极低、DexScreener 无该链数据（本次未再查） |

## 7. 待办 / 缺口

| # | 事项 | 状态 |
|---|------|------|
| 1 | 🔴 MagicSwap 前端域名失效 + 链上 2 个多月无交易，建议业务确认是否仍接入 | 📌 待确认 |
| 2 | 🔴 DYORSwap 在 B² 上 2024-06 后无交易，建议业务确认是否仍接入 | 📌 待确认 |
| 3 | DYORSwap 前端截图未显示 B²：需要用户连钱包、手动切到 B² 后补截 | ⬜ |
| 4 | MagicSwap 买入样本走的 `0x0bf56B5d…` 是什么合约（聚合器 / 旧版 Router） | ⚠️ 待查 |
| 5 | DYORSwap 在 B² 上有没有 V3（前端有 Positions 页签） | ⚠️ 待查 |
| 6 | DYORSwap / MagicSwap 只抽查了浏览器搜索返回的前 11 个 pair，可能还有其他 pair 有较新的交易 | ⚠️ 待补 |
| 7 | 本人实测 hash | ⬜ 未做（按口径用公开样本） |
