---
timezone: UTC+8
---

# lishiqian

**GitHub ID:** lishiqian2333

**Telegram:** @Newin2333

## Self-introduction

web3初学者，希望能学到新知识。

## Notes

<!-- Content_START -->
# 2025-08-12

# 今日笔记 -  The Graph实操步骤
**目的：** 为了巩固之前学习的关于The Graph的内容


在本地hardhat进行部署智能合约，部署脚本用于测试链（Sepolia）。
## 1 前期准备
### 1.1 克隆代码
代码为：`https://github.com/liangpeili/nft-marketplace-contracts.git`

克隆完成，进入目录，执行以下命令：

```
npx hardhat help
npx hardhat test
REPORT_GAS=true npx hardhat test
npx hardhat node
```
### 1.2 注册INFURA
访问 Infura 官网并登录账户：`https://infura.io 或 https://app.infura.io。`

安装后进入“Dashboard”或“Projects”页面。

点击已有项目或新建项目（Create New Project）。

在项目详情页可以看到“Project ID”和“Project Secret”。复制 Project ID，就是你需要填写到 .env 的字符串（不包括 https://.../v3/ 部分）

在 Infura 仪表板查看 Project ID

>注意查看`https://.../v3/xxxx`，其中`xxxx`为后续所需的`Infura Key`。

### 1.3 Subgraph准备
登陆注册Subgraph Studio，连接钱包后创建一个subgraph，并记下`Subgraph slug`与`Deploy Key`。

## 2 配置 Sepolia 网络
先`hardhat.config.js`在中更改代码：


```

require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config();

const { INFURA_API_KEY, SEPOLIA_PRIVATE_KEY } = process.env;

module.exports = {
  solidity: "0.8.20",
  networks: {
    sepolia: {
      url: `https://sepolia.infura.io/v3/${INFURA_API_KEY}`,  // 或 Alchemy RPC
      accounts: [`0x${SEPOLIA_PRIVATE_KEY}`],
      chainId: 11155111
    },
    hardhat: { chainId: 31337 }
  }
};

```
在根目录下新建一个`.env`文件，其中内容为：
```
INFURA_API_KEY=你的Infura Key
SEPOLIA_PRIVATE_KEY=你的私钥（无 0x 前缀）
```
## 3 部署合约
运行
`
npx hardhat run scripts/deploy.js --network sepolia
`

运行结果入下：

```
lxn@lxndeMacBook-Air nft-marketplace-contracts % npx hardhat run scripts/deploy.js --network sepolia
[dotenv@17.0.1] injecting env (2) from .env – [tip] encrypt with dotenvx: https://dotenvx.com
[dotenv@17.0.1] injecting env (0) from .env – [tip] encrypt with dotenvx: https://dotenvx.com
Deploying with account: 0x7cf83b1245833F87fbc5f842B4711479Fa5235C5
cUSDT deployed to: 0x3Dfa6FfCfF78960a6B4Ee03D3b528DdcE9882D89
MyNFT deployed to: 0xbA58c0C81e8eF6C45515DbA360eBb59926b9981B
Market deployed to: 0x284c9438858a3456d9579e767A04d17D9ffb4Ea2
Market contract address: 0x284c9438858a3456d9579e767A04d17D9ffb4Ea2
```
## 4 使用 The Graph 初始化子图

### 4.1 前置准备
在已安装Node.js环境，并安装npm或yarn前提下，全局安装The Graph CLI：

`npm install -g @graphprotocol/graph-cli@latest`

或者：

`yarn global add @graphprotocol/graph-cli`

### 4.2 初始化子图
命令如下：

`graph init`

接下来依次选择网络、填写合约地址:
```
lxn@lxndeMacBook-Air nft-marketplace-contracts % graph init
✔ Network · Ethereum Sepolia Testnet · sepolia · https://sepolia.etherscan.io
✔ Source · Smart Contract · ethereum
✔ Subgraph slug · nft-marketplace-subgraph-test
✔ Directory to create the subgraph in · nft-marketplace-subgraph-test
✔ Contract address · 0x284c9438858a3456d9579e767A04d17D9ffb4Ea2
✔ Fetching ABI from Sourcify API...
✖ Failed to fetch ABI: Failed to fetch ABI: Error: NOTOK - Contract source code not verified
✔ Do you want to retry? (Y/n) · false
✔ Fetching start block from Contract API...
✖ Failed to fetch contract name: Name not found
✔ Do you want to retry? (Y/n) · false
✔ ABI file (path) · abi/Market.json
✔ Start block · 8711590
✔ Contract name · Market
✔ Index contract events as entities (Y/n) · true
✔ Directory already exists, do you want to initialize the subgraph here (files will be overwritten) ? (y/N) · false
lxn@lxndeMacBook-Air nft-marketplace-contracts % graph init
✔ Network · Ethereum Sepolia Testnet · sepolia · https://sepolia.etherscan.io
✔ Source · Smart Contract · ethereum
✔ Subgraph slug · nft-marketplace-subgraph-test
✔ Directory to create the subgraph in · nft-marketplace-subgraph-test
✔ Contract address · 0x284c9438858a3456d9579e767A04d17D9ffb4Ea2
✔ Fetching ABI from Sourcify API...
✖ Failed to fetch ABI: Failed to fetch ABI: Error: NOTOK - Contract source code not verified
✔ Do you want to retry? (Y/n) · false
✔ Fetching start block from Contract API...
✖ Failed to fetch contract name: Name not found
✔ Do you want to retry? (Y/n) · false
✔ ABI file (path) · abi/Market.json
✔ Start block · 8711590
✔ Contract name · Market
✔ Index contract events as entities (Y/n) · true
✔ Directory already exists, do you want to initialize the subgraph here (files will be overwritten) ? (y/N) · true
  Generate subgraph
  Write subgraph to directory
