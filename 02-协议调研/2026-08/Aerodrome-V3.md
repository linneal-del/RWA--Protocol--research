# Aerodrome V3 —— Base 链 DEX（集中流动性 AMM）

> ⚠️ **范围声明**：本协议属于 **DEX 调研**（`04-DEX调研/`），与 `02-协议调研/` 下的 RWA 协议分开存放。V3 与 V2 **共用同一前端**（aerodrome.finance），但**池型机制不同**（V3 为集中流动性、LP 凭证是 NFT；V2 为恒定函数、LP 凭证是 ERC-20），解析侧不能用同一套适配器，故分开建报告。V2 见 [Aerodrome.md](Aerodrome.md)。
> **状态**：⬜ 新建骨架，待 Linnea 实测补素材
> **实测时间**：待补
> **测试钱包**：待补
> **链上验证方式**：Base 链 Basescan / Blockscout（`0x`+64 hex tx hash）
> **解析类型**：待定（集中流动性 AMM，机制接近 Uniswap V3）

## 0. 一句话结论

⬜ 待补。Aerodrome V3 是 Aerodrome 的集中流动性版本（机制接近 Uniswap V3：LP 在选定价格区间提供流动性，仓位以 NFT 表达）。与 V2 的恒定函数曲线**机制根本不同**，必须当成两个协议解析。本报告承载链上实测结论与解析规格，待实测后填入。

## 1. 基础信息（实测口径）

| 字段 | 值 |
|------|-----|
| 协议 | Aerodrome V3（app.aerodrome.finance） |
| 链 | **Base** |
| 版本 | **V3**（集中流动性，与 V2 分开，见 [Aerodrome.md](Aerodrome.md)） |
| 池型 | 待补（集中流动性，多 fee tier） |
| 治理代币 | $AERO（与 V2 共用） |
| TVL | 待补（YYYY-MM-DD UI 快照） |
| 24h Volume | 待补 |
| 产品网页 | https://aerodrome.finance |
| **操作入口** | ⚠️ **V3 与 V2 共用同一前端，无独立 URL**：**Swap**：https://aerodrome.finance/swap ｜ **Liquidity**：https://aerodrome.finance/liquidity（在此页选 pool 时区分 V2/V3 池型）｜ **Dashboard**：https://aerodrome.finance/dash |
| 文档 | https://aerodrome.finance/docs |
| 接入情况 | 待定 |

## 2. 协议背景

⬜ **未做**。Aerodrome V3 在 V2 基础上引入集中流动性（参考 Uniswap V3 设计），提升资金效率。背景 / 推出时间 / 与 V2 共存关系待补。

## 3. 底层资产

⬜ **未做**。同 V2，底层为 LP 池里的 token 对。V3 的差异在于** LP 可选价格区间**，资金在区间外不参与做市。区间选择策略待补。

## 4. 收益来源

| 收益腿 | 来源 |
|--------|------|
| ⬜ LP 交易手续费 | 待补（fee tier 多档可选，与 V2 单一 fee 不同） |
| ⬜ $AERO 流动性激励 | 待补（gauge 机制在 V3 上的适配方式） |

⚠️ V3 的资金效率更高（同 TVL 下做市更集中），但**LP 侧的 IL（无常损失）风险也更高**。解析口径要和 V2 区分。

## 5. 链上机制与凭证代币

⬜ **未做**。待补内容：
- **5.1 合约清单**：V3 Router / PoolFactory / PositionManager / Gauge 等关键合约地址
- **5.2 集中流动性机制**：tick 区间、价格区间上下限、single-side liquidity
- **5.3 LP 凭证**：🔴 **预计为 NFT（ERC-721）**，每笔仓位一个 tokenId，与 V2 的 ERC-20 LP token **完全不同**
- **5.4 swap 路由**：V3 是否支持跨 V2/V3 池的混合路由

🔴 **V2/V3 凭证差异是解析侧最大坑**：V2 用余额读 LP 仓位，V3 要枚举 NFT tokenId 读区间。**不能共用一套适配器。**

## 6. 数据接入要点

