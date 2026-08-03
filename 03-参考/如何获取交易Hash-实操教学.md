# 如何获取交易 Hash（实操教学）

> 面向：需要给解析同学提供样本交易，但不想（或不必）自己下单的场景。
> 覆盖本次要的 4 个标的：**Solana-BNSOL、Solana-Orca V3、ETH-Ember (Bitwise)、ETH-Re**
> 整理时间：2026-08-03

---

## 🔴 2026-08-03 更正：本文的定位变了

本文原来的第一句是"你不需要自己交易"。**在本轮调研的用途下这是错的** —— 捞来的 hash 无法确定对应用户的哪一步操作（Scott 指出的问题）。

**本轮的执行标准是 → [交易采集-操作清单与记录表.md](交易采集-操作清单与记录表.md)：亲手操作 + 四件套记录。**

**本文改为两个用途，仍然有价值**：
1. **教怎么在浏览器/RPC 上查交易** —— 你自己操作产生 hash 后，还是要靠这些方法去核对事件结构、decimals、合约地址
2. **收录已捞到的样本** —— 用于**交叉验证**（你的操作结果应该和这些结构一致）和**反例测试**（第五之二节那条 80 日志的交易）

---

## 一、先破除一个误解（仅适用于"验证解析结果"场景）

**验证解析逻辑时，你不需要自己交易。**

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

#### ✅ mint 地址已确认（2026-08-03 链上实测）

```
BNSOL mint: BNso1VUJnh4zcfpZa6986Ea66P6TCp59hvtNJ8b1X85
```

| 实测项 | 结果 |
|-------|------|
| owner（所属程序） | `TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA`（SPL Token 程序）✅ |
| 账户大小 | 82 字节 = 标准 SPL Mint ✅ |
| **decimals** | **9** ← 和 EVM 的 18/6 都不一样，务必注意 |
| supply | 9,043,715.3922 |
| **mintAuthority** | `75NPzpxoh8sXGuSENFMREidq6FMzEx4g2AfcBEB6qjCV` |

> 💡 地址来源：CoinGecko API 的 `platforms.solana` 字段，然后我用 Solana RPC 的 `getAccountInfo` **逐项验证过**（不是凭记忆）。你可以自己复现：
> ```bash
> curl -s https://api.coingecko.com/api/v3/coins/binance-staked-sol | python3 -m json.tool | grep -A3 platforms
> ```
> 注意 CoinGecko 免费接口限流很严（连续请求会 429），**一次只查一个，或者用 `/coins/list?include_platform=true` 一次拿全部**。

#### ✅ 已捞到的 signature（但**是 swap，不是质押**）

| signature | 说明 |
|-----------|------|
| `2vfcDXHjFiyUTQskvfGh1yHGmLLX9YfMsjvHpQSdeEX8eccvqAeu9Tbp1P7YAkmSQj1jGiDEPGeKzCSQWszE8yxp` | 经 DFlow 聚合器，BNSOL +0.2468 |
| `4sPac6c1cwWcArFAChcm59NP64kff4vRAUbUvbQtKih82BaFyAQKi9BJKo2RUUPdnWkNQAw2vvoSHUv2v2iyuiKs` | 经 Titan 聚合器，BNSOL +0.0008 |
| `3ANXHAQHvqvuHT28vn49Eaa6r8Be8covURadFJF8FWv9dpnd4oNUq9WaArtsi6odCt2a98vQLppxV91VUWb4kbL7` | USDC ↔ BNSOL |

🔴 **我一开始判断错了，这里如实记下来当教训**：
我先用"BNSOL 余额净增"来判断动作类型，把这几笔标成了"≈质押"。**逐笔看指令后发现全是 swap** —— 因为：
- 指令里只有**聚合器程序** + `spl-associated-token-account: createIdempotent`
- **没有 `mintTo` 指令**，也没有质押池程序

**→ 教训：判断 Solana 动作类型不能只看余额变化方向，必须看指令（instruction）。** 余额增加既可能是质押铸造，也可能是二级市场买入，两者的解析逻辑完全不同。

#### 怎么找**真正的**质押 / 解押交易

```
方式 A（最快）：Solscan 界面
  1. 打开 https://solscan.io/token/BNso1VUJnh4zcfpZa6986Ea66P6TCp59hvtNJ8b1X85
  2. 点 "Transfers" 标签
  3. 找 From 是 "—" 或零地址的记录 = mintTo = 真正的质押铸造
  4. 或者点 "Analytics" / "DeFi Activities"，Solscan 会自动标注动作类型

方式 B：从 mintAuthority 反查
  打开 https://solscan.io/account/75NPzpxoh8sXGuSENFMREidq6FMzEx4g2AfcBEB6qjCV
  它签名的交易里就包含铸造操作

方式 C（最可靠）：自己在 Binance Wallet 里质押一笔最小额
  Solana gas 极便宜（一笔约 0.000005 SOL ≈ 一分钱），成本可忽略
  然后从钱包交易历史里拿 signature
```

