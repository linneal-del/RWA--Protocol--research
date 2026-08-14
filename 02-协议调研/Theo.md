# Theo — BSC RWA（thBILL / thGOLD / thUSD）

> **状态**：⬜ 本周待实测 ｜ **调研时间**：2026-08-14 ｜ **解析类型**：待定（预判 A 类 NAV 累积）
> **调研质量**：低（docs 探测；⚠️ **app.theo.xyz 探测 403**，需浏览器打开确认真实入口）

## 0. 一句话结论

Theo = **机构级 RWA 协议**：把 T-Bill（thBILL）、黄金（thGOLD）、美元（thUSD）等资产带上链，"deploy and manage capital across competitive yield offerings"。本周在 **BSC** 上做一笔 mint/redeem。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Theo（theo.xyz） |
| 链 | **BSC**（多 L1/L2 部署） |
| 产品 | thBILL / thGOLD / thUSD（选 BSC 上可操作的 1 个） |
| 网页入口 | https://app.theo.xyz （⚠️ 探测 403，浏览器确认；docs：https://docs.theo.xyz） |
| Supply Coins | 待确认（USDC / USDT？） |
| Coins Integrated | thBILL / thGOLD / thUSD（地址 ⚠️ 待确认） |
| TVL / 收益率 | 待 UI 截图 |
| 池子类别 | 活期（NAV 累积预判） |
| 参考 | [docs.theo.xyz/platform/overview](https://docs.theo.xyz/platform/overview) ｜ [theo.xyz](https://theo.xyz) ｜ [融资报道](https://www.prnewswire.com/news-releases/theo-raises-20m-to-democratize-access-to-institutional-grade-trading-infrastructure-302437572.html) |

## 2. 本周操作清单（用户去做交易 + 截图）

| # | 操作 | 入口路径 | 截图要点 |
|---|------|---------|---------|
| 0 | 打开 app.theo.xyz 确认真实入口 | 浏览器 | 入口 URL、BSC 网络 |
| 1 | **Mint / Buy**（小额） | 选 thXXX → mint | 汇率、数量 |
| 2 | **Redeem** | redeem | 收回数量、是否排队 |

每笔操作后把 **tx hash** 和截图一起给我。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | app 真实入口（403 是地区挡还是已换域名） | 浏览器 |
| 2 | thXXX 合约地址 + decimals + 汇率机制 | BscScan + 实测 |
| 3 | BSC 上有哪些产品可操作 | UI |
