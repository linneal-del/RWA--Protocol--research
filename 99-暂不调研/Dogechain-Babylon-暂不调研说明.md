# Dogechain / Babylon — 暂不调研说明（扩链 DEX 协议）

> **状态**：⛔ 暂不调研（不采 hash、不截图）｜ **记录时间**：2026-09-29
> **来源**：Confluence「各链协议调研」子页 Dogechain（pageId=609479867）、Babylon（pageId=610323233），核实日期 2026-08-18
> **背景**：同批扩链 DEX 调研（B² / Ronin / Conflux / Metis / Mode / BounceBit / Zeta / Cyber / NEAR）见 `02-协议调研/<链>-DEX协议汇总.md`；这两条链因为下面的原因单独放这里

## 0. 一句话结论

| 链 | 结论 | 原因 |
|---|---|---|
| **Dogechain**（chainId 2000，EVM） | 🔴 **链已停运，不接入** | 官方 2026-06 公告 sunset，**2026-08-08 12:00 UTC 永久关闭**，跨链桥已关、链上资产不可取回 |
| **Babylon Genesis**（bbn-1，Cosmos SDK） | ⛔ **没有可适配的 DEX，不接入** | BTC 质押链，TVL 几乎全在再质押 / 流动性质押；链上 DEX 交易量可以忽略 |

## 1. Dogechain

- 关停前的状态：全链 24h 交易量约 **$11K**，几乎都在一个 OMNOM/WWDOGE 池里。
- 数据源：GeckoTerminal（404）、DexScreener、OKX 都没有收录，Ave 待确认。官方浏览器 `explorer.dogechain.dog` DNS 解析失败，疑似已停。⚠️ `dogechain.info` 是 Dogecoin L1 的浏览器，不是这条 EVM 链的。
- Confluence 当时按 **TVL 口径**（没有 volume 数据源）选出 3 个协议，仅作归档：

| 协议 | Dogechain 链上 TVL(USD) | 占比 | 累计 |
|---|---:|---:|---:|
| DogeSwapOrg | 279,340 | 75.5% | 75.5% |
| KibbleSwap | 40,180 | 10.9% | 86.4% |
| Yodeswap | 27,841 | 7.5% | 93.9% |

→ 链已经永久关闭，**这 3 个协议不再调研**，原来 Confluence 上的 hash 空位也不补。

## 2. Babylon Genesis

- 链类型：基于 Cosmos SDK 的比特币质押链，**不是 EVM**，没有 EVM chainId。浏览器：https://www.mintscan.io/babylon
- GeckoTerminal、DexScreener、OKX 都没有收录；Ave 主要覆盖 EVM，预计也不支持。
- TVL 来源：BTC 再质押 / 流动性质押（b14g ≈ $16.9 万、SatLayer、Escher），不是 DEX。
- 链上 DEX：Tower DEX ≈ $4,033、Persistence DEX ≈ $2,191，都是 Cosmos IBC 型 DEX，交易量可以忽略。

→ 当前的行情解析框架是按 EVM DEX 设计的，这条链不适用。如果以后要看，建议把质押 / 再质押数据单独作为观察对象，走 RWA / LST 线，不走 DEX 线。

## 3. 重新启用的条件

- Dogechain：官方重新启动主网（可能性很低）。
- Babylon：出现有意义的原生 DEX（比如日交易量达到其他扩链链的水平），或者业务明确要做 BTC 质押数据。
