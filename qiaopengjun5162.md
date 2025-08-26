---
timezone: UTC+8
---

# Paxon

**GitHub ID:** qiaopengjun5162

**Telegram:** @Qiao4812

## Self-introduction

web3 开发者，Python、Go、Rust、Solidity 等语言经验丰富，项目开发部署实操经验丰富

## Notes

<!-- Content_START -->

# 2025-08-26
<!-- DAILY_CHECKIN_2025-08-26_START -->
\# Move 语言核心：布尔逻辑与地址类型的实战精解

在掌握了 Aptos Move 项目的基本搭建流程后，下一步便是深入理解语言的核心——数据类型。在所有类型中，布尔（\`bool\`）和地址（\`address\`）无疑是构建智能合约逻辑的基石：前者构成了所有条件判断、权限检查的骨架，而后者则是区块链世界中识别用户与合约的唯一身份。

本文将通过一个完整且可测试的 \`Sample2\` 模块，带你深入实战。我们不仅会学习 \`bool\` 和 \`address\` 的基本用法，还将探索如何编写返回多个值的函数、如何使用常量，以及 Move 语言中至关重要的部分——如何通过单元测试（\`#\[test\]\`）来确保每一行逻辑都如你所愿地精确运行。

本文通过一个 Move 实例，深入讲解布尔（bool）与地址（address）核心类型。内容涵盖条件逻辑、多返回值处理（元组），并展示如何通过编写单元测试来验证函数行为，是初学者掌握 Move 编程基础的绝佳实战。

\## 实操

\`\`\`rust  
module net2dev\_addr::Sample2 {  
const MY\_ADDR: address = @net2dev\_addr;

fun confirm\_value(number: u64): bool {  
number > 0  
}

fun confirm\_value2(choice: bool): u64 {  
if (choice) { 1 }  
else { 0 }  
}

fun confirm\_value3(choice: bool): (u64, bool) {  
if (choice) {  
(1, choice)  
} else {  
(0, choice)  
}  
}

#\[test\_only\]  
use std::debug::print;

#\[test\]  
fun test\_function() {  
let std\_addr: address = @std;  
print(&std\_addr);  
let my\_addr: address = MY\_ADDR;  
print(&my\_addr);  
}

#\[test\]  
fun test\_confirm\_value() {  
print(&confirm\_value(1));  
print(&confirm\_value(0));

assert!(confirm\_value(1), 1);  
assert!(!confirm\_value(0), 2);  
}

#\[test\]  
fun test\_confirm\_value2() {  
print(&confirm\_value2(true));  
print(&confirm\_value2(false));

assert!(confirm\_value2(true) == 1, 1);  
assert!(confirm\_value2(false) == 0, 2);  
}

#\[test\]  
fun test\_confirm\_value3() {  
let (a, b) = confirm\_value3(true);  
print(&a);  
print(&b);  
let (c, d) = confirm\_value3(false);  
print(&c);  
print(&d);

assert!(a == 1, 1);  
assert!(b, 2);  
assert!(c == 0, 3);  
assert!(!d, 4);  
}  
}

\`\`\`

这段 Move 代码定义了一个名为 \`Sample2\` 的简单模块，其主要目的是用于功能演示和单元测试。模块内包含了几个内部辅助函数，用于执行基础的条件逻辑：\`confirm\_value\` 函数检查一个数字是否为正数，\`confirm\_value2\` 函数将一个布尔值转换为 \`u64\` 类型的整数（1 或 0），而 \`confirm\_value3\` 函数则根据输入的布尔值返回一个包含多个值的元组（tuple）。该模块的全部功能都通过一系列全面的单元测试（\`#\[test\]\`）来进行验证，这些测试不仅确保了辅助函数的逻辑正确性，同时也展示了 Move 语言的一些基本概念，例如如何使用地址常量以及如何对元组返回值进行解构赋值。

\### 测试

\`\`\`bash  
➜ aptos move test  
INCLUDING DEPENDENCY AptosFramework  
INCLUDING DEPENDENCY AptosStdlib  
INCLUDING DEPENDENCY MoveStdlib  
BUILDING my-dapp  
Running Move unit tests  
\[debug\] true  
\[debug\] false  
\[ PASS \] 0x48daaa7d16f1a59929ece03157b8f3e4625bc0a201988ac6fea9b38f50db5ef3::Sample2::test\_confirm\_value  
\[debug\] 1  
\[debug\] 0  
\[ PASS \] 0x48daaa7d16f1a59929ece03157b8f3e4625bc0a201988ac6fea9b38f50db5ef3::Sample2::test\_confirm\_value2  
\[debug\] 1  
\[debug\] true  
\[debug\] 0  
\[debug\] false  
\[ PASS \] 0x48daaa7d16f1a59929ece03157b8f3e4625bc0a201988ac6fea9b38f50db5ef3::Sample2::test\_confirm\_value3  
\[debug\] @0x1  
\[debug\] @0x48daaa7d16f1a59929ece03157b8f3e4625bc0a201988ac6fea9b38f50db5ef3  
\[ PASS \] 0x48daaa7d16f1a59929ece03157b8f3e4625bc0a201988ac6fea9b38f50db5ef3::Sample2::test\_function  
Test result: OK. Total tests: 4; passed: 4; failed: 0  
{  
“Result”: “Success”  
}  
\`\`\`

这个测试结果表明，\`my-dapp\` 项目已成功通过了其所有的单元测试。整个流程首先自动完成了项目的编译，然后执行了 \`Sample2\` 模块中定义的全部四个测试函数。每一条带有 \`\[ PASS \]\` 标记的记录都确认了一个测试用例的成功通过，意味着其内部所有的 \`assert!\` 断言都得到了满足。日志中穿插的 \`\[debug\]\` 行是在测试执行过程中由代码里的 \`print\` 函数输出的调试信息，它们清晰地展示了每个测试函数内部验证的实际值（如 \`true\`, \`false\`, \`1\`, \`0\` 以及账户地址）。最后，\`Test result: OK. Total tests: 4; passed: 4; failed: 0\` 的总结明确指出，所有四个测试全部通过，无一失败，证明了合约当前的功能逻辑符合预期。

\## 总结

恭喜你，通过本篇文章的实战演练，你不仅掌握了 \`bool\` 和 \`address\` 两种 Move 核心数据类型的用法，更重要的是，你亲身体验了 Move 语言以安全为本的设计哲学。

我们学习了如何构建返回条件结果、转换类型乃至返回多个值（元组）的函数。但最有价值的收获，是理解了通过编写单元测试（\`#\[test\]\`）来验证代码行为的重要性。日志中清晰的 \`\[ PASS \]\` 记录，就是我们代码正确性的最佳证明。这种“测试驱动”的思维，是编写可靠智能合约的关键。

现在，你已经打下了坚实的基础，下一步就可以将这些基础类型组合起来，构建更复杂的数据结构（\`struct\`），从而开启创造真正应用的大门。

\## 参考

\- [https://github.com/aptos-labs](https://github.com/aptos-labs)  
\- [https://aptos.dev/zh/network/faucet](https://aptos.dev/zh/network/faucet)  
\- [https://aptos.dev/zh/build/get-started](https://aptos.dev/zh/build/get-started)
<!-- DAILY_CHECKIN_2025-08-26_END -->


# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
# **Aptos Move**

## **实操**

### **安装 Aptos CLI**

```
# 方式一
brew update
brew install aptos
​
# 方式二
curl -fsSL "https://aptos.dev/scripts/install_cli.sh" | sh
```

更多详情请参考：[**https://aptos.dev/zh/build/cli/install-cli/install-cli-mac**](https://aptos.dev/zh/build/cli/install-cli/install-cli-mac)

### **验证安装**

```
aptos --version
aptos 7.7.0
```

### **创建并切换到项目目录**

```
mcd move-tut # mkdir move-tut && cd move-tut
/Users/qiaopengjun/Code/Aptos/move-tut
```

### **初始化项目**

```
aptos move init --name my-dapp
{
  "Result": "Success"
}
```

### **查看项目目录**

```
➜ tree . -L 6 -I "docs|target|node_modules"
.
├── Move.toml
├── scripts
├── sources
└── tests
​
4 directories, 1 file
```

### **初始化账户信息**

```
aptos init --network devnet
Configuring for profile default
Configuring for network Devnet
Enter your private key as a hex literal (0x...) [Current: None | No input: Generate new key (or keep one if present)]
​
No key given, generating key...
Account 0x48daaa7d16f1a59929ece03157b8f3e4625bc0a201988ac6fea9b38f50db5ef3 is not funded, funding it with 100000000 Octas
Account 0x48daaa7d16f1a59929ece03157b8f3e4625bc0a201988ac6fea9b38f50db5ef3 funded successfully
​
---
Aptos CLI is now set up for account 0x48daaa7d16f1a59929ece03157b8f3e4625bc0a201988ac6fea9b38f50db5ef3 as profile default!
---
​
{
  "Result": "Success"
}
```

`aptos init` 命令用于初始化与 Aptos 交互所需的本地开发环境。它的核心作用是**初始化账户信息**，这包括在本地创建一对新的密钥（公钥和私钥）以及一个指向特定网络（如 Devnet）的配置文件。在开发网或测试网上，该命令还会自动调用水龙头（Faucet），为这个新生成的地址充值，从而完成在区块链上的账户创建与激活。

## **参考**

-   [**https://aptos.dev/zh/build/smart-contracts/book**](https://aptos.dev/zh/build/smart-contracts/book)
    
-   [**https://aptos.dev/zh/build/cli**](https://aptos.dev/zh/build/cli)
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
## Solidity Gas 优化：智能合约开发核心指南

在以太坊等区块链平台上，Gas 是驱动每一次交易和计算的燃料。对于 Solidity 开发者而言，优化 Gas 消耗不仅是为了提升效率，更是构建经济、可行的去中心化应用（dApp）的关键。高昂的 Gas 费用会阻碍用户使用，甚至可能使一个优秀的合约在商业上变得不可行。本指南将全面介绍 Solidity 开发中的 Gas 优化技巧，从基本原则到高级策略。

### 1\. 理解 Gas 及其成本

以太坊虚拟机（EVM）中的每一个操作都有一个明确的 Gas 成本。操作越复杂，计算量越大，其 Gas 成本就越高。最终用户需要支付的交易费用是 **所消耗的 Gas 总量** 与 **Gas 价格（Gas Price）** 的乘积。因此，减少合约执行所需的计算和存储，是直接为用户省钱的最有效方式。

### 2\. 善用 Solidity 编译器

在深入研究代码层面的手动优化之前，首先要充分利用开发工具本身的功能。

-   **启用优化器（Optimizer）:** Solidity 编译器内置了一个强大的优化器，它可以在生成字节码的阶段对代码进行优化，从而显著减少 Gas 消耗。在部署生产环境的合约时，请务必启用优化器，并合理设置 `runs` 参数。`runs` 参数代表你预期该合约代码被调用的次数，设置一个较高的值会针对频繁调用的场景进行深度优化，但可能会略微增加合约的部署成本。
    

### 3\. 数据存储与管理：最大的 Gas 消耗来源

存储（Storage）操作是 EVM 中最昂贵的操作之一。因此，如何最小化链上存储以及如何高效管理数据，是 Gas 优化的重中之重。

-   **最小化链上数据:** 只在链上存储合约逻辑绝对必要的数据。对于大型数据（如图片、文档），应考虑使用 IPFS 等链下存储方案，仅将数据哈希值等索引信息存储在链上。
    
-   **变量打包（Variable Packing）:** Solidity 会将多个小尺寸的变量“打包”进同一个 256 位的存储槽（Storage Slot）中以节省空间。例如，将两个 `uint128` 类型的变量相邻声明，它们很可能会共享同一个存储槽，从而节省了一次存储写入的开销。在定义合约状态变量和结构体（Struct）时，请注意变量的声明顺序，将小尺寸的变量放在一起。
    
-   **优先使用定长字节数组:** 对于短小的字符串或字节数据，如果其长度是已知的且不超过 32 字节，使用 `bytes1` 到 `bytes32` 比使用动态的 `string` 或 `bytes` 类型要节省得多。
    
-   `mapping` **与** `array` **的选择:**
    
    -   `mapping` **更适合查找:** `mapping` 的查找操作非常高效（接近 O(1)），因为它不会存储键（key）本身的数据。
        
    -   `array` **更适合遍历:** 但在合约中遍历一个巨大的数组是极其消耗 Gas 的，应极力避免无边界的循环。如果必须遍历，可以考虑设计一种模式，让用户通过多次交易来分批次处理数组中的数据。
        
-   **避免初始化零值:** 不要将变量显式初始化为其默认值（例如 `uint256 public myVar = 0;`）。EVM 会自动将所有未赋值的变量初始化为零，显式初始化只会浪费不必要的 Gas。
    

### 4\. 高效的函数设计与执行

函数的结构和调用方式对 Gas 消耗有直接影响。

-   `external` **vs.** `public`**:** 如果一个函数只会被外部账户调用，而不是被合约内部的其他函数调用，应将其声明为 `external`。这比 `public` 更节省 Gas，因为 `external` 函数的参数直接从 `calldata` 读取，无需再复制到内存（memory）中。
    
-   **为外部函数参数使用** `calldata`**:** 对于 `external` 函数，应将动态类型的参数（如 `string`, `bytes`, `struct`）的存储位置声明为 `calldata` 而非 `memory`。这可以避免一次昂贵的数据从 `calldata` 到 `memory` 的复制过程。
    
-   **善用短路规则:** 在 `require` 语句或 `if` 判断中，当使用 `&&` (AND) 和 `||` (OR) 操作符时，将成本最低、最可能提前确定结果的判断条件放在前面。EVM 一旦能够确定整个表达式的结果，就会停止计算后续的条件，从而节省 Gas。
    
-   **避免重复计算:** 在函数内部，如果某个值（尤其是从存储中读取的值）被多次使用，应先将其读取到一个本地内存变量（local variable）中缓存起来，避免多次执行昂贵的 `SLOAD` 操作。
    

### 5\. 高级 Gas 优化技巧

对于追求极致性能的开发者，以下高级技巧能带来更多帮助。

-   **使用** `immutable` **和** `constant`**:**
    
    -   如果一个变量的值在合约部署时确定，并且之后永远不会改变，请将其声明为 `immutable`。
        
    -   如果一个变量的值在编译时就是已知的常量，请使用 `constant`。
        
    -   这两种变量的值会被直接嵌入到合约的字节码中，读取它们时无需进行昂贵的 `SLOAD` 操作。
        
-   **使用自定义错误（Custom Errors）:** 从 Solidity 0.8.4 版本开始，推荐使用自定义错误来替代 `require` 或 `revert` 中的字符串错误信息。自定义错误消耗的 Gas 要少得多。
    
    Solidity
    
    ```
    // 低效方式
    require(msg.sender == owner, "Caller is not the owner");
    
    // 高效方式
    error NotOwner();
    if (msg.sender != owner) {
        revert NotOwner();
    }
    ```
    
-   `unchecked` **数学运算:** Solidity 0.8.0 版本之后，所有的算术运算都会自动进行溢出检查，这会增加少量 Gas 成本。如果你通过逻辑判断能 100% 确定某项运算不会发生上溢或下溢，可以使用 `unchecked` 代码块来包裹该运算，从而节省 Gas。**请务必谨慎使用此功能，否则可能导致严重的安全漏洞。**
    
-   **删除存储变量以获得 Gas 返还:** 删除存储变量（即将其值设为零）可以获得一部分 Gas 返还。虽然这个机制在以太坊伦敦升级后有所削弱，但在某些场景下仍然是值得考虑的优化点。
    

### 6\. 工具与分析

手动进行 Gas 优化可能很困难。利用专业的工具可以帮助你识别代码中的性能瓶颈。

-   **Gas 报告工具:** 像 `hardhat-gas-reporter` 这样的工具可以在你运行测试时，生成一份详细的 Gas 消耗报告，清晰地展示每个函数的 Gas 使用情况。
    
-   **静态分析工具:** Slither 等工具可以静态扫描你的代码，不仅能发现潜在的安全漏洞，也能提示一些 Gas 优化的建议。
    

### 结论：寻求平衡

最后，需要强调的是，Gas 优化不应以牺牲代码的**可读性、安全性和可维护性**为代价。一个过度优化的、晦涩难懂的合约可能隐藏着更多的风险。

最佳实践是：首先从影响最大的方面着手，例如优化存储和利用编译器。然后，使用工具来定位消耗最高的函数，并针对性地进行优化。通过遵循这些原则，你将能够开发出功能强大且对用户经济友好的智能合约。
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
# 三明治攻击 和 套利的 关系
三明治攻击（Sandwich Attack）和套利（Arbitrage）关系紧密，但本质上又有关键区别。

简单来说，**三明治攻击是套利的一种特殊形式，是一种恶意的、针对单个用户的微观套利行为。**

我们可以把它们的关系理解为：

* **套利**是一个广泛的金融概念。
* **MEV (矿工可提取价值)** 是套利在区块链上的一种表现形式。
* **三明治攻击**是 MEV 中一种非常具体且具有攻击性的策略。

下面我们来详细拆解这两者。

---

### 1. 什么是套利 (Arbitrage)？

套利的核心是在**不同市场**之间，利用**同一资产**的**价格差异**来获取利润。这是一个古老且在所有金融市场中都存在的行为。

**经典例子：**
* 在交易所 A，1 个 ETH 的价格是 $3000 USDC。
* 在交易所 B，1 个 ETH 的价格是 $3010 USDC。

套利者会立即在交易所 A 买入 ETH，然后在交易所 B 卖出，从而赚取 $10 的无风险（理论上）差价。

**在 DeFi 领域的表现：**
这种行为在去中心化交易所（DEX）之间非常普遍。例如，一个套利机器人会持续监控 Uniswap 和 Sushiswap 上 ETH/USDC 交易对的价格。一旦出现价差，它就会在价格低的池子里买入，在价格高的池子里卖出，直到两个池子的价格趋于一致。

**关键特点：**
* **利润来源**：市场间的自然价差（market inefficiency）。
* **行为效果**：客观上帮助市场恢复价格平衡，提升了市场效率。因此，普通套利通常被认为是**中性或有益的**。
* **作用范围**：发生在**两个或多个**独立的市场（或流动性池）之间。

---

### 2. 什么是三明治攻击 (Sandwich Attack)？

三明治攻击是一种在**单一市场（同一个流动性池）** 内，通过**操控交易排序**来剥削特定用户的策略。攻击者像三明治一样，用自己的两笔交易把受害者的交易“夹”在中间。

**攻击步骤详解：**

假设一个用户（受害者）准备在 Uniswap 上用大量的 USDC 买入 ETH。

1.  **监控 (Monitoring)**：攻击者的机器人在内存池 (Mempool) 中“扫描”，发现了这笔即将被处理的大额买单。它计算出这笔交易将会对 ETH 的价格产生显著的“价格冲击 (Price Impact)”，使其上涨。

2.  **抢跑 (Front-running) - 第一片面包**：攻击者立刻提交一笔自己的“买入 ETH”交易。为了确保自己的交易能在受害者之前被执行，攻击者会支付更高的 Gas 费，贿赂矿工或验证者优先打包他的交易。这笔交易首先推高了 ETH 的价格。

3.  **受害者交易被执行 - 中间的夹心**：现在轮到受害者的交易被执行了。但由于价格已经被攻击者推高，受害者只能以一个**更差的价格**（更高的价格）买入 ETH，并遭受了严重的**滑点 (Slippage)** 损失。这笔大额买单会进一步推高 ETH 的价格。

4.  **尾随 (Back-running) - 第二片面包**：攻击者监控到受害者的交易完成后，立刻提交一笔“卖出 ETH”的交易。他卖掉刚刚在第一步中低价买入的 ETH，以当前被两次推高的价格卖出，从而获利。

**关键特点：**
* **利润来源**：受害者因滑点造成的**直接损失**。攻击者的利润完全来自于对单个用户的剥削。
* **行为效果**：对用户是**纯粹有害的**，破坏了交易的公平性，使用户体验变得极差。这是一种寄生行为。
* **作用范围**：发生在**一个**流动性池内，针对**一笔**特定的待处理交易。

---

### 关系与核心区别总结

| 特征 | **套利 (Arbitrage)** | **三明治攻击 (Sandwich Attack)** |
| :--- | :--- | :--- |
| **本质** | 利用**空间**上的价差（不同市场） | 利用**时间**上的顺序（交易排序） |
| **利润来源** | 市场间的自然价格不平衡 | **单个用户的滑点损失** |
| **作用市场** | **多个**市场或流动性池 | **单个**流动性池 |
| **受害者** | 没有直接的、单一的受害者 | 有一个**明确的、被针对的**受害者 |
| **市场影响** | **修正价格，提升效率**（良性/中性） | **剥削用户，破坏公平**（恶性/寄生） |
| **比喻** | 一个商人发现A村苹果便宜，B村贵，于是从A村进货到B村卖，使两地价格趋同。 | 一个黄牛看到你正要去买一张热门票，他抢在你前面买下最后一张，然后高价卖回给你。 |

**总结一句话：**

**套利利用的是市场的“不完美”，而三明治攻击是主动制造并利用对某个用户的“不公平”。** 因此，三明治攻击可以被看作是一种在微观层面，通过恶意操纵交易顺序来实现的、扭曲的“套利”行为。
<!-- DAILY_CHECKIN_2025-08-23_END -->

# 2025-08-21

# 解锁Web3未来：Rust与Solidity智能合约实战

Web3正在重塑互联网的未来，而Rust与Solidity的强强联合为开发者提供了打造高效、安全区块链应用的利器。本文通过“rust-chain”项目，带你走进Web3开发的实战前沿。从智能合约的编写到部署Holesky测试网，再到Rust后端与区块链的交互，这份教程将解锁Rust和Solidity的无限可能，助你快速掌握2025年Web3开发的核心技能。无论你是区块链新手还是资深开发者，都能在这里找到灵感与干货！

本文详细展示了基于Rust和Solidity的Web3区块链项目“rust-chain”的开发全流程。项目涵盖Rust项目初始化、Solidity智能合约（Counter）开发与部署、Rust后端API构建，以及与Holesky测试网的交互。文章通过清晰的代码示例和运行结果，演示了如何使用Foundry工具链和ethers-rs库实现合约部署与查询，并通过Axum框架提供高效API服务。读者将掌握Rust与Solidity结合的实战技巧，解锁Web3开发的前沿技能，适合希望快速上手区块链应用开发的从业者。

## 实操

### 创建项目

创建一个新的 Rust 项目，项目名称为 rust-chain

```bash
cargo new rust-chain  

    Creating binary (application) `rust-chain` package
