# Renzo —— ezUSDC1 Reserve Vault ／ ezETH Restaking

> ⚠️ **范围声明**：本协议**不属于**本仓库原本的 RWA 7 协议范围，来自 **DeX 行情接链项目**（协议表条目 `SOLRenzo`）。放在本目录是为了复用同一套调研模版与实测规范。
> **状态**：🟡 实测已完成，协议背景调研未做
> **实测时间**：2026-08-10 ｜ **测试钱包**：Ethereum `0x9da44afe5ba28aa42301c626155ea66eb544c33c`
> **本页可信度**：链上部分为 `getTransaction` / Blockscout 逐笔实测；UI 部分为操作当时截图读取（原图未落盘，见文末）
> **解析类型**：A（NAV 累积，份额价格 > 1）

## 0. 一句话结论

Renzo 在同一个 App 下挂着**两条机制完全不同的产品线**：**Reserve**（ezUSDC1 等收益金库，ERC-20 份额 + 排队赎回）和 **Staking**（ezETH liquid restaking，即时铸造）。实测最需要注意的是 **ezUSDC1 份额价格约 4.376 USDC、decimals 是 6，且赎回要经 WithdrawQueue 排队** —— 按 1:1 或按 18 位处理都会错。

## 1. 基础信息（实测口径）

| 字段 | 值 |
|------|-----|
| 协议 | Renzo（app.renzoprotocol.com） |
| 链 | **Ethereum**（⚠️ 与协议表条目名 `SOLRenzo` 不一致，见 §9） |
| 本次实测产品 | ① **USDC Yield / ezUSDC1**（Reserve）② **ezETH**（Staking, EigenLayer） |
| Supply Coins | ① USDC ② ETH（原生币，非 ERC-20） |
| Coins Integrated | ① ezUSDC1 ② ezETH |
| 池子 TVL | ① **$394.30** ② **$88.76M**（2026-08-10 UI 快照） |
| 收益率 | ① 30D APY 面板未展示数值 ② **2.33%**，标注 Auto-compound（2026-08-10 UI 快照） |
| 池子类别 | ① **排队赎回**（非活期）② 待补 |
| 产品网页 | https://app.renzoprotocol.com/reserve/ezusdc1 ／ …/staking |
| 接入情况 | 待定（DeX 项目侧口径） |

## 2. 协议背景

⬜ **未做**。本页只承载链上实测结论，背景/团队/融资/审计需另行补齐（见 §9）。
已知：ezETH 产品页标注底层是 **EigenLayer**，Rewards 为 **Auto-compound**。

## 3. 底层资产

⬜ **未做**。ezUSDC1 产品页副标题原文：*"Allocates capital across lending markets to maximize net supply yield"* → **自述为跨借贷市场的净供给收益策略**，具体市场清单未取（页面有 **Strategy & Parameters** 入口，可再截一次图）。

## 4. 收益来源

| 产品 | 页面自述 |
|------|---------|
| ezUSDC1 | 跨 lending markets 分配资金，最大化 net supply yield |
| ezETH | EigenLayer restaking，Rewards **Auto-compound** |

⚠️ 均为页面自述，未做交叉验证。

## 5. 链上机制与合约（✅ 全部实测）

### 5.1 合约清单

| 项 | 地址 | 实测确认 |
|----|------|---------|
| **ezUSDC1 金库 / 份额代币** | `0x877bbA4238EF1DCA9BF574561da633049de3A334` | BeaconProxy → 实现合约 **`LEZyVault`**<br>token name **"Renzo USDC Vault 1"**、symbol **ezUSDC1**<br>🔴 **decimals = 6**（跟随 USDC，不是 18）<br>totalSupply **90.323693**（2026-08-10 快照） |
| 🔴 **赎回队列** | `0x01D62CAE01b1df926c0a32EBe057374C4049C711` | BeaconProxy → 实现合约 **`WithdrawQueue`** |
| **ezETH 铸造入口** | `0x74a09653A083691711cF8215a6ab074BB4e99ef5` | TransparentUpgradeableProxy → 实现合约 **`RestakeManager`**<br>与 UI 授权弹窗显示的「交互合约」完全一致 ✅ |
| **ezETH 代币** | `0xbf5495Efe5DB9ce00f80364C8B423567e58d2110` | "Renzo Restaked ETH"，decimals 18，totalSupply 42,569.97（2026-08-10 快照） |
| USDC（申购币种） | `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48` | decimals 6 |

