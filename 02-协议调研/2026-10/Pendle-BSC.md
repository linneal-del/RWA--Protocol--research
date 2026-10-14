# Pendle — BSC 协议调研

> **链**：BNB Smart Chain ｜ chainId **56** ｜ 浏览器 https://bscscan.com
> **调研日期**：2026-10-13 ｜ hash 均为链上公开交易（非本人钱包），已逐条确认成功

## 合约与市场

| 项 | 值 |
|---|---|
| 前端 | 市场列表 https://app.pendle.finance/trade/markets?chains=bnbchain ｜ PT/YT 交易 `/trade/markets/<market>/swap?chain=bnbchain&view=pt\|yt` ｜ 限价单 `/trade/markets/<market>/limit-order?chain=bnbchain` ｜ LP `/trade/pools/<market>/zap/in?chain=bnbchain` ｜ 跨链 PT https://app.pendle.finance/trade/crosschain-pt |
| RouterV4（所有前端交易入口） | `0x888888888889758F76e7103c6CbF23ABbF58F946` |
| Limit Router（限价单撤单 / 成交） | `0x000000000000c9B3E2C3Ec88B1B4c0cD853f4321` |

BSC 当前 6 个 active 市场（Pendle API `core/v1/56/markets/active`，2026-10-13）：

| 市场 | 到期 | Market | PT | YT | SY |
|---|---|---|---|---|---|
| cUSDO（OpenEden） | 2026-10-29 | `0x03bc522d619fe24b788b9a2a39944f7de1685239` | `0xcd18d94fda553bb956d6130ad217b63ef65888d0` | `0x4894ad20c952c6e4a91c2e9ba6b96e65d9bc2664` | `0x7a657c50c6b640ecaab7bb8dad86344169bf8c16` |
| apxUSD（APYX） | 2026-11-05 | `0x34a1caef9f303a33e5d15bc5bc85064a39b7d1df` | `0x71f3408b1493eddf283e53e7bae6d36463e12737` | `0xc8ea9a8672ea5a61487cd596cc6dac7e288f7655` | `0x1a375dea39acad9a0625b92338e72112cab7f886` |
| slisBNB（Lista，前端显示 clisBNB） | 2026-10-29 | `0xe2a8b724885bdc8412a4f12f8aad461de6071b2a` | `0x493bab28fed4b4d87903463da656d2af24babb6e` | `0x8ea247d465acc0928ce60d88252bc958854e13be` | `0x27faf900007b4cba7803000251ec96bc69ff1bea` |
| USDat（Saturn） | 2027-01-14 | `0x48fd92fd80e0256d455e82b7709b265fe44da9d2` | `0x476aa4205b4eac187011fd8fa07e53d445f213fc` | `0x99010c74fb8733d399b21a5880cee15b35dc5773` | `0x81a77db87618d51bc12c9eabe08cc298764b8277` |
| XGLD（Unitas） | 2026-12-17 | `0x2e90f1f31b572313e84fde1a2a0507c2d9d8f225` | `0xb9e99f830f1ac2a128c58692e89100c3470aae13` | `0x11b15dc07d57dd5ea51e38542a7b3ff0bd04d2ec` | `0x3ccafc449bb902d5aed7edabeafa452aedacd1ed` |
| sUSDu（Unitas） | 2026-12-31 | `0xf037cb48fe2ca1cf785df2c34f4efec96fab8be8` | `0xe0ec248ded4f3c0c93ec4632f5ce7bd14595886b` | `0x3945a702bdc056eea913e1b3c833e110c512df4f` | `0xea8b6cb6157762ad63b9272bd82fc3fc06410c10` |