✔ Create subgraph scaffold
✔ Initialize networks config
✔ Initialize subgraph repository
✔ Install dependencies with yarn
✔ Generate ABI and schema types with yarn codegen
✔ Add another contract? (y/N) · false

Subgraph nft-marketplace-subgraph-test created in nft-marketplace-subgraph-test

Next steps:

  1. Run `graph auth` to authenticate with your deploy key.

  2. Type `cd nft-marketplace-subgraph-test` to enter the subgraph.

  3. Run `yarn deploy` to deploy the subgraph.

Make sure to visit the documentation on https://thegraph.com/docs/ for further information.
```
到这样就说明已经初始化子图 (subgraph) ，Graph CLI会自动生成subgraph.yaml、schema.graphql、mapping.ts 等模板文件，可以按照需要进行内容更改。

**注意事项：**
- 测试网名为：Ethereum Sepolia Testnet
- Subgraph slug为1.3步中设立的
- 当合约无法拉取到ABI file (path)时，手动为 graph init 提供已编译出的 ABI 文件：`abi/Market.json`
- Contract name是要索引的那份合约的 Solidity 合约名，例子中为：Market

```
graph codegen
graph build
graph deploy --studio <YOUR_SLUG>
```
## 5 部署子图
完成了子图的初始化、编写好schema.graphql、subgraph.yaml和mapping.ts文件之后，接下来就进入真正的核心阶段：生成代码、构建并部署子图。

1. 输入以下命令进入子图项目目录：`cd nft-marketplace-subgraph-test`

2. 执行下列命令生成类型代码：`graph codegen`，输出`Types generated successfully`说明成功了。

3. 执行命令如下，构建子图：`graph build`，输出`Build completed: build/subgraph.yaml`表明成功。

4. 登录Subgraph Studio在Studio子图页面获取Deploy Key，并执行授权命令。`graph auth 你的DEPLOY KEY`。运行成功后CLI会提示:`Deploy key set for https://api.studio.thegraph.com/deploy/`

5. 通过命令部署命令子图：`graph deploy 之前设置的Subgraph slug`

至此，subgraph部署完成。

## 6 查询验证
登陆Subgraph Studio，在Playground左侧编辑区输入查询语句即可查询相关信息，例如：

```
{
  newOrders(first: 5, orderBy: blockTimestamp, orderDirection: desc) {
    id
    seller
    tokenId
    price
    blockTimestamp
  }
}
```

查询成功后，结果会在右间区域以 JSON 形式展示。

# 2025-08-11

# 今日笔记
## 一、Dapp 架构与开发流程

