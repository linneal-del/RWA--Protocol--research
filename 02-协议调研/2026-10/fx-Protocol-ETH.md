# f(x) Protocol — Ethereum

> **链**：Ethereum ｜ 浏览器 https://etherscan.io （交易截图为 https://eth.blockscout.com ）
> **调研日期**：2026-09-01 本人实测，2026-10-13 补全交易类型 ｜ 本人实测与公开样本已标注；公开样本均为链上公开交易，非本人钱包，已逐条确认成功

## 合约

| 项 | 值 |
|---|---|
| 前端 | https://fx.aladdin.club （Trade / fxSAVE / fxMINT / Earn / Lock / Stats / Leaderboard）｜ fxUSD Bridge https://bridge.aladdin.club |
| Router Diamond | `0x33636D49FbefBE798e15e7F356E8DBef543CC708`（Trade 开平仓、fxSAVE 存取、稳定池存入） |
| FxMintRouter | `0xB753366082466c4B5984312f0c4Bb97554be067E`（fxMINT 存抵押铸 / 还款） |
| LimitOrderManager | `0x112873b395B98287F3A4db266a58e2D01779Ad96` |
| 仓位池 | WBTCLongPool `0xAB709e26Fa6B0A30c119D8c55B887DeD24952473` ｜ WstETHLongPool `0x6Ecfa38FeE8a5277B91eFdA204c235814F0122E8` |
| fxUSD | `0x085780639CC2cACd35E474e71f4d000e2405d8f6` |
| fxSAVE | `0x7743e50F534a7f9F1791DdE7dCD89F7783Eefc39` |
| 稳定池 fxSP / fxSP Gauge | `0x65C9A641afCEB9C0E6034e558A319488FA0FA3be` ／ `0xEd92dDe3214c24Ae04F5f96927E3bE8f8DbC3289` |
| Curve LP Gauge（示例 ETH+fxUSD） | `0x288810cdbdFEd9EA3Be3cA4E421Ab795FD0669f3`（每个池子各有 Gauge） |
| TokenMinter（FXN 排放） | `0xC8b194925D55d5dE9555AD1db74c149329F71DeF` |
| aFXN（FX² 自动复利池） | `0x00Bac667a4cCf9089aB1Db978238C555C4349545` |
| veFXN ／ FeeDistributor ／ VotingEscrowBoost | `0xEC6B8A3F3605B083F7044C0F31f2cac0caf1d469` ／ `0xd116513EEa4Efe3908212AfBAeFC76cb29245681` ／ `0x8Cc02c0D9592976635E98e6446ef4976567E7A81` |
| fxUSD OFT Adapter（LayerZero） | `0xA07d8cc424421cC2bce0544a65481376f010A438` |

## 交易类型覆盖

