---
timezone: UTC+5
---

# 岳鸿枢（Thomas）

**GitHub ID:** Thomas-YHS

**Telegram:** @Thomas_YHS

## Self-introduction

金融学硕士在读，本科CS，2年web2经验，会Solidity，参加了Arbitrum残酷共学，LXDAO成员

## Notes

<!-- Content_START -->
# 2025-08-16

想要了解智能合约，了解以太坊，Open Zeppelin是一个无法跳过的东西，一般源码的阅读，智能合约的使用都是从ERC-20开始的，我们也从这里翻开学习的篇章。

对于想要了解ERC-20我建议从OpenZepplin文档开始阅读：https://docs.openzeppelin.com/contracts/5.x/

那么什么是OpenZepplin呢，下面引用官方的介绍：

> **library for secure smart contract development.** Build on a solid foundation of community-vetted code.一个用于安全智能合约开发的库。建立在社区验证的代码坚实基础之上。
> 
> - Implementations of standards like [ERC20](https://docs.openzeppelin.com/contracts/5.x/erc20) and [ERC721](https://docs.openzeppelin.com/contracts/5.x/erc721).实现了 ERC20 和 ERC721 等标准。
> - Flexible [role-based permissioning](https://docs.openzeppelin.com/contracts/5.x/access-control) scheme.灵活的角色权限方案。
> - Reusable [Solidity components](https://docs.openzeppelin.com/contracts/5.x/utilities) to build custom contracts and complex decentralized systems.可重用的 Solidity 组件，用于构建自定义合约和复杂的去中心化系统。

安全，标准，灵活的角色权限，可充用的组件

## 扩展合约

OpenZeppelin大多数合约可以通过is来继承调用，例如`contract MyToken is ERC20` 

<aside>
🧠

不过不建议通过重写的方式来，使用更建议独立使用。

</aside>

<aside>
🌐

与 `contract` 不同，Solidity `library` 不是通过继承得到的，而是依赖于 `using for` 语法。

OpenZeppelin Contracts has some `library`s: most are in the [Utils](https://docs.openzeppelin.com/contracts/5.x/api/utils) directory.OpenZeppelin Contracts 

utils目录中，有大量可供调用的**library**库，[components](https://docs.openzeppelin.com/contracts/5.x/utilities) 这个组件库中也对utils中进行了详细的说明。

</aside>

## 访问控制Access Control

访问控制——也就是说，“谁被允许做这件事”——在智能合约的世界中至关重要。您合约的访问控制可能决定了谁可以铸造代币、投票、冻结转账以及许多其他事情。

### Ownable

Ownable是OpenZeppelin中几乎最常用的包了，其中包含对Owner的设定，对Owner的检查`modifier onlyOwner()` ，还包括对owner的转移和放弃，是权限管理的基础

```solidity
abstract contract Ownable is Context {
    address private _owner;
		// 无效Owner地址
    error OwnableUnauthorizedAccount(address account);
    // 非 owner 调用
    error OwnableInvalidOwner(address owner);
		// 转移 owner 权限日志
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);
		
		// 设定合约初始Owner
    constructor(address initialOwner) {
        if (initialOwner == address(0)) {
            revert OwnableInvalidOwner(address(0));
        }
        _transferOwnership(initialOwner);
    }
		
		// 只有合约Owner才能调用的方法
    modifier onlyOwner() {
        _checkOwner();
        _;
    }
		
		// 获取合约Owner
    function owner() public view virtual returns (address) {
        return _owner;
    }

    function _checkOwner() internal view virtual {
        if (owner() != _msgSender()) {
            revert OwnableUnauthorizedAccount(_msgSender());
        }
    }
		// 放弃合约Owner的身份
    function renounceOwnership() public virtual onlyOwner {
        _transferOwnership(address(0));
    }
		
		// 转移Owner身份
    function transferOwnership(address newOwner) public virtual onlyOwner {
        if (newOwner == address(0)) {
            revert OwnableInvalidOwner(address(0));
        }
        _transferOwnership(newOwner);
    }

    function _transferOwnership(address newOwner) internal virtual {
        address oldOwner = _owner;
        _owner = newOwner;
        emit OwnershipTransferred(oldOwner, newOwner);
    }
}
```

---

但是，Ownable也存在一些问题，比如说在进行合约所有权转移的时候可能会出现转移到错误账号的情况，所以Ownable2Step就诞生了！它解决了可能出现的所有权转移错误问题，它要求新所有者通过调用 `acceptOwnership` 明确接受所有权转移。

Note that **a contract can also be the owner of another one**! 

通过这种方式，您可以使用组合性为您的合约添加额外的访问控制复杂性层。例如，您可以使用由您的项目负责人管理的 2-of-3 多重签名，而不是只有一个常规以太坊账户（外部拥有账户，或 EOA）作为所有者。空间中的知名项目，如 MakerDAO，使用与此类似的系统。

### 

## ERC165

**让一个智能合约可以声明并让别人查询它是否实现了某个接口。**

在 ERC-165 之前，如果你和另一个合约交互（比如想调用它的某个函数），**你根本不知道对方支不支持这个接口**。

- 如果直接调用不支持的函数，会直接 revert。
- 每次交互前都要写额外的检测逻辑，很麻烦。

ERC-165 就是为了解决这个问题，**提供一个标准化的接口识别方法**。

在钱包调用智能合约时会率先，检测是否包含对应的合约接口，如果包含则调用，避免造成revert。

> 其实现逻辑如下：
> 

```solidity
function supportsInterface(bytes4 interfaceId) public view virtual returns (bool) {
        return interfaceId == type(IERC165).interfaceId;
    }
```

通过前四位的interfaceId进行对比，判断是否实现了所需接口。

## ERC165与ERC721的onERC721Received函数

# 2025-08-14

今天完成网路搭建输出了一篇问题记录的笔记，今天会继续阅读ERC20合约和Openzeppelin的相关源码，之后会仔细观看今天技术分享会的录屏。

# 2025-08-13

今天继续整理Dapp笔记，学习wagmi，之后开始阅读Defi的标志型产品Uniswap的Doc，之后尝试在我的ThomasToken 中融入Uniswap

# 2025-08-12

今天完成了ThomasToken的前端部分，使用的技术站是React + wagmi + RainbowKit + Viem + React Query，今天接下来的时间我会完成一些笔记方面的工作，已经对前端的进一步学习。

# 2025-08-11

今天参加了以太坊周后，并且学习了uniswapV4合约分析了PoolManager合约代码，开始构建ThomasToken的前端页面部分

# 2025-08-09

## **核心概念与创新特性**

### **1. 集中流动性（Concentrated Liquidity）**

- 与 V2 不同，V3 允许流动性提供者（LP）指定一个 **价格区间** 内提供流动性，而不是覆盖整个价格曲线 (0, ∞)，极大提升资金利用率 。
- 示例说明：Alice 在 V2 中分布在整条曲线上，而 Bob 在 V3 中仅在 1,000–2,250 区间提供流动性，两人收益相同但 Bob 使用资金更少 。

### **2. Ticks 与 Tick Spacing**

- 为实现集中流动性，V3 将价格空间划分为离散的预定义点，称为 **ticks** 。
- Tick 间隔（tick spacing）取决于设置的手续费等级，不同手续费等级对应不同的最小价格间隔和流动性分布 。

### **3. 多费率池（Fee Tiers）**

- 每个交易对可以创建多个池子，支持不同的手续费率：当前常见为 0.05%、0.30% 和 1%，通过治理可添加更多，例如稳定币可低至 0.01% 。

### **4. 非同质化流动性（NFT Positions）**

- 每个 LP 在 V3 中的流动性是以 NFT（ERC-721）形式表示的，因为每个头寸都有唯一的价格区间；不再是 fungible ERC-20 LP 代币 。

### **5. 灵活手续费和治理**

- 手续费结构可在池子创建时灵活设定，治理可以管理协议手续费份额、费率等级及 tick spacing 等参数 。

### **6. 预言机升级（Oracle Improvements）**

- V3 提供更强大的 TWAP（时间加权平均价格）功能，不再依赖用户存储历史累积价格，而是内置了一个环形缓冲区记录多个历史 checkpoint，支持计算几何平均价格（减少极端值影响）和流动性累积 。

# 2025-08-08

今天分享了，自己的笔记，明天准备学习uniswap和完善我的程序

# 2025-08-07

ww.notion.so/245a9133700780bd9b4ed60dde7e268c?pvs=21)

## 什么是Foundry

Foundry 是一个智能合约开发工具链。

Foundry 管理您的依赖项，编译您的项目，运行测试，部署，并允许您通过命令行和 Solidity 脚本与链交互。

```bash
curl -L https://foundry.paradigm.xyz | bash

# Install forge, cast, anvil, chisel
foundryup
```

通过这个命令导入Foundry

## **Foundry 的核心组件**

### **1. Forge - 主要开发工具**

- **编译合约**：forge build
- **运行测试**：forge test
- **部署合约**：forge script
- **管理依赖**：forge install

### **2. Cast - 合约交互工具**

- 与已部署合约交互
- 发送交易
- 查询区块链数据

### **3. Anvil - 本地测试网络**

- 快速启动本地以太坊节点
- 预配置的测试账户
- 可配置的区块时间

## Cast命令

<aside>
🌐

Cast命令是一种与区块链高效调用的方式，非常的方便快捷好用！



---

```bash
source .env && cast balance XXXX --rpc-url $SEPOLIA_RPC_URL
```

验证账户代币含量

---

### 部署前检查清单可使用的cast命令

```bash
# 1. 验证私钥
cast wallet address $PRIVATE_KEY

# 2. 检查余额
cast balance $(cast wallet address $PRIVATE_KEY) --rpc-url $SEPOLIA_RPC_URL

# 3. 检查网络
cast chain-id --rpc-url $SEPOLIA_RPC_URL  # 应该返回11155111 (Sepolia)

# 4. 检查gas价格
cast gas-price --rpc-url $SEPOLIA_RPC_URL
```

### cast 还可以调用合约函数

```bash
# 调用合约只读函数
cast call <合约地址> "balanceOf(address)" <账户地址> --rpc-url $RPC_URL

# 发送交易
cast send <合约地址> "transfer(address,uint256)" <接收者> <数量> --private-key $PRIVATE_KEY --rpc-url $RPC_URL
```

## 合约部署

[https://www.notion.so/ThomasToken-245a9133700780b3bfe5fd9d168da894](https://www.notion.so/ThomasToken-245a9133700780b3bfe5fd9d168da894?pvs=21)

合约的部署请参考这篇文章的部署部分！

</aside>

# 2025-08-06

## Foundry框架

状态: 进行中
创建日期: 2025年8月4日
标签: Foundry
笔记类型: 技术笔记
笔记目的: 日常学习

Foundry 需要注意的问题总结

## 什么是Foundry

Foundry 是一个智能合约开发工具链。

Foundry 管理您的依赖项，编译您的项目，运行测试，部署，并允许您通过命令行和 Solidity 脚本与链交互。

```bash
curl -L https://foundry.paradigm.xyz | bash

# Install forge, cast, anvil, chisel
foundryup
```

通过这个命令导入Foundry

## **Foundry 的核心组件**

### **1. Forge - 主要开发工具**

- **编译合约**：forge build
- **运行测试**：forge test
- **部署合约**：forge script
- **管理依赖**：forge install

### **2. Cast - 合约交互工具**

- 与已部署合约交互
- 发送交易
- 查询区块链数据

### **3. Anvil - 本地测试网络**

- 快速启动本地以太坊节点
- 预配置的测试账户
- 可配置的区块时间

# 2025-08-05

今天参加了，WEB3安全会议
1. 转账之前要多确认信息
2. 剪贴板也要注意！！！
3. RPC攻击
可以使用chainlist来防止RPC攻击
下载应用的时候一定要看清楚，放慢速度。
 假zoom
DeepFeek生成假的影片。
今天写了一个ERC20的项目，模仿PEPE币，编写了自己的ThomasToken，TT币，笔记方面用Notion构建了一套包含分类的数据库，可以更好的学习还有记笔记了。
按照计划

# 2025-08-04

今天是学习第一天，主要完成了基础工具的学习，创建了Twitter发了第一条博客，MetaMask建了新账号并领取了Sepolia ETH的测试币，阅读了远程工作的相关方法和建议，受益匪浅，明天计划开始入门导读的学习。


# 2025.08.02


<!-- Content_END -->
