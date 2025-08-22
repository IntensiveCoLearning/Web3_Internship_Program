---
timezone: UTC+8
---

# jared

**GitHub ID:** jaredchao

**Telegram:** @jared1027

## Self-introduction

一个不想上班的程序员

## Notes

<!-- Content_START -->
# 2025-08-22

# ERC-1363 可支付代币

**ERC-1363** 是一个扩展ERC-20的代币标准，也被称为"Payable Token"（可支付代币）。它允许代币在转账或授权后自动执行接收者合约中的回调函数，实现"支付即执行"的功能模式。

### 核心特性
- **扩展ERC-20**：完全兼容ERC-20标准
- **支付回调**：转账后自动调用接收者函数
- **授权回调**：授权后自动调用被授权者函数
- **无需双重交易**：一次交易完成支付+执行
- **Gas优化**：减少用户交互步骤

## 问题背景

在传统的ERC-20代币使用中，用户经常需要执行两步操作：

### 传统流程的问题
```solidity
// 问题：需要两个独立的交易
// 1. 用户授权代币给合约
token.approve(contract, amount);

// 2. 调用合约函数，合约内部transferFrom
contract.someFunction();
```

### 用户体验问题
- **多步操作**：用户需要签署多个交易
- **Gas成本高**：每个交易都需要单独的Gas费用
- **复杂交互**：用户体验不够流畅
- **失败风险**：中间步骤可能失败

## ERC-1363 解决方案

ERC-1363通过扩展转账和授权函数，添加回调机制来解决这些问题：

### 核心接口
```solidity
interface IERC1363 {
    // 扩展的转账函数（带回调）
    function transferAndCall(address to, uint256 value) external returns (bool);
    function transferAndCall(address to, uint256 value, bytes calldata data) external returns (bool);
    
    // 扩展的授权函数（带回调）
    function approveAndCall(address spender, uint256 value) external returns (bool);
    function approveAndCall(address spender, uint256 value, bytes calldata data) external returns (bool);
}
```

### 接收者接口
```solidity
interface IERC1363Receiver {
    function onTransferReceived(
        address operator,
        address from, 
        uint256 value,
        bytes calldata data
    ) external returns (bytes4);
}

interface IERC1363Spender {
    function onApprovalReceived(
        address owner,
        uint256 value,
        bytes calldata data  
    ) external returns (bytes4);
}
```

## 详细接口规范

### 主合约接口 (IERC1363)

#### transferAndCall 函数
```solidity
/**
 * @dev 转账代币并调用接收者的回调函数
 * @param to 接收者地址
 * @param value 转账金额
 * @return 成功返回true
 */
function transferAndCall(address to, uint256 value) external returns (bool);

/**
 * @dev 转账代币并调用接收者的回调函数（带数据）
 * @param to 接收者地址
 * @param value 转账金额
 * @param data 传递给接收者的附加数据
 * @return 成功返回true
 */
function transferAndCall(address to, uint256 value, bytes calldata data) external returns (bool);
```

#### approveAndCall 函数
```solidity
/**
 * @dev 授权代币并调用被授权者的回调函数
 * @param spender 被授权者地址
 * @param value 授权金额
 * @return 成功返回true
 */
function approveAndCall(address spender, uint256 value) external returns (bool);

/**
 * @dev 授权代币并调用被授权者的回调函数（带数据）
 * @param spender 被授权者地址
 * @param value 授权金额
 * @param data 传递给被授权者的附加数据
 * @return 成功返回true
 */
function approveAndCall(address spender, uint256 value, bytes calldata data) external returns (bool);
```

### 接收者接口 (IERC1363Receiver)

```solidity
/**
 * @dev 处理ERC1363代币转账的回调
 * @param operator 执行转账的地址（通常是msg.sender）
 * @param from 代币发送者
 * @param value 转账金额
 * @param data 附加数据
 * @return 必须返回 `IERC1363Receiver.onTransferReceived.selector`
 */
function onTransferReceived(
    address operator,
    address from,
    uint256 value, 
    bytes calldata data
) external returns (bytes4);
```

