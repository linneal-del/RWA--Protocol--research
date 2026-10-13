# Aerodrome V3（Slipstream / CL）—— Base 链 DEX（集中流动性 AMM）

> ⚠️ **范围声明**：本协议属于 **DEX 调研**（`04-DEX调研/`），与 `02-协议调研/` 下的 RWA 协议分开存放。V3（官方代号 **Slipstream**，前端叫 "Concentrated"）与 V2 **共用同一前端**（aerodrome.finance），但**池型机制不同**（V3 为集中流动性、LP 凭证是 ERC-721 NFT；V2 为恒定函数、LP 凭证是 ERC-20），解析侧不能用同一套适配器，故分开建报告。V2 见 [Aerodrome.md](Aerodrome.md)。
> **状态**：🟡 已首测（建池 + 全区间铸仓位，1 笔），swap / burn / collect 未测
> **实测时间**：2026-08-14 05:25:33 UTC（北京时间 13:25）
> **测试钱包**：`0x9da44afe5ba28aa42301c626155ea66eb544c33c`
> **链上验证方式**：Base 链 RPC（`https://base-rpc.publicnode.com`，`eth_getTransactionReceipt` + `eth_call`）/ Basescan（`0x`+64 hex tx hash）
> **解析类型**：集中流动性 AMM（Slipstream = Uniswap V3 分叉 + Velodrome ve(3,3) gauge 改造），**按 tickSpacing 而非 fee 索引池**

## 0. 一句话结论

Aerodrome V3（Slipstream）是 Aerodrome 的集中流动性版本，机制接近 Uniswap V3 但**有三处解析侧的关键分叉**：① 池的唯一键是 `(token0, token1, tickSpacing)`，**不是** Uniswap 的 `(token0, token1, fee)`，`positions()` 返回的也是 `tickSpacing`；② LP 仓位是 ERC-721（`AERO-CL-POS`），要按 tokenId 读，不能读余额；③ 🔴🔴 **走 "Launch pool" 建池时，LP NFT 的 `ownerOf()` 返回的是一个每次建池新克隆的 lock 代理合约，不是用户钱包**——直接用 `ownerOf` 归属仓位会导致所有 launch 建的仓位归属不到人。本次实测（2026-08-14）已在链上完整验证以上三点。

## 1. 基础信息（实测口径）

| 字段 | 值 |
|------|-----|
| 协议 | Aerodrome V3 / Slipstream（aerodrome.finance） |
| 链 | **Base**（chainId 8453） |
| 版本 | **V3 = Slipstream（集中流动性）**，与 V2 分开，见 [Aerodrome.md](Aerodrome.md) |
| 池型 | 集中流动性，**按 tickSpacing 分档**（实测档位 200，对应 fee 3000 = 0.30%） |
| 治理代币 | $AERO（与 V2 共用同一套 ve/gauge/Voter） |
| TVL | **$131.41M**（2026-08-14，DeFiLlama `aerodrome-slipstream`，Base 单链） |
| 24h Volume | 待补（DeFiLlama dex 接口未取） |
| 产品网页 | https://aerodrome.finance |
| **操作入口** | ⚠️ **V3 与 V2 共用同一前端，无独立 URL**：**Swap**：https://aerodrome.finance/swap ｜ **Liquidity**：https://aerodrome.finance/liquidity（在此页选 pool 时区分 V2 / Concentrated 池型）｜ **Dashboard**：https://aerodrome.finance/dash ｜ **Launch pool（本次实测用的建池入口）**：⬜ 精确 URL 待确认（`aerodrome.finance/launch` 返回 SPA 兜底页，未渲染出该流程） |
| 文档 | https://aerodrome.finance/docs |
| DeFiLlama 收录时间 | 2024-04-26（`listedAt` 1714088562，可视为 Slipstream 上线时间） |
| 接入情况 | 待定 |

## 2. 协议背景

Aerodrome 是 Base 链上的头部 DEX，由 Velodrome 团队构建，采用 ve(3,3) 模型（$AERO 锁仓成 veAERO → 投票决定 gauge 排放 → LP 拿激励）。

