---
timezone: UTC+8
---

# 李佳

**GitHub ID:** Ttllboy

**Telegram:** @tgshf tgjdld

## Self-introduction

大家好，我是➕，来自嘉兴，目前是一名后端java程序员，现在对web3很感兴趣，希望能和大家一起学习进步

## Notes

<!-- Content_START -->
# 2025-08-21

FundMe 是什么？
FundMe 是一个经典的 Solidity 实战项目，本质是 众筹合约，允许用户向合约发送 ETH 进行 “资助”，并支持合约所有者提取筹集的资金。

核心功能：接收 ETH 捐款、记录捐款人信息、所有者提款。
学习价值：涵盖 Solidity 基础语法、ETH 转账、权限控制、链上数据存储等核心知识点，是入门区块链开发的经典案例（类似编程中的 “TodoList”，但更贴近真实场景）。
前置知识回顾（快速衔接）
学习 FundMe 前，需先掌握以下基础，否则会影响理解：

Solidity 基础语法：
状态变量（uint、address、mapping）、函数定义（可见性 public/private、修饰符 payable）。
msg 全局变量：msg.sender（调用者地址）、msg.value（发送的 ETH 数量，单位为 wei）。
错误处理：require 语句（验证条件，不满足则回滚）。
ETH 单位换算：
1 ETH = 10^18 wei（Solidity 中 msg.value 默认单位为 wei，需用 ether 关键字转换，如 1 ether 表示 1 ETH）。
合约部署与交互：
知道如何用 Remix 或 Hardhat 部署合约，如何通过钱包向合约发送 ETH 并调用函数。
FundMe 核心功能拆解
1. 核心状态变量
FundMe 合约需要记录两个关键信息：

捐款人列表：记录所有捐款的地址（方便后续查询或感谢）。
捐款金额：记录每个地址对应的捐款数量（单位为 ETH 或 wei）。
// 存储每个地址的捐款金额（地址 → 金额，单位：wei）
mapping(address => uint256) public addressToAmountFunded;

// 存储所有捐款人地址（方便遍历）
address[] public funders;

// 合约所有者（有权提取资金）
address public owner;
捐款功能（核心函数 fund）
用户向合约捐款的函数，必须声明为 payable（允许接收 ETH），并包含以下逻辑：

验证捐款金额（至少大于 0.01 ETH，避免小额垃圾捐款）。
记录捐款人地址和金额。
function fund() public payable {
    // 验证捐款金额 ≥ 0.01 ETH（10^16 wei）
    require(msg.value >= 0.01 ether, "You need to spend more ETH!");
    
    // 记录捐款人地址（若首次捐款，添加到数组）
    if (addressToAmountFunded[msg.sender] == 0) {
        funders.push(msg.sender);
    }
    
    // 更新该地址的捐款总额（累加）
    addressToAmountFunded[msg.sender] += msg.value;
}
提款功能（核心函数 withdraw）
只有合约所有者能提取所有捐款，需包含以下逻辑：

验证调用者是所有者（权限控制）。
将合约中的所有 ETH 转账给所有者。
重置捐款记录（可选，根据需求设计）。
// 权限修饰符：限制只有 owner 能调用
modifier onlyOwner() {
    require(msg.sender == owner, "You are not the owner!");
    _; // 执行原函数逻辑
}

// 提款函数
function withdraw() public onlyOwner {
    // 遍历所有捐款人，重置捐款金额（可选操作）
    for (uint256 funderIndex = 0; funderIndex < funders.length; funderIndex++) {
        address funder = funders[funderIndex];
        addressToAmountFunded[funder] = 0;
    }
    
    // 重置捐款人数组
    funders = new address[](0);
    
    // 将合约余额转账给 owner（三种转账方式之一）
    (bool callSuccess, ) = payable(owner).call{value: address(this).balance}("");
    require(callSuccess, "Call failed");
}

# 2025-08-20

Solidity 工厂模式学习笔记
一、定义与概念
在 Solidity 中，工厂模式是一种创建型设计模式。它主要用于创建其他合约实例，就像是一个 “合约生成器”。通过工厂合约，可以在运行时动态创建新的合约，并对这些新创建的合约进行管理和配置。
二、使用场景
批量创建合约：当需要创建大量相似功能的合约时，比如创建多个具有相同业务逻辑但参数配置不同的 Token 合约，工厂模式可以简化创建流程。
动态配置合约：根据不同的需求，在创建合约时动态设置合约的初始化参数，例如在创建一个投票合约时，根据投票主题、参与人员等不同信息动态创建不同的投票合约实例。
合约管理：方便对创建的合约进行统一管理，比如记录合约的地址、创建时间等信息，还可以提供一些方法来调用或销毁创建的合约。
三、代码示例
（一）被创建的合约（以简单的计数器合约为例）
solidity
contract Counter {
    uint256 public count;

    constructor() {
        count = 0;
    }

    function increment() public {
        count++;
    }
}
（二）工厂合约
solidity
contract CounterFactory {
    // 用于存储创建的 Counter 合约地址
    address[] public createdCounters; 

    // 创建 Counter 合约的函数
    function createCounter() public {
        Counter newCounter = new Counter();
        createdCounters.push(address(newCounter));
    }

    // 获取创建的 Counter 合约数量
    function getCounterCount() public view returns (uint256) {
        return createdCounters.length;
    }

    // 调用某个 Counter 合约的 increment 函数
    function incrementCounter(uint256 index) public {
        require(index < createdCounters.length, "Index out of bounds");
        Counter(address(createdCounters[index])).increment();
    }
}