### 被授权者接口 (IERC1363Spender)

```solidity
/**
 * @dev 处理ERC1363代币授权的回调
 * @param owner 代币拥有者
 * @param value 授权金额  
 * @param data 附加数据
 * @return 必须返回 `IERC1363Spender.onApprovalReceived.selector`
 */
function onApprovalReceived(
    address owner,
    uint256 value,
    bytes calldata data
) external returns (bytes4);
```

## 使用场景

### 适合场景
- **支付系统**：一步完成支付和服务激活
- **DEX交易**：简化交易流程
- **订阅服务**：自动续费和激活
- **质押挖矿**：简化质押操作
- **NFT购买**：一键购买NFT
- **游戏道具**：购买后自动装备

### 不适合场景
- **高频交易**：回调开销可能较大
- **简单转账**：不需要额外功能时
- **批量操作**：可能增加复杂性

### 优势
- **用户体验优化**：减少交互步骤
- **Gas成本降低**：合并操作减少交易数
- **开发友好**：简化DApp开发
- **向后兼容**：完全兼容ERC-20

# 2025-08-19

## EIP-1155 多代币

**EIP-1155** 是以太坊的多代币标准，允许单个智能合约管理多种代币类型，包括同质化代币（FT）、非同质化代币（NFT）或其他配置（如半同质化代币）。

### 核心特点
- **单合约多代币类型**：一个部署的合约可以包含任意组合的同质化、非同质化代币
- **高效批量转账**：支持一次交易转移多种代币类型
- **降低 Gas 成本**：相比多个单独的代币合约，显著减少交易费用
- **统一接口**：为所有代币类型提供标准化的操作接口

## 与现有标准的对比

| 标准 | 特点 | 局限性 |
|------|------|--------|
| ERC-20 | 同质化代币 | 每种代币需要独立合约 |
| ERC-721 | 非同质化代币 | 每个集合需要独立合约 |
| ERC-1155 | 多代币类型统一管理 | 更复杂的实现逻辑 |

## 核心接口

### 主要函数

#### 1. 单个代币转账
```solidity
function safeTransferFrom(
    address _from,
    address _to, 
    uint256 _id,
    uint256 _value,
    bytes calldata _data
) external;
```

#### 2. 批量代币转账
```solidity
function safeBatchTransferFrom(
    address _from,
    address _to,
    uint256[] calldata _ids,
    uint256[] calldata _values, 
    bytes calldata _data
) external;
```

#### 3. 余额查询
```solidity
// 单个余额查询
function balanceOf(address _owner, uint256 _id) external view returns (uint256);

// 批量余额查询
function balanceOfBatch(
    address[] calldata _owners,
    uint256[] calldata _ids
) external view returns (uint256[] memory);
```

#### 4. 授权管理
```solidity
// 设置全部代币授权
function setApprovalForAll(address _operator, bool _approved) external;

// 查询授权状态
function isApprovedForAll(address _owner, address _operator) external view returns (bool);
```

### 重要事件

#### TransferSingle 事件
```solidity
event TransferSingle(
    address indexed _operator,
    address indexed _from, 
    address indexed _to,
    uint256 _id,
    uint256 _value
);
```

#### TransferBatch 事件
```solidity
event TransferBatch(
    address indexed _operator,
    address indexed _from,
    address indexed _to, 
    uint256[] _ids,
    uint256[] _values
);
```

## 接收者合约（Token Receiver）

接收 ERC-1155 代币的智能合约必须实现 `ERC1155TokenReceiver` 接口：

### 单个代币接收
```solidity
function onERC1155Received(
    address _operator,
    address _from,
    uint256 _id,
    uint256 _value,
    bytes calldata _data
) external returns (bytes4);
```

### 批量代币接收
```solidity
function onERC1155BatchReceived(
    address _operator, 
    address _from,
    uint256[] calldata _ids,
    uint256[] calldata _values,
    bytes calldata _data
) external returns (bytes4);
```

## 安全转账规则

