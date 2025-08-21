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
# 2025-08-22

| 现象 | 原因 | 解决思路 |
| --- | --- | --- |
| 新建 worktree 后看不到 .env的撒发的撒开的撒减肥打卡撒 | worktree 只会把 已追踪文件 checkout 出来，.env 被忽略 | 手动或自动把本地机密文件同步过去 |

的撒发的就卡

-   都是假的撒理发店就撒
    

| 的撒发的撒额外 | 的撒 | 的撒发的撒 |
| --- | --- | --- |
| 额哇 | 啊微风 | 撒发文 |
| 的撒 | 的撒 | 的撒d |
|   | d sa |   |

![05c5335ea6a53fe3e27b27c51ad50766.png](https://raw.githubusercontent.com/IntensiveCoLearning/Web3_Internship_Program/main/assets/brucexu-eth/images/1755818632934-05c5335ea6a53fe3e27b27c51ad50766.png)

# 2025-08-21

今天主要的内容是分享 Web3 英语相关的主题，作为嘉宾参与 Space

## 2 ⃣　在 **Node.js / React / Vite / Next.js** 项目里如何“引入” `.env`？

> 如果你指的是“在代码里使用环境变量”，大致有三种场景——
> 
> djskaj `ds ksd` dsklajk
> 
> dsklajkl

## kdsjak dkslajkl

-   jdskla
    
-   lksadj
    

1.  dsklajkl
    
2.  kldsaj
    
3.  `kdjsakl` dsklajfdksla lklkjdsakl
    

1.  **Node.js（后端脚本、CLI）**
    
    ```
    npm i dotenv --save
    ```
    
    ```
    // index.js
    import 'dotenv/config';       // 或 require('dotenv').config()
    console.log(process.env.DB_URL);
    ```
    
2.  **Vite / React / Vue**
    
    -   文件命名：`.env`, `.env.local`, `.env.development` …
        
    -   变量名前要加 `VITE_` 前缀才能在浏览器端可见：

# 2025-08-20

今天有点累，水一下打卡，未来再说

# 2025-08-18

# Solidity Opcode

## MSTORE

MSTORE 是内存存储操作码，用于将 32 字节数据写入内存。

  MSTORE 功能

  - 操作码: 0x52
  - Gas消耗: 3 + 内存扩展成本
  - 栈输入: [offset, value]
  - 栈输出: 无
  - 功能: 将 32 字节的 value 存储到内存 offset 位置

MSTORE 详细解释

  1. 核心特性

  - 总是写入 32 字节：即使 value 小于 32 字节，也会用 0 填充左侧
  - 可以覆盖：新值会完全覆盖指定位置的 32 字节
  - 内存扩展：访问新内存区域会触发内存扩展，增加 gas 成本

  2. 内存布局

  0x00-0x3F: Scratch space (临时空间)
  0x40-0x5F: Free memory pointer (自由内存指针)
  0x60-0x7F: Zero slot (零槽)
  0x80+: 可用内存

  3. Gas 成本计算

  - 基础成本：3 gas
  - 内存扩展：memory_cost = (memory_size^2) / 512 + (3 * memory_size)
  - 首次访问高地址会很贵

  4. 常见用途

  - 返回数据：配合 RETURN 使用
  - 构建数组/字符串：动态数据结构
  - 函数参数传递：ABI 编码
  - 临时计算：使用 scratch space

  5. 与 MSTORE8 的区别

  - MSTORE: 存储 32 字节
  - MSTORE8: 只存储 1 字节（最右边的字节）

  6. 注意事项

  - 跨 32 字节边界存储会覆盖两个槽
  - 内存不会自动清零，可能包含脏数据
  - 总是要更新自由内存指针（0x40）

Example:

```
mstore(0x40, 0x4444)
```

->

```
PUSH2(0x4444)
PUSH1(0x40)
MSTORE

memory:
040| 00...00 44 44
```

## EVM 内存布局详解

  0x00-0x3F (0-63字节): Scratch Space 临时空间

  - 用途: 编译器的临时工作区
  - 特点: 可以被随时覆盖，不保证持久
  - 常见操作:
    - keccak256 哈希计算的输入准备
    - 函数返回值的临时存放
    - ABI 编码的中间步骤

  0x40-0x5F (64-95字节): Free Memory Pointer 自由内存指针

  - 用途: 追踪下一个可用内存位置
  - 初始值: 0x80（跳过所有保留区域）
  - 重要性: 防止内存覆盖，确保内存分配安全
  - 使用方式:
  let ptr := mload(0x40)  // 获取当前可用位置
  // ... 使用内存 ...
  mstore(0x40, add(ptr, size))  // 更新指针

  0x60-0x7F (96-127字节): Zero Slot 零槽

  - 用途: 提供一个永远为 0 的位置
  - 优化: 避免使用 PUSH1 0x00 指令，节省 gas
  - 保证: EVM 确保这个位置始终为 0

  0x80+ (128字节起): 可用内存

  - 用途: 实际数据存储区域
  - 分配: 通过自由内存指针动态分配
  - 内容: 数组、字符串、结构体、返回数据等

  内存管理原则

  1. 永远不要硬编码内存地址（0x80 以下）
  2. 总是使用自由内存指针分配新内存
  3. 分配后更新指针防止覆盖
  4. 内存只会增长，不会缩小或释放

## 创建变量和 MLOAD 的流程

```
uint256 val0; uint256 val32; uint256 val64; uint256 val96; uint256 val128;
        assembly {
            val0 := mload(0x00)   // Read from byte 0
            val32 := mload(0x20)  // Read from byte 32
            val64 := mload(0x40)  // Read from byte 64
            val96 := mload(0x60)  // Read from byte 96
            val128 := mload(0x80) // Read from byte 128
        }
```

uint256 val0 -> PUSH0 to Stack 
uint256 val32 -> DUP1 ??TODO 为什么不使用 PUSH0？

val64 := mload(0x40) -> PUSH1(0x40) set address -> MLOAD get value -> SWAP3 set to the stack position -> POP remove the tempory value

## 几个之前的问题

### opcode 和源代码的关系

源代码实际上会翻译成 opcode 进行实际的执行，所以多个 opcode 对应一行具体的代码。通过 source map 进行映射

### EVM memory 相关知识

- 256-bit（32 byte）对齐的线性字节数组，按字节寻址（MLOAD/MSTORE 以 32 byte 为基本单位）。
- 生命周期：仅在 当前执行上下文 存在（一次外部调用 / CREATE / DELEGATECALL 结束就被丢弃）。
- 费用：第一次把某一字节范围扩展到比以往更高的地址时才付 gas（线性 + 微量二次项）。
- 与 stack、storage、calldata 完全独立：stack 最快最小、storage 永久最贵、calldata 只读且外部传入。

#### 0x00 – 0x3F：Scratch Space（64 byte 临时工作区）

Solidity 编译器在做 Keccak-256、ABI 编码、拼装返回值时，喜欢把原材料先丢进这 64 byte，然后立即计算哈希或 RETURN.

是两排 32 bytes 的内存数据空间。

TODO 如果临时工作区需要缓存的数据超过 64 bytes 怎么办？

#### 0x40 – 0x5F：Free Memory Pointer（32 byte）

任何向内存写入动态数据的 Solidity 代码，第一步都是：
let ptr := mload(0x40) 取旧指针 → 写数据 → 计算新尾址（对齐 32 byte）→ mstore(0x40, newPtr) 更新。默认 0x80，真正自由空间从 0x80 开始。

所以在所有 smart contract 的开头，是固定的：

```
PUSH1(0x80)
PUSH1(0x40)
MSTORE
```

这样将 0x80 写入到 0x40 的地址，方便之后进行增加。

TODO 编写一个写入动态数据的 example 进行测试和查看效果。

#### 0x60 – 0x7F：Zero Slot（32 byte 常量 0）

这 32 byte 在函数入口被置零一次，此后永远保持 0。

省 gas：随时需要 32 byte 的全 0 数据块（空动态数组长度、布尔 false、占位 padding 等）时，直接 mload(0x60) 拿现成的；不用再写入 32 byte 的 0。

同时确保 0x60–0x7F 也不会被误当作可写空间。

#### 0x80 以后：用户数据区

所有动态大小的数据（bytes, string, bytes[], struct、abi.encode* 结果、函数返回值等）都从这里向高地址连续生长。

```
assembly {
    // 申请 96 byte (=0x60) 空间示例
    let ptr := mload(0x40)       // 取当前 free mem ptr
    // ...在 [ptr .. ptr+0x5F] 写入内容...
    mstore(0x40, add(ptr, 0x60)) // 对齐后写回
}
```

TODO 研究几个常见的操作的 opcode 和 gas 变更：`keccak256(bytes memory)`、`abi.encode(...)`.

## 超过 64 byte 时，编译器（或你写的 Yul/assembly）会怎么做？

| 场景                                   | 处理策略                                                                                                                                                      | 关键点                                                |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| **1. 需要哈希 / 编码的数据 ≤ 64 byte**        | 直接写进 **0x00-0x3F** → `keccak256(0x00, len)`                                                                                                               | 无需扩展 memory，最省 gas                                 |
| **2. 数据 > 64 byte，但已存在于 memory 的别处** | 根本 **不拷贝** 到 Scratch Space；<br>直接 `keccak256(ptr, len)`                                                                                                   | 例如 `keccak256(someArray.offset, someArray.length)` |
| **3. 数据 > 64 byte，且要临时拼装后再哈希 / 返回**  | - 先用 **Free Memory Pointer (mload 0x40)** 申请一段从 **0x80** 开始的空闲区<br>- 把各字段按 ABI 规则拼到那里<br>- 更新 0x40 → `keccak256(ptr, totalLen)` 或 `return(ptr, totalLen)` | 这才会真正「扩展内存」并付扩展 gas                                |

Scratch Space 并不是“唯一的临时区”，只是 “够用就用、不够就去 0x80+” 的第一选择。0x00-0x3F 只是一个“高速缓存线”：小于 64 byte 时最省事；一旦超过，编译器（或你）就会改用从 0x80 起 的常规 free-memory 区域。关键是理解 Free Memory Pointer (0x40) 的分配协议：所有动态缓冲区都从这里申请、向高地址增长。因此，“临时工作区超过 64 byte” 并不会出错——它只意味着会触发一次正常的内存分配并多付一点扩展 gas。

# 2025-08-15

这个 forge debug 无法找到 source map 这个问题还是比较严重的，只能在 test case 里面 debug 有什么用呢？

# 2025-08-14

SHR = Shift Right（右移）

  功能：
  - 弹出两个值：[shift, value]
  - 将 value 右移 shift 位
  - 相当于除以 2^shift

  例子：
  [4, 0x1234] → SHR → [0x123]   // 右移4位
  [8, 0xFF00] → SHR → [0xFF]    // 右移8位（1字节）

  ---
  典型用法：提取函数选择器

  CALLDATALOAD     // 加载32字节: 0xa9059cbb00000000...（函数选择器+垃圾数据）
  PUSH1 0xE0       // 224 (= 256 - 32)
  SHR              // 右移224位，只留前4字节: 0xa9059cbb

  为什么右移 224 位？
  - CALLDATALOAD 加载 32 字节（256 bits）
  - 函数选择器只要前 4 字节（32 bits）
  - 需要去掉后 28 字节（224 bits）
  - 所以右移 224 位

  可视化：
  原始: 0xa9059cbb[后面还有28字节的0或垃圾数据]
  SHR:  0x00000000...00000000a9059cbb（右移后）
  结果: 0xa9059cbb（只要这4字节）

  这是从 calldata 提取函数选择器的标准方法。

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
