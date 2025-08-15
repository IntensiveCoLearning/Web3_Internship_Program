---
timezone: UTC+8
---

# 赵启睿

**GitHub ID:** ZRainbow1275

**Telegram:** @徒生

## Self-introduction

不想做AIGC创作和WayToAGI社群运营的全栈开发者，不是好数据安全、隐私保护、信息安全、数据安全律师

## Notes

<!-- Content_START -->
# 2025-08-15

## 1. EVM 核心与底层机制



### 存储、内存与Calldata的深度辨析



- **存储 (Storage)**:

  - **布局**: 状态变量被打包进 32 字节的“插槽” (Slot) 中。理解这一点是 Gas 优化的关键。EVM 为了节省空间，会尝试将多个小变量打包进同一个插槽。

  - **示例**:

    Solidity

    ```
    // 顺序不佳：占用 3 个插槽
    contract BadOrder {
        uint128 a; // Slot 0 (a 占前半部分)
        uint256 b; // Slot 1 (b 占满)
        uint128 c; // Slot 2 (c 占前半部分)
    }
    
    // 顺序优化：仅占用 2 个插槽
    contract GoodOrder {
        uint128 a; // Slot 0 (a 占前半部分)
        uint128 c; // Slot 0 (c 占后半部分)
        uint256 b; // Slot 1 (b 占满)
    }
    ```

  - **成本**: `SSTORE` (写入存储) 是 EVM 中最昂贵的操作之一。

- **内存 (Memory)**:

  - **作用**: 临时的、易失性的数据存储区，仅在函数执行期间存在。主要用于存储复杂类型，如 `struct`, `string`, `bytes` 和动态数组。
  - **成本**: 内存会动态扩展，其成本会以**二次方**级别增长。因此，在函数中应避免创建过大的内存数组。

- **Calldata**:

  - **作用**: 存储 `external` 函数调用数据的特殊数据位置。
  - **特性**: `calldata` 是只读的，且极其便宜。**对于 `external` 函数的引用类型参数，应始终优先使用 `calldata` 而非 `memory`**，这能显著降低 Gas 消耗。



### `delegatecall` 的威力与风险



`delegatecall` 是 Solidity 中最强大也最危险的底层函数之一，它是所有代理模式（可升级合约）的基石。

- **工作原理**: `A.delegatecall(B)` 表示合约 A 调用合约 B 的代码，但**执行上下文是合约 A**。这意味着：
  - 合约 B 的代码会读取和写入合约 **A** 的存储。
  - `msg.sender` 和 `msg.value` 保持为调用合约 **A** 的原始调用者。
- **风险**: **存储插槽冲突**。如果代理合约和逻辑合约中状态变量的声明顺序或类型不一致，逻辑合约可能会错误地修改代理合约中的变量，导致灾难性后果。



## 2. 高级设计模式





### 可升级合约：代理模式 (Proxy Pattern)



为了让不可变的智能合约变得“可升级”，社区发明了代理模式。

- **架构**:
  - **代理合约 (Proxy)**: 拥有合约的地址和所有状态数据。用户始终与这个地址交互。它本身几乎没有逻辑。
  - **逻辑合约 (Implementation)**: 包含所有业务逻辑。
- **流程**: 用户调用 `Proxy` -> `Proxy` 通过 `delegatecall` 将调用转发给 `Implementation` -> `Implementation` 的代码在 `Proxy` 的上下文中执行，修改 `Proxy` 的存储。
- **升级**: 只需部署一个新的 `Implementation` 合约，然后调用 `Proxy` 合约的一个管理函数，将其指向新的 `Implementation` 地址即可。
- **标准**:
  - **Transparent Proxy Pattern**: 将升级管理逻辑和业务逻辑分离，安全性高但部署成本略高。
  - **UUPS (EIP-1822)**: 将升级逻辑放在 `Implementation` 合约中，部署成本更低，是目前更受推荐的标准。



### EIP-2535: 钻石模式 (Diamonds)



当一个项目逻辑极其复杂，需要突破 24KB 合约大小限制时，钻石模式是一种更灵活的方案。

- **核心思想**: 一个钻石代理合约可以通过 `delegatecall` 调用**多个**逻辑合约（称为 “Facets”）。它可以精确地将不同的函数选择器（function selectors）映射到不同的 Facet，实现了高度的模块化和可扩展性。



## 3. Gas 优化

- **存储与读取优化**:

  - **变量打包 (Variable Packing)**: 如上文所述，合理安排状态变量顺序。

  - **缓存状态变量到内存**: 在循环中，如果需要多次读取同一个状态变量，应先将其读入一个内存变量，在循环中使用内存变量，可以避免多次昂贵的 `SLOAD` 操作。

    Solidity

    ```
    // 不佳
    for (uint i = 0; i < myArray.length; i++) {
        total += balances[myArray[i]]; // 每次循环都 SLOAD
    }
    // 优化
    uint256[] memory localArray = myArray; // SLOAD 一次
    for (uint i = 0; i < localArray.length; i++) {
        total += balances[localArray[i]];
    }
    ```

