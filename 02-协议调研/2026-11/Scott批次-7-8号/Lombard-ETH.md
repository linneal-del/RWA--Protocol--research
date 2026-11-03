# Lombard — Ethereum

> **链**：Ethereum ｜ 浏览器 https://etherscan.io （交易截图为 https://eth.blockscout.com ）
> **调研日期**：2026-09-01，2026-11-03 补全交易类型
> 本人实测钱包 `0x9DA44Afe5bA28aA42301C626155eA66eb544c33c`（Swap / Bridge / Bitcoin Earn 存入）；其余为公开样本，均为链上公开交易，非本人钱包，已逐条确认成功（receipt status = 0x1，方法选择器已核对）

## 合约

| 项 | 值 |
|---|---|
| 前端 | Portfolio https://www.lombard.finance/app/ ｜ Deposit https://www.lombard.finance/app/deposit/ ｜ LBTC https://www.lombard.finance/app/lbtc/ ｜ Earn（Bitcoin Earn）https://www.lombard.finance/app/earn/ ｜ Onchain Credit https://www.lombard.finance/app/earn/btcoc/ ｜ Borrow https://www.lombard.finance/app/borrow/ ｜ Bridge https://www.lombard.finance/app/bridge/ ｜ Swap https://www.lombard.finance/app/swap/ ｜ BARD 领取 https://claim.lombard.finance （2026-10-29 已关闭） |
| LBTC | `0x8236a87084f8B84306f72007F36F2618A5634494`（Lombard Staked BTC ｜ LBTC ｜ **decimals 8**） |
| BTC.b | `0xB0F70C0bD6FD87dbEb7C10dC692a2a6106817072`（decimals 8） |
| AssetRouter | `0x9eCe5fB1aB62d9075c4ec814b321e24D8EA021ac`（批量铸币、BTC.b→LBTC 转换） |
| StakeAndBake | `0xC8bbF6153D7Ba105f1399D992ebd32B0541996ef`（BTC 存入直接部署进 Bitcoin Earn） |
| Bascule | GMPBasculeV1 `0xC3ecFE771564e3f28CFB7a9b203F4d10279338eD`（铸币校验）；官方文档 Bascule Drawbridge `0xc750eCAC7250E0D18ecE2C7a5F130E3A765dc260` |
| Mailbox / BridgeV2 | Mailbox `0x964677F337d6528d659b1892D0045B8B27183fc0` ｜ BridgeV2 `0x451c54981C7DA5d95901b770C540547cF5FE0A2D` |
| Bridge（Chainlink CCIP） | Router `0x80226fc0Ee2b096224EeAc085Bb9a8cba1146f7D` ｜ LBTC TokenPool `0x626BdEe51E85A127eb6A08ed79c68a4b7AC80b4A`（2026-09 时为 `0x88E18636EfFC3b3cd520FC72B710eb99C0017BC7`）｜ BTC.b TokenPool `0xD7012F3Fda127e45022175b2C92933823A1868ea` ｜ OffRamp `0x408428bca0e24A25ac8baAc1b70f64AF257717c3` |
| Bitcoin Earn | BTCe `0x3a4baaBf4DC9910596821615e848f0e6545762F3`（份额，decimals 8）｜ 底层 BoringVault LBTCv `0x5401b8620E5FB570064CA9114fd1e135fd77D57c` ｜ BoringOnChainQueue `0x4a20F4948c435fDA923399F89800CdC373de88cB` ｜ BoringSolver `0x6300F657a332ec803DB9Fd08d7D9C30470BdFb9f` |
| Onchain Credit | BTCoc `0xf14F678d9c05798ba61652a950a05D74aD2E0A6C`（MultiAssetWithAsyncRedemptionStrategy，收 LBTC / BTC.b） |
| Rewards | BARD `0xf0DB65D17e30a966C2ae6A21f6BBA71cea6e9754` ｜ Season 3 TokenDistributor `0x39D438caF425C31f1a4883e0b399cC4Cc1280135` |
| Swap 聚合器 | LI.FI Diamond `0x1231DEB6f5749EF6cE6943a275A1D3E7486F4EaE` |
| 手续费接收 | `0x251a604e8e8f6906d60f8dedc5aaeb8cd38f4892`（GnosisSafe） |

## 交易类型覆盖

