# Aerodrome —— Base 链 DEX（V2 恒定函数 AMM）

> ⚠️ **范围声明**：本协议属于 **DEX 调研**（`04-DEX调研/`），与 `02-协议调研/` 下的 RWA 协议分开存放。
> **状态**：🟡 首笔 V2 swap 已实测并链上验证（多跳路由 ETH→wBLT→AERO）；✅ add liquidity（做市）已实测并确认 LP 凭证为 ERC-20；remove liquidity / collect fees 待补
> **实测时间**：2026-08-12
> **测试钱包**：`0x9da44afe5ba28aa42301c626155ea66eb544c33c`（与 Morpho/Renzo ETH 测试同一钱包）
> **链上验证方式**：Base 链 Basescan + Base RPC（`eth_getTransactionReceipt`）
> **解析类型**：AMM 交易解析（V2 恒定函数，见 §6.3 六维度自评）

## 0. 一句话结论

Aerodrome 是 Base 链上的主流 DEX，V2 版本为恒定函数 AMM（含 stable / volatile 池型）。**首笔实测是多跳 swap（0.00001 ETH → 经 3 个 V2 池 → 0.0466 AERO），链上发现 Aerodrome Router（`0xcAF22ce3...`）会发 `UniversalRouterSwap` 聚合事件，而每个 V2 池发 `Swap(sender, to, amount0In, amount1In, amount0Out, amount1Out)`——解析侧必须按池级 Swap 事件逐跳追踪，不能只认 Router 的聚合事件。** 本报告承载链上实测结论与解析规格。

## 1. 基础信息（实测口径）

| 字段 | 值 |
|------|-----|
| 协议 | Aerodrome（app.aerodrome.finance） |
| 链 | **Base** |
| 版本 | **V2**（恒定函数 AMM，与 V3 集中流动性分开，见 [Aerodrome-V3.md](Aerodrome-V3.md)） |
| 池型 | 待补（stable / volatile） |
| 治理代币 | $AERO |
| TVL | 待补（YYYY-MM-DD UI 快照） |
| 24h Volume | 待补 |
| 产品网页 | https://aerodrome.finance |
| **操作入口** | **Swap**：https://aerodrome.finance/swap ｜ **Liquidity（做市）**：https://aerodrome.finance/liquidity ｜ **Pools 浏览**：https://aerodrome.finance/liquidity ｜ **Dashboard**：https://aerodrome.finance/dash |
| 文档 | https://aerodrome.finance/docs |
| 接入情况 | 待定 |

## 2. 协议背景

⬜ **未做**。Base 链原生 DEX，继承原 Fantom 上 Solidly fork 谱系（ve(3,3) 模型）。背景 / 团队 / 融资 / 审计待补。

## 3. 底层资产

⬜ **未做**。DEX 协议底层为流动性池（LP）里的 token 对，非生息资产。Aerodrome 池型（stable / volatile）的差异与适用场景待补。

## 4. 收益来源

| 收益腿 | 来源 |
|--------|------|
| ⬜ LP 交易手续费 | 待补（fee tier 与池型关系） |
| ⬜ $AERO 流动性激励 | 待补（gauge 机制、veAERO 投票导向） |

⚠️ DEX 协议的"收益"与 RWA 协议不同——这里是 LP 侧的手续费分成 + 代币激励，不是底层资产生息。**解析口径要和 RWA 协议区分开**。

## 5. 链上机制与凭证代币

⬜ **未做**。待补内容：
- **5.1 合约清单**：Router / PoolFactory / Gauge 等关键合约地址
- **5.2 池型机制**：stable vs volatile 的曲线差异、token 排序规则
- **5.3 LP 凭证**：LP token（ERC-20）还是 uniV3-style NFT？仓位如何表达
- **5.4 swap 路由**：单跳 / 多跳 / 跨池路由的链上事件结构

## 6. 数据接入要点

### 6.1 ✅ 实测交易全集

Linnea 在 Basescan / Aerodrome UI 做 swap / add liquidity / remove liquidity 操作后，把每笔的 tx hash + 时间 + 操作 + 链上结果填入下表：

