# MarginFi — Solana 借贷池

> **状态**：🟡 Supply/Withdraw + Stake/Instant Unstake 已实测（2026-08-14 ~ 08-16）；**Borrow/Repay 页面无入口**
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图；**链上解析由解析同学做，本页不做深度解析**
> ⚠️ app.marginfi.com 已显示迁移到 Project 0（app.0.xyz），本次实测入口以截图为准（截图待补）

## 0. 一句话结论

marginfi 是 Solana 借贷协议。**⚠️ 官网已挂迁移公告指向 Project 0（app.0.xyz）**——本周操作前先打开两个入口看哪个还能 supply/withdraw，**以能出交易的为准**，并在截图里记录入口 URL。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | marginfi（→ Project 0） |
| 链 | **Solana** |
| 产品 | 借贷池（supply / withdraw；选 1 个主流资产如 USDC） |
| 网页入口 | https://app.marginfi.com （迁移公告）→ https://app.0.xyz |
| Supply Coins | 待确认（USDC 等） |
| Coins Integrated | 份额凭证（bank token ⚠️ 实测确认） |
| TVL / APY | 待 UI 截图 |
| 池子类别 | 活期借贷 |
| 主合约 | marginfi program ⚠️ 待链上确认（候选 `MFv2hWf31Z2WtzWX6o678Tgitm2n12m7W1ve9GxTyazn`，以交易内 program 为准） |

## 2. 本周操作清单（用户去做交易 + 截图）

| # | 操作 | 入口路径 | 截图要点 |
|---|------|---------|---------|
| 0 | 确认入口 | app.marginfi.com vs app.0.xyz | 哪个能操作、URL |
| 1 | **Supply**（小额 USDC） | Earn / Supply | 资产、数量、APY |
| 2 | **Withdraw**（全撤） | Withdraw | 收回数量 |

### 2.1 ✅ 实测交易（2026-08-14，UTC；只记哈希+截图，解析留给解析同学）

| # | 时间 | 操作（UI 视角） | 交易签名 |
|:---:|------|---------------|---------|
| # | 时间 | 操作（UI 视角） | 交易签名 | 截图 |
|:---:|------|---------------|---------|------|
| 1 | 08-14 05:36:50 | **Supply**（USDC） | `24FEn81SV2o84U4neRRCwdatpZRRV7b8M1tQCtQd4dxJJjfwrsRpEaoXU33F7ktvEfaBEBAPUGLJUhY2izrrs75G` | ⬜ 未提供 |
| 2 | 08-14 05:37:45 | **Withdraw** | `59AKZXhdFyb5Xrb1sqrKcxLXZy2G21RFSrgAz2eKJveFEkF46Laup677dQCTJamJnZ2CPBR5wYk1Dzv58zaPUSob` | ⬜ 未提供 |
| 3 | 08-16 | **Stake / Mint LST**（Stake SOL 页，LST POOL 5.18% APY，0.2 SOL ≈$15.02 → 0.1301 LST ≈$15.00，预估年化 0.0104 SOL） | `4bpVzJzHzmicwdvZ1qNpSmqDwTURfTCgBjDPx3ap265P9gZta3xGwiwRkTv2Po4QaWimLs9zxgsriJpZmnwm64cn` | [截图](截图/MarginFi-LST-stake-20260816.png) |
| 4 | 08-16 | **Instant Unstake**（0.1 LST ≈$11.55 → 0.1537 SOL ≈$11.54，Rate 1 LST = 1.537 SOL，Price impact 0.17%，Max slippage 1.00%） | `4gknTGwA5JdA65ichSdwNQyftb6q3BschXKKyjtmCXqwm8ZqaRharVXvvGjh5keH3fiXaUGjNCqgFdXRFuYcJh1h` | [截图](截图/MarginFi-LST-instantunstake-20260816.png) |

### 2.1.1 📷 LST Pool 页面结构（2026-08-22 截图确认）

截图 `MarginFi-unstake延迟档-20260822.png`：

| 项 | 值 |
|---|---|
| 页签 | **STAKE / UNSTAKE / DEPOSIT** 三个 |
| LST Pool | APY **4.92%** ｜ TOTAL STAKED **97.80K SOL ($9.16M)** ｜ 30D AVG APY 5.25% ｜ YOUR STAKE 0.04634 SOL |
| 🔴 **INSTANT UNSTAKE 开关** | **Off 时**：*receive SOL in 1-2 days at the epoch boundary*（延迟档）；On 时即时到账 |
| 赎回参数 | RATE **1 LST = 1.5382 SOL** ｜ TIME TO UNSTAKE **~14H 52M** ｜ **UNSTAKE FEE 0.10%** |

🔴 **同一个 UNSTAKE 按钮、一个开关决定两种链上行为** —— 即时档 vs 等 epoch，解析侧要按链上指令区分，不能只看"用户点了 Unstake"。

### 2.2 操作覆盖（页面可点击交易）

| 交易类型 | 已测 | 哈希 |
|---------|:---:|------|
| Supply | ✅ | `24FEn8…`（无截图） |
| Withdraw | ✅ | `59AKZX…`（无截图） |
| Stake / Mint LST | ✅ | `4bpVzJ…` |
| Instant Unstake | ✅ | `4gknTG…` |
| Unstake（1–2 days，延迟档） | 🟡 | **页面已确认存在**（截图 `MarginFi-unstake延迟档-20260822.png`）：UNSTAKE 页有 **INSTANT UNSTAKE 开关**，关闭即为「1–2 天后按 epoch 边界到账」；**交易哈希待补** |
| Deposit（LST Pool 第三个 tab） | ⬜ | —（截图确认存在 STAKE / UNSTAKE / **DEPOSIT** 三个 tab，未测） |
| **Borrow / Repay** | ❌ | **页面上没有该入口，无法提供哈希**（2026-08-16 用户实地确认；marginfi 借贷已迁移 Project 0） |

每笔操作后把 **Solana 签名** 和截图一起给我。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | 旧 app 是否还能出交易？迁移后份额是否互通 | 浏览器实测 |
| 2 | 份额凭证（bank token）mint 地址、汇率机制（NAV 累积？） | 交易内 token 账户 |
| 3 | marginfi program 地址 | 交易内确认 |
