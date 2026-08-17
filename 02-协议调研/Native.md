# Native — BSC / ETH 链上撮合交易 + 借贷型流动性池

> **状态**：⬜ 待实测 ｜ **调研时间**：2026-08-18
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **本次目标链**：**BSC / Ethereum**
> 🔴 **重大发现**：Native 的下单/成交在 **Native 自有链**上（50ms 出块、24h 1300 万笔），ETH/BSC 上只有 Pool 存取与入金 —— 见 §2.1.1

## 0. 一句话结论

Native 是**把定价与结算解耦**的流动性基础设施：自建撮合引擎（Native Core）做链上价格发现与订单撮合，把机构级流动性输出到各条链。用户侧有两类操作：**① Markets 页做 Swap（成交）② Pool 页 Supply/Withdraw 做 LP**（Pool 是**借贷型**池子，为结算提供资金并给 LP 生息，不是传统 AMM）。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Native（native.org） |
| 链 | **BSC / Ethereum（本次目标）**；Pool 覆盖 **8 条链** |
| 产品栈 | **Native Core**（链上撮合状态机）/ **Native Pro**（机构接入）/ **Native Relay**（钱包聚合器路由）/ **Native Pool**（借贷型 LP 池） |
| **操作入口** | **Markets（Swap 成交）**：https://app.native.org/markets/ETH-USDT ｜ **Pool（Supply/Withdraw）**：https://app.native.org/pool ｜ **Explorer**：https://app.native.org/explorer ｜ **Analytics**：https://app.native.org/analytics |
| Supply Coins | 按池而定（如 USDC；池列表可按链筛选） |
| Coins Integrated | Pool 存款凭证（份额，⚠️ 类型待实测确认） |
| Pool 规模 | TVL **$31.53M**（30D −22.49%）｜ Available Liquidity **$23.08M** ｜ Supply APY **up to 3.46%** ｜ **97 个池 / 8 条链** ｜ Utilization **48.0%**（$15.12M 已用） |
| 池样例 | USDC · Ethereum：APY 3.46%、24h Volume $1.82M、Pool Balance $2.12M、Total Deposit $2.21M |
| 文档 | https://docs.native.org/native-dev ｜ Twitter：`native_fi` |
| 状态标记 | app 标注 **Beta** |
| 接入情况 | 待定 |

## 2. 操作清单（用户去做交易 + 截图）

| # | 操作 | 入口路径 | 截图要点 |
|---|------|---------|---------|
| 1 | **Swap**（小额，BSC 或 ETH 交易对） | Markets → 选交易对 → Swap | 交易对、数量、成交价、滑点 |
| 2 | **Supply**（Pool 存款） | Pool → 选池（按链筛 BSC/ETH）→ Supply | 池名、链、数量、APY |
| 3 | **Withdraw**（Pool 取款） | Pool → My Deposit → Withdraw | 收回数量、是否有利用率限制 |

### 2.1 📷 页面结构（2026-08-18 截图确认）

> ⚠️ 截图原文件未落盘（会话图片缓存已清理），以下为逐项抄录，需原图请重截。

| 位置 | 内容 |
|------|------|
| 顶部导航 | **Markets** / **Explorer** / **Pool** / More（Analytics、Docs）+ 右上 **Deposit** 按钮；app 标 **BETA** |
| Markets 页 | **订单簿式交易界面**（非 AMM）：Order Book / Trades 切换，右侧 **Limit / Market** + **Buy / Sell**，**TIF = GTC**，Min order **10 USDC**，Fees **0.1% / 0.02%**（BNB/USDT 市场显示 0.001% / 0%） |
| 🔴 **Activate Account** | Markets 下单区有 **Activate Account** 按钮，**One-time activation fee ≈ $1.00**（弹窗显示 **0.000524 ETH**）；ETH 不足时按钮置灰显示 *Insufficient ETH for account activation* |
| **Deposit 弹窗** | Token（如 USDT）+ **Deposit chain**（下拉，如 Ethereum）+ Amount；即"**充值进交易账户**"，与 Pool 存款是两回事 |
| 底部账户区 | **Balances / Open Orders / Trade History / Account History** |
| Pool 页 | TVL **$31.54M**（30D −22.46%）｜ Available Liquidity **$23.05M** ｜ Supply APY **up to 5.00%** ｜ **97 池 / 8 链** ｜ Utilization **48.1%**（$15.18M 已用） |
| Pool 筛选 | All Chains 下拉 + 搜索框 + **All / Stocks / Crypto** |
| Pool 表头 | Asset / APY / 24h Volume / Pool Balance / Total Deposit / My Deposit → 每行 **Supply** 按钮 |
| Pool 样例 | USDC(Monad) 5.00%｜WETH(Monad) 4.00%｜USDC(Ethereum) 3.46% / $1.83M 24h｜USDC(Base) 3.24%｜cbBTC 3.00% |
| 代币化股票 | 顶部行情条含 **NVDA / QQQ / SPY / XAUt / PAXG**；Pool 有 Stocks 分类 |

