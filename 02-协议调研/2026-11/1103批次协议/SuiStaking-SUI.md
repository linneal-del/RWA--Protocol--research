# Sui Staking — SUI

> **链**：SUI（交易标识为 digest，base58） ｜ 浏览器 https://suiscan.xyz/mainnet ｜ 取数 GraphQL `https://graphql.mainnet.sui.io/graphql`
> **调研日期**：2026-08-29 ｜ 全部为公开样本，非本人钱包；原始出处 [SUI五协议-SUI.md §1](../../2026-08/SUI五协议-SUI.md)

## 合约

| 项 | 值 |
|---|---|
| 类型 | SUI 链原生验证人质押，非第三方协议 |
| 系统包 | `0x3::sui_system` |
| 共享对象 | SuiSystemState `0x5` |
| 前端 | 钱包内置质押（如 Sui Wallet / Binance Wallet → Stake SUI），无独立协议前端 |

## 交易类型覆盖

4 类：4 类有公开样本。

| # | 交易类型 | 前端入口 | 模块 · 函数 | digest | 来源 |
|---|---|---|---|---|---|
| 1 | 质押 | 钱包 → Stake SUI → 选验证人 | `sui_system::request_add_stake` | [3KpHbTtGCZYbmYmrRdiePk69MJYqo3pqit5B2cWZU2eJ](https://suiscan.xyz/mainnet/tx/3KpHbTtGCZYbmYmrRdiePk69MJYqo3pqit5B2cWZU2eJ) | 公开样本 |
| 2 | 质押（多币合并） | 同上（钱包自动合并多个 SUI coin） | `sui_system::request_add_stake_mul_coin` | [Gt1iHU3CwpK29gYdBtu9BYXSX4cX4JZu6wDTn4ybDioM](https://suiscan.xyz/mainnet/tx/Gt1iHU3CwpK29gYdBtu9BYXSX4cX4JZu6wDTn4ybDioM) | 公开样本 |
| 3 | 解除质押 | 钱包 → 质押详情 → Unstake | `sui_system::request_withdraw_stake` | [415XjDxcbKUMQW126hpSghAUxco3ZgS4gr1AAd3cpask](https://suiscan.xyz/mainnet/tx/415XjDxcbKUMQW126hpSghAUxco3ZgS4gr1AAd3cpask) | 公开样本 |
| 4 | 赎回可分割质押凭证 | 无（合约直调 / 第三方集成） | `sui_system::redeem_fungible_staked_sui` | [FV3etqgAmwNRUcSamHb4wCTPbwDtsDKRvSET3jVFM77S](https://suiscan.xyz/mainnet/tx/FV3etqgAmwNRUcSamHb4wCTPbwDtsDKRvSET3jVFM77S) | 公开样本 |

## 开发注意

- 质押有两个入口函数（#1 单币 / #2 多币合并），都要覆盖。

## 待确认

- 暂无前端与交易详情截图，是否需要补