- **运算优化**:

  - **使用 `unchecked` 块**: 在 Solidity 0.8+ 版本中，所有算术运算都会默认检查上溢和下溢，这会消耗少量 Gas。如果你通过逻辑能 100% 确定运算不会溢出，可以将其放入 `unchecked { ... }` 块中以节省 Gas。
  - **短路规则**: 合理利用 `&&` 和 `||` 的短路特性，将成本较低的检查放在前面。

- **数据优化**:

  - **使用事件 (Events)**: 不要将大量数据存储在链上，而应将计算结果或状态变更通过事件发射出去，让链下服务去监听和存储。
  - **最小化输入数据**: 函数参数越少，calldata 越小，交易成本越低。



## 4. Solidity 内联汇编 (Yul)



当 Solidity 无法满足对性能的极致追求时，可以使用内联汇编直接与 EVM 交互。

- **为什么需要汇编**:

  1. **极致的 Gas 优化**: 精确控制 EVM 操作。
  2. **实现 Solidity 无法实现的功能**: 例如，获取一个合约的代码大小。

- **基本语法**:

  Solidity

  ```
  assembly {
      // Yul 代码写在这里
      let free_mem_ptr := mload(0x40) // 获取空闲内存指针
      mstore(free_mem_ptr, 0x20)      // 将值 0x20 存入内存
  }
  ```

- **警告**: 内联汇编非常强大，但它绕过了 Solidity 的许多安全检查。错误的使用可能导致严重的安全漏洞。



## 5. 深入安全考量



- **签名重放攻击**: 在实现链下签名验证（元交易）时，必须确保每个签名只能被使用一次。通常的解决方案是：
  - 在签名数据中包含一个 nonce（递增的数字）。
  - 使用 EIP-712 标准，它为链下消息签名提供了结构化的、抗重放的格式。
- **预言机操纵 (Oracle Manipulation)**:
  - **风险**: 绝不能直接使用单个去中心化交易所（如 Uniswap）的瞬时价格作为价格预言机，因为它极易被攻击者通过一笔大的交易操纵。
  - **解决方案**: 使用时间加权平均价格 (TWAP)，或者聚合多个预言机源（如 Chainlink）。
- **MEV (Maximal Extractable Value)**:
  - **概念**: 矿工/验证者通过在区块中打包、排除或重排交易来获取超额利润的能力。常见的 MEV 类型包括**抢跑 (Front-running)** 和 **三明治攻击 (Sandwich Attacks)**。
  - **影响**: 作为开发者，需要意识到 MEV 的存在，并设计出能抵抗或减轻其影响的协议机制（例如，使用私密内存池，或设计对交易顺序不敏感的机制）。

# 2025-08-14

## 1\. 合约的基本结构

一个完整的 Solidity 文件通常包含版本声明和合约定义。

```solidity
// 1. SPDX 许可证标识符
// SPDX-License-Identifier: MIT

// 2. Solidity 编译器版本指令
pragma solidity ^0.8.20;

// 3. (可选) 导入其他合约文件
// import "./OtherContract.sol";

// 4. 合约定义，合约名应为大驼峰式
contract MyContract {
    // 合约内容写在这里
}
```

## 2\. 状态变量声明

状态变量是永久存储在合约存储空间中的变量。

**语法**: `[类型] [可见性] [修饰符] [变量名];`

```solidity
contract Variables {
    // 整数类型，public 可见性，任何人都能读取
    uint256 public myUint;

    // 字符串类型，private 可见性，只能在本合约内部访问
    string private myString = "Hello, Solidity!";

    // 地址类型，internal 可见性，本合约及子合约可访问
    address internal owner;

    // 特殊修饰符
    // - constant: 编译期常量，声明时必须初始化，无法修改
    uint256 public constant MY_CONSTANT = 123;

    // - immutable: 构造时常量，在构造函数中赋值一次后无法修改
    address public immutable contractDeployer;
}
```

## 3\. 数据类型与数据结构

### 值类型 (Value Types)

  * **布尔 (bool)**: `true` 或 `false`
  * **整数 (uint / int)**: `uint8` 到 `uint256` (步长为8)，`int8` 到 `int256`。`uint` 是 `uint256` 的别名。
  * **地址 (address)**: `address` 和 `address payable`
  * **定长字节数组 (bytes)**: `bytes1`, `bytes2`, ..., `bytes32`

### 引用类型 (Reference Types)

  * **数组 (Arrays)**:
    ```solidity
    // 定长数组 (状态变量)
    uint256[5] public fixedArray;

    // 动态数组 (状态变量)
    uint256[] public dynamicArray;

    function arrayExample() public {
        // 在内存中创建数组 (必须指定长度)
        string[] memory memoryArray = new string[](3);
    }
    ```
  * **结构体 (Structs)**: 自定义的数据结构。
    ```solidity
    struct User {
        uint id;
        string name;
        address userAddress;
    }
    // 声明一个结构体类型的状态变量
    User public myUser;
    ```
  * **映射 (Mappings)**: 键值对存储。
    ```solidity
    // 语法: mapping(键类型 => 值类型)
    // 注意：映射只能作为状态变量
    mapping(address => uint256) public balances;
    mapping(uint256 => User) public users;
    ```
  * **字符串 (string)**: 任意长度的动态字节数组，用于存储文本。`string public myName;`

