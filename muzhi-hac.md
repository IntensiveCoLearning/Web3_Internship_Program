---
timezone: UTC+8
---

# 王泽宇

**GitHub ID:** muzhi-hac

**Telegram:** @muzhi7778

## Self-introduction

国内本科生在读，暑假想入门web3，希望和大家一起多交流，希望这次能报名成功。

## Notes

<!-- Content_START -->
# 2025-08-12

今日总结： 今天深入学习了 Solidity 的高级交互和底层机制，收获巨大。核心是理解合约如何“对话”以及 EVM 如何“思考”。不再是单纯地写代码，而是开始能从“架构师”的角度审视合约的设计。总结一下关键点，加深记忆。

一、 合约间的“四种对话模式”
call

我的理解：最基础、最通用的“打电话”方式。可以调用任何合约的任何函数，还能附带 ETH。

关键点：非常底层，它不抛出错误，而是返回 (bool success, bytes memory data)。必须手动检查 success，否则对方调用失败了我的合约还在继续跑，会出大事。

delegatecall

我的理解：“灵魂互换”。这是今天最需要警惕的知识点。它用我的“身体”（storage, msg.sender, msg.value），去执行别人的“思想”（目标合约的代码）。

关键点：代理合约（Proxy Contract）的精髓所在。但也极其危险，必须确保两个合约的存储布局完全一致，否则会造成灾难性的数据错乱。

create vs create2

create：传统的合约创建方式，new 关键字的底层实现。地址依赖于创建者的 nonce，像银行的流水号，不好预测。

create2：今天的最大收获之一！核心是引入了 salt（盐） 这个可控变量。地址可以预先精确计算，像预留的门牌号。

关键应用：Uniswap V2 用两个代币地址排序后的哈希作为 salt，保证了任何交易对（Pair）的地址都是全网唯一的。这彻底解决了 create 可能导致的流动性碎片化问题。天才般的设计！

二、 合约的“生命终点”与异常处理
selfdestruct

我的理解：坎昆升级后，它不再是“终结者（Terminator）”，更像个“清算员（Liquidator）”。

关键点：对已部署的合约，它只转移 ETH，不再删除代码和存储。只有在“同一笔交易内创建并立即销毁”这个特殊场景下，代码才会被真正抹除。这个变化是为了以太坊长期的状态管理，必须跟上。

try-catch

我的理解：Solidity 里的“防火墙”，但不是什么火都能防。

关键点：它只保护外部调用和合约创建，不能捕获内部函数的失败。catch 还能细分为 Error（捕获 require/revert 的原因字符串）和 Panic（捕-获 assert 等致命错误），让容错处理更精细。

三、 与 EVM 的“沟通密码”
ABI (编码/解码)

我的理解：合约与外界沟通的“普通话”。

关键点：abi.encode 是官方标准，带填充，安全无歧义。abi.encodePacked 是“方言”，紧凑但有碰撞风险，只应该在需要计算哈希时使用。

keccak256 (Hash)

我的理解：一台“数字指纹机”。任何微小的输入变化都会导致输出结果天翻地覆。

关键点：记住，Solidity 里的 sha3() 其实是 keccak256，为了清晰，永远直接用 keccak256。

Selector (函数选择器)

我的理解：合约的“分机号”。

关键点：就是函数签名字符串哈希后的前4个字节。calldata 的开头就是它，EVM靠它来决定该调用哪个函数。

四、 代码的“模块化与重用”
library & import

我的理解：基础但重要，是代码工程化的体现。

关键点：library 里的函数默认是 internal 的。通过 using A for B; 这个语法糖，可以像调用成员函数一样调用库函数，让代码更优雅（比如 OpenZeppelin 的 SafeMath）。import 则是管理复杂项目的必备技能。

# 2025-08-10

https://unphishable.io/ 攻防挑战练习

# 2025-08-09

🤔调研一下相关产品，思考一下，计划做点有趣的东西

# 2025-08-08

solidity Overloading, Library, Import, Receive ETH, Sending ETH, Interact with Contract, Call, Delegatecall, Create, Create2, DeleteContract, ABI Encoding and Decoding, Hash, Function Selector, Try Catch

# 2025-08-06

solidity learing:
Function, Return, Datastorage, ArrayAndStruct,Mapping, InitialValue, Constant, InsertionSort, Modifier, Event.

# 2025-08-05

今日通过wtf学习solidily基本内容。

# 2025-08-04

了解区块链，web3，下载使用相关app，webseit
总结：
了解了相关概念，与群友sepolia相互交互
思考自身情况，有golang基础，决定暂时学习solidily
学习solidily开发，利用wtf学习，学习时间计划两天 
计划开发Dapp相关合约，构思创意，参加黑客松


# 2025.08.03


<!-- Content_END -->
