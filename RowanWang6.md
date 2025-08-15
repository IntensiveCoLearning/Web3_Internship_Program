---
timezone: UTC+8
---

# Rowan

**GitHub ID:** RowanWang6

**Telegram:** @BINgoN11

## Self-introduction

Web2金融行业转型中，做kyc以及反洗钱，对这方面比较感兴趣

## Notes

<!-- Content_START -->
# 2025-08-15

##React + Wagmi + Anvil 改造计划
把现有的React/Vue + wagmi/web3.js + hardhat改造，看上去只需要修改anvil，但坑好多。

##改造步骤

1. 替换 Hardhat 网络为 Anvil

```
 #安装 Foundry (包含 Anvil)
  curl -L https://foundry.paradigm.xyz | bash
  foundryup
```

  2. 修改开发流程

  // package.json 新增脚本
  {
    "scripts": {
      "anvil": "anvil --host 0.0.0.0 --port 8545 --accounts 10 --balance 10000",
      "dev:full": "concurrently \"npm run anvil\" \"npm run dev\" \"cd frontend-react
   && npm run dev\""
    }
  }

  3. 更新 Wagmi 配置

  // frontend-react/src/config/wagmi.ts
  import { createConfig, http } from 'wagmi'
  import { foundry } from 'wagmi/chains'
  import { injected, metaMask, safe, walletConnect } from 'wagmi/connectors'

  export const config = createConfig({
    chains: [foundry],
    connectors: [
      injected(),
      metaMask(),
      safe(),
    ],
    transports: {
      [foundry.id]: http('http://127.0.0.1:8545'),
    },
  })

  4. 优化部署脚本

  // scripts/anvil-deploy.js
  const hre = require("hardhat");

  async function main() {
    // 使用 Anvil 的固定地址进行部署
    const [deployer] = await hre.ethers.getSigners();
    console.log("Deploying with account:", deployer.address);

    const MyToken = await hre.ethers.getContractFactory("MyToken");
    const token = await MyToken.deploy();

    console.log("Token deployed to:", await token.getAddress());
  }


  5. 改进 DX (开发者体验)

  // frontend-react/src/hooks/useContract.ts
  import { useReadContract, useWriteContract } from 'wagmi'
  import { CONTRACT_ADDRESS, CONTRACT_ABI } from '../config/wagmi'

  export function useTokenContract() {
    const { writeContract } = useWriteContract()

    const mint = (to: string, amount: bigint) => {
      return writeContract({
        address: CONTRACT_ADDRESS,
        abi: CONTRACT_ABI,
        functionName: 'mint',
        args: [to, amount],
      })
    }

    return { mint }
  }

# 2025-08-14