### 6.1 ✅ 实测交易全集

⬜ 待实测。Linnea 在 Basescan / Aerodrome V3 UI 做 swap / mint 仓位 / burn 仓位 / collect 费用操作后，把每笔的 tx hash + 时间 + 操作 + 链上结果填入下表：

| # | 时间 | 操作（UI 视角） | 交易类型 | tx hash | 链上结果 |
|:---:|------|---------------|---------|---------|---------|
| ⬜ | | | | | |

### 6.2 🔴 给解析同学的规格

⬜ 待补。V3 解析的关键坑预计有：
- 🔴 **LP 仓位是 NFT，不是余额**——要按 `Transfer` 事件枚举 tokenId，按 `PositionManager` 的 `tokenId → owner` 读仓位
- **swap 事件的 tick 变动**（V3 的 swap 会跨越多个 tick，step-by-step 的 fee 累计）
- **fee tier 多档识别**（同一对可能存在 0.01% / 0.05% / 0.3% / 1% 多个池，需按 fee + tickSpacing 区分）
- **collect 与 swap 的区分**（LP 收手续费是 collect 事件，不是 swap）
- **区间外仓位的价值评估**（资金不在当前价格做市，余额读得到但实质不活跃）

### 6.3 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | ⬜ | 待测 | LP 仓位按 NFT tokenId 读取，与 V2 不同 |
| 交易历史解析 | ⬜ | 待测 | swap / mint / burn / collect 四类事件 |
| 池子自发现 | ⬜ | 待测 | PoolFactory 事件 + fee tier 枚举 |
| 成交量 / TVL | ⬜ | 待测 | DEX 核心指标，V3 的"活跃 TVL"与"总 TVL"要区分 |
| 费率历史 | ⬜ | 待测 | V3 的 fee 按区间累计，比 V2 复杂 |
| PNL | ⬜ | 待测 | LP 侧：区间内手续费 − IL，区间外无收益 |

### 6.4 需向项目方 / 文档索取

1. V3 Router 合约 ABI（swap 事件含 tick 步进字段吗）
2. PositionManager 合约的 `mint` / `burn` / `collect` 事件结构
3. NFT tokenId 与 pool / tickLower / tickUpper 的映射字段
4. V3 是否与 V2 共用 gauge 激励池
5. fee tier 与 tickSpacing 的取值清单

## 7. 风险

⬜ **未做**。V3 核心风险：**区间外资金闲置**（LP 选错区间导致不做市但仍承担仓位风险）/ **tick 步进的 gas 成本**（跨多个 tick 的 swap gas 更高）/ **集中流动性下 IL 放大**。待实测后补充。

## 8. 合规与准入

⬜ **未做**。同 V2，通常无 KYC，非托管。

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | 🔴 LP 仓位是否确为 NFT（ERC-721） | 实测 mint 仓位后看收到的凭证类型 |
| 2 | V2 / V3 是否共用同一个 Router | 读合约 / 文档 |
| 3 | swap 跨 tick 的 fee 如何在事件里体现 | 实测 swap 后解码 log |
| 4 | 同一 token 对存在几个 fee tier 池 | PoolFactory 事件枚举 |
| 5 | gauge 激励在 V3 上如何分配（按仓位区间加权？） | 文档 / 实测 |
| 6 | collect 事件与 swap 事件的区分字段 | 实测 LP 收费后看 log |
| 7 | 协议背景 / 风险 / 合规四章 | 未做，需补 |

## 10. 参考链接

- 产品页：https://aerodrome.finance
- 文档：待补
- Basescan：待补（合约地址实测后填）
- Uniswap V3 核心白皮书（机制参考）：https://uniswap.org/whitepaper-v3.pdf

## 11. 实测截图

⬜ **未落盘**。Linnea 实测后按 `references/screenshot-naming.md` 规范命名（`AerodromeV3-<操作>-<YYYYMMDD>.png`），存入 `04-DEX调研/截图/`。

| 文件 | 内容 | 状态 |
|------|------|------|
| ⬜ | 待实测 | — |