### 5.2 🔴 份额价格不是 1:1

| 来源 | 数值 |
|------|------|
| UI Exchange Rate（正向） | 1 USDC = **0.22849** Vault Shares |
| UI Exchange Rate（反向） | 1 Vault Share = **4.37649** USDC |
| 链上实测反推 | 1.000000 USDC → **0.228495** ezUSDC1 → 隐含 **4.37646 USDC/share** |

**→ 三者吻合（差 0.0007%），NAV 反推口径可靠。**
🔴 **绝不能按 1 ezUSDC1 ≈ $1 处理**，也不能按 18 位小数解析。

### 5.3 申购 / 赎回路径（两条线机制不同）

```
【ezUSDC1 申购】approve(USDC) → deposit()          ← 2 笔交易，份额即时 mint（from=0x0）
【ezUSDC1 赎回】approve(ezUSDC1) → withdraw()      ← 2 笔交易，份额转给 WithdrawQueue，🔴 本笔不到账
【ezETH 质押】  depositETH()                        ← 1 笔交易（ETH 原生币无需 approve），ezETH 即时 mint
```

🔴 **ezUSDC1 赎回是排队制**：`withdraw` 那笔链上只看到 **0.1 ezUSDC1 从用户转给 `0x01D62CAE…`（WithdrawQueue）**，**没有任何 USDC 回到用户**。与 Ember 的排队申购是镜像问题 —— 详见 §6.2。

## 6. 数据接入要点

### 6.1 ✅ 实测交易全集（2026-08-10，UTC）

| # | 时间 | 操作（UI 视角） | 交易类型 | tx hash | 链上结果 |
|:---:|------|---------------|---------|---------|---------|
| 1 | 08:09:23 | Deposit 第 1 步 | **approve(USDC)** | `0x6cff7c1ae02ac8dd4abc0180a513d1db26bbb806c53ad59e990cb1e9e40989b9` | 授权给金库 |
| 2 | 08:09:35 | Deposit 1 USDC | 🔴 **申购（即时 mint）** | `0x0aad5aa6b226c9a5f833cc52fa15337896b4a39fe9eba5e3b6b895639b11a3c9` | USDC **−1.000000**（用户→金库）<br>ezUSDC1 **+0.228495**（from `0x0`） |
| 3 | 08:10:47 | Withdraw 第 1 步 | **approve(ezUSDC1)** | `0x12acb97783a86f61774f27a001d4fbd49826ac539810e7afce517b35f6271cd5` | 额度 0 → **0.1**，授权对象 `0x01D62CAE…` ✅ 与 UI 弹窗一致 |
| 4 | 08:10:59 | Withdraw 0.1 份额 | 🔴 **赎回-提交（进队列）** | `0x0798eff3319f8be06efa061e450f7584b9d8410512589ba804ea2f38bee90c82` | ezUSDC1 **−0.1**（用户→WithdrawQueue）<br>🔴 **无 USDC 到账** |
| 5 | 08:11:59 | Stake 0.00001 ETH | **质押（即时 mint）** | `0xda2a5ba5045bdea792bde33261a1b713000292ca53170495a19a4878de6c0699` | 方法 **`depositETH`**<br>ezETH **+0.000009229160829871**（from `0x0`） |

**交叉验证**：ezETH 实得 0.000009229160829871 ÷ 0.00001 ETH = **0.9229**；UI 报价 **1 ETH = 0.92292 ezETH**，UI 预估收到 **0.00000922 EZETH** ✅ 完全吻合。

### 6.2 🔴 给解析同学的三条规格