note: see more `Cargo.toml` keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html
```

### 构建与运行Rust项目：rust-chain

```bash
cd rust-chain

ls
Cargo.toml src
cargo run
   Compiling rust-chain v0.1.0 (/Users/qiaopengjun/Code/Rust/rust-chain)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.63s
     Running `target/debug/rust-chain`
Hello, world!
```

### 创建合约项目目录

```bash
mkdir contracts
```

### 切换到合约项目目录并初始化

```bash
cd contracts && forge init
```

### 查看合约项目目录

```bash
rust-chain/contracts on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ tree . -L 6 -I "out|lib|cache|broadcast"
.
├── foundry.toml
├── note.md
├── README.md
├── remappings.txt
├── script
│   └── Counter.s.sol
├── src
│   └── Counter.sol
└── test
    └── Counter.t.sol

4 directories, 7 files

```

### 合约代码 Counter.sol

```ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Counter {
    uint256 public number;

    function setNumber(uint256 newNumber) public {
        number = newNumber;
    }

    function increment() public {
        number++;
    }
}

```

### 部署脚本 Counter.s.sol

```ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import {Script, console2} from "forge-std/Script.sol";
import {Counter} from "../src/Counter.sol";

contract CounterScript is Script {
    Counter public counter;

    function setUp() public {}

    function run() public {
        uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
        vm.startBroadcast(deployerPrivateKey);

        counter = new Counter();

        console2.log("Counter address: ", address(counter));
        counter.setNumber(69420);
        console2.log("Counter number: ", counter.number());
        vm.stopBroadcast();
    }
}

```

### 安装项目所需的依赖库

```bash
rust-chain/contracts on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ forge install                                 
Updating dependencies in /Users/qiaopengjun/Code/Rust/rust-chain/contracts/lib
```

### 项目依赖映射生成并保存到 remappings.txt 文件

```bash
rust-chain/contracts on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ forge remappings > remappings.txt
```

### 编译构建合约

`forge build` 就是 Foundry 用来将你的 Solidity 代码转换成计算机（以太坊虚拟机 EVM）可以理解和执行的格式的命令。

```bash
rust-chain/contracts on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ forge build                      
[⠊] Compiling...
No files changed, compilation skipped
```

### 部署并验证合约

```bash
rust-chain/contracts on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ source .env           

rust-chain/contracts on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ forge script CounterScript --rpc-url $HOLESKY_RPC_URL --broadcast --verify -vvvvv 
[⠊] Compiling...
No files changed, compilation skipped
Traces:
  [121] CounterScript::setUp()
    └─ ← [Stop]

  [167052] CounterScript::run()
    ├─ [0] VM::envUint("PRIVATE_KEY") [staticcall]
    │   └─ ← [Return] <env var value>
    ├─ [0] VM::startBroadcast(<pk>)
    │   └─ ← [Return]
    ├─ [96345] → new Counter@0x5A31b9407095dd9A295139c4F0183c076051632D
    │   └─ ← [Return] 481 bytes of code
    ├─ [0] console::log("Counter address: ", Counter: [0x5A31b9407095dd9A295139c4F0183c076051632D]) [staticcall]
    │   └─ ← [Stop]
    ├─ [22492] Counter::setNumber(69420 [6.942e4])
    │   └─ ← [Stop]
    ├─ [424] Counter::number() [staticcall]
    │   └─ ← [Return] 69420 [6.942e4]
    ├─ [0] console::log("Counter number: ", 69420 [6.942e4]) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::stopBroadcast()
    │   └─ ← [Return]
    └─ ← [Stop]


Script ran successfully.

== Logs ==
  Counter address:  0x5A31b9407095dd9A295139c4F0183c076051632D
  Counter number:  69420

## Setting up 1 EVM.
==========================
Simulated On-chain Traces:

  [96345] → new Counter@0x5A31b9407095dd9A295139c4F0183c076051632D
    └─ ← [Return] 481 bytes of code

  [22492] Counter::setNumber(69420 [6.942e4])
    └─ ← [Stop]


==========================

Chain 17000

Estimated gas price: 0.00349614 gwei

Estimated total gas used for script: 264249

Estimated amount required: 0.00000092385149886 ETH

==========================

##### holesky
✅  [Success] Hash: 0xc29b0a07ed4e464222824cf483c5fcb49d2be8a6d4b7928e69f3e322cf045c5d
Contract Address: 0x5A31b9407095dd9A295139c4F0183c076051632D
Block: 3787922
Paid: 0.000000548248384151 ETH (156817 gas * 0.003496103 gwei)


##### holesky
✅  [Success] Hash: 0x6bff519cb35a59975c8c7984d459e3651f7376fe33c68d15cecffd233e9489a4
Block: 3787922
Paid: 0.00000015284962316 ETH (43720 gas * 0.003496103 gwei)

✅ Sequence #1 on holesky | Total Paid: 0.000000701098007311 ETH (200537 gas * avg 0.003496103 gwei)
                                                                                                                                                                                                              

==========================

ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
##
Start verification for (1) contracts
Start verifying contract `0x5A31b9407095dd9A295139c4F0183c076051632D` deployed on holesky
EVM version: shanghai
Compiler version: 0.8.20

Submitting verification for [src/Counter.sol:Counter] 0x5A31b9407095dd9A295139c4F0183c076051632D.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (4 tries remaining)

Submitting verification for [src/Counter.sol:Counter] 0x5A31b9407095dd9A295139c4F0183c076051632D.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (3 tries remaining)

Submitting verification for [src/Counter.sol:Counter] 0x5A31b9407095dd9A295139c4F0183c076051632D.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (2 tries remaining)

Submitting verification for [src/Counter.sol:Counter] 0x5A31b9407095dd9A295139c4F0183c076051632D.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (1 tries remaining)

Submitting verification for [src/Counter.sol:Counter] 0x5A31b9407095dd9A295139c4F0183c076051632D.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (0 tries remaining)

Submitting verification for [src/Counter.sol:Counter] 0x5A31b9407095dd9A295139c4F0183c076051632D.
Submitted contract for verification:
        Response: `OK`
        GUID: `fgkwdamvcuihvdydrfaxyfwwnmwuwcncpri6ljnjtbwgaxajaw`
        URL: https://holesky.etherscan.io/address/0x5a31b9407095dd9a295139c4f0183c076051632d
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Warning: Verification is still pending...; waiting 15 seconds before trying again (7 tries remaining)
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
All (1) contracts were verified!

Transactions saved to: /Users/qiaopengjun/Code/Rust/rust-chain/contracts/broadcast/Counter.s.sol/17000/run-latest.json

Sensitive values saved to: /Users/qiaopengjun/Code/Rust/rust-chain/contracts/cache/Counter.s.sol/17000/run-latest.json


```

### 浏览器查看合约

<https://holesky.etherscan.io/address/0x5a31b9407095dd9a295139c4f0183c076051632d#code>

### 查看项目目录结构

```bash
rust-chain on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ tree . -L 6 -I "out|lib|cache|broadcast|target"
.
├── Cargo.lock
├── Cargo.toml
├── contracts
│   ├── foundry.toml
│   ├── note.md
│   ├── README.md
│   ├── remappings.txt
│   ├── script
│   │   └── Counter.s.sol
│   ├── src
│   │   └── Counter.sol
│   └── test
│       └── Counter.t.sol
├── src
│   ├── counter.json
│   ├── counter.rs
│   ├── lib.rs
│   ├── main.rs
│   └── routes.rs
└── test.http

6 directories, 15 files
```

### ABI 文件 counter.json

```json
[
  {
    "inputs": [],
    "name": "increment",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [],
    "name": "number",
    "outputs": [{ "internalType": "uint256", "name": "", "type": "uint256" }],
    "stateMutability": "view",
    "type": "function"
  },
  {
    "inputs": [
      { "internalType": "uint256", "name": "newNumber", "type": "uint256" }
    ],
    "name": "setNumber",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  }
]

```

### counter.rs 文件

```rust
use std::sync::Arc;

use ethers::{
    contract::{ContractError, abigen},
    providers::{Http, Provider},
    types::{Address, U256},
};

abigen!(CounterContract, "./src/counter.json");

#[derive(Debug, Clone)]
pub struct Counter {
    pub client: Arc<Provider<Http>>,
    pub contract: CounterContract<Provider<Http>>,
}

impl Counter {
    pub fn new(provider: Arc<Provider<Http>>, address: Address) -> Self {
        let contract = CounterContract::new(address, provider.clone());
        Self {
            client: provider,
            contract,
        }
    }

    pub async fn get_number(&self) -> Result<U256, ContractError<Provider<Http>>> {
        let number = self.contract.number().call().await?;
        Ok(number)
    }
}

```

### routes.rs 文件

```rust
use axum::{
    Extension, Json,
    http::StatusCode,
    response::{IntoResponse, Response},
};
use ethers::{
    contract::ContractError,
    providers::{Http, Middleware, Provider, ProviderError},
};
use thiserror::Error;
use tracing::info;

use crate::counter::Counter;

