# Upshift — 机构级收益金库（⚠️ 不支持 BSC，实际可用链见 §2.4）

> **状态**：⬜ 待实测 ｜ **调研时间**：2026-08-18
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **本次目标链**：原定 **BSC** —— 🔴 **实测确认 app 不支持 BSC**（2026-08-18 截图 `Upshift-链筛选无BSC-20260818.png`），见 §2.4

## 0. 一句话结论

Upshift 是 **August Digital** 旗下的 DeFi 金库基础设施：机构/фintech 可以自定义金库（存款资产、策略、风控参数、链），普通用户在 app 里选金库 **Deposit** 存入换份额、**Redeem/Withdraw** 赎回。另有独立的 **RWA Redeem** 入口。截至 2026-08-18 全平台 Total Deposited **$464,016,716**。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Upshift（app.upshift.finance，由 **August / August Digital** 提供底层） |
| 链 | 🔴 **无 BSC**。app 链筛选实有：**Ethereum / Fluent / Stellar / Monad / HyperEVM(Display only) / Solana / Flare Mainnet / Citrea / Sui**（2026-08-18 截图确认） |
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

### 2.1 📷 页面结构（2026-08-18 截图确认）

| 位置 | 内容 |
|------|------|
| 顶部导航 | **Deposit** / **RWA Redeem** / **Portfolio** / Points / Leaderboard / Referrals |
| 钱包面板 | EVM / Solana / Stellar 三类；实测钱包 `0x9DA4…44c33c`，Network = **Ethereum** |
| Deposit 弹窗 | Deposit With（选币）→ Receive in（金库份额）；显示 **Total APY / Slippage / Rewards**；例：Axis Origin USDx，1 USDx = **0.998880** Axis Origin USDx，Total APY 18.00% |
| 金库筛选 | All vaults / Lending / DeFi Yield / Pre-deposit / Sui D…（共 3 页） |
| 金库样例 | **Axis Origin USDx** $67.35M / 18.00% Target（DeFi Yield, Pre-deposit）｜ **NEMO ETH Prime** $5.55M / 18.75% 30D APY |
| **Portfolio 页签** | **Active positions / Pending Withdrawals / History** + All chains / All tokens 筛选 |

🔴 **关键**：Portfolio 有 **Pending Withdrawals** 页签 → **赎回是排队制**（链上对应 `requestRedeem` → `claim` 两步），另有 `instantRedeem` 即时赎回通道。

### 2.2 📎 公开样本交易（**非本人钱包**，供解析同学取样用）

以下为链上扫 Upshift **Ethereum** 8 个金库合约得到的方法全集与样本哈希（本人未做交易）。

**已扫金库**：`0x3cC0D33B…32167`、`0x74aD2F78…3718b`、`0x955256B3…D8522`、`0xAD958C4c…f595F`、`0xc824A08d…8859fD`(HGETH)、`0xcd69123b…0De54`、`0xe1B4d34E…6c2e`(AGETH)

| 交易类型 | 链上方法 | 样本 tx hash |
|---------|---------|-------------|
| **Deposit（存入金库）** | `deposit` | `0xc4e86fca6c450fdc8ab05b0a6b920b4de47e80de2739cac1a670f3857624411e` |
| **Deposit（子账户）** | `depositToSubaccount` | `0x8441029649aa96d5464eb9900b5b2d94cf8d22484fc98a6bbef2aedbd0c18785` |
| **赎回-申请**（进 Pending Withdrawals） | `requestRedeem` | `0x31aa09e200d05975f5e472715f08a7ed4fac0f8d5e2db03024bcc68d6b926a64` |
| **赎回-领取**（排队后到账） | `claim` | `0x49ad91a269f3b7268308b43d4c27659b1d7aa4190b1df7f1dca9ea4b04c86978` |
| **即时赎回** | `instantRedeem` | `0x640827eeba0a17aeba2c241656371d34f35edb14f681e4ab4fdaea4c6a3f2002` |
| approve（前置授权） | `approve` | `0xf51f60088b004ef55aea40df99f8c3f13196f135face78ceee0f00bd91e78373` |
| 份额转账 | `transfer` | `0x6243232374e4157f47046b9a39ca2965e8f3cfbbb94c7253af4509c30ecca51e` |

**协议方运维方法**（非用户操作，解析时应排除）：`chargeManagementFee` / `chargePerformanceFees` / `collectFees` / `processAllClaimsByDate` / `updateTotalAssets`

### 2.3 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| Deposit | ⬜ | ✅ |
| 赎回-申请 requestRedeem | ⬜ | ✅ |
| 赎回-领取 claim | ⬜ | ✅ |
| instantRedeem | ⬜ | ✅ |
| **RWA Redeem** | ⬜ | ⬜ **待定位合约** |
| **BSC 上的金库** | ❌ | ❌ **页面无 BSC，无法提供哈希** —— 见 §2.4 |

### 2.4 🔴 BSC 不在支持范围（2026-08-18 页面确认）

app 的**链筛选下拉**完整列表（截图 `Upshift-链筛选无BSC-20260818.png`）：

**Ethereum / Fluent / Stellar / Monad / HyperEVM（标注 Display only）/ Solana / Flare Mainnet / Citrea / Sui**

🔴 **没有 BSC / BNB Smart Chain 选项** —— 即使钱包面板 Network 已切到 *BNB Smart Chain*，金库列表里也筛不出任何 BSC 金库。
→ **结论：Upshift 在 BSC 上没有可操作的金库，该链无法提供任何交易哈希。** 若需覆盖 Upshift，应改用 **Ethereum**（金库最多，8 个）。
⚠️ 注：DeFiLlama 的协议页把 `Binance` 列为支持链，与 app 实际不符，**以 app 为准**。

**Featured vaults（页面首屏，均非 BSC）**：Axis Origin USDx $67.35M / 18.00% Target ｜ NEMO ETH Prime $5.54M / 18.75% ｜ Sentora USD $46.76M / 6.88% ｜ Flare XRP Yield Vault $33.53M / 1.03%
**金库分类页签**：All vaults / Lending / DeFi Yield / Pre-deposit / **Sui DeFi** / Recently…

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | BSC 上有哪几个金库、地址各是什么 | app 切 BSC 后看 `/pools/56/0x…` |
| 2 | 赎回是即时还是排队（有无 cooldown） | 实测 |
| 3 | 地域限制是否影响本地访问 | 浏览器打开确认 |
| 4 | RWA Redeem 与普通 Redeem 的区别 | UI / 文档 |
