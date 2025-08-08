---
timezone: UTC+8
---

# linhong

**GitHub ID:** midleser

**Telegram:** @hiro

## Self-introduction

六年web2前端，目前在成都，想进入web3邻域

## Notes

<!-- Content_START -->
# 2025-08-08

# 智能合约开发

## 一、Solidity

- 定义：Solidity 是一种专门为编写智能合约 (Smart Contracts) 而设计的高级编程语言。

- 用途：它主要用于在以太坊（Ethereum）以及其他兼容的区块链平台上创建和部署智能合约。

- 合约示例：

```solidity
// 指定 Solidity 编译器版本
pragma solidity ^0.8.0;

// 定义一个名为 HelloWorld 的合约
contract HelloWorld {
    
    // 定义一个 string 类型的状态变量来存储信息
    string private message;

    // 这是一个构造函数，当合约被部署到区块链上时，它会自动执行一次
    constructor() {
        message = "Hello, Web3!";
    }

    // 一个公开的函数，任何人都可以调用它来读取 message 的值
    function getMessage() public view returns (string memory) {
        return message;
    }

    // 一个公开的函数，允许我们修改 message 的值
    function setMessage(string memory newMessage) public {
        message = newMessage;
    }
}
```

### 基础语法

1. 基础数据类型

```solidity
    // 数值类型
    uint256 public number = 42;
    int256 public signedNumber = -42;

    // 布尔类型
    bool public flag = true;

    // 地址类型
    address public owner = 0x1234...;

    // 字符串和字节
    string public name = "Ethereum";
    bytes32 public hash;

    // 数组
    uint256[] public dynamicArray;
    uint256[5] public fixedArray;

    // 映射
    mapping(address => uint256) public balances;

    // 结构体
    struct User {
        string name;
        uint256 age;
        address wallet;
    }
```

2. 函数修饰符

```solidity
contract Modifiers {
    // 状态变量，用于存储合约所有者的地址。
    address public owner;

    constructor() {
      // 将 `msg.sender` (即部署合约的账户地址) 赋值给 `owner` 变量，将合约的部署者设置为所有者。
        owner = msg.sender;
    }

    modifier onlyOwner() {
        // 检查调用者是否是所有者
        require(msg.sender == owner, "Not owner");
        _; // 如果上面的 require 通过，则继续执行函数本身的代码
    }
    // 验证传入的地址是否为有效地址
    modifier validAddress(address _addr) {
        // 如果地址是零地址，则回滚交易。
        require(_addr != address(0), "Invalid address");
        _;
    }

    function restrictedFunction() 
        public  // public: 任何人都可以尝试调用此函数 (但会被修饰符拦截)
        onlyOwner  // 首先检查调用者是否是 owner
        validAddress(msg.sender) // 接着检查调用者地址是否有效
    {
        // 只有owner可以调用
    }
}
```

### 常见合约模式

1. ERC20代币合约

```solidity
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor(
        string memory name,
        string memory symbol,
        uint256 totalSupply
    ) ERC20(name, symbol) {
        _mint(msg.sender, totalSupply);
    }
}
```

2. 多重签名钱包

```solidity
contract MultiSig {
    address[] public owners;
    uint256 public requiredConfirmations;

    struct Transaction {
        address to;
        uint256 value;
        bytes data;
        bool executed;
        uint256 confirmations;
    }

    Transaction[] public transactions;
    mapping(uint256 => mapping(address => bool)) public isConfirmed;

    modifier onlyOwner() {
        require(isOwner(msg.sender), "Not owner");
        _;
    }

    function submitTransaction(
        address _to,
        uint256 _value,
        bytes memory _data
    ) public onlyOwner {
        // 提交交易逻辑
    }

    function confirmTransaction(uint256 _txIndex) public onlyOwner {
        // 确认交易逻辑
    }
}
```

## 二、 Dapp（Decentralized Application）去中心化应用

- 前端：一个看起来和普通网站或 App 差不多的界面。它也是用我们熟悉的技术（如 HTML, JavaScript, React）构建的。

- 后端：不是中心服务器，而是部署在区块链上的智能合约（就是我们之前说的用 Solidity 写的代码）。当你点击“交换代币”时，你的请求直接发送给区块链上的智能合约。

- 控制权：没有任何一个实体能单独控制。程序的规则（智能合约）公开透明地写在区块链上，只要满足条件就会自动执行，谁也无法篡改或阻止。

### DApp 的核心组成部分

