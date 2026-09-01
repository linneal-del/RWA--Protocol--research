# Axis Origin USDx — Ethereum

> **状态**：🟡 已定位为 Upshift 上的金库，链上样本可复用（2026-09-01）
> **目标链**：**Ethereum**（chainId 1）

## 0. 一句话说明

"Axis" 没有独立的官网 DApp —— 它是 **Upshift（August Digital）平台上的一个金库产品：Axis Origin USDx**。用户通过 Upshift 前端存取，走的是 Upshift 那套合约。所以本协议的操作类型和样本**直接复用 `Upshift-ETH.md`**。

⚠️ 排除项：`axisorigin.com`（动画工作室）、`axis.money`（待售域名）都**不是**这个协议，别被搜索结果带偏。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Axis Origin USDx（Upshift/August 平台上的金库） |
| 链 | **Ethereum**（chainId 1） |
| **金库合约** | `0xAD958C4c0c90bf0216e0f5472F074a9AB30f595F`（Upshift 官方 API 确认，vault_name = "Axis Origin USDx"） |
| 存款资产 | USDx / USDC 类稳定币 |
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

📌 上面的样本是 Upshift 8 个金库合约的通用样本。**Axis Origin USDx 自己金库（`0xAD958C4c…`）的专属交易样本待取**（需连钱包实测，或从该合约的 Transfer 日志单独捞）。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | Axis Origin USDx 金库自己的存取样本 | 扫 `0xAD958C4c…` 的 Transfer 日志 |
| 2 | 是否需要和 Upshift 分开算作独立协议 | 需求方对齐（同一平台同一套合约） |
| 3 | Axis 是否另有独立品牌/前端 | 待用户确认来源 |
