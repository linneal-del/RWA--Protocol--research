# RWA Protocol Research

Binance Web3 Wallet **RWA 理财**项目的协议调研库 —— 把「背景资料 + 协议清单 + 逐协议调研」整合在一个仓库里，供数据中台侧（解析 / NAV / PNL）与产品侧（详情页 Underlying / Yield Source / Risk / Compliance 文案）共用。

- **维护人**：Linnea（从 Scott 接管 RWA 数据对接）
- **首次整理**：2026-07-31
- **上线目标**：2026 年 8 月底完成 RWA 数据对接

## 目录结构

```
├── 00-背景/                        # 读这里知道"为什么做、要产出什么"
│   ├── 00-新手入门-通俗版.md         ← ⭐ 零基础先看这篇（概念、术语、三类记收益方式）
│   ├── 01-项目背景与目标.md
│   ├── 02-PRD要点-数据侧视角.md      ← ⭐ 数据侧必读（含解析类型三分法、赎回链路分析）
│   ├── 03-2026-07-30-需求对齐会记录.md
│   ├── 04-数据对接待办与依赖.md
│   └── 05-协议调研模版.md            ← 新增协议时复制这个
├── 01-协议清单/
│   ├── 协议清单总览.md              ← ⭐ 从这里进各协议文档，含冲突汇总与优先级建议
│   ├── 8月待冲刺RWA协议列表.md       # 模版来源（Scott）
│   └── [Master] RWA Season - 产品信息.csv   # 原始主表
├── 02-协议调研/                     # 16 篇，覆盖 Master 表 20 行产品
│   ├── Ember.md  Re.md  DMZ-RWAlpha.md  Nest-Credit.md
│   ├── Unitas.md  Ondo.md  OpenTrade.md
│   ├── USDai.md  OnRe.md  Asseto.md  Ethena.md
│   └── Huma.md  DigiFT.md  Midas.md  XAUE.md  Theo.md
└── 03-参考/
    ├── 组合层风险与交叉依赖.md        ← ⭐ 跨协议相关性（Ethena / 黄金 / 私人信贷集中度）
    ├── Plume-生态背景.md
    └── 调研方法与信息源.md
```

## 从哪开始读

| 你是谁 / 想干什么 | 先看 |
|-----------------|------|
| **第一次接触 RWA / 链上数据** | [00-背景/00-新手入门-通俗版.md](00-背景/00-新手入门-通俗版.md) —— 不需要任何前置知识 |
| 想快速知道每个协议是什么 | [01-协议清单/协议清单总览.md](01-协议清单/协议清单总览.md) |
| 中台 / 解析同学 | [00-背景/02-PRD要点-数据侧视角.md](00-背景/02-PRD要点-数据侧视角.md) + 各协议文档第 6 章 |
| 产品 / 风控同学 | [03-参考/组合层风险与交叉依赖.md](03-参考/组合层风险与交叉依赖.md) + 各协议文档第 3/4/7/8 章 |

## 协议索引

### ✅ 已确认真实解析（7 个）

| 协议 | 产品 | 链 | 类别 | 解析类型 | 文档 |
|------|------|----|------|---------|------|
| Ember (Bitwise) | RWA Multiply Vault | ETH | Private Credit | A | [Ember.md](02-协议调研/Ember.md) |
| Re | reUSD / reUSDe | ETH | Reinsurance | A | [Re.md](02-协议调研/Re.md) |
| DMZ (RWAlpha) | rAI Stocks basket | BNB | Stocks | **C 派息** | [DMZ-RWAlpha.md](02-协议调研/DMZ-RWAlpha.md) |
| Nest Credit | nOPAL / nFXCF | Plume 系 | Private Credit | A | [Nest-Credit.md](02-协议调研/Nest-Credit.md) |
| Unitas | XGLD | BNB | Gold | A | [Unitas.md](02-协议调研/Unitas.md) |
| Ondo | USDY / QYLDon / JAAAon | BNB | TBills / Equities | A + B | [Ondo.md](02-协议调研/Ondo.md) |
| OpenTrade | PRIME+ Vault | ETH | MMF | A | [OpenTrade.md](02-协议调研/OpenTrade.md) |

### 🟡 待确认（9 个）

| 协议 | 产品 | 链 | 关键阻塞 | 文档 |
|------|------|----|---------|------|
| USD.AI | sUSDai | ARB⚠️ | 30 天 epoch + FIFO 队列 vs「活期」 | [USDai.md](02-协议调研/USDai.md) |
| OnRe | ONyc | **Solana** | 季度赎回 + 赎回需合格投资者 | [OnRe.md](02-协议调研/OnRe.md) |
| Huma | PST | **Solana** | Solana 解析能力；定期 60D 口径 | [Huma.md](02-协议调研/Huma.md) |
| Theo | sthUSD | BNB | 状态自相矛盾；申购币种是 thUSD | [Theo.md](02-协议调研/Theo.md) |
| Midas | mF-ONE | ETH⚠️ | 链冲突；NAV 更新频率 | [Midas.md](02-协议调研/Midas.md) |
| XAUE | XAUE | ETH⚠️ | 白名单铸造，零售只能二级买 | [XAUE.md](02-协议调研/XAUE.md) |
| DigiFT | pEAK | ETH⚠️ | 仅合格投资者；无稳定 APY | [DigiFT.md](02-协议调研/DigiFT.md) |
| Asseto | NGI+ | ETH⚠️ | 私募股权，季度估值 vs 天级 NAV | [Asseto.md](02-协议调研/Asseto.md) |
| Ethena | ? | ? | **产品指向未定义** | [Ethena.md](02-协议调研/Ethena.md) |

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

## 三个最重要的结论

调研 20 个产品后，最值得先处理的三件事：

**1. 🔴「池子类别」口径系统性错误 —— 6 个协议标「活期」但实际不是**
USD.AI（30 天 epoch + FIFO 可跨月）、Re reUSDe（季度）、OnRe（季度 + 30 天通知期）、Theo（约两周队列）、Asseto（私募股权）、DigiFT（对冲基金）。
→ 直接影响 PRD 4.5.1 时间线组件与 4.6.2 赎回流程。**找 Marcus 对齐口径。**

**2. 🔴 DMZ rAI 的 PNL 公式必须单独处理**
它是唯一的**现金流派息型**产品（每周派 USDT），`PNL = NAV 变化 + 累计派息`。沿用通用的 NAV 差分公式会**严重低估收益**，且这类 bug 通常上线后才被发现。
→ **和 Jackson / Calvin 单独对齐一次。**

**3. 🔴 两个协议在 Solana 上，与「EVM 为主」基线冲突**
OnRe ONyc、Huma PST 都是 SPL 代币。
→ **先问 Jeff / Johnson 中台是否具备 Solana 解析能力**，答案是"否"就能直接砍掉两个协议的调研与对接投入。

更多跨协议结论见 [03-参考/组合层风险与交叉依赖.md](03-参考/组合层风险与交叉依赖.md)。

## 使用与维护约定

- **数字必须带日期**：TVL / APY 是快照，写就写清抓取时间
- **区分事实与推断**：公开信息可证的直接写；推断标「推断：」；冲突或缺失标 **⚠️ 待核实** 并写明核实路径
- **不编合约地址**：本轮除少数几个（OnRe ONyc mint、Ember Earn vault）外，**合约地址普遍待项目方提供**，不要凭猜测填
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
