---
timezone: UTC+8
---

# 杨玉妍

**GitHub ID:** kyamaaa

**Telegram:** @WeChat：wysmshcnxhbc_f

## Self-introduction

最常出现的位置：假期在广东东莞，非假期在广东茂名

## Notes

<!-- Content_START -->
# 2025-08-13

一、智能合约安全基础学习
（一）常见漏洞及复现
重入攻击
原理：攻击者利用合约调用外部合约时的漏洞，在外部合约中再次调用原合约的函数，从而重复获取资产。
复现过程：模拟一个简单的转账合约，当合约向外部地址转账时，未先更新状态变量，攻击者创建恶意合约，在接收转账的 fallback 函数中再次调用原合约的转账函数，导致多次转账。
整数溢出 / 下溢
原理：当整数达到其数据类型的最大值或最小值时，继续进行增减操作会导致数值异常。
复现过程：编写一个使用 uint8 类型的计数器合约，当计数器达到 255 时，再进行加 1 操作，数值变为 0，体现整数溢出；当计数器为 0 时，进行减 1 操作，数值变为 255，体现整数下溢。
权限管理漏洞
原理：合约中未合理设置访问权限，导致未授权用户可以执行敏感操作。
复现过程：创建一个带有修改重要参数函数的合约，但该函数未设置只有管理员可调用的权限，普通用户调用后成功修改参数。
（二）最佳安全实践
采用最小权限原则，为不同的函数和操作设置严格的访问权限，只允许必要的角色进行操作。
避免在合约中使用复杂的逻辑和不必要的外部调用，减少攻击面。
对用户输入进行严格验证和过滤，防止恶意数据注入。
定期对合约进行安全审计和漏洞扫描，及时发现和修复安全问题。
二、OpenZeppelin 合约学习与引入
（一）OpenZeppelin 合约简介
OpenZeppelin 是一个开源的智能合约库，提供了一系列经过安全审计和测试的合约，包括 ERC20、ERC721 等标准代币合约，以及安全相关的合约如 ReentrancyGuard、Ownable 等。
（二）引入 OpenZeppelin 合约到项目
在项目中安装 OpenZeppelin 库，可通过 npm 命令：npm install @openzeppelin/contracts。
在自己的合约中通过 import 语句引入所需的 OpenZeppelin 合约，例如：import "@openzeppelin/contracts/token/ERC20/ERC20.sol";。
继承引入的合约，从而使用其提供的功能，例如：contract MyToken is ERC20 {...}。
三、编写测试用例
（一）测试工具选择
使用 Hardhat 作为开发环境，搭配 Chai 断言库进行测试用例的编写。
（二）测试用例内容
测试代币转账功能：创建代币合约实例，进行转账操作，验证转账前后账户余额的变化是否正确。

    // 转账100个代币从owner到addr1
    await token.transfer(addr1.address, 100);
    expect(await token.balanceOf(addr1.address)).to.equal(100);

    // 转账50个代币从addr1到addr2
    await token.connect(addr1).transfer(addr2.address, 50);
    expect(await token.balanceOf(addr1.address)).to.equal(50);
    expect(await token.balanceOf(addr2.address)).to.equal(50);
  });
});

测试权限管理功能：验证只有管理员可以执行特定操作，普通用户无法执行。

  // 管理员执行操作成功
  await expect(contract.adminOperation())
    .to.emit(contract, "OperationPerformed")
    .withArgs(owner.address);

  // 普通用户执行操作失败
  await expect(contract.connect(addr1).adminOperation())
    .to.be.revertedWith("Not authorized");
});

测试防重入功能：模拟重入攻击场景，验证引入 ReentrancyGuard 后的合约是否能有效防止重入攻击。

  // 向易受攻击的合约存入一些以太币
  await owner.sendTransaction({ to: vulnerableContract.address, value: ethers.utils.parseEther("1.0") });

  // 攻击者尝试重入攻击
  await expect(attackerContract.attack())
    .to.be.revertedWith("ReentrancyGuard: reentrant call");
});