共 30 类上链交易，全部有 hash；其中 8 类为本人实测（测试钱包 `0x9DA44Afe5bA28aA42301C626155eA66eb544c33c`）。

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash | 来源 |
|---|---|---|---|---|---|
| 1 | Trade 开多 / 加仓（含加保证金、调杠杆） | Trade → Long → Open（Market） | Router Diamond · `openOrAddPositionFlashLoanV2` | [0x24acba5528caa8d0507bf7a82f132e22c7c3cdd89cc646bd3a5c4724804ad91e](https://etherscan.io/tx/0x24acba5528caa8d0507bf7a82f132e22c7c3cdd89cc646bd3a5c4724804ad91e) | 本人实测 |
| 2 | Trade 平多 / 减仓 | Trade → Long → Close；持仓面板（需连钱包） | Router Diamond · `closeOrRemovePositionFlashLoanV2` | [0xa5b2a21cffa3df6d2695dce7e7f34366afd74e9edac44ba775ea7bb52e801c77](https://etherscan.io/tx/0xa5b2a21cffa3df6d2695dce7e7f34366afd74e9edac44ba775ea7bb52e801c77) | 公开样本 |
| 3 | Trade 开空 / 加仓 | Trade → Short → Open | Router Diamond · `openOrAddShortPositionFlashLoan` | [0x5ffed9f47cdd139b67f3e054c8c05a9f27e94e70df9629396a331bdf3c7aa8e4](https://etherscan.io/tx/0x5ffed9f47cdd139b67f3e054c8c05a9f27e94e70df9629396a331bdf3c7aa8e4) | 公开样本 |
| 4 | Trade 平空 / 减仓 | Trade → Short → Close（需连钱包） | Router Diamond · `closeOrRemoveShortPositionFlashLoan` | [0x44497c1fd08d4289369d614f3bac60a9d9f989f571b4b01492799ad88bf476be](https://etherscan.io/tx/0x44497c1fd08d4289369d614f3bac60a9d9f989f571b4b01492799ad88bf476be) | 公开样本 |
| 5 | 限价单撤单 | Trade → Limit → Open Orders（需连钱包） | LimitOrderManager · `cancelOrder` | [0x267f06a93b87a37fe1a6a1577bcb0ab3afabff52adb2de44aa50a7f44e0bcd9e](https://etherscan.io/tx/0x267f06a93b87a37fe1a6a1577bcb0ab3afabff52adb2de44aa50a7f44e0bcd9e) | 公开样本 |
| 6 | fxSAVE 存入 | fxSAVE → Deposit | Router Diamond · `depositToFxSave` | [0x1f3ccd1d471165c2e2de204cab4cb7627a558b57e222a7eb8eddc27f61e4d353](https://etherscan.io/tx/0x1f3ccd1d471165c2e2de204cab4cb7627a558b57e222a7eb8eddc27f61e4d353) | 本人实测 |
| 7 | fxSAVE 申请取出（排队） | fxSAVE → Withdraw（不勾 1%） | fxSAVE · `requestRedeem` | [0x4a29e2dedeba98cb12c95b1f4354e5a8b74e02420ca5790f2e2bc9be6b49aa08](https://etherscan.io/tx/0x4a29e2dedeba98cb12c95b1f4354e5a8b74e02420ca5790f2e2bc9be6b49aa08) | 公开样本 |
| 8 | fxSAVE 到期领取（fxUSD + USDC） | fxSAVE → Withdraw（需连钱包） | fxSAVE · `claim` | [0x7cbba15d96863112ea6f0a85dec8feb1524ba56b20fb33a55bc882e003a86303](https://etherscan.io/tx/0x7cbba15d96863112ea6f0a85dec8feb1524ba56b20fb33a55bc882e003a86303) | 公开样本 |
| | fxSAVE 到期领取并换成单币 | 同上 | Router Diamond · `redeemFromFxSave` | [0xe4ab7bf3914246946eb2d50d9496010a3b59b6b9125a45df9dc69a5e4330a346](https://etherscan.io/tx/0xe4ab7bf3914246946eb2d50d9496010a3b59b6b9125a45df9dc69a5e4330a346) | 公开样本 |
| 9 | fxSAVE 立即取出（付 1%） | fxSAVE → Withdraw（勾选 1%） | Router Diamond · `instantRedeemFromFxSave` | [0xe0be51062ab44dc46ce66e3894fa3bd64945c90bec2dd2e1f8e5ef9f48182a50](https://etherscan.io/tx/0xe0be51062ab44dc46ce66e3894fa3bd64945c90bec2dd2e1f8e5ef9f48182a50) | 公开样本 |
| 10 | fxMINT 用 USDC 铸 fxUSD | fxMINT → Deposit & Mint（USDC） | Router Diamond · `openOrAddPositionFlashLoanV2`（WBTCLongPool） | [0x9a2c7ec339f1a624909f6186b0fa81c4a0513a2dbbc2891900b6fb98daaf7d72](https://etherscan.io/tx/0x9a2c7ec339f1a624909f6186b0fa81c4a0513a2dbbc2891900b6fb98daaf7d72) | 本人实测 |
| 11 | fxMINT 存抵押（WBTC）铸 fxUSD | fxMINT → Deposit & Mint（WBTC） | FxMintRouter · `borrowFromLong` | [0xc918919b5642b2fda9bb627a918374a67323ceddf771ed97c493d73a1ecfd8b4](https://etherscan.io/tx/0xc918919b5642b2fda9bb627a918374a67323ceddf771ed97c493d73a1ecfd8b4) | 本人实测 |
| 12 | fxMINT 还款取回抵押 | fxMINT → Repay & Withdraw | FxMintRouter · `repayToLong` | [0xef57c08cb79dd10ff66b427ecaf11416a919ed794d91db8f503e859ddfe5f2c4](https://etherscan.io/tx/0xef57c08cb79dd10ff66b427ecaf11416a919ed794d91db8f503e859ddfe5f2c4) | 公开样本 |
| | fxMINT 还款并换出其他币 | 同上 | FxMintRouter · `repayToLongAndZapOut` | [0x29a7bd44a210150cb3b4cbd558f23994ac94290ece2e91a0705857ce5ec44000](https://etherscan.io/tx/0x29a7bd44a210150cb3b4cbd558f23994ac94290ece2e91a0705857ce5ec44000) | 公开样本 |
| 13 | Earn 稳定池存入并质押（USDC → fxSP → Gauge） | Earn → fxUSD Stability Pool Gauge → Deposit | Router Diamond · `depositToFxBaseGaugeV2` | [0xd0b2db450f4903f06b09a3adbcb4985cbdfe92773443d60f9e437c4c3ba19416](https://etherscan.io/tx/0xd0b2db450f4903f06b09a3adbcb4985cbdfe92773443d60f9e437c4c3ba19416) | 本人实测 |
| 14 | Earn 稳定池解押（Gauge → fxSP） | 同上 → Withdraw | fxSP Gauge · `withdraw` | [0x53f3a8c14871d2b87ec6ba9c80ee9b3237aca8197a836a7f166e4b15223994ee](https://etherscan.io/tx/0x53f3a8c14871d2b87ec6ba9c80ee9b3237aca8197a836a7f166e4b15223994ee) | 本人实测 |
| 15 | Earn 稳定池申请赎回（排队 60 分钟） | 同上 → Withdraw（默认） | fxSP · `requestRedeem` | [0xd00239241edd6bf0fd8de7e9e0ddd089fa5d8c84930c79dbea3c56d1455819ca](https://etherscan.io/tx/0xd00239241edd6bf0fd8de7e9e0ddd089fa5d8c84930c79dbea3c56d1455819ca) | 本人实测 |
| 16 | Earn 稳定池到期赎回 | 同上（需连钱包） | fxSP · `redeem` | [0x03598b42155155ca0daf2615a8ec3070541ea9ec19bd69304f346d530f97a43f](https://etherscan.io/tx/0x03598b42155155ca0daf2615a8ec3070541ea9ec19bd69304f346d530f97a43f) | 公开样本 |
| 17 | Earn 稳定池立即赎回（付 1%） | 同上 → Withdraw（勾选 1%） | fxSP · `instantRedeem` | [0x26f8b52934449d8b881aa9fade85b3470395fc39955b65b237a8cf7eeafd6df8](https://etherscan.io/tx/0x26f8b52934449d8b881aa9fade85b3470395fc39955b65b237a8cf7eeafd6df8) | 公开样本 |
| 18 | Earn 稳定池领奖励（wstETH + FXN） | 同上 → Claim Rewards | fxSP Gauge · `claim` | [0xa6e175833035e6914e073990f2a518b10deb7e99713fc2404731f768e9fb3a3c](https://etherscan.io/tx/0xa6e175833035e6914e073990f2a518b10deb7e99713fc2404731f768e9fb3a3c) | 公开样本 |
| 19 | Earn Curve LP 质押 | Earn → Curve 池 → Deposit | LP Gauge · `deposit` | [0xc1577311f86b080f930734402ec67c36e1e0b973e0b03860b3b9efb45fba1ac3](https://etherscan.io/tx/0xc1577311f86b080f930734402ec67c36e1e0b973e0b03860b3b9efb45fba1ac3) | 公开样本 |
| 20 | Earn Curve LP 解押 | 同上 → Withdraw | LP Gauge · `withdraw` | [0x7393d29b9064fbc775ed0fc2a34c0e14735c8df32337097b82b2c85095f6aba2](https://etherscan.io/tx/0x7393d29b9064fbc775ed0fc2a34c0e14735c8df32337097b82b2c85095f6aba2) | 公开样本 |
| 21 | Earn 领 FXN（LP Gauge 排放） | 同上 → Claim FXN | TokenMinter · `mint` | [0x50ad16ea39e59196a2dbe538060c590624e34cb00ce5feb885687c001b75c638](https://etherscan.io/tx/0x50ad16ea39e59196a2dbe538060c590624e34cb00ce5feb885687c001b75c638) | 公开样本 |
| 22 | Earn FX² 自动复利池存入 | Earn → FX² Autocompounding Pool | aFXN · `deposit`（另有 `depositWithFXN` / `depositWithStkCvxFxn`） | [0xc49f1cd8fc661fc8d6645fa1fa59ac8928bcf37caea941737960f25c807881ae](https://etherscan.io/tx/0xc49f1cd8fc661fc8d6645fa1fa59ac8928bcf37caea941737960f25c807881ae) | 公开样本 |
| 23 | Earn FX² 赎回 | 同上 | aFXN · `redeem` | [0x24afa0db445dc882b064340d89bb75b790c6dd034d91319b6d0aa5910b2455f8](https://etherscan.io/tx/0x24afa0db445dc882b064340d89bb75b790c6dd034d91319b6d0aa5910b2455f8) | 公开样本 |
| 24 | Lock 创建 veFXN 锁仓 | Lock → Create Lock | veFXN · `create_lock` | [0x7aa70b9f66019b618d2d1bc82d199ea81e8225c88ded0f00c728962fd77cfeb0](https://etherscan.io/tx/0x7aa70b9f66019b618d2d1bc82d199ea81e8225c88ded0f00c728962fd77cfeb0) | 公开样本 |
| 25 | Lock 追加锁仓数量 | Lock（需连钱包） | veFXN · `increase_amount` | [0xf61df6b4af2752fe4e24f0e4a5225b5e9fd3ba7bb3e9801f4dded67d72fdc5c5](https://etherscan.io/tx/0xf61df6b4af2752fe4e24f0e4a5225b5e9fd3ba7bb3e9801f4dded67d72fdc5c5) | 公开样本 |
| 26 | Lock 延长锁仓时间 | Lock（需连钱包） | veFXN · `increase_unlock_time` | [0xfc44bcc4bb30a79346003260c572d720892719ff91be49ca9aa2f84839658987](https://etherscan.io/tx/0xfc44bcc4bb30a79346003260c572d720892719ff91be49ca9aa2f84839658987) | 公开样本 |
| 27 | Lock 到期提取 FXN | Lock（需连钱包） | veFXN · `withdraw` | [0x9e53d359a57ac61c2735839590c4341d661fb3e1d2696e3dd606f77027909ae8](https://etherscan.io/tx/0x9e53d359a57ac61c2735839590c4341d661fb3e1d2696e3dd606f77027909ae8) | 公开样本 |
| 28 | Lock 领 wstETH 收益 | Lock → wstETH Rewards（需连钱包） | FeeDistributor · `claim` | [0x57e67a96fcdadb76bae879ac07a83606bc6af4b6c95965f64dfd1728ce6c9ff8](https://etherscan.io/tx/0x57e67a96fcdadb76bae879ac07a83606bc6af4b6c95965f64dfd1728ce6c9ff8) | 公开样本 |
| 29 | Lock veFXN 委托（Delegation） | Lock → + New Delegation | VotingEscrowBoost · `boost` | [0x9cf022be81fe751a140d26c569848732b595b8ade00509b8d5932e30566e2c5a](https://etherscan.io/tx/0x9cf022be81fe751a140d26c569848732b595b8ade00509b8d5932e30566e2c5a) | 公开样本 |
| 30 | fxUSD 跨链（LayerZero OFT） | fxUSD Bridge | fxUSD OFT Adapter · `send` | [0x94d11a244b7034c80861b3a77c0f651bfc9272ea3863b36e923400334200213e](https://etherscan.io/tx/0x94d11a244b7034c80861b3a77c0f651bfc9272ea3863b36e923400334200213e) | 公开样本 |

页面有入口但**不产生 f(x) 交易**：限价单挂单 / TP·SL（链下签名，由 keeper 成交）；Stats / Leaderboard / FAQ；fxMINT 的 Mt Pelerin 法币买卖（第三方站外）；Earn 底部 f(x) Earn 外链（Euler / Morpho / Fluid / Spectra 等）；fxMINT 页 Privacy Pools 隐私存入（“here” 跳转第三方 https://privacypools.com/pools/1/fxusd ，在 Privacy Pools 合约上完成，已确认）。

## 截图

**前端页面（本人实测，2026-09-01）**

| Trade 开多 BTC | Trade 持仓面板（5x 多头） |
|:-:|:-:|
| <img src="截图/fx-杠杆开多BTC-20260901.png" width="460"> | <img src="截图/fx-杠杆开多BTC-5x-20260901.png" width="460"> |
| **fxSAVE 存入** | **fxMINT 存 WBTC 铸 fxUSD** |
| <img src="截图/fx-fxSAVE存入-20260901.png" width="460"> | <img src="截图/fx-fxMINT铸fxUSD-20260901.png" width="460"> |
| **Earn 稳定池质押** | **Earn 稳定池解押 / 赎回（60 分钟排队 / 1% 立即）** |
| <img src="截图/fx-Earn质押池-20260901.png" width="460"> | <img src="截图/fx-Earn解押提取-20260901.png" width="460"> |

**前端页面（其余入口）**

| Trade · Limit | fxSAVE · Withdraw（1% 立即到账开关） |
|:-:|:-:|
| <img src="截图/fx-ETH-Trade限价页-20261013.png" width="460"> | <img src="截图/fx-ETH-fxSAVE取出页-20261013.png" width="460"> |
| **fxMINT · Repay & Withdraw** | **Earn（Curve LP / FX²）** |
| <img src="截图/fx-ETH-fxMINT还款提取页-20261013.png" width="460"> | <img src="截图/fx-ETH-Earn页-20261013.png" width="460"> |
| **Lock** | **fxUSD Bridge** |
| <img src="截图/fx-ETH-Lock页-20261013.png" width="460"> | <img src="截图/fx-ETH-fxUSDBridge页-20261013.png" width="460"> |

**连钱包确认**

| fxMINT 页 Privacy Pools 入口（“here” 跳转 privacypools.com） |
|:-:|
| <img src="截图/fx-ETH-PrivacyPools入口-20261013.png" width="460"> |

**交易详情（每类一张，均为 Blockscout；1 / 6 / 10 / 11 / 13 / 14 / 15 为本人交易）**

| 1 开多（`0x24acba…`，本人） | 2 平多（`0xa5b2a2…`） |
|:-:|:-:|
| <img src="截图/fx-ETH-开多交易-20261013.png" width="460"> | <img src="截图/fx-ETH-平多交易-20261013.png" width="460"> |
| **3 开空（`0x5ffed9…`）** | **4 平空（`0x44497c…`）** |
| <img src="截图/fx-ETH-开空交易-20261013.png" width="460"> | <img src="截图/fx-ETH-平空交易-20261013.png" width="460"> |
| **5 限价撤单（`0x267f06…`）** | **6 fxSAVE 存入（`0x1f3ccd…`，本人）** |
| <img src="截图/fx-ETH-限价撤单交易-20261013.png" width="460"> | <img src="截图/fx-ETH-fxSAVE存入交易-20261013.png" width="460"> |
| **7 fxSAVE 申请取出（`0x4a29e2…`）** | **8 fxSAVE 到期领取（`0x7cbba1…`）** |
| <img src="截图/fx-ETH-fxSAVE申请取出交易-20261013.png" width="460"> | <img src="截图/fx-ETH-fxSAVE到期领取交易-20261013.png" width="460"> |
| **8 fxSAVE 领取换单币（`0xe4ab7b…`）** | **9 fxSAVE 立即取出（`0xe0be51…`）** |
| <img src="截图/fx-ETH-fxSAVE领取换单币交易-20261013.png" width="460"> | <img src="截图/fx-ETH-fxSAVE立即取出交易-20261013.png" width="460"> |
| **10 fxMINT 用 USDC 铸（`0x9a2c7e…`，本人）** | **11 fxMINT 存 WBTC 铸（`0xc91891…`，本人）** |
| <img src="截图/fx-ETH-fxMINT用USDC铸本人交易-20261014.png" width="460"> | <img src="截图/fx-ETH-fxMINT铸造交易-20261013.png" width="460"> |
| **12 fxMINT 还款提取（`0xef57c0…`）** | **13 稳定池存入（`0xd0b2db…`，本人）** |
| <img src="截图/fx-ETH-fxMINT还款提取交易-20261013.png" width="460"> | <img src="截图/fx-ETH-稳定池存入交易-20261013.png" width="460"> |
| **14 稳定池解押（`0x53f3a8…`，本人）** | **15 稳定池申请赎回（`0xd00239…`，本人）** |
| <img src="截图/fx-ETH-稳定池解押交易-20261013.png" width="460"> | <img src="截图/fx-ETH-稳定池申请赎回本人交易-20261014.png" width="460"> |
| **16 稳定池到期赎回（`0x03598b…`）** | **17 稳定池立即赎回（`0x26f8b5…`）** |
| <img src="截图/fx-ETH-稳定池到期赎回交易-20261013.png" width="460"> | <img src="截图/fx-ETH-稳定池立即赎回交易-20261013.png" width="460"> |
| **18 稳定池领奖励（`0xa6e175…`）** | **19 LP 质押（`0xc15773…`）** |
| <img src="截图/fx-ETH-稳定池领奖励交易-20261013.png" width="460"> | <img src="截图/fx-ETH-LP质押交易-20261013.png" width="460"> |
| **20 LP 解押（`0x7393d2…`）** | **21 领 FXN（`0x50ad16…`）** |
| <img src="截图/fx-ETH-LP解押交易-20261013.png" width="460"> | <img src="截图/fx-ETH-领FXN交易-20261013.png" width="460"> |
| **22 FX² 存入（`0xc49f1c…`）** | **23 FX² 赎回（`0x24afa0…`）** |
| <img src="截图/fx-ETH-aFXN存入交易-20261013.png" width="460"> | <img src="截图/fx-ETH-aFXN赎回交易-20261013.png" width="460"> |
| **24 创建锁仓（`0x7aa70b…`）** | **25 追加锁仓（`0xf61df6…`）** |
| <img src="截图/fx-ETH-创建锁仓交易-20261013.png" width="460"> | <img src="截图/fx-ETH-追加锁仓交易-20261013.png" width="460"> |
| **26 延长锁仓（`0xfc44bc…`）** | **27 锁仓到期提取（`0x9e53d3…`）** |
| <img src="截图/fx-ETH-延长锁仓交易-20261013.png" width="460"> | <img src="截图/fx-ETH-锁仓到期提取交易-20261013.png" width="460"> |
| **28 领 veFXN 收益（`0x57e67a…`）** | **29 veFXN 委托（`0x9cf022…`）** |
| <img src="截图/fx-ETH-领veFXN收益交易-20261013.png" width="460"> | <img src="截图/fx-ETH-veFXN委托交易-20261013.png" width="460"> |
| **30 fxUSD 跨链（`0x94d11a…`）** | |
| <img src="截图/fx-ETH-fxUSD跨链交易-20261013.png" width="460"> | |

## 开发注意

- **Trade / fxSAVE / 稳定池存入多走同一个 Router Diamond `0x33636D49…`，只能按方法名区分**；多空与标的看目标池（WBTCLongPool / WstETHLongPool / ShortPool），加仓、加保证金、调杠杆复用 open / close 两个方法。仓位以 NFT 形式铸给用户（如 `f(x) WBTC position`）。
- **fxMINT 用 USDC 铸 fxUSD 与 Trade 开多链上完全相同**：同为 `openOrAddPositionFlashLoanV2`、同在 WBTCLongPool 开仓，只差杠杆参数；fxMINT 存 WBTC 抵押铸才走 FxMintRouter `borrowFromLong`。
- **取出都有“排队 / 付 1% 立即到账”两条路**：稳定池是 fxSP `requestRedeem`（60 分钟）→ `redeem`，或 `instantRedeem`；fxSAVE 是 `requestRedeem` → `claim`（或 Router `redeemFromFxSave` 领取并换单币），或 Router `instantRedeemFromFxSave`。

## 待确认

- 限价单 / TP·SL 由 keeper 代为成交，用户地址上只有撤单，解析是否需要覆盖 keeper 成交交易
- fxMINT 用 USDC 铸与 Trade 开多链上不可区分，业务上是否需要区分前端来源
- veFXN 委托（#29）近年几乎无人使用，是否纳入解析范围