#[derive(Debug, Error)]
pub enum ApiError {
    #[error(": ContractError {0}")]
    ContractError(#[from] ContractError<Provider<Http>>),
    #[error(": ProviderError {0}")]
    ProviderError(#[from] ProviderError),
}

impl IntoResponse for ApiError {
    fn into_response(self) -> Response {
        let body = match self {
            Self::ContractError(e) => format!("Contract Error: {}", e),
            Self::ProviderError(e) => format!("Provider Error: {}", e),
        };
        (StatusCode::INTERNAL_SERVER_ERROR, body).into_response()
    }
}

pub async fn handle_number(
    Extension(counter): Extension<Counter>,
) -> Result<Json<String>, ApiError> {
    info!("Getting number");

    let number = counter.get_number().await?;
    Ok(Json(number.to_string()))
}

pub async fn handle_block_number(
    Extension(counter): Extension<Counter>,
) -> Result<Json<String>, ApiError> {
    info!("Getting block number");
    let block_number: u64 = counter.client.get_block_number().await?.as_u64();

    Ok(Json(block_number.to_string()))
}

```

### lib.rs 文件

```rust
pub mod counter;
pub mod routes;
```

### Cargo.toml 文件

```toml
[package]
name = "rust-chain"
version = "0.1.0"
edition = "2024"

[dependencies]
anyhow = "1.0.98"
dotenv = "0.15.0"
ethers = { version = "2.0.14", default-features = false, features = ["rustls"] }
tokio = { version = "1.45.0", features = ["full"] }
tracing = "0.1.41"
tracing-subscriber = "0.3.19"
axum = { version = "0.8.4", features = ["json"] }
thiserror = "2.0.12"
eyre = "0.6.12"
axum-macros = "0.5.0"

```

### 构建项目

**`cargo build`**: Rust 的构建命令，默认会：

- 编译当前项目及其依赖。
- 生成 **未优化的调试版本**（适合开发阶段）。

```bash
rust-chain on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 took 1h 21m 13.5s 
➜ cargo build
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.53s

```

### 基本检查（当前包）

`cargo check` 是 Rust 的 **快速静态检查命令**，它只进行语法和类型检查，**不生成可执行文件**，因此速度比 `cargo build` 快很多。

```bash
rust-chain on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ cargo check
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.50s

```

### **编译并运行**项目

`cargo run` 是 Rust 项目中用于 **编译并运行** 可执行程序的命令。它会自动处理代码编译（如果需要）并执行生成的二进制文件。

```bash
rust-chain on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ cargo run  
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.42s
     Running `target/debug/rust-chain`
2025-05-08T11:14:33.351987Z  INFO rust_chain: listening on 127.0.0.1:8080


rust-chain on  master [+?] is 📦 0.1.0 via 🦀 1.86.0 
➜ cargo run  
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.42s
     Running `target/debug/rust-chain`
2025-05-08T11:14:33.351987Z  INFO rust_chain: listening on 127.0.0.1:8080
2025-05-08T12:59:16.022041Z  INFO rust_chain::routes: Getting number
2025-05-08T12:59:27.234464Z  INFO rust_chain::routes: Getting block number

```

### 测试

#### test.http 文件

请求：

```http
### hello world

GET http://127.0.0.1:8080/
Accept: application/json
Content-Type: application/json

### rust chain number

GET http://127.0.0.1:8080/api/number
Accept: application/json
Content-Type: application/json


### handle_block_number

GET http://127.0.0.1:8080/api/block_number
Accept: application/json
Content-Type: application/json

```

响应：

```bash
HTTP/1.1 200 OK
content-type: text/html; charset=utf-8
content-length: 22
connection: close
date: Thu, 08 May 2025 12:57:59 GMT

<h1>Hello, World!</h1>

HTTP/1.1 200 OK
content-type: application/json
content-length: 7
connection: close
date: Thu, 08 May 2025 12:59:16 GMT

"69420"

HTTP/1.1 200 OK
content-type: application/json
content-length: 9
connection: close
date: Thu, 08 May 2025 12:59:27 GMT

"3803337"

```

## 总结

“rust-chain”项目展现了Rust与Solidity在Web3开发中的强大潜力。从项目搭建到Counter智能合约的部署，再到Rust后端与区块链的无缝交互，本文完整呈现了构建Web3应用的全流程。借助Foundry的高效工具链和ethers-rs的灵活性，开发者能够快速开发、部署并验证智能合约，同时通过Axum框架提供可靠的API服务。项目成功在Holesky测试网运行，验证了技术的可行性与前景。这份实战教程为Web3开发者提供了宝贵参考，助你解锁区块链未来的无限可能！

## 参考

- <https://holesky.etherscan.io/address/0x5a31b9407095dd9a295139c4f0183c076051632d#readContract>
- <https://github.com/alloy-rs>
- <https://alloy.rs/>
- <https://alloy.rs/introduction/getting-started/>
- <https://github.com/gakonst/ethers-rs>
- <https://medium.com/@chalex-eth/how-to-build-a-web3-backend-in-rust-part-1-a92b649d42ad>
- <https://github.com/tokio-rs/axum/blob/main/examples/hello-world/src/main.rs>
- <https://github.com/chalex-eth/web3_rust_backend>
- <https://www.gakonst.com/ethers-rs/>
- <https://github.com/tokio-rs/axum/blob/main/examples/handle-head-request/src/main.rs>
- <https://github.com/chalex-eth/awesome-ethers-rs>
- <https://github.com/qiaopengjun5162/rust-chain>
![hua](https://learnblockchain.cn/image/avatar/18602_big.jpg)

# 2025-08-20

# Solidity on Polkadot: Web3 实战开发指南

Polkadot 2.0 为 Web3 开发者打开了一扇新大门：用熟悉的 Solidity 在跨链生态中挥洒创意。本文通过实战带你一步步掌握从项目搭建到合约部署的全流程，打造一个功能完备的 PaxonToken 代币合约。无论你是初学者还是资深开发者，这份指南都将助你快速融入 Polkadot 的 Web3 世界！

本文以 Polkadot 的 Westend Asset Hub 为实验场，基于 RISC-V 的 PVM 运行时，展示如何用 Solidity 开发并部署 PaxonToken——一个标准的 ERC-20 代币合约。从项目初始化、代码编写、全面测试到覆盖率分析，再到通过 Remix 实现部署上线，每一步都详细记录。最终，PaxonToken 在 Asset Hub 成功运行，为开发者提供了一条清晰的 Web3 实战路径，助力 Solidity 技能无缝迁移至 Polkadot 生态。

Asset Hub 是系统平行链。在 Westend 的 Asset Hub 上可以支持 Solidity 编写合约。它是在新的基于 RISC-V 的 PVM 上运行 Solidity 代码。**对于很多 DApp 和智能合约的开发者，这将是进入和了解 Polkadot 2.0 的绝佳路径**。

## 实操

Write a ERC20 contract according to IERC20 from scratch. Don't use library.

```bash
1. fork the project
2. create a folder with your ID like `homework-2/001`
3. complete the homework and create a PR
```

### 第一步：fork 并克隆项目

```bash
git clone git@github.com:qiaopengjun5162/2025-17-solidity-on-polkadot.git
正克隆到 '2025-17-solidity-on-polkadot'...
remote: Enumerating objects: 14, done.
remote: Counting objects: 100% (14/14), done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 14 (delta 0), reused 11 (delta 0), pack-reused 0 (from 0)
接收对象中: 100% (14/14), 完成.
```

### 第二步：创建并初始化项目

在终端运行以下命令创建项目：

```bash
forge init PaxonToken
cd PaxonToken
```

实操如下：

```bash
2025-17-solidity-on-polkadot on  feature/homework on 🐳 v27.5.1 (orbstack) via 🅒 base 
➜ cd homework-2/1490/code        

2025-17-solidity-on-polkadot/homework-2/1490/code on  feature/homework on 🐳 v27.5.1 (orbstack) via 🅒 base 
➜ forge init PaxonToken

Warning: This is a nightly build of Foundry. It is recommended to use the latest stable version. Visit https://book.getfoundry.sh/announcements for more information. 
To mute this warning set `FOUNDRY_DISABLE_NIGHTLY_WARNING` in your environment. 

Initializing /Users/qiaopengjun/Code/polkadot/2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken...
Installing forge-std in /Users/qiaopengjun/Code/polkadot/2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken/lib/forge-std (url: Some("https://github.com/foundry-rs/forge-std"), tag: None)
Cloning into '/Users/qiaopengjun/Code/polkadot/2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken/lib/forge-std'...
remote: Enumerating objects: 2086, done.
remote: Counting objects: 100% (1017/1017), done.
remote: Compressing objects: 100% (133/133), done.
remote: Total 2086 (delta 937), reused 893 (delta 884), pack-reused 1069 (from 1)
Receiving objects: 100% (2086/2086), 653.50 KiB | 997.00 KiB/s, done.
Resolving deltas: 100% (1413/1413), done.
    Installed forge-std v1.9.6
    Initialized forge project

2025-17-solidity-on-polkadot/homework-2/1490/code on  feature/homework [!+?] on 🐳 v27.5.1 (orbstack) via 🅒 base took 2.6s 
➜ export FOUNDRY_DISABLE_NIGHTLY_WARNING=1

2025-17-solidity-on-polkadot/homework-2/1490/code on  feature/homework [!+?] on 🐳 v27.5.1 (orbstack) via 🅒 base 
➜ cd PaxonToken          
```

### 第三步：查看项目目录结构

```bash
➜ tree . -L 6 -I 'target|cache|lib|out'   


.
├── README.md
├── foundry.toml
├── remappings.txt
├── script
│   └── PaxonToken.s.sol
├── src
│   └── PaxonToken.sol
└── test
    └── PaxonToken.t.sol

4 directories, 6 files
```

### 第四步：编写`PaxonToken.sol`文件

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

// IERC20 Interface
interface IERC20 {
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);

    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address to, uint256 amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address from, address to, uint256 amount) external returns (bool);
    function name() external view returns (string memory);
    function symbol() external view returns (string memory);
    function decimals() external view returns (uint8);
}

contract PaxonToken is IERC20 {
    string private _name;
    string private _symbol;
    uint8 private _decimals;
    uint256 private _totalSupply;
    mapping(address => uint256) private _balances;
    mapping(address => mapping(address => uint256)) private _allowances;
    address private _owner;

    constructor(string memory name_, string memory symbol_, uint8 decimals_, uint256 initialSupply_) {
        _name = name_;
        _symbol = symbol_;
        _decimals = decimals_;
        _owner = msg.sender;
        _totalSupply = initialSupply_ * 10 ** uint256(decimals_);
        _balances[msg.sender] = _totalSupply;
        emit Transfer(address(0), msg.sender, _totalSupply);
    }

    modifier onlyOwner() {
        require(msg.sender == _owner, "PaxonToken: caller is not the owner");
        _;
    }

    function name() external view override returns (string memory) {
        return _name;
    }

    function symbol() external view override returns (string memory) {
        return _symbol;
    }

    function decimals() external view override returns (uint8) {
        return _decimals;
    }

    function totalSupply() external view override returns (uint256) {
        return _totalSupply;
    }

    function balanceOf(address account) external view override returns (uint256) {
        return _balances[account];
    }

    function transfer(address to, uint256 amount) external override returns (bool) {
        require(to != address(0), "PaxonToken: transfer to the zero address");
        require(_balances[msg.sender] >= amount, "PaxonToken: transfer amount exceeds balance");

        _balances[msg.sender] -= amount;
        _balances[to] += amount;
        emit Transfer(msg.sender, to, amount);
        return true;
    }

    function approve(address spender, uint256 amount) external override returns (bool) {
        require(spender != address(0), "PaxonToken: approve to the zero address");

        _allowances[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function allowance(address owner, address spender) external view override returns (uint256) {
        return _allowances[owner][spender];
    }

    function transferFrom(address from, address to, uint256 amount) external override returns (bool) {
        require(from != address(0), "PaxonToken: transfer from the zero address");
        require(to != address(0), "PaxonToken: transfer to the zero address");
        require(_balances[from] >= amount, "PaxonToken: transfer amount exceeds balance");
        require(_allowances[from][msg.sender] >= amount, "PaxonToken: transfer amount exceeds allowance");

        _balances[from] -= amount;
        _balances[to] += amount;
        _allowances[from][msg.sender] -= amount;
        emit Transfer(from, to, amount);
        return true;
    }

    function mint(address to, uint256 amount) external onlyOwner returns (bool) {
        require(to != address(0), "PaxonToken: mint to the zero address");
        require(amount > 0, "PaxonToken: mint amount must be greater than zero");

        _totalSupply += amount;
        _balances[to] += amount;
        emit Transfer(address(0), to, amount);
        return true;
    }

    function burn(uint256 amount) external returns (bool) {
        require(amount > 0, "PaxonToken: burn amount must be greater than zero");
        require(_balances[msg.sender] >= amount, "PaxonToken: burn amount exceeds balance");

        _totalSupply -= amount;
        _balances[msg.sender] -= amount;
        emit Transfer(msg.sender, address(0), amount);
        return true;
    }
}

```

### 第五步：编写测试

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import {Test, console} from "forge-std/Test.sol";
import {PaxonToken} from "../src/PaxonToken.sol";

contract PaxonTokenTest is Test {
    PaxonToken token;
    address owner;
    address alice = address(0x1);
    address bob = address(0x2);

    function setUp() public {
        owner = address(this);
        token = new PaxonToken("Paxon Token", "PAX", 18, 1000); // 1000 tokens
    }

    function testInitialSupply() public view {
        assertEq(token.totalSupply(), 1000 * 10 ** 18);
        assertEq(token.balanceOf(address(this)), 1000 * 10 ** 18);
    }

    function testTransfer() public {
        token.transfer(alice, 100 * 10 ** 18);
        assertEq(token.balanceOf(alice), 100 * 10 ** 18);
        assertEq(token.balanceOf(address(this)), 900 * 10 ** 18);
    }

    function testApproveAndTransferFrom() public {
        token.approve(bob, 200 * 10 ** 18);
        assertEq(token.allowance(address(this), bob), 200 * 10 ** 18);

        vm.prank(bob);
        token.transferFrom(address(this), alice, 150 * 10 ** 18);
        assertEq(token.balanceOf(alice), 150 * 10 ** 18);
        assertEq(token.allowance(address(this), bob), 50 * 10 ** 18);
    }

    function testMint() public {
        uint256 initialSupply = token.totalSupply();
        token.mint(alice, 500 * 10 ** 18);
        assertEq(token.totalSupply(), initialSupply + 500 * 10 ** 18);
        assertEq(token.balanceOf(alice), 500 * 10 ** 18);
    }

    function testMintFailNotOwner() public {
        vm.prank(alice);
        vm.expectRevert("PaxonToken: caller is not the owner");
        token.mint(alice, 100 * 10 ** 18);
    }

    function testBurn() public {
        uint256 initialSupply = token.totalSupply();
        token.burn(300 * 10 ** 18);
        assertEq(token.totalSupply(), initialSupply - 300 * 10 ** 18);
        assertEq(token.balanceOf(address(this)), 700 * 10 ** 18);
    }

    function testBurnFailInsufficientBalance() public {
        vm.expectRevert("PaxonToken: burn amount exceeds balance");
        token.burn(2000 * 10 ** 18);
    }

    function testTransferToZeroAddress() public {
        vm.expectRevert("PaxonToken: transfer to the zero address");
        token.transfer(address(0), 100 * 10 ** 18);
    }

    function testMintZeroAmount() public {
        vm.expectRevert("PaxonToken: mint amount must be greater than zero");
        token.mint(alice, 0);
    }

    function testMetadata() public view {
        assertEq(token.name(), "Paxon Token");
        assertEq(token.symbol(), "PAX");
        assertEq(token.decimals(), 18);
    }

    function testAllowance() public {
        token.approve(bob, 200 * 10 ** 18);
        assertEq(token.allowance(address(this), bob), 200 * 10 ** 18);
    }

    function testTransferFromMaxAllowance() public {
        token.approve(bob, 200 * 10 ** 18);
        vm.prank(bob);
        token.transferFrom(address(this), alice, 200 * 10 ** 18);
        assertEq(token.balanceOf(alice), 200 * 10 ** 18);
        assertEq(token.allowance(address(this), bob), 0);
    }

    function testBurnZeroAmount() public {
        vm.expectRevert("PaxonToken: burn amount must be greater than zero");
        token.burn(0);
    }

    function testTransferInsufficientBalance() public {
        token.transfer(alice, 500 * 10 ** 18); // 先转移一些给 alice
        vm.prank(alice);
        vm.expectRevert("PaxonToken: transfer amount exceeds balance");
        token.transfer(bob, 501 * 10 ** 18); // alice 余额不足
    }

    function testApproveExplicit() public {
        token.approve(bob, 100 * 10 ** 18);
        assertEq(token.allowance(address(this), bob), 100 * 10 ** 18);
    }

    function testTransferFromFromZeroAddress() public {
        vm.prank(address(0));
        vm.expectRevert("PaxonToken: transfer from the zero address");
        token.transferFrom(address(0), alice, 100 * 10 ** 18);
    }

    function testTransferFromToZeroAddress() public {
        token.approve(bob, 200 * 10 ** 18);
        vm.prank(bob);
        vm.expectRevert("PaxonToken: transfer to the zero address");
        token.transferFrom(address(this), address(0), 100 * 10 ** 18);
    }

    function testTransferFromInsufficientBalance() public {
        token.transfer(alice, 100 * 10 ** 18);
        token.approve(bob, 200 * 10 ** 18);
        vm.prank(bob);
        vm.expectRevert("PaxonToken: transfer amount exceeds balance");
        token.transferFrom(alice, address(this), 101 * 10 ** 18);
    }

    function testTransferFromInsufficientAllowance() public {
        token.transfer(alice, 200 * 10 ** 18);
        vm.prank(alice);
        token.approve(bob, 100 * 10 ** 18);
        vm.prank(bob);
        vm.expectRevert("PaxonToken: transfer amount exceeds allowance");
        token.transferFrom(alice, address(this), 101 * 10 ** 18);
    }

    function testApproveToZeroAddress() public {
        vm.expectRevert("PaxonToken: approve to the zero address");
        token.approve(address(0), 100 * 10 ** 18);
    }

    function testTransferFromExplicitSuccess() public {
        token.transfer(alice, 200 * 10 ** 18);
        vm.prank(alice);
        token.approve(bob, 150 * 10 ** 18);
        vm.prank(bob);
        token.transferFrom(alice, address(this), 100 * 10 ** 18);
        assertEq(token.balanceOf(address(this)), 800 * 10 ** 18 + 100 * 10 ** 18);
    }

    function testMintToZeroAddress() public {
        vm.expectRevert("PaxonToken: mint to the zero address");
        token.mint(address(0), 100 * 10 ** 18);
    }
}

```

### 第六步：运行测试

```bash
2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken on  feature/homework [+?] on 🐳 v27.5.1 (orbstack) via 🅒 base 
➜ forge test --match-path test/PaxonToken.t.sol --show-progress -vvvv
[⠊] Compiling...
[⠑] Compiling 1 files with Solc 0.8.20
[⠘] Solc 0.8.20 finished in 5.79s
Compiler run successful!
... ...

Suite result: ok. 22 passed; 0 failed; 0 skipped; finished in 6.71ms (19.21ms CPU time)

Ran 1 test suite in 195.04ms (6.71ms CPU time): 22 tests passed, 0 failed, 0 skipped (22 total tests)


2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken on  feature/homework [+?] on 🐳 v27.5.1 (orbstack) via 🅒 base took 7.2s 
➜ forge test --match-path test/PaxonToken.t.sol --show-progress -vv  
[⠊] Compiling...
No files changed, compilation skipped
test/PaxonToken.t.sol:PaxonTokenTest
  ↪ Suite result: ok. 22 passed; 0 failed; 0 skipped; finished in 5.88ms (14.33ms CPU time)

Ran 22 tests for test/PaxonToken.t.sol:PaxonTokenTest
[PASS] testAllowance() (gas: 36455)
[PASS] testApproveAndTransferFrom() (gas: 73307)
[PASS] testApproveExplicit() (gas: 36235)
[PASS] testApproveToZeroAddress() (gas: 9063)
[PASS] testBurn() (gas: 24292)
[PASS] testBurnFailInsufficientBalance() (gas: 11024)
[PASS] testBurnZeroAmount() (gas: 9249)
[PASS] testInitialSupply() (gas: 14309)
[PASS] testMetadata() (gas: 20369)
[PASS] testMint() (gas: 45470)
[PASS] testMintFailNotOwner() (gas: 13694)
[PASS] testMintToZeroAddress() (gas: 11191)
[PASS] testMintZeroAmount() (gas: 13204)
[PASS] testTransfer() (gas: 43464)
[PASS] testTransferFromExplicitSuccess() (gas: 74568)
[PASS] testTransferFromFromZeroAddress() (gas: 11678)
[PASS] testTransferFromInsufficientAllowance() (gas: 70213)
[PASS] testTransferFromInsufficientBalance() (gas: 70068)
[PASS] testTransferFromMaxAllowance() (gas: 53223)
[PASS] testTransferFromToZeroAddress() (gas: 36798)
[PASS] testTransferInsufficientBalance() (gas: 44359)
[PASS] testTransferToZeroAddress() (gas: 9508)
Suite result: ok. 22 passed; 0 failed; 0 skipped; finished in 5.88ms (14.33ms CPU time)

Ran 1 test suite in 194.77ms (5.88ms CPU time): 22 tests passed, 0 failed, 0 skipped (22 total tests)

```

### 第七步：查看测试覆盖率

```bash
2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken on  feature/homework [+?] on 🐳 v27.5.1 (orbstack) via 🅒 base 
➜ forge coverage                                                  
Warning: optimizer settings and `viaIR` have been disabled for accurate coverage reports.
If you encounter "stack too deep" errors, consider using `--ir-minimum` which enables `viaIR` with minimum optimization resolving most of the errors
[⠊] Compiling...
[⠒] Compiling 23 files with Solc 0.8.20
[⠢] Solc 0.8.20 finished in 3.13s
Compiler run successful!
Analysing contracts...
Running tests...

Ran 22 tests for test/PaxonToken.t.sol:PaxonTokenTest
[PASS] testAllowance() (gas: 38620)
[PASS] testApproveAndTransferFrom() (gas: 80129)
[PASS] testApproveExplicit() (gas: 38577)
[PASS] testApproveToZeroAddress() (gas: 9922)
[PASS] testBurn() (gas: 26957)
[PASS] testBurnFailInsufficientBalance() (gas: 11715)
[PASS] testBurnZeroAmount() (gas: 9522)
[PASS] testInitialSupply() (gas: 15397)
[PASS] testMetadata() (gas: 23286)
[PASS] testMint() (gas: 48708)
[PASS] testMintFailNotOwner() (gas: 14832)
[PASS] testMintToZeroAddress() (gas: 12088)
[PASS] testMintZeroAmount() (gas: 14202)
[PASS] testTransfer() (gas: 46170)
[PASS] testTransferFromExplicitSuccess() (gas: 79946)
[PASS] testTransferFromFromZeroAddress() (gas: 12944)
[PASS] testTransferFromInsufficientAllowance() (gas: 74764)
[PASS] testTransferFromInsufficientBalance() (gas: 73798)
[PASS] testTransferFromMaxAllowance() (gas: 57424)
[PASS] testTransferFromToZeroAddress() (gas: 39425)
[PASS] testTransferInsufficientBalance() (gas: 47024)
[PASS] testTransferToZeroAddress() (gas: 9986)
Suite result: ok. 22 passed; 0 failed; 0 skipped; finished in 9.51ms (23.01ms CPU time)

Ran 1 test suite in 201.54ms (9.51ms CPU time): 22 tests passed, 0 failed, 0 skipped (22 total tests)

╭-------------------------+-----------------+-----------------+-----------------+-----------------╮
| File                    | % Lines         | % Statements    | % Branches      | % Funcs         |
+=================================================================================================+
| script/PaxonToken.s.sol | 0.00% (0/6)     | 0.00% (0/4)     | 100.00% (0/0)   | 0.00% (0/2)     |
|-------------------------+-----------------+-----------------+-----------------+-----------------|
| src/PaxonToken.sol      | 100.00% (58/58) | 100.00% (45/45) | 100.00% (24/24) | 100.00% (13/13) |
|-------------------------+-----------------+-----------------+-----------------+-----------------|
| Total                   | 90.62% (58/64)  | 91.84% (45/49)  | 100.00% (24/24) | 86.67% (13/15)  |
╰-------------------------+-----------------+-----------------+-----------------+-----------------╯

```



### 第八步：**运行测试并生成覆盖率报告**

```bash
2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken on  feature/homework [+?] on 🐳 v27.5.1 (orbstack) via 🅒 base took 4.1s 
➜ forge coverage > test-report.txt
Warning: optimizer settings and `viaIR` have been disabled for accurate coverage reports.
If you encounter "stack too deep" errors, consider using `--ir-minimum` which enables `viaIR` with minimum optimization resolving most of the errors
```

Test-report.txt

```txt
Compiling 23 files with Solc 0.8.20
Solc 0.8.20 finished in 3.13s
Compiler run successful!
Analysing contracts...
Running tests...

Ran 22 tests for test/PaxonToken.t.sol:PaxonTokenTest
[PASS] testAllowance() (gas: 38620)
[PASS] testApproveAndTransferFrom() (gas: 80129)
[PASS] testApproveExplicit() (gas: 38577)
[PASS] testApproveToZeroAddress() (gas: 9922)
[PASS] testBurn() (gas: 26957)
[PASS] testBurnFailInsufficientBalance() (gas: 11715)
[PASS] testBurnZeroAmount() (gas: 9522)
[PASS] testInitialSupply() (gas: 15397)
[PASS] testMetadata() (gas: 23286)
[PASS] testMint() (gas: 48708)
[PASS] testMintFailNotOwner() (gas: 14832)
[PASS] testMintToZeroAddress() (gas: 12088)
[PASS] testMintZeroAmount() (gas: 14202)
[PASS] testTransfer() (gas: 46170)
[PASS] testTransferFromExplicitSuccess() (gas: 79946)
[PASS] testTransferFromFromZeroAddress() (gas: 12944)
[PASS] testTransferFromInsufficientAllowance() (gas: 74764)
[PASS] testTransferFromInsufficientBalance() (gas: 73798)
[PASS] testTransferFromMaxAllowance() (gas: 57424)
[PASS] testTransferFromToZeroAddress() (gas: 39425)
[PASS] testTransferInsufficientBalance() (gas: 47024)
[PASS] testTransferToZeroAddress() (gas: 9986)
Suite result: ok. 22 passed; 0 failed; 0 skipped; finished in 13.52ms (43.74ms CPU time)

Ran 1 test suite in 204.43ms (13.52ms CPU time): 22 tests passed, 0 failed, 0 skipped (22 total tests)

╭-------------------------+-----------------+-----------------+-----------------+-----------------╮
| File                    | % Lines         | % Statements    | % Branches      | % Funcs         |
+=================================================================================================+
| script/PaxonToken.s.sol | 0.00% (0/9)     | 0.00% (0/9)     | 100.00% (0/0)   | 0.00% (0/2)     |
|-------------------------+-----------------+-----------------+-----------------+-----------------|
| src/PaxonToken.sol      | 100.00% (58/58) | 100.00% (45/45) | 100.00% (24/24) | 100.00% (13/13) |
|-------------------------+-----------------+-----------------+-----------------+-----------------|
| Total                   | 86.57% (58/67)  | 83.33% (45/54)  | 100.00% (24/24) | 86.67% (13/15)  |
╰-------------------------+-----------------+-----------------+-----------------+-----------------╯

```

### 第九步：生成详细的 HTML 覆盖率报告

需要更详细的可视化报告，可以生成 LCOV 文件并转换为 HTML 格式：

首先运行以下命令生成 LCOV 文件：

```bash
2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken on  feature/homework [+?] on 🐳 v27.5.1 (orbstack) via 🅒 base took 4.1s 
➜ forge coverage --report lcov --report-file coverage.lcov
Warning: optimizer settings and `viaIR` have been disabled for accurate coverage reports.
If you encounter "stack too deep" errors, consider using `--ir-minimum` which enables `viaIR` with minimum optimization resolving most of the errors
[⠊] Compiling...
[⠒] Compiling 23 files with Solc 0.8.20
[⠆] Solc 0.8.20 finished in 3.15s
Compiler run successful!
Analysing contracts...
Running tests...

Ran 22 tests for test/PaxonToken.t.sol:PaxonTokenTest
[PASS] testAllowance() (gas: 38620)
[PASS] testApproveAndTransferFrom() (gas: 80129)
[PASS] testApproveExplicit() (gas: 38577)
[PASS] testApproveToZeroAddress() (gas: 9922)
[PASS] testBurn() (gas: 26957)
[PASS] testBurnFailInsufficientBalance() (gas: 11715)
[PASS] testBurnZeroAmount() (gas: 9522)
[PASS] testInitialSupply() (gas: 15397)
[PASS] testMetadata() (gas: 23286)
[PASS] testMint() (gas: 48708)
[PASS] testMintFailNotOwner() (gas: 14832)
[PASS] testMintToZeroAddress() (gas: 12088)
[PASS] testMintZeroAmount() (gas: 14202)
[PASS] testTransfer() (gas: 46170)
[PASS] testTransferFromExplicitSuccess() (gas: 79946)
[PASS] testTransferFromFromZeroAddress() (gas: 12944)
[PASS] testTransferFromInsufficientAllowance() (gas: 74764)
[PASS] testTransferFromInsufficientBalance() (gas: 73798)
[PASS] testTransferFromMaxAllowance() (gas: 57424)
[PASS] testTransferFromToZeroAddress() (gas: 39425)
[PASS] testTransferInsufficientBalance() (gas: 47024)
[PASS] testTransferToZeroAddress() (gas: 9986)
Suite result: ok. 22 passed; 0 failed; 0 skipped; finished in 11.14ms (24.29ms CPU time)

Ran 1 test suite in 209.53ms (11.14ms CPU time): 22 tests passed, 0 failed, 0 skipped (22 total tests)
Wrote LCOV report.

```

然后使用 genhtml（需要先安装 LCOV 工具，例如通过 sudo apt install lcov 或 brew install lcov）将 LCOV 文件转换为 HTML 报告：

```bash
2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken on  feature/homework [+?] on 🐳 v27.5.1 (orbstack) via 🅒 base took 3.9s 
➜ genhtml coverage.lcov --output-directory coverage-report
Reading tracefile coverage.lcov.
Found 2 entries.
Found common filename prefix "/Users/qiaopengjun/Code/polkadot/2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken"
Generating output.
Processing file src/PaxonToken.sol
  lines=58 hit=58 functions=13 hit=13
Processing file script/PaxonToken.s.sol
  lines=9 hit=0 functions=2 hit=0
Overall coverage rate:
  source files: 2
  lines.......: 86.6% (58 of 67 lines)
  functions...: 86.7% (13 of 15 functions)
Message summary:
  no messages were reported
```

完成后，打开 coverage-report/index.html 文件即可在浏览器中查看详细的覆盖率报告，显示每行代码的执行情况。

### ![image-20250321212123782](/images/image-20250321212123782.png)

### 第十步：remix 连接 本地项目

![image-20250321212747574](/images/image-20250321212747574.png)

```bash
2025-17-solidity-on-polkadot on  feature/homework [+?] on 🐳 v27.5.1 (orbstack) via 🅒 base took 25.9s 
➜ remixd -s /Users/qiaopengjun/Code/polkadot/2025-17-solidity-on-polkadot/homework-2/1490/code/PaxonToken -u http://remix.ethereum.org
[WARN] latest version of remixd is 0.6.44, you are using 0.6.41
[WARN] please update using the following command:
[WARN] yarn global add @remix-project/remixd
[WARN] You may now only use IDE at http://remix.ethereum.org to connect to that instance
[WARN] Any application that runs on your computer can potentially read from and write to all files in the directory.
[WARN] Symbolic links are not forwarded to Remix IDE

[INFO] Fri Mar 21 2025 21:39:28 GMT+0800 (China Standard Time) remixd is listening on 127.0.0.1:65520
[INFO] Fri Mar 21 2025 21:39:28 GMT+0800 (China Standard Time) slither is listening on 127.0.0.1:65523
[INFO] Fri Mar 21 2025 21:39:28 GMT+0800 (China Standard Time) foundry is listening on 127.0.0.1:65525
```



连接失败：


### 第十步：部署合约


点击确认：


成功部署：



### 第十一步：查看合约

合约地址：0x8b5a5b438ca58167a7a6552d45f36490d4a1dadb

- <https://assethub-westend.subscan.io/account/0x8b5a5b438ca58167a7a6552d45f36490d4a1dadb>



## 总结

通过本次实战，我们用 Solidity 在 Polkadot 的 Asset Hub 上成功打造并上线了 PaxonToken，完整验证了从开发到部署的 Web3 开发流程。Polkadot 2.0 的开放性让开发者能轻松将以太坊经验应用于跨链生态，开启更多创新可能。这份指南不仅是入门的敲门砖，更是探索 Web3 未来的起点——现在，就用 Solidity 在 Polkadot 上开启你的实战之旅吧！

## 参考

- <https://www.youtube.com/watch?v=2QBa0Rk_4vo&list=PLKgwQU2jh_H-aBKhG3X7XueytJVl_YuiF&index=5>
- <https://wiki.polkadot.network/docs/build-smart-contracts>
- <https://wiki.acala.network/build/development-guide>
- <https://contracts.polkadot.io/tutorial/try>
- <https://support.subscan.io/doc-361776>
- <https://pro.subscan.io/overview>
- <https://github.com/papermoonio/2025-17-solidity-on-polkadot>
- <https://github.com/paritytech/>
- <https://github.com/paritytech/rust-contract-template/blob/master/src/main.rs>
- <https://github.com/paritytech/polkadot-sdk/blob/master/substrate/frame/revive/fixtures/contracts/balance.rs>
- <https://github.com/polkadot-evm/frontier>
- <https://remix-ide.readthedocs.io/en/latest/remixd.html#ports-usage>
- <https://remix-ide.readthedocs.io/zh-cn/latest/remixd.html>
- <https://faucet.polkadot.io/westend?parachain=1000>
- <https://mp.weixin.qq.com/s/f7QAQExGMLT4L7jbdwb_zw>
- <https://github.com/smol-dot/smoldot>

# 2025-08-19

# Web3 金融：Uniswap V2 资金效率深度剖析

在 Web3 时代，去中心化金融（DeFi）正重塑传统金融格局，而 Uniswap V2 作为自动做市商（AMM）的先锋，以其独特的 x·y=k 模型为市场提供了无限价格区间的流动性。然而，低资金利用率成为其核心痛点：大量资金闲置在非活跃价格区间，限制了流动性提供者的收益效率。本文将深入剖析 Uniswap V2 的资金利用率问题，通过实际案例计算 ETH/DAI 交易对在价格波动下的资金效率，并探讨其局限性及后续优化的方向。无论你是 DeFi 新手还是资深玩家，这场 Web3 金融的深度解析都将为你揭开 Uniswap V2 的效率密码！

Uniswap V2 凭借 x·y=k 的恒定乘积模型，为 Web3 金融市场提供去中心化流动性，但其资金利用率偏低的问题限制了资本效率。本文以 ETH/DAI 交易对为例，通过价格波动（1300 DAI/ETH 和 2200 DAI/ETH）下的资金利用率计算，揭示 DAI 和 ETH 两侧的资金变化规律：价格下降时 DAI 利用率约 6.92%，ETH 增加 7.4%；价格上涨时 DAI 增加 21.24%，ETH 利用率达 17.33%。分析表明，Uniswap V2 的流动性分散在无限价格区间，导致大部分资金未被高效利用，且在单向市场压力下可能出现资产“耗尽”。本文进一步探讨这一局限性如何推动 Uniswap V3 的集中流动性创新，为 Web3 金融的未来提供启示。

## Uniswap V2 的资金利用率

假设 ETH/DAI 交易对的实时价格为 1500 DAI/ETH，交易对的流动性池中共有资金：4500 DAI 和 3 ETH，根据 Uniswap V2 的公式(x *y = k)，x  = 4500，y = 3，k = 4500* 3 = 13500。
假设 x 表示流动性池中的 DAI，y 表示流动性池中的 ETH，k 表示流动性池中 DAI 和 ETH 的乘积。即初始阶段 x1  = 4500，y1 = 3，k1 = 13500。

### 当价格下降到 1300 DAI/ETH 时

$$
x \cdot y = k = L^2 \\
x2 \cdot  y2 = 13500 \\
$$
$$
\frac{x2}{y2} = 1300
$$
计算 x2 和 y2：
$$
x2 = 1300 \cdot y2 = \frac{13500}{y2} \\
$$
$$
y2 = \frac{x2}{1300} = \frac{13500}{x2} \\
$$
因为：
$$
\begin{align*}
x_2 \cdot y_2 &= 13500 \\
x_2 &= 1300 \cdot y_2 \\
1300 \cdot y_2^2 &= 13500 \\
y_2^2 &= \frac{13500}{1300} \\
y_2 &= \sqrt{\frac{13500}{1300}}
\end{align*}
$$
我们来计算：
$$
y_2 = \sqrt{\frac{13500}{1300}}
$$
先算分数：

$$
\frac{13500}{1300} = \frac{1350}{130} = \frac{135}{13} \approx 10.3846
$$

再开根号：

$$
y_2 = \sqrt{10.3846} \approx 3.222
$$

**所以，计算结果是：**

$$
y_2 \approx 3.222
$$

已知：

$$
x_2 = 1300 \cdot y_2
$$

刚才算出：

$$
y_2 \approx 3.222
$$

代入计算：

$$
x_2 = 1300 \times 3.222 \approx 4188.6
$$

**所以：**

$$
x_2 \approx 4188.6
$$

综上可得：
**x2 = 4188.6，y2 = 3.222**

## 资金利用率

我们分别计算 DAI 和 ETH 两侧的资金利用率。

### 1. DAI 侧资金利用率

$$
\text{DAI 侧资金利用率} = \frac{x_1 - x_2}{x_1} \times 100\%
$$

- \( x_1 = 4500 \)
- $( x_2 \approx 4188.6 $)

$$
\begin{align*}
\frac{4500 - 4188.6}{4500} \times 100\% \approx \\ \frac{311.4}{4500} \times 100\% \\ \approx 6.92\%
\end{align*}
$$

### 2. ETH 侧资金利用率

$$
\text{ETH 侧资金利用率} = \frac{y_2 - y_1}{y_1} \times 100\%
$$

注意：这里 \(y_2 > y_1\)，因为 ETH 增加了，实际应该是流出 ETH 的比例（即 ETH 被买走的比例），所以用：

$$
\text{ETH 侧资金利用率} = \frac{y_2 - y_1}{y_2} \times 100\%
$$
或者
$$
\text{ETH 侧资金利用率} = \frac{y_2 - y_1}{y_1} \times 100\%
$$

但通常和 DAI 一样，计算“减少的部分占初始的比例”，所以：

$$
\text{ETH 侧资金利用率} = \frac{y_1 - y_2}{y_1} \times 100\%
$$

- \( y_1 = 3 \)
- \( y_2 \approx 3.222 \)

$$
\begin{align*}
\frac{3 - 3.222}{3} \times 100\% = \frac{-0.222}{3} \times 100\% \\ \approx -7.4\%
\end{align*}
$$

负号表示 ETH 增加了（DAI 换成了 ETH），这和 AMM 的 swap 方向有关。

### 3. 总结

- **DAI 侧资金利用率**：\(\boxed{6.92\%}\)
- **ETH 侧资金利用率**：\(\boxed{-7.4\%}\)（ETH 增加，DAI 减少）

如果你想要 ETH 被“卖出”的比例（即 ETH 减少的情况），那要看价格上涨的场景。

---

DAI 侧资金利用率为：
$$
\frac{4500 - 4188.6}{4500} \times 100\% \approx 6.92\%
$$

ETH 侧资金利用率为：
$$
\frac{3 - 3.222}{3} \times 100\% \approx -7.4\%
$$

### 假设当价格涨到 2200 DAI/ETH 时

$$
\begin{align*}
x \cdot y = k = L^2 \\
x3 \cdot  y3 = 13500 \\
\end{align*}
$$
$$
\frac{x3}{y3} = 2200
$$
计算 x3 和 y3：
$$
x3 = 2200 \cdot y3 = \frac{13500}{y3} \\
$$
$$
y3 = \frac{x3}{2200} = \frac{13500}{x3} \\
$$
因为：
$$
\begin{align*}
x_3 \cdot y_3 &= 13500 \\
x_3 &= 2200 \cdot y_3 \\
2200 \cdot y_3^2 &= 13500 \\
y_3^2 &= \frac{13500}{2200} \\
y_3 &= \sqrt{\frac{13500}{2200}}
\end{align*}
$$
我们来计算：

$$
y_3 = \sqrt{\frac{13500}{2200}}
$$

先算分数：
$$
\frac{13500}{2200} = \frac{1350}{220} = \frac{135}{22} \approx 6.1364
$$

再开根号：

$$
y_3 = \sqrt{6.1364} \approx 2.48
$$

**所以，计算结果是：**

$$
y_3 \approx 2.48
$$

已知：

$$
x3 = 2200 \cdot y3
$$

刚才算出：

$$
y3 \approx 2.48
$$

代入计算：

$$
x3 = 2200 \times 2.48 \approx 5456
$$

**所以：**

$$
x3 \approx 5456
$$

综上可得：**x3 = 5456，y3 = 2.48**

## 资金利用率计算

我们分别计算 DAI 和 ETH 两侧的资金利用率。

### 1. DAI 侧资金利用率

$$
\text{DAI 侧资金利用率} = \frac{x_1 - x_3}{x_1} \times 100\%
$$

- \( x_1 = 4500 \)
- $( x_3 \approx 5456 $)

$$
\begin{align*}
\frac{4500 - 5456}{4500} \times 100\% \approx \frac{-956}{4500} \times 100\% \\ \approx -21.24\%
\end{align*}
$$

### 2. ETH 侧资金利用率

$$
\text{ETH 侧资金利用率} = \frac{y_1 - y_3}{y_1} \times 100\%
$$

- $( y_1 = 3 $)
- $( y_3 \approx 2.48 $)

$$
\frac{3 - 2.48}{3} \times 100\% = \frac{0.52}{3} \times 100\% \approx 17.33\%
$$

### 3. 总结

- **DAI 侧资金利用率**：$\boxed{-21.1\%}$
- **ETH 侧资金利用率**：$\boxed{17.33\%}$

## Uniswap V2 有什么问题？

资金利用率问题
> 基于 x⋅y=k 的恒定乘积自动做市商（AMM）模型，其核心特点是理论上能够在从零到正无穷的整个价格区间内为市场提供流动性。然而，正是因为流动性资金被分散到如此广阔、乃至无限的价格范围上，导致在任何给定时间，只有围绕当前市场价格的很小一部分资金被实际用于促成交易。这就造成了整体资金利用率偏低的问题：大部分存入的资金处于“非活跃”或“闲置”状态，未能被高效利用，这通常会影响流动性提供者（LPs）的资本回报效率。当市场价格发生剧烈且持续的单向变动，导致资金池中某一侧的资产（例如，代币 X）被完全兑换耗尽时，该 AMM 将无法继续提供该种已耗尽资产（代币 X）的兑换服务（即池子无法再卖出代币 X）。这种固有的资本效率限制也是驱动后续 AMM 创新（如集中流动性模型）以寻求更高资金利用率的重要原因之一。

- x⋅y=k 这个公式本身并不能阻止其中一种代币在持续的单向市场压力下被买到接近枯竭。
- 当一种代币（比如X）被大量买走，其在池中的存量 x 变得极小，那么它的价格就会变得极高。
- “耗尽”在实践中意味着该代币的存量小到无法进行有意义的交易，或者其价格高到无人愿意购买。此时，AMM池中几乎只剩下另一种代币（Y）。
- 这种情况正是由“市场价格发生剧烈且持续的单向变动”所驱动的，AMM通过调整内部价格来响应外部市场的变化，直到自身储备达到极限。
  
所以，尽管 k 试图保持不变，但它约束的是 x 和 y 的乘积，而不是它们各自的绝对数量。在极端单向市场条件下，一个变量可以趋向于0，而另一个变量则会相应地趋向于无穷大，从而导致事实上的“耗尽”。

为了解决 Uniswap V2 资金利用率偏低这一核心问题，后续诞生了 Uniswap V3 协议。

## 总结

Uniswap V2 的 x·y=k 模型为 Web3 金融奠定了去中心化交易的基础，但其资金利用率偏低的问题不容忽视。通过对 ETH/DAI 交易对的分析，我们看到价格波动下资金利用率仅在 6.92% 至 17.33% 之间，大量流动性闲置在非活跃价格区间，且单向市场压力可能导致资产耗尽。这一局限性揭示了 AMM 模型的资本效率瓶颈，也推动了 Uniswap V3 集中流动性方案的诞生。未来，Web3 金融的创新将继续聚焦资金效率优化，为流动性提供者和投资者创造更大价值。探索 Uniswap 的效率之道，正是解锁 DeFi 潜力的关键一步！

## 参考

- <https://github.com/Uniswap/v3-core>
- <https://github.com/Uniswap/v3-periphery/tree/main/contracts>
- <https://docs.uniswap.org/contracts/v3/guides/local-environment>
- <https://j1mmy.fi/uniswapv3/pool>

# 2025-08-18

## Solidity `approve` 函数授权的抢跑问题及其解决方案

在 Solidity 智能合约中，特别是遵循 ERC20 标准的代币合约，`approve` 函数存在一个常见的安全隐患，即“授权抢跑”（Front-running）问题。该问题源于以太坊交易的异步特性。当一个用户决定修改之前给予另一个地址（例如一个去中心化交易所）的代币花费授权额度时，会发起一笔新的 `approve` 交易。在旧的授权额度（例如 1000）尚未被新额度（例如 500）覆盖的这笔交易等待被矿工打包的间隙，被授权方可以监听到这笔待处理的交易。恶意攻击者可以立即以更高的 Gas 费发起一笔 `transferFrom` 交易，抢先将原有的 1000 额度全部或部分转走，待用户的修改授权交易被确认后，攻击者可能还可以继续使用新的 500 额度，从而导致用户的资产损失超出预期。

为解决此问题，业界提出了几种有效的解决方案：

1.  **先归零再授权**：这是一种简单有效的策略。用户在修改授权额度之前，先发送一笔交易将授权额度设置为 0，等待该交易确认后，再发送一笔新的交易，将额度设置为期望的目标值。这样就彻底杜绝了旧额度被利用的风险窗口，但缺点是需要用户执行两次交易，增加了操作复杂性和 Gas 成本。

2.  **使用 `increaseAllowance` 和 `decreaseAllowance`**：许多现代的 ERC20 实现（如 OpenZeppelin 的早期版本）提供了 `increaseAllowance` (增加授权) 和 `decreaseAllowance` (减少授权) 这两个函数来替代标准的 `approve`。这些函数通过在当前授权值的基础上进行原子化的加减操作来更新额度，而不是直接覆盖。例如，若想将授权从 1000 降至 500，应调用 `decreaseAllowance(500)`。即使攻击者在此期间抢跑交易，也只会影响减少前的额度，而不会出现“旧额度+新额度”的重复花费问题。

3.  **使用 `safeApprove`**：尽管在最新版本的 OpenZeppelin 合约库中已被弃用，但 `safeApprove` 曾是推荐的解决方案之一。它强制要求在设置非零的新额度之前，当前额度必须为零，从而在合约层面强制执行了“先归零再授权”的最佳实践。

在当前的开发实践中，最推荐的做法是谨慎处理授权变更，优先考虑使用原子化的额度增减函数。如果合约未提供此类函数，则应在用户界面引导用户采用“先归零再授权”的两步操作流程，以确保资金安全。

# 2025-08-17

# Monad 验证合约
```bash
➜ forge verify-contract \
  --rpc-url https://testnet-rpc.monad.xyz \
  --verifier sourcify \
  --verifier-url 'https://sourcify-api-monad.blockvision.org' \
  0x464bDd2610B4f039E18d8a8E2A720D39cD559EF1 \
  src/MonaPacketAccount.sol:MonaPacketAccount


➜ # 指令: 调用 transfer 函数，把 1 个 MPKT 转给新的接收者 0x750E...
cast calldata "transfer(address,uint256)" 0x750Ea21c1e98CcED0d4557196B6f4a5974CCB6f5 1000000000000000000
0xa9059cbb000000000000000000000000750ea21c1e98cced0d4557196b6f4a5974ccb6f50000000000000000000000000000000000000000000000000de0b6b3a7640000

```

# 2025-08-16

# 调用合约方法 参考
```bash
➜ cast send 0x1d3a01A7218357a73142750F8d067468132ED63B \
  "execute(address,uint256,bytes,uint8)" \
  0xa006cD3172Ef04D0199F4df9682E8297c133D69e \
  0 \
  $TRANSFER_DATA \
  0 \
  --private-key $PRIVATE_KEY \
  --rpc-url $MONAD_RPC_URL

blockHash            0x90f96384239a8a675033e51e6ae7eb9e8590763784ac304a3b7c46a3ad264094
blockNumber          31095210
contractAddress      
cumulativeGasUsed    9712955
effectiveGasPrice    50000000001
from                 0x750Ea21c1e98CcED0d4557196B6f4a5974CCB6f5
gasUsed              23048
logs                 []
logsBloom            0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
root                 
status               1 (success)
transactionHash      0x98993c0155530d8b27ed6d79f4dda21489a086845eec75f5daade67be23960f7
transactionIndex     43
type                 2
blobGasPrice         
blobGasUsed          
to                   0x1D3A01A7218357A73142750f8D067468132ed63B
```

# 2025-08-15

# Web3开发必知：Solidity内存布局（Storage、Memory、Stack）解析

在以太坊智能合约开发中，Solidity的内存布局是确保合约高效运行的核心。理解Storage（存储区）、Memory（内存区）和Stack（栈）三种存储位置的特性与用途，不仅有助于优化gas成本，还能提升合约的安全性和性能。本文将深入探讨这三者的工作原理、内存布局规则及实际应用场景，为开发者提供清晰的指导。

本文深入解析了Web3智能合约开发中Solidity的三大存储位置：Storage、Memory和Stack。Storage用于持久化区块链数据，gas成本高；Memory适合临时数据处理，高效低耗；Stack由EVM管理，优化快速计算。通过对比特性、成本和应用场景，并结合代码示例，助力开发者优化Web3合约性能。

## Storage、Memory、Stack

在 Solidity 中，内存布局是智能合约执行的核心部分，涉及三种主要的存储位置：Storage（存储区）、Memory（内存区） 和 Stack（栈）。

### Storage（存储区）

Storage 是区块链上持久化存储数据的区域，每个合约都有自己的 Storage 空间，用于存储状态变量。

特点:

- 持久性: 数据存储在区块链上，合约执行后数据会永久保留（除非被修改或合约销毁）。
- 高成本: 读写 Storage 的 gas 成本非常高，尤其是写入操作（约 20,000 gas 初次写入，5,000 gas 修改）。
- 结构: Storage 是一个键值存储，数据按槽位（slot）组织，每个槽位 32 字节（256 位）。状态变量按声明顺序依次存储。
- 访问: 状态变量默认存储在 Storage 中，且可以通过合约直接访问。

用途:

- 存储合约的持久化数据，如用户余额、合约配置等。
- 状态变量（如 uint public value;）默认存储在 Storage。

内存布局规则:

- 变量按声明顺序分配槽位（从槽 0 开始）。
- 小于 32 字节的变量会尽量打包到同一个槽位（优化存储）。
- 动态数据（如数组、映射）使用复杂的存储布局（涉及哈希计算）。

示例：

```ts
contract StorageExample {
    uint public value; // 存储在 Storage 槽 0
    address public owner; // 存储在 Storage 槽 1

    function setValue(uint _value) public {
        value = _value; // 写入 Storage，消耗较多 gas
    }
}
```

### Memory（内存区）

Memory 是临时存储区域，用于存储在函数执行期间需要的数据，生命周期仅限于函数调用。

特点:

- 临时性: 数据在函数执行结束后销毁。
- 低成本: 读写 Memory 的 gas 成本远低于 Storage（通常只需几 gas）。
- 动态分配: Memory 是动态分配的，适合处理临时数据或复杂的数据结构。
- 访问: 局部变量（除基本类型的简单变量外）需要显式声明为 memory（如 string memory 或 uint[] memory）。

用途:

- 存储函数中的临时变量、函数参数或返回值。
- 处理动态数据结构（如数组、字符串、结构体）。
- 常用于函数内部的计算或数据处理。

内存布局规则:

- Memory 按 32 字节对齐，数据从低地址向高地址分配。
- 动态数组和字符串会分配额外的元数据（如长度）。
- Solidity 提供 memory 关键字来明确指定存储位置。

示例：

```ts
contract MemoryExample {
    function processData(uint[] memory data) public pure returns (uint) {
        uint sum = 0;
        for (uint i = 0; i < data.length; i++) {
            sum += data[i]; // 访问 Memory 数据
        }
        return sum;
    }
}
```

### Stack（栈）

Stack 是 EVM（以太坊虚拟机）用于存储临时变量的内存区域，基于后进先出（LIFO）的结构。

特点:

- 极短生命周期: 栈中的数据仅在函数执行的特定操作期间存在。
- 高效: 栈操作几乎不消耗 gas，适合快速计算。
- 限制: 栈大小有限（最大 1024 个元素），每个元素 32 字节。
- 访问: 栈主要由 EVM 自动管理，开发者无法直接控制栈的内容。

用途:

- 存储简单类型的局部变量（如 uint、address）的中间值。
- 用于函数调用时的参数传递和返回值的临时存储。
- EVM 操作码（如 ADD、MUL）会使用栈来处理计算。

示例：

```bash
contract StackExample {
    function add(uint a, uint b) public pure returns (uint) {
        uint result = a + b; // a, b, result 可能短暂存储在栈中
        return result;
    }
}
```

### 三者对比

| 特性       | Storage                                 | Memory                    | Stack               |
| ---------- | --------------------------------------- | ------------------------- | ------------------- |
| 存储位置   | 区块链上                                | 内存（RAM）               | EVM 栈              |
| 生命周期   | 永久（直到合约销毁）                    | 函数调用期间              | 操作期间（极短）    |
| Gas 成本   | 高（读 ~200 gas，写 ~5,000-20,000 gas） | 低（几 gas）              | 几乎为 0            |
| 数据类型   | 状态变量、映射、动态数组                | 局部变量、数组、字符串    | 简单变量、中间值    |
| 大小限制   | 几乎无限制（受区块链限制）              | 受内存分配限制            | 1024 个 32 字节元素 |
| 开发者控制 | 直接控制（状态变量）                    | 显式声明（memory 关键字） | 间接（由 EVM 管理） |
| 典型用途   | 持久化数据存储                          | 临时数据处理              | 快速计算和参数传递  |

### 技术精粹

- Storage 适合持久化数据，但 gas 成本高，需优化使用。
- Memory 适合临时数据处理，gas 成本低，需显式声明。
- Stack 由 EVM 管理，适合快速计算，但受限于大小和生命周期。

## 总结

Solidity的内存布局是Web3智能合约开发的关键。Storage确保数据持久但成本高，Memory灵活处理临时数据，Stack支持高效计算。开发者应根据Web3应用场景合理选择存储位置，优化gas成本，提升合约效率与安全性。

## 参考

- <https://soliditylang.org/>
- <https://docs.soliditylang.org/en/v0.8.29/>
- <https://ethereum.github.io/yellowpaper/paper.pdf>
- <https://ethereum.stackexchange.com/>
- <https://github.com/ethereum/solidity>

# 2025-08-14

# 私钥怎么在 TEE 里面管理

在 **TEE（受信执行环境，Trusted Execution Environment）** 中管理 **私钥** 的方法主要依赖于 TEE 提供的硬件级隔离和安全性机制，以确保私钥不会泄露或被恶意访问。TEE 通过其安全硬件功能创建一个隔离的环境，在其中执行敏感的操作，比如密钥管理和加密计算。以下是私钥在 TEE 中管理的基本原理和流程：

### 1. **私钥存储**

在 TEE 中，私钥并不是直接存储在操作系统或者应用程序可访问的内存中。相反，TEE 提供了一个隔离的“安全区域”，其中私钥被存储和管理。只有在 TEE 环境内，受信的代码才能访问到私钥。

- **加密存储**：私钥在 TEE 中存储时，通常会被加密并保存在专门的受信存储空间内，只有 TEE 能够解密和访问这些数据。
- **受信区域**：通过硬件支持，TEE 提供了一个**安全区域**，通常被称为“安全世界”（Secure World），私钥只能在这个区域内操作。

### 2. **私钥的使用**

当需要使用私钥进行签名或解密操作时，私钥不会离开 TEE 的安全区域。所有操作都在这个安全区域内部执行，并且不会暴露给主操作系统或其他应用程序。这有助于防止私钥被恶意软件、操作系统漏洞或硬件攻击泄露。

#### 签名操作

- 当需要使用私钥签名数据时，数据首先会被传递到 TEE 内部。
- 在 TEE 内部，私钥会被用于生成签名，而签名过程本身是在隔离的环境中执行的。
- 签名结果（而非私钥本身）会返回给请求方，私钥仍然保持隔离。

#### 解密操作

- 对于加密操作，私钥也不会暴露。只有在 TEE 内部，解密操作才能被执行，明文数据会直接返回，而私钥不外泄。

### 3. **密钥生成与保护**

在 TEE 中，私钥的生成通常是通过硬件随机数生成器（HRNG）来确保密钥的安全性。TEE 提供了硬件级别的随机数生成器，以产生高质量的密钥，并通过受信环境保证其不可预测性和安全性。

- **密钥生成**：密钥生成过程发生在 TEE 内部，私钥在生成时就被存储在安全区域内，并受到硬件保护。
- **加密保护**：在生成后，私钥通常会通过加密算法加密存储，防止它在未授权访问的情况下被读取。

### 4. **防止泄漏和攻击**

TEE 设计的核心之一就是防止私钥泄漏。它通过多种机制确保私钥不受外部攻击的影响：

- **硬件隔离**：TEE 与主操作系统和应用程序运行在不同的环境中，主操作系统无法直接访问或修改 TEE 中的敏感数据。
- **访问控制**：TEE 中的私钥只能由受信任的代码访问。只有经过认证并通过权限验证的应用程序或服务，才能在 TEE 内部执行与私钥相关的操作。
- **物理攻击防护**：TEE 通过硬件支持的反物理攻击机制（如抗侧信道攻击、物理插拔攻击）确保私钥不会因物理访问设备而泄露。

### 5. **密钥生命周期管理**

在 TEE 中，私钥的生命周期通常受到严格的管理，从生成、使用到销毁，整个过程都需要确保密钥的安全性。TEE 提供的安全功能包括：

- **密钥更新**：如果需要更新密钥（例如，定期轮换密钥），TEE 内部可以生成新的密钥并安全替换，而不需要将旧密钥暴露给外部。
- **销毁密钥**：当密钥不再需要时，TEE 提供了安全销毁功能，确保私钥在内存中被彻底清除，避免因后续恢复操作泄漏密钥。

### 6. **常见的 TEE 实现**

- **Intel SGX（Software Guard Extensions）**：Intel SGX 提供了硬件支持的安全环境，可以用于存储和管理私钥。SGX 支持创建“安全区域”来保护密钥操作。
- **ARM TrustZone**：ARM TrustZone 提供了一个基于硬件的隔离环境，将设备的硬件资源分为“安全世界”和“普通世界”，私钥和敏感数据通常存储在“安全世界”中。
- **AMD SEV（Secure Encrypted Virtualization）**：AMD SEV 提供的硬件加密功能，可以确保虚拟化环境中的私钥存储和处理安全。

### 总结

在 TEE 中管理私钥的关键是通过硬件隔离和安全区域保护私钥免受外部攻击，确保密钥的生成、存储和使用都在受信的环境内进行。TEE 提供的硬件支持和加密机制能够有效防止私钥泄露或被非法访问，确保敏感操作（如数字签名、加密通信等）在一个高度安全的环境中执行。

# 2025-08-13

# Web3 开发实战：用 Foundry 高效探索以太坊区块链

Web3 时代的到来，让以太坊区块链开发成为开发者关注的热点。Foundry 作为一款强大的 Solidity 开发工具集，凭借其命令行工具 cast，为开发者提供了查询区块链数据、调试交易和分析智能合约的高效途径。本文通过一系列实操案例，带你走进 Web3 开发的实战场景，探索如何用 Foundry 查询以太坊区块、交易详情、事件日志，并进行交易模拟，助力你在 Web3 开发中游刃有余！

本文详细介绍了 Foundry 的 cast 命令在以太坊开发中的多种应用，包括查询最新区块高度、区块详情、交易收据、Gas 单价，以及解析合约调用数据、事件签名和函数选择器等功能。通过具体案例，展示了从环境变量配置到 JSON 数据格式化，再到交易模拟执行的完整流程。文章还介绍了如何通过 cast run 调试交易调用栈，为 Web3 开发者提供实用参考，助力开发高效的去中心化应用。

## 实操

### 查询最新区块号（十六进制）

**通过 `export ETH_RPC_URL` 设置环境变量，让后续的 `cast` 命令自动使用该 RPC 节点，无需重复指定 `--rpc-url`。**

```bash
export ETH_RPC_URL="**************************************************************
echo $ETH_RPC_URL
*************************************************************
cast rpc eth_blockNumber
"0x7f93c9"

cast rpc eth_blockNumber
"0x95075b"
```

### 查询当前以太坊网络的最新区块高度（十进制格式）

```bash
cast block-number
8360917

# cast block-number
cast block-number

21914148
```

### 查询以太坊区块链上指定高度的区块详情

```bash
cast block 8360917


cast block 9766752


baseFeePerGas        7
difficulty           2
extraData            0x010000753003f85c47000c8eaa00000000000000000000000000000000000000c11e60141cf73be3d65bddbb95688e2465691424b881a7cd71c8560c2200d5480f35bcad7d1bd50a0ecac7ba68d7ff26bf6f059fde74dfd159a542faf39edc4600
gasLimit             2000000000
gasUsed              21000
hash                 0x09d4ff5bcf56f63f8fdd6b15b1f33f187ff7b1d9df6ff4bb6e6e294a7830328f
logsBloom            0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
miner                0x0000000000000000000000000000000000000000
mixHash              0x0000000000000000000000000000000000000000000000000000000000000000
nonce                0x0000000000000000
number               9766752
parentHash           0xaff11e9fd37dcc9ff6e3799f307ea81d6ed308e7ef29aa41ca40301ed485e38a
parentBeaconRoot     
transactionsRoot     0x27cecda093398159176bb88196189432b283fa6f75494ed72d6aa4af9e7319a6
receiptsRoot         0x056b23fbba480696b65fe5a59b8f2148a1299103c4f57df839233af2cf4ca2d2
sha3Uncles           0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347
size                 721
stateRoot            0x7e231fb86f75067ee6b5db48d60fb13585461aba09ee7fae9201484a8578f0aa
timestamp            1740373647 (Mon, 24 Feb 2025 05:07:27 +0000)
withdrawalsRoot      
totalDifficulty      19533505
blobGasUsed          
excessBlobGas        
requestsHash         
transactions:        [
 0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913
]
```

### 查询以太坊区块链上指定交易哈希的完整详细信息

```bash
# cast tx
cast tx 0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913

blockHash            0x09d4ff5bcf56f63f8fdd6b15b1f33f187ff7b1d9df6ff4bb6e6e294a7830328f
blockNumber          9766752
from                 0x228466F2C715CbEC05dEAbfAc040ce3619d7CF0B
transactionIndex     0
effectiveGasPrice    1361240798

gas                  21000
gasPrice             1361240798
hash                 0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913
input                0x
nonce                8584261
r                    0x43f3d959986d09dddff767cbfcc4a49641c04494533a78f40385106240de571a
s                    0x0cb55a2d14a186798c5f71432e66d6be8d1b9c3ce7ddb2ab8e30703147bb9850
to                   0x228466F2C715CbEC05dEAbfAc040ce3619d7CF0B
type                 0
v                    1
value                100
```

### 获取指定以太坊交易的 **收据（receipt）信息**

```bash
cast receipt 0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913

blockHash            0x09d4ff5bcf56f63f8fdd6b15b1f33f187ff7b1d9df6ff4bb6e6e294a7830328f
blockNumber          9766752
contractAddress      
cumulativeGasUsed    21000
effectiveGasPrice    1361240798
from                 0x228466F2C715CbEC05dEAbfAc040ce3619d7CF0B
gasUsed              21000
logs                 []
logsBloom            0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
root                 
status               1 (success)
transactionHash      0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913
transactionIndex     0
type                 0
blobGasPrice         
blobGasUsed          
to                   0x228466F2C715CbEC05dEAbfAc040ce3619d7CF0B
```

### 查询特定以太坊交易的 `gasPrice`（Gas 单价）

```bash
cast tx 0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913 gasPrice
1361240798
```

### 查询交易 **实际生效的 Gas 单价（effectiveGasPrice）**

```bash
cast receipt 0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913 effectiveGasPrice
1361240798
```

### 获取交易 `0xa279` 的完整 JSON 格式数据

```bash
cast tx 0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913 --json
{"type":"0x0","chainId":"0xe705","nonce":"0x82fc45","gasPrice":"0x5122e2de","gas":"0x5208","to":"0x228466f2c715cbec05deabfac040ce3619d7cf0b","value":"0x64","input":"0x","r":"0x43f3d959986d09dddff767cbfcc4a49641c04494533a78f40385106240de571a","s":"0xcb55a2d14a186798c5f71432e66d6be8d1b9c3ce7ddb2ab8e30703147bb9850","v":"0x1ce2e","hash":"0xa2797bab1ce8307072cff59ac03c3aef8614e0b9df491d253ea3de954df5f913","blockHash":"0x09d4ff5bcf56f63f8fdd6b15b1f33f187ff7b1d9df6ff4bb6e6e294a7830328f","blockNumber":"0x950760","transactionIndex":"0x0","from":"0x228466f2c715cbec05deabfac040ce3619d7cf0b"}
```

### 获取交易 `0x847e` 的完整 JSON 数据并使用 `jq` 格式化输出

```bash
cast tx 0x847ec42998f5b0fb603ff909b9c9dc575d6876a89c8f30ce9343ce8062d76e88 --json | jq
{
  "type": "0x2",
  "chainId": "0x1",
  "nonce": "0x51177",
  "gas": "0x3bd5a",
  "maxFeePerGas": "0x24ca2ff8",
  "maxPriorityFeePerGas": "0x1d3ed",
  "to": "0x68d3a973e7272eb388022a5c6518d9b2a2e66fbf",
  "value": "0x14e6235",
  "accessList": [],
  "input": "0xa00000000000000000000000000000006ac6b053a2858bea8ad758db680198c16e523184000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000040ba76f00000000000000000000000000000000000000000000000cbf18a462d9f5c1d1000000000000000000000000a0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
  "r": "0x7046b9d4144e7e031f14d1cf48e624ca082a647a1dff2a4b138946c5bf604813",
  "s": "0xe6de3584212a453feab79484cf04c4e7737b128c351e97febdfea7f28b91278",
  "yParity": "0x0",
  "v": "0x0",
  "hash": "0x847ec42998f5b0fb603ff909b9c9dc575d6876a89c8f30ce9343ce8062d76e88",
  "blockHash": "0xa742ebfe24abfe4b611a6d3152a15c7fd6e3eb5dbd85bf91d90ddc9579e17954",
  "blockNumber": "0x14e6235",
  "transactionIndex": "0xa5",
  "from": "0x448166a91e7bc50d0ac720c2fbed29e0963f5af8",
  "gasPrice": "0x24ca2ff8"
}
```

### 获取交易 `0x847e` 的 **input 数据**（即合约调用的原始 ABI 编码数据）

```bash
cast tx 0x847ec42998f5b0fb603ff909b9c9dc575d6876a89c8f30ce9343ce8062d76e88 input
0xa00000000000000000000000000000006ac6b053a2858bea8ad758db680198c16e523184000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000040ba76f00000000000000000000000000000000000000000000000cbf18a462d9f5c1d1000000000000000000000000a0b86991c6218b36c1d19d4a2e9eb0ce3606eb48
```

### 解析并美化显示给定的 calldata

```bash
cast pretty-calldata 0xa9059cbb0000000000000000000000005494befe3ce72a2ca0001fe0ed0c55b42f8c358f000000000000000000000000000000000000000000000000000000000836d54c

 Possible methods:
 - transfer(address,uint256)
 ------------
 [000]: 0000000000000000000000005494befe3ce72a2ca0001fe0ed0c55b42f8c358f
 [020]: 000000000000000000000000000000000000000000000000000000000836d54c
```

### 解码给定的 calldata

```bash
cast 4byte-decode 0xa9059cbb0000000000000000000000005494befe3ce72a2ca0001fe0ed0c55b42f8c358f000000000000000000000000000000000000000000000000000000000836d54c
1) "transfer(address,uint256)"
0x5494befe3CE72A2CA0001fE0Ed0C55B42F8c358f
137811276 [1.378e8]
```

### 计算 `transferFrom(address,address,uint256)` 函数的 Keccak-256 哈希（即函数选择器）

```bash
cast keccak 'transferFrom(address,address,uint256)'
0x23b872dd7302113369cda2901243429419bec145408fa8b352b3dd92b66c680b
```

### 获取 `transferFrom(address,address,uint256)` 函数的 **4字节函数选择器**（function selector）

```bash
cast sig "transferFrom(address,address,uint256)"
0x23b872dd
```

### 查询交易 `0x2258` 的收据信息

```bash
cast receipt 0x225853c513c75a4276979841a480ad1856bc649c496c16765107cb5d62d1def7

blockHash            0x3da09c83e6beee5b50540a0fd330a1b3c5f7528bc8f5be43ef1c7fd6df6851fa
blockNumber          21915751
contractAddress      
cumulativeGasUsed    6566577
effectiveGasPrice    2679745233
from                 0x28C6c06298d514Db089934071355E5743bf21d60
gasUsed              62272
logs                 [{"address":"0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48","topics":["0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef","0x00000000000000000000000028c6c06298d514db089934071355e5743bf21d60","0x000000000000000000000000c1af05a7a4f27cd1b61de823ea31ab5049b38ea8"],"data":"0x0000000000000000000000000000000000000000000000000000000de5eaacc5","blockHash":"0x3da09c83e6beee5b50540a0fd330a1b3c5f7528bc8f5be43ef1c7fd6df6851fa","blockNumber":"0x14e6867","blockTimestamp":"0x67bc518f","transactionHash":"0x225853c513c75a4276979841a480ad1856bc649c496c16765107cb5d62d1def7","transactionIndex":"0x15","logIndex":"0xc3","removed":false}]
logsBloom            0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000800000000000000a000008000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000010000000000000000000000000000000000000000000000000010008000000000000000000000000000000200000000000000000000000000000000000000000020000000000000002000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
root                 
status               1 (success)
transactionHash      0x225853c513c75a4276979841a480ad1856bc649c496c16765107cb5d62d1def7
transactionIndex     21
type                 2
blobGasPrice         
blobGasUsed          
to                   0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
```

### 获取交易 `0x2258` 的事件日志（logs）

```bash
cast receipt 0x225853c513c75a4276979841a480ad1856bc649c496c16765107cb5d62d1def7 logs
[
 
 address: 0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
 blockHash: 0x3da09c83e6beee5b50540a0fd330a1b3c5f7528bc8f5be43ef1c7fd6df6851fa
 blockNumber: 21915751
 data: 0x0000000000000000000000000000000000000000000000000000000de5eaacc5
 logIndex: 195
 removed: false
 topics: [
  0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef
  0x00000000000000000000000028c6c06298d514db089934071355e5743bf21d60
  0x000000000000000000000000c1af05a7a4f27cd1b61de823ea31ab5049b38ea8
 ]
 transactionHash: 0x225853c513c75a4276979841a480ad1856bc649c496c16765107cb5d62d1def7
 transactionIndex: 21
]
```

### 解码事件签名哈希

```bash
# cast 4byte-event
cast 4byte-event 0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef
Transfer(address,address,uint256)
```

### **调试/模拟执行**交易

```bash
# Run 调用栈 blocksec tenderly
cast run 0x225853c513c75a4276979841a480ad1856bc649c496c16765107cb5d62d1def7
Executing previous transactions from the block.
Traces:
  [40652] 0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48::transfer(0xc1AF05a7A4F27Cd1b61De823EA31aB5049b38eA8, 59691936965 [5.969e10])
    ├─ [33363] 0x43506849D7C04F9138D1A2050bbF3A0c054402dd::transfer(0xc1AF05a7A4F27Cd1b61De823EA31aB5049b38eA8, 59691936965 [5.969e10]) [delegatecall]
    │   ├─ emit Transfer(param0: 0x28C6c06298d514Db089934071355E5743bf21d60, param1: 0xc1AF05a7A4F27Cd1b61De823EA31aB5049b38eA8, param2: 59691936965 [5.969e10])
    │   └─ ← [Return] 0x0000000000000000000000000000000000000000000000000000000000000001
    └─ ← [Return] 0x0000000000000000000000000000000000000000000000000000000000000001


Transaction successfully executed.
Gas used: 62272
```

## 总结

Foundry 的 cast 命令为 Web3 开发者提供了从查询到调试的全面工具支持，显著提升了以太坊区块链开发的效率。本文通过实战案例展示了其在区块查询、交易分析和事件日志解析中的实用功能，为开发者深入理解智能合约提供了清晰指引。在 Web3 开发的大潮中，掌握 Foundry 将助你事半功倍。快来结合参考资源动手实践，开启你的 Web3 开发新篇章！

## 参考

- <https://www.youtube.com/watch?v=EXYeltwvftw&t=212s>
- <https://soliditylang.org/>
- <https://solidity-by-example.org/>
- <https://docs.soliditylang.org/en/latest/>
- <https://book.getfoundry.sh/>

# 2025-08-12

# Web3实战：打造属于你的NFT数字资产

Web3时代，NFT（非同质化代币）正重塑数字所有权的未来。无论是独一无二的艺术品还是虚拟资产，ERC721标准让你轻松实现NFT的创建与管理。本文通过一个完整的实战案例，带你深入Solidity智能合约开发，快速部署属于你的NFT代币，解锁Web3开发的无限可能。准备好加入这场数字资产革命了吗？

本文通过一个基于OpenZeppelin的ERC721智能合约MyERC721Token，展示了NFT开发的完整流程，包括合约编写、部署脚本、Sepolia测试网部署及验证。合约支持URI存储、可销毁、投票和EIP712签名等功能，适合Web3开发者快速上手。文章还涵盖部署问题优化与实用建议，为打造个性化NFT项目提供清晰指引。

## MyERC721Token 实操

### 合约代码

```ts
// SPDX-License-Identifier: MIT
// Compatible with OpenZeppelin Contracts ^5.0.0
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Votes.sol";

contract MyERC721Token is ERC721, ERC721URIStorage, ERC721Burnable, Ownable, EIP712, ERC721Votes {
    uint256 private _nextTokenId;

    constructor(address initialOwner)
        ERC721("MyERC721Token", "MTK721")
        Ownable(initialOwner)
        EIP712("MyERC721Token", "1")
    {}

    function safeMint(address to, string memory uri) public onlyOwner {
        uint256 tokenId = _nextTokenId++;
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, uri);
    }

