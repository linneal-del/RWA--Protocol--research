# Axis Origin USDx — Ethereum

> **状态**：✅ 交易类型覆盖已补全（2026-10-13）：8 类上链交易全部有 hash + 交易截图；存取已恢复（9/4 解锁赎回，Upshift 页可存入；Axis 自家页存入窗口已关）｜ 按需求方要求单独立项
> **目标链**：**Ethereum**（chainId 1）

## 交易类型覆盖（2026-10-13）

**存取现状**：9/1 的暂停已结束。Origin 金库 9/4 解锁，赎回已开放（7 天队列或即时）；Axis 自家前端 https://app.axis.to/origin 的存入窗口 7/29–8/5 已关闭（FAQ：“Can I still deposit? No”），但 Upshift 金库页仍可存入，链上 10/13 仍有新 `deposit`。

前端导航：Origin Vault（Deposit / Withdraw）/ Stake（Swap / Stake〔Stake·Unstake·Withdraw〕）/ Earn / Coordinates / Transparency。Origin 金库 4 类 + 同站 USDx/sUSDx 4 类，共 8 类上链，全部有 hash（已逐条确认成功且日志里有对应事件）。

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash |
|---|---|---|---|---|
| 1 | 存入 Origin 金库（USDT/USDC/USDx → ogUSDx） | Upshift 金库页 Deposit（Axis 页 Deposit 窗口 8/5 已关） | Upshift TokenizedVault `0xAD958C4c0c90bf0216e0f5472F074a9AB30f595F` · `deposit` | [0x01b30339f3a15f3a2e11fc665ff5ad438ec76c61bb2f10976f29b83a77cce731](https://etherscan.io/tx/0x01b30339f3a15f3a2e11fc665ff5ad438ec76c61bb2f10976f29b83a77cce731)（公开样本） |
| 2 | 申请赎回（7 天队列） | Origin Vault → Withdraw | 同上 · `requestRedeem` | [0x96490a4048e62d1658fe191f7f71c8f6126e54d9e63da848737f46a800b596d0](https://etherscan.io/tx/0x96490a4048e62d1658fe191f7f71c8f6126e54d9e63da848737f46a800b596d0)（公开样本） |
| 3 | 队列到期领取 USDx | Origin Vault → Withdraw（需连钱包） | 同上 · `claim` | [0xe593808f900d23284eaeaba1ccb4e79d103ea458d51245bc7fd2b4d3ec049e83](https://etherscan.io/tx/0xe593808f900d23284eaeaba1ccb4e79d103ea458d51245bc7fd2b4d3ec049e83)（公开样本） |
| 4 | 即时赎回（约 30bps） | Origin Vault → Withdraw（需连钱包；Earn 页标“7-day cooldown or Instant”） | 同上 · `instantRedeem` | [0x1193fdc7e9652461429e0a6034ae19b23075a19c679a825144dcc0c212a2fe03](https://etherscan.io/tx/0x1193fdc7e9652461429e0a6034ae19b23075a19c679a825144dcc0c212a2fe03)（公开样本） |
| 5 | Swap USDT→USDx | Stake 页 → Swap | Curve USDx/USDT 池 `0xE521BA88837B2726E5343ccA91adA6f19F356C45` · `exchange` | [0x2c6d0ac20f324dbe02a238ac0f8e1c3730834cae74587e81f6200939150be8a3](https://etherscan.io/tx/0x2c6d0ac20f324dbe02a238ac0f8e1c3730834cae74587e81f6200939150be8a3)（公开样本） |
| 6 | Stake（USDx→sUSDx） | Stake 页 → Stake → Stake | sUSDx `0xEB892628D1E58BC475A6dCB7F5dBC4F591632AA4` · `deposit` | [0x9ac010780bc4cfd44b95c184a0ad8b5f92a3c7c6a9a6fdfb0542b4701e58793d](https://etherscan.io/tx/0x9ac010780bc4cfd44b95c184a0ad8b5f92a3c7c6a9a6fdfb0542b4701e58793d)（公开样本） |
| 7 | Unstake（发起 7 天冷却） | Stake → Unstake | sUSDx · `requestRedeem` | [0x0836d4556816e9401da7f1ac696ded3e318bbc183d2fb3f1a967fd0a3265bb83](https://etherscan.io/tx/0x0836d4556816e9401da7f1ac696ded3e318bbc183d2fb3f1a967fd0a3265bb83)（公开样本） |
| 8 | 冷却后提取 USDx | Stake → Withdraw（需连钱包） | sUSDx · `redeem` | [0x9470efa1a0f4437f0b74b340409069269af8f8339141861d5b23c1ba61cc0a54](https://etherscan.io/tx/0x9470efa1a0f4437f0b74b340409069269af8f8339141861d5b23c1ba61cc0a54)（公开样本） |

页面有入口但**不产生 Axis 交易**：Coordinates（积分、推荐，链下）、Earn（汇总页，Curve / Pendle 跳外链）、Transparency（数据页）、“Apply for primary access”（大额直铸直赎申请，链下）。

### 截图

**前端页面**

| Origin Vault · Deposit | Origin Vault · Withdraw |
|:-:|:-:|
| <img src="截图/Axis-ETH-Origin存入页-20261013.png" width="460"> | <img src="截图/Axis-ETH-Origin赎回页-20261013.png" width="460"> |
| **Upshift 金库页（存取均开放）** | **Stake 页 · Swap** |
| <img src="截图/Axis-ETH-Upshift金库页-20261013.png" width="460"> | <img src="截图/Axis-ETH-Swap页-20261013.png" width="460"> |
| **Stake 页 · Stake** | **Stake 页 · Unstake** |
| <img src="截图/Axis-ETH-Stake页-20261013.png" width="460"> | <img src="截图/Axis-ETH-Unstake页-20261013.png" width="460"> |
| **Stake 页 · Withdraw** | **Earn（汇总页，外链）** |
| <img src="截图/Axis-ETH-Stake提取页-20261013.png" width="460"> | <img src="截图/Axis-ETH-Earn页-20261013.png" width="460"> |
| **Coordinates（积分，链下）** | **** |
| <img src="截图/Axis-ETH-Coordinates页-20261013.png" width="460"> |  |

**交易详情（每类一张，均为 Blockscout）**

| 1 存入（`0x01b303…`） | 2 申请赎回（`0x96490a…`） |
|:-:|:-:|
| <img src="截图/Axis-ETH-Origin存入交易-20261013.png" width="460"> | <img src="截图/Axis-ETH-Origin申请赎回交易-20261013.png" width="460"> |
| **3 到期领取（`0xe59380…`）** | **4 即时赎回（`0x1193fd…`）** |
| <img src="截图/Axis-ETH-Origin到期领取交易-20261013.png" width="460"> | <img src="截图/Axis-ETH-Origin即时赎回交易-20261013.png" width="460"> |
| **5 Swap（`0x2c6d0a…`）** | **6 Stake（`0x9ac010…`）** |
| <img src="截图/Axis-ETH-Swap交易-20261013.png" width="460"> | <img src="截图/Axis-ETH-Stake交易-20261013.png" width="460"> |
| **7 Unstake（`0x0836d4…`）** | **8 冷却后提取（`0x9470ef…`）** |
| <img src="截图/Axis-ETH-Unstake交易-20261013.png" width="460"> | <img src="截图/Axis-ETH-Stake提取交易-20261013.png" width="460"> |

⚠️ 开发注意：
- **Origin 金库份额是 ogUSDx `0x29e0fD0771BfAb37897e7F9AE8c5eba7caaf0bfD`，赎回付的是 USDx `0xa1fA7777974312f7d801A8880714a218F76233f8`（不是 USDC/USDT）**。`requestRedeem` 只把 ogUSDx 转进金库，到 `claim`（或运营方 `processAllClaimsByDate` 批量处理）才销毁并付 USDx。
- **Stake 线两段式**：sUSDx `requestRedeem` 时即销毁 sUSDx（`RedeemRequest` 事件），7 天后 `redeem` 才付 USDx；中间的 `serviceRedeemRequest` 是运营方调用，不是用户操作。

## 0. 一句话说明

**Axis Origin USDx** 单独作为一个协议调研（需求方 2026-09-01 明确要求分开算）。它托管在 **Upshift（August Digital）平台**上，用户通过 Upshift 前端操作，用的是 Upshift 那套金库合约，但作为独立条目跟踪。

🔴 **当前存取都被暂停**（2026-09-01 页面确认；⬜ 缺图：原截图 `Axis-存取暂停-20260901.png` 未入库，暂停已于 9/4 结束无法重截，同一 Upshift 页面 10-13 现状见 `截图/Axis-ETH-Upshift金库页-20261013.png`）：页面横幅 *"Deposits & Withdrawals Disabled — until 4 September 2026, 14:00 UTC"*，标签 *Withdrawals paused / Pre-deposit*。本人尝试存 5 USDC 被拒：*"Deposits are currently paused for this vault."*
→ 所以链上现在**一条用户交易都没有**，要等 **9/4 14:00 UTC 之后**才能做第一笔。

**页面确认的参数**（截图 `Axis-存取暂停-20260901.png` ⬜ 缺图，见上；同页面 10-13 现状 `Axis-ETH-Upshift金库页-20261013.png`）：TVL **$67.58M** ｜ Share price **1.0045 USDx** ｜ Target APY **18.00%** ｜ 存款币种 USDT/USDC（存入时 swap 成 USDx，1 USDC≈0.9999 USDx，max 1% 滑点）｜ **提取周期 7 天** ｜ Vault `0xAD95…595F` ｜ receipt = Axis Origin USDx。

⚠️ 排除项：`axisorigin.com`（动画工作室）、`axis.money`（待售域名）都**不是**这个协议，别被搜索结果带偏。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Axis Origin USDx（Upshift/August 平台上的金库） |
| 链 | **Ethereum**（chainId 1） |
| **金库合约** | `0xAD958C4c0c90bf0216e0f5472F074a9AB30f595F`（Upshift 官方 API 确认，vault_name = "Axis Origin USDx"） |
| 存款资产 | USDx / USDC 类稳定币 |
| TVL | **$67.58M**（Upshift API，2026-09-01）|
| 阶段 | **Pre-deposit（预存，未开放赎回）** ｜ 类型 multiAssetVault ｜ receipt = Axis Origin USDx |
| **操作入口** | https://app.upshift.finance （在金库列表里选 Axis Origin USDx） |
| 金库页 URL 格式 | `https://app.upshift.finance/pools/1/0xAD958C4c0c90bf0216e0f5472F074a9AB30f595F` |
| 接入情况 | 待定 |

## 2. 操作类型（= Upshift 那套）

Axis Origin USDx 是 Upshift 金库之一，操作类型和链上函数与 Upshift 完全一致，**详见 `Upshift-ETH.md`**：

| 操作 | 链上方法 | 样本（Upshift 通用，同一批合约）|
|------|---------|------|
| Deposit（存入金库） | `deposit` | `0xc4e86fca6c450fdc8ab05b0a6b920b4de47e80de2739cac1a670f3857624411e` |
| 赎回-申请 | `requestRedeem` | `0x31aa09e200d05975f5e472715f08a7ed4fac0f8d5e2db03024bcc68d6b926a64` |
| 赎回-领取 | `claim` | `0x49ad91a269f3b7268308b43d4c27659b1d7aa4190b1df7f1dca9ea4b04c86978` |
| 即时赎回 | `instantRedeem` | `0x640827eeba0a17aeba2c241656371d34f35edb14f681e4ab4fdaea4c6a3f2002` |

🔴 **但 Axis Origin USDx 金库自己（`0xAD958C4c…`）近 10 万块无任何份额铸造/销毁事件** —— 因为处于 **Pre-deposit 阶段还没开放常规存取**。上面的样本是 Upshift 其他已运行金库的通用样本，仅供解析同学参考函数结构；**Axis 金库的真实交易要等它上线正式存取后才有**，或你连钱包在 Pre-deposit 页做一笔预存。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | Pre-deposit 阶段的"存入"链上长什么样（走哪个函数） | 连钱包做一笔预存看交易 |
| 2 | ✅ **已确认独立立项**（需求方 2026-09-01） | — |
| 3 | 正式上线后的赎回机制（instant / 排队） | 上线后实测 |
