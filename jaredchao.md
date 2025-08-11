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