* Dapp 核心构成

  * 前端。通过 HTML/CSS/JavaScript构建界面，并集成区块链钱包（MetaMask）与合约互动。
  * 智能合约。用 Solidity 编写核心业务逻辑，部署在以太坊等链上，由 EVM 执行。
  * 数据检索器（Indexer）。监听合约事件，把链上数据写入传统数据库，供高效查询显示。
  * 区块链 + 去中心化存储。链上存储状态与交易记录，IPFS/Arweave 用于保存如图片等大体量数据，不易丢失与篡改。 

* 开发流程

  1. 需求分析 & 平台选择。明确功能、选用以太坊、Solana、Polygon等链，设计 UX。
  2. 智能合约编写、测试与安全审计。包括单元测试、安全漏洞检查（如重入、整数溢出）。
  3. 检索器的实现与部署。使用 TypeScript 和工具（如 Ponder 或 Subgraph）处理链上事件，写入数据库。
  4. 前端开发。集成钱包、构建 UI、显示链上及数据库数据、处理交易签名。
  5. 合约交互逻辑。用 Viem、Ethers.js、Wagmi 读取合约状态、发送交易。
  6. 部署与上线。使用工具如 Hardhat 或 Foundry 部署合约，前端可部署至 IPFS 或传统平台。

## 二、以太坊开发环境搭建

* 基础配置

     Node.js，npm 或 yarn，Git。

## 三、Solidity 编程基础与安全实践

* 版本声明 (`pragma solidity ^0.8.0;`)；基本类型（`uint`, `address`, `string` 等）；映射、结构体、枚举等复合类型。
* 函数可见性和状态修饰符（如 `public`, `view`, `payable`）；函数修饰器（modifiers）；继承、多重继承、接口与抽象合约。

# 2025-08-10

# 今日笔记
## 1. 中国 Web3 合规环境与主要法律风险

* 自 2017 年起，中国已全面禁止 ICO、IEO、IDO 等形式的代币发行与公开融资，无论代币名称如何包装，只要具备融资或流通功能，就可能被认定为非法企图。参与代币模型设计或合约部署的技术方也可能被视为共谋者，不得仅以“写代码”为由免责。

* 链游中常见的“充值—抽奖—提现”的玩法，可能构成“开设赌场罪”；某些 NFT 或挖矿项目中的多层级返利机制，也可能构成“组织、领导传销活动罪”。

* 利用虚拟货币跨境换汇、规避外汇管控，可能触及非法经营外汇业务或洗钱罪。对方若涉及电信诈骗、赌博等犯罪，参与者即使不知情也可能面临刑责。

* 虚拟货币交易合同能否得到司法支持存在很高不确定性。法院常认定此类合同无效，且许多交易如价格争议、交割失败等，往往无法得到法院受理。代投行为亦可能面临法律责任，建议在代投前明确双方职责、保留协议凭证并监督资金。


## 2. 全球监管趋势与合规实践

* 欧盟 MiCA正在推进实施；
* 新加坡正强化 AML/CTF 合规，接轨 FATF 标准；
* 美国国会近期提出《稳定币法案》，为稳定币发行、反洗钱及消费者保护设定制度基础。
* 越来越多机构主动采用 KYC（身份认证）、尽职调查、地址筛查、交易监控等措施，以提升风控能力与用户信任。
* 匿名性与跨链交互增加了风险评估、团队协作与合规报告的难度，要求更高效的监控与内部流程建设。

# 2025-08-07

# Web3 合规性要求与法律风险的表格总结

| 核心领域        | 关键风险 / 要点                       |
| :-----------: | :-------------------------------: |
| **合规法律风险**  | ICO、代币分发、抽奖机制、返佣等易触及非法金融、传销法律底线 |
| **合约安全**    | 重入漏洞、编译一致性、权限控制、使用安全库与审计机制      |
| **前端安全**    | 私钥泄露风险、钓鱼诈骗、攻击脚本防护              |
| **基础设施安全**  | 51% 攻击、节点安全、结合主网和Layer‑2、隐私保护技术 |
| **治理与升级安全** | 多签、时间锁、DAO治理、多样防御机制             |

# 2025-08-06

# 今日内容：
## 1.  DeFi（去中心化金融）
利用区块链构建无需传统金融中介的金融服务体系。

