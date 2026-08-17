# Native — BSC / ETH 链上撮合交易 + 借贷型流动性池

> **状态**：⬜ 待实测 ｜ **调研时间**：2026-08-18
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **本次目标链**：**BSC / Ethereum**

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

### 2.1 ✅ 实测交易（只记哈希+截图，解析留给解析同学）

| # | 时间 | 操作（UI 视角） | 链 | tx hash | 截图 |
|:---:|------|---------------|---|---------|------|
| ⬜ | | 待实测 | | | |

### 2.2 操作覆盖（页面可点击交易）

| 交易类型 | 已测 | 哈希 |
|---------|:---:|------|
| Swap（Markets 成交） | ⬜ | — |
| Supply（Pool 存款） | ⬜ | — |
| Withdraw（Pool 取款） | ⬜ | — |
| approve（授权，前置） | ⬜ | — |

**页面实际导航**：Markets / Explorer / Pool（+ More：Analytics / Docs）。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | BSC / ETH 上分别有哪些池可 Supply | Pool 页按链筛选 |
| 2 | Pool 存款凭证是份额代币还是合约记账 | 实测 Supply 后看钱包 |
| 3 | Utilization 高时能否全额 Withdraw | 实测 |
| 4 | Swap 成交是链上撮合还是 RFQ 报价 | 实测交易 + 文档 |