### 关键规则
1. **权限检查**：调用者必须获得转出账户的授权
2. **地址验证**：接收地址不能是零地址
3. **余额验证**：转出余额必须充足
4. **事件发出**：必须发出相应的转账事件
5. **合约检查**：如果接收者是合约，必须调用相应的钩子函数

### 安全检查流程
1. 验证转账参数有效性
2. 检查授权和余额
3. 更新余额状态
4. 发出转账事件
5. 如果接收者是合约，调用接收钩子
6. 验证钩子返回值

## 元数据标准

### URI 模板
代币元数据 URI 支持 ID 替换：
```
https://token-cdn-domain/{id}.json
```
其中 `{id}` 会被替换为64位十六进制代币ID（小写，补零）。

### JSON Schema
```json
{
  "name": "代币名称",
  "decimals": 18,
  "description": "代币描述", 
  "image": "https://example.com/image/{id}.png",
  "properties": {
    "custom_property": "自定义属性值"
  }
}
```

# 2025-08-18

##### ERC721
- **定义**：**ERC-721** 是以太坊上用于**非同质化代币（NFT）**的标准协议
	- - **NFT（Non-Fungible Token）** 具有**唯一性**，每个 Token 都有独立的 ID 和属性，不可互换
- **作用**：与 ERC-20（同质化代币）不同，ERC-721 适用于**数字艺术品、游戏道具、域名、身份认证**等场景。

##### 非同质化代币
如果同一个集合的两个物品具有不同的特征，这两个物品是非同质的，而同质是某个部分或数量可以被另一个同等部分或数量所代替。

非同质性其实广泛存在于我们的生活中，如图书馆的每一本，宠物商店的每一只宠物，歌手所演唱的歌曲，花店里不同的花等等，因此ERC721合约必定有广泛的应用场景。通过这样一个标准，也可建立跨功能的NFTs管理和销售平台（就像有支持ERC20的交易所和钱包一样），使生态更加强大。


每个符合 ERC-721 的合约都必须实现 ERC721 和 ERC165 接口

##### 必需函数

1. `balanceOf(address owner)` - 查询地址拥有的NFT数量
```
  function balanceOf(address _owner) external view returns (uint256);
```
	


2. `ownerOf(uint256 tokenId)` - 查询NFT所有者
```
function ownerOf(uint256 _tokenId) external view returns (address);
```


3. `safeTransferFrom(address from, address to, uint256 tokenId)` - 安全转账
```
  function safeTransferFrom(address _from, address _to, uint256 _tokenId) external payable;
```

4. `transferFrom(address from, address to, uint256 tokenId)` - 普通转账
```
function transferFrom(address _from, address _to, uint256 _tokenId) external payable;
```

5. `approve(address to, uint256 tokenId)` - 授权单个NFT
```
 function approve(address _approved, uint256 _tokenId) external payable;
```

6. `setApprovalForAll(address operator, bool approved)` - 批量授权

```
  function setApprovalForAll(address _operator, bool _approved) external;
```

7. `getApproved(uint256 tokenId)` - 查询单个授权
```
function getApproved(uint256 _tokenId) external view returns (address);
```

8. `isApprovedForAll(address owner, address operator)` - 查询批量授权
```
  function isApprovedForAll(address _owner, address _operator) external view returns (bool);

```

##### 必须事件
1. 所有权转移事件
```
event Transfer(address indexed _from, address indexed _to, uint256 indexed _tokenId);
```
2. 单个授权事件
```
event Approval(address indexed _owner, address indexed _approved, uint256 indexed _tokenId);
```
3. 批量授权事件
```
 event ApprovalForAll(address indexed _owner, address indexed _operator, bool _approved);
```
每个符合 ERC-721 的合约都必须实现 ERC721 和 ERC165 接口
```
interface ERC165 {
    /// @notice Query if a contract implements an interface
    /// @param interfaceID The interface identifier, as specified in ERC-165
    /// @dev Interface identification is specified in ERC-165. This function
    ///  uses less than 30,000 gas.
    /// @return `true` if the contract implements `interfaceID` and
    ///  `interfaceID` is not 0xffffffff, `false` otherwise
    function supportsInterface(bytes4 interfaceID) external view returns (bool);
}
```
如果钱包/经纪人/拍卖应用程序要接受安全转账，则必须实现钱包接口。
```
/// @dev Note: the ERC-165 identifier for this interface is 0x150b7a02.
interface ERC721TokenReceiver {

    function onERC721Received(address _operator, address _from, uint256 _tokenId, bytes _data) external returns(bytes4);
}
```

