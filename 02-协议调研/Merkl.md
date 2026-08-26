# Merkl — 多链激励分发（Ethereum / BSC / Base / Robinhood Chain）

> **状态**：✅ **四条链的用户侧 claim 样本已全部取到**（2026-08-26）｜ **调研时间**：2026-08-26
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
| 创建激励活动（项目方侧） | ⬜ | ✅ 见 §2.3 |

**✅ 用户侧可点击的交易类型（Claim）四链已全覆盖。**

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

⚠️ **页面截图待补**：尚未实地打开 app.merkl.xyz 确认前端有哪些可点击入口（Claim 之外是否还有 Stake / Deposit 类操作、活动列表长什么样）。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | app.merkl.xyz 前端除 Claim 外还有哪些可点击操作 | 浏览器截图 |
| 2 | Claim 是否支持一次领多链/多 token | 读 `claim` 函数签名 |
| 3 | Robinhood Chain 上 Merkl 在给哪些协议发激励 | API `campaigns?chainId=4663` |
| 4 | 是否需要把项目方侧（createCampaign）也纳入接入范围 | 需求方对齐 |
