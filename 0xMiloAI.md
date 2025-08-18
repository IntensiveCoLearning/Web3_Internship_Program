---
timezone: UTC+8
---

# milo

**GitHub ID:** 0xMiloAI

**Telegram:** @0xmilo_eth

## Self-introduction

web3初学者

## Notes

<!-- Content_START -->
# 2025-08-18

今日总结：

一、测试网的作用

目的：验证合约功能、安全性，避免主网部署出错和浪费。

代币：测试网用无价值的 ETH，可免费获取，无经济风险。

二、常用测试网

Sepolia：主流 PoS 测试网，最接近主网，适合部署前验证。

Holesky：用于验证者、质押协议和大规模测试。

三、获取测试币（Sepolia ETH）

准备钱包：使用 MetaMask，切换网络至 Sepolia；

领取方式：

访问：https://sepolia-faucet.pk910.de/

粘贴地址 → 点击 Start Mining → 等待领取。

四、使用 Remix 部署合约

连接钱包：在 Remix 选择 Injected Provider - MetaMask；

编译合约：在 Solidity Compiler 面板点击 Compile；

部署合约：

切换回 Deploy 面板；

点击 Deploy，MetaMask 弹窗确认；

Remix 输出部署地址和交易哈希。

五、在 Etherscan 验证合约

网址：https://sepolia.etherscan.io

查询方式：

使用交易哈希查看部署详情；

使用合约地址查看代码、事件、调用记录等。

六、交互与验证

在 Remix 中调用合约函数（如 leaveMessage）；

查看 MetaMask 提交交易；

前往 Etherscan 查看交易记录和事件日志，验证执行效果。

是否还需要进一步浓缩为图示流程或导出文

# 2025-08-15

今日总结：
1. 帐户模型
EOA（外部拥有账户）：
地址由 keccak256(pubKey)[12:] 生成，用私钥签名控制。可主动发交易，Gas 由账户自身余额支付，常用于钱包、个人账户。
合约账户：
地址由 CREATE/CREATE2 生成，由合约代码控制，不能主动发交易，只能被 EOA 或合约调用。Gas 由调用者支付，常见于 ERC-20/721、DeFi、DAO。
2. Gas 机制
Gas：执行 EVM 指令的工作量单位。
Gas Limit (Tx)：单笔交易可用的 Gas 上限，防止死循环。
Gas Used：实际消耗的 Gas。
Base Fee：EIP-1559 引入，动态调整，销毁以控制费用波动。
Priority Fee / Tip：激励打包交易的额外费用。
Max Fee Per Gas：最高支付单价，通常由钱包自动估算。
3. 交易生命周期
签名构造：收集交易参数 → 私钥签名（v,r,s） → RLP 序列化。
广播：交易进入 mempool，节点按费率、nonce 筛选。
打包执行：验证者/矿工选取并执行交易 → 生成交易收据。
区块传播与共识：区块头记录新 stateRoot，PoS 下约 12 分钟达成 Finality。
确认数：常以 ≥12 个区块确认为低风险，最终终结由 Casper FFG 确认。

# 2025-08-14

今天看了智能合约开发的第四节：
总结了一下：
1. 环境准备
使用 Remix IDE（浏览器在线 IDE，支持编写/编译/部署/调试 Solidity）。
2. 合约功能
用户地址可提交多条链上留言（永久保存、不可篡改）；
提供查询单条留言、留言总数功能；
触发 NewMessage 事件方便追踪。
3. 新建文件
在 Remix File Explorer 新建 messageboard.sol 文件；
粘贴合约代码。
4. 编译
进入 Solidity Compiler 面板；
选择与代码一致的编译器版本；
点击 Compile messageboard.sol 编译（✅ 表示成功）。
5. 部署
在 Deploy & Run Transactions 面板选择 JavaScript VM 环境；
点击 Deploy 部署合约；
部署成功后显示合约地址、可调用函数、状态变量。
6. 调用函数
找到 leaveMessage 输入框，输入留言（如 Hello World）；
点击按钮发起交易；
在交易详情中查看链上存储的留言。

# 2025-08-13