- **Slipstream（本报告的 V3）**：2024-04 上线，把 Uniswap V3 的集中流动性引入 Aerodrome，同时保留 Aerodrome 原有的 Voter / gauge 激励体系——即**集中流动性池也能挂 gauge 吃 $AERO 排放**，这是它与纯 Uniswap V3 最大的产品差异。
- **与 V2 共存**：V2 的恒定函数池（stable / volatile）没有下线，两套池型在同一个 Voter 下竞争排放。
- 审计：DeFiLlama 未登记审计链接（`audit_links` 为空），需向项目方索取（见 §6.4）。
- 团队 / 融资：⬜ 未做（Velodrome 团队，公开融资信息待补）。

## 3. 底层资产

底层是 LP 池里的 token 对本身，无外部 RWA 资产。V3 相对 V2 的差异在于 **LP 可选价格区间（tick 区间）**，资金在区间外不参与做市。

**本次实测池的资产构成**（值得注意，是"稳定币 + 生息稳定币"组合）：

| | token0 | token1 |
|---|---|---|
| 地址 | `0x833589fcd6edb6e08f4c7c32d4f71b54bda02913` | `0xbeef0e0834849acc03f0089f01f4f1eeb06873c9` |
| symbol | USDC | steakUSDC |
| name | USD Coin | **Steakhouse Prime USDC** |
| decimals | 6 | 18 |
| 性质 | 稳定币 | **Morpho 上 Steakhouse 策展的 ERC-4626 生息金库份额** |

⚠️ **decimals 不对称（6 vs 18）**：解析侧算价格时必须做 `10^(dec0-dec1)` 修正，否则 `sqrtPriceX96` 换出来的价格会差 10^12 倍（本次实测原始价 `999997356176`，修正后才是 `0.999997`）。

## 4. 收益来源

| 收益腿 | 来源 | 实测状态 |
|--------|------|---------|
| LP 交易手续费 | 按 tickSpacing 档位收取，本次池 fee = **3000（0.30%）**；只有价格落在仓位区间内才计费 | 🟡 建池时 `tokensOwed0/1 = 0`，未产生成交，费用累计逻辑未验证 |
| $AERO 流动性激励 | Slipstream 池可挂 CLGauge 吃 $AERO 排放，由 veAERO 投票决定 | ⬜ **本次建池交易未创建 gauge**，激励腿未验证 |
| token1 自带收益 | 本池 token1 是 steakUSDC（Morpho 金库份额），**份额本身在增值** | 📌 这会让"稳定对"持续单向偏移，LP 在全区间下会被动换成 USDC |

⚠️ V3 资金效率更高（同 TVL 下做市更集中），但 **LP 侧 IL 风险也放大**。本次实测用的是**全区间**仓位（等价于 V2 的做市曲线），IL 特征与 V2 相近，不代表窄区间场景。

## 5. 链上机制与凭证代币

### 5.1 合约清单（本次实测链上取到的地址）

| 角色 | 地址 | 说明 |
|------|------|------|
| **CLFactory（本次实测）** | `0xade65c38cd4849adba595a4323a8c7ddfe89716a` | `voter()` = `0x16613524e02ad97edfef371bc883f2f5d6c480a5`（Aerodrome 官方 Voter）；`owner()` = `0xe6a41fe61e7a1996b59d508661e3f524d6a32075` |
| **CLFactory（老版 / 公开文档常见）** | `0x5e7BB104d84c7CB9B682AaC2F3d509f5F406809A` | `voter()`、`owner()` **与上面完全相同** → 两套都是官方部署 |
| **PositionManager（本次实测）** | `0xa990c6a764b73bf43cee5bb40339c3322fb9d55f` | name = `Slipstream Position NFT v1`，symbol = `AERO-CL-POS`，`factory()` = `0xade65c38…` |
| **PositionManager（老版）** | `0x827922686190790b37229fd06084350E74485b72` | `factory()` = `0x5e7bb104…` |
| 本次建的池 | `0xa11ebf7059a0a03226bf66f1cf2e07f45855b62c` | EIP-1167 最小代理，implementation = `0x942e97a4c6fdc38b4cd1c0298d37d81fdd8e5a16` |
| **Launcher（建池入口，tx 的 `to`）** | `0xb9a1094d614c70b94c2cd7b4efc3a6adc6e6f4d3` | `owner()` = `0xe6a41fe6…`（同 factory owner）→ 官方组件 |
| **Locker（LP NFT 托管）** | `0x8bf02b8da7a6091ac1326d6db2ed25214d812219` | `owner()` = `0xe6a41fe6…`，发 `LockCreated` 事件 |
| 本次 lock 实例（每次建池新克隆） | `0xe4ddf5e5077734d395b39193b508bf89f3327955` | EIP-1167，implementation = `0xe5123ac41655b1a5ca09b3a6be6c7151e67c00e0`；**`owner()` = 用户钱包** |