## 4\. 函数 (Functions)

**完整语法**: `function <函数名>(<参数列表>) <可见性> <状态可变性> [returns (<返回类型>)] { ... }`

```solidity
contract FunctionSyntax {
    uint256 public value;

    // 1. 参数: 类型 + 数据位置(calldata/memory) + 名称
    // 2. 可见性: external
    // 3. 状态可变性: payable (可以接收ETH)
    function deposit(uint256 _amount) external payable {
        require(msg.value == _amount, "ETH sent does not match amount");
        value += _amount;
    }

    // 可见性: public
    // 状态可变性: view (只读状态，不修改)
    // 返回值: returns (单个返回类型)
    function getValue() public view returns (uint256) {
        return value;
    }

    // 状态可变性: pure (不读取也不修改状态)
    // 返回值: returns (多个返回类型)
    function add(uint256 a, uint256 b) public pure returns (uint256, string memory) {
        return (a + b, "Success");
    }
}
```

  * **可见性 (Visibility)**: `public`, `private`, `internal`, `external`
  * **状态可变性 (State Mutability)**:
      * `view`: 只读，不修改状态。
      * `pure`: 不读取也不修改状态。
      * `payable`: 可接收 ETH。
      * (默认为空): 可修改状态。

## 5\. 流程控制与操作符

与大多数编程语言类似。

```solidity
function controlFlow(uint256 a) public pure returns (string memory) {
    if (a == 0) {
        return "Zero";
    } else if (a < 10) {
        return "Less than 10";
    } else {
        return "10 or more";
    }

    // For 循环
    uint sum = 0;
    for (uint i = 0; i < 5; i++) {
        sum += i;
    }
}
```

  * **操作符**:
      * 算术: `+`, `-`, `*`, `/`, `%` (取余), `**` (指数)
      * 比较: `==`, `!=`, `<`, `<=`, `>`, `>=`
      * 逻辑: `!` (非), `&&` (与), `||` (或)

## 6\. 错误处理

用于验证条件和回滚交易。

```solidity
// 1. require: 用于验证输入或外部条件
// 如果条件为 false，交易将被回滚
require(msg.sender != address(0), "Invalid address");

// 2. revert: 直接回滚交易
revert("Something went wrong");

// 3. assert: 用于检查内部错误或不变量
// 如果条件为 false，交易将被回滚。通常用于测试阶段
uint x = 1;
assert(x == 1);
```

## 7\. 事件 (Events)

用于在链上记录日志，方便链下应用监听。

```solidity
// 1. 声明事件
event Transfer(address indexed from, address indexed to, uint256 amount);

function transfer(address _to, uint256 _amount) public {
    // ...转账逻辑...

    // 2. 触发事件
    emit Transfer(msg.sender, _to, _amount);
}
```

  * `indexed` 关键字可以让该字段被高效地索引和搜索。

## 8\. 构造函数与修饰器

  * **构造函数 (Constructor)**: 在合约部署时仅执行一次的特殊函数。
    ```solidity
    address public owner;
    constructor() {
        owner = msg.sender;
    }
    ```
  * **修饰器 (Modifiers)**: 用于在函数执行前复用检查逻辑。
    ```solidity
    // 1. 定义修饰器
    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        _; // 特殊符号，代表函数体的执行位置
    }

    // 2. 在函数上使用修饰器
    function changeOwner(address _newOwner) public onlyOwner {
        owner = _newOwner;
    }
    ```

# 2025-08-13

## 核心理念：重新思考应用架构



### Web2 vs. Web3 架构对比



- **Web2**: `前端 -> 中心化后端API -> 中心化数据库`
- **Web3**: `前端 -> 钱包/节点提供商 -> 智能合约 (区块链)`



### DApp的核心组件



一个完整的DApp通常由以下几个部分协同工作：

1. **前端 (Frontend)**: 用户与之交互的界面，通常使用React (Next.js) 或 Vue.js 构建。
2. **钱包 (Wallet)**: 用户的身份、账户和资产管理器（如MetaMask）。它是用户与区块链交互的签名工具和安全门户。
3. **节点/提供商 (Provider)**: 前端与区块链通信的网关。开发者可以使用 Alchemy 或 Infura 等节点服务，而无需自己运行一个完整的节点。
4. **智能合约 (Smart Contracts)**: 部署在区块链上、不可篡改的后端业务逻辑。
5. **去中心化存储 (Decentralized Storage)**: 用于存储大数据或文件（如图片、视频、元数据），常见方案有 IPFS 和 Arweave。
6. **链下数据索引 (Off-chain Data Indexing)**: 用于高效查询和展示链上数据，The Graph 是该领域的绝对王者。

------



## 第一层：智能合约 (后端核心)



### 设计模式 (Design Patterns)