# 2025-08-19

单元测试覆盖
分支测试：覆盖函数的所有分支（如 if-else、require 条件），确保逻辑健壮。
模拟攻击：用测试框架模拟重入攻击、权限漏洞，验证合约安全性（如 Foundry 的 vm.prank 模拟恶意调用）。
集成测试
多合约交互：测试合约间的调用（如 ERC20 转账 + 质押合约的联动逻辑）。
链上数据验证：测试部署后的数据持久化（如检查 mapping 状态是否正确更新）。
代码覆盖率
工具：Hardhat 配合 solidity-coverage，Foundry 用 forge coverage，确保关键逻辑无遗漏。
脚本化部署：用 Hardhat/Foundry 编写部署脚本，支持不同网络（主网、测试网、本地节点）。
示例（Hardhat）：
task("deploy", "Deploy contract")
  .setAction(async (taskArgs, hre) => {
    const MyContract = await hre.ethers.getContractFactory("MyContract");
    const myContract = await MyContract.deploy();
    await myContract.deployed();
    console.log("Deployed to:", myContract.address);
  });
合约验证
区块链浏览器验证：通过 Hardhat/Foundry 自动提交合约代码到 Etherscan/BSCscan，方便审计。
示例（Hardhat）：
await hre.run("verify:verify", {
  address: myContract.address,
  constructorArguments: [],
});
升级合约（Proxy Pattern）
透明代理：学习 OpenZeppelin 的 TransparentUpgradeableProxy，实现合约逻辑升级（数据与逻辑分离）。
UUPS 代理：更轻量的升级方案，需注意初始化逻辑。
ERC 标准实现
ERC20/ERC721/ERC1155：深入理解代币标准，实现自定义逻辑（如手续费、 mint 控制）。
扩展标准：ERC2771（元交易，支持无 Gas 交易）、ERC1363（支付代币）。
DeFi 基础模块
AMM 算法：实现简单的恒定乘积做市商（如 x*y=k 模型），理解流动性池逻辑。
借贷逻辑：模拟抵押借贷（如存入资产 → 借出 ETH，处理清算）。
NFT 与链上数据
链上 SVG：生成动态 NFT（如用 Solidity 拼接 SVG 代码，实现链上艺术）。
On-Chain Metadata：直接在合约中存储 NFT 元数据（无需 IPFS），探索极端案例。
跨链与 Layer2 基础
跨链通信：了解 CCIP（Chainlink Cross-Chain Interoperability Protocol）、Hyperlane 等跨链方案，尝试简单消息传递。
Layer2 适配：学习 Optimism/Arbitrum 的基础概念，修改合约适配 Layer2（如处理不同的 gas 模型）。

# 2025-08-18