1. 前端 (Frontend)：用户能看到和交互的界面。
2. 智能合约 (Smart Contracts)：这就是 DApp 的**“后端灵魂”**。所有核心的业务逻辑、规则和状态都记录在这里，并由 Solidity 等语言编写，运行在以太坊等区块链上。
3. 资产管理：你的加密货币、NFT 等资产都存放在钱包地址里。

# 2025-08-07

# web3学习笔记

## 法律与监管框架

### 法律框架与安全

法律环境概览

- 禁止 ICO（首次代币发行）
- 禁止虚拟货币交易所运营与提供撮合服务；
- 打击以虚拟货币为载体的非法集资、传销、洗钱、赌博等活动
- 境内使用人民币购买USDT,ETH等虚拟币，通过链上或境外平台兑换美元等外币，绕过了外汇监管，在不知情的情况下被卷入“协助洗钱”的刑事链条，交易必须谨慎

监管背景与趋势

- 分国家/地区监管政策对比

| 国家/地区    | 核心监管机构 | 总结 |
| ----------- | ----------- | ----------- |
| 美国      | SEC, CFTC, OCC       | 将比特币和以太坊视为商品、 将大部分代币视为证券进行监管、允许银行提供加密货币托管服务     |
| 欧盟   | ESMA        | MiCA（加密资产市场法规）      |
| 香港   | VASP、稳定币监管        | 要求平台实施严格的 KYC/AML 措施、要求稳定币发行商获得香港金管局许可      |

### 法律风险防范

1. 许多项目方在中国境内无注册公司，无法依法缴纳五险一金，难以依照《劳动合同法》享受合法保障。
2. 薪酬结构“人民币 + Token”或“全 USDT”，特别注意注意，用自发 Token 支付薪资的项目，其代币价值波动剧烈，可能归零。
3. 虚拟货币出金与合规风险，虚拟货币兑换为人民币为出金，出金过程中极易卷入刑事风险，务必通过可信渠道进行出金。

## 典型攻击案例与安全防范

1. 攻击类型分类

- 智能合约漏洞 (Smart Contract Vulnerabilities):
 重入攻击、整数溢出
- 协议逻辑错误 (Protocol Logic Errors):
  闪电贷攻击
- 外部安全风险 (External Security Risks):
  私钥泄露、钓鱼攻击、Rug Pull

2. 防护清单

- 只用官方 Zoom/Teams/腾讯会议等公开工具
- 任何要求“连接钱包”“签名验证”“输入助记词/私钥”的网页，99% 是骗局
- 不轻信群内“官方人员”“管理员”“学长学姐”的私聊和链接
- 重要账户启用 2FA（短信易被劫持，优选谷歌验证或硬件密钥）；不要在同一浏览器里保存助记词与日常 Cookie。

# 2025-08-06

# Web3 前端工程师的岗位职责

1. 核心技能与职责
构建用户界面 (UI)：设计和实现直观、响应式的用户界面，确保用户能够轻松地与 DApp 交互。
状态管理与数据流：处理 DApp 中的复杂状态，包括用户钱包连接状态、智能合约数据、交易状态等。与区块链交互：通过特定的库（如 Ethers.js 或 Web3.js）与智能合约进行通信，发送交易、读取链上数据。
钱包集成：集成主流的加密货币钱包（如 MetaMask、WalletConnect），使用户能够连接自己的钱包并授权交易。
安全性考量：在前端层面也要考虑安全性，防止常见的攻击，并确保用户资产的安全。
2. 常用前端技术栈React：
作为目前最流行的前端框架之一，React 因其组件化、声明式编程和高效的虚拟 DOM 而成为 DApp 开发的首选。
3. 了解常用的区块链网络理解不同区块链网络的特性对于选择合适的开发平台至关重要。

以太坊 (Ethereum)：智能合约平台：第一个支持智能合约的区块链，拥有最成熟的开发者生态系统。EVM 兼容：许多其他区块链（如 Polygon, Binance Smart Chain）都与以太坊虚拟机 (EVM) 兼容，这意味着在以太坊上开发的智能合约和工具可以轻松迁移。
Gas 费用：交易需要支付 Gas 费，费用会根据网络拥堵程度波动。
PoS 机制：已从工作量证明 (PoW) 转向权益证明 (PoS)，提高了效率和可扩展性。

Solana：高性能：以其极高的交易吞吐量和低廉的交易费用而闻名。
Rust 语言：智能合约主要使用 Rust 语言编写，与以太坊的 Solidity 不同。
生态发展：近年来生态系统迅速发展，吸引了大量开发者和项目。

