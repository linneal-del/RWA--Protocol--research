# Cyber 链 DEX 协议汇总（CyberSwap）

> **状态**：✅ CyberSwap 6 条 hash 已齐（2 买 + 2 卖 + 加流动性 + 减流动性），补齐了旧页缺的加/减流动性
> **调研时间**：2026-09-29
> **交付口径**：每个协议 = 需要调研的协议 + 页面操作截图 + 对应行为的交易 hash + 背景信息；**不做链上深度解析**
> **链**：**Cyber**（chainId **7560**，OP Stack L2）
> **样本来源**：样本来自链上公开交易，**非本人钱包**
> **来源 Confluence**：pageId=609479859

## 0. 一句话结论

入选 1 / 1 个协议（CyberSwap），交易量覆盖率 100%；**OKX 不支持 / Ave 不支持（协议级）**。🟡 **链还在出块，但 DEX 极度萎缩**：DefiLlama 上 CyberSwap TVL 只有约 **$3,281**，主池 CYBER/WETH 大约每天 1～7 笔 swap。

🔴🔴 **CyberSwap 不是 Uniswap V3 fork，是 iZiSwap（点位 / DLOB）fork**：池子叫 `CyberSwapPool`，事件用 `tokenX/tokenY`、`leftPoint/rightPoint`，Swap 的 topic0 是 `0x0fe977d6…`（不是 V3 的 `0xc42079f9…`），还有**限价单**事件（AddLimitOrder / DecLimitOrder / CollectLimitOrder）。按 V3 topic0 扫是扫不到 swap 的。

## 1. 链基础信息

| 字段 | 值 |
|------|-----|
| Chain ID | **7560** |
| 原生币 | **ETH**（18 位） |
| RPC | `https://rpc.cyber.co/` ✅ ｜ `https://cyber.alt.technology/` ✅（2026-09-29 实测都可用；`eth_getLogs` 限 **1000 区块/次**，旧结论"不支持 eth_getLogs"不准确，是窗口太大被拒）｜ `cyber.drpc.org` ❌ 404 |
| 区块浏览器 | https://cyberscan.co （Blockscout，`/api/v2/` 和 etherscan 兼容 `/api?module=` 都能直接调） |
| DefiLlama TVL | 链 **$3,281**（2026-09-29，与 CyberSwap 的 TVL 相同） |
| 链状态 | 🟡 活着，最新区块 38,609,659（2026-09-29 02:11 UTC）；DEX 量极小 |
| OKX / Ave | 链级：OKX ❌ ｜ Ave ✅；协议级（旧页）：Ave ❌ / OKX ❌ |

---

## 2. CyberSwap

### 2.1 基础信息

| 字段 | 值 |
|------|-----|
| 类型 | DEX，集中流动性 + 限价单（**iZiSwap fork**，按点位 point 记价） |
| 官网 | https://cyberswap.cc |
| **操作入口** | https://cyberswap.cc/trade/swap ｜ https://cyberswap.cc/trade/liquidity （2026-09-29 截图确认两页均显示 Cyber 网络） |
| Factory | `0x8c7d3063579BdB0b90997e18A770eaE32E1eBb08`（示例池的创建者） |
| Swap 路由 | `0x3EF68D3f7664b2805D4E88381b64868a56f88bC4`（浏览器名 `Swap`，multicall） |
| LiquidityManager（LP NFT） | `0x19b683A2F45012318d9B2aE1280d68d3eC54D663`（加/减流动性都经它的 multicall） |
| 示例 pool | `0xa3c6ef565ab5cf989b1fb1ada2e89473ec06299f`（**CYBER / WETH**；tokenX = CYBER `0x14778860E937f509e651192a90589dE711Fb88a9`，tokenY = WETH `0x4200000000000000000000000000000000000006`） |
| Ave / OKX | Ave ❌ ｜ OKX ❌ |
| 交易量占比 | 100%（GT：日交易 7 笔 / $211.94） |

### 2.2 协议背景

CyberSwap 是 Cyber 链（原 CyberConnect 社交链）上唯一有量的 DEX，DefiLlama 描述是"Cyber 社交网络上做交易和挖矿的 DEX"。合约是 iZiSwap 架构，一个池子里同时有区间流动性和限价单。主池是 CYBER/WETH，TVL 几千美元。

### 2.3 操作清单

| # | 操作 | 入口 |
|---|------|------|
| 1 | Swap（买 / 卖） | cyberswap.cc → Trade → Swap |
| 2 | 加流动性 | Trade → Liquidity → Add（生成 LP NFT） |
| 3 | 减流动性 | Liquidity → 我的仓位 → Remove |
| 4 | 限价单（挂单 / 撤单 / 领取） | Swap 页 → Limit Order 页签（截图可见）；链上池子有 AddLimitOrder / DecLimitOrder 事件 |

### 2.4 公开样本交易（非本人钱包）

`sellXEarnY = false` 表示付 WETH 买 CYBER（买），`true` 表示卖 CYBER 得 WETH（卖）。