对于 ERC-721 智能合约，可选的元数据 。这允许查询您的智能合约的名称以及您的 NFT 所代表的资产的详细信息。
```
interface ERC721Metadata /* is ERC721 */ {
    /// @notice A descriptive name for a collection of NFTs in this contract
    function name() external view returns (string _name);

    /// @notice An abbreviated name for NFTs in this contract
    function symbol() external view returns (string _symbol);

    /// @notice A distinct Uniform Resource Identifier (URI) for a given asset.
    /// @dev Throws if `_tokenId` is not a valid NFT. URIs are defined in RFC
    ///  3986. The URI may point to a JSON file that conforms to the "ERC721
    ///  Metadata JSON Schema".
    function tokenURI(uint256 _tokenId) external view returns (string);
}
```
元数据json
```
{
    "title": "Asset Metadata",
    "type": "object",
    "properties": {
        "name": {
            "type": "string",
            "description": "Identifies the asset to which this NFT represents"
        },
        "description": {
            "type": "string",
            "description": "Describes the asset to which this NFT represents"
        },
        "image": {
            "type": "string",
            "description": "A URI pointing to a resource with mime type image/* representing the asset to which this NFT represents. Consider making any images at a width between 320 and 1080 pixels and aspect ratio between 1.91:1 and 4:5 inclusive."
        }
    }
}
```

# 2025-08-16

#### ERC20
- **定义**：ERC20是以太坊上的代币标准，规定了代币合约的基本接口和功能。
- **作用**：确保代币在以太坊生态中的兼容性，使钱包、交易所等平台能够统一支持不同项目发行的代币。

#### ERC-20 核心功能
ERC20 标准要求代币合约必须实现以下 6 个基本函数和 2 个事件：

##### 必需函数

1. **totalSupply()** - 返回代币的总供应量
   ```solidity
   function totalSupply() public view returns (uint256)
   ```

2. **balanceOf(address _owner)** - 返回特定地址的代币余额
   ```solidity
   function balanceOf(address _owner) public view returns (uint256 balance)
   ```

3. **transfer(address _to, uint256 _value)** - 向指定地址转移代币
   ```solidity
   function transfer(address _to, uint256 _value) public returns (bool success)
   ```

4. **transferFrom(address _from, address _to, uint256 _value)** - 从指定地址向另一个地址转移代币（需配合approve使用）
   ```solidity
   function transferFrom(address _from, address _to, uint256 _value) public returns (bool success)
   ```

5. **approve(address _spender, uint256 _value)** - 允许指定地址花费一定数量的代币
   ```solidity
   function approve(address _spender, uint256 _value) public returns (bool success)
   ```

6. **allowance(address _owner, address _spender)** - 返回授权者允许被授权者花费的代币数量
   ```solidity
   function allowance(address _owner, address _spender) public view returns (uint256 remaining)
   ```

##### 必需事件

1. **Transfer** - 当代币转移时触发（包括零值转移）
   ```solidity
   event Transfer(address indexed _from, address indexed _to, uint256 _value)
   ```

2. **Approval** - 当代币授权时触发
   ```solidity
   event Approval(address indexed _owner, address indexed _spender, uint256 _value)
   ```

#### ERC20 使用场景
1. **代币发行** - 创建新的加密货币
2. **众筹/ICO** - 通过代币销售筹集资金
3. **治理代币** - DAO 治理投票
4. **奖励系统** - 用户激励
5. **资产代币化** - 将实物资产表示为代币

# 2025-08-14

Uniswap V4合约

仅明白的
1. 依赖管理使用 soldeer  
2. 组合函数优于继承
3. 尽量不写view函数，节省合约体积

入门先学 erc20  再去看其他 uniswap v2 、v3、 safe 合约

