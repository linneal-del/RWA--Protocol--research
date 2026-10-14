# Axis Origin USDx — Ethereum

> **链**：Ethereum（chainId 1） ｜ 浏览器 https://eth.blockscout.com
> **调研日期**：2026-10-13 ｜ 本人实测与公开样本已在「来源」列标注；公开样本均为链上公开交易（非本人钱包），已逐条确认成功
> **存取现状**：赎回已开放（9/4 起，7 天队列或约 30bps 即时赎回）；Axis 自家页存入窗口 7/29–8/5 已关闭，Upshift 金库页仍可存入。

## 合约

| 项 | 值 |
|---|---|
| 前端 | Axis https://app.axis.to/origin （Origin Vault / Stake / Earn / Coordinates / Transparency） ｜ Upshift 金库页 https://app.upshift.finance/pools/1/0xAD958C4c0c90bf0216e0f5472F074a9AB30f595F |
| Origin 金库（Upshift TokenizedVault） | `0xAD958C4c0c90bf0216e0f5472F074a9AB30f595F` |
| ogUSDx（金库份额） | `0x29e0fD0771BfAb37897e7F9AE8c5eba7caaf0bfD` |
| USDx（赎回付出资产） | `0xa1fA7777974312f7d801A8880714a218F76233f8` |
| sUSDx（Stake 线） | `0xEB892628D1E58BC475A6dCB7F5dBC4F591632AA4` |
| Curve USDx/USDT 池（Swap） | `0xE521BA88837B2726E5343ccA91adA6f19F356C45` |

## 交易类型覆盖

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash | 来源 |
|---|---|---|---|---|---|
| 1 | 存入 Origin 金库（USDT/USDC/USDx → ogUSDx） | Upshift 金库页 Deposit（Axis 页存入窗口已关） | Origin 金库 · `deposit` | [0x01b30339f3a15f3a2e11fc665ff5ad438ec76c61bb2f10976f29b83a77cce731](https://eth.blockscout.com/tx/0x01b30339f3a15f3a2e11fc665ff5ad438ec76c61bb2f10976f29b83a77cce731) | 公开样本 |
| 2 | 申请赎回（7 天队列） | Origin Vault → Withdraw | Origin 金库 · `requestRedeem` | [0x96490a4048e62d1658fe191f7f71c8f6126e54d9e63da848737f46a800b596d0](https://eth.blockscout.com/tx/0x96490a4048e62d1658fe191f7f71c8f6126e54d9e63da848737f46a800b596d0) | 公开样本 |
| 3 | 队列到期领取 USDx | Origin Vault → Withdraw（需连钱包） | Origin 金库 · `claim` | [0xe593808f900d23284eaeaba1ccb4e79d103ea458d51245bc7fd2b4d3ec049e83](https://eth.blockscout.com/tx/0xe593808f900d23284eaeaba1ccb4e79d103ea458d51245bc7fd2b4d3ec049e83) | 公开样本 |
| 4 | 即时赎回（约 30bps） | Origin Vault → Withdraw（需连钱包） | Origin 金库 · `instantRedeem` | [0x1193fdc7e9652461429e0a6034ae19b23075a19c679a825144dcc0c212a2fe03](https://eth.blockscout.com/tx/0x1193fdc7e9652461429e0a6034ae19b23075a19c679a825144dcc0c212a2fe03) | 公开样本 |
| 5 | Swap USDT→USDx | Stake → Swap | Curve USDx/USDT 池 · `exchange` | [0x2c6d0ac20f324dbe02a238ac0f8e1c3730834cae74587e81f6200939150be8a3](https://eth.blockscout.com/tx/0x2c6d0ac20f324dbe02a238ac0f8e1c3730834cae74587e81f6200939150be8a3) | 公开样本 |
| 6 | Stake（USDx→sUSDx） | Stake → Stake | sUSDx · `deposit` | [0x9ac010780bc4cfd44b95c184a0ad8b5f92a3c7c6a9a6fdfb0542b4701e58793d](https://eth.blockscout.com/tx/0x9ac010780bc4cfd44b95c184a0ad8b5f92a3c7c6a9a6fdfb0542b4701e58793d) | 公开样本 |
| 7 | Unstake（发起 7 天冷却） | Stake → Unstake | sUSDx · `requestRedeem` | [0x0836d4556816e9401da7f1ac696ded3e318bbc183d2fb3f1a967fd0a3265bb83](https://eth.blockscout.com/tx/0x0836d4556816e9401da7f1ac696ded3e318bbc183d2fb3f1a967fd0a3265bb83) | 公开样本 |
| 8 | 冷却后提取 USDx | Stake → Withdraw（需连钱包） | sUSDx · `redeem` | [0x9470efa1a0f4437f0b74b340409069269af8f8339141861d5b23c1ba61cc0a54](https://eth.blockscout.com/tx/0x9470efa1a0f4437f0b74b340409069269af8f8339141861d5b23c1ba61cc0a54) | 公开样本 |

页面有入口但**不产生 Axis 交易**：Coordinates（积分、推荐，链下）、Earn（汇总页，Curve / Pendle 跳外链）、Transparency（数据页）、「Apply for primary access」（大额直铸直赎申请，链下）。

## 截图

**前端页面**

| Origin Vault · Deposit | Origin Vault · Withdraw |
|:-:|:-:|
| <img src="截图/Axis-ETH-Origin存入页-20261013.png" width="460"> | <img src="截图/Axis-ETH-Origin赎回页-20261013.png" width="460"> |
| **Upshift 金库页（存取均开放）** | **Stake · Swap** |
| <img src="截图/Axis-ETH-Upshift金库页-20261013.png" width="460"> | <img src="截图/Axis-ETH-Swap页-20261013.png" width="460"> |
| **Stake · Stake** | **Stake · Unstake** |
| <img src="截图/Axis-ETH-Stake页-20261013.png" width="460"> | <img src="截图/Axis-ETH-Unstake页-20261013.png" width="460"> |
| **Stake · Withdraw** | **Earn（汇总页，外链）** |
| <img src="截图/Axis-ETH-Stake提取页-20261013.png" width="460"> | <img src="截图/Axis-ETH-Earn页-20261013.png" width="460"> |
| **Coordinates（积分，链下）** | |
| <img src="截图/Axis-ETH-Coordinates页-20261013.png" width="460"> | |

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

## 开发注意

- **Origin 金库托管在 Upshift（August Digital）金库体系**：合约与方法（`deposit` / `requestRedeem` / `claim` / `instantRedeem`）与其他 Upshift 金库一致，按金库地址 `0xAD958C4c…` 区分 Axis。
- **份额是 ogUSDx，赎回付 USDx（不是 USDC/USDT）**：`requestRedeem` 只把 ogUSDx 转进金库，到 `claim`（或运营方 `processAllClaimsByDate` 批量处理）才销毁并付 USDx。
- **Stake 线两段式**：sUSDx `requestRedeem` 时即销毁 sUSDx（`RedeemRequest` 事件），7 天后 `redeem` 才付 USDx；中间的 `serviceRedeemRequest` 为运营方调用，不是用户操作。

## 待确认

- 同站的 USDx / sUSDx Stake 线（#5～8）是否算入 Axis Origin 一并解析
- Axis 自家页存入窗口是否会再开放，开放后是否仍走同一金库 `deposit`
- 运营方批量 `processAllClaimsByDate` 是否需要按用户拆分记账