案例：Uniswap —— 作为典型代表，引入自动做市商（AMM）模式，通过“恒定乘积公式”实现代币定价，支持用户提供流动性并从交易手续费中获益 

## 2. NFT（非同质化代币）
虽然文中尚未展开详细内容，但此板块通常涵盖数字收藏、数字资产确权、社区驱动 IP 等应用方向，具备显著用户识别与社交价值（Web3 本文外普遍认知）。

## 3. DAO（去中心化自治组织）
同样作为核心板块之一，DAO 区块链治理模式通过智能合约实现组织自治，无需中心化管理，具备透明自治与社区治理的优势（文中作为重点前瞻方向提及）。

## 4. Meme 币（模因币）
Meme 币以网络文化与幽默元素为基础，通过社区共鸣形成影响力，多依靠社交传播与网络效应驱动价值增长（本文列为生态重要组成）。

## 总结
|赛道 |核心定位	|案例 / 机制亮点|
|:----:|:----:|:----:|
|DeFi |	去中心化金融服务	| Uniswap、AMM 模式、流动性挖矿 |
NFT	| 数字资产确权与收藏、社群文化 |	 |
DAO |	链上治理与去中心化组织结构	| 智能合约自治组织、社区驱动机制 |
Meme 币	| 社区共识与文化传播驱动的代币形式 |	Meme 次文化传播为核心推动力 |

# 2025-08-05

# 今日份总结：

##  一、以太坊是什么？

* **以太坊（Ethereum）** 是于 2015 年推出的开源区块链平台，由 Vitalik Buterin 等人创建，支持去中心化应用（DApps）与智能合约的开发和部署。
* 它不仅有原生加密货币 ETH，还支持去中心化金融（DeFi）、NFT、DAO 等创新应用。

---

##  二、核心特性与技术组件

* **智能合约（Smart Contracts）**：存储在区块链上的自动执行的代码，由 Solidity 等语言编写并运行于 EVM。合约一旦部署即不可更改，提升透明度与自治性。
* **以太坊虚拟机（EVM）**：面向图灵完备的执行环境，支持复杂逻辑运算，保证智能合约的执行安全性。
* **ERC 标准**：

  * **ERC‑20**：定义可互操作的代币接口；
  * **ERC‑721**：定义非同质化代币（NFT）接口；
  * 还有 ERC‑1155、灵魂绑定令牌（SBT）等补充标准。

---

##  三、以太坊的演进：从 1.0 到 2.0（Serenity）

* **发展阶段**：

  * **Frontier（2015）**：测试网启动；
  * **Homestead（2016）**：提升易用性与稳定性；
  * **Metropolis（2017-2019）**：包含拜占庭（Byzantium）及伊斯坦布尔（Istanbul）等升级；
  * **Serenity / Ethereum 2.0（起始于 2020）**：转向 Proof-of-Stake、引入分片与 eWasm 提升性能。
* **关键升级**：

  * **The Merge（2022 年 9 月 15 日）**：彻底从 PoW 转向 PoS，大幅节省能源约 99%，减少 ETH 新发行，提升可持续性。
  * **EIP‑1559（2021 年 8 月）**：调整手续费结构，引入“基础费燃烧机制”，降低通胀甚至实现净通缩。

---

##  四、生态与扩展现状

* **开发者与项目活跃性**：

  * 以太坊拥有全球最多的活跃开发者团队，远超 Solana 和比特币，在 Web 3 领域具备显著先发优势。
* **扩展方案**：

  * Layer-2 方案（如 Arbitrum、zkSync 等）已部署数百个项目，采用 Rollups 和 State Channel 技术缓解主网拥堵问题。
  * 最新提案如 **EIP‑4844（proto‑danksharding）** 将降低 gas 成本、提高数据可用性，进一步支持 Layer-2 的普及。
* **治理与创新**：

  * DAO 是典型组织模型之一，链上自治运行、无中心管理，比如著名的 The DAO 事件及后续以太坊硬分叉回滚历史。

---

##  五、优势与挑战

* **优势**：

  * 去中心化、高安全性、多样化生态与强大的网络效应；
  * 支持兼容可组合（composable）的 DeFi、NFT、DAO 项目，开发者活跃度高。