| 交易类型 | tx hash | 时间 (UTC) | 说明 | 截图 |
|---------|---------|-----------|------|------|
| 买（WETH→CYBER） | [0xcbab557ebd995728c6fb9e0f34f054a1b8045519f608b1abe20561553f192c9f](https://cyberscan.co/tx/0xcbab557ebd995728c6fb9e0f34f054a1b8045519f608b1abe20561553f192c9f) | 2026-08-18 02:58 | 旧页已有 | — |
| 买（WETH→CYBER） | [0x3e58f50c37854a1affac3f4470b3f803f2baf9600d0966def33e72376542fd77](https://cyberscan.co/tx/0x3e58f50c37854a1affac3f4470b3f803f2baf9600d0966def33e72376542fd77) | 2026-09-28 10:45 | 本次新补 | `CyberSwap-Cyber-swap交易-20260929.png` |
| 卖（CYBER→WETH） | [0xacc8b6da7243d0a6f38fa5778210b67ede1bbd5a4b5b979de2d927cbecef468d](https://cyberscan.co/tx/0xacc8b6da7243d0a6f38fa5778210b67ede1bbd5a4b5b979de2d927cbecef468d) | 2026-08-18 02:18 | 旧页已有 | — |
| 卖（CYBER→WETH） | [0x49040374da6f65cd756c431ad9ac78580d751729561fe53475b1a88d690b4978](https://cyberscan.co/tx/0x49040374da6f65cd756c431ad9ac78580d751729561fe53475b1a88d690b4978) | 2026-08-17 15:10 | 旧页已有 | — |
| 加流动性（Mint） | [0xd780db089dd106a2c081010092a0634b8ec49b3d3417ce0c144084f5444e5d27](https://cyberscan.co/tx/0xd780db089dd106a2c081010092a0634b8ec49b3d3417ce0c144084f5444e5d27) | 2026-09-06 20:29 | LiquidityManager multicall，本次新补 | `CyberSwap-Cyber-加流动性交易-20260929.png` |
| 减流动性（Burn） | [0xb1622996d2c1fa15362b6f025b5e9be51ac6c62db88088672a2a5069a138822d](https://cyberscan.co/tx/0xb1622996d2c1fa15362b6f025b5e9be51ac6c62db88088672a2a5069a138822d) | 2026-09-26 12:54 | LiquidityManager multicall，liquidity > 0，本次新补 | `CyberSwap-Cyber-减流动性交易-20260929.png` |

📌 旧页第 3 条 `0x0eb55bf68be9cf25337162154df3a9e4344fe2fb0cfa339e52622402d558cacf` 也是卖出，但它是**多跳**（同一笔里还打了池子 `0x0dA461…`），本页没作为主样本，旧页里保留不影响。
📌 池子里有不少 `Burn` 是 liquidity = 0（只结算手续费，如 `0xed233f3f…`），这类不能当"减流动性"样本。

### 2.5 操作覆盖

| 交易类型 | hash | 截图 | 备注 |
|---------|------|:---:|------|
| Swap 买 ×2 | ✅ `0xcbab55…` / `0x3e58f5…` | 见 §4 | |
| Swap 卖 ×2 | ✅ `0xacc8b6…` / `0x490403…` | — | |
| 加流动性 | ✅ `0xd780db…` | 见 §4 | 池子全历史只有 3 次 Mint |
| 减流动性 | ✅ `0xb16229…` | 见 §4 | |
| 限价单 | 🟡 链上有（如 AddLimitOrder `0xf5bcb86f91a52b4cf7662af4e7b4896acddfe7a2144ff882804edc481ca99f24`） | ⬜ | 不在 6 条交付口径内，列出供解析同学参考 |

---

## 3. 采集尝试记录

| 数据源 | 结果 |
|--------|------|
| RPC `rpc.cyber.co` / `cyber.alt.technology` | ✅ 可用；`eth_getLogs` 超过 1000 区块返回 `block range greater than 1000 max` |
| cyberscan.co `/api/v2/addresses/<pool>/logs` | ✅ 直接拿到全池 1000 条日志（Swap 882 / Burn 45 / CollectLiquidity 41 / DecLimitOrder 13 / CollectLimitOrder 9 / AddLimitOrder 7 / Mint 3），带解码后的事件名 |
| cyberscan.co `/api/v2/transactions/<hash>/logs` | ✅ 逐条确认 6 条样本都是 status=ok，且 logs 里含对应事件（Swap / Mint / Burn） |

## 4. 截图

| 文件 | 内容 | 状态 |
|------|------|:---:|
| `CyberSwap-Cyber-swap页-20260929.png` | https://cyberswap.cc/trade/swap ，右上角网络显示 **Cyber**；有 Swap / **Limit Order** 两个页签，导航 Swap / Pools / Farm / Points / Bridge / Analytics | ✅ |
| `CyberSwap-Cyber-流动性页-20260929.png` | https://cyberswap.cc/trade/liquidity ，网络选择器显示 **Cyber**，有 Add Liquidity 按钮（未连钱包） | ✅ |
| `CyberSwap-Cyber-swap交易-20260929.png` | cyberscan 交易详情 `0x3e58f5…` | ✅ |
| `CyberSwap-Cyber-加流动性交易-20260929.png` | cyberscan 交易详情 `0xd780db…`（LiquidityManager multicall） | ✅ |
| `CyberSwap-Cyber-减流动性交易-20260929.png` | cyberscan 交易详情 `0xb16229…`（CyberSwapPool → LiquidityManager 转出 CYBER） | ✅ |

## 5. 待办 / 缺口

| # | 事项 | 状态 |
|---|------|------|
| 1 | 🔴🔴 解析口径：CyberSwap 是 **iZiSwap 架构**，事件签名与 Uniswap V3 不同，且有限价单 | 📌 提醒解析同学 |
| 2 | 链上量极小（TVL ~$3K），业务是否仍值得接入 | 📌 待确认 |
| 3 | 本人实测 hash | ⬜ 未做（按口径用公开样本） |
