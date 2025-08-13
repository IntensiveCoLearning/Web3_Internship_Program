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
# 2025-08-13

典型操作：

```
CALLVALUE -> msg.value 获取发送的 ETH
DUP1 -> duplicate copy 第一个，因为后面 ISZERO 会把值干掉
0007 | ISZERO -> pop 第一个 stack value，如果是 0 压入 1，如果非 0 压入 0
0008 | PUSH2(0x000f) -> push stack 00000...0000f
000b | JUMPI -> Jump If -> pop x2 -> [destination, condition] -> condition = 0 to next -> condition <> 0 to destination
000f | JUMPDEST -> 类似 tag，JUMP/JUMPI 只能跳转到这里然后继续
```

前面的 000x 是 PC，PC 不是连续的，PC = Program Counter = 当前执行位置的字节偏移。

操作码字节数计算：

  基本规则：

  - 普通操作码：1 字节
  - PUSH 系列：1 字节（操作码）+ N 字节（数据）

  具体例子：

  - ADD, DUP1, JUMPI：1 字节
  - PUSH1 0x80：2 字节（1 + 1）
  - PUSH2 0x000f：3 字节（1 + 2）
  - PUSH32 [32字节数据]：33 字节（1 + 32）

0x000f = 15（十进制）虽然 15 这个值很小，用 1 字节就能表示（最大 255），但 PUSH2 固定推送 2 字节：

  PUSH1: 总是推送 1 字节（0x00 - 0xFF）
  PUSH2: 总是推送 2 字节（0x0000 - 0xFFFF）
  PUSH3: 总是推送 3 字节（0x000000 - 0xFFFFFF）

存储格式：
```
  PUSH2 0x000f 在内存中：
  [0x61] [0x00] [0x0f]
    ↑      ↑      ↑
  操作码  高字节  低字节
```
  - 0x61 = PUSH2 的操作码
  - 0x00 = 高位字节
  - 0x0f = 低位字节（15）

  为什么不用 PUSH1？

  如果值小于 256，编译器通常会优化用 PUSH1 0x0f（2字节）而不是 PUSH2 0x000f（3字节）。

  但有时需要 PUSH2 是因为：
  1. 跳转地址可能超过 255
  2. 编译时不确定最终地址大小
  3. 保持指令格式一致性

  总结：PUSH2 的"2"指推送 2 字节数据，所以总占用 3 字节（1 操作码 + 2 数据）

所以可以看到 PC 有一些跳跃：

```
  0007: ISZERO            1       → 0008
  0008: PUSH2 0x0010      3       → 000b  (8+3=b)
  000b: JUMPI             1       → 000c
```

CALLDATASIZE = 获取调用数据的大小（字节数）

  功能：
  - 压入 calldata 的总字节数到栈顶
  - 不消耗栈上任何值

32 字节 = 64 个十六进制字符 = 32 个 00 这样的长度 = 256-bit

  因为：
  - 1 字节 = 8 bits = 2 个十六进制字符
  - 32 字节 = 64 个十六进制字符

  例子：

  address (20字节) = 40个十六进制字符
  0x5B38Da6a701c568545dCfcB03FcB875f56beddC4

  uint256 (32字节) = 64个十六进制字符
  0x0000000000000000000000000000000000000000000000000000000000000123

  ---
  Calldata 计算示例：

  调用 transfer(address to, uint256 amount)：
  0xa9059cbb                                                          // 4字节 函数选择器
  0000000000000000000000005B38Da6a701c568545dCfcB03FcB875f56beddC4  // 32字节 address参数
  0000000000000000000000000000000000000000000000000000000000000064  // 32字节 uint256参数

  总计：4 + 32 + 32 = 68字节

  CALLDATASIZE 会返回 68

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

## 合约执行的 opcode、stack、memory 等变化和解释

### 基本概念

- Opcode（指令）：EVM 的最小执行单元，只会对栈进行入栈/出栈操作；少数指令把栈顶的值当作地址或长度去读写内存、存储或触发 call。
- Stack（栈）：LIFO，最大深度 1024，元素都是 256-bit word。几乎所有 opcode 都只和栈对话：
例：ADD 从栈顶弹出 2 个数→相加→把结果压回栈。
- Memory（内存）：按字节寻址的易失性缓冲区（每个 调用帧独立，初始为零）。通过 MLOAD/MSTORE/MSTORE8/xxxCOPY 访问；按 32B word 扩容计费（越大越贵）。
- Call（外部调用/新调用帧）：CALL/DELEGATECALL/STATICCALL 会创建新的调用上下文：
  - 新的 stack（空栈）与 memory（清零）
  - storage 是否共享取决于调用类型（见下）
  - 参数与返回数据通过 caller 的 memory ↔ callee 的 calldata/returndata 传递

小结：opcode 操作 stack；通过特定 opcode 才能读写 memory 或发起 call；call 会开启新的“世界”，拥有自己的 stack/memory。

### 比较常见的操作举例

```
PUSH1(0x80)
PUSH1(0x40)
```

生成 stack:

```
0x40
0x80
```

之后调用 `MSTORE` 相当于将 0x80 (value) 存入 0x40 的 memory 位置，选择之后，两个 stack 变成蓝色，表示这两条正在被当前 opcode 读取。之后 stack 清空，memory 变成：

```
00| 00
20| 00
40| 0x80
```

max expansion: 96 bytes 表示目前有 96 bytes 的内存总是用量，这样就对应了 stack 的每个 item 是 256-bit word，256 bit = 32 bytes 长度。所以这是 evm

插入到了 0x40 的空间，会自动将前面的设置为 0 并且分配内存。然后 0x40 = 64，表示指针插入到第 64 bytes 的位置。所以目前 0x40 这里存储了一个指针，然后值是 0x80（128 bytes）。TODO 告诉 EVM "下次分配内存从第 128 字节开始"？需要验证

到 CALLVALUE 之后，memory 的 0x40 行显示绿色，意思是这一条是之前的 opcode 写入的，方便你知道 memory 变更。

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
