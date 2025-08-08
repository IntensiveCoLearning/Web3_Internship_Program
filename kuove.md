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
