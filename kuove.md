---
timezone: UTC+8
---

# kuve

**GitHub ID:** kuove

**Telegram:** @stephkuove

## Self-introduction

web2转型web3,希望学习测试与开发

## Notes

<!-- Content_START -->
# 2025-08-18

# ERC-20学习

参考 /https://learnblockchain.cn/article/15741

有一些核心合约实现了 ERC-20 标准中指定的行为：

- [`IERC20`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#IERC20)：所有 ERC-20 实现都应符合的接口。
- [`IERC20Metadata`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#IERC20Metadata)：扩展的 ERC-20 接口，包括 [`name`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20-name--)、[`symbol`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20-symbol--) 和 [`decimals`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20-decimals--) 函数。
- [`ERC20`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20)：ERC-20 接口的实现，包括 [`name`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20-name--)、[`symbol`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20-symbol--) 和 [`decimals`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20-decimals--) 标准接口的可选扩展。

此外，还有多个自定义扩展，包括：

- [`ERC20Permit`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20Permit)：代币的 gasless approval（标准化为 ERC-2612）。
- [`ERC20Burnable`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20Burnable)：销毁自己的代币。
- [`ERC20Capped`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20Capped)：在铸造代币时强制执行总供应量的上限。
- [`ERC20Pausable`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20Pausable)：暂停代币转账的能力。
- [`ERC20FlashMint`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20FlashMint)：通过临时代币的铸造和销毁对闪电贷的代币级别支持（标准化为 ERC-3156）。
- [`ERC20Votes`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20Votes)：对投票和投票委托的支持。
- [`ERC20Wrapper`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20Wrapper)：包装器，用于创建由另一个 ERC-20 支持的 ERC-20，具有存款和取款方法。 与 [`ERC20Votes`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20Votes) 结合使用非常有用。
- [`ERC20TemporaryApproval`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20TemporaryApproval)：支持仅持续一笔交易的 approval，如 ERC-7674 中定义。
- [`ERC1363`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC1363)：支持调用转移或 approval 的目标，从而可以在单笔交易中在接收者上执行代码。
- [`ERC4626`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC4626)：代币化金库，管理由资产（另一个 ERC-20）支持的股份（表示为 ERC-20）。

最后，有一些实用程序可以以各种方式与 ERC-20 合约交互：

- [`SafeERC20`](https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#SafeERC20)：接口的包装器，无需处理布尔返回值。

可以在代码库中找到支持 ERC-20 资产的其他实用程序：

- 可以使用 [`VestingWallet`](https://docs.openzeppelin.com/contracts/5.x/api/finance#VestingWallet) 对 ERC-20 代币进行时间锁定（为受益人持有到指定时间）或归属（按照给定的时间表发布）。

# 2025-08-17

## **8.11**

- [defi-fixed-yield-course](https://github.com/crazyyuan/defi-fixed-yield-course/tree/main/docs/lessons)有一个简单的利率合约，和基础的合约语言和概念
- [EIP Fun](https://eip.fun/) 就是一个围绕以太坊技术标准（EIP）的社区，通过周刊和其他活动来推广和促进EIP 的应用。
- [solidity基础教学](https://www.thinkingsolidity.com/solidity/)，有点久远
- [lido](https://help.lido.fi/zh-CN/)，质押以获得收益，代币是LDO

## **8.12**

- [web3求职](http://web3.career/)网站
- [ethernaut](https://ethernaut.openzeppelin.com/)合约攻防挑战

## **8.13**

[本地搭建测试网](https://www.notion.so/24bdceffe40b80148b07fa28483662cf?pvs=21)

## **8.14**

- [wongssh分享](https://www.bilibili.com/video/BV13RbEz8EBt/?spm_id_from=333.337.search-card.all.click&vd_source=e8f402625b500cebc8306d525d537727)，一个真实的 DApp 开发全流程，Gas 优化 & 审计技巧分享
- 建议的学习路线 ERC20 -> Safe -> ~~AAVE v3~~ Uniswap v2 v3
- [wong老师个人博客](https://blog.wssh.trade/)
- 特别值得看的
    - [Foundry教程：编写测试部署ERC-20代币智能合约](https://blog.wssh.trade/posts/foundry-with-erc20/)
    - [现代 DeFi: Uniswap V3](https://blog.wssh.trade/posts/uniswap-v3/)
- [wong老师推荐](https://t.me/web3list)
- [blue老师总结的学习路线](https://github.com/B1u-e/My-Web3-Journey)

## **8.15**

- [DeFiLlama](https://defillama.com/)链上数据分析
- [SpeedRunEthereum](https://speedrunethereum.com/) DApp学习

# 2025-08-15

今天参加了周例会，开始进行 Ethernaut 的挑战，并感叹同学们学的好快

Ethernaut.sol 合约（核心协议）

角色：游戏主合约，负责全局管理和关卡逻辑校验
关键功能：

createLevelInstance()：部署新的关卡实例合约
submit()：验证玩家提交的关卡实例
registerLevel()：允许管理员注册新关卡模板
claim()：测试网络代币分发水龙头
Level Instance（动态生成的子合约）

角色：每个关卡的具体逻辑容器
关键特性：

通过new关键字动态部署（每个实例唯一地址）
继承自基础关卡模板（如LevelInstance.sol）
包含待解决的谜题逻辑和验证方法
TruffleContract（开发工具封装）

角色：前端与智能合约交互的桥梁
核心功能：

ABI解析：自动将ABI转换为JavaScript方法
交易处理：签名、Gas估算、重播保护
事件监听：自动解码合约事件
ethernaut（TruffleContract实例化对象）
关系图：ethernaut = new TruffleContract(ethernautABI, ethernautAddress)
功能特性：

已部署合约的本地引用
包含所有可调用方法的快捷方式
自动处理交易签名和网络切换

# 2025-08-14

参加了王首豪老师的技术分享，内容很多正在慢慢消化

把defi-fixed-yield-course中剩下的两个合约看了一下，总结了作用

MockERC20.sol

模仿ERC20的mint

RewardToken.sol
作用：允许指定的金库或账户 mint 奖励代币给用户。

主要功能：
- 继承自 ERC20 标准和 Ownable，支持标准代币操作和权限管理。
- 通过 isMinter 映射，合约拥有者可以授权或取消任意账户的铸币权限。
- 只有被授权的 minter 或合约 owner 才能调用 mint 方法。

# 2025-08-13

nonReentrant关键字
nonReentrant 是 Solidity 中用于防止重入攻击的修饰符，来自 ReentrancyGuard 合约（是 OpenZeppelin 提供的一个 Solidity 合约，用于防止重入攻击（Reentrancy Attack））。
作用：它确保一个函数在执行过程中不能被再次（递归或嵌套）调用，防止攻击者通过合约回调反复进入函数，造成资金损失或状态异常。


deposit函数基于父合约 ERC4626，通过 super.deposit(assets, receiver)，调用并返回父合约的 deposit 方法的结果，确保父合约的存款流程被执行。

_acccrue函数的作用是结算和累积用户的奖励

claim 用于用户领取自己已累计的奖励，并将奖励以代币形式发放给用户。

balanceOf函数
继承于ERC20，用于查询某个地址持有的代币数量。

getPendingReward
作用：查询某个用户当前可领取的奖励数量，包括已累计但未领取的奖励和最近还未结算的奖励。
详细流程：
1.获取已累计奖励读取 rewardAccruedByUser[user]，这是用户之前已经结算但还没领取的奖励。
2.计算用户当前资产用 convertToAssets(balanceOf(user)) 得到用户当前持有的资产数量。
3.如果资产为 0直接返回已累计奖励，无需进一步计算。
4.计算当前全局奖励累加值
- 先取 globalAccumulatedRewardPerToken。
- 如果金库有存款（totalSupply() > 0），计算自上次结算到现在的新增奖励，并加到全局累加值上。
5.计算用户未结算奖励
- 计算用户自上次结算后应得但还没结算的奖励（unpaidRewards）。
- 按资产比例计算实际奖励（pendingRewards = (assets * unpaidRewards) / 1e18）。
6.返回总奖励返回已累计奖励加上未结算奖励。

# 2025-08-12

一个 DApp（去中心化应用）通常由以下几个主要组成部分：

1.智能合约（Smart Contracts）部署在区块链上的后端逻辑，负责核心业务处理和数据存储。

2.前端界面（Frontend）用户交互界面，通常用 Web 技术（如 React/Vue/HTML/CSS/JS）开发，通过 Web3 库与智能合约通信。

3.区块链网络（Blockchain Network）例如以太坊、Polygon、BSC 等，承载智能合约和交易。

4.钱包（Wallet）用户用于身份认证和签名交易的工具，如 MetaMask、WalletConnect。

5.后端服务（可选）用于辅助功能，如数据索引、缓存、分析等（如 The Graph、中心化 API）。

6.Web3 库前端与区块链交互的桥梁，如 ethers.js、web3.js。


#####defi-fixed-yield-course

1.合约层contrats
包含了三个合约文件
- FixedRateERC4626Vault.sol
- MockERC20.sol
- RewardToken.sol
FixedRateERC4626Vault.sol
功能
定义了一个基于 ERC4626 标准的固定利率收益金库合约 FixedRateERC4626Vault，主要作用如下：
1.金库管理用户可以存入和取出资产（ERC20 代币），合约按照固定年化利率（annualRateBps）计算奖励。
2.奖励分发奖励以单独的奖励代币（RewardToken）发放，用户可随时领取已累计的奖励。
3.核心功能
a.存款、取款、铸造、赎回等金库操作均自动结算奖励。
b.合约拥有者可调整年化利率。
c.通过事件记录奖励领取和利率变更。
4.安全性继承 Ownable（权限管理）和 ReentrancyGuard（防重入攻击）。
代码分析
interface IRewardToken {
    function mint(address to, uint256 amount) external;
}
interface定义了IRewardToken，IRewardToken声明一个mint函数，实现IRewardToken接口的合约必须有这个函数（有点像c++虚函数？）
external表示该函数只能从外部被调用

# 2025-08-11

参加 DApp 架构与从 0 到部署 分享会

复习之前学习的hardhat相关内容
结合分享会的教学继续学习

interface 关键字用于定义一个接口。接口是一组函数声明，没有具体实现。它通常用来描述合约之间的交互规范，比如你希望和某个外部合约交互，但只关心它暴露的函数，而不需要知道它的具体实现。

immutable 关键字用于声明不可变变量。
这种变量只能在合约的构造函数中赋值，之后不能再更改。它的值在部署后固定，且比 storage 变量更省 gas

constructor 关键字用于定义合约的构造函数。
构造函数只在合约部署时执行一次，用于初始化合约状态，比如设置初始参数、赋值 immutable 变量等。

# 2025-08-08

OpenZeppelin学习
大纲
- Contracts

&emsp;用于智能合约开发的库。
- Upgrades

&emsp;OpenZeppelin 提供了用于部署和保证可升级的智能合约的工具。
升级插件部署可以自动检查的合约。

&emsp;可升级合约，使用 Solidity 组件构建合约。

&emsp;Defender Admin用于管理生产升级和自动化。

- Defender

&emsp;OpenZeppelin Defender 为以太坊提供了具有内置最佳实践的安全操作(SecOps) 平台。
到2026停止维护，转到开源计划，可以自行托管 Relayers、Monitor 的开源版本以及即将推出的工具，以便在自己的环境中维护当前功能。

- Subgraphs

用于轻松索引 OpenZeppelin Contracts 活动的模块

- Test Helpers

用于以太坊智能合约测试的断言库。

- solidity-docgen
智能合约库的文档生成器。使用 Solidity 代码中的内连文档来生成网站或任何类型的文档。

# 2025-08-07

- 继续forge的学习

&emsp;forge test 运行后会生成两个新的目录，产生了两个新目录：out 和 cache
&emsp;out 目录包含你的合约工件(artifact，例如 ABI，而 cache 目录被 forge 使用来（记录），以便仅仅去重新编译那些必要编译的内容。

- 了解Forge依赖项
&emsp;使用 git submodules 管理，意味着它可以与任何包含智能合约的 GitHub 代码库一起使用

- Soldeer 是forge的包管理器

- 注意到了openzeppelin

&emsp;OpenZeppelin 是构建在 EVM 之上的开源智能合约开发工具，让开发者者可以安全地开发和管理智能合约和 Dapp。OpenZeppelin 使用以太坊智能合约语言 Solidity 进行构建，并支持所有 EVM 和 eWASM 的跨平台移植。

# 2025-08-06

Foundry学习

Foundry的四个工具包及其作用
forge:       Build, test, debug, deploy and verify smart contracts
anvilRun: a local Ethereum development node with forking capabilities
cast:        Interact with contracts, send transactions, and retrieve chain data
chisel:      Fast Solidity REPL for rapid prototyping and debugging

创建一个项目，了解项目结构

参加Bruce组破冰
参加 Web3 故事会

# 2025-08-05

静态数组和动态数组
状态变量被永久保存在区块链中。所以在你的合约中创建动态数组来保存成结构的数据是非常有意义的。

public
private：私有函数命名用“_”开头
view函数：只读
pure函数：不访问数据
散列函数keccak256：把string转换为256位16进制数（不安全）

事件：是合约和区块链通讯的机制，前端监听事件，作出反映

映射：mapping(key => value)，通过键找值

msg.sender:当前调用者的address
注意：在 Solidity 中，功能执行始终需要从外部调用者开始。 一个合约只会在区块链上什么也不做，除非有人调用其中的函数。所以 msg.sender总是存在的。
require: 当不满足条件时抛出错误，停止执行
注：solidity不支持字符串比较，只能用keccak256函数进行哈希值比较

# 2025-08-04

web3测试方法
1.单元测试（Unit Testing）
目标：
测试智能合约中每个函数的独立功能。

方法：
编写测试用例：为每个函数编写测试用例，覆盖正常和异常情况。

使用测试框架：

Truffle：内置 Mocha 和 Chai 支持。

Hardhat：支持 Waffle 和 Ethers.js。

Foundry：支持 Solidity 原生测试。

模拟环境：使用本地区块链（如 Ganache、Hardhat Network）进行测试。


# 2025.07.29


<!-- Content_END -->