- **可升级合约 (Proxy Pattern)**: 这是生产级DApp的标配。通过一个代理合约(Proxy)指向您的逻辑合约(Implementation)，当需要修复Bug或升级功能时，只需将代理指向一个新的逻辑合约地址，而无需用户迁移数据。常用标准有 UUPS 和 Transparent Proxy。
- **工厂模式 (Factory Pattern)**: 使用一个主合约来部署和管理其他合约实例。例如，一个DEX的工厂合约可以用来创建新的交易对合约。
- **访问控制 (Access Control)**: 考虑更复杂的权限管理，例如 `AccessControl` 模式，它可以设置不同的角色（如 `minter`, `admin`），并为每个角色分配不同的权限。



### 专业开发框架与测试



- **Hardhat vs. Foundry**:
  - **Hardhat**: 基于JavaScript，生态系统成熟，与前端工具链（Ethers.js, Waffle）集成度极高。是目前社区的主流选择。
  - **Foundry**: 基于Rust，用Solidity写测试，以其极高的编译和测试速度著称，受到越来越多追求极致性能的开发者的青睐。
- **深度测试**:
  - **分叉测试 (Forking Test)**: 直接从主网的某个区块高度分叉一个本地测试环境。这使得你可以在真实的主网状态下测试你的合约交互，例如测试你的借贷协议与Aave的集成。
  - **模糊测试 (Fuzz Testing)**: 使用 Echidna 等工具，对你的函数输入大量随机数据，以探索意想不到的边界情况和漏洞。



### 安全与审计



"**Code is Law**" 意味着没有回头路。

- **静态分析**: 使用 Slither 等工具在编码阶段自动扫描代码，发现潜在的漏洞。
- **专业审计**: 在主网部署前，必须聘请专业的第三方安全公司进行全面审计。这是项目信誉和用户资金安全的保障。

------



## 第二层：前端与区块链的交互



前端是用户体验的直接体现，其流畅度和可靠性至关重要。



### 现代Web3前端库



- **Ethers.js**: 目前社区公认的与以太坊交互的首选库，比 Web3.js 更轻量、API更友好。核心概念：
  - `Provider`: 读取区块链数据的连接器。
  - `Signer`: 代表一个可以签署交易和消息的账户。
  - `Contract`: 与链上合约进行交互的抽象对象。
- **Wagmi**: **（强烈推荐）** 一个强大的React Hooks库，它封装了Ethers.js的复杂性。使用Wagmi可以极其简单地：
  - 管理钱包连接状态（连接、断开、切换账户/网络）。
  - 获取链上数据，如账户余额、ENS名称等，并自动处理缓存和更新。
  - 调用合约的读/写方法。
- **连接器库 (Connectors)**: 为了支持多种钱包（MetaMask, WalletConnect, Coinbase Wallet等），可以使用 **Web3-Onboard** 或 **RainbowKit / ConnectKit** (基于Wagmi) 这样的库，它们可以一键生成美观且功能强大的“连接钱包”模态框。



### 提升用户体验 (UX) 的关键



- **交易状态反馈**: 必须为用户提供清晰的交易状态反馈：等待签名、交易发送中 (Pending)、交易成功 (Success) 或失败 (Fail)，并提供Etherscan链接供用户查询。
- **乐观更新 (Optimistic UI)**: 在交易发送后，可以先在UI上“假设”它会成功，并立即更新状态（例如，一个点赞）。如果交易最终失败，再将UI回滚。
- **可读性**: 将复杂的十六进制地址转换为ENS域名，将庞大的Wei单位转换为ETH，能极大提升用户体验。

------



## 第三层：数据层与扩展性



区块链不适合存储所有数据，需要一个强大的数据层来支撑应用。



### 去中心化存储：IPFS 与 Arweave



- **为什么需要？**: 直接在智能合约中存储一张图片会消耗天价的Gas费。
- **IPFS (InterPlanetary File System)**: 内容寻址系统。你根据文件的内容哈希来请求文件，非常适合存储NFT的图片和元数据。缺点是需要有节点“钉住”(Pin)文件，否则文件可能丢失。
- **Arweave**: 一次性付费，永久存储。它更像一个去中心化的硬盘，适合存储需要永久保存且不可更改的数据，如文章、历史记录等。



### 数据索引与查询：The Graph



- **为什么需要？**: 假设想获取一个用户过去所有的交易记录，或者一个NFT的所有持有者。直接遍历区块链来查询几乎是不可能的。
- **工作原理**: The Graph 允许你定义一个“子图”(Subgraph)，它会监听你的智能合约触发的事件。当事件被触发时，The Graph 会将数据处理并存储在自己的索引节点中，然后通过一个极其高效的 **GraphQL API** 将数据提供给你的前端。
- **结论**: 任何需要展示复杂历史数据或聚合数据的DApp，都**必须**使用The Graph。

------



## 终极目标：一个完整的DApp开发流程



1. **规划**: 定义应用功能，设计合约架构和数据流。
2. **合约开发**: 使用Hardhat/Foundry编写和进行单元/集成/分叉测试。
3. **测试网部署**: 将合约部署到Sepolia等测试网。
4. **前端开发**: 使用Next.js + TypeScript搭建前端框架。
5. **连接**: 使用Wagmi + Ethers.js实现前端与测试网合约的交互。
6. **数据层**: (如果需要) 为你的合约创建一个Subgraph，并用它来获取链上历史数据。将NFT元数据上传到IPFS。
7. **审计**: 将代码送去专业审计。
8. **主网部署**: 审计通过后，将合约和Subgraph部署到主网。