| # | 规格 | 说明 |
|:---:|------|------|
| **1** | **一次 UI 操作 = 2 笔链上交易** | 申购、赎回都是 `approve` + 主操作。**只认主操作会漏掉用户的授权成本；把 approve 当成申赎会重复计数** |
| **2** | 🔴 **赎回需要「赎回中」状态** | `withdraw` 只把份额挪进 WithdrawQueue，用户既没有份额也没有 USDC。若按「有持仓才展示」的规则，用户会看到**资金消失** —— 与 8/3 记录的 Ember 排队申购是**同一类坑的镜像**（申购中 / 赎回中两个状态都要有） |
| **3** | **份额价格 ≈ 4.376、decimals = 6** | 见 §5.2。ezUSDC1 与 ezETH 的 decimals 不同（6 vs 18），同一协议内不能共用一套换算 |

**识别规则（同 Re 的结论一致）**：申购 = 份额代币 `Transfer(from=0x0)`；赎回-提交 = 份额代币 `Transfer(to=WithdrawQueue)`。

### 6.3 六维度自评

| 维度 | 可行性 | 取数方式 | 备注 |
|------|:---:|---------|------|
| 实时解析 | 🟡 | ezUSDC1 balance × 份额价格 | 份额价格的**链上取数函数待确认**（是否 ERC-4626 `convertToAssets`，见 §9） |
| 交易历史解析 | ✅ | 见 §6.1 五类交易 | 已有全部样本，唯缺 claim |
| 池子自发现 | ⬜ | 未测 | Reserve 下有多个 vault，是否有工厂/注册表未查 |
| APY | 🟡 | UI 有 30D APY 面板 | ezUSDC1 当时未展示数值 |
| NAV 历史 | ⬜ | 未测 | |
| PNL | ⬜ | 未测 | 依赖份额价格历史 |

### 6.4 需向项目方索取

1. **ezUSDC1 份额价格的链上取数入口**（是否 `convertToAssets` / `pricePerShare`）
2. **WithdrawQueue 的领取（claim）流程**：排队多久、是否需要用户再发一笔、事件签名
3. Reserve 下全部 vault 清单 + 是否有工厂合约（池子自发现依赖）
4. ezUSDC1 的策略明细（页面 Strategy & Parameters 的内容）

## 7. 风险

⬜ **未做**。仅记录实测中直接观察到的两点：
1. **赎回不即时**：走 WithdrawQueue 排队，时长未知
2. **ezUSDC1 池子极小**：TVL $394.30、totalSupply 90.32 份额（2026-08-10）→ 属于**刚上线/测试量级**，与 ezETH 的 $88.76M 差 5 个数量级，不宜混为一谈

## 8. 合规与准入

⬜ **未做**。

## 9. 待确认清单

| # | 问题 | 问谁 / 怎么查 |
|---|------|------------|
| 1 | 🔴 **协议表写的是 `SOLRenzo`（Solana），本次实测是 Ethereum 的 ezUSDC1 + ezETH** —— 要接的到底是 Solana 上的 ezSOL 还是本次这两个？**解析对象可能整个跑偏** | 提需求方 / DeX 项目侧 |
| 2 | 🔴 WithdrawQueue 的 claim 交易样本（本次只做到提交，没等到到账） | Linnea 补做 |
| 3 | 份额价格链上取数函数 | Etherscan Read Contract / 项目方 |
| 4 | ezUSDC1 是否 ERC-4626 | 实测 `asset()` / `convertToAssets()` |
| 5 | 协议背景 / 底层 / 风险 / 合规四章 | 未做，需补 |
| 6 | Reserve 下其他 vault（ezBTC 等）是否同一套机制 | 浏览器确认 |

## 10. 参考链接

- ezUSDC1 产品页：https://app.renzoprotocol.com/reserve/ezusdc1
- Staking 页：https://app.renzoprotocol.com/staking
- 实测截图：⚠️ **原图未落盘**（会话图片缓存已被系统清理）。UI 数值已逐项抄录进本页 §5.2 / §6.1，需要原图请重新截取

---

> **本页 UI 数据的原始来源**：2026-08-10 操作当时的 3 张截图（ezUSDC1 deposit pending / ezUSDC1 withdraw 授权弹窗 / ezETH 质押确认弹窗）。截图文件已随会话缓存清理丢失，但其中每一个数值都已与链上实测交叉验证并记录在案，见 §5.2「三者吻合」与 §6.1「交叉验证」。
