# MarginFi — Solana 借贷池

> **状态**：⬜ 本周待实测 ｜ **调研时间**：2026-08-14 ｜ **解析类型**：待定（借贷份额，预判 A/B 类）
> **调研质量**：低（官网探测；⚠️ **app.marginfi.com 已显示迁移到 Project 0（app.0.xyz）**，旧 app 可能停用，实测前先确认哪个入口可用）

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

每笔操作后把 **Solana 签名** 和截图一起给我。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | 旧 app 是否还能出交易？迁移后份额是否互通 | 浏览器实测 |
| 2 | 份额凭证（bank token）mint 地址、汇率机制（NAV 累积？） | 交易内 token 账户 |
| 3 | marginfi program 地址 | 交易内确认 |
