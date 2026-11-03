# EigenLayer — Ethereum

> **链**：Ethereum ｜ 浏览器 https://etherscan.io （交易截图为 https://eth.blockscout.com ）
> **调研日期**：2026-11-03
> 本人实测 2 类（测试钱包 `0x9DA44Afe5bA28aA42301C626155eA66eb544c33c`，2026-11-03 连钱包 Restake 0.0019 stETH 并委托 P2P.org），其余为公开样本；公开样本均为链上公开交易，非本人钱包，均为 EOA 直接调用；全部已确认成功（receipt status=0x1）并核对方法选择器

## 合约

| 项 | 值 |
|---|---|
| 前端 | Dashboard https://app.eigenlayer.xyz/ ｜ Token 列表 https://app.eigenlayer.xyz/token ｜ Token 详情（LST / ERC-20 / EIGEN）https://app.eigenlayer.xyz/token/stETH 、https://app.eigenlayer.xyz/token/EIGEN 等 ｜ Native Restaking https://app.eigenlayer.xyz/token/ETH ｜ Operator 列表 https://app.eigenlayer.xyz/operator ｜ Operator 详情 https://app.eigenlayer.xyz/operator/0x71c6f7ed8c2d4925d0baf16f6a85bb1736d412eb （示例 AltLayer） ｜ AVS https://app.eigenlayer.xyz/avs ｜ Apps https://app.eigenlayer.xyz/apps |
| StrategyManager | `0x858646372CC42E1A627fcE94aa7A7033e7CF075A`（Proxy；LST / ERC-20 / EIGEN 存入） |
| DelegationManager | `0x39053D51B77DC0d36036Fc1fCc8Cb819df8Ef37A`（Proxy；委托、解委托、排队取款、完成取款） |
| EigenPodManager | `0x91E677b07F7AF907ec9a428aafA9fc14a0d3A338`（Proxy；创建 EigenPod，原生质押份额记账） |
| EigenPod | 每个用户一个（BeaconProxy），地址由 EigenPodManager `PodDeployed(eigenPod, podOwner)` 事件给出，或调 `getPod(owner)` 查 |
| RewardsCoordinator | `0x7750d328b314EfFa365A0402CcfD489B80B0adda`（Proxy；领奖励） |
| AllocationManager | `0x948a420b8CC1d6BFd0B6087C2E7c344a2CD0bc39`（Proxy；Operator 向 Operator Set 分配可罚没份额，Staker 前端无直接交互） |
| beaconChainETHStrategy | `0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0`（原生 ETH 的虚拟 Strategy 地址，只出现在参数 / 事件里） |
| EigenStrategy（EIGEN） | `0xaCB55C530Acdb2849e6d4f36992Cd8c9D50ED8F7`；EIGEN `0xec53bF9167f50cDEB3Ae105f56099aaaB9061F83`，bEIGEN `0x83E9115d334D248Ce39a6f36144aEaB5b3456e75` |
| 主要 LST Strategy | stETH `0x93c4b944D05dfe6df7645A86cd2206016c51564D` ｜ rETH `0x1BeE69b7dFFfA4E2d53C2a2Df135C388AD25dCD2` ｜ cbETH `0x54945180dB7943c0ed0FEE7EdaB2Bd24620256bc` ｜ swETH `0x0Fe4F44beE93503346A3Ac9EE5A26b130a5796d6`（均为 StrategyBaseTVLLimits） |
| StrategyFactory | `0x5e4C39Ad7A3E881585e383dB9827EB4811f6F647`（无许可 ERC-20 Strategy 由此部署，Token 页的 PEPE、USDC、AUSD 等走这类 Strategy） |

## 交易类型覆盖

14 类：13 类有样本（本人实测 2 类，公开样本 11 类），1 类 ⬜。

