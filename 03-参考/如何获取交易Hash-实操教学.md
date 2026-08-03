# 如何获取交易 Hash（实操教学）

> 面向：需要给解析同学提供样本交易，但不想（或不必）自己下单的场景。
> 覆盖本次要的 4 个标的：**Solana-BNSOL、Solana-Orca V3、ETH-Ember (Bitwise)、ETH-Re**
> 整理时间：2026-08-03

---

## 一、先破除一个误解

**你不需要自己交易才能拿到 hash。**

区块链上所有交易都是**公开的**。任何人的申购、赎回、swap 都能在区块浏览器上查到，包括：谁做的、调了哪个合约、花了多少、拿到多少、抛了什么事件。

所以拿 hash 有三条路，**优先用第一条**：

| 路径 | 成本 | 速度 | 什么时候用 |
|------|:---:|:---:|-----------|
| **① 从浏览器捞别人的交易** | **免费** | **几分钟** | **默认用这个**。只要知道合约地址就行 |
| ② 找项目方要 | 免费 | 看对方回复速度 | 拿不到地址、或需要特殊场景（失败交易、特殊路径）时 |
| ③ 自己下单 | 要钱 + gas | 几分钟～一周 | 只有当"这个事件必须有真实持仓才会产生"时（比如 DMZ 的每周派息） |

> 📌 **本次 4 个标的里，Re 我已经用路径①捞到了**（见 [已确认合约地址与链上实测.md §6.3](已确认合约地址与链上实测.md)），另外 3 个下面教你怎么捞。

---

## 二、通用方法：EVM 链（ETH / BNB）

### 方法 A：从合约的 Events 页捞（最推荐）

以 Etherscan 为例（BNB 链用 **bscscan.com**，操作完全一样）：

```
1. 打开 https://etherscan.io/address/<合约地址>
2. 点 "Events" 标签页
3. 页面会列出这个合约最近的所有事件，每一行右侧都有对应的 Txn Hash
4. 找你要的事件类型（Transfer / Deposit / Withdraw…），点 hash 复制
```

**怎么认出"申购"和"赎回"**：
- **申购 = 铸造（mint）** → 找 `Transfer` 事件里 **From = 0x0000...0000**（零地址）
- **赎回 = 销毁（burn）** → 找 `Transfer` 事件里 **To = 0x0000...0000**

### 方法 B：从代币的 Transfers 页捞

```
1. 打开 https://etherscan.io/token/<代币地址>
2. 点 "Transfers" 标签页 → 看到所有转账
3. 想只看铸造：URL 后面加 ?a=0x0000000000000000000000000000000000000000
```

### 方法 C：从持有人反查（想看完整用户旅程时）

```
1. 代币页 → "Holders" 标签 → 挑一个余额不大的地址（大概率是真实散户，不是做市商）
2. 点进这个地址 → 看它的交易列表
3. 能看到这个人「申购 → 持有 → 赎回」的完整过程，一次拿到多条 hash
```

### 方法 D：命令行（不想开浏览器时）

```bash
# 捞某代币最近的铸造事件（= 申购）
curl -s -X POST https://eth.drpc.org -H 'Content-Type: application/json' -d '{
  "jsonrpc":"2.0","id":1,"method":"eth_getLogs","params":[{
    "address":"<代币地址>",
    "fromBlock":"<起始区块的16进制>","toBlock":"latest",
    "topics":[
      "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
      "0x0000000000000000000000000000000000000000000000000000000000000000"
    ]}]}'
```

- 第一个 topic 是 `Transfer(address,address,uint256)` 的哈希（**固定不变，所有 ERC-20 都一样，可以背下来**）
- 第二个 topic 填零地址 = 只看 mint；想看 burn 就把零地址挪到第三个位置（第二个填 `null`）

> ⚠️ 公共 RPC 会限制查询的区块跨度（常见上限 5,000～10,000 个区块 ≈ 1 天）。跨度太大会报 `400 Bad Request`。**分段查，或者直接用浏览器界面**。
> 实测可用的免费端点：`eth.drpc.org`、`ethereum-rpc.publicnode.com`、`eth.merkle.io`。BNB 链用 `bsc-dataseed.binance.org`。

---

## 三、通用方法：Solana

Solana 和 EVM 有几个**关键差异**，第一次接触很容易踩：

| | EVM | **Solana** |
|---|---|---|
| 交易标识叫什么 | Transaction Hash | 🔴 **Signature（签名）** |
| 长什么样 | `0x` 开头，66 字符 | 🔴 **base58 字符串，无 0x 前缀**，约 87–88 字符 |
| 合约叫什么 | Contract | **Program（程序）**，有 Program ID |
| 代币标识 | 合约地址 | **Mint 地址** |
| 一笔交易的内部结构 | 一串 events (logs) | **Instructions + Inner Instructions**（嵌套指令） |
| 用户的代币余额存在哪 | 合约里的 mapping | 🔴 **独立的 Token Account（ATA）**，每个用户+每个币一个账户 |

