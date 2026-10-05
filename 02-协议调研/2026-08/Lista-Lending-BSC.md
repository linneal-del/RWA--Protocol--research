# Lista Lending — BSC 借贷（SPYB / USD1 市场）

> **状态**：✅ **页面可点击交易类型已全覆盖**（存抵押 / 借款 / 还款 / 提取抵押 + Supply/Withdraw 公开样本）｜ **调研时间**：2026-08-24
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**BSC（BNB Chain）** ｜ **测试钱包**：`0x9DA4…c33c`

## 0. 一句话结论

Lista Lending（代号 **moolah**）是 BNB Chain 上的 P2P 借贷协议，**经链上比对确认是 Morpho Blue 的分叉** —— 函数选择器与 Morpho Blue 逐一对应。本次实测市场 **SPYB / USD1**：存 **SPYB**（代币化标普 500 ETF）作抵押，借出 **USD1**（World Liberty Financial USD）。核心六动作：Supply / Withdraw（出借侧）+ SupplyCollateral / Borrow / Repay / WithdrawCollateral（借款侧）。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Lista Lending（ListaDAO 旗下，代码库代号 **moolah**） |
| 链 | **BSC**（另有 Ethereum 部署） |
| 本次市场 | **SPYB / USD1**，marketId `0x2f2dcbb5ad16f1a99a894108f7d1851b1f9f40bf386c1d3dbd152ae18187f509` |
| **操作入口** | https://lista.org/lending/market/bsc/0x2f2dcbb5ad16f1a99a894108f7d1851b1f9f40bf386c1d3dbd152ae18187f509?tab=market |
| **Moolah 核心合约（BSC）** | `0x8F73b65B4caAf64FBA2aF91cC5D4a2A1318E5D8C` —— 六类操作**全部打到这一个合约** |
| Moolah 核心合约（Ethereum） | `0xf820fB4680712CD7263a0D3D024D5b5aEA82Fd70` |
| **SPYB（抵押品）** | `0x7138b48df7d98d7e3cc221bfe7192d0a178182d8` ｜ symbol `SPYB` / name `SPY` / **decimals 18** ｜ 代币化标普 500 ETF |
| **USD1（借出资产）** | `0x8d0D000Ee44948FC98c9B98A4FA4921476f08B0d` ｜ World Liberty Financial USD ｜ **decimals 18** |
| 市场列表 API | `https://api.lista.org/api/moolah/borrow/marketList?page=1&pageSize=1000&chain=bsc`（BSC 共 **200** 个市场） |
| 审计 | https://github.com/lista-dao/moolah/tree/master/docs/audits |
| 市场数据 | 总供应 **987.11K USD1**（$986,870）｜ 剩余可借 **91.36K USD1** ｜ 借贷利率 **−0.87%**（带激励后为负）｜ 原始借款利率 **3.38%** ｜ **清算 LTV 85.00%** |
| 接入情况 | 待定 |

## 2. 页面结构（2026-08-24 截图确认）

顶部导航：流动性质押 / **借贷** / DEX / lisAster / 股票 & 债券 / DAO / 更多

市场页右侧操作面板分**两个页签**：

| 页签 | 含内容 |
|------|--------|
| **存入/借款** | 存入抵押物（SPYB）+ 借 USD1 —— 可一次同时做，也可只做一边 |
| **还款/提取** | 还款（USD1）+ 提取抵押物（SPYB）—— 同上 |

面板实时显示：我的抵押物金额 / 我的借款金额 / 借贷利率 / **清算价格比（SPYB/USD1）** / **贷款价值比 vs 清算时贷款价值比**，还有一个「**绑定 Bot**」入口。

🔴 **链上看不出的三点**：
1. **一个页签 = 两个动作**：「存入/借款」面板里存抵押和借款是两个输入框，用户可能只填一个 → 链上就是一笔；也可能两个都填 → 链上是两笔独立交易。**UI 的"一次操作"和链上笔数不是一一对应**
2. **借贷利率显示为 −0.87%**（负利率，因为有激励补贴），但**原始借款利率是 3.38%** —— 解析取哪个口径要跟产品对齐
3. **有「绑定 Bot」功能**（自动管理仓位/防清算），链上可能出现**非用户本人签名**的仓位操作

## 2.1 ✅ 实测交易（2026-08-24，UTC；只记哈希+截图，解析留给解析同学）

全部打到 Moolah 核心合约 `0x8F73b65B…5D8C`。

