# Plume 生态背景

> 为什么单独一页：Master 表里有 **4 行**协议名带 "Plume"（Nest Credit、OnRe、Asseto、FalconX、DigiFT 中的 4 个写成了 "X <> Plume"），Plume 实际上是这批协议里**最重要的单一生态依赖**。
> 调研时间：2026-07-31

## 一、Plume 是什么

- **定位**：**首个全栈 L1 RWA 链**，为 **RWAfi（real-world asset finance）** 而建；EVM 兼容、内置合规能力
- **投资方**：**Apollo Global Management、Galaxy Digital / Galaxy Ventures、YZi Labs、Brevan Howard Digital、Haun Ventures、SV Angel**
  📌 **YZi Labs 是币安生态相关的投资方** —— 这解释了为什么这批 RWA 协议里 Plume 系占这么大比重
- **Genesis 主网**：**2025-06-05** 上线，开局即有 Superstate、Blackstone、Invesco 等机构级 RWA，**$1.5 亿+** 已动用 RWA 资本，200+ 生态项目
- **核心组件**：
  - **$PLUME**：gas、staking、治理、抵押、生态准入
  - **SkyLink**：跨链基础设施，把 RWA 收益**流式传输到 20+ 网络**
  - **Plume Portal**：dApp 发现 / 奖励追踪 / 仓位管理
- **资产类型**：美债、货币市场基金、收益型稳定币、私人信贷基金，以及榴莲农场、文化 IP、GPU、矿产权益等另类资产

## 二、Nest —— Plume 的旗舰收益层（我们主要对接的就是它）

- 用户**无需许可**质押稳定币进机构级金库，拿到**可组合的收益型 RWA 代币**，还能拿去借贷协议做 looping
- **金库矩阵（7 个）**：nTBILL、nBASIS、nALPHA、**nOPAL**、nWISDOM、nCREDIT、nACRDX
- **多链**：Plume 起步 → Solana（5 个金库）→ Ethereum、Avalanche；UI 内置 LayerZero 桥
- **嵌入式化**：2026-03-19 接入 **EtherFi**（$60 亿+ 用户存款），Messari 认为 Nest 正在成为"嵌在消费级加密服务里的收益基础设施"
- 📌 **重要先例**：**Binance Wallet 已上线过 Plume 的 nBASIS 金库**（底层 Invesco USTB $8.6 亿+ AUM + Bitwise USCC $1.7 亿+ AUM，均由 Superstate 代币化，约 3.5% APY），官方称是"钱包首个结构化收益 RWA 产品"
  → **我们不是从零开始，这条链路已经跑过一次**

## 三、TVL 规模与波动（⚠️ 值得警惕）

| 时间 | RWA TVL | 备注 |
|------|---------|------|
| 2025 Q3 | **$645M** | 28 万+ 持有人，1 亿链上交易 |
| 2026 Q1 | **$340M** | 仍领跑 RWA 类别持有人数 |
| 公司口径 | $600M+ | 与上面季报数字不一致 |

🔴 **半年 TVL 近乎腰斩（$645M → $340M）**，波动很大。接入前应确认目标金库的**当前实际规模**，而不是用公司宣传口径。

## 四、监管与机构资质

- **ADGM**（阿布扎比全球市场）商业牌照
- **百慕大 BMA Class M 数字资产牌照**
- 🔴 **已在 SEC 注册为代币化证券的 transfer agent**（这在 RWA 链里很少见，是硬资质）
- $PLUME 已上 Upbit、Coinbase
- **Grove 向 Apollo 的 ACRDX 基金在 Plume 上投入 $5,000 万**私人信贷

## 五、对我们的接入含义

| 事项 | 说明 |
|------|------|
| **链支持** | 若要接 Plume 原生资产，中台需支持 **Plume 链**（EVM 兼容，但仍是新链，需确认节点/索引可用性） |
| **多链同名代币** | nOPAL 等在 Plume / Solana / Ethereum / Avalanche 都有，**每条链独立合约、可能独立 NAV** —— 配置必须按链拆开 |
| **跨链桥风险** | Nest UI 内置 LayerZero；SkyLink 做收益跨链流传 —— 若用户资产走桥，持仓解析要考虑桥后的代币 |
| **可复用先例** | Binance Wallet 已接过 nBASIS，可找当时的对接同学问链路 |
| **生态集中风险** | Master 表里 4–5 个协议依赖 Plume，**Plume 链出问题会同时影响多个产品** |

## 六、"X <> Plume" 写法的核查结论

Master 表把几个协议写成 "X <> Plume"，本次调研的核查结果：

| CSV 写法 | 核查结论 |
|---------|---------|
| **Nest Credit (Plume)** | ✅ 正确，Nest 就是 Plume 的旗舰协议 |
| **FalconX <> Plume**（nFXCF/FALX） | ✅ 正确，2026-06-30 Plume 官方宣布 FALX 上线，与 OpenTrade 合作部署 |
| **OnRe <> Plume**（ONyc） | ⚠️ **未找到公开证据**。OnRe 官方文档只说 Solana |
| **Asseto <> Plume**（NGI+） | ⚠️ **未找到公开证据**。Asseto 官方只说"多链"，未指明 |
| **DigiFT <> Plume**（pEAK） | ⚠️ **未找到公开证据** |

→ 📌 **推测**：后三个可能是通过 **Plume 的 Global RWA Alliance**（全球 RWA 联盟）产生的合作关系，而非技术部署。**建议向 Cece 核实"<>"到底表示什么关系**——这直接决定我们要不要在 Plume 上找合约。

## 七、参考链接

- 官网：https://plume.org/
- Genesis 主网上线：https://plume.org/blog/genesis
- Q1 2026 季报：https://plume.org/blog/plume-update-q1-2026
- Nest 官方文档：https://docs.nest.credit/about/available-vaults/
- Messari：Nest 作为多链 RWA 收益层：https://tokenpost.com/news/technology/20584
- Messari：从 Genesis 到成长：https://messari.io/report/plume-from-genesis-to-growth-in-the-rwafi-ecosystem
- Nest 上线 Solana：https://www.prnewswire.com/news-releases/plume-brings-institutional-real-world-yield-to-solana-with-launch-of-rwa-nest-vaults-302634100.html
- **Binance Wallet 上线 nBASIS（先例）**：https://www.theblock.co/post/407632/binance-wallet-plume-yield-vault-invesco-bitwise-funds
- Global RWA Alliance：https://plume.org/blog/plume-launches-the-global-rwa-alliance
- 链上数据：https://app.rwa.xyz/networks/plume