19 类：19 类有样本（本人实测 3 类，公开样本 16 类），0 类 ⬜。

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash | 来源 |
|---|---|---|---|---|---|
| 1 | BTC 存入铸 LBTC（协议批量代铸） | Deposit → 目标 LBTC → 向 BTC 地址转账 | AssetRouter · `batchMintWithFee`（`0x59aae4ba`） | [0xc1f33d528da743957adcac383e4fc20a129a2e395cc49b65e2c6b0b79689b80d](https://etherscan.io/tx/0xc1f33d528da743957adcac383e4fc20a129a2e395cc49b65e2c6b0b79689b80d) | 公开样本 |
| 2 | BTC 存入铸 LBTC（用户自领） | Deposit → 到账后 Claim | LBTC · `mint(bytes,bytes)`（`0x6bc63893`） | [0x1dd638ff1c58a1cafe9b74d6377377d103473606e36a31d18cab4b54f7fa8600](https://etherscan.io/tx/0x1dd638ff1c58a1cafe9b74d6377377d103473606e36a31d18cab4b54f7fa8600) | 公开样本 |
| 3 | BTC 存入并部署进 Bitcoin Earn（协议代发） | Deposit → 目标 Bitcoin Earn | StakeAndBake · `batchStakeAndBake`（`0x2ab73c23`） | [0x63d2d07c851d7186d7a280cc59d9f474a96935ca64c177d260442cc4b55b3828](https://etherscan.io/tx/0x63d2d07c851d7186d7a280cc59d9f474a96935ca64c177d260442cc4b55b3828) | 公开样本 |
| 4 | BTC.b 领取铸币 | Deposit → 目标 BTC.b；或 #6 到账后领取 | BTC.b · `mintV1`（`0x8307f738`） | [0x6baaef1fdd01678ad7eefbb75ffb5d371f3f10964c82a5cb04418f1482be3be1](https://etherscan.io/tx/0x6baaef1fdd01678ad7eefbb75ffb5d371f3f10964c82a5cb04418f1482be3be1) | 公开样本 |
| 5 | BTC.b 转 LBTC | LBTC 页 → Deposit（付 BTC.b） | AssetRouter · `deposit(fromAddress,toToken,amount)`（`0x8340f549`），之后按 #1 / #2 铸 LBTC | [0x3094531bd532dc33013e21565a659470e432494c17e8c649d4a676faab19103a](https://etherscan.io/tx/0x3094531bd532dc33013e21565a659470e432494c17e8c649d4a676faab19103a) | 公开样本 |
| 6 | LBTC 转 BTC.b | LBTC 页 → Withdraw（到账 BTC.b） | LBTC · `redeem(uint256)`（`0xdb006a75`），最长 10 天后按 #4 领 BTC.b | [0x072116beeeee36a681f1d6be27223ca5748d1054fec91680b028217b3d6e0add](https://etherscan.io/tx/0x072116beeeee36a681f1d6be27223ca5748d1054fec91680b028217b3d6e0add) | 公开样本 |
| 7 | LBTC 赎回 BTC（Unstake） | LBTC 页 → Withdraw（到账 BTC） | LBTC · `redeemForBtc(bytes scriptPubkey,uint256)`（`0x30b93d85`） | [0xfdcff57b7e0d8a3e42bf20e6023bf951f5e24db659178dfaefb893c2a7ba676a](https://etherscan.io/tx/0xfdcff57b7e0d8a3e42bf20e6023bf951f5e24db659178dfaefb893c2a7ba676a) | 公开样本 |
| 8 | BTC.b 赎回 BTC | Portfolio / LBTC 页 → Withdraw（资产 BTC.b） | BTC.b · `redeemForBtc(bytes,uint256)`（`0x30b93d85`） | [0xb0da777f80433813d700780a1bed10732233b876065519cb2bd75a0c07fd6eb0](https://etherscan.io/tx/0xb0da777f80433813d700780a1bed10732233b876065519cb2bd75a0c07fd6eb0) | 公开样本 |
| 9 | Swap（USDC → LBTC） | Swap | LI.FI Diamond · `swapTokensMultipleV3ERC20ToERC20`（`0x5fd9ae2e`） | [0x4a13011fa35faca9f349fabceb49fbdb5ba0ff084e9943ee8105d8ef59ada8f5](https://etherscan.io/tx/0x4a13011fa35faca9f349fabceb49fbdb5ba0ff084e9943ee8105d8ef59ada8f5)（5 USDC → 0.00006362 LBTC）<br>[0x78764aed8998ed6ab42748b686b820ca1f87592618633973b0106c74c2b29e9e](https://etherscan.io/tx/0x78764aed8998ed6ab42748b686b820ca1f87592618633973b0106c74c2b29e9e)（10 USDC → 0.00012733 LBTC） | 本人实测 |
| 10 | Bridge 跨出 LBTC（ETH → BSC） | Bridge → From Ethereum，资产 LBTC | CCIP Router · `ccipSend`（`0x96f4e9f9`），LBTC 经 TokenPool → BridgeV2 销毁 | [0xc75e75b858eb46d984facb792cb7081eb950c639dfd5069abe7579047552c010](https://etherscan.io/tx/0xc75e75b858eb46d984facb792cb7081eb950c639dfd5069abe7579047552c010)（0.00006 LBTC） | 本人实测 |
| 11 | Bridge 跨出 BTC.b（ETH → Avalanche） | Bridge → From Ethereum，资产 BTC.b | CCIP Router · `ccipSend`（`0x96f4e9f9`），BTC.b TokenPool | [0x0b99844975ba1151fcee7a15859f1b1bbbcc1ee27abdb6ef26b8ab502a44e6fd](https://etherscan.io/tx/0x0b99844975ba1151fcee7a15859f1b1bbbcc1ee27abdb6ef26b8ab502a44e6fd) | 公开样本 |
| 12 | Bridge 跨入（他链 → ETH 到账） | Bridge → To Ethereum（ETH 侧由 CCIP 执行方代发） | OffRamp · `execute`（`0x3b81ab9b`），铸 LBTC 给收款人 | [0x01031bcd501b1d1c5cbf8e8e993e875c7c33ad5338d08cc7cb055dd01da95085](https://etherscan.io/tx/0x01031bcd501b1d1c5cbf8e8e993e875c7c33ad5338d08cc7cb055dd01da95085) | 公开样本 |
| 13 | Bitcoin Earn 存入（LBTC → BTCe） | Earn → Bitcoin Earn → Deposit | BTCe · `deposit(token,assets,receiver,minShareAmount)`（`0x90d25074`） | [0x608149b74895570700873ebdf5f0e90900a4931cc3b99ce08abc911e476c0467](https://etherscan.io/tx/0x608149b74895570700873ebdf5f0e90900a4931cc3b99ce08abc911e476c0467)（0.00011 LBTC → 0.00010784 BTCe） | 本人实测 |
| 14 | Bitcoin Earn 取回①：BTCe 解包为 LBTCv | Earn → Bitcoin Earn → Withdraw | BTCe · `withdraw(assets,receiver,owner)`（`0xb460af94`；也有 `redeem` `0xba087652`） | [0xf9e48ed969c6a560868bd65506013a1ab7b29a4a4a4dab54b32558de1c7e608c](https://etherscan.io/tx/0xf9e48ed969c6a560868bd65506013a1ab7b29a4a4a4dab54b32558de1c7e608c) | 公开样本 |
| 15 | Bitcoin Earn 取回②：LBTCv 排队换 LBTC | 同上（紧接 #14 的第二笔签名） | BoringOnChainQueue · `requestOnChainWithdraw`（`0x6bb3b476`） | [0x6ff99ee0d26e13d81e86dfc1efbc120f8d040beb284360d19fcb6004b8615c04](https://etherscan.io/tx/0x6ff99ee0d26e13d81e86dfc1efbc120f8d040beb284360d19fcb6004b8615c04) | 公开样本 |
| 16 | Bitcoin Earn 撤销取回申请 | Portfolio → Positions → 待处理取回 | BoringOnChainQueue · `cancelOnChainWithdraw`（`0x4a2dc5e4`） | [0x1297e489eaa4af47b72ed0cad9c7315a12607f475fb643298ee2e0edd50d7d06](https://etherscan.io/tx/0x1297e489eaa4af47b72ed0cad9c7315a12607f475fb643298ee2e0edd50d7d06) | 公开样本 |
| 17 | Onchain Credit 存入 | Earn → Bitcoin Onchain Credit → Deposit | BTCoc · `deposit(depositAsset,assets,receiver,minSharesOut)`（`0x90d25074`） | [0xecd21ce820226e6a006846140322a1beb9a7a9079ddcd25cf7ad097ad2b5a784](https://etherscan.io/tx/0xecd21ce820226e6a006846140322a1beb9a7a9079ddcd25cf7ad097ad2b5a784) | 公开样本 |
| 18 | Onchain Credit 申请赎回 | Onchain Credit → Withdraw | BTCoc · `requestRedeem(shares,owner)`（`0x107703ab`） | [0xd9f02ba5f73261b54cd53b271da62a05d71678c71f3b514ae51118b87bb76642](https://etherscan.io/tx/0xd9f02ba5f73261b54cd53b271da62a05d71678c71f3b514ae51118b87bb76642) | 公开样本 |
| 19 | Rewards 领 BARD | Rewards 页 → claim.lombard.finance（Season 3 已于 2026-10-29 关闭，Rewards 入口已下线） | TokenDistributor · `claim(account,amount,merkleProof)`（`0x3d13f874`；另有 `claimAndStake` `0xaa5a6d81`） | [0xc75e660392a97ae76752ba17b531f85c69ae7e5f942f2ce08144d39fb0900747](https://etherscan.io/tx/0xc75e660392a97ae76752ba17b531f85c69ae7e5f942f2ce08144d39fb0900747) | 公开样本 |

页面有入口但**不产生 Lombard 合约交易**：Borrow（跳转 Aave / Spark / Morpho / Jupiter 市场，交易在外部协议）、Portfolio（持仓展示）、Bitcoin Earn 的 BARD 奖励（经 Merkl 领取，外部合约）、$BARD 质押（2026-09-17 停止，解押转到 Mellow 前端）。运营方交易不计入：Bitcoin Earn 取回的执行 BoringSolver · `boringRedeemSolve`（如 [0x30bbb2d7…](https://etherscan.io/tx/0x30bbb2d7afb34a123dd8c6ac61a6c7e4246e16d379dc351ce8f720454634eace)），Onchain Credit 赎回的执行 BTCoc · `fulfillRedeemRequests`（如 [0x2a7e882e…](https://etherscan.io/tx/0x2a7e882e482ebb6a10a662336858f92e5fa93fcce32c0393f3381b8725034728)，直接付 BTC.b 给用户）。

## 截图

**前端页面**

| Deposit 流程（BTC 收款地址，2026-09-01） | LBTC 页（Deposit / Withdraw，2026-09-01） |
|:-:|:-:|
| <img src="截图/Lombard-deposit流程-20260901.png" width="460"> | <img src="截图/Lombard-LBTC页-20260901.png" width="460"> |
| **Strategies（LBTC / Bitcoin Earn / Onchain Credit，2026-09-01）** | **Portfolio 首页（当前导航，2026-11-03）** |
| <img src="截图/Lombard-strategies-20260901.png" width="460"> | <img src="截图/Lombard-ETH-Portfolio首页-20261103.png" width="460"> |
| **Bitcoin Earn 存入（2026-09-01）** | **Bitcoin Earn 取回（Withdraw tab，2026-09-01）** |
| <img src="截图/Lombard-BitcoinEarn存入-20260901.png" width="460"> | <img src="截图/Lombard-BitcoinEarn取回-20260901.png" width="460"> |
| **Onchain Credit 产品页（BTCoc）** | **Borrow（外部借贷市场列表）** |
| <img src="截图/Lombard-ETH-OnchainCredit产品页-20261103.png" width="460"> | <img src="截图/Lombard-ETH-Borrow页-20261103.png" width="460"> |
| **Bridge（LBTC，2026-09-01）** | **Bridge（当前默认 BTC.b，2026-11-03）** |
| <img src="截图/Lombard-bridge-20260901.png" width="460"> | <img src="截图/Lombard-ETH-Bridge页-20261103.png" width="460"> |
| **Swap（USDC → LBTC，2026-09-01）** | **Swap（当前 ETH → LBTC，2026-11-03）** |
| <img src="截图/Lombard-swap-20260901.png" width="460"> | <img src="截图/Lombard-ETH-Swap页-20261103.png" width="460"> |

**交易详情（公开样本 + #10，均为 Blockscout）**

| 1 协议批量铸 LBTC（`0xc1f33d…`） | 2 用户自领 LBTC（`0x1dd638…`） |
|:-:|:-:|
| <img src="截图/Lombard-ETH-协议批量铸LBTC交易-20261103.png" width="460"> | <img src="截图/Lombard-ETH-用户自领LBTC交易-20261103.png" width="460"> |
| **3 存入并部署 Bitcoin Earn（`0x63d2d0…`）** | **4 BTC.b 领取铸币（`0x6baaef…`）** |
| <img src="截图/Lombard-ETH-存入并部署BitcoinEarn交易-20261103.png" width="460"> | <img src="截图/Lombard-ETH-BTCb领取铸币交易-20261103.png" width="460"> |
| **5 BTC.b 转 LBTC（`0x309453…`）** | **6 LBTC 转 BTC.b（`0x072116…`）** |
| <img src="截图/Lombard-ETH-BTCb转LBTC交易-20261103.png" width="460"> | <img src="截图/Lombard-ETH-LBTC转BTCb交易-20261103.png" width="460"> |
| **7 LBTC 赎回 BTC（`0xfdcff5…`）** | **8 BTC.b 赎回 BTC（`0xb0da77…`）** |
| <img src="截图/Lombard-ETH-LBTC赎回BTC交易-20261103.png" width="460"> | <img src="截图/Lombard-ETH-BTCb赎回BTC交易-20261103.png" width="460"> |
| **10 Bridge 跨出 LBTC（`0xc75e75…`，本人实测）** | **11 Bridge 跨出 BTC.b（`0x0b9984…`）** |
| <img src="截图/Lombard-ETH-Bridge跨出LBTC交易-20261103.png" width="460"> | <img src="截图/Lombard-ETH-Bridge跨出BTCb交易-20261103.png" width="460"> |
| **12 Bridge 跨入（`0x01031b…`）** | **14 Bitcoin Earn 解包（`0xf9e48e…`）** |
| <img src="截图/Lombard-ETH-Bridge跨入交易-20261103.png" width="460"> | <img src="截图/Lombard-ETH-BitcoinEarn解包交易-20261103.png" width="460"> |
| **15 Bitcoin Earn 取回申请（`0x6ff99e…`）** | **16 Bitcoin Earn 撤销取回（`0x1297e4…`）** |
| <img src="截图/Lombard-ETH-BitcoinEarn取回申请交易-20261103.png" width="460"> | <img src="截图/Lombard-ETH-BitcoinEarn撤销取回交易-20261103.png" width="460"> |
| **17 Onchain Credit 存入（`0xecd21c…`）** | **18 Onchain Credit 申请赎回（`0xd9f02b…`）** |
| <img src="截图/Lombard-ETH-OnchainCredit存入交易-20261103.png" width="460"> | <img src="截图/Lombard-ETH-OnchainCredit申请赎回交易-20261103.png" width="460"> |
| **19 领 BARD（`0xc75e66…`）** |  |
| <img src="截图/Lombard-ETH-领BARD奖励交易-20261103.png" width="460"> |  |

## 开发注意

- **BTC 存入不是以太坊交易**：用户向一次性、有时效的 BTC 地址转账（比特币网络），约 60 分钟后才在 ETH 上铸 LBTC；ETH 上只能看到协议铸币（#1 / #3 的 `tx.from` 是 relayer `0xCd1b5b2E6C1ff8b606cF4B5731e2f3361474c01b`，不是用户），受益人要看 LBTC `Transfer(0x0 → 用户)` 的 `to`。#3 一笔内连续完成铸 LBTC → 存 BTCe，资产路径是 BTC → LBTC → BTCe。
- **铸币事件里混有手续费**：赎回（#6 / #7 / #8，固定 0.0001 LBTC / BTC.b）、批量铸币（#1）、存入并部署（#3）、Onchain Credit 存入（#17，铸 BTCoc）都会向 `0x251a604e8e8f6906d60f8dedc5aaeb8cd38f4892` 铸手续费。按「从 0x0 铸出 = 用户申购」解析会把手续费计成申购，铸币事件须排除该地址。
- **按入口合约 + 方法判类型，不靠 Transfer**：LBTC / BTC.b 铸销在铸币、赎回、跨链里都出现。跨链出站统一走 CCIP Router `ccipSend`，按 TokenPool 区分 LBTC / BTC.b，TokenPool 会随版本换地址（#10 走旧池 `0x88E1…`）；Bitcoin Earn 取回是两笔用户交易（#14 BTCe → LBTCv，#15 LBTCv 排队），LBTC 由 BoringSolver 批量付出，`tx.from` 不是用户。

## 待确认

- 测试环境 IP 访问 LBTC / Earn 页显示 "Product unavailable"，#5～#8、#14～#18 的前端入口按前端代码与官方文档整理，需可访问地区的钱包实测确认按钮位置
- #6 LBTC 转 BTC.b 到账后由用户 `mintV1` 自领还是由协议批量代发（BTC.b 也有 `batchMintV1WithFee`）
- Onchain Credit 赎回用户是否还需二次领取（目前样本显示运营方 `fulfillRedeemRequests` 直接付 BTC.b）