| # | 时间 | 操作 | selector | tx hash | 金额 | 截图 |
|:---:|------|------|---------|---------|------|------|
| 1 | 10:53:55 | **存入抵押物** | `0x238d6579` | `0x9736c37b8c696a38e014643f567dcfa270f63371c80615855b8903c72625ce7e` | SPYB 0.001636351695441049 | [截图](截图/Lista-存抵押物-20260824.png) |
| 2 | 10:56:10 | **存入抵押物**（第 2 笔） | `0x238d6579` | `0xeb4f5a8a1196ef1a70e9aecf68c98e726840b5d733a0d803321d2c88e9a0c457` | SPYB 0.004909055086323147 | 同上 |
| 3 | 10:57:04 | **存入抵押物**（第 3 笔） | `0x238d6579` | `0x6f489eab42897025a82de5a281360deae476dd80771e01e5d99f43f4bc648015` | SPYB 0.027445640064952585 | 同上 |
| 4 | 10:57:39 | **借款** | `0x50d8cd4b` | `0xa43d5b8ec1336cbd1ec9bea47b6a979fcafccfd6b6fd35c9f86b550d62206345` | 收到 **USD1 16.0** | [截图](截图/Lista-借款-20260824.png) |
| 5 | 10:58:42 | **提取抵押物** | `0x8720316d` | `0x362e70da15836a66ac7d84cac8f32ba04e92ed4da4ab8392fcc5d7120cd7eadc` | 收回 SPYB 0.0088650064142113 | [截图](截图/Lista-还款提取-20260824.png) |
| 6 | 11:01:24 | **还款（全部还款）** | `0x20b76e81` | `0x932a5cfdea952a21cd318a5cca1e7f9e18ba5c4ad9e6765fc161f48150892e4f` | 还 **USD1 16.000003863696268** | 同上 |

📌 **注意**：第 5 笔「提取抵押物」发生在还款**之前** —— 因为当时 LTV 尚在安全线内，可以部分取回。全部还款后才能取回剩余抵押。

## 2.2 📎 公开样本（**非本人钱包**）—— 补齐出借侧

本人未做出借侧（Supply/Withdraw），以下为同一合约上的公开样本，按 Morpho Blue 事件签名从 BSC 日志直接取得：

| 交易类型 | 事件 topic0 | 样本 tx hash |
|---------|------------|-------------|
| **Supply（存 USD1 赚息）** | `0xedf88704…30fe0` | `0x54fd80ce7d51314d5bb26e0463a9bb91218e0985279dc4e7d4c30d8a2d5fc81f` |
| **Withdraw（取回出借资产）** | `0xa56fc0ad…58fbf` | `0x54fd80ce7d51314d5bb26e0463a9bb91218e0985279dc4e7d4c30d8a2d5fc81f` |
| Borrow（他人样本） | `0x57095454…42a43` | `0x1b5b024364535504ca2db9ca74b1495d2261c4f4ed915ba18aa47d279485a4cb` |
| WithdrawCollateral（他人样本） | `0xe80ebd7c…21142` | `0xeaee3edaf59b21c3edeb372bec9a83c650c68a3976bef4cb7841b400c4f4b593` |

**取样方法**（可复现）：对 BSC RPC 调 `eth_getLogs`，`address` = `0x8F73b65B…5D8C`，`topics[0]` 用上表签名，扫最近 5000 个区块即可（Supply 53 条 / Withdraw 90 条 / Borrow 4 条 / Repay 4 条 / WithdrawCollateral 5 条）。

## 2.3 🔴 selector 与 Morpho Blue 完全一致（已逐一比对）

| 操作 | Lista moolah selector | Morpho Blue 同名函数 |
|------|----------------------|---------------------|
| supplyCollateral | `0x238d6579` | ✅ 一致 |
| borrow | `0x50d8cd4b` | ✅ 一致 |
| withdrawCollateral | `0x8720316d` | ✅ 一致 |
| repay | `0x20b76e81` | ✅ 一致 |

→ **对解析同学**：可直接复用 Morpho Blue 的解析逻辑（marketId 为 32 字节、`idToMarketParams(bytes32)` 返回 `loanToken/collateralToken/oracle/irm/lltv`），只需把合约地址换成 moolah。仓库里 **Morpho.md** 已有的那套坑同样适用。

## 2.4 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| 存入抵押物 SupplyCollateral | ✅ ×3 | — |
| 借款 Borrow | ✅ | ✅ |
| 还款 Repay | ✅ | — |
| 提取抵押物 WithdrawCollateral | ✅ | ✅ |
| Supply（出借） | ⬜ | ✅ |
| Withdraw（撤出借） | ⬜ | ✅ |
| approve（前置授权） | ⬜ | 标准 ERC-20，未单独取样 |

**✅ 市场页可点击的交易类型已全覆盖。**

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | 「绑定 Bot」会以谁的身份发交易 | 页面 + 实测 |
| 2 | 借贷利率 −0.87%（含激励）与原始 3.38% 的口径差 | 产品对齐 |
| 3 | Vault（策展金库）是否为独立入口、是否 ERC-4626 | 页面确认 |
| 4 | Ethereum 侧部署是否同一套 selector | 链上比对 |
| 5 | ListaDAO 其他产品线（流动性质押 slisBNB / lisUSD CDP / DEX / 股票&债券）是否需单独调研 | 需求方对齐 |