🔴 **链上看不出的三件事**：① **Activate Account 一次性开户费**（$1）是交易前置条件；② **Deposit（充值到交易账户）** 与 **Pool Supply（做 LP）** 是两个完全不同的入口；③ 交易走订单簿撮合，不是 AMM swap。

### 2.1.1 🔴🔴 Native 有自己的链（Native Core appchain）——解析口径的根本分叉

Explorer 页标题即 **"Native chain explorer"**（截图 `Native-explorer-链-20260818.png`）：

| 指标 | 值（2026-08-18 UI 快照） |
|------|------|
| Avg TPS · 24h | **151.85** |
| **Block Time · 24h** | **50.00 ms** |
| Transactions · 24h | **13,119,938** |
| Active markets | **58** |
| 最新区块高度 | 162,441,067（每块 5~10 笔） |
| 交易 Type | **Batch order** / **Oracle** |

**交易详情页字段**（截图 `Native-batchorder详情-20260818.png`）：Tx Hash / **Submit Status** / **Action**（Batch order）/ **Signer** / Block / **Tx Index** / Timestamp（毫秒级）/ **Nonce** / **Expires After (ms)** / Events。

🔴 **结论：Native 的下单/成交发生在 Native 自己的链上，不在 Ethereum / BSC**。
- 扫 Ethereum / BSC 永远抓不到 Native 的成交记录 —— 只能抓到 **Pool 存取**（LP）和 **Deposit 入金**
- Native 链的 tx hash 也是 `0x`+64 hex，**但和 EVM 主网哈希不在同一命名空间**，解析侧不能混着查
- 查询入口：`https://app.native.org/explorer/tx/{hash}`（页面可访问，未找到公开 JSON API）
- 交易带 **Nonce + Expires After** 字段 → 订单有效期机制，非 EVM 常规语义

**Native 链交易样本（Batch order）**：`0x1c4da30f572a904c1880ee36eef9f5320b10e69eb70224f4727eff23c11fb51a`
（Signer `0x28229fae4b…2632bd6a`，Block #162441401，Tx Index 8，2026-08-17 17:07:50.960 UTC，20 个 Events）

### 2.2 📎 公开样本交易（**非本人钱包**，供解析同学取样用）

**已定位合约（Ethereum）**：
- **NativeLPToken**（LP 份额凭证，Pool 存款拿到的就是它）：`0xDd3DC634C127c999643c99b115Eca98fa14B7958`（另有 `0x716338B2…`、`0xA2EfE8e3…`、`0xB8858806…`、`0x888eF3F0…`）
- **CreditPoolFacet**（Diamond 分面，当前 Credit Pool 逻辑）：`0x59c738612c6F78Dd30593a12106954F50Da5d21f`、`0x3283f3C5…`、`0x2bf7E1df…`、`0x4C158bfE…`、`0x05c55c35…`
- 旧版 **NativePool**（2023 部署，已不活跃）：`0xA7E69a85…`、`0x0367b9C8…` 等

| 交易类型 | 链上方法 | 样本 tx hash |
|---------|---------|-------------|
| **Pool Supply（存款）** | `deposit` | `0xb6522ecda3ea6f1d42ee64374670fb8905377b806d4a773dce144aaa895f88d4` |
| **Pool Withdraw（赎回）** | `redeem` | `0x7b1a43f93fc165839a12d7ca5af9738354d2258d12a7cddf575222db3c0bce9b` |

🔴 **赎回有冷却期**：LP 合约存在 `setMinRedeemInterval` / `setRedeemCooldownExempt` 两个治理方法（样本 `0x139933fe328612d5c2c8ef163decd29f9ee3d904feaf52e3388d57f1ef042e0e`），说明**赎回受最小间隔/冷却限制**，解析侧需要"赎回中"状态。

**协议方运维方法**（非用户操作，解析应排除）：`setTrustedOperator` / `transferOwnership` / `setMinRedeemInterval` / `setRedeemCooldownExempt`

### 2.3 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| **Activate Account**（开户，$1） | ⬜ | ⬜ 待定位 |
| **Deposit**（充值进交易账户） | ⬜ | ⬜ 待定位 |
| **Trade / 下单**（Limit / Market，Buy/Sell） | ⬜ | ✅ **Native 链** `0x1c4da3…`（Batch order）｜⚠️ 不在 ETH/BSC 上 |
| Oracle（喂价，非用户操作） | — | 📌 Explorer 里另一类 Type，解析应排除 |
| **Pool Supply** | ⬜ | ✅ `0xb6522e…` |
| **Pool Withdraw / redeem** | ⬜ | ✅ `0x7b1a43…` |

⚠️ **本人钱包 `0x9DA4…c33c` 未开户**（ETH 余额 0.000119，不足 0.000524 开户费），故交易类操作无本人样本。

**页面实际导航**：Markets / Explorer / Pool（+ More：Analytics / Docs）。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | BSC / ETH 上分别有哪些池可 Supply | Pool 页按链筛选 |
| 2 | Pool 存款凭证是份额代币还是合约记账 | 实测 Supply 后看钱包 |
| 3 | Utilization 高时能否全额 Withdraw | 实测 |
| 4 | Swap 成交是链上撮合还是 RFQ 报价 | 实测交易 + 文档 |
