# 8 月待冲刺 RWA 协议列表

> **来源**：`[Master] RWA Season - 产品信息.csv`（同目录）
> **整理时间**：2026-07-31
> **说明**：数据侧需求盘点。分两块——「协议历史」记录已确认真实解析的 7 个协议基础信息；「项目管理」按数据维度追踪各协议进度。

## 一、协议历史（已确认真实解析 7 个）

> 这 7 个是我们 RWA 真实解析的协议（用户 2026-07-31 确认）。

| # | 协议 | 链 | Supply Coins | Coins Integrated | 类别 | 产品 | 池子类别 | 产品网页 |
|---|------|----|----|----|----|----|----|----|
| 1 | Ember (Bitwise) | ETH | USDC | - | Private Credit | RWA Vault（循环贷：PST、sUSDai、PRIME） | 活期 | [ember.so/earn/PPLUS](https://ember.so/earn/PPLUS) |
| 2 | Re | ETH | USDC | reUSD / reUSDe | Reinsurance | Junior & Senior | 活期 | [app.re.xyz/reusde](https://app.re.xyz/reusde) |
| 3 | DMZ | BNB | USDT | rAI | Stocks | rAI Stocks basket | 活期 | [sp.rwalpha.ai/vault/AI](https://sp.rwalpha.ai/vault/AI) |
| 4 | Nest Credit (Plume) | BNB | USDT | nOPAL | Private Credit | BlackOpal LiquidStone II | 活期 | [nest.credit/vaults/nest-opal-vault](https://www.nest.credit/vaults/nest-opal-vault) |
| 5 | Unitas | BNB | XAUT | XGLD | Gold | Unitas Gold (XGLD) | 活期 | [evm.unitas.so/xgld](https://evm.unitas.so/xgld) |
| 6 | Ondo | BNB | USDT | USDY | TBills | Ondo TBill (USDY) | 活期 | [app.ondo.finance/assets/usdy](https://app.ondo.finance/assets/usdy?tab=buy&fromToken=usdt&toToken=usdy) |
| 7 | OpenTrade | ETH | USDC | PRIME+ | Money Market Fund | PRIME+ Vault | 活期 | [primeplusvault.lovable.app](https://primeplusvault.lovable.app/) |

## 二、项目管理（数据维度盘点）

> 各协议数据维度接入状态。图例：✅ 已完成 ｜ 🚧 进行中 ｜ ⬜ 待开始 ｜ — 不适用（待逐项确认填写）。

| 协议 | 实时解析 | 交易历史解析 | 池子自发现 | APY | NAV 历史 | PNL |
|------|:---:|:---:|:---:|:---:|:---:|:---:|
| Ember (Bitwise) | | | | | | |
| Re | | | | | | |
| DMZ | | | | | | |
| Nest Credit (Plume) | ✅ | ✅ | ✅ | ✅ | | |
| Unitas | ✅ | ✅ | ✅ | ✅ | | |
| Ondo | | | | | | |
| OpenTrade | | | | | | |

## 字段说明

**协议历史**
- **Supply Coins**：用户申购时投入的代币
- **Coins Integrated**：解析接入的凭证/收据代币
- **池子类别**：活期（Flexible）/ 定期（Locked，含锁定天数）
- **产品网页**：协议对应产品的官方页面

**项目管理（数据维度）**
- **实时解析**：链上实时数据解析
- **交易历史解析**：历史交易记录解析
- **池子自发现**：池子自动发现能力
- **APY / NAV 历史 / PNL**：对应指标数据接入

> 完整原始字段（池子 TVL、收益率、POC、执行、接入情况、DD 文档、产品描述、Onboarding、预算金额等）见同目录 CSV。