今日学习总结：
1.深入学习和了解智能合约安全基础
学习常见漏洞：重入攻击（Reentrancy）、访问控制缺失（Access Control）、整数溢出（Overflow/Underflow）、拒绝服务（DoS）等。
掌握安全设计模式：CEI（Checks-Effects-Interactions）、最小权限原则（Least Privilege）、多签控制（Multi-sig）等。
了解安全工具：Slither（静态分析）、Mythril（安全分析）、Tenderly（调试和监控）等。
2. 学习和了解 OpenZeppelin 合约，并引入到项目
熟悉 OpenZeppelin 的标准合约库（如 ERC20、ERC721、Ownable、AccessControl）。
通过 npm install @openzeppelin/contracts 或 forge install OpenZeppelin/openzeppelin-contracts 引入到项目。
学会继承和使用其模块，提高安全性与可维护性。
3. 编写 2-3 条测试用例
选择 Hardhat 或 Foundry 编写单元测试。
编写功能测试：验证正常逻辑是否符合预期。
编写安全测试：验证边界条件、异常输入是否安全并正确 revert。
4. 尝试优化 Gas，并记录消耗区别
使用优化方法：
用 calldata 替代 memory
使用 immutable / constant 固定值
变量打包减少存储槽
避免不必要的 SSTORE 和循环
测量工具：Foundry forge test --gas-report、Hardhat hardhat-gas-reporter。
对比记录优化前后的 Gas 差异。

# 2025-08-12

今天学习了智能合约开发的第三大节的内容，学到了一些新知识，总结：
1. 基础
面向合约的高级语言，运行在 EVM；
静态类型，支持继承、库、自定义类型；
常用于以太坊智能合约开发。
2. 语法
版本声明：pragma solidity ^0.8.0；
数据类型：
基本：bool、uint/int、address、bytes、string；
复合：数组、mapping、struct、enum。
函数修饰符：
可见性：public、external、internal、private；
状态可变性：pure、view、payable。
3. 合约结构
状态变量：链上永久存储；
构造函数：初始化状态；
普通函数：执行逻辑；
修饰符：如 onlyOwner 权限控制；
继承/重写：virtual / override；
事件：链上记录状态变化。
4. 安全实践
重入攻击：
防护：CEI 模式、ReentrancyGuard；
访问控制：
防护：onlyOwner、AccessControl、多签；
整数溢出：
防护：Solidity 0.8+ 自动检测、逻辑限制。

# 2025-08-11

今天看了智能合约开发的第一，第二节的内容，总结了一下：
一、Dapp 架构
前端（UI）：
1.用 HTML/CSS/JS（React、Vue 等）构建；
2.集成 Web3 钱包（MetaMask 等）实现身份验证与交易签名；
3.通过 Viem / Ethers.js / Wagmi 与区块链交互；
智能合约（Smart Contract）：
1.用 Solidity 编写，部署到区块链；
2.负责业务逻辑与状态存储；
3.需编写测试、审计和优化；
数据检索器（Indexer）：
1.监听合约事件（如 Transfer）；
2.将数据写入传统数据库（PostgreSQL 等）供前端高效查询；
3.常用框架：Ponder、Subgraph；
区块链与去中心化存储
1.链上存储交易和状态
2.IPFS / Arweave 等存储大文件，保证持久性与去中心化
二、Dapp 开发流程
需求分析：功能、平台选择（以太坊、Polygon 等）、UX 设计；
智能合约开发：编写、测试、审计、部署；
检索器开发：确定前端需要的数据 → 编写监听/入库逻辑 → 部署运维；
前端开发：UI 构建、钱包连接、数据展示、交易交互；
部署上线：
1.合约：Hardhat / Foundry 部署到测试网或主网；
2.前端：部署到 IPFS/Vercel；
3.维护与更新。
三、以太坊开发环境搭建
基础环境：
1.Node.js（建议 nvm 管理）、npm/yarn、Git；
2.安装 nvm → Node LTS → yarn；
本地开发链：
1.Foundry（Rust 实现，快）；
2.forge（构建/测试/部署）、anvil（本地节点）、cast（命令行交互）；
3.Hardhat（推荐）；
一体化框架，支持本地节点、部署、测试；
钱包与前端交互：
1.MetaMask 作为开发钱包；
2.前端库：Viem、Wagmi；
其他工具：
1.Remix IDE（在线快速测试）；
2.OpenZeppelin（合约库）；
3.Chainlink（预言机）。

# 2025-08-09

今天看了社区运营指南这个小节上的内容，总结了一些：
日常运营：更新社媒、维护社群秩序、引导互动。
内容发布：活动预热、推文宣发、多渠道扩散。
活动策划：线上（Space、课程）、线下（Meetup、黑客松）；涵盖策划、执行、复盘。
对外合作：联动社区、KOL、媒体扩大曝光。
常用工具：Twitter、Discord、TG、微信群；Notion、Figma、ChatGPT；Etherscan、Dune 等。
执行模板：明确主题、嘉宾、时间表；分阶段宣发；现场主持与互动；事后数据回顾与优化。
一些重点细节没记录了，以后复习可以看这个回忆一下。

