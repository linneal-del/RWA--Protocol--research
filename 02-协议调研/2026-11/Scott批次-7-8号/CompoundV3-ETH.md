# Compound V3（Comet）— Ethereum

> **链**：Ethereum 主网 ｜ 浏览器 https://etherscan.io （交易截图为 https://eth.blockscout.com ）
> **调研日期**：2026-11-03 ｜ 本协议无本人实测交易，全部为公开样本；公开样本均为链上公开交易，非本人钱包（发起方均为 EOA），已逐条用 RPC 确认 status=0x1 并核对方法选择器

## 合约

| 项 | 值 |
|---|---|
| 前端 | Dashboard https://app.compound.xyz/?market=usdc-mainnet （`market=` 切换市场：`usdc-mainnet` / `weth-mainnet` / `usdt-mainnet` / `usds-mainnet` / `wbtc-mainnet` / `wsteth-mainnet` / `usdc-institutional-mainnet`） ｜ Markets https://app.compound.xyz/markets ｜ 市场详情 https://app.compound.xyz/markets/usdc-mainnet ｜ Rewards https://app.compound.xyz/rewards ｜ Extensions https://app.compound.xyz/extensions ｜ Vote https://app.compound.xyz/vote （原域名 https://app.compound.finance 已跳转到 app.compound.xyz） |
| cUSDCv3（USDC 市场） | `0xc3d688B66703497DAA19211EEdff47f25384cdc3` |
| cWETHv3（ETH 市场，base 为 WETH，前端显示 ETH） | `0xA17581A9E3356d9A858b789D68B4d866e593aE94` |
| cUSDTv3（USDT 市场） | `0x3Afdc9BCA9213A35503b077a6072F3D0d5AB0840` |
| cUSDSv3（USDS 市场） | `0x5D409e56D886231aDAf00c8775665AD0f9897b56` |
| cWBTCv3（WBTC 市场，当前暂停） | `0xe85Dc543813B8c2CFEaAc371517b925a166a9293` |
| cwstETHv3（wstETH 市场） | `0x3D0bb1ccaB520A66e607822fC55BC921738fAFE3` |
| cUSDCv3 Institutional（USDC Institutional 市场） | `0x207158a267CBD2598BB3d611D8CBdEE2709F2F8C` |
| Bulker | MainnetBulker `0xa397a8C2086C554B531c02E29f3291c9704B00c7`（上面 6 个市场共用） ｜ MainnetBulkerWithWstETHSupport `0x2c776041CCFe903071AF44aa147368a9c8EEA518`（仅 cwstETHv3） |
| CometRewards | `0x1B0e765F6224C21223AeA2af16c1C46E38885a40`（6 个老市场） ｜ `0x561e8E1E7EB56f558922C198A3C228545093f32d`（Institutional） |
| Configurator | `0x316f9708bB98af7dA9c68C1C3b5e79039cD336E3`（6 个老市场） ｜ `0xd61c0169e931381fb3cC4B40316805333808c1Fa`（Institutional） |
| COMP | `0xc00e94Cb662C3520282E6f5717214004A7f26888` |

地址来源：compound-finance/comet `deployments/mainnet/*/roots.json`；Institutional 市场 GitHub 未收录，取自前端代码中的市场配置，并在 Blockscout 核对为 `CometWithExtendedAssetList` 代理。Comet 合约本身即 cToken（ERC-20），无单独 cToken 合约。

## 交易类型覆盖

