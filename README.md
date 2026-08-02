# RWA Protocol Research

Binance Web3 Wallet **RWA 理财**项目的协议调研库 —— 把「背景资料 + 协议清单 + 逐协议调研」整合在一个仓库里，供数据中台侧（解析 / NAV / PNL）与产品侧（详情页 Underlying / Yield Source / Risk / Compliance 文案）共用。

- **维护人**：Linnea（从 Scott 接管 RWA 数据对接）
- **本轮范围**：**「已确认真实解析」的 7 个协议**（2026-07-31 收敛）
- **上线目标**：2026 年 8 月底完成 RWA 数据对接

## 目录结构

```
├── 00-背景/                        # 读这里知道"为什么做、要产出什么"
│   ├── 00-新手入门-通俗版.md         ← ⭐ 零基础先看这篇
│   ├── 01-项目背景与目标.md
│   ├── 02-PRD要点-数据侧视角.md      ← ⭐ 数据侧必读
│   ├── 03-2026-07-30-需求对齐会记录.md
│   ├── 04-数据对接待办与依赖.md
│   └── 05-协议调研模版.md            ← 新增协议时复制这个
├── 01-协议清单/
│   ├── 协议清单总览.md              ← ⭐ 7 个协议的入口 + 冲突汇总 + 优先级
│   ├── 8月待冲刺RWA协议列表.md       # 模版来源（Scott）
│   └── [Master] RWA Season - 产品信息.csv
├── 02-协议调研/                     # 本轮 7 篇
│   ├── Ember.md  Re.md  DMZ-RWAlpha.md  Nest-Credit.md
│   └── Unitas.md  Ondo.md  OpenTrade.md
├── 03-参考/
│   ├── 已确认合约地址与链上实测.md      ← ⭐ **给中台的交付物**（实测确认，非文档推断）
│   ├── 实操清单-去哪里操作与需要提供什么.md  ← 要动手的看这篇（含安全提醒）
│   ├── 需要提供的交易Hash清单.md      ← 还缺的：DMZ 派息 / Ondo 申购
│   ├── 组合层风险与交叉依赖.md
│   ├── Plume-生态背景.md
│   └── 调研方法与信息源.md
└── 99-暂不调研/                     # 待确认组 9 篇，已完成未删除，进排期时直接取用
```

## 从哪开始读

| 你是谁 / 想干什么 | 先看 |
|-----------------|------|
| **第一次接触 RWA / 链上数据** | [00-背景/00-新手入门-通俗版.md](00-背景/00-新手入门-通俗版.md) —— 不需要任何前置知识 |
| 想快速知道每个协议是什么 | [01-协议清单/协议清单总览.md](01-协议清单/协议清单总览.md) |
| 中台 / 解析同学 | [00-背景/02-PRD要点-数据侧视角.md](00-背景/02-PRD要点-数据侧视角.md) + 各协议文档第 6 章 |
| 产品 / 风控同学 | [03-参考/组合层风险与交叉依赖.md](03-参考/组合层风险与交叉依赖.md) + 各协议文档第 3/4/7/8 章 |

## 协议索引（7 个）

| 协议 | 产品 | 链 | 类别 | 解析类型 | 当前最大卡点 | 文档 |
|------|------|----|------|---------|------------|------|
| Ember (Bitwise) | RWA Multiply Vault | ETH | Private Credit | A | 拿不到 PPLUS 合约地址（官网 403） | [Ember.md](02-协议调研/Ember.md) |
| Re | reUSD / reUSDe | ETH | Reinsurance | A | reUSDe 是**季度赎回**，标"活期"会误导 | [Re.md](02-协议调研/Re.md) |
| DMZ (RWAlpha) | rAI Stocks basket | BNB | Stocks | **C 派息** | **PNL 必须含派息**，否则显示为 0 | [DMZ-RWAlpha.md](02-协议调研/DMZ-RWAlpha.md) |
| Nest Credit | nOPAL | BNB⚠️ | Private Credit | A | 链冲突（官方只见 Plume/SOL/ETH/AVAX） | [Nest-Credit.md](02-协议调研/Nest-Credit.md) |
| Unitas | XGLD | BNB | Gold | A | 美元 PNL 会混入金价波动 | [Unitas.md](02-协议调研/Unitas.md) |
| Ondo | USDY | BNB⚠️ | TBills | **A/B 待定** | **是 USDY 还是 rUSDY**（决定要不要 NAV 曲线） | [Ondo.md](02-协议调研/Ondo.md) |
| OpenTrade | PRIME+ Vault | ETH | MMF | A | TVL=0，可能是新池子（**对我们是好事**） | [OpenTrade.md](02-协议调研/OpenTrade.md) |

> Master CSV 的 **Ember (Securitize)**、**FalconX nFXCF**、**Ondo QYLDon / JAAAon** 属待确认组，因主协议在范围内，保留在对应文档的子章节里。

## 每篇协议文档包含什么

统一模版（见 [00-背景/05-协议调研模版.md](00-背景/05-协议调研模版.md)），10 个章节：