# 2025-08-11

## 第一步：搭建基础与核心概念





### 什么是 Solidity？



Solidity 是一种面向合约的高级编程语言，专门为编写**智能合约**而设计。它是静态类型的，支持继承、库和复杂的用户定义类型。如果您熟悉 C++、Python 或 JavaScript，会发现 Solidity 的语法有些相似之处。它是运行在以太坊虚拟机（EVM）上的所有智能合约最主流的语言。

### 合约的基本结构



一个 Solidity 文件（通常以 `.sol` 结尾）的基本结构如下：

Solidity

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

// 定义一个合约，合约名通常采用大驼峰命名法
contract MyFirstContract {
    // 这里是合约的内容
}
```

- `// SPDX-License-Identifier: MIT`: 这是代码开源许可证的声明，建议始终加上。
- `pragma solidity ^0.8.20;`: 这是编译器版本指令。`^`符号表示该合约可以使用 `0.8.20` 或以上版本，但在 `0.9.0` 版本以下的编译器进行编译。



## 第二步：掌握 Solidity 的数据类型





### 值类型 (Value Types)



这些类型的变量在赋值或传参时，总是进行值拷贝。

- `bool`: 布尔类型，值为 `true` 或 `false`。
- `uint` / `int`: 无符号整数和有符号整数。`uint` 是 `uint256` 的别名，这是最常用的整数类型。
- `address`: 地址类型，用于存储一个20字节的以太坊地址。
  - `address payable`: 一种可以接收以太币的特殊地址类型。
- `bytes`: 定长字节数组，如 `bytes1`, `bytes32`。



### 引用类型 (Reference Types)



这类变量更为复杂，在赋值时需要考虑其数据存储位置（`storage`, `memory`, `calldata`）。

- `string`: 用于存储任意长度的 UTF-8 编码字符串。

- `数组 (Arrays)`:

  - 定长数组: `uint[5] public myArray;`
  - 动态数组: `uint[] public myDynamicArray;`

- `结构体 (Structs)`: 允许你定义自己的复杂数据类型。

  Solidity

  ```
  struct Person {
      uint age;
      string name;
  }
  ```

- `映射 (Mappings)`: 强大的键值对存储结构，可以理解为哈希表或字典。

  Solidity

  ```
  // 将地址映射到一个无符号整数（例如：查询用户余额）
  mapping(address => uint) public balances;
  ```



## 第三步：函数、变量与流程控制





### 状态变量 vs. 局部变量



- **状态变量 (State Variables)**: 声明在合约层级，其值被永久地存储在区块链上。修改状态变量会消耗大量 Gas。
- **局部变量 (Local Variables)**: 声明在函数内部，其生命周期仅限于函数执行期间，不会永久存储在链上。



### 函数 (Functions)



函数是合约中可执行的代码块。

Solidity

```
uint public myNumber;

// 一个简单的函数，用于更新状态变量 myNumber 的值
function setNumber(uint _newNumber) public {
    myNumber = _newNumber;
}
```

- **函数可见性**:
  - `public`: 任何人都可以调用。
  - `private`: 只能在当前合约内部调用。
  - `internal`: 当前合约及继承它的子合约可以调用。
  - `external`: 只能从合约外部调用。



### 错误处理



在 Solidity 中，当出现问题时，交易会被“回滚”（revert），所有状态更改都会被撤销。

- `require(bool condition, string message)`: **最常用**。用于检查函数输入或外部条件是否满足。如果不满足，交易回滚。
- `assert(bool condition)`: 用于检查代码内部的错误，如整数溢出。若不满足，交易回滚。
- `revert(string message)`: 直接触发回滚。



## 第四步：与以太坊交互





### 全局变量



Solidity 提供了一些全局变量，用于获取关于交易和区块链的信息：

- `msg.sender` (address): 调用当前函数的账户地址。**是实现权限控制的核心**。
- `msg.value` (uint): 随调用发送的以太币数量（单位是 wei）。
- `block.timestamp` (uint): 当前区块的时间戳。



### 事件 (Events)



事件是合约与外部世界（如你的 DApp 前端）通信的桥梁。当合约中发生重要事情时，可以触发一个事件，前端应用可以监听这些事件并做出响应。

Solidity

```
// 声明一个事件
event NumberChanged(address indexed who, uint newNumber);

function setNumber(uint _newNumber) public {
    myNumber = _newNumber;
    // 触发事件
    emit NumberChanged(msg.sender, _newNumber);
}
```

# 2025-08-09

## 核心开发环境与工具链



专业的智能合约开发不仅仅是在 Remix 上编写代码，通常会使用更强大的本地工具链来支持复杂的项目。

- **Node.js**: 许多开发工具（如 Hardhat）都基于 Node.js 环境，是开发的基础。
- **Hardhat**: 目前最流行的以太坊开发框架。它允许你：
  - 在本地运行一个模拟的以太坊网络（Hardhat Network），用于快速开发和测试。
  - 自动化编译、测试和部署合约的流程。
  - 轻松集成其他工具（如 Ethers.js, Waffle）。