**判定标准**：打开交易详情，指令列表里出现 **`mintTo`（质押）** 或 **`burn`（解押）** 才是对的；如果只看到 swap / 聚合器程序，那就是二级市场交易。

**要提供给解析同学的 3 类 signature**：
1. **质押（SOL → BNSOL）** → 找 `mintTo`
2. **解押 / 赎回（BNSOL → SOL）** → 找 `burn`；注意可能分两笔（申请 + 到账）
3. **二级市场 swap** → 上面三条已经有了，可以直接用（**这类要能和质押区分开，别算成申购**）

---

### 2️⃣ Solana — Orca V3

> ⚠️ 同上，属于 DeX 那条线。

#### ✅ Program ID 已确认（2026-08-03 链上实测）

```
Orca Whirlpools 程序: whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc
```

| 实测项 | 结果 |
|-------|------|
| owner | `BPFLoaderUpgradeab1e11111111111111111111111` |
| **executable** | **true** → ✅ 确认是一个可执行程序，不是普通账户 |

#### 🔴 关于「Orca V3」：实测证据说这个叫法有问题

我从 5 笔成功交易的日志里解出了实际的 Anchor 指令名：

```
Whirlpools 自己的指令 : Swap、SwapV2、Swap2、Deposit、Withdraw
外层聚合器的指令      : SwapTob、SwapTobEnhanced、SwapRouteV3、CreateTokenAccount
```

**Whirlpools 的指令集里没有任何 "V3"。** 唯一带 V3 的是 `SwapRouteV3`，而它属于**外层聚合器**，不是 Orca 的。

→ 📌 **动手前先确认「Orca V3」指什么**。两种可能：
1. 是内部对 **Whirlpools（CLMM 集中流动性）** 的口头叫法
2. 是把聚合器的 `SwapRouteV3` 误当成了 Orca 的版本号

**搞错会让解析对象整个偏掉。** 一句话就能问清，值得先问。

#### ✅ 已捞到的真实 signature（已分类）

| 推荐度 | signature | 指令构成 |
|:---:|-----------|---------|
| ⭐ **先用这笔** | `nBy9BhsNKK2GxQCyu4p3aqoRyP1WrFfLiuCaSv29kBGqawgWKyAZNzZLELysyLymwcgSUqBqvhsiudXiPNygH5a` | SwapRouteV3, SwapV2｜只涉及 2 个 mint，**结构最干净** |
| ⭐ **要 LP 的话用这笔** | `2VR4X5o5Na8XkUR9GPmo1FLzCsLTR56wUdLDN2iNGTP5CnQmZCEsReSoTG8qwUmTFJmeK96KLY8d4btRuXRwN9bG` | CreateTokenAccount, SwapTobEnhanced, SwapV2, Swap, **Deposit**, **Withdraw** ← 唯一含流动性操作 |
| | `3vzNc5Nx2m2gJYgdsKG4kTvNxuMJ6nwod7cgJrY2C7noqxYZZRG4CzkVviToAr2UuWYyXZKWAP3ur2dzepaGnbGA` | SwapTob, SwapV2, Swap2（多跳） |
| | `2zWZGWt4AWTFsX9Go8ot7jBrfjSwC5mtPYCmWvAyCxNDjKzmTjaqEScynZGCLhdhbmVmjGG1uCgymDxat4GUiE9` | SwapRouteV3, SwapV2, Swap2 |
| | `2GxVKDNBUytBKZ4wnpFTfFJ9ELsqfij3nG2fS3uCeXGE6HiQ5BBXg6DvqBXRo87dQnVSo5eSabT1SHvc6AiiejBe` | SwapTobEnhanced, SwapV2 |

⚠️ **Orca 程序的失败率极高：25 笔里只有 5 笔成功**，大量是套利机器人抢跑失败。
→ **捞样本必须过滤 `err == null`**，否则十条里有八条是废的。

#### 还缺的两类（需要你或项目方补）

1. **纯 Orca 直连的 swap**（不经聚合器）—— 上面 5 笔全都套了外层聚合器。最快办法：**自己在 orca.so 上做一笔最小额 swap**（gas ≈ 一分钱）
2. **收手续费（collect fees）** —— CLMM 特有动作，最容易被漏，需要有 LP 仓位才会产生

> 📌 **务必提醒解析同学**：Whirlpools 是**集中流动性（CLMM）**，仓位是 **NFT**（每个 position 一个 NFT），不是传统的 LP token。持仓解析逻辑和恒定乘积 AMM 完全不同 —— 需要读 position NFT + tick 范围才能算出实际仓位价值。这一点如果不提前说，解析同学大概率会按传统 LP 去做，然后整个返工。

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