复杂数据类型深化
（1）动态数组与固定数组
动态数组：uint[]（长度可变），学习 push（追加）、pop（删除末尾）、length（获取长度）等操作。
映射（Mapping）高级用法
嵌套映射：mapping(address => mapping(uint => bool)) public nestedMap;（地址 → 数字 → 布尔值）。
映射与数组结合：因映射无法直接遍历，需用数组记录键（如 address[] public keys; mapping(address => uint) public balances;），实现遍历逻辑。
结构体（Struct）与枚举（Enum）
结构体：定义复杂类型（如 struct User { string name; uint age; }），结合映射 / 数组存储（如 mapping(address => User) public users;）。
枚举：简化状态管理（如 enum Status { Pending, Approved, Rejected }），减少魔法值（Magic Number）。
合约继承（Inheritance）
基础继承：contract A { ... } contract B is A { ... }，子类继承父类的状态变量、函数。
构造函数继承：constructor() 需用 constructor() A() {} 显式继承父类构造逻辑。
函数重写（Override）：子类重写父类函数，需加 override 关键字（父类函数需标记 virtual）。
接口（Interface）与抽象合约（Abstract）
接口：interface IERC20 { function transfer(address to, uint value) external; }，定义函数签名，用于跨合约调用（如与其他代币合约交互）。
抽象合约：含未实现函数（function foo() external pure virtual;），需被子类继承并实现，用于规范接口或复用部分逻辑。
重入攻击（Reentrancy）
原理：外部调用（如 call）前未锁定状态，导致合约被重复调用，篡改状态。
防范：
使用 reentrancy guard（锁变量：bool locked; modifier noReentrant { require(!locked); locked = true; _; locked = false; }）。
遵循 “检查 - 效果 - 交互”（CEI）模式：先改状态（如扣余额），再外部调用。
整数溢出（Overflow/Underflow）
问题：uint8 a = 255; a++; 会溢出为 0（旧版本 Solidity 问题）。
防范：
使用 Solidity 0.8+（默认检测溢出，报错回滚）。
手动校验（如 require(a + b <= type(uint).max, "Overflow");）。
权限控制
最小权限原则：状态变量、函数可见性严格限制（如 private 变量仅内部访问）。
管理员权限：用 modifier onlyOwner { require(msg.sender == owner); _; } 控制关键操作（如 mint 代币）。
Gas 优化
存储优化：状态变量尽量用 uint256（EVM 对 32 字节操作最友好），减少 storage 读写。
函数优化：external 函数优先用 calldata（而非 memory）传参，降低 gas。
避免循环：长循环会消耗大量 gas，甚至触发区块 gas 上限，尽量拆分逻辑。

# 2025-08-16

学习solidity。
函数可见性：深理解 public、private、internal、external 的区别（尤其是 internal 和 private、external 和 public 的使用场景）。例如：external 函数不能被合约内部直接调用，需用 this.函数名()，适合外部交互；internal 可被继承合约调用，适合内部逻辑复用。
函数修饰器（Modifiers）：学习定义和使用 modifier，用于复用代码（如权限校验、条件检查）。
payable 函数 ：理解 payable 关键字的作用 —— 允许函数接收以太币（ETH），以及如何通过 msg.value 获取传入的 ETH 数量（单位是 wei）。
理解 memory、storage、calldata 的区别：
storage：存储在区块链上的永久数据（如状态变量），修改会消耗较多 gas。
memory：临时存储在内存中，函数执行结束后销毁（如函数内的临时变量）。
calldata：用于函数的外部输入参数（类似 memory，但不可修改），更省 gas。
区块链特有概念与交互
1. 地址与以太币交互
地址类型的核心操作：转账 ETH（address.transfer(amount)、address.send(amount)、call{value: amount}("") 的区别，尤其是错误处理机制）。
msg 全局变量：msg.sender（调用者地址）、msg.value（传入的 ETH 数量）、msg.data（调用数据）的实际应用。
2.合约部署与调用
学习如何在合约中调用其他合约：通过接口（Interface）或地址直接调用。
事件的定义与触发：用于记录合约状态变化，方便前端或外部工具监听（区块链上的日志，不可修改）。
indexed 关键字：用于索引事件参数，最多 3 个，方便后续过滤查询。
错误处理机制
理解 require、revert、assert 的区别：
require：用于输入验证或条件检查，失败时回滚并返还剩余 gas（最常用）。
revert：手动触发回滚，可自定义错误消息（适合复杂条件判断）。
assert：用于内部逻辑校验（如 invariants 不变量），失败通常表示合约有 bug，会消耗所有 gas。
 简单安全意识
了解「重入攻击」的基本概念：当合约调用外部合约时，外部合约可能再次调用原合约，导致状态异常。
Gas 优化初步：例如尽量使用 calldata 而非 memory 作为外部参数，避免不必要的存储操作等。

# 2025-08-14

熟悉了 Remix 的布局：包括左侧的 “文件浏览器”（创建 / 管理.sol 合约文件）、“编译面板”（选择编译器版本、编译合约）、“部署与运行环境”（选择测试网络、部署合约、调用函数）。新建新合约文件（.sol 后缀），并通过语法高亮识别代码结构（如关键字、注释、变量类型）。
编译合约：解决编译报错（如版本不匹配、语法错误），查看编译后的 ABI 和字节码（区块链部署的核心数据）。
部署合约：使用内置的 “JavaScript VM”（本地测试环境）快速部署，无需真实代币，方便调试。
交互测试：部署后在界面上调用合约的函数（包括读写函数），观察状态变量变化、查看返回值和 Gas 消耗。Gas 概念的直观感受
通过 Remix 的部署和函数调用，观察不同操作的 Gas 消耗（如修改状态变量比读取消耗更多 Gas，复杂逻辑比简单逻辑消耗更多），理解 “链上资源昂贵” 的特性，初步形成优化代码的意识。
部署与状态的不可逆性
意识到合约部署后，代码无法修改（除非设计升级机制），状态变量的修改会被永久记录在区块链上，因此编写代码时需更注重逻辑严谨性。
理解 Remix 的 “JavaScript VM” 是本地测试网络，用于快速验证合约逻辑，无需连接真实区块链，降低学习成本。后续可尝试连接测试网（如 Goerli），体验真实链上部署流程。

