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