4. DApp 开发经验者优先具备 DApp 开发经验意味着你对整个 Web3 应用的生命周期有实践经验，
包括：钱包集成与交互：熟练使用 Web3 库连接钱包并调用智能合约方法。
链上数据处理：能够从区块链上获取和解析数据，并在前端进行展示。
交易签名与发送：理解交易的生命周期，包括签名、广播和确认。
去中心化思维：理解去中心化应用的独特挑战和优势。
智能合约开发智能合约是运行在区块链上的代码，它们定义了 DApp 的核心业务逻辑。

Solidity 编程语言以太坊智能合约语言：Solidity 是编写以太坊兼容区块链（如以太坊、Polygon、BNB Chain 等）上智能合约的主要语言。
面向合约：它是一种静态类型、面向合约的高级语言，语法类似于 JavaScript。
关键概念：学习其数据类型、函数、修饰符、事件、错误处理、继承等核心概念。
安全性：智能合约一旦部署就无法更改，因此安全性至关重要。学习常见的安全漏洞（如重入攻击、整数溢出）及防范措施。

 需求分析在编写智能合约之前，进行彻底的需求分析是必不可少的。
 明确业务逻辑：理解 DApp 的核心功能、用户角色和交互流程。
 定义合约功能：确定智能合约需要实现哪些函数、存储哪些数据、触发哪些事件。考虑可扩展性与升级：规划合约未来的升级路径（如果需要）。
 
 智能合约开发开发环境：使用 Remix IDE、Hardhat 或 Foundry 等工具进行开发和测试。
 编写合约代码：根据需求分析编写 Solidity 代码，实现 DApp 的核心逻辑。
 测试：编写单元测试和集成测试，确保合约在各种场景下都能按预期工作，并且没有安全漏洞。
 优化 Gas 消耗：由于链上操作需要 Gas 费，编写高效的合约代码可以降低用户成本。
 
 前端界面开发，链接钱包，与区块链交互这部分是前端和智能合约的结合点。
 集成 Web3 库：在前端项目中使用 Ethers.js 来与区块链网络和智能合约进行通信。
 连接钱包：通过库提供的 API，实现用户钱包（如 MetaMask）的连接功能。
 调用合约方法：读取数据 (Call)：调用合约的 view 或 pure 函数，这些操作不修改链上状态，不需要 Gas 费。
 发送交易 (Send)：调用合约的 payable 或修改状态的函数，这些操作会修改链上状态，需要用户签名并支付 Gas 费。
 监听事件：订阅智能合约发出的事件，以便在前端实时更新 UI。
 
 部署上线选择网络：决定将合约部署到哪个区块链网络（测试网如 Sepolia、主网如 Ethereum Mainnet）。部署工具：使用 Hardhat、Foundry 或 Remix 等工具将编译好的合约字节码部署到区块链上。验证合约：在 Etherscan 等区块链浏览器上验证合约代码，提高透明度和用户信任。

# 2025-08-05

以太坊和BTC的区别：
1.以太坊有图灵完备的编程语言，可开发复杂智能合约；BTC只支持脚本开发。
2.从工作量证明（PoW）转向权益证明（PoS）以前是矿工将新交易打包到新的区块，现在是从所有质押 ETH 的验证者中，随机选择一位验证者来创建下一个区块，该验证者负责将网络中的新交易打包成一个区块，并将其广播到网络中，其他验证者会检查这个区块是否有效，再将区块添加到链上
3.以太坊使用一种叫做 Solidity 的编程语言来编写智能合约，智能合约本质上就是一段能自动执行协议的，就像一个自动售货机，按照写好的程序来执行交易

# 2025-08-04

了解去中心化的思想，比特币的原理：区块主要数据就是一系列交易，Merkle Hash是每个交易的hash组合而来，篡改一个交易就要计算后续所有的hash，所以不可篡改。工作量证明：就是先给定一个难度值，不断计算，找到一个比给定难度值低的Hash。共识算法: 如果两个矿工在同一时间各自找到了有效区块,出现分叉，有的会在分叉1继续挖矿，有的会在分叉2继续挖矿，最终总有一个先挖到矿，最长分叉的共识算法，会废弃一个分叉，所有的矿工又继续在最长的链上挖矿。
交易原理：比特币只支持脚本编程，当a给b支付比特币时，是a创建一个锁定脚本，该脚本引入了b的地址，所以只有b能创建正确的解锁脚本，钱包软件创建交易实际上就是脚本编程，编写符合条件的脚本。
智能合约：当一个预先编好的条件被触发时，就如创建锁定脚本，输入解锁脚本，自动执行相应的程序，自动完成数字资产的转移


# 2025.08.01


<!-- Content_END -->