# 2025-08-13

##漏洞修复
Ethernaut 第二关的合约 

漏洞分析 

1. 你的贡献超过 owner 的贡献就可以成为获得所有权
2. 需要 触发receive 获得所有权
	a. 先通过contribute转小于 0.001 的交易 在 contributions 有你的记录
	a. 触发receive的条件
		i. 向合约地址发送一笔不带 data 的交易就可以

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {
    mapping(address => uint256) public contributions;
    address public owner;

    constructor() {
        owner = msg.sender;
        contributions[msg.sender] = 1000 * (1 ether);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "caller is not the owner");
        _;
    }

    function contribute() public payable {
        require(msg.value < 0.001 ether);
        contributions[msg.sender] += msg.value;
        if (contributions[msg.sender] > contributions[owner]) {
            owner = msg.sender;
        }
    }

    function getContribution() public view returns (uint256) {
        return contributions[msg.sender];
    }

    function withdraw() public onlyOwner {
        payable(owner).transfer(address(this).balance);
    }

// 注释掉receive 
 //   receive() external payable {
   //     require(msg.value > 0 && contributions[msg.sender] > 0);
   //     owner = msg.sender;
// }
}

# 2025-08-12

https://github.com/jaredchao/hello-Contract/blob/main/packages/contracts/src/HelloWorld.sol

1. 无用的变量 string memory oldGreeting = greeting; 会额外的消耗内存
2. 问候语设置可以限制长度可以使用固定的长度来 减少内存存储 减少gas消耗

3. external修饰符 与public 的区别
  1. external修饰符仅仅可以从外部调用， public 内部和外部都可以调用
  2. external的参数直接从calldata中获取 public 的参数从 calldata中复制到内存中

4. 优化字符串字面量 ，使用常量代替

# 2025-08-11

使用 Foundry 开发简单 hello 合约 部署到sepolia 测试网

github ： https://github.com/jaredchao/hello-Contract


## 🏗️ 项目架构

### Monorepo 结构
```
hello-contract/
├── packages/
│   ├── contracts/    # 智能合约 (Foundry)
│   └── frontend/     # 前端应用 (Next.js + Wagmi)
├── docs/            # 技术文档
├── package.json     # 根配置
└── pnpm-workspace.yaml  # 工作空间配置
```

### 技术栈选择

#### 智能合约部分
- **Foundry**: 现代化合约开发框架
  - 快速编译和测试
  - 内置测试框架
  - 强大的部署工具
- **Solidity ^0.8.13**: 智能合约语言
- **Anvil**: 本地区块链测试网络

#### 前端部分  
- **Next.js 15**: React 全栈框架
  - App Router 架构
  - 服务端渲染优化
- **TypeScript**: 类型安全开发
- **Wagmi v2**: React 以太坊交互库
- **RainbowKit**: 钱包连接 UI 组件
- **Tailwind CSS v4**: 原子化样式框架

#### 开发工具
- **pnpm**: 高效包管理器
- **ESLint**: 代码质量检查

## 🔧 核心技术实现

### 1. 智能合约开发 (HelloWorld.sol)

#### 合约结构
```solidity
contract HelloWorld {
    string private greeting;    // 私有状态变量
    address public owner;      // 公开所有者地址
    
    event GreetingChanged(string newGreeting, address changedBy);
    
    constructor() {
        greeting = "Hello World";
        owner = msg.sender;
    }
}
```

#### 核心功能实现
```solidity
// 1. 只读函数 - 获取问候语
function getHello() public view returns (string memory) {
    return greeting;
}

// 2. 写入函数 - 设置问候语
function setGreeting(string memory _newGreeting) public {
    string memory oldGreeting = greeting;
    greeting = _newGreeting;
    emit GreetingChanged(_newGreeting, msg.sender);  // 触发事件
}

// 3. 重置功能
function resetGreeting() public {
    greeting = "Hello World";
    emit GreetingChanged("Hello World", msg.sender);
}

// 4. 获取所有者
function getOwner() public view returns (address) {
    return owner;
}
``` 