🔴 **两套 CLFactory / PositionManager 并存**：老地址（`0x5e7bb104…` / `0x82792268…`）是公开文档和多数第三方索引在用的；本次交易走的是**新的一套**（`0xade65c38…` / `0xa990c6a7…`）。两套共享同一个 Voter 和同一个 owner，都是官方部署。**解析侧只索引老地址会漏掉本次这类池。**

### 5.2 集中流动性机制（实测验证）

- 池的唯一键是 **`(token0, token1, tickSpacing)`**，`PoolCreated(address token0, address token1, int24 indexed tickSpacing, address pool)`——**第三个索引字段是 tickSpacing，不是 Uniswap V3 的 fee**。
- 本次池：`tickSpacing = 200`，`fee() = 3000`（0.30%）。fee 由 factory 按 tickSpacing 映射，池上可读。
- 建池时 `Initialize(sqrtPriceX96, tick)`：`sqrtPriceX96 = 79228057781537899283318961129827820`，`tick = 276324`。修正 decimals 后 ≈ `1 USDC = 0.999997 steakUSDC`，与 UI 显示的 `~$1.0` 一致。
- 本次仓位区间 **`tickLower = -887200` / `tickUpper = 887200`** → tickSpacing=200 下的**最大区间（全区间）**。"Launch pool → Guided" 引导流程默认就是全区间。

### 5.3 LP 凭证：ERC-721（已验证）

✅ **确认为 NFT**：`0xa990c6a7…` 支持 ERC-165 `0x80ac58cd`，name `Slipstream Position NFT v1` / symbol `AERO-CL-POS`。本次 tokenId = **5147057**。

`positions(tokenId)` 返回结构（实测解码）：

```
nonce, operator, token0, token1, tickSpacing, tickLower, tickUpper,
liquidity, feeGrowthInside0LastX128, feeGrowthInside1LastX128,
tokensOwed0, tokensOwed1
```

🔴 **第 5 个字段是 `tickSpacing`（本次 = 200），Uniswap V3 同位置是 `fee`**。直接套 Uniswap V3 的 ABI 解码，会把 200 当成 fee=0.02% 用，池地址也会算错。

### 5.4 🔴🔴 仓位归属：ownerOf 拿不到用户

本次建池的 NFT 归属链路（实测事件顺序）：

```
PositionManager.mint → ERC721 Transfer: 0x0 → Locker(0x8bf02b8d…)      [tokenId 5147057]
Locker             → ERC721 Transfer: Locker → lock 实例(0xe4ddf5e5…)
Locker             → LockCreated(owner=用户钱包, lock=0xe4ddf5e5…, tokenId=5147057, unlockTime=1801875917, …, 10000)
lock 实例          → OwnershipTransferred(0x0 → 用户钱包)
```

- 当前 `ownerOf(5147057)` = `0xe4ddf5e5…`（lock 代理），**不是** `0x9da44afe…`（用户）。
- 真正的归属要多跳一层：`lock 实例.owner()` = 用户钱包。
- `LockCreated` 里的 `unlockTime = 1801875917` = **2027-02-06 01:05:17 UTC**，距建池 **175.82 天**（约 6 个月锁仓）。UI 上对应 "Deposit amount 🔒 Locked"。
- 事件末位 `10000` = `0x2710`，疑为 bps 口径的 100%（锁定比例），待确认。

### 5.5 swap 路由