- **Foundry**: 一个新兴的、基于 Rust 的开发工具集，以其极高的性能和原生 Solidity 测试能力而受到欢迎。主要由两部分组成：
  - **Forge**: Solidity 测试框架。
  - **Anvil**: 本地测试节点。
- **Ethers.js**: 一个完整且简洁的 JavaScript 库，用于与以太坊区块链及其生态系统进行交互。是前端 DApp 和后端脚本与智能合约通信的桥梁。



## Solidity 语言核心要点





### 全局变量和函数



在 Solidity 中，有一些全局可用的变量和函数，它们提供了关于交易和区块链的重要信息：

- `msg.sender` (address): 当前外部调用（或交易）的发起方地址。这是最常用的用于身份验证的变量。
- `msg.value` (uint): 随消息发送的 Wei 的数量。主要用于处理合约收到的以太币。
- `block.timestamp` (uint): 当前区块的时间戳（自 Unix 纪元以来的秒数）。
- `tx.origin` (address): 交易的原始发送方。**注意**：为安全起见，通常应避免使用 `tx.origin` 进行授权，因为它可能导致类似钓鱼的攻击。优先使用 `msg.sender`。



### 函数可见性 (Visibility)



- `public`: 任何人都可以调用（外部或内部）。Solidity 会自动为 public 状态变量创建同名的 `getter` 函数。
- `private`: 只能在当前合约内部调用，即使是继承的合约也无法调用。
- `internal`: 类似于 `private`，但继承的合约也可以调用。
- `external`: 只能从合约外部调用，不能被合约内部的其他函数调用（除非使用 `this.functionName()`）。通常用于接收外部交易的函数，Gas 效率更高。



### 数据位置 (Data Locations)



理解数据存储位置对于 Gas 优化和避免 Bug 至关重要。

- `storage`: 永久存储在区块链上的变量。这是 Gas 成本最高的操作。
- `memory`: 临时存储，仅在函数执行期间存在。Gas 成本中等。
- `calldata`: 存储外部函数调用数据的特殊位置，是只读的。这是 Gas 成本最低的数据位置，是传递参数的理想选择。



## 智能合约的生命周期



1. **编写 (Write)**: 使用 Solidity 等语言编写合约代码 (`.sol` 文件)。
2. **编译 (Compile)**: 使用编译器（如 `solc`）将 Solidity 代码编译成两部分：
   - **字节码 (Bytecode)**: 将被部署到以太坊网络上的机器码。
   - **ABI (Application Binary Interface)**: JSON 格式文件，定义了如何与合约的函数和数据进行交互，相当于合约的“API文档”。
3. **部署 (Deploy)**: 将编译后的字节码作为一笔交易发送到以太坊网络。网络确认后，会创建一个新的合约账户，并返回一个唯一的地址。
4. **交互 (Interact)**: 使用合约的地址和 ABI，通过 Web3 库（如 Ethers.js）从前端或其他合约调用其 `public` 或 `external` 函数。



## 重要标准：ERC (Ethereum Request for Comments)



ERC 是以太坊上的应用级标准，确保了不同项目（钱包、交易所、DApp）之间的可组合性和互操作性。

- **ERC-20**: 可替代代币（Fungible Token）的标准接口。所有我们熟知的同质化代币（如 USDT, UNI）都遵循此标准。核心函数包括 `balanceOf()`, `transfer()`, `approve()`, `transferFrom()` 等。
- **ERC-721**: 非替代代币（Non-Fungible Token, NFT）的标准接口。每个代币都是独一无二的，拥有唯一的 `tokenId`。常用于数字艺术品、收藏品和游戏道具。
- **ERC-1155**: 一种多代币标准，允许在一个合约中同时管理多种类型的代币（既可以是非替代的，也可以是可替代的）。极大地提高了批量处理代币的效率，常用于游戏领域。



## 开发最佳实践与安全考量



- **使用成熟的库**: 永远优先使用经过审计和社区检验的库，如 **OpenZeppelin**。不要自己从零开始发明轮子，尤其是对于像 ERC20 或访问控制这样的标准实现。
- **测试，测试，再测试**: 编写全面的测试用例，覆盖所有函数、边界条件和预期的失败情况。测试是保证合约质量的最低要求。
- **遵循“检查-生效-交互”模式 (Checks-Effects-Interactions Pattern)**: 这是一种防止**重入攻击 (Re-entrancy Attacks)** 的重要设计模式。
  1. **检查 (Checks)**: 先执行所有的条件检查（如 `require(msg.sender == owner)`）。
  2. **生效 (Effects)**: 然后更新合约的内部状态（如 `balances[msg.sender] -= amount`）。
  3. **交互 (Interactions)**: 最后再与外部合约或地址进行交互（如 `payable(msg.sender).transfer(amount)`）。
- **Gas 优化**: 注意会消耗大量 Gas 的操作，如 `SSTORE`（写入存储），循环等。合理使用数据位置 (`calldata`, `memory`) 可以显著降低 Gas 成本。
- **了解常见攻击**: 除了重入攻击，还应了解**整数溢出/下溢**（尽管 Solidity 0.8+ 已内置保护）、**预言机操纵**、**三明治攻击**等。