#### Wagmi 配置 (wagmi.ts)
```typescript
import { getDefaultConfig } from '@rainbow-me/rainbowkit'
import { localhost, sepolia } from 'wagmi/chains'

// 链配置
const chains = [{
  id: 31337,
  name: 'Localhost',
  nativeCurrency: { decimals: 18, name: 'Ether', symbol: 'ETH' },
  rpcUrls: { default: { http: ['http://localhost:8545'] } },
  testnet: true,
}, sepolia]

// Wagmi 配置
export const config = getDefaultConfig({
  appName: 'Hello Contract DApp',
  projectId: process.env.NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID,
  chains,
})

// 合约配置
export const helloWorldContract = {
  address: process.env.NEXT_PUBLIC_CONTRACT_ADDRESS as `0x${string}`,
  abi: [...],  // 完整 ABI 定义
}
```

#### 钱包连接组件 (WalletConnect.tsx)
```typescript
import { ConnectButton } from '@rainbow-me/rainbowkit'

export default function WalletConnect() {
  return (
    <div className="flex flex-col items-center space-y-4">
      <ConnectButton />
    </div>
  )
}
```

#### 合约交互组件 (HelloContract.tsx)
```typescript
import { useReadContract, useWriteContract } from 'wagmi'
import { helloWorldContract } from '@/lib/wagmi'

export default function HelloContract() {
  // 读取合约数据
  const { data: greeting, refetch } = useReadContract({
    ...helloWorldContract,
    functionName: 'getHello',
  })

  // 写入合约数据
  const { writeContract, isPending } = useWriteContract()

  const handleSetGreeting = (newGreeting: string) => {
    writeContract({
      ...helloWorldContract,
      functionName: 'setGreeting',
      args: [newGreeting],
    })
  }

  return (
    <div className="space-y-4">
      <p>当前问候语: {greeting}</p>
      <button onClick={() => refetch()}>
        刷新
      </button>
      <input 
        type="text" 
        onChange={(e) => setNewGreeting(e.target.value)}
      />
      <button 
        onClick={() => handleSetGreeting(newGreeting)}
        disabled={isPending}
      >
        {isPending ? '处理中...' : '设置问候语'}
      </button>
    </div>
  )
}
```

# 2025-08-10

## Foundry 学习

### 1. 核心概念

* **forge**：编译、测试、部署
* **cast**：命令行交互（读/写链上数据）
* **anvil**：本地 EVM 节点（支持主网分叉）
* **chisel**：Solidity REPL

---

### 2. 安装

```bash
curl -L https://foundry.paradigm.xyz | bash
```

---

### 3. 基本流程

```bash
forge init MyProject   # 初始化项目
forge build            # 编译
forge test             # 运行测试
forge script ...       # 部署脚本
```

---

### 4. 测试要点

* 测试文件：`*.t.sol`
* 函数名以 `test` 开头
* 支持 **fuzz 测试**、**cheatcodes**、**主网分叉测试**

---

### 5. 常用命令

```bash
forge build              # 编译
forge test               # 测试
forge test -vvvv         # 打印调试信息
anvil                    # 启动本地节点
cast call ...            # 调用合约
```

# 2025-08-08

https://github.com/lxdao-official/myfirstnft-backend/blob/main/utils/uploadToIPFS.js

学到怎么把资源上传的ipfs
需要先获取到 infura 的key 和 secret
把资源转成base64，通过 infura 的 ipfs接口 https://ipfs.infura.io:5001/api/v0/add 上传到 ipfs 

参数是formData 格式 ， 返回值是资源的 hash 值.

# 2025-08-07

Web3岗位方面法律相关

技术上识别合规， 是否封锁大陆IP， 注册是否需要较高难度的方式


KYC（客户认证）——证明“你是谁”

查身份证（护照/驾照+人脸识别）

验住址（水电账单/银行证明）

问用途（钱从哪来？用来干嘛？）

不做KYC = 高危（洗钱、诈骗、非法集资温床）

（核心：真人真钱真用途，否则违法！）

AML（反洗钱） 是防止非法资金（如黑钱、恐怖融资）进入金融体系的合规措施，核心包括：