# 2025-08-08

1.重温了基于区块链的加密货币安全审计指南：
威胁建模：CIA、STRIDE、DREAD、PASTA 四大模型。
测试方法：黑盒（模拟攻击）、灰盒（部分源码）、白盒（全读代码）。
漏洞分级：Critical > High > Medium > Low > Weakness > Suggestion > Info。
漏洞检查方向：签名、交易、RPC、P2P、共识、权限、合约逻辑等。
审计服务类型：
主网审计（L1/L2）
源码审计（静态+手动）
应用链审计（Cosmos、Substrate）
智能合约审计（支持主流链）
桥、钱包、ZK、交易所等安全审计；
2.阅读了区块链黑暗森林自救手册1，2节，准备以后继续看；
3.观看了大量区块链被黑案例，时刻警醒自己；
4.学习使用了密码管理和授权钱包的工具。

# 2025-08-07

总结了岗位职责与要求
一，技术岗
1.前端工程师
职责：开发 Dapp UI，集成钱包与链上交互
要求：熟悉 React/Vue、Viem，懂链上数据处理

2.后端工程师
职责：构建 API，处理链上链下数据交互
要求：掌握 Node.js/Go，熟悉数据库与 Web3 工具

3.智能合约工程师
职责：编写/部署 Solidity 合约，保障安全与性能
要求：精通 Solidity，熟悉审计工具与主流公链

二.非技术岗
1.产品运营
职责：推动产品上线，提升用户增长与留存
要求：会数据分析，能协调资源推动项目

2.社区管理
职责：运营 Discord/Twitter，策划活动提升活跃
要求：有 Web3 社区经验，擅长写作与互动

3.研究分析
职责：输出行业/协议分析报告，支持产品策略
要求：懂数据工具，了解 DeFi、链上分析平台

总结了web3合规指南：
Web3合规与安全指南：警惕非法集资、洗钱、传销类项目，避免触犯刑法；入职前查清项目注册地、资金流向及薪酬代发方式；注意网络钓鱼、签名木马、私钥泄露等常见攻击，保护资产安全；远离高收益诱惑与无实际业务的“画饼”项目，牢记自己不仅是用户，更是法律责任主体。

# 2025-08-06

1.再一次温习了去中心化交易所的机制，因为昨天看过今天又忘了；
2.加深了对Uniswap协议的理解；
3.看了一遍区块链岗位，对前端工程师挺感兴趣的，因为后端工程师所需的几种技术语言没怎么学；
4.智能合约工程师感觉也不错，但需要学的东西有很多；
5.收藏了手册推荐的几个招聘平台；
6.通读了Web3 从业/参与的核心法律风险，感觉国家对web3行业挺重视的，相关法律很多；
7.Web3 项目常注册在境外，不能提供合法的五险一金，合同效力不受保护，这个到是提醒到我了；
8.也进一步提高了防骗意识，一些诈骗手段如钓鱼攻击，恶意软件，伪装社交身份等有了一定防范意识；

# 2025-08-05

1.重温了一遍扩展阅读“我的第一个NTF”，感觉又加深了我对NTF的理解，提高了防钓鱼的意识；
2.去中心化交易所的概念；
3.去中心化与中心化交易所的异同；
4.明白了去中心化借贷协议；
5.稳定币系统：超额抵押资产生成DAl,利用稳定费率来调整DAl的供需关系，
6.更深入了解了NFT；
7.了解了MEME币的发展历史；
8.知道了交叉创新领域，对AI + DeFi：智能化金融服务听感兴趣的；
9.看了看以太坊官网，这教程相当全面啊；
10.深入了解了web3相关工具。

# 2025-08-04

1.第一次系统性的弄清了区块链与比特币的概念；
2.弄懂了公链，联盟链与私链的关系；
3.术语：RDF：描述事实，三元组；OWL：在RDF上增加逻辑推理能力，表达更丰富的语义和规则
4.知道web3与web3.0是不同的，后者是对语义网的数据组织升级，使得简单的展示转变为智能化的理解和推理；
5.去中心化的优势与不足；
6.NTF的概念；
7.了解了以太坊的概念；
8.了解了以太坊的发展历史，以及以太坊的文化价值观；
9.明白了以太坊核心机制：从账户到执行的完整链路
10.以太坊交易的一般流程


# 2025.07.30


<!-- Content_END -->
