---
timezone: UTC+12
---

# Bruce

**GitHub ID:** brucexu-eth

**Telegram:** @brucexu_eth

## Self-introduction

E 卫兵

## Notes

<!-- Content_START -->
# 2025-08-12

## Forge debugger

写完测试用例，可以用这个进入 debug 模式：`forge test --debug test/VulnerableContract.t.sol --match-test testReentrancyAttack`

```
常见 EVM 操作码解释：

  栈操作

  - PUSH1 / PUSH2 / PUSH32: 将 1/2/32 字节数据压入栈
  - POP: 从栈顶弹出一个元素
  - DUP1 - DUP16: 复制栈上第 N 个元素到栈顶
  - SWAP1 - SWAP16: 交换栈顶和第 N+1 个元素

  内存操作

  - MSTORE: 存储 32 字节到内存
  - MLOAD: 从内存加载 32 字节
  - MSTORE8: 存储 1 字节到内存

  存储操作

  - SSTORE: 写入持久化存储（最贵，消耗 20000 gas）
  - SLOAD: 从存储读取（消耗 2100 gas）

  算术运算

  - ADD / SUB / MUL / DIV: 加减乘除
  - GT / LT / EQ: 大于/小于/等于比较
  - AND / OR / XOR: 位运算

  控制流

  - JUMP / JUMPI: 无条件/有条件跳转
  - JUMPDEST: 跳转目标位置
  - REVERT: 回滚并返回错误
  - RETURN: 正常返回

  调用相关

  - CALL: 调用其他合约
  - DELEGATECALL: 委托调用（使用当前合约存储）
  - STATICCALL: 静态调用（只读）
  - CALLVALUE: 获取 msg.value
  - CALLER: 获取 msg.sender
  - CALLDATALOAD: 加载调用数据

  其他重要操作码

  - BALANCE: 获取账户余额
  - ADDRESS: 获取当前合约地址
  - GAS: 获取剩余 gas
  - ISZERO: 检查是否为零
  - CODECOPY: 复制代码到内存
```

整个调试器界面：

1. 顶部信息栏

  - Address: 0x7FA9385b... - 当前执行代码的合约地址
  - PC: 6 - Program Counter（程序计数器），当前执行到第 6 条指令
  - Gas used: 20 - 到目前为止消耗的 gas
  - Code section: 0 - 代码段编号

  2. 操作码执行流程

```
  0000 | PUSH1(0x80)    # 将 0x80 (128) 压入栈
  0002 | PUSH1(0x40)    # 将 0x40 (64) 压入栈
  0004 | MSTORE         # 将栈顶值存到内存地址 0x40
  0005 | CALLVALUE      # 获取 msg.value（发送的 ETH 数量）
  ▶0006| DUP1           # 复制栈顶元素（当前指针位置）
  0007 | ISZERO         # 检查栈顶是否为 0
  0008 | PUSH3(0x000010) # 压入跳转地址
```

  这是 Solidity 合约的标准初始化代码：
  - 设置内存指针（free memory pointer）到 0x80
  - 检查是否有 ETH 发送（用于 payable 检查）

  3. Stack（栈）
```
  Stack: 1
  00| 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00...
```

  - 栈中有 1 个元素（全是 0，表示 msg.value = 0）
  - EVM 栈是 LIFO（后进先出）结构
  - 每个栈元素是 32 字节（256 位）

  4. Memory（内存）

```
  Memory (max expansion: 96 bytes)
  00| 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00...
  20| 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00...
  40| 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 80
```

  - 内存按 32 字节为单位显示
  - 位置 0x40 存储了 0x80（free memory pointer）
  - 内存是临时的，函数调用结束后清空

设置断点：

```
function testBreakpoint() public {
    vm.breakpoint("a");
}
```

# 2025-08-11

学习 foundry 相关的工具，初始化自己的合约。

# 2025-08-10

今天主要看了一下 solidity 相关的知识

# 2025-08-09

今天主要是在 LXDAO 周会上给大家介绍了一下 LXDAO 核心路线图，然后来了不少实习生，愿意加入 LXDAO 或者参与一些工作。

# 2025-08-08

今天主要是扫一下 https://web3intern.xyz/zh/smart-contract-development/#_3-%E5%B0%8F%E7%BB%93 然后帮忙邀请了嘉宾，把一些具体的分享敲定了。

# 2025-08-07

今天主要是完善了一些信息，跟进思考了第二周的分享和课程安排。

# 2025-08-04

软件都装好了，早都装好了，打卡。
基础也看完了，我写了一部分。


# 2025.07.29


<!-- Content_END -->