> 📌 **最容易踩的坑**：Solana 上用户的代币不是记在"代币合约里的余额表"，而是记在一个**独立账户（Associated Token Account, ATA）**里。所以解析持仓的逻辑和 EVM 完全不同 —— 这点要提前告知解析同学。

**推荐浏览器**（三个都行，Solscan 最好用）：
- **Solscan**：https://solscan.io
- Solana Explorer（官方）：https://explorer.solana.com
- SolanaFM：https://solana.fm

**通用操作**：
```
1. Solscan 搜索框输入代币名 / mint 地址 / 程序 ID
2. 代币页 → "Transfers" 或 "Txs" 标签 → 每行右侧的 Signature 就是你要的
3. 想看某个程序的交易：程序页 → "Transactions"
4. 点进一笔交易 → 展开 "Instruction Details" 能看到嵌套指令
```

---

## 四、逐个标的的操作路径

### 1️⃣ Solana — Binance Staked SOL（BNSOL）

> ⚠️ 这个和 Ember/Re 不是同一个项目，属于 DeX / 基础解析那条线。

**先拿 mint 地址（三种方式任选）**：

| 方式 | 操作 |
|------|------|
| **最可靠** | 在 **Binance Wallet** 里打开自己的 BNSOL 资产 → 详情页会显示合约/mint 地址 → 复制 |
| 次选 | Solscan 搜索框输入 `BNSOL` → 在结果里找 **Binance Staked SOL**，注意核对名称完全一致（山寨币会用相同符号） |
| 兜底 | 官方文档 / 公告页 |

> 🔴 **务必核对**：Solana 上同名代币极多，**只认名称 + 发行方都对得上的那个**。拿错 mint 地址会导致解析对象整个错掉。我这里不写具体地址，就是为了避免你抄到一个我记错的值。

**再捞 hash**：
```
Solscan → 打开 BNSOL 的 mint 地址页
  → "Transfers" 标签：找 From 为空/零 的记录 = 质押铸造（stake）
  → "Holders" 标签：挑一个余额小的持有人 → 点进去看它的交易历史
     （能一次拿到「质押 → 持有 → 解押」的完整链路）
```

**要提供给解析同学的 3 类 signature**：
1. **质押（SOL → BNSOL）**
2. **解押 / 赎回（BNSOL → SOL）** —— 注意 Binance 的解押可能有等待期，会分成两笔（申请 + 到账）
3. **兑换率变化相关**（BNSOL 是生息代币，兑 SOL 比率会涨，找一笔能看出比率的交易）

---

### 2️⃣ Solana — Orca V3

> ⚠️ 同上，属于 DeX 那条线。

**先确认"V3"指什么**（这一步不能省）：
Orca 的历史版本有：
- 早期的 **恒定乘积池（Constant Product / V1）**
- 现在主力的 **Whirlpools（集中流动性 CLMM）** —— 很多人口头叫它 V2/V3

🔴 **"V3" 是内部叫法还是官方版本名，需要先跟提需求的人确认**。否则你捞到的 hash 可能是错的池子类型，解析同学白做一遍。

**拿 Program ID**：
```
Solscan 搜 "Orca" → 找到官方的 Whirlpools 程序
（Whirlpools 的 Program ID 是个 vanity 地址，以 whirL… 开头，很好认）
→ ⚠️ 但请在 Solscan 上核对"官方认证/标签"，不要凭记忆抄
```

或者更直接：**在 orca.so 上做一笔最小额 swap，从钱包历史里拿 signature** —— Solana gas 极便宜（一笔约 0.000005 SOL），成本可以忽略。

**要提供的 4 类 signature**：
1. **Swap**（最常见，优先给这个）
2. **加流动性**（open position / increase liquidity）
3. **移除流动性**（decrease liquidity / close position）
4. **收手续费**（collect fees）—— CLMM 特有，容易被漏掉

> 📌 **提醒解析同学**：Whirlpools 是**集中流动性**，仓位是 NFT（每个 position 一个 NFT），不是简单的 LP token。持仓解析逻辑和传统 AMM 完全不同，务必提前说明。

---

### 3️⃣ ETH — Ember (Bitwise Premium+)

**当前卡点**：我还没有 PPLUS 金库的合约地址（ember.so 有防护，我抓不到）。

**最快路径（不用花钱）**：
```
1. 打开 https://ember.so/earn/PPLUS
2. 点 "Details" 或 "Transparency" 标签  ← 上次截图里有这两个 tab
3. 找 Vault Address / Contract / Share Token 那一栏 → 截图或复制地址
4. 把地址贴进 https://etherscan.io/address/<地址>
5. 点 "Events" → 找 Transfer 事件里 From = 0x0 的（= 申购铸份额）
6. 复制 Txn Hash
```

**要提供的 3 类 hash**：
1. **申购（存 USDC → 铸 share）**
2. 🔴 **"每日批处理"那笔** —— Ember 页面写着 Daily Processing / Shares Received: After processing。
   **这意味着申购可能是两笔交易**（用户存入 + 系统批量铸份额）。
   → **请务必找一个能看到"存入"和"铸份额"分开的例子**，这直接决定我们要不要给 PRD 加「申购处理中」状态