# 2025-08-08

## 中国监管环境与主要法律风险





### 核心政策



中国对虚拟货币采取严格的监管政策，但对 Web3 技术本身持有限容忍态度。核心要点包括：

- **禁止ICO**: 禁止任何形式的代币发行融资活动。
- **禁止交易所运营**: 禁止虚拟货币交易所在境内运营。
- **禁止虚拟货币支付**: 虚拟货币不能作为法定货币在市场上流通使用。



### 主要法律风险



- **非法金融活动**:
  - **代币发行与交易**: 任何形式的代币融资都可能构成非法集资，技术人员也可能承担连带责任。
  - **场外交易 (OTC)**: 极易卷入洗钱、非法换汇等案件，导致银行账户被冻结甚至承担刑事责任。
- **刑事风险**:
  - **开设赌场罪**: 链游中的“下注-随机收益-可兑现”机制可能被认定为赌博。
  - **组织、领导传销活动罪**: 项目中的“邀请返利”、“多级推广”等模式可能触犯法律。
- **民商事纠纷**:
  - 虚拟货币相关的买卖或投资合同，可能因违反国家政策而被认定为无效，导致损失需自行承担。



## 全球监管趋势



全球范围内，加密货币的监管正日益明确和严格，合规是行业大势所趋。

- **欧盟 (EU)**: 发布了《加密资产市场监管法案》(MiCA)，标志着行业走向全面监管。
- **新加坡**: 出台了反洗钱（AML）新规。
- **美国 (USA)**: 正在推进针对稳定币的法案。
- **FATF (金融行动特别工作组)**: 提出的“旅行规则”(Travel Rule) 要求虚拟资产服务提供商 (VASP) 在转账时收集和传输交易双方信息，这对 DeFi 和 DEX 构成了合规挑战。



## 求职者风险防范



在 Web3 行业求职时，需要特别注意以下几点：

- **雇佣关系**:
  - **劳动合同**: 许多 Web3 项目主体在境外，可能导致无法签订有效的劳动合同，也无法缴纳社保，影响落户、购房等。
- **薪酬结构**:
  - **代币薪酬**: 以 Token 或 USDT 支付薪酬存在法律和税务风险，且代币价格波动可能导致收入不稳定。
  - **出金风险**: 将虚拟货币兑换为法币时，要警惕收到“黑钱”，导致银行账户被冻结。
- **项目审查**:
  - 入职前应仔细审查项目的**白皮书**、**代币模型**和**运营模式**，避免加入涉嫌非法的项目。



## 常见骗局与安全防护





### 常见骗局



- **钓鱼攻击**: 通过邮件、私信等方式发送恶意链接，诱导用户授权钱包或泄露私钥。
- **假冒网站/App**: 利用 Punycode 等技术制造与官方域名极度相似的假网站，或提供带有恶意代码的假App。
- **恶意软件**: 通过空投、软件下载等方式植入木马，窃取你的敏感信息。



### 核心防护措施



- **谨慎点击链接**: 对任何来源不明的链接保持警惕，始终通过官方渠道访问项目。
- **授权检查**: 定期检查并取消钱包中不再需要或可疑的授权。
- **使用硬件钱包**: 将大额资产存储在硬件钱包（冷钱包）中。
- **物理备份**: 离线保存助记词和私钥，切勿在联网设备上存储或传输。
- **启用双重认证 (2FA)**: 为所有交易平台和重要账户启用 2FA。

# 2025-08-07

## 什么是智能合约？



智能合约是一种存储在区块链上的计算机程序或交易协议。它被设计用来在满足预设条件时，自动执行、控制或记录法律相关事件和行为。可以把它想象成一个数字自动售货机：你投入指定的金额（满足条件），机器就会自动给你对应的商品（执行合约）。

它的核心目标是：

- **减少对可信中介的需求**：交易可以自动执行，无需银行、律师等第三方机构的介入。
- **降低成本**：减少了中介费用、仲裁和执行成本。
- **提高效率和精确度**：代码自动执行，避免了人工操作的延迟和错误。
- **增强安全性和透明度**：交易记录被加密并分布在整个区块链网络上，难以篡改。所有感兴趣的参与方都可以公开查看和验证合约代码。



## 智能合约如何工作？



智能合约的运作遵循简单的“如果…那么…” (if/then) 逻辑，其工作流程大致如下：

1. **构建**: 开发者使用特定的编程语言（如Solidity）编写合约代码，将协议的条款和条件包含进去。
2. **存储**: 合约被部署到区块链上，成为分布式账本的一部分。网络中的每个节点都会存有这份合约的副本。
3. **执行**: 当外部事件（如收到一笔付款）触发了合约中设定的条件，网络中的所有节点会同时执行合约代码，并对结果达成共识。交易完成后，区块链的状态会被更新。

一旦部署到区块链上，智能合约通常是不可更改的，这确保了其条款不会被单方面篡改。



## 智能合约开发入门





### 核心语言和工具