    // The following functions are overrides required by Solidity.

    function _update(address to, uint256 tokenId, address auth)
        internal
        override(ERC721, ERC721Votes)
        returns (address)
    {
        return super._update(to, tokenId, auth);
    }

    function _increaseBalance(address account, uint128 value) internal override(ERC721, ERC721Votes) {
        super._increaseBalance(account, value);
    }

    function tokenURI(uint256 tokenId) public view override(ERC721, ERC721URIStorage) returns (string memory) {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId) public view override(ERC721, ERC721URIStorage) returns (bool) {
        return super.supportsInterface(interfaceId);
    }
}

```

### MyERC721Token 智能合约代码的解释

1. 头部和许可证

```solidity
// SPDX-License-Identifier: MIT
// Compatible with OpenZeppelin Contracts ^5.0.0
pragma solidity ^0.8.20;
```

- **SPDX-License-Identifier: MIT**：声明代码采用MIT许可证，允许开源使用，几乎无限制，便于社区共享和复用。
- **Compatible with OpenZeppelin Contracts ^5.0.0**：表明合约与OpenZeppelin合约库5.0.0及以上版本兼容，确保依赖的库功能一致。
- **pragma solidity ^0.8.20**：指定Solidity编译器版本为0.8.20或兼容版本，^允许小版本更新，以保证安全性和新特性支持。

2. 导入库

```ts
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Votes.sol";
```

这些行导入了OpenZeppelin的标准化、经过审计的合约库，为NFT合约扩展功能：

- **ERC721.sol**：ERC721标准的实现，提供NFT核心功能，如所有权管理、转移等。
- **ERC721URIStorage.sol**：扩展功能，用于存储每个NFT的元数据URI（如指向JSON文件的链接，包含图片、描述等）。
- **ERC721Burnable.sol**：添加“销毁”功能，允许永久删除NFT，减少流通量。
- **Ownable.sol**：实现访问控制，限制特定功能（如铸造）仅限合约拥有者调用。
- **EIP712.sol**：支持EIP-712标准，用于结构化数据签名，可实现无gas交易（如签名授权）。
- **ERC721Votes.sol**：为NFT添加治理功能，允许NFT持有者参与投票（如`DAO`治理）。

3. 合约定义和继承

```ts
contract MyERC721Token is ERC721, ERC721URIStorage, ERC721Burnable, Ownable, EIP712, ERC721Votes {
```

- 定义合约名为 MyERC721Token，通过多重继承整合多个OpenZeppelin模块：
  - ERC721：提供NFT基本功能。
  - ERC721URIStorage：支持存储和查询NFT元数据。
  - ERC721Burnable：支持销毁NFT。
  - Ownable：限制功能访问，仅限合约拥有者。
  - EIP712：支持签名验证。
  - ERC721Votes：支持NFT用于治理投票。
- 这种模块化继承方式利用OpenZeppelin的标准化代码，减少开发时间并提高安全性。

4. **状态变量**

```ts
uint256 private _nextTokenId;
```

- _nextTokenId 是一个私有变量，用于跟踪下一个可用的NFT代币ID。
- 每次铸造新NFT时，_nextTokenId 自增，确保每个NFT有唯一ID。

5. 构造函数

```ts
constructor(address initialOwner)
    ERC721("MyERC721Token", "MTK721")
    Ownable(initialOwner)
    EIP712("MyERC721Token", "1")
{}
```

- **作用**：初始化合约，设置NFT名称、符号、拥有者和EIP-712参数。
- **参数**：
  - initialOwner：指定合约的初始拥有者地址，拥有特殊权限（如铸造NFT）。
- **初始化**：
  - ERC721("MyERC721Token", "MTK721")：设置NFT名称为“MyERC721Token”，符号为“MTK721”（类似代币的简写）。
  - Ownable(initialOwner)：将合约所有权分配给 initialOwner。
  - EIP712("MyERC721Token", "1")：初始化EIP-712签名域，名称为“MyERC721Token”，版本为“1”，用于签名验证。
- **空函数体**：{} 表示构造函数仅执行初始化，无额外逻辑。

6. 铸造函数

```ts
function safeMint(address to, string memory uri) public onlyOwner {
    uint256 tokenId = _nextTokenId++;
    _safeMint(to, tokenId);
    _setTokenURI(tokenId, uri);
}
```

- **作用**：铸造新的NFT并分配给指定地址，同时设置元数据URI。
- **参数**：
  - to：接收NFT的地址。
  - uri：NFT的元数据链接（通常指向存储图片、名称等信息的JSON文件）。
- **修饰符**：
  - onlyOwner：限制仅合约拥有者可调用此函数（来自 Ownable 模块）。
- **逻辑**：
  - uint256 tokenId = _nextTokenId++：获取当前_nextTokenId 作为新NFT的ID，并自增。
  - _safeMint(to, tokenId)：调用ERC721的_safeMint 函数，铸造NFT并分配给 to（安全检查接收者是否支持ERC721）。
  - _setTokenURI(tokenId, uri)：设置NFT的元数据URI，存储在 ERC721URIStorage 中。
- **用途**：这是创建NFT的核心功能，允许拥有者铸造新代币并关联元数据。

7. 重写函数

以下函数是Solidity要求的重写，用于解决多重继承中的冲突，确保功能正确实现：

7.1 **更新函数**

```ts
function _update(address to, uint256 tokenId, address auth)
    internal
    override(ERC721, ERC721Votes)
    returns (address)
{
    return super._update(to, tokenId, auth);
}
```

- **作用**：处理NFT的转移或销毁逻辑，需同时兼容 ERC721 和 ERC721Votes。
- **参数**：
  - to：NFT转移的目标地址。
  - tokenId：NFT的ID。
  - auth：授权地址（通常为调用者）。
- **重写**：override(ERC721, ERC721Votes) 表示重写两个父合约的 _update 函数，解决继承冲突。
- **逻辑**：调用父类的 _update 方法，保持默认行为，同时更新投票权重（用于治理）。

7.2 **余额更新函数**

```ts
function _increaseBalance(address account, uint128 value) internal override(ERC721, ERC721Votes) {
    super._increaseBalance(account, value);
}
```

- **作用**：更新账户的NFT余额，兼容 `ERC721` 和 `ERC721Votes`。
- **参数**：
  - account：账户地址。
  - value：增加的余额（通常为1，表示一个NFT）。
- **重写**：解决多重继承冲突，调用父类的 _increaseBalance 方法，确保余额和投票权重同步更新。

7.3 **元数据查询函数**

```ts
function tokenURI(uint256 tokenId) public view override(ERC721, ERC721URIStorage) returns (string memory) {
    return super.tokenURI(tokenId);
}
```

- **作用**：返回指定NFT的元数据URI。
- **参数**：
  - tokenId：NFT的ID。
- **重写**：兼容 ERC721 和 ERC721URIStorage，优先使用 ERC721URIStorage 的实现（支持存储URI）。
- **逻辑**：调用父类的 tokenURI 方法，返回存储的URI。

7.4 **接口支持函数**

```solidity
function supportsInterface(bytes4 interfaceId) public view override(ERC721, ERC721URIStorage) returns (bool) {
    return super.supportsInterface(interfaceId);
}
```

- **作用**：检查合约是否支持特定接口（如ERC721或扩展接口）。
- **参数**：
  - interfaceId：接口的标识符（基于ERC-165标准）。
- **重写**：兼容 ERC721 和 `ERC721URIStorage`，确保正确报告支持的接口。
- **逻辑**：调用父类的 `supportsInterface` 方法，返回是否支持指定接口。

8. 功能总结

- **核心功能**：
  - 铸造NFT（safeMint）：仅限拥有者铸造，设置元数据URI。
  - 元数据管理：通过 `ERC721URIStorage` 存储和查询NFT元数据。
  - 销毁NFT：通过 `ERC721Burnable` 支持销毁功能。
  - 访问控制：通过 `Ownable` 限制关键操作。
  - 治理支持：通过 `ERC721Votes` 允许NFT用于投票。
  - 签名支持：通过 `EIP712` 启用结构化数据签名。
- **设计亮点**：
  - 使用`OpenZeppelin`的模块化库，代码安全且易于扩展。
  - 支持多种高级功能（如投票、签名），适用于复杂NFT项目。
  - 通过重写函数解决多重继承冲突，保证功能一致性。

9. **潜在用例**

- **NFT市场**：可用于数字艺术、收藏品或游戏资产的NFT创建。
- **去中心化治理**：NFT持有者可通过 `ERC721Votes` 参与DAO投票。
- **元数据管理**：通过URI存储NFT的动态内容（如图片、属性）。
- **签名授权**：支持EIP-712的签名机制，可实现无gas交易或授权。

10. **注意事项**

- **权限管理**：onlyOwner 限制了 safeMint，需确保拥有者地址安全。
- **Gas成本**：多重继承和扩展功能可能增加部署和调用成本，需优化。
- **元数据存储**：URI通常指向IPFS或中心化服务器，需保证链接的持久性。
- **测试网验证**：如文章所示，合约已在Sepolia测试网部署，建议在主网部署前充分测试。

MyERC721Token 是一个功能全面的ERC721 NFT智能合约，集成了铸造、销毁、元数据管理、治理投票和签名验证等功能。通过OpenZeppelin的标准化库，代码安全可靠，适合Web3开发者快速构建NFT项目。理解其结构和逻辑，有助于深入掌握Solidity开发和Web3生态的NFT应用。

### 部署脚本

```ts
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Script, console} from "forge-std/Script.sol";
import {MyERC721Token} from "../src/MyERC721Token.sol";