# 2025-08-13

今天学习了Chainlink 预言机。
智能合约本身无法主动获取链外数据，Chainlink 预言机能够将区块链智能合约连接到现实世界的数据、活动及支付行为等，以安全的方式为其引进链下资料，避免依赖中心化实体。
可靠的数据输入输出工具：它为各种区块链上的复杂智能合约提供可靠、防篡改的输入与输出。Chainlink 预言机节点可格式化并验证数据，让智能合约能安全连接数据提供商、Web API、物联网设备、支付系统等各类链下资源。
去中心化的服务网络：其作为去中心化预言机，凭借多节点、多数据源聚合并加密签名的特性，降低了单点故障以及数据被篡改的风险。节点分布广泛，能接入多家数据提供商，避免单一数据源被操控的状况。每个节点提交数据后进行签名，链上合约通过验证聚合结果，确保数据真实性。
经济激励与安全保障体系：Chainlink 的原生代币 LINK 用于支付节点服务费用、质押及激励等。提供快速且精准信息的节点会获得 LINK 币奖励，提供错误信息的节点，其质押的部分 LINK 币会被没收，借此激励节点准确服务，增强系统的整体安全性与数据准确性。

# 2025-08-12

理解了 Solidity 是以太坊等区块链平台的智能合约编程语言，语法类似 JavaScript，但专为区块链环境设计。掌握了合约的 骨架：以pragma solidity 版本号;声明编译器版本，通过contract 合约名 { ... }定义合约主体，这是所有智能合约的基础框架。明确了合约与 “类” 的相似性：合约包含数据（状态变量）和操作数据的逻辑（函数），类似面向对象编程中的 “类”。学习了基础数据类型：如uint256（无符号整数）、string（字符串）、bool（布尔值）等，知道不同类型的适用场景（例如uint256常用于计数、金额等数值场景）。核心区分：状态变量（合约内、函数外声明，存储在区块链上，持久化存在，修改消耗 Gas）与局部变量（函数内声明，仅临时存在于内存，不消耗 Gas），这是理解区块链存储逻辑的关键。掌握了函数的基本定义：function 函数名(参数) { ... }，知道函数是合约的 “行为”，用于处理逻辑、修改状态或返回数据。初步了解函数的作用：可以读取 / 修改状态变量、接收 / 发送代币、调用其他合约等。

# 2025-08-11

学习了区块链的相关常识，以前总是有点不懂，现在稍微明白一些了。区块链的历史交易记录是不可更改的，看教程上总是有出现更改的内容，把我弄的有点懵，但其实区块链是在不停生成新的区块。目前我理解到的流程是，所有新生成的交易记录会先验证，验证好之后放到交易池进行打包，打包结束之后，就会算作一个新的区块被加入到区块链在最后。同时学习了hardhat的部分操作，目前还没有学通

# 2025-08-09

今天学习了hardhat框架相关内容，不过还没有实操。学习了Hardhat的基本概念和操作
学习Hardhat的编译部署、测试和调试智能合约的工具套件
通过创建一个加法合约来学习Hardhat的基本操作
使用np hard head compile进行合约编译和部署的方法，同时提供了一些注意事项和解决缓存问题的方法。最后学习了使用菜断言库进行测试的方法。
使用np hard head compile进行合约编译
使用内置网络部署合约
使用断言库测试合约在HK的开发

# 2025-08-08

今天学习了区块链的入门知识，包括区块前后是以hash关联，每一个改动都需要挖矿。然后部署了智能合约的开发环境，不过还没有完全部署好，学习了一部分的智能合约开发知识

# 2025-08-07

今天学习了智能合约开发的部分内容，安装好了nvm，正看到状态变量部分。
看到群里发的Blockchain Demo学习，去youtube学习了区块链的基础知识。

# 2025-08-06

今天搞懂了一些概念和代码。
1、钱包余额，以前不懂区块链是怎么算钱的，现在知道了钱包余额是这个地址的交易记录的计算结果。
2、contract CounterTest is Test {}。在这一行代码里面，contract 是一个核心关键字，用于定义智能合约。contract 就像java中的 class（类）。
3、RPC地址和合约地址， RPC 地址是用来连接区块链节点的，合约是部署在区块链上的一段代码，合约地址是合约在链上的 身份标识。

# 2025-08-05

今天学到了很多防骗知识，感觉这个行业资产还是有很多危险的

# 2025-08-04

今天学到了，发币和领币要看好要在同一个网络，不然币就凭空消失了


# 2025.07.31


<!-- Content_END -->