| # | 交易类型 | 前端入口 | 合约 · 方法 | tx hash | 来源 |
|---|---|---|---|---|---|
| 1 | Restake 存入 LST / ERC-20 | Dashboard → Restake → 选资产 → Deposit；Token → 资产详情 → Restake；Operator 详情 → Restake | StrategyManager · `depositIntoStrategy(address strategy, address token, uint256 amount)`（`0xe7a050aa`）；本例 stETH Strategy `0x93c4b944D05dfe6df7645A86cd2206016c51564D`，前置一笔 stETH `approve` 给 StrategyManager（[0x0c6c4a978cec9500ada305c28b712719e368546e50ec13013e70a678aca594d4](https://etherscan.io/tx/0x0c6c4a978cec9500ada305c28b712719e368546e50ec13013e70a678aca594d4)） | [0x6ed2cb5c1d53d93c8a76038ca61b5486ebc38448718b909c4ded66c46be86778](https://etherscan.io/tx/0x6ed2cb5c1d53d93c8a76038ca61b5486ebc38448718b909c4ded66c46be86778) | 本人实测 |
| 2 | Restake 存入 EIGEN | Token → EIGEN → Restake | StrategyManager · `depositIntoStrategy`（strategy = EigenStrategy） | [0xef6987062e87888f37055af418eb68e3949daa640ca59343d851497a5b345c27](https://etherscan.io/tx/0xef6987062e87888f37055af418eb68e3949daa640ca59343d851497a5b345c27) | 公开样本 |
| 3 | Delegate 委托给 Operator | 首次 Restake 时第 2 步「Delegate to an operator」选 Operator → Submit；Operator 详情 → Delegate | DelegationManager · `delegateTo(address operator, (bytes,uint256) approverSignatureAndExpiry, bytes32 approverSalt)`（`0xeea9064b`）；本例 operator = P2P.org [no slashing AVS] `0xDbEd88D83176316fc46797B43aDeE927Dc2ff2F5` | [0x2860025a84492fad8f35aecba504ebc380726d192ace426c4c687aae06ab603e](https://etherscan.io/tx/0x2860025a84492fad8f35aecba504ebc380726d192ace426c4c687aae06ab603e) | 本人实测 |
| 4 | Undelegate 解除委托（自动排队取出全部份额） | Dashboard → Undelegate；Operator 详情 → Undelegate | DelegationManager · `undelegate(address staker)`（`0xda8be864`） | [0xb04ccf2b5ffb3103f61e8db89409195526b6ea3384e2acf262341a33b4a94e0d](https://etherscan.io/tx/0xb04ccf2b5ffb3103f61e8db89409195526b6ea3384e2acf262341a33b4a94e0d) | 公开样本 |
| 5 | Redelegate 换绑 Operator | Operator 详情 → Redelegate（仅「部分委托」状态的用户可见） | DelegationManager · `redelegate(address newOperator, (bytes,uint256), bytes32)`（`0xa33a3433`） | ⬜ 无样本 | 回溯约 100 万个区块（约 4.6 个月），没找到 EOA 直接调用的成功交易 |
| 6 | Unstake 排队取款（LST / ERC-20 / EIGEN） | 资产详情 → Unstake | DelegationManager · `queueWithdrawals((address[] strategies, uint256[] depositShares, address __deprecated_withdrawer)[])`（`0x0dd8dd02`） | [0x980c003da38c6fda939553099041f61edfb83b5fd03aed37702af20a70c3ce8e](https://etherscan.io/tx/0x980c003da38c6fda939553099041f61edfb83b5fd03aed37702af20a70c3ce8e) | 公开样本（swETH） |
| 7 | Native Queue Withdrawal 原生 ETH 排队取款 | Token → ETH → Withdrawals → Queue Withdrawal | DelegationManager · `queueWithdrawals`（strategy = beaconChainETHStrategy） | [0x198334897a104df36a71389d5b0fb3aa081e8932c5b35e70a4c33703eeb63029](https://etherscan.io/tx/0x198334897a104df36a71389d5b0fb3aa081e8932c5b35e70a4c33703eeb63029) | 公开样本 |
| 8 | Withdraw 完成取款（拿回代币） | 资产详情 → Withdraw（托管期满后） | DelegationManager · `completeQueuedWithdrawal(Withdrawal, address[] tokens, bool receiveAsTokens=true)`（`0xe4cc3f90`）；批量为 `completeQueuedWithdrawals`（`0x9435bb43`） | [0x181d53d847fb3e38cf79b0f8142920b1fe779755937384e343ce5053c26d00e6](https://etherscan.io/tx/0x181d53d847fb3e38cf79b0f8142920b1fe779755937384e343ce5053c26d00e6) | 公开样本（swETH） |
| 9 | Redeposit 重新存入（取款转回份额） | 资产详情 → Unstake → Redeposit（托管期满后） | DelegationManager · `completeQueuedWithdrawals(Withdrawal[], address[][], bool[] receiveAsTokens=false)`（`0x9435bb43`），单笔为 `completeQueuedWithdrawal(..., false)` | [0x99a027ed7c43d6f987be0e91aa01109078b8c1dedc7d969ac85a4f0a4686836d](https://etherscan.io/tx/0x99a027ed7c43d6f987be0e91aa01109078b8c1dedc7d969ac85a4f0a4686836d) | 公开样本（3 笔取款全部 false） |
| 10 | Claim Rewards 领奖励 | Dashboard → Claim Rewards → 选代币 → Claim Tokens | RewardsCoordinator · `processClaim(RewardsMerkleClaim claim, address recipient)`（`0x3ccc861d`） | [0x3cd546ed497808b107bbff3258c162455e432ea03d2cd18515b195ee57c74f12](https://etherscan.io/tx/0x3cd546ed497808b107bbff3258c162455e432ea03d2cd18515b195ee57c74f12) | 公开样本（领 EIGEN） |
| 11 | Create EigenPod | Token → ETH → Create EigenPod（连钱包后显示） | EigenPodManager · `createPod()`（`0x84d81062`） | [0xb78702a31f91278bcec425de8b091c3b4d0353a1ce104bb3c33772d873d02320](https://etherscan.io/tx/0xb78702a31f91278bcec425de8b091c3b4d0353a1ce104bb3c33772d873d02320) | 公开样本 |
| 12 | Restake 验证者（验证提款凭证） | Token → ETH → Validators → Unproven 验证者 → Restake | EigenPod（用户自己的 Pod） · `verifyWithdrawalCredentials(uint64, (bytes32,bytes), uint40[], bytes[], bytes32[][])`（`0x3f65cf19`） | [0x166dffd5414c2c1ee3d82bb10005d35f8dea4b414a59ea40527ca8b8509b26bb](https://etherscan.io/tx/0x166dffd5414c2c1ee3d82bb10005d35f8dea4b414a59ea40527ca8b8509b26bb) | 公开样本 |
| 13 | Checkpoint 第 1 步：发起 | Token → ETH → Checkpoint | EigenPod · `startCheckpoint(bool revertIfNoBalance)`（`0x88676cad`） | [0x38d301614ede675de2f732945145c2f7bbe824a5d2169ab60bb91933801f5140](https://etherscan.io/tx/0x38d301614ede675de2f732945145c2f7bbe824a5d2169ab60bb91933801f5140) | 公开样本 |
| 14 | Checkpoint 第 2 步：提交证明 | Checkpoint 流程内自动弹出第 2 笔签名 | EigenPod · `verifyCheckpointProofs((bytes32,bytes), (bytes32,bytes32,bytes)[])`（`0xf074ba62`） | [0xceaaa14ff6a2f46d5e33cd9d31e7619247ec7eceecbec745b56f93214da76d3a](https://etherscan.io/tx/0xceaaa14ff6a2f46d5e33cd9d31e7619247ec7eceecbec745b56f93214da76d3a) | 公开样本（与 #13 是同一 Pod、同一次 Checkpoint） |

页面有入口但**不产生 EigenLayer 交易**：AVS 页、Apps 页（展示与外链）、顶部 Check Status 页、Operator / Token 页的排序和筛选。首次 Restake 前的 ERC-20 `approve` 是代币合约上的标准授权，不单列（hash 见 #1）。Dashboard 的「Manage Balances」是汇总弹窗：Withdrawable → Withdraw 即 #8，按资产 Unstake 即 #6，不产生新的交易类型（2026-11-03 连钱包确认）。Native Restaking 把验证者提款凭证指向 EigenPod 是信标链存款 / Launchpad 操作，不在本前端发起。

## 截图

**前端页面（未连钱包）**

| Dashboard（Undelegate / Claim Rewards 入口） | Claim Rewards 弹窗 |
|:-:|:-:|
| <img src="截图/EigenLayer-ETH-Dashboard页-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-ClaimRewards弹窗-20261103.png" width="460"> |
| **Token 列表** | **LST 详情（stETH，Unstake / Restake）** |
| <img src="截图/EigenLayer-ETH-Token列表页-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-LST详情页stETH-20261103.png" width="460"> |
| **EIGEN 详情（Unstake / Restake）** | **Native Restaking（Checkpoint / Queue Withdrawal）** |
| <img src="截图/EigenLayer-ETH-EIGEN详情页-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-NativeETH页-20261103.png" width="460"> |
| **Operator 列表** | **Operator 详情（Delegate / Restake）** |
| <img src="截图/EigenLayer-ETH-Operator列表页-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-Operator详情页-20261103.png" width="460"> |
| **AVS（展示，无交易）** |  |
| <img src="截图/EigenLayer-ETH-AVS页-20261103.png" width="460"> |  |

**本人实测前端（连钱包，2026-11-03）**

| Restake → 选资产 | Deposit 面板（stETH 0.0019） |
|:-:|:-:|
| <img src="截图/EigenLayer-ETH-Restake选资产连钱包-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-Deposit面板连钱包-20261103.png" width="460"> |
| **Delegate to an operator（选 P2P.org）** | **Deposit & delegate 进度窗（Approve → Deposit → Delegate）** |
| <img src="截图/EigenLayer-ETH-Delegate选Operator连钱包-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-DepositDelegate进度窗-20261103.png" width="460"> |
| **Dashboard（有仓位后 Undelegate / Manage Balances 可点）** | **Manage Balances 弹窗（Withdraw / Unstake）** |
| <img src="截图/EigenLayer-ETH-Dashboard连钱包有仓位-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-ManageBalances弹窗-20261103.png" width="460"> |

**交易详情（每类一张，均为 Blockscout）**

| 1 Restake stETH（本人，`0x6ed2cb…`） | 2 Restake EIGEN（`0xef6987…`） |
|:-:|:-:|
| <img src="截图/EigenLayer-ETH-RestakeStETH本人交易-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-RestakeEIGEN交易-20261103.png" width="460"> |
| **3 Delegate（本人，`0x286002…`）** | **4 Undelegate（`0xb04ccf…`）** |
| <img src="截图/EigenLayer-ETH-Delegate本人交易-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-Undelegate交易-20261103.png" width="460"> |
| **6 Unstake 排队取款（`0x980c00…`）** | **7 Native Queue Withdrawal（`0x198334…`）** |
| <img src="截图/EigenLayer-ETH-Unstake排队取款交易-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-NativeQueueWithdrawal交易-20261103.png" width="460"> |
| **8 Withdraw 完成取款（`0x181d53…`）** | **9 Redeposit（`0x99a027…`）** |
| <img src="截图/EigenLayer-ETH-Withdraw完成取款交易-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-Redeposit交易-20261103.png" width="460"> |
| **10 Claim Rewards（`0x3cd546…`）** | **11 Create EigenPod（`0xb78702…`）** |
| <img src="截图/EigenLayer-ETH-ClaimRewards交易-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-CreateEigenPod交易-20261103.png" width="460"> |
| **12 Restake 验证者（`0x166dff…`）** | **13 Checkpoint 发起（`0x38d301…`）** |
| <img src="截图/EigenLayer-ETH-RestakeValidator交易-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-StartCheckpoint交易-20261103.png" width="460"> |
| **14 Checkpoint 提交证明（`0xceaaa1…`）** | **1 前置 approve stETH（本人，`0x0c6c4a…`）** |
| <img src="截图/EigenLayer-ETH-VerifyCheckpoint交易-20261103.png" width="460"> | <img src="截图/EigenLayer-ETH-授权stETH本人交易-20261103.png" width="460"> |

## 开发注意

- **原生质押的交易 `to` 是用户自己的 EigenPod，不是固定合约**（#12～#14）：先用 EigenPodManager `PodDeployed(eigenPod, podOwner)` 建 Pod→Owner 映射，再按 Pod 地址识别。原生 ETH 的份额变化看 EigenPodManager `PodSharesUpdated` / `NewTotalShares`，排队取款（#7）与 LST 共用 `queueWithdrawals`，只是 strategy 为 `0xbeaC0eeE…BEaC0`。
- **Withdraw 与 Redeposit 是同一个方法，按 `receiveAsTokens` 区分**（#8 / #9）：`true` 时 Strategy 把代币转回钱包；`false` 时没有代币转账，只把份额加回，并且会再抛一次 StrategyManager `Deposit` 事件。所以 `Deposit` 事件≠新存入，要结合方法和代币转账判断。Undelegate（#4）会自动为每个 strategy 抛 `SlashingWithdrawalQueued`，等同于一次性排队取出全部份额。
- **EIGEN 存入在交易内包装成 bEIGEN**（#2）：EIGEN 从用户转到 EigenStrategy 后被销毁，再由 bEIGEN 合约把等量 bEIGEN 转给 Strategy，Strategy 实际持有 bEIGEN。份额按 Strategy 的 shares 计，不是 1:1 代币数量，换算用 Strategy `sharesToUnderlyingView`。

## 待确认

- Redelegate（#5）近 4.6 个月没有 EOA 直接调用的样本，官方文档也建议不要用这个按钮，是否纳入解析范围待定
- 公开样本均为 EOA 直接调用核心合约，链上无法区分是不是从官方前端发起的
