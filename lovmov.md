---
timezone: UTC+8
---

# 刘劲松

**GitHub ID:** lovmov

**Telegram:** @-vooodooo-

## Self-introduction

坐标深圳，证券行业，原来做IT，目前在市场部。

## Notes

<!-- Content_START -->
# 2025-08-20

Solana出块时间为400毫秒  
每秒能处理71万笔交易  
相当于Visa目前处理能力的30倍  
交易费用大约在1%美分左右  

共识机制：  
不完成使用POS证明，而使用一种叫历史证明的机制，本质上是权益证明，但加入了一个特殊的时间变量：时间。  
时间戳给区块表示具体的日期和时间。

# 2025-08-18

Farcaster概述  
Farcaster 是一个基于以太坊构建的开源社交网络，旨在实现去中心化，允许用户创建个人资料、发布帖子（称为 Cast）、回复和组织社区（称为 Channels）。任何人都可以在 Farcaster 上创建账户，这是一个替代其他社交网络的选项，如 X（即 Twitter）和 Threads（由 Meta 提供）。  
Farcaster 在链上存储用户身份，而像帖子和回复这样的数据则存储在链外。链上存储的主要数据包括 - 账户创建、发布数据（帖子、回复等）和将其他账户密钥添加到应用程序（稍后我们会讨论）。Farcaster 的链外架构使用被称为 “Hubs” 的节点，允许用户/开发者从 Farcaster 读取/写入。大多数 Farcaster 数据存储在这个网络上。任何人都可以运行 Hubble 节点，但你需要符合最低硬件 要求。   

客户端  
Warpcast 是一个旨在运行类似 Farcaster 的应用和网络的客户端。它只是 Farcaster 运行的应用。用户最初可能会混淆二者，但 Warpcast 只是一个用于运行 Farcaster 的客户端（没有其他功能）。还有其他替代客户端，如 Supercast 、Tako等。

# 2025-08-16

今天安装好了docker和kurtosis，但运行kurtosis run --enclave my-testnet github.com/ethpandaops/ethereum-package  还是出错

There was an error validating Starlark code 
Couldn't create validator environment as we ran into errors fetching existing services and ports
	Caused by: An error occurred while fetching service 'cl-1-lighthouse-geth' for its private port mappings
 --- at /home/circleci/project/core/server/api_container/server/startosis_engine/startosis_validator.go:292 (getServiceNameToPortIDsMap) ---

Error encountered running Starlark code.

⭐ us on GitHub - https://github.com/kurtosis-tech/kurtosis
WARN[2025-08-16T23:38:22+08:00] Tried getting number of services in the enclave to log metrics but failed 
Name:            my-testnet
UUID:            51c55d1d5bf7
Status:          RUNNING
Creation Time:   Sat, 16 Aug 2025 23:20:57 CST
Flags:

# 2025-08-15

今天在macOS系统上完成了docker和kurtosis的安装，但还没完成搭建测试网络。在前几天的老师讲课中第一次听说kurtosis，目前还在网站https://docs.kurtosis.com/install上看说明一步步学习。

# 2025-08-13

下午试着在办公电脑的wsl环境中去安装docker等，由于内部系统用的是win10的定制版，wsl也没安装成功，后面就看资料学习，后续再研究研究怎么把环境搭建起来。  
晚上再跟着老师学习技术向和运营向的直播。

# 2025-08-12

学习了一些英文缩写，比如ABI、Alpha、Ape、Oracle、Optimism、Rollup等。   
晚上的产品课学到的启示：
1. 可组合性（Composability）
2. 吸血鬼攻击 （Vampire Attack）
3. 代币标准化 （Token Standards）
4. 治理与团队稳定性
5. 长期主义 vs 短期投机

运营课学到如何实操组织活动，包括Telegram的运营、figma的使用等。

# 2025-08-11

一、下午参加每周一的以太坊中文周会   
二、晚上参加技术向分享会和运营向分享会 
   
Dapp开发有以下几个步骤：
1. 需求分析
2. 智能合约开发
3. 前端开发
4. 与区块链交互
5. 部署与上线
  
还有运营向所需要的基础能力和进阶能力，
以及该项工作的魅力和风险。

# 2025-08-10

今天安装了MetaMask，从google水龙头提取了测试币，也和其他学员互转了测试币。

# 2025-08-09

今天很幸运注册telegram成功了，前几天用魔法后tg、tg x都不能成功注册。随后登上X，关注了@ETHPanda_Org  @LXDAO_Official  @IntensiveCL，并发布了一条推文。

# 2025-08-08

听了几位学员的分享，有技术方面的也有网上被骗的经历等，受益良多，solidity、rust语言和remix开发平台等都很陌生，后续要多抽时间学习。

# 2025-08-07

Web3 法律知识学习

# 2025-08-06

学习web3发展历史、BM和Vitalik的故事，其他同学的提问和Marcus老师的讲解。

# 2025-08-05

今天学习钓鱼、反钓鱼安全相关内容和https://unphishable.io/ 网站的操作。
Web3 Phishing?
●與Web2釣魚的差異
         ■ Web2:重點是偷帳號(帳密)
         ■ Web3:重點是偷資産(真金白銀)
         ■ Web2可透過客服申訴找回帳號,Web3無法逆轉交易
● Web3特有的風險點
         ■使用者只要簽署一次交易，就可能授權合約永久存取資産
         ■錢包一般來説無法「鎖帳號」,被盗後資産難以追回
         ■很多交易看起來「無害」,但實際是轉帳或授權

# 2025-08-04

今天第一次参加以太坊中文周会，听着各位大咖的分享，目前还有很多专业的听不懂，后期需要加强学习。


# 2025.08.01


<!-- Content_END -->