四、使用 Slither 进行安全检查及修复
（一）Slither 工具安装
通过 pip 命令安装 Slither：pip install slither-analyzer。
（二）使用 Slither 进行检查
在项目根目录下运行命令：slither .，Slither 会对合约进行静态分析，输出安全问题报告。
（三）修复安全问题
根据 Slither 的报告，对发现的安全漏洞进行修复。例如，若发现存在重入漏洞，则引入 OpenZeppelin 的 ReentrancyGuard 合约，并在相关函数上添加 nonReentrant 修饰符。

# 2025-08-12

完成前端页面与合约的交互，并将代码上传到 Git 仓库，以此完善整个小 Demo 的开发流程。
二、创建简单的前端页面与合约交互
（采用 HTML、CSS 和 JavaScript 进行简单的前端页面开发，同时使用 Ethers.js 库来实现与智能合约的交互。Ethers.js 是一个轻量级的以太坊库，对于前端与智能合约交互非常友好。
（二）搭建前端基础页面
创建一个新的文件夹frontend作为前端项目目录，在该目录下创建index.html文件。
在index.html中编写基础的页面结构，包括一个显示合约返回信息的区域和一个用于触发交互的按钮，代码如下：
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello World DApp</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-top: 50px;
        }
        #greeting {
            margin: 20px 0;
            padding: 10px;
            border: 1px solid #ccc;
        }
        button {
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <h1>Hello World DApp</h1>
    <div id="greeting"></div>
    <button onclick="fetchGreeting()">Get Greeting</button>

    <script src="https://cdn.ethers.io/lib/ethers-5.2.umd.min.js"></script>
    <script src="app.js"></script>
</body>
</html>

（三）编写与合约交互的 JavaScript 代码
在frontend目录下创建app.js文件。
在app.js中编写代码，实现连接到测试网、加载合约实例以及调用合约方法的功能，代码如下：
// 合约地址（需要替换为你部署到测试网的HelloWorld合约地址）
// 合约ABI（从Foundry编译后的合约文件中获取）
    // 检查浏览器是否安装了MetaMask等钱包
            // 请求用户授权连接钱包
            // 加载合约实例
            // 调用合约的greeting方法
            // 在页面上显示结果
ABI 可以从 Foundry 项目的 out 目录下对应的合约 JSON 文件中提取。
（四）测试前端与合约交互
将 HelloWorld 合约部署到测试网（使用 Foundry 的forge create命令进行部署，具体命令需根据测试网配置调整）。
替换app.js中的合约地址为部署后的实际地址。
在浏览器中打开index.html文件，确保已安装 MetaMask 并切换到对应的测试网，点击 “Get Greeting” 按钮，授权后即可看到合约返回的 “Hello, World!” 信息，说明前端与合约交互成功。
三、创建 Git 仓库并上传代码
在项目根目录（hello-world）下打开终端，执行git init命令，初始化一个新的 Git 仓库。
.gitignore 文件
node_modules/
out/
cache/
.DS_Store
.env

执行git add .命令，将项目中的所有文件添加到暂存区。
执行git commit -m "Initial commit: Hello World DApp with Foundry and frontend"命令，提交暂存区的文件到本地仓库。
在 GitHub 等代码托管平台创建一个新的远程仓库。
按照平台提示，在终端执行关联远程仓库的命令，例如：git remote add origin https://github.com/yourusername/hello-world-dapp.git。
执行git push -u origin main命令，将本地仓库的代码上传到远程仓库。

掌握了 DApp 的基本开发流程，包括智能合约编写、开发环境搭建、前端与合约交互以及代码管理

# 2025-08-11

今日开始学习 web3intern.xyz 上关于智能合约开发的技术方向手册，重点关注与前端相关的智能合约交互部分。通过手册初步了解到，智能合约开发与前端开发存在紧密联系，前端需要与智能合约进行数据交互以实现 DApp 的功能。​

二、Solidity 语法学习​
（一）基本概念​
Solidity 是一种用于编写智能合约的高级编程语言，语法类似于 JavaScript，它是面向合约的，运行在以太坊虚拟机（EVM）上。​
（二）关键语法点​
合约声明：使用contract关键字声明一个合约，例如contract HelloWorld {}。​​
其中memory关键字表示参数在函数执行结束后会被销毁。​
4. 数据类型：包括整数型（int/uint，有不同位数）地址（address）等。地址类型比较特殊，用于表示以太坊账户。​
三、开发环境搭建（Foundry）​
（一）Foundry 简介​
Foundry 是一个用于以太坊应用开发的工具链，包含编译器、测试框架、部署工具等，对于开发和测试智能合约很方便。​
（二）安装步骤​
打开终端，执行安装命令：
curl -L https://foundry.paradigm.xyz | bash​
​
安装完成后，运行foundryup来更新到最新版本，确保工具链是最新的。​
验证安装​ forge --version
四、DApp 的架构和运行方式初步了解​
（一）DApp 架构​
DApp 主要由前端界面和后端智能合约两部分组成。前端负责与用户交互，展示数据和接收用户操作；智能合约部署在区块链上，负责处理核心业务逻辑和数据存储。前端通过 RPC（远程过程调用）与区块链节点进行通信，从而与智能合约进行交互。​
（二）运行方式​
用户在前端进行操作后，前端将操作转化为对智能合约的调用请求，通过区块链节点发送到区块链网络。智能合约在区块链上执行相应的逻辑，处理结果会被记录在区块链上，前端再从区块链节点获取处理结果并展示给用户。​
五、实践：使用 Foundry 编写第一个 Hello World 合约​
（一）创建项目​
在终端进入想要创建项目的目录，执行forge init hello-world，创建一个名为 hello - world 的 Foundry 项目。​
（二）编写合约​
进入项目的 src 目录，创建HelloWorld.sol文件，编写如下合约代码：​
​
// SPDX-License-Identifier: MIT​
pragma solidity ^0.8.0;​
​
contract HelloWorld {​
    string public greeting = "Hello, World!";​
}​
​
// SPDX-License-Identifier: MIT指定了许可证；
pragma solidity ^0.8.0指定了 Solidity 的版本；
合约中定义了一个公共的字符串状态变量greeting，并初始化为 “Hello, World!”。​
（三）编译合约​
在项目根目录下，执行forge build命令编译合约，会在 out 目录下生成编译后的合约文件。

# 2025-08-09

规划前端技术栈和方向

# 2025-08-08

- #### 前端找到intern需要准备的技术
		- 我需要学的技术栈：
			- React   HTML5、CSS3、JavaScript（ES6+）
			- 基于 GraphQL 或 RESTful API 获取链上和链下数据
			- Viem 与智能合约进行交互
			- DApp开发经验
		- 学习路线：
			- 区块链基础概念
			- 以太坊开发工具链
				- viem库的学习
					- 学习连接钱包、读取链上数据、发送交易
					- 读取和写入智能合约
				- 开发环境
					- Hardhat 或 Foundry
					- Alchemy/Infura 节点服务
				- 工具
					- Etherscan API
					- 区块浏览器使用
			- 项目实践：打算做出以下三个项目并且发布到GitHub上
				- 代币余额查看器
					- React + Viem
					- 用etherscan api 实现交易历史展示
				- 开发一个 NFT 展示和转账 DApp
					- Next.js + Viem + TailwindCSS
					- 理解 nft 标准差异
					- 写一个简单的 NFT 合约（用 Hardhat）并与之交互
				- 构建一个 DeFi 协议前端 (如简单的借贷或交换界面)
					- React + Viem + Hardhat
				- DApp开发
					- Next.js + Viem + GraphQL
			- **避坑提示**：遇到 `ethers.providers.Web3Provider` 或 `web3.eth` 这类代码的仓库直接跳过，它们已经过时。

# 2025-08-07

- #### 行业赛道
		- DeFi（decentralized finance）
			- 协议
				- 借贷
					- Aave 
				- DEX - 去中心化交易所
					- Uniswap
						- 代币定价 - 依赖恒定乘积公式
					- Compound
						- 借贷
					- MakerDAO (Sky) 
						- 稳定币系统，抵押
		-   ---------------DeFi + NFT---数字资产金融化------------
		-   ---------------DeFi + AI-----智能化金融服务------------
		-   ---------------Web3 + 乡建-南塘DAO-------------------
		- NFT
			- 智能合约
				- 一种自执行的协议，意味着合约中的条款在满足特定条件时会自动执行，而无需第三方中介
				- 为NFT在转售时，原作者收版税的过程中，不需要第三方收额外的钱
				- CryptoPunks
					- 由Larva Labs创建
			- OpenSea
				- NFT交易平台
		- DAO —— 去中心化自治组织
			- Nouns DAO
				- 通过贩卖NFT，让买家的钱成为组织的启动资金，并且让买家投票决定资金的用处的组织
			- LXDAO
				- 工会 加 奖励平台
			- ConstitutionDAO
				- 拍卖会


		- 我需要学的技术栈：
			- React   HTML5、CSS3、JavaScript（ES6+）
			- 基于 GraphQL 或 RESTful API 获取链上和链下数据
			- Viem 与智能合约进行交互
			- Dapp开发经验

# 2025-08-06

- #### 行业赛道
		- DeFi（decentralized finance）
			- 协议
				- 借贷
					- Aave 
				- DEX - 去中心化交易所
					- Uniswap
						- 代币定价 - 依赖恒定乘积公式
					- Compound
						- 借贷
				- 衍生品
					- dYdX
				- RWA
	- NFT
		- OpenSea
		- 社交、游戏资产，如 Axie
	- Layer2扩容方案
		- 烂大街 雪崩  空投
	- 基础设施
		- 跨链桥 Wormhole D。。
	- 外部竞争/公链
		- Solana
		-  雪崩
	- 稳定币
		- USDT
		- USDC
		- DAI
	- 去中心化金额
		- 组池子，放贷，空投，链上测试LP，借贷
	- 闪电贷
		- ==很有意思，不成功就会回滚==
	- uniswapp交互
		- 推荐okx而不是metamask
		-  闪电贷套利  
			发布土狗合约  
			闪兑
		- v3和v4的差别
			- v4提出brollprotical
			- 所有流动性变成一个池子，
		- 做测试网
		- 熊链

# 2025-08-05

- #### 以太坊
		- 和比特币的区别：
			- 比特币是区块链1.0，货币属性
			- 以太坊是2.0，智能合约和可编程性
				- Solidity开发智能合约
			- 区块时间由10min -> 12sec
			- ==通胀 -> 通缩==
			- The Merge：PoW -> PoS
				- PoW 依靠计算机算力挖矿，消耗电力
				- PoS   32ETH - 质押 - 验证者
		- 特点：灵活
		- 升级方向：
			- 分片
				- Layer2扩容 - 数据分片 - EIP-4844
					- L2使用常规交易，**EIP-4844引入Blob交易类型**，数据存储成本降低
				- 全面分片预计 **2025-2026 年** 启动，重点是 Proto-Danksharding
			- ZK-Rollup
				- 证明
			- 其他：
				- Verkle树技术：优化状态存储结构，减少节点同步所需的数据量
				- 执行环境优化：提升 EVM 性能，支持更复杂的智能合约应用
					- EVM 以太坊虚拟机
		- 生态概览：
			- 应用层 Application Layer
				- 用户直接交互
				- **DeFi 应用**：Uniswap（去中心化交易所）、Aave（借贷协议）、Compound（借贷协议）
				- **NFT 平台**：OpenSea、Foundation、SuperRare
				- **钱包应用**：MetaMask、Coinbase Wallet、Rainbow
				- **DAO 工具**：Snapshot、Aragon、Colony
			- 协议层 Protocol Layer
				- **共识层客户端**：Prysm、Lighthouse、Nimbus、Teku
				- **执行层客户端**：Geth、Nethermind、Erigon、Besu
				- **核心协议**：EVM、状态管理、Gas 机制
			- 扩展层 Scaling Layer  
				- 提升性能、降低成本
					- **Layer 2 Rollups**：Arbitrum、Optimism、Polygon zkEVM、zkSync Era
					- **侧链**：Polygon PoS、xDAI（Gnosis Chain）
					- **状态通道**：Lightning Network for Ethereum
		- 核心机制
			- 账户系统
				- 私钥控制 EOA
				- 智能合约控制 CA
			- Gas 模型
				- 激励矿工/验证者
				- ==矿工就是验证者吗？==
				- 基础费用用来帮助ETH通缩，会消失
				- ==怎么理解通缩？==
			- 以太坊虚拟机 EVM
				- 运行智能合约的虚拟计算机

# 2025-08-04

# 学习内容：
#### 简介：1、看了入门导读之区块链的基础概念，做了笔记    2、下载并注册了web3常用的应用程序，使用metamask实现了链上代币测试   

#### 相关图片佐证：
https://postimg.cc/T5gY5rBv
https://postimg.cc/jD50GHvq
https://postimg.cc/kRgPqW9y

#### 关于图片的笔记：
交易哈希：
- 区块链上这笔交易的唯一ID
区块：
- 这个交易所在的区块高度
Gas价格：
- 交易中每个执行单元的花费（用ether和gwei做单位），Gas价格越高，被写到区块中的机会越大。

笔记：
- ### 概念
	- #### 区块
		- 每一个区块只有有限的存储容量。
		- 每个区块会在一定时间内打包， 10 分钟打包生成一个区块。
		- **核心特性**
			- 去中心化
			- 公开透明（但匿名）
				- 钱包是随机生成的
			- 快速交易
			- 不可篡改，其实可以篡改，但是很难很难，因为区块链是依照由节点串联起来的，若有人想要去篡改，被修改记录的节点不超过 51%，这个改动将不会被认可。
				- 网络节点服务提供商可以得到比特币的奖励
					- 比特币的特性：
						- 白皮书设计，价格由供需关系决定：
						- 不通胀，不稳定，价格高
			- 智能合约
				- 以太坊 V神  (ethereum)
			- 共识机制
				- ![[Pasted image 20250804195248.png]]
				- PoW 
				- PoS
					- 单独质押  ethereum.org/zh/staking/solo/
						- 32个以太币 验证者  节点记账
					- 服务质押
						- 第三方机构背书
					- 联合质押
				- EOS
				- ==？？：所以这几个之间有什么关系==
		- **核心组成：
			- 节点通过计算验证交易获得代币，无数节点提供的服务统称为挖矿，维持服务的人叫矿工
			- 步骤：
				1. **用户发起交易**：用户通过钱包应用发起转账、智能合约调用等操作
				2. **交易广播**：交易信息被广播到**整个网络中的各个节点**
				3. **节点验证**：网络中的矿工节点验证**交易的合法**性（余额是否足够、签名是否正确等）
				4. **打包成块**：通过共识机制（如工作量证明），矿工将验证过的交易**打包成新的区块**
				5. **链接上链**：新区块被添加到区块链上，更新全网的账本状态
				6. **奖励发放**：成功打包区块的矿工获得代币奖励和交易手续费
	- #### 公链 vs 私链 vs 联盟链
		- 不同点：成为节点的方法 - 管理数据的模式
		- 公链
			- 去中心化，共识机制，透明，效率低成本高
		- 联盟链
			- 多中心化，半公开，节点邀请制，成员权限分级
		- 私链
			- 中心化
	- #### Web3 vs Web3.0 vs Web2
		- Web3.0    语义网驱动
			- 知识图谱、语义搜索
			- RDF、OWL 描述数据关系
			- 区块链混合存储，部分开放
		- Web3        区块链驱动
			- 去中心化金融
			- 智能合约
			- 区块链存储 IPFS
			- ![[Pasted image 20250804220323.png]]
			- 技术需求
				- React + Ethers.js + Solidity/Rust + IPFS
	- 以太坊
	- 以太坊生态赛道
	- DeFi（finance）
		- 协议
			- 借贷
				- Aave 
			- DEX
				- Uniswap 交易所  代币
				- Curve
				- h
			- 衍生品
				- dYdX
			- RWA
	- NFT
		- OpenSea
		- 社交、游戏资产，如 Axie
	- Layer2扩容方案
		- 烂大街 雪崩  空投
	- 基础设施
		- 跨链桥 Wormhole D。。
	- 外部竞争/公链
		- Solana
		-  雪崩
	- 稳定币
		- USDT
		- USDC
		- DAI
	- 去中心化金额
		- 组池子，放贷，空投，链上测试LP，借贷
	- 闪电贷
		- ==很有意思，不成功就会回滚==
	- uniswapp交互
		- 推荐okx而不是metamask
		-  闪电贷套利  
			发布土狗合约  
			闪兑
		- v3和v4的差别
			- v4提出brollprotical
			- 所有流动性变成一个池子，
		- 做测试网
		- 熊链

# 2025.07.31


<!-- Content_END -->
