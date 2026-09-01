# f(x) Protocol — fxUSD（杠杆 + 稳定币）· Ethereum

> **状态**：🟡 fxUSD 铸造/赎回链上样本已取到（2026-09-01）
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Ethereum**

## 0. 一句话说明

f(x) Protocol（Aladdin DAO 旗下）是链上交易平台：一边给 ETH/WBTC 提供**无爆仓压力的杠杆**，一边给稳定币持有人**可持续收益**。核心稳定币是 **fxUSD**，另有稳定池 fxSP。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | f(x) Protocol（Aladdin DAO / aladdin.club） |
| 链 | **Ethereum** |
| **操作入口** | https://fx.aladdin.club |
| **fxUSD** | `0x085780639CC2cACd35E474e71f4d000e2405d8f6`（symbol fxUSD / name f(x) USD） |
| fxSP（稳定池） | `0x65C9A641afCEB9C0E6034e558A319488FA0FA3be`（symbol fxSP / f(x) Stability Pool） |
| 底层敞口 | ETH / WBTC |
| 接入情况 | 待定 |

## 2. 操作清单（页面可点击）

| # | 操作 | 说明 |
|---|------|------|
| 1 | **Mint fxUSD** | 存抵押铸稳定币 |
| 2 | **Redeem fxUSD** | 赎回 |
| 3 | **开杠杆头寸** | ETH/WBTC 杠杆（无爆仓压力） |
| 4 | **稳定池 fxSP 存/取** | 稳定币赚收益 |

## 2.1 📎 公开样本交易（**非本人钱包**，链上直接取样）

| 交易类型 | 事件 | 样本 tx hash |
|---------|------|-------------|
| **Mint fxUSD（铸造）** | fxUSD `Transfer from 0x0` | `0x49e16d6d4bb59541b1cae1e80836e80beeb9a2923f941a375cb8bcff0d602ea7` |
| **Redeem fxUSD（销毁）** | fxUSD `Transfer to 0x0` | `0x9fb6f43c04ef1cc6ee7366dd5d659f4dd1004aad3ce748ee3a50b708779ae427` |

**取样方法**：`eth_getLogs` address=fxUSD `0x085780…` topics=`[Transfer,0x0]`（铸造，近 3000 块 7 条）/ `[Transfer,null,0x0]`（销毁，2 条）。

## 2.2 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| Mint fxUSD | ⬜ | ✅ `0x49e16d…` |
| Redeem fxUSD | ⬜ | ✅ `0x9fb6f4…` |
| 开/平杠杆头寸 | ⬜ | ⬜ 待取样 |
| fxSP 稳定池存/取 | ⬜ | ⬜ 待取样 |

⚠️ **页面截图待补**：你去 fx.aladdin.club 截图，确认页面有哪几类操作（Mint/Redeem 之外的杠杆头寸、稳定池入口），做几笔补哈希。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | 杠杆头寸是 NFT 还是账户记录 | 实测 |
| 2 | fxUSD 铸造接受哪些抵押（ETH/WBTC/稳定币） | 页面 |
| 3 | fxSP 稳定池凭证机制 | 实测 |