⬜ **未测**。本次交易不含 swap。V3 是否支持跨 V2/V3 混合路由待验证（V2 侧 swap 实测见 [Aerodrome.md](Aerodrome.md)）。

## 6. 数据接入要点

### 6.1 ✅ 实测交易全集（Base 链，UTC）

| # | 时间 | 操作（UI 视角） | 交易类型 | tx hash | 链上结果 |
|:---:|------|---------------|---------|---------|---------|
| 1 | 2026-08-14 05:25:33 | Launch pool → Guided → 创建 USDC/steakUSDC 0.3% "Concentrated 200" 池并注入 ~0.8 美元流动性 | 建池 + 铸全区间 LP 仓位 + 锁仓 | `0x042810a8cb89ae113c9a56dfcebbf90591e2addfc7fc42efe72268e04242abf8` | ✅ 成功（block 49947893，21 条 log）。调用 `Launcher.launch(...)`（selector `0x499a98fd`）→ `PoolCreated`（pool `0xa11ebf70…`，tickSpacing 200）→ `Initialize`（tick 276324，1 USDC≈1 steakUSDC）→ `Mint`（区间 −887200~887200，liquidity 800000942467，**amount0 = 0.800002 USDC + amount1 = 0.799999884935470821 steakUSDC**）→ LP NFT `tokenId 5147057` 铸给 Locker 并转入 lock 实例 `0xe4ddf5e5…`（用户为其 owner，解锁 2027-02-06）→ 多余的 `0.000000115064529179 steakUSDC` 退回用户。gas 1,559,822 @ 0.006 gwei ≈ **0.00000936 ETH** |

**本次交易账本（用户视角）**：

| 项 | 金额 |
|---|---|
| 转出 USDC | 0.800002 |
| 转出 steakUSDC | 0.8 |
| 退回 steakUSDC | 0.000000115064529179 |
| 净投入 | 0.800002 USDC + 0.799999884935470821 steakUSDC（≈ $1.6，非 UI 显示的 $0.8） |
| 拿到 | LP 仓位 NFT tokenId 5147057（被锁至 2027-02-06） |
| gas | 0.00000936 ETH |

⚠️ **UI 与链上口径不一致**：截图上 "Deposit amount ~$0.8 / Liquidity amount 0.8 USDC"，只反映**单边**；链上实际双边各扣了 ~0.8。做 PNL / 成本归集时不能信 UI 单边数字。

### 6.2 🔴 给解析同学的规格

1. 🔴🔴 **LP 仓位 owner 要穿透 lock 代理**。走 Launch pool 建的仓位，`ownerOf(tokenId)` 返回的是一次性克隆的 lock 合约（本次 `0xe4ddf5e5…`，EIP-1167，impl `0xe5123ac4…`）。归属人要再读 `lock.owner()`，或直接监听 Locker `0x8bf02b8d…` 的 `LockCreated(address indexed owner, address indexed lock, uint256 tokenId, uint32 unlockTime, …)` 建映射。**只用 ownerOf 会把所有 launch 仓位归到一堆无名代理上。**
2. 🔴 **池的唯一键是 `(token0, token1, tickSpacing)`，不是 fee**。`PoolCreated` 的第三个 indexed 字段是 `int24 tickSpacing`；`positions(tokenId)` 第 5 个返回值也是 `tickSpacing`。套 Uniswap V3 ABI 会解错。fee 需另外读 `pool.fee()`（本次 3000）。
3. 🔴 **两套 CLFactory / PositionManager 并存**，都挂在官方 Voter `0x16613524…` 下：老 `0x5e7bb104…`/`0x82792268…`，新 `0xade65c38…`/`0xa990c6a7…`。池子自发现必须**两套 factory 都订阅 `PoolCreated`**。
4. 🔴 **sender 不是用户**。建池交易的 `tx.to` 是 Launcher `0xb9a1094d…`，`Mint` 事件的 `sender` 是 PositionManager，`owner` 也是 PositionManager。用户地址只出现在 ERC-20 `Transfer` 的 from、`LockCreated.owner`、`Launch` 事件第三个 indexed 字段里。
5. **decimals 不对称必须修正**。`sqrtPriceX96` 换算价格后要乘 `10^(dec0-dec1)`。本池 USDC(6)/steakUSDC(18) 差 10^12，不修正价格会离谱。
6. **池是 EIP-1167 最小代理**（本次 impl `0x942e97a4…`）。按字节码识别池合约的方案会失效，要按 factory 事件识别。
7. **LP 净投入 ≠ UI 显示金额**，取 `Mint` 事件的 `amount0/amount1`（或 `IncreaseLiquidity`），并**扣掉退款腿**（本次 Launcher 退了 0.000000115 steakUSDC 给用户）。
8. **区间外仓位要单独标记**。全区间（±887200 对 tickSpacing 200）仓位始终在区间内；窄区间仓位价格走出区间后余额读得到但不做市，"总 TVL" 与 "活跃 TVL" 要分开算。
9. **collect 与 swap 要区分**。LP 收手续费是 `Collect` 事件，不是 `Swap`。⬜ 本次未实测，事件结构待验证。
10. **steakUSDC 这类 ERC-4626 份额代币会持续增值**，稳定对会单向偏移，PNL 里要把"份额增值"和"做市盈亏"拆开。