contract MyERC721TokenScript is Script {
    MyERC721Token public my721token;

    function setUp() public {}

    function run() public {
        vm.startBroadcast();

        my721token = new MyERC721Token(msg.sender);
        console.log("MyERC721Token deployed to:", address(my721token));

        vm.stopBroadcast();
    }
}

```

### 部署合约

```bash
NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base took 1m 18.6s
➜ source .env

NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base
➜ forge script --chain sepolia script/MyERC721Token.s.sol:MyERC721TokenScript --rpc-url $SEPOLIA_RPC_URL --broadcast --account MetaMask --verify -vvvv

[⠊] Compiling...
[⠔] Compiling 1 files with Solc 0.8.20
[⠒] Solc 0.8.20 finished in 1.44s
Compiler run successful!
Enter keystore password:
Traces:
  [2119248] MyERC721TokenScript::run()
    ├─ [0] VM::startBroadcast()
    │   └─ ← [Return]
    ├─ [2072840] → new MyERC721Token@0xC39B0eE94143C457449e16829837FD59d722933C
    │   ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: DefaultSender: [0x1804c8AB1F12E6bbf3894d4083f33e07309d1f38])
    │   └─ ← [Return] 10002 bytes of code
    ├─ [0] console::log("MyERC721Token deployed to:", MyERC721Token: [0xC39B0eE94143C457449e16829837FD59d722933C]) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::stopBroadcast()
    │   └─ ← [Return]
    └─ ← [Stop]