### 5️⃣ 顺手补上的：Nest nOPAL / Unitas XGLD（BNB 链）

| 协议 | 合约地址（BNB Chain） | decimals |
|------|---------------------|:---:|
| **Nest nOPAL** | `0x119dd7daff816f29d7ee47596ae5e4bdc4299165` | 🔴 **6** |
| **Unitas XGLD** | `0xe60106a5cAb7e7C64830919d36Ab20CaAf50Ac91` | 🔴 **6** |

**已捞到的 hash**：

| 协议 | 动作 | tx hash |
|------|------|---------|
| nOPAL | **赎回 burn** | `0xd9f45ecb06b1b87d2b72daac161f1752bc1dd0cd5ff576cbf485781bd8f4d3b4` |
| XGLD | **申购 mint** | `0x5a9c4bc2ad7215045daffe12990d2f0c88b37af1a93d587ffd028cff37677e30` |
| XGLD | **申购 mint** | `0xb31d83868885ce81582f509bb34ee85eee9cc08d5cd3976614bf714fc4e65e17` |

⚠️ 两笔 XGLD 申购的接收方是同一个地址（`0x0a4db057…`），大概率是 router / 做市商而非散户，用的时候要说明。

**还缺**：nOPAL 的申购、XGLD 的赎回（最近 5,000 区块内没发生）。用 **bscscan.com** 翻更早的记录即可：
```
https://bscscan.com/token/<地址> → Transfers → 找 From/To = 0x000…000
```

> 💡 **BSC 节点备忘**：`bsc-rpc.publicnode.com` 支持 `eth_getLogs`；`bsc-dataseed.binance.org` 会报 `limit exceeded`；`bsc.drpc.org` / `binance.llamarpc.com` / `1rpc.io/bnb` 实测连不上。

---

## 五之二、实战案例：一条"看起来对、其实不对"的 hash

> 这是 2026-08-03 真实发生的一次核对，非常典型，单独记下来。

**背景**：我说还缺 reUSD 的赎回样本，收到一条 hash：
```
0xa122e5a8826929a0ddb30917ac1912486ed6a4ed3e49090f13d6acc033d8f35a
```

**核对结果：这不是 reUSD 赎回。** 判定过程：

| 检查 | 结果 |
|------|------|
| 交易存在吗 | ✅ 存在，区块 25,671,740，状态成功 |
| 日志条数 | 🔴 **80 条**（正常的 Re 赎回应该只有 2–3 条） |
| 调用的合约 | `0x1aea38a9…f6eb` —— **不是 Re 的入口合约** |
| 里面有 reUSD 吗 | ✅ 有，16.121786 reUSD 在流转 |
| 🔴 **有 reUSD 的 `Transfer(to = 0x0)` 吗** | ❌ **没有** → **没有销毁 → 不是赎回** |
| 唯一的 mint 是什么 | 是另一个代币（`0x9487bd5a…1648`）的铸造，不是 reUSD |

**它实际是什么**：一笔经过聚合器的复合交易，路径里出现了 WETH、USDT、sUSDe、reUSD，还调用了 **Permit2**、**Uniswap V4 PoolManager**、**Pendle Router**（这三个是业内公认地址，按地址特征判断）。属于**杠杆 / 套利 / Pendle 相关操作**，reUSD 只是中途经过。

### 🎯 这个案例的三个价值

**1. 它示范了"怎么一眼判断"**
```
赎回 = 凭证代币的 Transfer 里，To = 0x0000...0000
没有这一条，就不是赎回，不管交易里出现了多少该代币
```

**2. 它示范了"日志条数就是信号"**
| 日志条数 | 说明 |
|:---:|------|
| **2–3 条** | 用户直连协议的干净样本 ← **要的就是这种** |
| **10 条以上** | 大概率经过了聚合器 / 多跳路由 |
| **50 条以上** | 几乎肯定是套利或杠杆组合操作，**不适合当解析样本** |

**3. 🔴 它本身是一个极有价值的「反例样本」，别扔**

把它交给解析同学，标注为 **"负向测试用例"**：

```
tx: 0xa122e5a8826929a0ddb30917ac1912486ed6a4ed3e49090f13d6acc033d8f35a
类型：负向用例（NOT 申购、NOT 赎回）
说明：这笔交易里有 16.12 reUSD 的转账，但没有 mint 也没有 burn，
      是用户把 reUSD 拿去 Pendle / Uniswap V4 做组合操作。
期望结果：解析器应当【不产生任何申购/赎回记录】，也不改变持仓成本基准。
```

**为什么这个用例重要**：reUSD / reUSDe 是可组合的（能在 Pendle、Curve、Morpho 用）。如果解析器只按"用户地址收到/转出该代币"来判断申赎，就会把这类交易**误判成申购或赎回**，导致用户的持仓成本和收益全错。**这类 bug 只有靠反例样本才能测出来。**

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