3. **赎回（Withdrawal，最长 7 天 → 大概率也是两段式）**

> 💡 **技巧**：拿到 vault 地址后，在 Etherscan 上点 "Holders" 找一个余额小的地址，看它的完整交易史 —— 一次就能把"存入 → 批处理铸份额 → 赎回"三段全找到。

---

### 4️⃣ ETH — Re ✅ 已完成，不用你动手

我已经用 `eth_getLogs` 捞到并逐笔剖析完了：

| 动作 | tx hash | 说明 |
|------|---------|------|
| **reUSD 申购** | `0xf252d674eacd3b3ba48449f71a28a202d1b71e0f46f8aa87e7cd5f6f92c9bdbc` | 35,000 USDC → 32,032.1169 reUSD |
| **reUSDe 申购** | `0x38a21b691067a5de68f440123d6231ec8b6133fc0b24e1eecaed7efe8e83e76f` | 24,994.5262 USDe → 17,892.7882 reUSDe |
| **reUSDe 赎回** | `0xb5ef00777bea1d3f8c34ac9cf071d25d4efcf1f634bc517c3f82420943056036` | 销毁 1.0000 reUSDe |

**还缺一条：reUSD 的赎回（burn）** —— 公共 RPC 区块跨度受限没捞到。你可以这样补：
```
https://etherscan.io/token/0x5086bf358635B81D8C47C66d1C8b9E567Db70c72
  → Transfers 标签
  → 找 To = 0x000...000 的记录
  → 复制 hash
```

完整的合约地图、方法选择器、事件结构、解析规格见 [已确认合约地址与链上实测.md §6](已确认合约地址与链上实测.md)。

---

## 五. 交给解析同学时，别只给 hash

光给一串 hash，对方还得自己猜这是什么。**用这个模板**：

```
协议：Re
链：Ethereum
动作：申购（用户存 USDC 铸 reUSD）
tx hash：0xf252d674eacd3b3ba48449f71a28a202d1b71e0f46f8aa87e7cd5f6f92c9bdbc

涉及合约：
  申购入口 0x4691c475be804fa85f91c2d6d0adf03114de3093
  凭证代币 0x5086bf358635B81D8C47C66d1C8b9E567Db70c72（18 decimals）
  支付代币 USDC 0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48（6 decimals）

调用方法：deposit(address,uint256,uint256) = 0x0efe6a8b

期望解析出：
  用户地址   0x1dd9d35b3fa0d39e9f2f12100c1647260ad22331
  投入金额   35,000 USDC
  获得份额   32,032.116942 reUSD
  入场 NAV   1.09264（= 投入 ÷ 份额）

识别规则：
  申购 = 凭证代币的 Transfer(from = 0x0)
  赎回 = 凭证代币的 Transfer(to = 0x0)

注意：USDC 是 6 位小数，reUSD 是 18 位，不能混用
```

**为什么要写"期望解析出"**：这是**验收标准**。解析同学写完代码，跑这笔 hash，结果对不上就是有 bug。没有这一栏，双方会在"到底谁错了"上来回拉扯。

---

## 六、校验清单：怎么确认这条 hash 是对的

| # | 检查项 | 怎么查 |
|---|-------|-------|
| 1 | **链对不对** | 同一个地址可能在 ETH 和 BNB 上都存在，但是不同合约 |
| 2 | **是我们要接的那个池子吗** | 同一个协议常有多个 vault（Ember 就有 EARN 和 PPLUS 两个）。**核对合约地址，不是只看协议名** |
| 3 | **代币名称/发行方对得上吗** | 🔴 Solana 上山寨币极多，同名同符号的假币常见 |
| 4 | **交易成功了吗** | 浏览器上看 Status = Success（失败交易也有用，但要标注清楚） |
| 5 | **decimals 是多少** | 6 位和 18 位混用会差 10¹² 倍，是最常见的解析 bug |
| 6 | **是不是走了聚合器/router** | 如果用户是通过 1inch、Jupiter 之类的聚合器进的，交易结构会完全不同，解析规则也不同。**优先给"直连协议"的样本** |

---

## 七、几个能省时间的小习惯

1. **一次多给几笔**：同一个动作给 2–3 笔不同金额/不同用户的，能帮解析同学发现边界问题（比如金额极小、涉及零头）
2. **失败交易也留着**：能暴露合约的限制条件（最低申购额、白名单、额度上限）
3. **优先找余额小的持有人**：大额地址往往是做市商或项目方，交易路径特殊，不代表普通用户
4. **Solana 记得说明 ATA**：解析同学如果只做过 EVM，很可能不知道 Solana 的代币余额在独立账户里
5. **hash 是公开信息，可以随便贴**；但**钱包地址会暴露该地址的全部资产和历史**，贴之前想一下是不是自己的主钱包