Script ran successfully.

== Logs ==
  MyERC721Token deployed to: 0xC39B0eE94143C457449e16829837FD59d722933C

## Setting up 1 EVM.
==========================
Simulated On-chain Traces:

  [2072840] → new MyERC721Token@0xC39B0eE94143C457449e16829837FD59d722933C
    ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: DefaultSender: [0x1804c8AB1F12E6bbf3894d4083f33e07309d1f38])
    └─ ← [Return] 10002 bytes of code


==========================

Chain 11155111

Estimated gas price: 10.85383427 gwei

Estimated total gas used for script: 2990587

Estimated amount required: 0.03245933566801649 ETH

==========================

##### sepolia
✅  [Success]Hash: 0x8e5b0e3a9df4e5231b88d28af9c0e6e903bf7afac027a2ee54bf5faaf67b40c0
Contract Address: 0xC39B0eE94143C457449e16829837FD59d722933C
Block: 6326900
Paid: 0.012441733790006772 ETH (2301162 gas * 5.406717906 gwei)

✅ Sequence #1 on sepolia | Total Paid: 0.012441733790006772 ETH (2301162 gas * avg 5.406717906 gwei)


==========================

ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
##
Start verification for (1) contracts
Start verifying contract `0xC39B0eE94143C457449e16829837FD59d722933C` deployed on sepolia

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0xC39B0eE94143C457449e16829837FD59d722933C.

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0xC39B0eE94143C457449e16829837FD59d722933C.

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0xC39B0eE94143C457449e16829837FD59d722933C.
Submitted contract for verification:
        Response: `OK`
        GUID: `q1v8v6kswcqvnzfdifksth4hdk1ss7ukejzxxfuktumivdrr5e`
        URL: https://sepolia.etherscan.io/address/0xc39b0ee94143c457449e16829837fd59d722933c
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
All (1) contracts were verified!