### 6.3 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | 🟡 | `positions(tokenId)` 读区间/流动性 + `pool.slot0()` 读现价 | 已实测跑通；但 owner 要穿透 lock 代理（§6.2-1） |
| 交易历史解析 | 🟡 | 事件：`PoolCreated` / `Initialize` / `Mint` / `IncreaseLiquidity` / `LockCreated` / `Launch` 已验证 | `Swap` / `Burn` / `Collect` 未实测 |
| 池子自发现 | 🟡 | 订阅 CLFactory `PoolCreated` | 已验证；但**必须两套 factory 都订阅** |
| 成交量 / TVL | ⬜ | 待测（需 `Swap` 事件） | 本池目前唯一 LP 就是本次仓位，`pool.liquidity()` = 800000942467 |
| 费率历史 | ⬜ | 待测 | `fee()` 静态可读（3000）；`feeGrowthInside*X128` 累计逻辑未验证 |
| PNL | ⬜ | 待测 | 需 IL + 手续费 + steakUSDC 份额增值三项拆分 |

### 6.4 需向项目方 / 文档索取

1. ⬜ 新老两套 CLFactory / PositionManager 的关系与迁移计划（老的会不会停用？前端何时切换？）
2. ⬜ "Launch pool" 流程的官方文档与合约地址表（Launcher `0xb9a1094d…` / Locker `0x8bf02b8d…` 未见于公开文档）
3. ⬜ `LockCreated` 完整字段语义（末位 `10000` 是否为 bps 锁定比例？中间两个 0 是什么？）
4. ⬜ lock 实例的解锁 / 提取接口（用户 6 个月后怎么取回 NFT）
5. ⬜ Slipstream 审计报告链接（DeFiLlama 未登记）
6. ⬜ tickSpacing → fee 的完整映射表（本次 200→3000 已验证，其余档位待补）
7. ⬜ CLGauge 在 Slipstream 池上的挂载与排放分配规则

## 7. 风险

| 风险 | 说明 | 实测观察 |
|------|------|---------|
| **锁仓风险** 🔴 | Launch pool 建的仓位被强制锁定，本次锁至 **2027-02-06**（175.8 天），期间无法撤出流动性 | ✅ 链上 `LockCreated.unlockTime` 已确认 |
| **区间外资金闲置** | 窄区间选错，资金不做市但仍承担仓位风险 | 本次是全区间，不受影响 |
| **集中流动性下 IL 放大** | 区间越窄，IL 越大 | 本次全区间，IL 特征≈V2 |
| **token1 单向增值导致被动换仓** | steakUSDC 作为生息份额持续升值，全区间做市会被动把 steakUSDC 换成 USDC | 📌 待观察 |
| **新池无深度** | 本池目前只有本次这一笔 LP（`pool.liquidity()` = 800000942467，约 $1.6），任何成交都会大幅滑点 | ✅ 已确认 |
| **合约信息不透明** | Launcher / Locker 未见于公开文档，lock 实例是最小代理，用户资产由代理托管 | ⚠️ 见 §6.4-2 |
| **tick 步进 gas 成本** | 跨多个 tick 的 swap gas 更高 | ⬜ 未测（本次建池 gas 155.98 万，已属偏高） |