* **挑战**：

  * 扩展性仍有限，手续费高低波动影响用户体验；
  * 安全风险与治理集中问题（如 MEV、DAO 地位集中）；
  * 监管不断收紧，影响混合器、交易审核等去中心化属性([ODaily][14], [fx168news][12])。

---

##  总结

| 项目   | 内容简介                                          |
| ---- | --------------------------------------------- |
| 平台定位 | 去中心化计算机级区块链平台，支持 DApp 与智能合约                   |
| 核心组件 | EVM、智能合约、ERC 标准、PoS 验证器                       |
| 重要升级 | The Merge/PoS 转型、EIP-1559、分片、eWasm            |
| 扩展方式 | Layer-2 Rollups（Arbitrum、zkSync 等）、EIP-4844 等 |
| 生态领域 | DeFi、NFT、DAO、稳定币、身份模块、ENS 等                   |
| 发展优势 | 领先开发者生态、强社区凝聚力、网络效应明显                         |
| 持续挑战 | 扩展性、安全性、治理、监管不确定性                             |

# 2025-08-04

今日学习区块链基础知识，做了简单笔记进行记录：
一、什么是区块链？
定义：区块链是一种分布式账本技术，将事务数据按时间顺序打包为“区块”，并通过哈希链接构成链结构，实现数据的不可篡改、公开透明、可追溯。

核心特性：

不可篡改：修改任何历史区块需连锁修改后续区块，几乎不可能。
公开透明 & 匿名：所有交易记录对网络公开，但参与者身份不直接关联钱包地址。
去中心化：由多个节点共同维护账本，无单一控制方。
快速交易：交易一旦打包进区块，即完成确认，速度相对传统跨国转账更优。
运行机制：

用户提交交易。
交易广播至网络。
节点验证交易合法性。
通过共识机制（如 PoW）将交易打包为区块。
区块链上链，其他节点更新账本。
节点（矿工）获得加密代币及手续费奖励。
二、比特币（BTC）简介
价值与激励机制：
区块链服务的提供者（矿工）通过验证交易获得比特币奖励。
比特币总量有限，不依赖通货膨胀，具有分散的货币属性。
优缺点：
优势：匿名性、公开透明、不受中心化限制，支持全球转账。
劣势：易被不法分子用于洗钱，区块生成周期（约 10 分钟）影响实时性，交易费用与扩展性受限。
三、区块链的三种架构类型
类型	成员加入	数据公开性	管理模式	合适场景
公链	任何人自由加入	全网可见	去中心化共治	加密货币、公开存证
联盟链	经邀请或审批	限定成员可见	多中心治理	跨机构协作、供应链、金融
私链	严格审批	仅企业内部可见	中心化管理	企业内部审计、管理系统
四、Web2 / Web 3.0 / Web3 的范式差异
Web2（现有互联网）：由平台控制数据，用户生成内容但不拥有数据。以广告和抽佣为盈利模式。

Web 3.0（语义网）：通过 RDF、OWL 等语义标准组织数据，便于机器理解，但非基于区块链技术。

Web3（去中心化互联网）：

数据由用户自主管理（钱包控制）；
利用智能合约无需信任中介；
资产（如 NFT、DeFi）真正归用户所有；
构建可组合、去中心化的应用生态，内容永不丢失。
常见误区纠正：

Web3 ≠ Web 3.0：前者指区块链生态，后者指语义网；
Web3 并非万能技术，某些场景（如社交媒体）更适合 Web2；
存在 Web2.5 过渡模型，例如 Reddit 的链上积分系统。
开发技术栈对比：

Web2：React + Node.js + MySQL
Web3：React + Ethers.js + Solidity + IPFS
Web 3.0：Python + RDFLib + SPARQL
五、去中心化的优势与挑战
主要优势：

最小化信任成本；
抗审查、高可用性；
用户自主管理资产与数据；
激励创新生态，任何人都能参与开发。
面临挑战：

可扩展性问题（吞吐量、延迟）；
安全漏洞与治理风险（如 DAO 投票权集中）；
UX 不友好，手续费波动；
法律合规不完善，跨境监管仍不明确


# 2025.07.30


<!-- Content_END -->
