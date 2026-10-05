# Merkl — 多链激励分发（Ethereum / BSC / Base / Robinhood Chain）

> **状态**：✅ **已完成** —— 四链 claim 样本全覆盖 + 页面机制已确认（Merkl 是激励层，用户侧仅 Claim 上链）｜ **调研时间**：2026-08-26
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Ethereum(1) / BNB Chain(56) / Base(8453) / Robinhood Chain(4663)**

## 0. 一句话结论

Merkl 是**激励分发基础设施**（Angle Labs 出品），项目方在上面创建激励活动、按 Merkle 树把奖励分给 LP，用户到 app.merkl.xyz **claim** 领取。它**不是一个存取型协议** —— 用户侧只有一个动作：**Claim**。协议方侧另有一整套建活动的方法（createCampaign 等），两侧走**两个不同的合约**。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Merkl（Angle Labs） |
| 支持链 | **67 条**（本次覆盖 Ethereum / BNB Chain / Base / Robinhood Chain 四条） |
| **操作入口** | https://app.merkl.xyz/ |
| 官方 API | `https://api.merkl.xyz/v4/chains`（链列表）｜ `https://api.merkl.xyz/v4/campaigns?chainId={id}` |
| 活跃度参考 | Base **104 个活跃活动**（2026-08-26 API） |
| 接入情况 | 待定 |

### 已定位合约（四条链同地址）

| 角色 | 地址 | 说明 |
|------|------|------|
| **Distributor（用户 claim）** | `0x3Ef3D8bA38EBe18DB133cEc108f4D14CE00Dd9Ae` | ✅ 四条链均为**同一地址**，用户领奖走这里 |
| **CampaignCreator（项目方建活动）** | `0x8BB4C975Ff3c250e0ceEA271728547f3802B36Fd` | ERC1967Proxy，方法见 §2.3 |

**Claimed 事件 topic0**：`0xf7a40077ff7a04c7e61f6f26fb13774259ddf1b6bce9ecf26a8276cdd3992683`

## 2. 操作清单

| # | 操作 | 谁做 | 入口 |
|---|------|------|------|
| 1 | **Claim 奖励** | 普通用户 | app.merkl.xyz → 连钱包 → Claim |
| 2 | 创建激励活动 | 项目方 | app.merkl.xyz 建活动流程 |

## 2.1 📎 公开样本交易（**非本人钱包**，链上直接取样）

四条链均按 `Claimed` 事件从 Distributor 合约日志直接取得：

| 链 | chainId | 样本 tx hash | 活跃度 |
|----|:---:|-------------|--------|
| **Ethereum** | 1 | `0xe2ff48d84533d613cf82208c553e3276f94ee0fcbd604ffe10ade7b88c78561c` | 近 2000 块 **157 笔** |
| **Base** | 8453 | `0xc98370474b67ba267a076c5b5680aba161ef7b334bda000a57e9f371cb0abd6c` | 近 1000 块 **69 笔** |
| **BNB Chain** | 56 | `0x656a774a8ad9328555d5681d4f926dfa8b55d13d4e8d4a55828a064b846df2d4` | 近 2000 块 **2 笔** |
| **Robinhood Chain** | 4663 | `0xb5f444db211bd7c586ba0624d8730316ea82c0a1af3852f6e907d7f89e02ddfa` | 近 1000 块 **4 笔** |

**取样方法**（可复现）：`eth_getLogs`，`address` = `0x3Ef3D8bA…9Ae`，`topics[0]` = 上面的 Claimed 签名，扫最近 1000~2000 个区块。

🔴 **节点坑（实测踩到，务必记下）**：不同公共 RPC 对 `eth_getLogs` 的限制差异极大，**报错很容易被误读成"该链没有数据"**：

| 链 | ✅ 可用节点 | ❌ 失败节点及原因 |
|----|-----------|-----------------|
| Ethereum | `rpc.flashbots.net` | `publicnode` / `llamarpc` → **403**；`1rpc.io` → 套餐限额 |
| Base | `mainnet.base.org` | `base-rpc.publicnode.com` → **403**；`base.llamarpc.com` → 403 |
| BNB Chain | `bsc-rpc.publicnode.com` | `bsc-dataseed1~4` → **`limit exceeded`**；`drpc` → 408；`blastapi` → 429 |
| Robinhood | `rpc.mainnet.chain.robinhood.com` | — |

→ 我第一轮扫描时因为 publicnode 返回 403，一度得出"Ethereum / Base / BSC 都没有 claim"的**错误结论**，换节点后实际都很活跃。

## 2.2 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| **Claim（Ethereum）** | ⬜ | ✅ `0xe2ff48…` |
| **Claim（Base）** | ⬜ | ✅ `0xc98370…` |
| **Claim（BNB Chain）** | ⬜ | ✅ `0x656a77…` |
| **Claim（Robinhood Chain）** | ⬜ | ✅ `0xb5f444…` |
| **OperatorToggled（授权代领）** | ⬜ | ✅ `0x24f819…`（Ethereum） |
| 创建激励活动（项目方侧） | ⬜ | ✅ 见 §2.3 |

### 🔴 2026-08-26 补充：Distributor 上还有第二个用户侧操作

对 Distributor 合约做**全事件扫描**（不只筛 Claimed）后发现，除 `Claimed` 外还有两类事件：