| # | 时间 | 操作（UI 视角） | 交易类型 | tx hash | 链上结果 |
|:---:|------|---------------|---------|---------|---------|
| 1 | 2026-08-12 07:39:17 UTC | Swap 0.00001 ETH → AERO（多跳） | 🔴 **V2 多跳 swap（3 跳：ETH→WETH→wBLT→AERO）** | `0x12457f9bf19697daf7888e5b8f8ca64d5e2486792549540cab5af6cbd40c8e75` | Router `0xcAF22ce3...` 收 0.00001 ETH → 包裹 WETH → 经 3 个 V2 池逐跳兑换：<br>① 池 `0x1975dbe5...`：WETH→wBLT<br>② 池 `0x61907c8c...`：wBLT 中转<br>③ 池 `0xa601c462...`：→ AERO<br>用户最终收到 **0.046644740996467356 AERO**（`0x940181a9...`）<br>每个池发 `Swap(sender=router, to=下一跳, amount0In/Out, amount1In/Out)`，Router 最后发 `UniversalRouterSwap` 聚合事件<br>gas 628,930 ｜ status SUCCESS ｜ block 49865505 |
| 2 | 2026-08-12 07:50:19 UTC | Add Liquidity（做市） | 🔴 **add liquidity（LP 凭证 mint，ERC-20）** | `0x81f502b3061dcc7f661cf68817db7b786ee65532957bb7fe613524d1bd167ad5` | Router `0xcf77a3ba...`（**与 swap Router 不同**）<br>value = 0（无 ETH 原生币，用 ERC-20）<br>3 个 token 合约交互：<br>① AERO (`0x940181a9...`) 转出 240264208423807（~0.00024 AERO）<br>② WETH (`0x833589fc...`) 转出 100（wei）<br>③ 池合约 `0x6cdcb1c4...` 铸给用户 **2811695 LP token**（from `0x0` = mint）<br>🔴 **LP 凭证是 ERC-20（mint from 0x0），不是 NFT** —— 与 V3 的 NFT 凭证根本不同<br>gas 230,816 ｜ status SUCCESS ｜ block 49865836 ｜ 6 条 log |

### 6.2 🔴 给解析同学的规格

DEX 解析的关键坑（首笔实测已确认 + 预判待补）：

| # | 规格 | 说明 |
|:---:|------|------|
| **1** | 🔴 **sender 是 Router，不是用户** | 实测 tx 里所有 V2 池的 `Swap` 事件 `sender` 都是 `0xcAF22ce3...`（Router），用户 `0x9DA44Afe...` 只在最终 token Transfer 的 recipient 里出现。**按 sender 归属会全错**，必须读每个 Swap 事件的 `to` 字段逐跳追踪 |
| **2** | 🔴 **多跳路由要逐跳解析** | 一笔交易经过 3 个 V2 池（`0x1975dbe5...` → `0x61907c8c...` → `0xa601c462...`），中间 token（wBLT）在 Router 手里短暂持有。**只看最终到账 token 会漏掉中间跳**，影响金额归因和路由还原 |
| **3** | **Router 有聚合事件，别和池级 Swap 混** | Router 最后发 `UniversalRouterSwap(sender, recipient)`，这是聚合层事件，不含金额。**金额在池级 Swap 事件里**，不要用聚合事件算金额 |
| **4** | ✅ **LP 仓位是 ERC-20 不是 NFT** | 已实测 add liquidity：LP token 由池合约 `mint from 0x0` 铸造（ERC-20 标准），**不是 NFT**。与 V3 的 NFT 凭证根本不同。V2 LP 仓位按 `balanceOf(user)` 读取，V3 要按 tokenId 枚举 NFT |
| **5** | ⬜ **跨池路由的中间 token 追踪**（待实测） | 多跳已见雏形（WETH→wBLT→AERO），路径编码（amountIn/Out 传递）待系统化 |
| **6** | ⬜ **$AERO 激励发放的归属**（待实测） | gauge claim 事件与 LP 仓位关联，待补 |
| **7** | 🔴 **swap Router 和 liquidity Router 不是同一个** | 实测确认：swap 用 `0xcAF22ce3...`（selector `0x3593564c`），add liquidity 用 `0xcf77a3ba...`（selector `0x5a47ddc3`）。**解析侧要按 selector / Router 地址区分操作类型** |

