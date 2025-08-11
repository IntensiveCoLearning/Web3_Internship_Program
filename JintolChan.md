---
timezone: UTC+8
---

# Jintol

**GitHub ID:** JintolChan

**Telegram:** @JintolOfficial

## Self-introduction

Freelance

## Notes

<!-- Content_START -->
# 2025-08-11

## 智能合约基础特性
### 什么是智能合约
智能合约是以太坊应用层的基础构建模块。它们是存储在区块链上的计算机程序，遵循“如果这样，那么那样”的逻辑，并保证按照其代码定义的规则执行，一旦创建，这些规则就无法更改。

Nick Szabo提出了“智能合约”这一概念。1994年，他撰写了关于该概念的介绍性文章，1996年又撰写了关于智能合约潜在应用的探索性文章。

萨博设想了一个数字市场，其中自动、加密安全的流程使交易和商业功能能够在无需可信中介的情况下实现。以太坊上的智能合约将这一愿景付诸实践。 
### 智能合约核心特性
不可篡改性：部署代码后无法修改  
透明性：所有人可查看验证  
可编程性：灵活定制业务逻辑  
去中心化：运行在分布式网络
### 状态存储与执行环境
区块链是一个全局状态机，每个区块包含一组交易，这些交易会改变整个网络的状态。状态包括所有账户余额、合约存储数据等信息。  
状态转换过程：  
区块创建：矿工收集待处理交易  
状态计算：按顺序执行每笔交易，计算新状态  
状态根更新：所有状态变化汇总为新的状态根哈希  
区块链接：新区块通过哈希链接到前一个区块  
### Gas 机制
总费用 = Gas Used * (Base Fee + Priority Fee)


Gas Used：实际消耗的计算量，由操作复杂度决定固定值  
Base Fee：网络基础费用，网络自动调整被销毁  
Priority Fee：优先级小费，用户设置给验证者

网络拥堵时费用飙升的原因：  
有限的区块空间：每个区块只能容纳约 30M Gas  
Base Fee 自动调整：高使用率推高基础费用  
Priority Fee 竞价：用户提高小费争夺优先级

## Hardhat部署合约流程
### 开发设备环境准备
本地开发环境
```
# 确保 Node.js 版本 >= 18
node --version

# 安装项目依赖
npm install

# 编译合约检查语法
npx hardhat compile
```
环境变量配置  
创建 .env 文件：
```
# RPC 节点配置
SEPOLIA_RPC_URL=https://sepolia.infura.io/v3/YOUR_PROJECT_ID
# 或使用 Alchemy: https://eth-sepolia.g.alchemy.com/v2/YOUR_API_KEY

# 部署者私钥
PRIVATE_KEY=0x... # ⚠️ 仅用于测试网，注意安全

# 合约验证
ETHERSCAN_API_KEY=YOUR_ETHERSCAN_API_KEY
```
### 部署配置与网络连接
Hardhat 网络配置

### 合约部署执行
部署命令执行
```
# 执行部署到 Sepolia
npx hardhat ignition deploy yourfile --network sepolia
```
### 部署验证清单
 合约编译成功  
 测试网 ETH 余额充足  
 所有合约部署成功  
 获得12个区块确认  
 Etherscan 验证通过  
 前端环境变量已更新  
 合约交互测试正常

# 2025-08-09

# Solidity开发入门 Ⅰ
## 合约
pragma是一个用于指定编译器版本的关键字。它的作用是确保代码能够在特定版本的编译器下正确编译和执行，以避免潜在的兼容性问题。
```solidity
pragma solidity ^0.8.20;
```
在最新版本的ERC20中，使用的编译器版本不低于0.8.20  

使用关键字 contract 定义合约，一个 Solidity 的 .sol 文件可以包含一个或多个 contract。  
```solidity
contract Name { }//要定义一个 合约，我们使用关键字 contract，后面跟上合约的名称。
```
## 变量
int 表示整数的变量类型  
uint 表示正整数的变量类型  
bool 布尔变量只有两个值：true 或 false，通常用于判断。

# 2025-08-08

# 以太坊开发环境搭建
## 基础环境搭建  
Node.js  
npm  
Git  
## 开发框架Hardhat
创建项目
```
npm install --global hardhat
mkdir eth-dev && cd eth-dev
npx hardhat
```
启动本地节点
```
npx hardhat node
```
部署智能合约
```
npx hardhat run scripts/deploy.js --network localhost
```

# 2025-08-07

# 判断Web3公司/项目是否合规

项目是否在明确、受监管的司法辖区内注册  
是否为了为了取得合规资质，经过专业机构的第三方审计或安全测试  
是否具备KYC、AML等反洗钱与用户身份识别制度  
是否对外公开项目负责人、团队背景、资金来源路径等基本信息  
是否具备高风险模块  
是否存在高风险的扩张方式

# 2025-08-06

Web3 故事会

2018年：Dapp爆发年

EOS失败的原因：
过度中心化，DPos的治理危机；资源模型设计失败，RAM炒作与用户体验恶化；技术承诺未兑现，性能与安全性问题；生态激励不足，资金耗尽与开发者流失；社区分裂与品牌失信。

# 2025-08-05

打卡


参加Web3 安全分享和答疑，学习web3安全，学习区块链黑暗森林自救手册

# 2025-08-04

打卡

参加以太坊中文周会，了解最新行业动态；
Gather Town自习，学习Uniswap V3

# 2025.07.31


<!-- Content_END -->