Transactions saved to: /Users/qiaopengjun/Code/solidity-code/NFTMarketHub/broadcast/MyERC721Token.s.sol/11155111/run-latest.json

Sensitive values saved to: /Users/qiaopengjun/Code/solidity-code/NFTMarketHub/cache/MyERC721Token.s.sol/11155111/run-latest.json


```

### 浏览器查看合约

<https://sepolia.etherscan.io/address/0xc39b0ee94143c457449e16829837fd59d722933c>


### 部署问题修改

```bash
NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base
➜ source .env

NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base
➜ forge script --chain sepolia MyERC721TokenScript --rpc-url $SEPOLIA_RPC_URL --broadcast --verify -vvvv

[⠊] Compiling...
[⠔] Compiling 1 files with Solc 0.8.20
[⠒] Solc 0.8.20 finished in 1.46s
Compiler run successful!
Traces:
  [2120084] MyERC721TokenScript::run()
    ├─ [0] VM::envUint("PRIVATE_KEY") [staticcall]
    │   └─ ← [Return] <env var value>
    ├─ [0] VM::envAddress("ACCOUNT_ADDRESS") [staticcall]
    │   └─ ← [Return] <env var value>
    ├─ [0] VM::startBroadcast(<pk>)
    │   └─ ← [Return]
    ├─ [2072840] → new MyERC721Token@0x7eA36391c7127A7f40E5c23212A8016d6E494546
    │   ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: 0x750Ea21c1e98CcED0d4557196B6f4a5974CCB6f5)
    │   └─ ← [Return] 10002 bytes of code
    ├─ [0] console::log("MyERC721Token deployed to:", MyERC721Token: [0x7eA36391c7127A7f40E5c23212A8016d6E494546]) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::stopBroadcast()
    │   └─ ← [Return]
    └─ ← [Stop]


Script ran successfully.

== Logs ==
  MyERC721Token deployed to: 0x7eA36391c7127A7f40E5c23212A8016d6E494546

## Setting up 1 EVM.
==========================
Simulated On-chain Traces:

  [2072840] → new MyERC721Token@0x7eA36391c7127A7f40E5c23212A8016d6E494546
    ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: 0x750Ea21c1e98CcED0d4557196B6f4a5974CCB6f5)
    └─ ← [Return] 10002 bytes of code


==========================

Chain 11155111

Estimated gas price: 53.318074191 gwei

Estimated total gas used for script: 2990587

Estimated amount required: 0.159452339540640117 ETH

==========================

##### sepolia
✅  [Success]Hash: 0x77190a6bbe59f4b98dc06b1219ca34fcf5cc1ace40f6998bb26568fcb93e5380
Contract Address: 0x7eA36391c7127A7f40E5c23212A8016d6E494546
Block: 6355525
Paid: 0.057633696474121704 ETH (2301162 gas * 25.045475492 gwei)

✅ Sequence #1 on sepolia | Total Paid: 0.057633696474121704 ETH (2301162 gas * avg 25.045475492 gwei)


==========================

ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
##
Start verification for (1) contracts
Start verifying contract `0x7eA36391c7127A7f40E5c23212A8016d6E494546` deployed on sepolia

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0x7eA36391c7127A7f40E5c23212A8016d6E494546.

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0x7eA36391c7127A7f40E5c23212A8016d6E494546.

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0x7eA36391c7127A7f40E5c23212A8016d6E494546.
Submitted contract for verification:
        Response: `OK`
        GUID: `bgjivlpsttkmre2wq8etbj9gbv6txntfhwwe3rnh3zzduq12pm`
        URL: https://sepolia.etherscan.io/address/0x7ea36391c7127a7f40e5c23212a8016d6e494546
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
All (1) contracts were verified!

Transactions saved to: /Users/qiaopengjun/Code/solidity-code/NFTMarketHub/broadcast/MyERC721Token.s.sol/11155111/run-latest.json

Sensitive values saved to: /Users/qiaopengjun/Code/solidity-code/NFTMarketHub/cache/MyERC721Token.s.sol/11155111/run-latest.json


NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base took 1m 27.4s
➜
```

### 浏览器查看

<https://sepolia.etherscan.io/address/0x7ea36391c7127a7f40e5c23212a8016d6e494546#code>


## 总结

通过本次实战，我们成功开发并部署了一个功能强大的ERC721 NFT合约MyERC721Token，在Sepolia测试网上实现数字资产的创建与管理。结合OpenZeppelin的标准化工具和Foundry的部署流程，Web3开发者可以高效构建安全可靠的NFT项目。未来，你可以扩展合约功能，接入NFT市场或探索跨链应用，在Web3的浪潮中打造属于自己的数字资产帝国。

## 参考

- <https://eips.ethereum.org/EIPS/eip-712>
- <https://eips.ethereum.org/EIPS/eip-2612>
- <https://www.openzeppelin.com/contracts>
- <https://github.com/AmazingAng/WTF-Solidity/blob/main/37_Signature/readme.md>
- <https://github.com/AmazingAng/WTF-Solidity/blob/main/52_EIP712/readme.md>
- <https://github.com/jesperkristensen58/ERC712-Permit-Example>
- <https://book.getfoundry.sh/tutorials/testing-eip712>

# 2025-08-11

# solidity智能合约的 pure 与 view 使用原理及场景
## pure 与 view 原理
pure：不读取更不修改区块上的变量，使用本机的CPU资源计算我们的函数。所以不消耗任何的资源这是很容易的理解的。

view: 但是 view 既然要读取区块链上的值，为什么也不用消耗 gas 呢？

其实很简单，因为作为一个全节点来说，会同步保存所有的信息，保存在本地中。

那么我们要查看区块链上的资源，同样可以直接在一个全节点之上查询数据即可。

我不需要全世界的节点都知道。都去同时的处理这笔事务。我也不需要将调用这笔函数的信息记录在区块链上。

所以 view 仍然不消耗 gas。

view: 可以自由调用，因为它只是“查看”区块链的状态而不改变它

pure: 也可以自由调用，既不读取也不写入区块链

# 2025-08-10

# Solana 开发学习之Solana 基础知识

## Install the Solana CLI

### 相关链接

- <https://docs.solanalabs.com/cli/install>
- <https://solanacookbook.com/zh/getting-started/installation.html#%E5%AE%89%E8%A3%85%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7>
- <https://www.solanazh.com/course/1-4>
- <https://solana.com/zh/developers/guides/getstarted/setup-local-development>

### 实操

- 安装

```bash
sh -c "$(curl -sSfL https://release.solana.com/v1.18.2/install)"

downloading v1.18.2 installer
  ✨ 1.18.2 initialized
Adding
export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH" to /Users/qiaopengjun/.profile
Adding
export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH" to /Users/qiaopengjun/.zprofile
Adding
export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH" to /Users/qiaopengjun/.bash_profile

Close and reopen your terminal to apply the PATH changes or run the following in your existing bash:

export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH"

```

- 配置环境变量

```bash
vim .zshrc

# 复制并粘贴下面命令以更新 PATH
export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH"
```

- 通过运行以下命令确认您已安装了所需的 Solana 版本：

```bash
solana --version