| 章节 | 服务对象 |
|------|---------|
| 0. 一句话结论 / 1. 基础信息（Master CSV 全字段） | 所有人 |
| 2. 协议背景 / 3. 底层资产 / 4. 收益来源 | 产品侧 → 详情页 **Underlying / Yield Source Tab** |
| 5. 链上机制与凭证代币 / **6. 数据接入要点（六维度自评 + 取数口径 + 需索取清单）** | **中台解析侧（本仓库重点）** |
| 7. 风险 / 8. 合规与准入 | 产品侧 → 详情页 **Risk / Compliance Tab** |
| 9. 待确认清单（问谁 / 怎么查） | 推进用 |
| 10. 参考链接 | 复查用 |

## 当前进展（2026-08-02 更新）

### ✅ 已实测核销（4 个协议）

Linnea 提供产品页截图 + Re 合约地址后，用链上 `eth_call` 与截图交叉验证，核销了这些：

| 结论 | 说明 |
|------|------|
| **Nest nOPAL 确实在 BNB Chain** | **CSV 是对的，我错了** —— 官方 docs 的部署列表不全，**产品页才是真相** |
| **Re 两个代币地址已确认** | reUSD `0x5086bf…0c72`（供应 155M）、reUSDe `0xdDC0f8…cC5a`（供应 13.45M） |
| **Unitas 是 NAV 累积型** | 页面原文 "continuous NAV growth"，PNL 用净值差公式 |
| **Ember PPLUS = Bitwise Premium+** | 不是我推测的 OpenTrade PRIME+；curator 确认就是 Bitwise |

→ 交付物：[03-参考/已确认合约地址与链上实测.md](03-参考/已确认合约地址与链上实测.md)

### 🔴 实测又挖出四个新问题（都比原来的问题更实际）

**1. Re 不是 ERC-4626，NAV 取不到**
实测 `asset()` / `totalAssets()` / `convertToAssets()` / `previewRedeem()` **全部 revert**。我原先写的"从 `convertToAssets` 取 NAV"是错的。产品页 PRICE 栏标注 **"NAV Oracle"** → **必须向项目方要预言机地址**，这是 Re 接入的第一前置条件。
（附带好消息：两个代币指向**同一实现合约**，一套适配器覆盖两个。）

**2. Ember 申购是「每日批处理」，PRD 缺一个状态**
页面写 **Daily Processing** + **Shares Received: After processing** → 用户签完交易、钱扣了，但 share 要等到当天 00:00 才铸。这段真空期里 **balance = 0，按现有解析逻辑持仓显示为 0**。PRD 只为「赎回中」设计了中间态，**没有「申购中」**。需找 Marcus 新增。

**3. Nest 有两种赎回、两种费率**
Instant 0.15% ／ Standard 0.015%（差 10 倍）。**PRD 后台只有一个「赎回费」字段，装不下。**

**4. Master CSV 的 TVL 严重过期**
Unitas 表里写 100k、**实际 $15.99M（差约 160 倍）**；Nest 30M→$53M；Ember 3M→$5.91M。这不只是显示问题 —— **看着 10 万和实际 1600 万，排期优先级判断会完全不同**。

### 还缺的

- **DMZ 的派息 hash**（决定 PNL 能否自动化）、**Ondo 的链与代币版本**（决定要不要做 NAV 曲线）、**OpenTrade 域名确认**
- Nest / Ember / Unitas 的合约地址 —— **页面上点一下就有，再截一次图即可**

> 📌 **收敛带来的三个好消息仍然成立**：7 个全是 EVM 链、黄金只剩 Unitas、纳指 covered call 只剩 DMZ。

## 使用与维护约定

- **数字必须带日期**：TVL / APY 是快照，写就写清抓取时间
- **区分事实与推断**：公开信息可证的直接写；推断标「推断：」；冲突或缺失标 **⚠️ 待核实** 并写明核实路径
- **不编合约地址**：本轮除 Ember Earn vault 外普遍缺失，不要凭猜测填
- **解决待确认项时**：在对应文档「9. 待确认清单」勾掉并注明来源，**不删除**（保留决策轨迹）
- **新增协议**：复制模版 → 写文档 → 更新 `01-协议清单/协议清单总览.md` 与本 README 索引

## 原始来源

| 内容 | 位置 |
|------|------|
| Scott 原仓库（模版 + Master CSV + PRD 快照 + 会议记录） | https://git.toolsfdg.net/scott-l/DEFI---RWA--- |
| PRD「[PRD] RWA 理财」v81 | Confluence pageId=**594744395**（Space=WW） |
| 协议调研主表 | [Google Sheet](https://docs.google.com/spreadsheets/d/1i0Cer3oe4PPnnSJsvfDpZhsSf12RWWgQAGWPWGsRe9g/edit?gid=648039480) |
| 需求对齐会 | 2026-07-30 11:00–11:31 (UTC+8) |

> **免责说明**：本仓库调研基于 2026-07-31 前的**公开信息**（官方文档、官方公告、一线媒体、数据站）。**未读取任何 DD 文档**（DD 只在 Master 表里有文件名）。所有涉及合约地址、赎回条款、投资者准入的内容，**上线前必须以项目方书面确认与 DD 为准**。