17 类：16 类有公开样本，1 类 ⬜。

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash | 来源 |
|---|---|---|---|---|---|
| 1 | 授权资产（Enable） | Dashboard → 资产首次 Supply / Repay 前的 Enable | 资产 ERC-20 · `approve(address,uint256)`（`0x095ea7b3`，spender = Comet） | [0x9c346b94516b294ce53d9c12ec77440e85495795361fcc10f848d55a20fdf7d7](https://etherscan.io/tx/0x9c346b94516b294ce53d9c12ec77440e85495795361fcc10f848d55a20fdf7d7) | 公开样本 |
| 2 | Supply base（存款生息） | Dashboard → Supply USDC | Comet · `supply(address,uint256)`（`0xf2b9fdb8`，asset = base；事件 `Supply` + `Transfer` 从 0x0 铸出） | [0xde532379230bb5db9c29f4d7dcd32ea0dd35eaccfd36a7ec6ca9d26475f51fa3](https://etherscan.io/tx/0xde532379230bb5db9c29f4d7dcd32ea0dd35eaccfd36a7ec6ca9d26475f51fa3) | 公开样本 |
| 3 | Supply collateral（存抵押） | Dashboard → 抵押资产行「+」 | Comet · `supply(address,uint256)`（asset = 抵押品；事件 `SupplyCollateral`） | [0xf06c69f6f773a1c3c4fffc9d133b41c75055644ac38fe54871a7d67869703b66](https://etherscan.io/tx/0xf06c69f6f773a1c3c4fffc9d133b41c75055644ac38fe54871a7d67869703b66) | 公开样本 |
| 4 | Repay（还款） | Dashboard → Repay（需连钱包，有借款时出现） | Comet · `supply(address,uint256)`（asset = base；事件只有 `Supply`，无 `Transfer` 铸出） | [0x761350a05ed4ad949150b2476ab793aa511a0750c1f1239aa954a172f1ce43cf](https://etherscan.io/tx/0x761350a05ed4ad949150b2476ab793aa511a0750c1f1239aa954a172f1ce43cf) | 公开样本 |
| 5 | Withdraw base（取存款） | Dashboard → Withdraw（需连钱包，有存款时出现） | Comet · `withdraw(address,uint256)`（`0xf3fef3a3`，asset = base；事件 `Withdraw` + `Transfer` 销毁到 0x0） | [0x1bf13abd2b3985ef22075a1459598ee8f2f534a53dea7ed80b58cb0815685d52](https://etherscan.io/tx/0x1bf13abd2b3985ef22075a1459598ee8f2f534a53dea7ed80b58cb0815685d52) | 公开样本 |
| 6 | Borrow（借款） | Dashboard → Borrow USDC | Comet · `withdraw(address,uint256)`（asset = base；事件只有 `Withdraw`，无 `Transfer` 销毁） | [0x51bad788a3db0585198527a786f2cbb9ebcb5f3ea6ef1ce0bbf116543975b794](https://etherscan.io/tx/0x51bad788a3db0585198527a786f2cbb9ebcb5f3ea6ef1ce0bbf116543975b794) | 公开样本 |
| 7 | Withdraw collateral（取抵押） | Dashboard → 抵押资产行「−」 | Comet · `withdraw(address,uint256)`（asset = 抵押品；事件 `WithdrawCollateral`） | [0x08248fbb6a95d7831fb34b82ea724af2e9766c9f19c5c44fac7c366e69ff6d72](https://etherscan.io/tx/0x08248fbb6a95d7831fb34b82ea724af2e9766c9f19c5c44fac7c366e69ff6d72) | 公开样本 |
| 8 | 授权 Bulker（Enable Operator） | Extensions → Advanced Transactions → Enable；首次用 ETH / stETH 或多动作时前端也会先弹出 | Comet · `allow(address,bool)`（`0x110496e5`，manager = Bulker；事件 `Approval`） | [0xd6ee8d3910326e336ec7fe5a286ff9c37a75cf6a82e9f33f254b6c0ddc9c44a4](https://etherscan.io/tx/0xd6ee8d3910326e336ec7fe5a286ff9c37a75cf6a82e9f33f254b6c0ddc9c44a4) | 公开样本 |
| 9 | 存 ETH 作抵押（经 Bulker） | USDC 等市场 → Ether 行「+」 | Bulker · `invoke(bytes32[],bytes[])`（`0x555029a6`）= `ACTION_SUPPLY_NATIVE_TOKEN`；cUSDCv3 发 `SupplyCollateral`（WETH） | [0xa3bae020b21b15d58af0dd025a4d30c11e5b2d149e8406c75210ede82d1979fc](https://etherscan.io/tx/0xa3bae020b21b15d58af0dd025a4d30c11e5b2d149e8406c75210ede82d1979fc) | 公开样本 |
| 10 | 存 ETH 为 base（ETH 市场存款 / 还款，经 Bulker） | ETH 市场 → Supply ETH / Repay | Bulker · `invoke` = `ACTION_SUPPLY_NATIVE_TOKEN`；cWETHv3 发 `Supply` + `Transfer` 铸出 | [0xf7f6c37a25f64b6a58e8119f7c3b88ddcd69e0af9ddd62ba1d37c0ad9193c720](https://etherscan.io/tx/0xf7f6c37a25f64b6a58e8119f7c3b88ddcd69e0af9ddd62ba1d37c0ad9193c720) | 公开样本 |
| 11 | 取 ETH 抵押（经 Bulker） | USDC 等市场 → Ether 行「−」 | Bulker · `invoke` = `ACTION_WITHDRAW_NATIVE_TOKEN`；cUSDCv3 发 `WithdrawCollateral` | [0x495ab2e7f68ec1e952415318503215363f0f397ee95195d884dbb54dbba18200](https://etherscan.io/tx/0x495ab2e7f68ec1e952415318503215363f0f397ee95195d884dbb54dbba18200) | 公开样本 |
| 12 | 取 ETH base（ETH 市场取款 / 借 ETH，经 Bulker） | ETH 市场 → Withdraw / Borrow ETH | Bulker · `invoke` = `ACTION_WITHDRAW_NATIVE_TOKEN`；cWETHv3 发 `Withdraw` + `Transfer` 销毁（借款时无销毁） | [0x2875f5fc37646c2fcee516449646153ef932e7389c13727ddaa174a2f475927a](https://etherscan.io/tx/0x2875f5fc37646c2fcee516449646153ef932e7389c13727ddaa174a2f475927a) | 公开样本 |
| 13 | 存 stETH（自动包成 wstETH 作抵押，经 Bulker） | 市场 → wstETH 行「+」→ 选 stETH（需连钱包） | Bulker · `invoke` = `ACTION_SUPPLY_STETH`；cUSDCv3 发 `SupplyCollateral`（wstETH） | [0x6ebc1ab2fa3250641f7206d1cace3bd15d9984326c73455e5a4f4793a810e29b](https://etherscan.io/tx/0x6ebc1ab2fa3250641f7206d1cace3bd15d9984326c73455e5a4f4793a810e29b) | 公开样本 |
| 14 | 取回为 stETH（经 Bulker） | 市场 → wstETH 行「−」→ 选 stETH（需连钱包） | Bulker · `invoke` = `ACTION_WITHDRAW_STETH` | ⬜ 无样本 | MainnetBulker 2025-01 至今约 3.9 万笔 `invoke` 中无此动作 |
| 15 | 多动作打包（存抵押 + 借款） | Dashboard 连续加入多个操作后一次提交 | Bulker · `invoke` = `ACTION_SUPPLY_ASSET` + `ACTION_WITHDRAW_ASSET`；cUSDCv3 发 `SupplyCollateral` + `Withdraw` | [0xd920f518762dd29bdb3f3d69c87f512e75e9a80412382758fa47b8468ffecdcf](https://etherscan.io/tx/0xd920f518762dd29bdb3f3d69c87f512e75e9a80412382758fa47b8468ffecdcf) | 公开样本 |
| 16 | 领 COMP（单市场） | 顶部奖励下拉 → Claim（需连钱包） | CometRewards · `claim(address comet,address src,bool shouldAccrue)`（`0xb7034f7e`；事件 `RewardClaimed`） | [0x9f4925af078e1fb68f4eb9b106b1b138a3038edfabc4ba1bb772d19ae70272e5](https://etherscan.io/tx/0x9f4925af078e1fb68f4eb9b106b1b138a3038edfabc4ba1bb772d19ae70272e5) | 公开样本 |
| 17 | 领 COMP（多市场一次领，经 Bulker） | 顶部奖励下拉 → 多个市场一起 Claim | Bulker · `invoke` = `ACTION_CLAIM_REWARD` ×N（本例 cUSDCv3 + cUSDTv3）；CometRewards 发 `RewardClaimed` ×2 | [0xe569bc7170ef7807cd0f61fb60948a4e8376726f06089c77e5b4e19e9e2de737](https://etherscan.io/tx/0xe569bc7170ef7807cd0f61fb60948a4e8376726f06089c77e5b4e19e9e2de737) | 公开样本 |

页面有入口但**不产生 Comet 交易**：Rewards 页（COMP 奖励已改由 Merkl 发放，按钮跳到 app.merkl.xyz 领取，链上走 Merkl 合约）、Markets / 市场详情页（只展示数据）、Vote 页（委托与投票走 COMP 代币和 Governor 合约，属治理）、Extensions → Comp.Vote（链下签名投票）、Extensions → DeFi Saver（授权同 #8 的 `allow`，manager 换成 DeFi Saver 合约，后续操作在 DeFi Saver 站内完成）。未连钱包时 Supply / Borrow 按钮为禁用状态。

## 截图

**前端页面**

| Dashboard（USDC 市场） | Dashboard（ETH 市场） |
|:-:|:-:|
| <img src="截图/CompoundV3-ETH-Dashboard页USDC-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Dashboard页ETH-20261103.png" width="460"> |
| **Markets（全部市场）** | **市场详情（USDC）** |
| <img src="截图/CompoundV3-ETH-Markets页-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-市场详情页USDC-20261103.png" width="460"> |
| **Extensions** | **Extensions → Advanced Transactions（Bulker）** |
| <img src="截图/CompoundV3-ETH-Extensions页-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Extensions-AdvancedTransactions-20261103.png" width="460"> |
| **Rewards（跳 Merkl 领取）** | **Vote（治理）** |
| <img src="截图/CompoundV3-ETH-Rewards页-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Vote页-20261103.png" width="460"> |

**交易详情（每类一张，均为 Blockscout）**

| 1 授权资产（`0x9c346b…`） | 2 Supply base（`0xde5323…`） |
|:-:|:-:|
| <img src="截图/CompoundV3-ETH-Approve授权资产交易-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-SupplyBase存款交易-20261103.png" width="460"> |
| **3 Supply collateral（`0xf06c69…`）** | **4 Repay（`0x761350…`）** |
| <img src="截图/CompoundV3-ETH-SupplyCollateral存抵押交易-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Repay还款交易-20261103.png" width="460"> |
| **5 Withdraw base（`0x1bf13a…`）** | **6 Borrow（`0x51bad7…`）** |
| <img src="截图/CompoundV3-ETH-WithdrawBase取款交易-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Borrow借款交易-20261103.png" width="460"> |
| **7 Withdraw collateral（`0x08248f…`）** | **8 授权 Bulker（`0xd6ee8d…`）** |
| <img src="截图/CompoundV3-ETH-WithdrawCollateral取抵押交易-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Allow授权Bulker交易-20261103.png" width="460"> |
| **9 存 ETH 抵押（`0xa3bae0…`）** | **10 存 ETH 为 base（`0xf7f6c3…`）** |
| <img src="截图/CompoundV3-ETH-Bulker存ETH抵押交易-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Bulker存ETH为Base交易-20261103.png" width="460"> |
| **11 取 ETH 抵押（`0x495ab2…`）** | **12 取 ETH base（`0x2875f5…`）** |
| <img src="截图/CompoundV3-ETH-Bulker取ETH抵押交易-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Bulker取ETHBase交易-20261103.png" width="460"> |
| **13 存 stETH（`0x6ebc1a…`）** | **15 存抵押 + 借款（`0xd920f5…`）** |
| <img src="截图/CompoundV3-ETH-Bulker存stETH交易-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Bulker存抵押加借款交易-20261103.png" width="460"> |
| **16 领 COMP（`0x9f4925…`）** | **17 多市场领 COMP（`0xe569bc…`）** |
| <img src="截图/CompoundV3-ETH-ClaimCOMP交易-20261103.png" width="460"> | <img src="截图/CompoundV3-ETH-Bulker多市场ClaimCOMP交易-20261103.png" width="460"> |

## 开发注意

- **同一个 `supply` / `withdraw` 方法对应 6 种业务，要按 asset 参数 + 事件区分**：asset = base 时，`supply` 是存款生息（#2，有 `Transfer` 铸出）或还款（#4，无铸出），`withdraw` 是取存款（#5，有 `Transfer` 销毁）或借款（#6，无销毁）；Comet 会先还债再记存款、先扣存款再记借款，所以一笔里可能同时含两部分，金额以 `Transfer` 铸出 / 销毁量与 `Supply` / `Withdraw` 金额之差拆分。asset = 抵押品时只发 `SupplyCollateral` / `WithdrawCollateral`，抵押品不生息、不铸 cToken。
- **Bulker `invoke` 要逐个解动作**：`actions` 是 bytes32 字符串（如 `"ACTION_SUPPLY_NATIVE_TOKEN"` 右补零，不是哈希），`data[i]` 第一个参数是 Comet 地址。ETH / stETH 动作由 Bulker 先包成 WETH / wstETH 再存，或取出后再解包，所以 Comet 事件里 Bulker 是中间方：存入时 `from` = Bulker、`dst` = 用户，取出时 `src` = 用户、`to` = Bulker。ERC-20 动作（`ACTION_SUPPLY_ASSET` / `ACTION_WITHDRAW_ASSET`）走 `supplyFrom` / `withdrawFrom`，事件两端都是用户。用户归属统一取存入事件的 `dst`、取出事件的 `src`。cwstETHv3 用独立的 Bulker `0x2c776041…`，两个 Bulker 都要监听。
- **7 个 Comet 都是代理，事件结构一致**：Institutional 市场是前端标「New」的新市场，GitHub deployments 未收录，它的 CometRewards / Configurator 也是独立地址。

## 待确认

- COMP 奖励已改由 Merkl 发放（连钱包后 Rewards 页只引导去 Merkl 领取），CometRewards `claim` 近期抽查 14 笔只有 2 笔真正领到 COMP（其余无 `RewardClaimed`），领 COMP（#16 / #17）是否还需纳入解析
- 取回为 stETH（#14）前端是否仍提供：MainnetBulker 2025-01 至今无 `ACTION_WITHDRAW_STETH`，需连钱包确认 wstETH 行「−」是否还有 stETH 选项
- cWBTCv3 当前处于暂停状态（前端公告），是否仍纳入解析范围
