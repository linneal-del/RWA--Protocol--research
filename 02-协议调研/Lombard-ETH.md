# Lombard — LBTC（比特币流动性质押）· Ethereum

> **状态**：🟡 页面结构已确认 + 链上质押/赎回样本已取到（2026-09-01）
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Ethereum**（LBTC 另有 BSC 等多链部署）

## 0. 一句话说明

Lombard 把比特币变成能生息的资产：你把 BTC 打给它，它在以太坊上给你铸出 **LBTC**（1:1 锚定 BTC），LBTC 可以拿去 DeFi 里赚收益。

🔴 **最要紧的一点**：**存入不是以太坊交易** —— 用户是往一个**比特币地址转账**，大约 60 分钟后协议才在以太坊上铸 LBTC。所以扫以太坊**看不到用户的存入动作**，只能看到协议铸币。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Lombard Finance |
| 链 | **Ethereum**（本次）｜ LBTC 也部署在 BSC 等链 |
| **LBTC 合约（ETH）** | `0x8236a87084f8B84306f72007F36F2618A5634494` |
| LBTC 合约（BSC） | `0xecAc9C5F704e954931349Da37F60E39f515c11c1` |
| 代币 | symbol **LBTC** ｜ name **Lombard Staked Bitcoin** ｜ 🔴 **decimals = 8**（跟 BTC 一致，**不是 18**） |
| **操作入口** | https://www.lombard.finance/app/stake/ |
| 手续费接收地址 | `0x251a604e8e8f6906d60f8dedc5aaeb8cd38f4892`（见 §3 的坑） |
| 接入情况 | 待定 |

## 2. 页面结构（2026-09-01 截图确认）

**左侧导航**：Portfolio / Rewards / **LBTC** / Strategies / **Bridge** / **Swap** / $BARD
**右上**：Deposit 按钮 ｜ 网络选择器（Ethereum）｜ 钱包 `0x9DA…4c33c`

### 2.1 Deposit（存入铸 LBTC）—— 🔴 走比特币网络，不是 EVM 交易

截图 `Lombard-deposit流程-20260901.png`。整个流程 **6 步**：DEST → SOURCE → AMOUNT → ADDRESS → **SEND** → TRACK

| 项 | 内容 |
|---|---|
| 目的地 | Arrives in **Bitcoin Earn** · minted as LBTC |
| 来源 | From my own wallet or exchange |
| 金额 | 0.00020000 BTC |
| 🔴 **实际动作** | 页面给一个 **BTC 收款地址**（`bc1qa7ktcc0a9lz9lkp3ty4xdm348wrp4dzxmxrlrm`）+ 二维码，**让你从比特币钱包转账过去** |
| 手续费 | Deposit & deploy fee **0.000001 BTC** |
| 授权有效期 | 到 2026-09-01 23:30（**地址有时效**） |
| 资产路径 | BTC → LBTC → **Bitcoin Earn** |
| 目标收益 | **4.5% APY** |
| 状态 | *Watching for your BTC* — 自动检测，可关页面 |
| 到账说明 | *"In about 60 minutes it mints as LBTC and deploys into Bitcoin Earn"* |

🔴🔴 **对解析同学最重要的一条**：
1. **用户的存入交易在比特币网络上**，以太坊上查不到
2. 以太坊上那笔 LBTC 铸造是**协议方发起的**，`tx.from` 不是用户
3. 存入会**自动部署进 Bitcoin Earn**（不是单纯拿着 LBTC），资产路径是三段
4. 每个用户拿到的 BTC 收款地址**不一样且有时效**，不能当固定合约地址处理

### 2.2 Swap（USDC ↔ LBTC）

截图 `Lombard-swap-20260901.png`。这个**是以太坊交易**。

| 项 | 内容 |
|---|---|
| 交易对 | USDC → LBTC（可切换方向） |
| 实测报价 | 1 USDC = **0.00001257 LBTC**（≈$0.98） |
| 提示 | *"This trade has a high price impact"* —— 小额兑换滑点很大 |
| 本次结果 | 用户取消（*Failed to swap. User rejected the request*），**无哈希** |

### 2.3 其他入口（未测）

| 入口 | 说明 |
|------|------|
| **Strategies** | 策略页，疑似金库存取 |
| **Bridge** | LBTC 跨链 |
| **Rewards** | 领奖励 |
| **Portfolio** | 持仓查看（应该不产生交易） |
| **$BARD** | 代币页 |

## 3. 📎 公开样本交易（**非本人钱包**，链上直接取样）

| 交易类型 | 说明 | 样本 tx hash |
|---------|------|-------------|
| **赎回（LBTC → BTC）** | 用户销毁 **0.03191301 LBTC**，同时协议铸 0.0001 LBTC 手续费；selector `0x30b93d85` | `0xfdcff57b7e0d8a3e42bf20e6023bf951f5e24db659178dfaefb893c2a7ba676a` |
| **铸造（真实用户）** | 铸 **0.1037801 LBTC** 给用户 `0x1920a414…` | `0xc1f33d528da743957adcac383e4fc20a129a2e395cc49b65e2c6b0b79689b80d` |

**取样方法**（可复现）：对以太坊 RPC 调 `eth_getLogs`，`address` = LBTC 合约，`topics` = `[Transfer, 0x0]` 查铸造 / `[Transfer, null, 0x0]` 查销毁。
⚠️ **可用节点**：`https://eth.drpc.org` ✅ ｜ flashbots 和 publicnode 对 `eth_call` 读 symbol 会失败。

### 🔴 一个和 Morpho 同类的坑：手续费会被误判成用户申购

扫最近 1000 个区块的 LBTC 铸造事件，**6 笔里 4 笔是手续费**，收款方都是同一个地址 `0x251a604e8e8f6906d60f8dedc5aaeb8cd38f4892`，每笔固定 0.0001 LBTC。

→ **如果按"从 `0x0` 铸出 = 用户质押"来解析，会把协议手续费当成用户申购记录**，而且数量上手续费比真实用户还多。
→ **正确做法**：铸造事件要**排除手续费接收地址**，或者按赎回交易的上下文判断（赎回 tx 里同时有 burn 和 fee mint）。

📌 这跟仓库里 `Morpho-Base-Katana.md` 记过的坑是同一类（那边是 performance fee 被误判成申购）。

## 4. 操作覆盖

| 交易类型 | 本人实测 | 公开样本 | 说明 |
|---------|:---:|:---:|------|
| **Deposit（BTC→LBTC）** | ⬜ | ✅ `0xc1f33d…` | ⚠️ 用户侧动作在**比特币网络**，以太坊只有协议铸币 |
| **赎回（LBTC→BTC）** | ⬜ | ✅ `0xfdcff5…` | |
| **Swap（USDC↔LBTC）** | ❌ 取消了 | ⬜ 待取样 | 页面可点，是 EVM 交易 |
| Strategies | ⬜ | ⬜ | 未测 |
| Bridge | ⬜ | ⬜ | 未测 |
| Rewards（领奖励） | ⬜ | ⬜ | 未测 |

## 5. 待办

| # | 事项 | 谁做 |
|---|------|------|
| 1 | Swap 补一笔成功的（上次取消了） | 你 |
| 2 | Strategies / Bridge / Rewards 三个页面截图，看有哪些可点操作 | 你 |
| 3 | 上述三类的链上样本 | 我（拿到页面结构后去捞） |
