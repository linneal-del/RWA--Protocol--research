# Upshift — BSC 机构级收益金库

> **状态**：⬜ 待实测 ｜ **调研时间**：2026-08-18
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **本次目标链**：**BSC**（chainId 56）

## 0. 一句话结论

Upshift 是 **August Digital** 旗下的 DeFi 金库基础设施：机构/фintech 可以自定义金库（存款资产、策略、风控参数、链），普通用户在 app 里选金库 **Deposit** 存入换份额、**Redeem/Withdraw** 赎回。另有独立的 **RWA Redeem** 入口。截至 2026-08-18 全平台 Total Deposited **$464,016,716**。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Upshift（app.upshift.finance，由 **August / August Digital** 提供底层） |
| 链 | **BSC（本次目标）**；官方多链：Ethereum / Base / Avalanche / Solana / Sui / Stellar / Monad / Flare / Ink / Mezo / Citrea / Plasma / Fluent / Hyperliquid L1 |
| 产品 | 收益金库（Vault），份额型；另有 RWA 赎回通道 |
| **操作入口** | **Deposit（金库列表/存款）**：https://app.upshift.finance ｜ **RWA Redeem**：https://app.upshift.finance/rwa-redeem ｜ **Portfolio（持仓/赎回）**：https://app.upshift.finance/portfolio ｜ Points：https://app.upshift.finance/points ｜ Leaderboard：https://app.upshift.finance/leaderboard ｜ Referrals：https://app.upshift.finance/referrals |
| 金库页 URL 格式 | `https://app.upshift.finance/pools/{chainId}/{vault合约地址}` — **BSC 的 chainId = 56** |
| Supply Coins | 按金库而定（稳定币为主，如 AUSD / USDC） |
| Coins Integrated | 金库份额代币（如 earnAUSD），带 Share Price |
| 平台规模 | Total Deposited **$464,016,716**（2026-08-18 UI）｜ 累计 500M+ 存款、40+ 金库（2025-04 起）、60k+ 存款人、$50B 交易量 |
| 支持钱包 | EVM / Solana / Stellar 三类，另有 Institutional Wallets、WalletConnect、Coinbase Wallet |
| 审计 | https://docs.upshift.finance/architecture/smart-contract-audits |
| 文档 | https://docs.augustdigital.io ｜ Twitter：`upshift_fi` |
| 接入情况 | 待定 |

⚠️ **地域限制**：app 明示 *Restricted Jurisdiction* —— 部分司法辖区零售用户不可访问。实测前先确认能否正常连钱包。

## 2. 操作清单（用户去做交易 + 截图）

| # | 操作 | 入口路径 | 截图要点 |
|---|------|---------|---------|
| 0 | 切到 **BSC** 网络，筛出 BSC 金库 | app 首页 → 链筛选 | 金库名、TVL、APY、Share Price、金库地址 |
| 1 | **Deposit**（小额） | 选 BSC 金库 → Deposit | 存入资产/数量、收到份额、Share Price |
| 2 | **Withdraw / Redeem** | Portfolio → 该金库 → 赎回 | 收回数量、是否排队/cooldown |
| 3 | **RWA Redeem**（如可用） | /rwa-redeem | 页面有无可赎回标的 |

### 2.1 ✅ 实测交易（只记哈希+截图，解析留给解析同学）

| # | 时间 | 操作（UI 视角） | 链 | tx hash | 截图 |
|:---:|------|---------------|---|---------|------|
| ⬜ | | 待实测 | | | |

### 2.2 操作覆盖（页面可点击交易）

| 交易类型 | 已测 | 哈希 |
|---------|:---:|------|
| Deposit（存入金库） | ⬜ | — |
| Withdraw / Redeem | ⬜ | — |
| RWA Redeem | ⬜ | —（需确认 BSC 上是否有标的） |
| approve（授权，前置） | ⬜ | — |

**页面实际导航**：Deposit / RWA Redeem / Portfolio / Points / Leaderboard / Referrals（后三个非交易类）。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | BSC 上有哪几个金库、地址各是什么 | app 切 BSC 后看 `/pools/56/0x…` |
| 2 | 赎回是即时还是排队（有无 cooldown） | 实测 |
| 3 | 地域限制是否影响本地访问 | 浏览器打开确认 |
| 4 | RWA Redeem 与普通 Redeem 的区别 | UI / 文档 |