KYC（实名认证用户）

监控交易（查异常大额/频繁转账）

筛查黑名单（冻结受制裁账户）

上报可疑活动（否则可能违法）

区块链行业必须做AML，否则可能被重罚或封杀。
危险信号：无需KYC、支持匿名币、办公地模糊。

（一句话：谁的钱？哪来的？去哪了？ 说不清就是高风险。）

1. 项目存在传销性质不去、存在多级返佣，动态收益大于静态收益，社区拉人头等
2. 有赌博特征，NFT高额交易

# 2025-08-06

- HTML5
- CSS3
- JavaScript (ES6+)
- React / Vue
- TypeScript
- Next.js
- Ethers.js / Web3.js / Viem / 

web3 前端开发技术栈  与 web2 前端  多了 Ethers.js / Web3.js / Viem/ Wagmi 库的使用。

包含钱包调用，合约调用。

缺乏前置知识， 看 api 比较懵。

# 2025-08-05

以太坊是什么？

以太坊是开源的去中心化区块链平台， 原生的加密货币是 ETH （Ether）。  提供了 evm 虚拟机来处理合约
核心创新是 智能合约， 合约是存储在链上的可执行代码。 可以构建 Dapp 应用。


以太坊和比特币的区别

| **维度**    | **比特币（Bitcoin）**                  | **以太坊（Ethereum）**                       |
| --------- | --------------------------------- | --------------------------------------- |
| **目标与定位** | 去中心化的数字货币，强调安全、稳定和稀缺性（总量 2100 万枚） | 去中心化平台，支持智能合约和 Dapps，定位为“区块链 2.0”       |
| **编程能力**  | 脚本语言有限，仅支持简单的交易验证逻辑               | 图灵完备的编程语言（如 Solidity），可开发复杂智能合约         |
| **共识机制**  | 工作量证明（PoW），矿工通过算力竞争记账权            | 从 PoW 转向权益证明（PoS），通过 The Merge 实现能源效率优化 |
| **交易速度**  | 每 10 分钟生成一个区块，交易确认较慢              | 区块时间约 12 秒，交易确认更快，适合高频应用                |
| **经济模型**  | 总量固定，强调抗通胀属性                      | 供应灵活，通过 EIP-1559 等机制可能呈现通缩趋势            |

pos 机制
1. 张三质押代币成为验证者获得权利
2. 系统随机选择验证者来提议新区块，其他验证者验证有效
3. 成功出块 获得代币奖励+gas 费
4. 恶意行为 销毁质押的代币

# 2025-08-04

## 什么是区块链
可以理解为一个记账本，所有人都在这个账本上记录信息， 所记录的信息谁都可以查阅，且无法更改。 
## 什么是区块
账本上每一页可以理解为一个区块。 所记录的信息就是区块信息。
1. 区块头
	1. 当前区块号
	2. 上一次的区块hash
	3. 当前区块hash
	4. 时间戳
2. 交易数据
	1.  交易记录
		1. 张三给李四转账 0.1BTC
		2. 手续费0.0001BTC
	
## 为什么区块链不可篡改？
每个区块都带有hash值，这个区块的hash值是由，当前区块的交易记录和上一个区块的hash值加密计算生成的。如果修改一个区块的的记录。必须将后面的所有区块的hash全部更改。

## 区块链的运行
1. **用户发起交易**
	1. 张三使用metamask给李四转账1BTC，并支付手续费
2. **交易广播**
	1. 张三的交易被发送到网络，所有的节点都能收到
3. **节点验证**
	1. 节点的矿工检查交易是否合法， 
		1. 余额是否足够
		2. 签名是否正确
		3. gas费给的够不够。给的多就快
4. **打包成块**
	1. 矿工通过共识机制将交易打包成区块
		1. pow 通过计算，谁计算的快就是谁的
		2. pos 通过质押， 谁质押的多就是谁的
5. **链接上链**
	1. 新区块被添加到区块链上，更新账本
6. **奖励发放**
	1. 成功打包区块的矿工获得代币奖励和交易手续费


# 2025.07.29


<!-- Content_END -->