## 8. 合规与准入

- **无 KYC**：全程钱包直连，未出现身份验证环节。
- **非托管（有例外）**：普通 CL 仓位是非托管的；但**走 Launch pool 建的仓位在锁定期内由 lock 代理合约托管**，用户仅持有该代理的 `owner` 权限。
- **地域限制**：⬜ 未做（前端是否有 IP 屏蔽未验证）。
- 访问入口：https://aerodrome.finance（Base 链，需自备 ETH 付 gas）。

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 | 状态 |
|---|------|------------|:---:|
| 1 | LP 仓位是否确为 NFT（ERC-721） | 实测 mint 仓位后看凭证类型 | ✅ 已确认：`AERO-CL-POS` ERC-721，tokenId 5147057 |
| 2 | V2 / V3 是否共用同一个 Router | 读合约 / 文档 | ⬜ 未验证（本次未走 Router） |
| 3 | swap 跨 tick 的 fee 如何在事件里体现 | 实测 swap 后解码 log | ⬜ 待实测 |
| 4 | 同一 token 对存在几个池 | PoolFactory 事件枚举 | 🟡 已确认按 **tickSpacing** 而非 fee 分档；完整档位表待补 |
| 5 | gauge 激励在 V3 上如何分配 | 文档 / 实测 | ⬜ 本次建池未创建 gauge |
| 6 | collect 事件与 swap 事件的区分字段 | 实测 LP 收费后看 log | ⬜ 待实测 |
| 7 | 协议背景 / 风险 / 合规四章 | — | ✅ 已补（§2 / §7 / §8） |
| 8 | 🔴 新老两套 CLFactory / PositionManager 谁是主用 | 官方文档 / 前端抓包 | ⬜ 新增，见 §6.4-1 |
| 9 | 🔴 Launch pool 的锁仓能否提前解除 / 到期怎么取回 | 读 lock 实现 `0xe5123ac4…` / 问项目方 | ⬜ 新增 |
| 10 | Launch pool 流程的前端入口精确 URL | 问 Linnea（本次操作路径） | ⬜ 新增 |
| 11 | 24h Volume 数据源 | DeFiLlama dex 接口 | ⬜ 新增 |

## 10. 参考链接

- 产品页：https://aerodrome.finance
- 文档：https://aerodrome.finance/docs
- DeFiLlama（Slipstream）：https://defillama.com/protocol/aerodrome-slipstream
- 本次交易：https://basescan.org/tx/0x042810a8cb89ae113c9a56dfcebbf90591e2addfc7fc42efe72268e04242abf8
- 本次建的池：https://basescan.org/address/0xa11ebf7059a0a03226bf66f1cf2e07f45855b62c
- PositionManager（新）：https://basescan.org/address/0xa990c6a764b73bf43cee5bb40339c3322fb9d55f
- CLFactory（新）：https://basescan.org/address/0xade65c38cd4849adba595a4323a8c7ddfe89716a
- Locker：https://basescan.org/address/0x8bf02b8da7a6091ac1326d6db2ed25214d812219
- steakUSDC（Steakhouse Prime USDC）：https://basescan.org/address/0xbeef0e0834849acc03f0089f01f4f1eeb06873c9
- Uniswap V3 核心白皮书（机制参考）：https://uniswap.org/whitepaper-v3.pdf

## 11. 实测截图

| 文件 | 内容 | 状态 |
|------|------|------|
| `截图/AerodromeV3-createpool-20260814.png` | Launch pool → Guided 确认页：USDC / steakUSDC 0.3%、Concentrated 200、Base、价格 ~$1.0（1 USDC = 1 steakUSDC）、Deposit amount 🔒 Locked ~$0.8、Liquidity amount 0.8 USDC | ✅ 已落盘，对应 §6.1 第 1 笔 |
| ⬜ | swap / burn / collect 实测截图 | 待补 |
