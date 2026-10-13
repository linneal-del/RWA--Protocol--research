# Axis Origin USDx — Ethereum

> **状态**：🟡 独立协议，Pre-deposit 阶段（2026-09-01）｜ **按需求方要求单独立项**
> **目标链**：**Ethereum**（chainId 1）

## 0. 一句话说明

**Axis Origin USDx** 单独作为一个协议调研（需求方 2026-09-01 明确要求分开算）。它托管在 **Upshift（August Digital）平台**上，用户通过 Upshift 前端操作，用的是 Upshift 那套金库合约，但作为独立条目跟踪。

🔴 **当前存取都被暂停**（2026-09-01 页面确认，截图见文末）：页面横幅 *"Deposits & Withdrawals Disabled — until 4 September 2026, 14:00 UTC"*，标签 *Withdrawals paused / Pre-deposit*。本人尝试存 5 USDC 被拒：*"Deposits are currently paused for this vault."*
→ 所以链上现在**一条用户交易都没有**，要等 **9/4 14:00 UTC 之后**才能做第一笔。

**页面确认的参数**（截图 `Axis-存取暂停-20260901.png`）：TVL **$67.58M** ｜ Share price **1.0045 USDx** ｜ Target APY **18.00%** ｜ 存款币种 USDT/USDC（存入时 swap 成 USDx，1 USDC≈0.9999 USDx，max 1% 滑点）｜ **提取周期 7 天** ｜ Vault `0xAD95…595F` ｜ receipt = Axis Origin USDx。

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