https://github.com/RowanWang6/hardhat-beibei-coin-tutorialDapp
技术向交作业走过路过不要错过呀
## BeiBeiCoin (BBC) DApp 🪙

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Hardhat](https://img.shields.io/badge/Built%20with-Hardhat-yellow.svg)](https://hardhat.org/)
[![React](https://img.shields.io/badge/Frontend-React%2019-blue.svg)](https://reactjs.org/)
[![Vue](https://img.shields.io/badge/Frontend-Vue%203-green.svg)](https://vuejs.org/)
[![Ethers](https://img.shields.io/badge/Web3-Ethers.js%20v6-purple.svg)](https://docs.ethers.org/)

> 一个功能完整的 ERC-20 代币 DApp，提供铸造、转账、授权、销毁等功能，支持 React 和 Vue 两个前端版本

## 📖 项目简介

BeiBeiCoin (BBC) 是一个基于以太坊的 ERC-20 代币项目，提供完整的智能合约和现代化的前端界面。项目包含：

- **智能合约**: 基于 Solidity 的 ERC-20 代币合约，支持铸造和销毁功能
- **React 前端**: 使用 React 19 + RainbowKit + Wagmi v2 构建的现代化 Web3 界面
- **Vue 前端**: 使用 Vue 3 + ethers.js v6 构建的原生 Web3 连接界面
- **本地开发**: 完整的 Hardhat 开发环境配置

## ✨ 主要特性

### 🔒 智能合约功能

- ✅ 标准 ERC-20 代币实现
- ✅ 所有者权限的代币铸造
- ✅ 代币销毁功能
- ✅ 转账和授权机制
- ✅ 完整的事件日志

### 🎨 前端特性

- ✅ 双前端支持（React + Vue）
- ✅ 现代化响应式 UI 设计
- ✅ 实时余额更新
- ✅ 完整的错误处理
- ✅ 网络自动检测和切换
- ✅ 交易状态跟踪

## 🏗️ 技术架构

### 智能合约层

```
MyToken.sol (ERC-20)
├── 铸造功能 (仅所有者)
├── 转账功能
├── 授权机制
├── 销毁功能
└── 余额查询
```

### React

```
React 19 + TypeScript
├── RainbowKit (钱包连接)
├── Wagmi v2 (Web3 Hooks)
├── Viem (以太坊客户端)
├── TanStack Query (数据获取)
├── Tailwind CSS (样式)
└── Lucide React (图标)
```

### Vue

```
Vue 3.5.17 + TypeScript
├── ethers.js v6.15.0 (Web3库)
├── BrowserProvider (钱包连接)
├── Vite 6.x (构建工具)
├── Tailwind CSS (样式)
└── Composition API (状态管理)
```

## 实现与改进
主要实现了ERC20，mint、transfer一系列基础功能，在这基础上解决了一些技术bug。
### 想用固定地址部署合约
写了一大堆脚本，最后发现是nonce的原因，地址生成有nonce参与，在不停止hardhat节点的情况下，多次部署会导致nonce增加，因此永远不可能获得相同的地址。到最后的解决方法只找到重新部署前应先停止 hardhat node。或许wagmi+anvil可以解决这个问题，待学习。


#### Vue 版本 ethers.js 问题
在开发中感觉vue在web3里确实不是主流，有很多机制web3都没有适配。比如
```
❌ Cannot read private member #notReady 
```
一些私有属性，在vue3的响应式方法里读取是会报错的，虽然可以使用 BrowserProvider 替代 provider.ready，但谁让我恰好又写了react版本，在react里就没有类似问题。

#### 缓存问题
很多时候需要打开浏览器控制台后清除缓存的刷新，这个也看很多帖子里提到过，具体怎么解决还在研究中，可能也不是一个影响生产的问题。

# 2025-08-12

## 一些Solidity笔记
## 函数 Returns 和 Return

Solidity 中与函数输出相关的有两个关键字：`return`和`returns`。它们的区别在于：

- `returns`：跟在函数名后面，用于声明返回的变量类型及变量名。
- `return`：用于函数主体中，返回指定的变量。

```
// 返回多个变量
function returnMultiple() public pure returns(uint256, bool, uint256[3] memory){
    return(1, true, [uint256(1),2,5]);
}
```

在上述代码中，我们利用 `returns` 关键字声明了有多个返回值的 `returnMultiple()` 函数，然后我们在函数主体中使用 `return(1, true, [uint256(1),2,5])` 确定了返回值。

这里`uint256[3]`声明了一个长度为`3`且类型为`uint256`的数组作为返回值。因为`[1,2,3]`会默认为`uint8(3)`，因此`[uint256(1),2,5]`中首个元素必须强转`uint256`来声明该数组内的元素皆为此类型。数组类型返回值默认必须用 memory 修饰。
### 数据位置

Solidity 数据存储位置有三类：`storage`，`memory`和`calldata`。不同存储位置的`gas`成本不同。`storage`类型的数据存在链上，类似计算机的硬盘，消耗`gas`多；`memory`和`calldata`类型的临时存在内存里，消耗`gas`少。整体消耗`gas`从多到少依次为：`storage` > `memory` > `calldata`。大致用法：

1.

`storage`：合约里的状态变量默认都是`storage`，存储在链上。

2.

`memory`：函数里的参数和临时变量一般用`memory`，存储在内存中，不上链。尤其是如果返回数据类型是变长的情况下，必须加 memory 修饰，例如：string, bytes, array 和自定义结构。

3.

`calldata`：和`memory`类似，存储在内存中，不上链。与`memory`的不同点在于`calldata`变量不能修改（`immutable`），一般用于函数的参数。例子：

```
function fCalldata(uint[] calldata _x) public pure returns(uint[] calldata){
    //参数为calldata数组，不能被修改
    // _x[0] = 0 //这样修改会报错
    return(_x);
}
```

**Example:**

![5-1.png](https://www.wtf.academy/_next/image?url=https%3A%2F%2Fstatic.wtf.academy%2Fimage%2Fb397b502f088d34dad8eff7be5670210.png&w=3840&q=75)

#### 数据位置和赋值规则

在不同存储类型相互赋值时候，有时会产生独立的副本（修改新变量不会影响原变量），有时会产生引用（修改新变量会影响原变量）。规则如下：

-

赋值本质上是创建**引用**指向本体，因此修改本体或者是引用，变化可以被同步：

- `storage`（合约的状态变量）赋值给本地`storage`（函数里的）时候，会创建引用，改变新变量会影响原变量。例子：

  ```
  uint[] x = [1,2,3]; // 状态变量：数组 x

  function fStorage() public{
      //声明一个storage的变量 xStorage，指向x。修改xStorage也会影响x
      uint[] storage xStorage = x;
      xStorage[0] = 100;
  }
  ```

  **Example:**

  ![5-2.png](https://www.wtf.academy/_next/image?url=https%3A%2F%2Fstatic.wtf.academy%2Fimage%2F7b28744dddb57264a6373f873f331a1e.png&w=3840&q=75)

- `memory`赋值给`memory`，会创建引用，改变新变量会影响原变量。

-

其他情况下，赋值创建的是本体的副本，即对二者之一的修改，并不会同步到另一方。这有时会涉及到开发中的问题，比如从`storage`中读取数据，赋值给`memory`，然后修改`memory`的数据，但如果没有将`memory`的数据赋值回`storage`，那么`storage`的数据是不会改变的。

## 事件

`event` 是智能合约告诉外部世界“刚才我干了什么”的方式，用来打日志、提供链上行为的追踪线索。

`Solidity`中的事件（`event`）是`EVM`上日志的抽象，它具有两个特点：

- 响应：应用程序（[`ethers.js`](https://learnblockchain.cn/docs/ethers.js/api-contract.html#id18)）可以通过`RPC`接口订阅和监听这些事件，并在前端做响应。
- 经济：事件是`EVM`上比较经济的存储数据的方式，每个大概消耗 2,000 `gas`；相比之下，链上存储一个新变量至少需要 20,000 `gas`。

# 2025-08-11

## 社区运营核心职责

### 日常内容与社群维护
- 定期更新社媒内容，保持活跃度。
- 管理社群秩序：答疑、禁言、打击垃圾内容。
- 引导话题讨论，促进用户参与。

### 内容发布与互动引导
- 活动预热公告、AMA 宣传、Twitter Space 提醒。
- 转发与品牌调性契合的内容，并多渠道推送。

### 活动策划与组织
- **线上活动**：Twitter Space、线上课程、线上黑客松等。
- **线下活动**：Meetup、Workshop、黑客松等。
- 活动前：策划主题、邀约嘉宾、设计海报与推文。
- 活动中：主持互动、记录要点。
- 活动后：内容总结、发推、媒体投稿。

### 对外合作与社区联动
- 联合其他社区开展 AMA 或活动。
- 与 KOL 保持沟通，争取转发与联名。
- 联系媒体进行活动快讯与曝光。

---

## 常用工具与平台

### 社交媒体渠道
| 渠道 | 用途 |
| --- | --- |
| Twitter (X) | 更新项目、活动传播 |
| 微信公众号 | 中文市场推广 |
| Medium / Substack | 长文、进展发布 |

### 社群沟通平台
| 平台 | 用途 |
| --- | --- |
| Discord | 社区主阵地，分频道管理 |
| Telegram | AMA、用户支持 |
| 微信群 | 中国用户日常互动 |

### 内容创作工具
| 工具 | 功能 |
| --- | --- |
| Notion / Notion AI | 文档整理、活动计划 |
| ChatGPT | 文案生成、翻译 |
| Figma / Canva | 海报与视觉设计 |
| Tally / Typeform | 调研问卷、报名表 |

### 数据与行业调研工具
| 工具 | 用途 |
| --- | --- |
| Etherscan | 交易与合约查看 |
| Dune Analytics | 链上数据可视化 |
| CoinGecko / CMC | 币价监控 |
| Token Terminal | 项目财务与用户增长分析 |

---

## 常见任务案例模板

### 线上活动执行模板
**案例**：ETHPanda 在 Pectra 升级节点的 Twitter Space  
**目标**：宣传升级、增加社区曝光  
**预期**：Twitter 粉丝 +200，TG 社群 +100

**时间节点流程**：
- **T-5 ~ T-4 天**：确定主题、嘉宾、主持、问题库，设计海报与文案。
- **T-4 ~ T-2 天**：第一波宣发，多渠道同步。
- **T-1 天 ~ 当天**：第二波预热，活动当天提前提醒嘉宾与社群。
- **执行流程**：开场介绍 → 嘉宾问答 → 观众提问 → 活动奖励。
- **T+7 日内**：数据复盘、优化建议。

---

### 线下活动策划与执行
**流程**：
1. **策划阶段（提前 4-5 周）**
   - 明确主题与形式（Keynote、Panel、Workshop 等）。
   - 设定目标（人数、社媒增长、合作机会）。
   - 制定预算与分工，确认合规事项。

2. **筹备阶段（活动前 2-4 周）**
   - 制定活动流程与邀约嘉宾。
   - 准备宣传物料与社媒日历。
   - 联动媒体与合作社区。
   - 周边物资采购与物流安排。

3. **执行阶段（活动日）**
   - 场地布置与技术调试。
   - 嘉宾接待与观众签到。
   - 分工明确，设立应急预案。

4. **复盘阶段（活动后一周内）**
   - 产出 Recap 内容（推特 thread、公众号、视频剪辑）。
   - 数据与效果分析（到场人数、曝光量、互动量）。
   - 总结成功要素与待优化项。

---

# 2025-08-10

## 智能合约的 Gas 费优化
智能合约的 Gas 费高低，和代码写法、逻辑设计直接相关。感觉是变相的强制优化代码，毕竟能省一点是一点。总结以下几点：
### 精简代码
- 真正使用公共代码：从接触开发到现在，每个项目基本都有公共functions或者类似的东西，只能说用了但没完全用。要么就是其他开发者没阅读完代码就开始干活了，总之重复的内容还是写了很多。
- 减少不必要计算：合约理有些临时变量只在调试时有用，正式部署前要删掉，多余的计算会白白消耗 Gas。
### 精简存储空间
- 链上存储：链上存储（比如storage变量）比内存（memory）费 Gas 得多。如果数据不需要长期存在链上，尽量用内存临时存储，用完就清。比如处理临时数据时，先用内存变量运算，最后只把结果存到链上。
优化变量类型：选合适的变量类型能省空间。比如用uint256够的话，别用更大的类型；数组长度固定时，用固定长度数组比动态数组更省 Gas。另外，把多个小变量按 “打包规则” 排列，比如几个uint128放一起，能减少存储占用。
### 函数设计
- 少用外部调用：调用其他合约的函数（尤其是外部合约）会增加 Gas 消耗，能自己实现的逻辑就别依赖外部合约。如果必须调用，先确认对方合约可靠，避免重复调用同一个合约。
- 优化函数可见性：函数的可见性（public/private/internal/external）会影响 Gas。比如内部用的函数设为private或internal，比public更省 Gas；外部交互的函数用external，比public更高效。
- 循环：循环里如果处理大量数据（比如遍历长数组），Gas 费会飙升，甚至可能超过区块 Gas 上限导致交易失败。尽量限制循环次数，或拆分处理步骤。
- 用自定义错误代替字符串报错：合约里报错时，用solidity 0.8.4以上支持的自定义错误（比如error InvalidAddress()），比require带字符串提示更省 Gas，字符串越长省得越多。
测试时测 Gas 消耗：部署前用工具（比如 Hardhat 的 gas reporter 插件）测每个函数的 Gas 消耗，针对性优化消耗高的部分。

# 2025-08-09

## 方向确认
主前端方向 + solidity
### vue3 + web3.js + hardhat实现DApp
可以本地测试，实现了ERC20的基本功能
![BeiBeiCoin](https://github.com/RowanWang6/hardhat-beibei-coin-tutorialDapp/raw/main/docs/images/react-dashboard.png)
### 部署测试网
#### 测试网意义
- 结构与主网一致，但代币无经济价值。
- 用于验证合约功能、稳定性、安全性，避免高额主网 Gas 损失。
- 成功运行后再迁移主网，可降低研发与运维成本。
- 可通过 Etherscan 等区块浏览器查看部署信息。

#### 主要测试网
| 名称     | 共识 | 状态 | 特点 | 场景 |
|----------|------|------|------|------|
| Sepolia  | PoS  | 活跃 | 稳定，长期支持，接近主网 | 最终部署前测试、Dapp 集成 |
| Holesky  | PoS  | 活跃 | 面向验证者，大规模网络 | 节点、质押、大型测试 |

#### 获取测试币（Sepolia ETH）
1. MetaMask 切换至 **Sepolia Test Network** 获取测试地址。
2. 使用 Faucet 申请测试币（例：https://sepolia-faucet.pk910.de/）。
3. 注意：可能需 GitHub/Twitter 验证，避免使用 VPN。

#### Remix 部署流程
1. **连接钱包**  
   - 在 Deploy & Run Transactions 选择 `Injected Provider - MetaMask`。  
   - 确保 MetaMask 已切至 Sepolia。
2. **编译合约**  
   - 在 Solidity Compiler 面板点击 Compile。
3. **部署合约**  
   - 在 Deploy 面板点击 Deploy，MetaMask 确认交易。  
   - 记录交易哈希与合约地址。

#### 部署验证
- **Etherscan 查询**  
  - 搜索交易哈希查看交易详情（From、To、Gas、字节码等）。
  - 搜索合约地址查看代码、交易记录、事件日志。
- **合约交互测试**  
  - 在 Remix 已部署实例中调用函数，确认交易并在 Etherscan 验证事件输出。

# 2025-08-07

# Web3安全与合规核心笔记

## Web3合规性要求与法律风险

### 核心监管态度
政策基调："全面封堵金融属性，有限容忍技术创新"
管辖范围：属人管辖 + 属地管辖，境外犯罪同样适用中国刑法
高风险场景：Token发行、融资、交易、矿池运营

### 核心法律风险

#### 1. 代币发行风险
不论名称为"积分""凭证"或"治理Token"，只要具备融资功能或流通性即违法
技术人员参与代币模型设计、合约部署等环节，无法以"只是写代码"免责

#### 2. 赌博传销风险
"充值—抽奖—提现"闭环 + "下注—随机收益—兑现"机制
"邀请返利""算力挂靠""多级推广"+ 团队计酬模式

#### 3. 洗钱与非法经营风险
人民币→USDT→境外平台→外币，形成地下换汇
虚拟币成为跨境资金流转"桥梁资产"

#### 4. 民商事争议
虚拟货币买卖合同大概率被认定无效
投资亏损可能面临民事赔偿甚至刑事责任

### Web3入职防范指南

#### 雇佣关系新形态
1. 境外注册公司无法签署有效劳动合同
2. 影响落户、购房、贷款、子女教育等生活事项
3. 通过名义雇主委托国内公司签约的合规解决方案

#### 薪酬结构风险
1. 工资应以人民币支付，Token/USDT支付存在无效风险
2. 项目Token可能归零，收入大打折扣
3. C2C交易易卷入涉赌涉诈资金，面临银行卡冻结

## 网络安全风险与防护措施

### 六大攻击方式

#### 1. 钓鱼攻击
- **伪造面试软件**：以"技术面试助手"等名义投毒
- **奖学金空投**：要求连接钱包并签名"验证身份"
- **社交平台冒充**：伪装官方人员、HR、学长发布虚假信息

#### 2. 恶意软件/木马
- **剪贴板劫持**：监控并替换钱包地址
- **浏览器后门**：窃取Cookie、密码等敏感数据
- **远程控制**：键盘记录、截屏、文件窃取

#### 3. 社交工程攻击
- **信任建立**：冒充导师、同学等可信身份
- **紧急情况**：以"急需用钱""帮忙测试"等理由诱导操作

#### 4. 供应链攻击
- **恶意插件**：Chrome商店等平台的带后门插件
- **开源库投毒**：在常用依赖包中植入恶意代码

#### 5. 地址污染
- **剪贴板监控**：实时替换复制的钱包地址
- **输入框劫持**：监听浏览器输入内容

### 核心防护策略

#### 面试安全
只用官方Zoom/Teams/腾讯会议，拒绝"专用面试软件"
多渠道确认面试邀请的真实性

#### 钱包安全
转账前核对地址前6位和后4位
定期检查并取消不必要的钱包授权
只离线保存，绝不截图或上传

#### 账号安全
- **2FA启用**：优选谷歌验证或硬件密钥，避免短信验证
- **密码管理**：使用1Password、Bitwarden等工具，每平台独立强密码
- **权限分离**：不在同一浏览器保存助记词与日常Cookie

## 典型诈骗案例

### 高级钓鱼手法
通过Office宏脚本或漏洞植入木马
使用相似字符构造钓鱼域名（如trẹzor.com）
利用相似域名和SPF配置漏洞伪造官方邮件

### BigGame赌博平台案
EOS区块链赌博平台，"掷骰子""红黑大战"等游戏
2万余用户投注3.3亿余个EOS币，平台盈利322万余个
主犯判刑5年9个月，罚金60万元；技术开发等从犯判刑2-4年不等

# 2025-08-06

# 区块链岗位精简概览

## 技术岗

### 1. 前端工程师
- **职责**：开发 Web3 前端应用，集成钱包和智能合约，优化 UI 和性能。
- **要求**：精通 HTML/CSS/JavaScript、React/Vue、Viem，了解以太坊/Solana，熟悉 Git，有 Dapp 经验优先。
- **技术栈**：HTML5, CSS3, JavaScript (ES6+), React/Vue, TypeScript, Next.js, Viem

### 2. 后端工程师
- **职责**：开发 Dapp 后端服务，构建 API，集成智能合约，优化性能。
- **要求**：精通 Node.js/Go/Python、Viem，熟悉 RESTful/GraphQL、数据库，了解 Docker/Kubernetes，有 Web3 经验优先。
- **技术栈**：Node.js, Go, Python, Viem, RESTful API/GraphQL, MySQL/PostgreSQL, Docker

### 3. 智能合约工程师
- **职责**：开发/部署 Solidity 智能合约，审计漏洞，优化 Gas 费用。
- **要求**：3年以上 Solidity 经验，熟悉以太坊/Polygon、Foundry/Hardhat，了解 ERC 标准和 IPFS。
- **技术栈**：Solidity, Remix, Foundry/Hardhat, Phalcon/Tenderly, Yul

## 非技术岗

### 1. 产品与运营
- **职责**：协调产品发布，优化用户体验，执行增长策略，分析 KPI。
- **要求**：熟悉产品上线流程，精通 SQL/Excel，具备跨部门协调能力。

### 2. 社区管理
- **职责**：管理 Telegram/Twitter/Discord 社区，组织 AMA/活动，分析用户行为。
- **要求**：了解 Web3/DAO/NFT，具备文案和活动策划能力，熟悉数据分析。
- **平台**：Telegram, Twitter (X), Discord

### 3. 研究分析
- **职责**：分析 Web3 市场数据，撰写报告，支持加密经济模型设计。
- **要求**：熟悉 Excel/SPSS/Python、Glassnode/Token Terminal，了解 DeFi/加密经济学。
- **工具**：Excel, SPSS, Python, Glassnode, Token Terminal

## 扩展阅读
- **求职平台**：
  - [DeJob](https://www.dejob.top/)：中文，Web3 工作机会
  - [SmartDeer](https://www.smartdeer.com/)：中文，综合招聘
  - [电鸭社区](https://eleduck.com/)：中文，远程工作
  - [Web3Career](https://web3.career/)：英文，Web3 招聘
  - [CryptoJobs](https://crypto.jobs/)：英文，区块链招聘

# 2025-08-05

# 以太坊​
作为支持智能合约的公共区块链平台，其技术架构包含应用层、合约层等多层结构，能够为去中心化应用（DApps）的开发提供支撑。以太币是该平台的原生加密货币，在网络中承担着支付交易费用等重要功能。而以太坊 2.0 正处于从工作量证明（PoW）向权益证明（PoS）的过渡阶段，此举旨在优化网络的性能、安全性和可扩展性。​

---

# Web3 信息安全​
需要应对多方面的风险，在智能合约层面，存在重入攻击等威胁；前端方面，私钥泄露是常见风险；网络层面则可能遭遇 51% 攻击等问题。针对这些风险，可通过使用**安全库**、**实施权限控制**、运用**加密技术**等方式解决。同时，借助 OpenZeppelin 等工具以及开展代码审计等最佳实践，能够有效保障 Web3 的信息安全。​
## 交易所无私钥钱包​
交易所提供的钱包通常为托管钱包，用户在使用时无需自行管理私钥，私钥由交易所统一保管。用户通过账户密码等方式登录交易所账户，间接操作钱包内的资产。​例如币安的无私钥钱包。
优点是操作简便，对新手友好，无需担心私钥丢失问题；缺点是资产控制权**掌握在交易所手中**，若交易所遭遇黑客攻击、倒闭（跑路）或出现合规问题，资产可能面临损失风险。
## OpenZeppelin​
OpenZeppelin 是一个开源的智能合约开发框架与安全库，专注于为以太坊及其他区块链平台提供经过审计、可复用的智能合约组件，旨在降低区块链应用的安全风险，推动去中心化开发的标准化。​
安全合约模板：提供涵盖代币发行（如 ERC20、ERC721 标准实现）、访问控制（Ownable、Roles）、权限管理（AccessControl）等核心场景的预编写合约模板，开发者可直接复用，避免重复开发引入安全漏洞。​
- 安全工具库：包含防重入（ReentrancyGuard）、整数安全（SafeMath，适用于 Solidity 0.8 以下版本）、时间锁（TimelockController）等工具，针对性解决智能合约开发中的高频安全问题。​
- 审计与合规支持：合约代码经过社区和专业团队多轮审计，遵循行业最佳实践，部分组件支持合规场景（如代币增发限制、权限分级），降低法律与安全风险。​
安全保障机制​
- 社区协作审计：代码开源且接受全球开发者审查，持续修复潜在漏洞，形成动态迭代的安全生态。​
版本控制与更新：通过严格的版本管理（如语义化版本）确保合约兼容性，及时发布安全补丁，应对新出现的攻击向量。​

# 2025-08-04

# 笔记8.4
## 区块链
比起一种技术，我更愿意将区块链称为一种去中心化的信任机制。简单来说就是，它把交易数据按时间顺序打包成一个个区块，再用加密技术串成链，全网节点一起通过共识机制（比如比特币的工作量证明、以太坊的权益证明）验证账本。这样一来，就不用靠第三方中介（没错就是我司），直接用技术实现了数据的透明、安全，这才是它能改很多行业规则的关键。就像比特币，不只是第一个用区块链的币，更用去中心化发币、转币证明了不用央行这类中心机构，价值也能安全转移。

## 去中心化
区块链的不同形态，其实是对 “去中心化程度” 的灵活调整。公链比如比特币、以太坊，完全开放，谁都能参与，虽然慢点，但够去中心化，适合加密货币、开放应用这些要全网认的场景；联盟链、私链就通过权限管节点提高效率，适合企业合作或者机构内部管理这种 “半信任” 的情况。这种灵活性让它不只是个工具，更像个能跨金融、身份验证、版权登记的信任基础设施。

## Web2->Web3
不是简单的技术升级，而是从平台到用户。Web2 里，社交记录、购物数据都存在平台，流量收益、数据赚钱也多归平台；Web3 靠区块链，用户数据存在分布式网络里，自己用私钥管理。这其实是互联网权力结构在变。

## 安全和合规问题
作为极度中心化机构的KYC和AML工程师，我认为区块链的确能打破垄断，小团队现在层出不穷，生态多样性目不暇接。但公链吞吐量不行，比特币每秒才处理几笔交易，Layer2 这些扩容办法还在试。且普通用户私钥丢了就找不回，智能合约还有各种BUG（被测试支配的恐惧）；而且去中心化和现在的监管规则，难啊。


# 2025.07.29


<!-- Content_END -->