# 实操
solana --version
solana-cli 1.18.2 (src:13656e30; feat:3352961542, client:SolanaLabs)
```

- 切换版本

```bash
solana-install init 1.16.4
```

# 2025-08-09

# Web3学习之DAPP开发流程与架构

集中化帮助数十亿人接入万维网，并创建了万维网赖以生存的稳定、强大的基础设施。与此同时，少数中心化实体在万维网的大片地区拥有据点，单方面决定什么应该被允许，什么不应该被允许。

Web3 就是这个困境的答案。 Web3 不是由大型科技公司垄断的 Web，而是拥抱去中心化，并由其用户构建、运营和拥有。 Web3 将权力交给个人而不是公司。在讨论 Web3 之前，让我们先探讨一下我们是如何走到这一步的。

## What is Web3?

Web3 has become a catch-all term for the vision of a new, better internet. At its core, Web3 uses blockchains, cryptocurrencies, and NFTs to give power back to the users in the form of ownership. [A 2020 post on Twitter(opens in a new tab)](https://twitter.com/himgajria/status/1266415636789334016) said it best: Web1 was read-only, Web2 is read-write, Web3 will be read-write-own.

## Web 进化过程

Web 的进化过程可以分为几个关键阶段：

1. **Web 1.0 (静态页面)**:
   - Web 1.0 是互联网的起步阶段，网页内容主要是静态的，用户主要是被动消费信息。

2. **Web 2.0 (互动和用户生成内容)**:
   - Web 2.0 的到来带来了互动和用户生成内容的概念。网站变得更加动态，用户可以参与内容的创建和分享，社交媒体、博客和在线社区开始兴起。

3. **移动优先和响应式设计**:
   - 随着移动设备的普及，Web 开发者开始采用响应式设计来确保网站在各种设备上都能良好展示和操作。

4. **Web 应用程序的兴起**:
   - 随着浏览器和技术的发展，Web 应用程序开始变得越来越强大和复杂。单页面应用程序 (SPA)、前端框架和库如React、Angular和Vue.js等的兴起推动了Web 应用的发展。

5. **增强现实和虚拟现实**:
   - 最近的发展趋势包括增强现实 (AR) 和虚拟现实 (VR)，这些技术在Web上也有了应用。WebXR API的引入使得开发者可以创建支持AR和VR体验的Web内容。

6. **Web3.0 和去中心化应用 (DApps)**:
   - Web3.0 是一个正在发展的概念，它致力于推动互联网的下一个阶段，主要关注去中心化、区块链和加密货币。去中心化应用 (DApps) 的兴起成为Web3.0的重要组成部分。

这些阶段展示了Web技术如何从静态页面发展到支持复杂的Web应用和未来的技术趋势。

#### 总结

### Evolution of the Web

| Era     | Time Period   | Features                                                     |
| ------- | ------------- | ------------------------------------------------------------ |
| Web 1.0 | 1990-2004     | Read-only                                                    |
| Web 2.0 | 2004-present  | Read (consumption), Write (user-generated content)           |
| Web 3.0 | 2014-present? | Read (consumption), Write (contributions to decentralized platforms), Own (digital assets) |

## Web3 核心

Web3 核心是通过区块链、加密货币、NFT将所有权归还用户。也就是说，Web3 核心是利用区块链技术和加密货币，通过智能合约和去中心化应用（DApps），实现用户对数字资产的所有权和控制权，推动互联网从中心化向去中心化的转变。

## 参考

- <https://zh.wikipedia.org/wiki/Web3>
- <https://ethereum.org/en/web3/>

# 2025-08-08

# 部署 NFT 合约

## 实操

### 部署脚本 
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

import {Script, console} from "forge-std/Script.sol";
import {stdJson} from "forge-std/StdJson.sol";
import {MFNFT} from "../src/MFNFT.sol";

contract MFNFTScript is Script {
    function setUp() public {}

    function run() public {
        uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
        address deployerAddress = vm.addr(deployerPrivateKey);
        console.log("Deployer Address:", deployerAddress);

        vm.startBroadcast(deployerPrivateKey);

        // 部署 MFNFT 合约
        // 使用部署者地址作为初始 signer，后续可以通过 setSigner 更改
        MFNFT mfnft = new MFNFT(deployerAddress);
        console.log("MFNFT deployed to:", address(mfnft));
        console.log("Initial signer set to:", deployerAddress);

        vm.stopBroadcast();

        // 部署成功后保存信息
        saveDeploymentInfo(deployerAddress, address(mfnft));
    }

    function saveDeploymentInfo(
        address deployerAddress,
        address mfnftAddress
    ) internal {
        string memory path = "./deployments/MFNFT.json";

        // 1. 为我们的 JSON 对象定义一个唯一的标识符（root key）
        string memory rootKey = "mfnftDeploymentInfo";
        string memory finalJson;

        // 2. 告诉 Foundry 在名为 rootKey 的对象上进行所有操作
        // 每一次 serialize 调用都会返回完整的、更新后的 JSON 字符串。
        vm.serializeAddress(rootKey, ".deploy.mfnftAddress", mfnftAddress);
        vm.serializeUint(rootKey, ".deploy.chainId", block.chainid);
        // 3. 我们只需要捕获最后一次调用的返回值即可。
        finalJson = vm.serializeAddress(
            rootKey,
            ".deploy.deployerAddress",
            deployerAddress
        );

        // 4. 将完整的 JSON 写入文件
        vm.writeJson(finalJson, path);
        console.log("Deployment info saved to: %s", path);
    }
}

/*
== Logs ==
  Deployer Address: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
  MFNFT deployed to: 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
  Initial signer set to: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
  Deployment info saved to: ./deployments/MFNFT.json

  https://sepolia.etherscan.io/address/0xe9d6e87644e4498f5d94391b6f4553d3a58d6198#code





  YuanqiGenesis on  master [✘!+?] on 🐳 v28.2.2 (orbstack) took 18.4s
➜ make deploy-sepolia CONTRACT=MFNFT
Cleaning and building all contracts...
[⠊] Compiling...
[⠑] Compiling 92 files with Solc 0.8.30
[⠢] Solc 0.8.30 finished in 9.96s
Compiler run successful!
Deploying MFNFT to Sepolia testnet and verifying...
[⠒] Compiling...
No files changed, compilation skipped
Traces:
  [132] MFNFTScript::setUp()
    └─ ← [Stop]

  [1650691] MFNFTScript::run()
    ├─ [0] VM::envUint("PRIVATE_KEY") [staticcall]
    │   └─ ← [Return] <env var value>
    ├─ [0] VM::addr(<pk>) [staticcall]
    │   └─ ← [Return] 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
    ├─ [0] console::log("Deployer Address:", 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::startBroadcast(<pk>)
    │   └─ ← [Return]
    ├─ [1602470] → new MFNFT@0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
    │   ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5)
    │   └─ ← [Return] 7525 bytes of code
    ├─ [0] console::log("MFNFT deployed to:", MFNFT: [0xE9d6E87644e4498f5d94391b6F4553D3a58d6198]) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] console::log("Initial signer set to:", 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::stopBroadcast()
    │   └─ ← [Return]
    ├─ [0] VM::serializeAddress("mfnftDeploymentInfo", ".deploy.mfnftAddress", MFNFT: [0xE9d6E87644e4498f5d94391b6F4553D3a58d6198])
    │   └─ ← [Return] "{\".deploy.mfnftAddress\":\"0xE9d6E87644e4498f5d94391b6F4553D3a58d6198\"}"
    ├─ [0] VM::serializeUint("mfnftDeploymentInfo", ".deploy.chainId", 11155111 [1.115e7])
    │   └─ ← [Return] "{\".deploy.chainId\":11155111,\".deploy.mfnftAddress\":\"0xE9d6E87644e4498f5d94391b6F4553D3a58d6198\"}"
    ├─ [0] VM::serializeAddress("mfnftDeploymentInfo", ".deploy.deployerAddress", 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5)
    │   └─ ← [Return] "{\".deploy.chainId\":11155111,\".deploy.deployerAddress\":\"0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5\",\".deploy.mfnftAddress\":\"0xE9d6E87644e4498f5d94391b6F4553D3a58d6198\"}"
    ├─ [0] VM::writeJson("{\".deploy.chainId\":11155111,\".deploy.deployerAddress\":\"0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5\",\".deploy.mfnftAddress\":\"0xE9d6E87644e4498f5d94391b6F4553D3a58d6198\"}", "./deployments/MFNFT.json")
    │   └─ ← [Return]
    ├─ [0] console::log("Deployment info saved to: %s", "./deployments/MFNFT.json") [staticcall]
    │   └─ ← [Stop]
    └─ ← [Return]


Script ran successfully.

== Logs ==
  Deployer Address: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
  MFNFT deployed to: 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
  Initial signer set to: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
  Deployment info saved to: ./deployments/MFNFT.json

## Setting up 1 EVM.
==========================
Simulated On-chain Traces:

  [1602470] → new MFNFT@0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
    ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5)
    └─ ← [Return] 7525 bytes of code


==========================

Chain 11155111

Estimated gas price: 0.000610107 gwei

Estimated total gas used for script: 2330715

Estimated amount required: 0.000001421985536505 ETH

==========================

##### sepolia
✅  [Success] Hash: 0xfad2a81b872700d168b42a871656dce3d8a03ff83ef5ac25f5855112182aeabd
Contract Address: 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
Block: 8930361
Paid: 0.000001093731230042 ETH (1792858 gas * 0.000610049 gwei)

✅ Sequence #1 on sepolia | Total Paid: 0.000001093731230042 ETH (1792858 gas * avg 0.000610049 gwei)


==========================

ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
##
Start verification for (1) contracts
Start verifying contract `0xE9d6E87644e4498f5d94391b6F4553D3a58d6198` deployed on sepolia
EVM version: cancun
Compiler version: 0.8.30
Optimizations:    200
Constructor args: 000000000000000000000000e91e2df7ce50bca5310b7238f6b1dfcd15566be5

Submitting verification for [src/MFNFT.sol:MFNFT] 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (4 tries remaining)

Submitting verification for [src/MFNFT.sol:MFNFT] 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (3 tries remaining)

Submitting verification for [src/MFNFT.sol:MFNFT] 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198.
Submitted contract for verification:
        Response: `OK`
        GUID: `jpr5wsiyph1mmpsblsvdrwkd9nbsdncbljw1xfhai7fwund4us`
        URL: https://sepolia.etherscan.io/address/0xe9d6e87644e4498f5d94391b6f4553d3a58d6198
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Warning: Verification is still pending...; waiting 15 seconds before trying again (7 tries remaining)
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
All (1) contracts were verified!

Transactions saved to: /Users/qiaopengjun/Code/Solidity/YuanqiGenesis/YuanqiGenesis/broadcast/MFNFT.s.sol/11155111/run-latest.json

Sensitive values saved to: /Users/qiaopengjun/Code/Solidity/YuanqiGenesis/YuanqiGenesis/cache/MFNFT.s.sol/11155111/run-latest.json

Extracting MFNFT ABI...
ABI for MFNFT copied to abis/MFNFT.json

*/

```

# 2025-08-07

# Web3学习之ERC721

## ERC721

### 加密猫

<https://www.cryptokitties.co/>

加密猫（CryptoKitties）是一个建立在以太坊区块链上的区块链游戏和收藏品平台。它是最早将区块链技术应用于非同质化代币（NFT）的项目之一，并且对区块链游戏和NFT的普及起到了重要作用。

<https://etherscan.io/token/0x06012c8cf97bead5deae237070f9587f8e7a266d#code>

### 加密猫的关键特点

1. **NFT（非同质化代币）**：
   - 每只加密猫都是一个唯一的ERC-721代币，这意味着每只猫都有独特的属性和基因，无法被复制或替代。
   - NFT的特点使得每只猫在区块链上都是唯一的，这为数字收藏品提供了真正的稀缺性和所有权证明。

2. **繁殖和遗传**：
   - 玩家可以通过繁殖两只猫来生成新的小猫，每只小猫的基因是父母基因的组合，这使得新生的小猫具有独特的属性和外观。
   - 繁殖过程中的基因组合是随机的，这为游戏增加了趣味性和不确定性。

3. **市场交易**：
   - 玩家可以在市场上买卖加密猫，价格由市场需求决定。稀有和独特的猫通常会卖出高价。
   - 玩家也可以通过拍卖和直接交易等方式进行猫的买卖。

4. **区块链技术**：
   - 加密猫是建立在以太坊区块链上的，所有交易和所有权记录都是透明和不可篡改的。
   - 由于使用区块链技术，玩家可以放心地进行交易和收藏，因为他们的猫的所有权是安全和受保护的。

### 示例代码：创建简单的ERC-721代币

以下是一个简单的ERC-721代币合约示例，展示了如何创建一个基本的NFT合约：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract CryptoKitty is ERC721, Ownable {
    uint256 public nextTokenId;
    
    constructor() ERC721("CryptoKitty", "CK") {}

    function createKitty() external onlyOwner {
        _safeMint(msg.sender, nextTokenId);
        nextTokenId++;
    }

    function getKitty(uint256 tokenId) external view returns (address) {
        return ownerOf(tokenId);
    }
}
```

### 加密猫对区块链和NFT的影响

- **普及NFT概念**：加密猫是最早的成功NFT项目之一，使得NFT的概念被更多人理解和接受。
- **推动以太坊网络**：由于加密猫的流行，一度导致以太坊网络拥堵，这也促使开发者和社区更关注区块链的扩展性和性能问题。
- **激发更多创新**：加密猫的成功激发了许多类似的区块链游戏和NFT项目的出现，如CryptoPunks、Axie Infinity等。

### 结论

加密猫（CryptoKitties）不仅是一个有趣的区块链游戏，更是NFT和数字收藏品领域的重要推动力。通过将区块链技术应用于游戏和收藏品，它为整个行业提供了一个新的视角和可能性。

## ERC721标准

**<https://eips.ethereum.org/EIPS/eip-721>**

思考：从ERC20到ERC721的协议是怎么实现的？

转账的时候参数怎么设计

查询的时候参数怎么设计

授权批准的时候参数怎么设计

## ERC1155

<https://eips.ethereum.org/EIPS/eip-1155>

查询

交易 transfer

授权 approve

<https://www.lootproject.com/>

RWA

图床

# 2025-08-06

# Web3 学习之私钥保护

# ——将私钥导入加密密钥库

## 私钥

#### 什么是私钥？

在Web3和区块链世界中，私钥是一串唯一的数字和字母组合，用于控制和管理你的加密货币和数字资产。拥有私钥的人可以访问相应的数字资产并执行交易，因此私钥必须高度保密。

简单来说，私钥即为随机生成的复杂密码。有了私钥，您就能使用自己的数字货币。他人获知您的私钥之后，即可访问您的所有资产和币种，甚至签署和执行交易。

**为确保您的数字货币安全，妥善保管私钥至关重要**

#### 私钥的重要性

1. **访问权限**：私钥是访问你的加密钱包和数字资产的唯一凭证。没有私钥，你将无法控制或管理你的资产。
2. **安全性**：私钥应保密并安全存储。如果私钥泄露，资产可能会被盗。
3. **不可恢复**：如果私钥丢失，没有任何机构能够帮助恢复。因此，备份私钥非常重要。

#### 如何生成和管理私钥

1. **生成私钥**：私钥可以通过多种方法生成，最常见的是通过加密钱包应用程序生成。

2. **存储私钥**：私钥应以安全的方式存储，常见的存储方式包括：
    - **纸钱包**：将私钥打印或手写在纸上，并保存在安全的地方。
    - **硬件钱包**：使用专用的硬件设备来存储私钥，增加安全性。
    - **加密密钥库**：使用加密技术将私钥存储在数字文件中。以下是将私钥导入加密密钥库的示例代码：

3. **备份私钥**：始终确保有私钥的备份，最好是多个备份，存放在不同的位置以防丢失。

#### 使用私钥

私钥可以用来签名交易和验证所有权。以下是使用私钥签名交易的示例代码：

#### 结论

私钥是Web3世界中的核心概念，管理好私钥是保障数字资产安全的关键。通过学习如何生成、存储、备份和使用私钥，你可以更好地掌握和保护自己的数字资产。

## 私钥注意事项

- **私钥是保密的，不能透露给他人**。如果私钥丢了，钱就丢了。不建议把私钥放在手机或者电脑设备，联网后有机会丢失。
- **通过私钥可以反算出公钥，但通过公钥不能反算出私钥**

- 公钥和钱包地址是公开的**。如果别人要给你转钱，你把钱包地址告诉他就可以。**
- **助记词，可以用于重新生成私钥**。所以**助记词不能透露给他人**。

## 将私钥导入 encrypted keystore

### Import a private key into an encrypted keystore

#### [EXAMPLES](https://book.getfoundry.sh/reference/cast/cast-wallet-import#examples)

1. Create a keystore from a private key:

   ```sh
   cast wallet import BOB --interactive
   ```

2. Create a keystore from a mnemonic:

   ```sh
   cast wallet import ALICE --mnemonic "test test test test test test test test test test test test"
   ```

3. Create a keystore from a mnemonic with a specific mnemonic index:

   ```sh
   cast wallet import ALICE --mnemonic "test test test test test test test test test test test test" --mnemonic-index 1
   ```

### 实操

```bash

~ via 🅒 base
➜
cast wallet list

~ via 🅒 base
➜
cast wallet -h
Wallet management utilities

Usage: cast wallet <COMMAND>

Commands:
  new               Create a new random keypair [aliases: n]
  new-mnemonic      Generates a random BIP39 mnemonic phrase [aliases: nm]
  vanity            Generate a vanity address [aliases: va]
  address           Convert a private key to an address [aliases: a, addr]
  sign              Sign a message or typed data [aliases: s]
  verify            Verify the signature of a message [aliases: v]
  import            Import a private key into an encrypted keystore [aliases: i]
  list              List all the accounts in the keystore default directory
                        [aliases: ls]
  private-key       Derives private key from mnemonic [aliases: pk]
  decrypt-keystore  Decrypt a keystore file to get the private key [aliases: dk]
  help              Print this message or the help of the given subcommand(s)

Options:
  -h, --help  Print help

~ via 🅒 base
➜
cast wallet import MetaMask --interactive
Enter private key:

~ via 🅒 base took 2m 57.8s
➜
cast wallet import MetaMask --interactive
Enter private key:
Enter password:
`MetaMask` keystore was saved successfully. Address: 0x750ea21c1e98cced0d4557196b6f4a5974ccb6f5

~ via 🅒 base took 39.6s
➜
cast wallet list
MetaMask (Local)

#  The path to store the encrypted keystore. Defaults to ~/.foundry/keystores.
~ via 🅒 base
➜
ls ~/.foundry/
bin       cache     keystores share

~ via 🅒 base
➜
ls ~/.foundry/keystores/
MetaMask

#  将私钥转换为地址
~ via 🅒 base
➜
cast wallet address --keystore ~/.foundry/keystores/MetaMask
Enter keystore password:
0x750Ea21c1e98CcED0d4557196B6f4a5974CCB6f5

~ via 🅒 base took 4.3s
➜
```


## 参考

- <https://book.getfoundry.sh/reference/cli/cast/wallet/import>
- <https://learnblockchain.cn/docs/foundry/i18n/zh/reference/cast/cast-wallet.html>
- <https://book.getfoundry.sh/reference/cast/cast-wallet-import>
- <https://www.okx.com/zh-hans/learn/what-are-public-and-private-encryption-keys-crypto-wallets-explained>

# 2025-08-05

# Solana 

surfpool 之于 Solana 就像 anvil 之于以太坊：一个速度极快的⚡️内存测试网，能够立即分叉 Solana 主网。

Surfpool 提供了一个速度极快、开发者友好的 Solana 主网模拟环境，可在您的本地计算机上无缝运行。它无需高性能硬件，同时保持了真实的测试环境。

无论您是在开发、调试还是学习 Solana，Surfpool 都能为您提供一个即时、独立的网络，可以根据需要动态获取缺失的主网数据 - 无需再手动设置帐户。

### 特点

- 快速且轻量——可在任何机器上顺利运行，无需繁重的系统要求。
- 动态账户获取——在交易执行期间自动检索必要的主网账户。
- Anchor 集成 – 检测 Anchor 项目并自动部署程序。
- 教育和调试友好 - 对交易执行和状态变更提供明确的见解。
- 易于安装 - 可通过Homebrew，Snap和Direct Binaries获得。

## 实操

### 安装 surfpool

```bash
brew install txtx/taps/surfpool
```

更多详情请查看：https://docs.surfpool.run/install

### 验证安装

```bash
surfpool --version
surfpool 0.9.6
```

### 查看帮助信息

```bash
surfpool --help
Where you train before surfing Solana

Usage: surfpool <COMMAND>

Commands:
  start        Start Simnet
  completions  Generate shell completions scripts
  run          Run, runbook, run!
  ls           List runbooks present in the current direcoty
  cloud        Txtx cloud commands
  mcp          Start MCP server
  help         Print this message or the help of the given subcommand(s)

Options:
  -h, --help     Print help
  -V, --version  Print version

```

### 启动本地 Solana 网络

```bash
surfpool start                                                                                                                                         
```

#### `surfpool start`解释说明示意图

![surfpool](https://docs.surfpool.run/assets/terminal.svg)























## 总结





## 参考

- https://github.com/txtx/surfpool
- https://docs.surfpool.run/
- https://docs.surfpool.run/install

# 2025-08-04

OP Stack 链的核心权限转移记录

经验教训：部署任何类似系统时，都应遵循以下流程：

1. **拿到配置文件后，第一件事就是“权力审计”**：逐一检查文件中的每一个地址，识别出哪些是权限类地址，哪些是操作类地址。
2. **生成并分配钥匙**：为所有需要控制的地址生成新的、安全的密钥对，并做好备份。
3. **替换所有权**：将配置文件中所有默认的、未知的权限和操作地址，全部替换成你新生成的、可控的地址。
4. **带着信心部署**：在确认了蓝图上的每一个关键角色都属于自己之后，再执行最终的部署命令。


# 2025.07.29


<!-- Content_END -->