- **Solidity**: 以太坊上最主流的智能合约编程语言，语法上与JavaScript等语言有相似之处。
- **Remix**: 一个非常适合新手的在线集成开发环境（IDE），可以直接在浏览器中编写、编译和部署智能合约。
- **Web3.js / Ethers.js**: JavaScript库，用于通过网页应用与以太坊区块链上的智能合约进行交互。
- **Truffle / Brownie**: 流行的智能合约开发框架，提供了一整套工具用于编译、测试和部署。
- **OpenZeppelin合约库**: 一个经过安全审计的、可重用的智能合约组件库，可以帮助开发者避免重复编写标准功能（如代币合约）并减少安全漏洞。



### 关键概念



- **状态变量**: 永久存储在合约存储空间中的变量。
- **函数**: 合约中可执行的代码单元，可以改变合约状态。
- **事件 (Event)**: 一种方便的机制，用于在EVM（以太坊虚拟机）的日志中记录操作，常用于通知外部应用合约发生了什么。
- **修饰器 (Modifier)**: 用于在函数执行前以声明的方式检查条件，例如检查调用者是否是合约的所有者。
- **Gas费**: 在以太坊网络上执行任何操作（包括部署和调用智能合约）都需要支付的交易费用，用以激励矿工维护网络。



### 安全性

安全性是智能合约开发中最关键的一环，因为代码漏洞可能导致巨额的资金损失。开发者需要特别关注以下几点：

- **重入攻击 (Re-entrancy Attack)**: 一种常见的攻击方式，黑客利用合约的漏洞，在一次函数调用完成前重复调用该函数，从而窃取资金。
- **整数溢出/下溢**: 在进行数学运算时，要防止数字超出其数据类型的存储范围。
- **权限控制**: 明确设置不同函数的调用权限，防止未经授权的操作。

# 2025-08-06

# Web3 学习笔记





## 1. 什么是 Web3？



Web3 是下一代互联网的构想，它利用区块链等去中心化技术，旨在将数据的所有权和控制权归还给用户。与当前由大型科技公司主导的 Web2.0 不同，Web3 的核心理念是**去中心化、去信任化和无权限化**。



### Web3 的核心特征:



- **去中心化 (Decentralization):** 信息不再集中存储在单一服务器上，而是分散在网络中的多个节点，降低了单点故障和审查的风险。
- **去信任化和无权限化 (Trustless and Permissionless):** 用户之间可以直接交互，无需依赖受信任的第三方中介。任何人都可以参与网络，无需经过授权。
- **用户拥有数据主权:** 用户可以真正拥有和控制自己的数字身份、数据和资产。
- **可组合性:** Web3 的应用可以像乐高积木一样相互组合，创造出更强大的功能。



## 2. Web3 的核心技术



Web3 的技术栈仍在不断发展，但主要包括以下几个核心要素：

- **区块链 (Blockchain):** 一种去中心化的、不可篡改的分布式账本技术，是 Web3 的底层基础。
- **加密资产 (Crypto Assets):** 包括加密货币（如比特币、以太坊）和非同质化代币（NFT），是 Web3 经济体系的重要组成部分。
- **智能合约 (Smart Contracts):** 部署在区块链上的可自动执行的程序，可以实现各种复杂的业务逻辑。
- **预言机 (Oracles):** 将现实世界的数据安全地引入区块链，为智能合约提供可靠的数据源。
- **去中心化应用 (dApps):** 运行在区块链或点对点网络上的应用程序，不受任何单一实体控制。
- **加密钱包 (Crypto Wallets):** 用于管理加密资产和与 dApp 交互的工具。



## 3. Web3 的应用场景



Web3 正在颠覆各个行业，应用场景：

- **去中心化金融 (DeFi):** 提供开放、透明、无需许可的金融服务，如借贷、交易、保险等。
- **非同质化代币 (NFT):** 代表独特的数字资产，可用于数字艺术品、收藏品、游戏道具等。
- **去中心化自治组织 (DAO):** 由社区成员共同治理的组织，通过智能合约实现自动化管理。
- **游戏 (GameFi):** 玩家可以在游戏中赚取加密资产，实现 "边玩边赚"。
- **元宇宙 (Metaverse):** 一个持久的、共享的、三维的虚拟空间，用户可以在其中进行社交、娱乐、工作等活动。



## 4. 如何开始学习 Web3？

对于初学者来说，可以从以下几个方面入手：

1. **了解基本概念:** 学习区块链、加密货币、智能合约等基本概念。
2. **体验 Web3 应用:** 尝试使用加密钱包、DEX（去中心化交易所）、NFT 市场等 Web3 应用。
3. **学习开发技术:** 如果有兴趣，可以学习 Solidity（以太坊智能合约语言）、JavaScript 等开发技术。
4. **关注行业动态:** 关注 Web3 领域的最新资讯和项目，了解行业发展趋势。



## 5. 总结

Web3 仍然处于早期发展阶段，充满了机遇和挑战。但它所倡导的去中心化、用户主权的理念，将对未来的互联网产生深远的影响。希望这份笔记能为你开启 Web3 的学习之旅，祝你学习愉快！


# 2025.07.29


<!-- Content_END -->
