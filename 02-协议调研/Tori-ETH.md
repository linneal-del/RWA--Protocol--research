# Tori Finance — trUSD / strUSD（机构策略稳定币）· Ethereum

> **状态**：🟡 页面结构已确认 + strUSD 质押链上样本已取到（2026-09-01）
> **交付口径**：覆盖页面可点击的全部交易类型 + 给哈希 + 截图 + 背景信息；**链上解析由解析同学做，本页不做深度解析**
> **目标链**：**Ethereum**

## 0. 一句话说明

Tori 把机构级算法交易策略（delta 中性）包装成稳定币产品：用户拿稳定币铸 **trUSD**（1:1 稳定币），再把 trUSD **质押成 strUSD** 赚 delta 中性策略收益。跟 Ethena(USDe/sUSDe)、Unitas(USDu/sUSDu) 是同一类"合成美元 + 收益版"结构。当前 APY **11.2%**。

## 1. 基础信息

| 字段 | 值 |
|------|-----|
| 协议 | Tori Finance |
| 链 | **Ethereum** |
| **操作入口** | https://app.tori.finance |
| 官网 | https://tori.finance ｜ 当前 APY **11.2%** |
| trUSD（基础稳定币） | `0xd0580192E98eA6CEB9c7b6191Ed2E27560911697`（symbol TRUSD） |
| strUSD（质押收益版） | `0x280839980a7eD0D7717F64125fE241012E5F5815`（symbol STRUSD） |
| Ecosystem Vault | `0x6f20aE2C98c2D34e6A57f3411f2C5Af92E32592d`（symbol ETRUSD） |
| 解析类型 | trUSD = 稳定币(1:1)；strUSD = **A 类 NAV 累积**（质押收益体现在汇率） |
| 接入情况 | 待定 |

## 2. 操作清单（页面可点击）

| # | 操作 | 说明 |
|---|------|------|
| 1 | **Mint trUSD** | 用稳定币（USDC 等）铸 trUSD |
| 2 | **Redeem trUSD** | trUSD 换回稳定币 |
| 3 | **Stake（trUSD→strUSD）** | 质押赚 delta 中性策略收益 |
| 4 | **Unstake（strUSD→trUSD）** | 解押 |
| 5 | **Ecosystem Vault 存入** | USDC 存入生态金库（页面另有此入口） |

页面说明原文：*"Stake trUSD for strUSD, and automatically earn rewards from our delta-neutral trading strategies."* / *"Earn Cores just by holding trUSD or using it on DeFi protocols."*（trUSD 持有本身也攒积分 Cores）

## 2.1 📎 公开样本交易（**非本人钱包**，链上直接取样）

| 交易类型 | 事件 | 样本 tx hash |
|---------|------|-------------|
| **Stake（铸 strUSD）** | strUSD `Transfer from 0x0` | `0x70e945a2be799df0d646d36438f41d358270bd7773a9d70615c0331e3e4b4b8c` |

**取样方法**：`eth_getLogs` address=strUSD `0x280839…` topics=`[Transfer,0x0]`，近 3000 块 3 条。
⚠️ 可用节点 `https://eth.drpc.org`（有限流，报空要换节点重试）。

⬜ **未取到的**：trUSD 铸造/赎回、strUSD 解押、Ecosystem Vault 存入 —— trUSD 的铸造疑似经 minter 合约（非直接 `from 0x0`），近 80 万块未捕获到标准 mint 事件，需连钱包实测或翻交易历史定位 minter。

## 2.2 操作覆盖

| 交易类型 | 本人实测 | 公开样本 |
|---------|:---:|:---:|
| Stake（trUSD→strUSD） | ⬜ | ✅ `0x70e945…` |
| Mint trUSD | ⬜ | ⬜ 待定位 minter |
| Redeem trUSD | ⬜ | ⬜ 待补 |
| Unstake（strUSD→trUSD） | ⬜ | ⬜ 待补 |
| Ecosystem Vault 存入 | ⬜ | ⬜ 待补 |

⚠️ **页面截图待补**：你去 app.tori.finance 截图确认几个入口（Mint/Redeem/Stake/Unstake/Vault）都在哪，做几笔把哈希补上。

## 3. 待确认清单

| # | 问题 | 怎么查 |
|---|------|--------|
| 1 | trUSD 的 minter 合约地址（铸造走哪） | 翻 trUSD 交易历史 |
| 2 | 赎回是否即时/排队 | 实测 |
| 3 | Ecosystem Vault 与 strUSD 的区别 | 页面/文档 |