### 6.3 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | ⬜ | 待测 | swap 余额 / LP 仓位实时读取 |
| 交易历史解析 | ⬜ | 待测 | swap / add / remove / claim 四类事件 |
| 池子自发现 | ⬜ | 待测 | PoolFactory 事件枚举 / gauge 注册表 |
| 成交量 / TVL | ⬜ | 待测 | DEX 核心指标，与 RWA 的 APY 不同维度 |
| 费率历史 | ⬜ | 待测 | swap event 里的 amountIn/amountOut 反推 |
| PNL | ⬜ | 待测 | LP 侧：手续费收益 − IL（无常损失） |

### 6.4 需向项目方 / 文档索取

1. Router 合约的 swap 事件 ABI（含路由中间步骤的字段）
2. PoolFactory 事件结构（如何枚举所有 pool）
3. Gauge 合约的激励发放机制与 claim 事件
4. stable / volatile 池型的判定字段

## 7. 风险

⬜ **未做**。DEX 协议的核心风险：无常损失（IL）/ 激励代币通胀 / 路由合约升级。待实测后补充观察到的具体风险。

## 8. 合规与准入

⬜ **未做**。DEX 通常无 KYC，非托管。具体地域限制 / 前端合规拦截待补。

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | ✅ **swap 交易里如何区分用户 vs router 的 sender** —— 已实测：sender 是 Router，用户在 `to` / recipient 字段 | Basescan 解码 swap 事件 |
| 2 | 🔴 **多跳路由的中间 token 如何在事件里逐跳追踪** —— 已见 3 跳雏形（WETH→wBLT→AERO），路径编码待系统化 | 实测更多多跳 swap 后归纳 |
| 3 | 🔴 **Router 的 `UniversalRouterSwap` 聚合事件 vs 池级 `Swap` 事件的口径区分** —— 聚合事件不含金额，金额在池级事件 | 已实测确认，待写入解析规格文档 |
| 4 | ✅ **LP 仓位是 ERC-20 还是 NFT** —— 已实测：是 ERC-20（mint from 0x0），不是 NFT | 实测 add liquidity tx `0x81f502b3...`，池合约 `0x6cdcb1c4...` mint 2811695 LP token 给用户 |
| 5 | 🔴 **多跳路由的中间 token 如何在事件里逐跳追踪** —— 已见 3 跳雏形（WETH→wBLT→AERO），路径编码待系统化 | 实测更多多跳 swap 后归纳 |
| 6 | V2 和 V3 是否共用同一个 Router | 读合约 / 文档（已实测 V2 Router `0xcAF22ce3...`，V3 待测） |
| 7 | 协议背景 / 风险 / 合规四章 | 未做，需补 |

## 10. 参考链接

- 产品页：https://aerodrome.finance
- 文档：待补
- Basescan：待补（合约地址实测后填）

## 11. 实测截图

⬜ **未落盘**。Linnea 实测后按 `references/screenshot-naming.md` 规范命名（`Aerodrome-<操作>-<YYYYMMDD>.png`），存入 `04-DEX调研/截图/`。

| 文件 | 内容 | 状态 |
|------|------|------|
| ![Aerodrome swap](截图/Aerodrome-swap-20260812.png) | **Basescan tx 详情页**：tx `0x12457f9b...c8e75` ｜ From `0x9DA44Afe...c33c` → To（Router）`0xcAF22ce3...ad7c67` ｜ Value **0.00001 ETH** ｜ gas 628,930 ｜ status **Success** ｜ block 49865505 ｜ 18 条 log（3 个 V2 池级 Swap + 多个 Transfer + Router 的 UniversalRouterSwap 聚合事件） | ✅ 已落盘 |
| ![Aerodrome add liquidity](截图/Aerodrome-swap2-20260812.png) | **Basescan tx 详情页**：tx `0x81f502b3...67ad5` ｜ From `0x9DA44Afe...c33c` → To（Liquidity Router）`0xcf77a3ba...4e43` ｜ Value **0**（无 ETH，用 ERC-20）｜ gas 230,816 ｜ status **Success** ｜ block 49865836 ｜ 6 条 log（AERO + WETH 转出 + LP token mint from 0x0）｜ 🔴 **LP 凭证为 ERC-20，不是 NFT** | ✅ 已落盘 |