| topic0 | 判定 | Ethereum 近 3000 块 | Base 近 3000 块 | 样本 tx |
|--------|------|:---:|:---:|---------|
| `0xf7a40077…3992683` | **Claimed（用户领奖）** | 270 次 | 248 次 | 见上表 |
| `0x42343f44…36cc95c` | 🔴 **OperatorToggled（用户授权操作员代领）** —— topics = [user, operator]，data = true/false | **309 次**（比 Claimed 还多） | 4 次 | `0x24f81909a4077521f220dcbed69e64737a3014df619ddc3c119cfd86f1562f3e` |
| `0x23aa2e4f…c34aef62e` | 协议方更新 Merkle 树（selector `0xd9c98087`，keeper 运维） | 1 次 | — | `0x30b9f7ade7f7b0010d8ca7253230362a27ada0e9b09fa595cc987b17bb6bd541` |

🔴 **`OperatorToggled` 是被遗漏的用户侧动作**：用户（样本里是个 **Gnosis Safe 多签**，走 `execTransaction`）授权某个地址代自己领奖。Ethereum 上比 Claim 还频繁。
→ **对解析同学**：① 这类交易的 `from` 是用户/多签，不是 Merkl；② 被授权的 operator 之后发起的 Claim，**受益人不是签名者** —— 归属要读 Claimed 事件里的 user 字段，不能按 tx.from 归属。

**✅ 用户侧交易类型（Claim + OperatorToggled）已覆盖，四链 Claim 样本齐全。**

## 2.3 项目方侧合约方法（`0x8BB4C975…`，Ethereum 实测）

| 方法 | 样本 tx hash | 时间 |
|------|-------------|------|
| `acceptConditions` | `0x175195c7c84368a69d3adb372d7696be1807b975ec1f16fe84b8331a68aafb7d` | 08-26 14:22 |
| `createCampaign` | `0x2bdadf353a293ec4ed20d45bf3108b35e8fe27db3e5926b2e127172b99344052` | 08-26 12:07 |
| `createCampaigns`（批量） | `0x37e908268373acaefb25dc3760fe6f91b32c00e3f40da647a3134b64e293e8c5` | 08-25 16:00 |
| `overrideCampaign` | `0x8971ddefbee03bf27b0c4af989ba99323865fbac18c3b60f0b5e82dc844d0fa5` | 08-25 23:43 |
| `reallocateCampaignRewards` | `0xc6d8cc91d714a1123ebeb0848f1d3cf2578ff915dae408418863ff5970e80802` | 08-24 14:44 |
| `setUserFeeRebate` | `0x66f0f55008105f5a193d3d6c97fc90d48617e57d6b92154b582d862123b600fb` | 08-25 08:42 |

📌 **对解析同学**：**用户领奖（Distributor）和项目方建活动（Creator）是两个合约、两类行为**，不要混在一起统计。项目方侧的这些方法属于运营动作，通常应排除在用户行为之外。

## 2.4 📷 页面结构（2026-08-26 截图确认）—— 🔴🔴 Merkl 是"激励层"，不是存取型协议

截图 `Merkl-opportunity页-20260826.png`（一个 Opportunity 详情页）：

| 项 | 内容 |
|---|---|
| 顶部导航 | **Opportunities** / **Dashboard** / More / Connect |
| 本例活动 | *Deposit USDe as collateral on USDe/USDC 91.5%* —— **底层协议是 Morpho on Base** |
| 右侧数据 | **Merkl APR 4.5%** ｜ DAILY REWARDS **$40.82K**（up to $96.26K）｜ **TVL $331.11M** |
| What you need / earn | USDe on Base → 赚 USDe on Base |
| 活动信息 | Dates **20 Aug → 27 Aug 2026** ｜ Reward chain **Base** ｜ Distribution strategy **Capped Reward Rate** ｜ APR cap 4.5% ｜ Max daily rewards $96.26K ｜ Total reward **345.205K USDe** ｜ **Campaign ID `0xfc6d…568e`** |
| 规则 | Blacklist 5（黑名单地址） |
| 辅助入口 | Simulate a deposit ｜ View campaign details ｜ Past rewards 9 ｜ 1 active / 0 upcoming / 9 past |
| **唯一行动按钮** | **Lend ↗** |

🔴🔴 **最关键的机制（链上绝对看不出来）**：页面上那个 **Lend ↗ 按钮是外链跳转**，点击后**离开 Merkl、跳到底层协议**——本例跳到 `app.morpho.org/base/variable/0x54cf9be5…/usde-usdc#market`（Morpho 的市场页）。

→ **用户为了赚 Merkl 奖励而做的存款/做市交易，链上归属的是底层协议（Morpho / Uniswap / Aerodrome…），不是 Merkl。**
→ **Merkl 自己产生的用户侧链上交易，有且只有 `Claim` 一种。**

**对解析同学的口径结论**：
1. Merkl **不是**存取型协议，不要给它建"存款/取款"交易类型
2. 用户在 Merkl 页面点 Lend 产生的那笔交易，应归到**底层协议**账下
3. Merkl 侧只需解析 **Distributor 的 Claim**（`0x3Ef3D8bA…9Ae`）
4. **Campaign ID**（如 `0xfc6d…568e`）是 Merkl 的活动标识，可用于把奖励归因到具体活动

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | ✅ **已确认**：Opportunity 页唯一行动按钮是 **Lend ↗**，且为**外链跳转到底层协议**（本例 Morpho on Base）；Merkl 自身用户操作只有 Claim | 2026-08-26 截图 |
| 2 | Claim 是否支持一次领多链/多 token | 读 `claim` 函数签名 |
| 3 | Robinhood Chain 上 Merkl 在给哪些协议发激励 | API `campaigns?chainId=4663` |
| 5 | Dashboard 页有无「授权代领 / Claim」以外的按钮（`OperatorToggled` 在前端哪里触发？） | 连钱包后截图 |
| 4 | 是否需要把项目方侧（createCampaign）也纳入接入范围 | 需求方对齐 |