### 交易类型覆盖

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash | 来源 |
|---|---|---|---|---|---|
| 1 | 买 PT（Token→PT） | 市场 → PT → Swap → Buy PT | RouterV4 · `swapExactTokenForPt` | [0x45e36b14284beeabe093ebff9fae4ab01584b37f93449cd243c107eff23e192d](https://bscscan.com/tx/0x45e36b14284beeabe093ebff9fae4ab01584b37f93449cd243c107eff23e192d) | 公开样本 |
| 2 | 卖 PT（PT→Token） | PT → Swap → Sell PT | RouterV4 · `swapExactPtForToken` | [0x98c5189c4d3894f8c6d42d627ce1775e14090233015e33e89bc3cacfd63f644c](https://bscscan.com/tx/0x98c5189c4d3894f8c6d42d627ce1775e14090233015e33e89bc3cacfd63f644c) | 公开样本 |
| 3 | 买 YT（Token→YT） | 市场 → YT → Swap → Buy YT | RouterV4 · `swapExactTokenForYt` | [0x43d8f4a714e108e8d4fea20a8d9e4a981c537c3945d83990a030c126a7ed6961](https://bscscan.com/tx/0x43d8f4a714e108e8d4fea20a8d9e4a981c537c3945d83990a030c126a7ed6961) | 公开样本 |
| 4 | 卖 YT（YT→Token） | YT → Swap → Sell YT | RouterV4 · `swapExactYtForToken` | [0xc9802c14a60267127aeb9e543b1efeec3df84bbf01e05b3edfb94ca50a88cc3c](https://bscscan.com/tx/0xc9802c14a60267127aeb9e543b1efeec3df84bbf01e05b3edfb94ca50a88cc3c) | 公开样本 |
| 5 | 以 SY 计价买卖 PT/YT（输入/输出选 SY） | Swap（代币选 SY） | RouterV4 · `swapExactSyForPt` | [0x2ee4f09b4772ac52e31af4301036d29816849d6e1c0bc3e7611ff0b75284da0e](https://bscscan.com/tx/0x2ee4f09b4772ac52e31af4301036d29816849d6e1c0bc3e7611ff0b75284da0e) | 公开样本 |
| | | | RouterV4 · `swapExactPtForSy` | [0x667382a57073a81e624162979b065a5d138563a9c7ec8809fec854a0532d36aa](https://bscscan.com/tx/0x667382a57073a81e624162979b065a5d138563a9c7ec8809fec854a0532d36aa) | 公开样本 |
| | | | RouterV4 · `swapExactSyForYt` | [0x1704ea1f885c45b0cc0c66dab646a62ddd229e46667e8e9c2e4b6d9f0708fb08](https://bscscan.com/tx/0x1704ea1f885c45b0cc0c66dab646a62ddd229e46667e8e9c2e4b6d9f0708fb08) | 公开样本 |
| | | | RouterV4 · `swapExactYtForSy` | [0x08db3060f088a887acb47edd10dba54364ca269754a6d70a51b4b86596590f84](https://bscscan.com/tx/0x08db3060f088a887acb47edd10dba54364ca269754a6d70a51b4b86596590f84) | 公开样本 |
| 6 | 限价单成交（市价单顺带吃掉挂单） | Swap（路由自动撮合挂单） | RouterV4 · `swapExactTokenForPt`，同笔 Limit Router 发出成交事件 | [0x141a47d1efb5955a66cf6fbe2f361ba76183c31f61fa95fd4800d0f7383e1efb](https://bscscan.com/tx/0x141a47d1efb5955a66cf6fbe2f361ba76183c31f61fa95fd4800d0f7383e1efb) | 公开样本 |
| 7 | 限价单撤单（单笔） | Limit → 我的订单（需连钱包） | Limit Router · `cancelSingle` | [0xe03d8fe651400f66c92f3e7939a65ee66a3a73fac9058e576618e74b0b0d9fb4](https://bscscan.com/tx/0xe03d8fe651400f66c92f3e7939a65ee66a3a73fac9058e576618e74b0b0d9fb4) | 公开样本 |
| 8 | 限价单批量撤单 | Limit → 我的订单（需连钱包） | Limit Router · `cancelBatch` | [0x3e676e38b1f76f349718ea0c436ed41e0273c9300aa693f0dc860bd0151978a2](https://bscscan.com/tx/0x3e676e38b1f76f349718ea0c436ed41e0273c9300aa693f0dc860bd0151978a2) | 公开样本 |
| 9 | 撤销全部挂单 | Limit → 我的订单（需连钱包） | Limit Router · `increaseNonce` | [0x6eb11a5f5eb70f7b794bae8f2ad1ccf8c4226b7db8eff70e43d52cb930d1212e](https://bscscan.com/tx/0x6eb11a5f5eb70f7b794bae8f2ad1ccf8c4226b7db8eff70e43d52cb930d1212e) | 公开样本 |
| 10 | 单币加流动性（Zap In） | LP → Zap → Deposit | RouterV4 · `addLiquiditySingleToken` | [0xa1886cd1d5a48acfddcf83091d01e9ab55510469d9f613f314653f9190fa467b](https://bscscan.com/tx/0xa1886cd1d5a48acfddcf83091d01e9ab55510469d9f613f314653f9190fa467b) | 公开样本 |
| 11 | 单币加流动性 · Keep YT 模式 | LP → Zap → Deposit（打开 Keep YT Mode） | RouterV4 · `addLiquiditySingleTokenKeepYt` | [0x6b979c9759fd1672489ae27570337c88b96f52d3c56f07810aa227fa577553f5](https://bscscan.com/tx/0x6b979c9759fd1672489ae27570337c88b96f52d3c56f07810aa227fa577553f5) | 公开样本 |
| | （输入为 SY） | | RouterV4 · `addLiquiditySingleSyKeepYt` | [0xb51bf9b66c76b67356c6ada769dac9334a1338152142ef4e17087c520156b75f](https://bscscan.com/tx/0xb51bf9b66c76b67356c6ada769dac9334a1338152142ef4e17087c520156b75f) | 公开样本 |
| 12 | 双币加流动性（Token + PT） | LP → Manual → Add Liquidity | RouterV4 · `addLiquidityDualTokenAndPt` | [0xa08ce83b9032f9ab7cb70d2dc64aefd3960390c6d3467c452cd139fed62ae96f](https://bscscan.com/tx/0xa08ce83b9032f9ab7cb70d2dc64aefd3960390c6d3467c452cd139fed62ae96f) | 公开样本 |
| 13 | 单币减流动性（Zap Out） | LP → Zap → Withdraw | RouterV4 · `removeLiquiditySingleToken` | [0x86d0900df56a9f0c8b317c82e9d461d85e6d6b0ba47db33bb671fe48a0c1fbe5](https://bscscan.com/tx/0x86d0900df56a9f0c8b317c82e9d461d85e6d6b0ba47db33bb671fe48a0c1fbe5) | 公开样本 |
| 14 | 双边减流动性（拿回 SY + PT） | LP → Manual → Remove Liquidity | RouterV4 · `removeLiquidityDualSyAndPt` | [0xcc652714e0e3b37283f48cdf387b99511bd2e4a76aa8f62ce222fddcebd01b8c](https://bscscan.com/tx/0xcc652714e0e3b37283f48cdf387b99511bd2e4a76aa8f62ce222fddcebd01b8c) | 公开样本 |
| 15 | 铸造 PT+YT（Mint PY） | Swap 旁 Mint → Mint | RouterV4 · `mintPyFromToken` | [0xb52c4d5d9babcd524794ed4f1536bc7b7f6258ec572d3c1c9c53c352c289cf4a](https://bscscan.com/tx/0xb52c4d5d9babcd524794ed4f1536bc7b7f6258ec572d3c1c9c53c352c289cf4a) | 公开样本 |
| | （输入为 SY） | | RouterV4 · `mintPyFromSy` | [0x6dcaf252c40ee7948f45334d2cfd405e0e0e12c8194327d7546fcb3b68ed8349](https://bscscan.com/tx/0x6dcaf252c40ee7948f45334d2cfd405e0e0e12c8194327d7546fcb3b68ed8349) | 公开样本 |
| 16 | 赎回 PT+YT（Redeem PY） | Mint → Redeem | RouterV4 · `redeemPyToToken` | [0x1181fa8d45f3747f3277dd2549a1498a46445615974ec67e5529216e695426d7](https://bscscan.com/tx/0x1181fa8d45f3747f3277dd2549a1498a46445615974ec67e5529216e695426d7) | 公开样本 |
| | （输出为 SY） | | RouterV4 · `redeemPyToSy` | [0x6dbaee085a18d9cf17fe9ee18e72539d166bc8e57d922315dc0555bd59e1ede9](https://bscscan.com/tx/0x6dbaee085a18d9cf17fe9ee18e72539d166bc8e57d922315dc0555bd59e1ede9) | 公开样本 |
| 17 | Wrap SY（Token→SY） | Mint 下拉 / LP Tools → SY Converter → Wrap SY | RouterV4 · `mintSyFromToken` | [0xc3d55fa9c99fa84dcf1d67ffee1e5ff3ab6d4c56e4ef07389257b9d89594257b](https://bscscan.com/tx/0xc3d55fa9c99fa84dcf1d67ffee1e5ff3ab6d4c56e4ef07389257b9d89594257b) | 公开样本 |
| 18 | Unwrap SY（SY→Token） | SY Converter → Unwrap SY | RouterV4 · `redeemSyToToken` | [0x5b2229913f3d7affdf5ad4a4f231cc0b6eae4ce7fa4ee7505d0b187d91c422e4](https://bscscan.com/tx/0x5b2229913f3d7affdf5ad4a4f231cc0b6eae4ce7fa4ee7505d0b187d91c422e4) | 公开样本 |
| 19 | Claim YT 利息 / LP 奖励 | Dashboard → Claim（需连钱包） | RouterV4 · `redeemDueInterestAndRewardsV2` | [0x3431d35f72838bafae6d2264b59546379f8c78e8dca3ce80b38d7d510c78ebf2](https://bscscan.com/tx/0x3431d35f72838bafae6d2264b59546379f8c78e8dca3ce80b38d7d510c78ebf2) | 公开样本 |
| | （旧版方法） | | RouterV4 · `redeemDueInterestAndRewards` | [0xb1c9472c43d7b0d7fe24080c01696dc2707788beaaa4dd9bf5ecf8674d93cf94](https://bscscan.com/tx/0xb1c9472c43d7b0d7fe24080c01696dc2707788beaaa4dd9bf5ecf8674d93cf94) | 公开样本 |
| | （包在 multicall 里） | | RouterV4 · `multicall` → `redeemDueInterestAndRewardsV2` | [0x2958d77d1db3bb97f1c0473de0417a0ca2bb3a60d62eccf08dd2b91019e5587f](https://bscscan.com/tx/0x2958d77d1db3bb97f1c0473de0417a0ca2bb3a60d62eccf08dd2b91019e5587f) | 公开样本 |
| 20 | 到期后退出（PT/YT/LP 一键换回 Token） | 到期市场 / Dashboard（需连钱包） | RouterV4 · `exitPostExpToToken` | [0x05ccf6e195ffa1e12dc10ab9835aa39d254c804cf39a656c197d2e7c8dd6b852](https://bscscan.com/tx/0x05ccf6e195ffa1e12dc10ab9835aa39d254c804cf39a656c197d2e7c8dd6b852) | 公开样本 |
| 21 | 转移流动性 / 换仓（到期仓位换到新市场） | Dashboard 持仓（需连钱包） | RouterV4 · `callAndReflect` → `redeemPyToSy` + `swapExactSyForPt` | [0x347bbfc39767f635d7106840f2590de4918700eaa5371999174be0b88f8890ad](https://bscscan.com/tx/0x347bbfc39767f635d7106840f2590de4918700eaa5371999174be0b88f8890ad) | 公开样本 |
| 22 | PendleSwap（任意代币兑换，走外部聚合器） | 顶部 Swap → PendleSwap | RouterV4 · `swapTokensToTokens` | [0x207821deee7d9ffa622f0ae142fe7ebc216eba41c24ba7677b5aa80c589cbc21](https://bscscan.com/tx/0x207821deee7d9ffa622f0ae142fe7ebc216eba41c24ba7677b5aa80c589cbc21) | 公开样本 |
| 23 | Bridge PT 跨出 BSC（LayerZero OFT） | More → Bridge PT | 跨链 PT 合约（如 PT-sUSDE-7MAY2026 `0x721105716ab5419f1aaadbbc7835e3eabd7e067d`）· `send` | [0xc247e870152e45a256a1aa8b0299935b5b54629badca54b47383165c7aa4c6f4](https://bscscan.com/tx/0xc247e870152e45a256a1aa8b0299935b5b54629badca54b47383165c7aa4c6f4) | 公开样本 |

页面有入口但**不产生 BSC 链上 Pendle 交易**：
- 限价单挂单：链下签名，不上链（只有撤单 / 成交上链）。
- sPENDLE 质押（原 vePENDLE）：只在以太坊，BSC 上 ⬜ 无此功能。
- PT Looping：筛选 BNB Chain 后 0 个市场，⬜ BSC 上无样本。
- Deploy Pool（社区部署市场）：低频操作，⬜ 本次未找到 BSC 样本。
- Token Bridge（PendleSwap 弹窗第二个 tab）、Boros、Pendle Morpho Vault 等都跳转到外部或其他链。

### 截图

**前端页面**

| Markets（筛选 BNB Chain，6 个市场） | PT 买卖（Swap） |
|:-:|:-:|
| <img src="截图/Pendle-BSC-Markets页-20261013.png" width="460"> | <img src="截图/Pendle-BSC-PT买卖页-20261013.png" width="460"> |
| **YT 买卖** | **限价单（Limit）** |
| <img src="截图/Pendle-BSC-YT买卖页-20261013.png" width="460"> | <img src="截图/Pendle-BSC-限价单页-20261013.png" width="460"> |
| **Mint / Redeem PY** | **SY Converter（Wrap / Unwrap SY）** |
| <img src="截图/Pendle-BSC-Mint铸造赎回PY页-20261013.png" width="460"> | <img src="截图/Pendle-BSC-SY转换页-20261013.png" width="460"> |
| **LP · Zap（含 Keep YT Mode）** | **LP · Manual（双币加 / 减）** |
| <img src="截图/Pendle-BSC-LP-Zap页-20261013.png" width="460"> | <img src="截图/Pendle-BSC-LP-Manual页-20261013.png" width="460"> |
| **PendleSwap 弹窗** | **Dashboard（需连钱包）** |
| <img src="截图/Pendle-BSC-PendleSwap页-20261013.png" width="460"> | <img src="截图/Pendle-BSC-Dashboard页-20261013.png" width="460"> |
| **Bridge PT** | **PT Looping（BNB Chain 0 个市场）** |
| <img src="截图/Pendle-BSC-BridgePT页-20261013.png" width="460"> | <img src="截图/Pendle-BSC-PTLooping页-20261013.png" width="460"> |
| **sPENDLE（仅以太坊）** | **Deploy Pool** |
| <img src="截图/Pendle-BSC-sPENDLE页-20261013.png" width="460"> | <img src="截图/Pendle-BSC-DeployPool页-20261013.png" width="460"> |

**交易详情（每类一张，均为 bscscan）**

| 1 买 PT（`0x45e36b…`） | 2 卖 PT（`0x98c518…`） |
|:-:|:-:|
| <img src="截图/Pendle-BSC-买PT交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-卖PT交易-20261013.png" width="460"> |
| **3 买 YT（`0x43d8f4…`）** | **4 卖 YT（`0xc9802c…`）** |
| <img src="截图/Pendle-BSC-买YT交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-卖YT交易-20261013.png" width="460"> |
| **5 SY 买 PT（`0x2ee4f0…`）** | **5 PT 换 SY（`0x667382…`）** |
| <img src="截图/Pendle-BSC-SY买PT交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-PT换SY交易-20261013.png" width="460"> |
| **5 SY 买 YT（`0x1704ea…`）** | **5 YT 换 SY（`0x08db30…`）** |
| <img src="截图/Pendle-BSC-SY买YT交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-YT换SY交易-20261013.png" width="460"> |
| **6 限价单成交（`0x141a47…`）** | **7 撤单（`0xe03d8f…`）** |
| <img src="截图/Pendle-BSC-限价单成交交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-撤单交易-20261013.png" width="460"> |
| **8 批量撤单（`0x3e676e…`）** | **9 全部撤单（`0x6eb11a…`）** |
| <img src="截图/Pendle-BSC-批量撤单交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-全部撤单交易-20261013.png" width="460"> |
| **10 Zap 加流动性（`0xa1886c…`）** | **11 Keep YT 加流动性（`0x6b979c…`）** |
| <img src="截图/Pendle-BSC-Zap加流动性交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-KeepYT加流动性交易-20261013.png" width="460"> |
| **11 SY Keep YT 加流动性（`0xb51bf9…`）** | **12 双币加流动性（`0xa08ce8…`）** |
| <img src="截图/Pendle-BSC-SY-KeepYT加流动性交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-双币加流动性交易-20261013.png" width="460"> |
| **13 Zap 减流动性（`0x86d090…`）** | **14 双边减流动性（`0xcc6527…`）** |
| <img src="截图/Pendle-BSC-Zap减流动性交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-双币减流动性交易-20261013.png" width="460"> |
| **15 Token 铸 PY（`0xb52c4d…`）** | **15 SY 铸 PY（`0x6dcaf2…`）** |
| <img src="截图/Pendle-BSC-Token铸PY交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-SY铸PY交易-20261013.png" width="460"> |
| **16 PY 赎回 Token（`0x1181fa…`）** | **16 PY 赎回 SY（`0x6dbaee…`）** |
| <img src="截图/Pendle-BSC-PY赎回Token交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-PY赎回SY交易-20261013.png" width="460"> |
| **17 Wrap SY（`0xc3d55f…`）** | **18 Unwrap SY（`0x5b2229…`）** |
| <img src="截图/Pendle-BSC-WrapSY交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-UnwrapSY交易-20261013.png" width="460"> |
| **19 Claim（`0x3431d3…`）** | **19 Claim 旧版（`0xb1c947…`）** |
| <img src="截图/Pendle-BSC-Claim收益奖励交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-Claim旧版交易-20261013.png" width="460"> |
| **19 Multicall Claim（`0x2958d7…`）** | **20 到期退出（`0x05ccf6…`）** |
| <img src="截图/Pendle-BSC-Multicall-Claim交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-到期退出交易-20261013.png" width="460"> |
| **21 转移流动性（`0x347bbf…`）** | **22 PendleSwap（`0x207821…`）** |
| <img src="截图/Pendle-BSC-转移流动性交易-20261013.png" width="460"> | <img src="截图/Pendle-BSC-PendleSwap交易-20261013.png" width="460"> |
| **23 Bridge PT 跨出（`0xc247e8…`）** | |
| <img src="截图/Pendle-BSC-BridgePT跨出交易-20261013.png" width="460"> | |

**连钱包确认（2026-10-13）**

| Dashboard（连钱包，无仓位） |
|:-:|
| <img src="截图/Pendle-BSC-Dashboard连钱包无仓位-20261013.png" width="460"> |

### 开发注意
- **方法名不带市场，必须看事件**：所有前端操作都打到同一个 RouterV4，市场 / PT / YT 地址藏在参数和事件里；买卖 YT 在市场合约层面表现为 PT↔SY 的 Swap 加 YT 合约的 Mint/Burn，只看 Market 的 Swap 事件会把 YT 交易误判成 PT 交易，要以 Router 发出的 SwapYtAndToken / SwapPtAndToken 等事件区分。
- **一笔交易可能包含多个动作**：`multicall` / `callAndReflect` 里会叠加减流动性 + Claim、赎回 + 再买 PT 等；市价单还可能顺带吃掉限价单（#6，同笔里有 Limit Router 事件），解析时要按事件拆分，不能按 tx 方法名算一类。

## 待确认

- #21 `callAndReflect`：SDK 中由「退出旧市场（exitMarket）+ 领取利息与奖励（redeemRewards）+ 转发调用（reflect）」组成，用于换仓；入口在 Dashboard 的持仓行上，需持有 LP 仓位才显示，按钮名称待用有仓位的账户确认
- PendleSwap（#22，走外部聚合器，非 Pendle 自有市场）和 Bridge PT（#23，LayerZero OFT）是否纳入解析范围
- BSC 上 6 个市场流动性都很小（最大 slisBNB 约 $3M，其余 $2K～$380K），业务上是否值得全部接入
