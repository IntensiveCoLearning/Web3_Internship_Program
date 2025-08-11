---
timezone: UTC+8
---

# An

**GitHub ID:** silver-x

**Telegram:** @hh_An404

## Self-introduction

Web2开发转型

## Notes

<!-- Content_START -->
# 2025-08-11

## 以太坊开发环境与 Solidity 合约编程笔记

## 一、开发环境搭建

### 1. 基础环境准备
- Node.js（建议用 nvm 管理）
- npm 或 yarn
- Git

#### 安装命令示例
```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
# 安装 Node.js LTS
nvm install --lts
nvm use --lts
# 安装 yarn（可选）
npm install -g yarn
```

### 2. 以太坊本地开发链
#### Foundry（Rust 实现，极快）
- 官网：https://getfoundry.sh/introduction/getting-started
- 安装：
  ```bash
  curl -L https://foundry.paradigm.xyz | bash
  foundryup
  ```
- 初始化项目：`forge init Counter`
- 编译测试：`forge build`、`forge test`
- 启动本地节点：`anvil`
- 部署合约：
  ```bash
  export PRIVATE_KEY="你的私钥"
  forge script script/Counter.s.sol --rpc-url http://127.0.0.1:8545 --broadcast --private-key $PRIVATE_KEY
  ```

#### Hardhat（主流推荐）
- 官网教程
- 安装：`npm install --global hardhat`
- 初始化项目：
  ```bash
  mkdir eth-dev && cd eth-dev
  npx hardhat
  ```
- 启动本地节点：`npx hardhat node`
- 部署合约：`npx hardhat run scripts/deploy.js --network localhost`

### 3. 钱包与前端交互
- 推荐使用 MetaMask 浏览器插件
- 前端库推荐 Viem、Wagmi

### 4. 其他工具
- Remix IDE：https://remix.ethereum.org
- OpenZeppelin 合约库：`npm install @openzeppelin/contracts`
- Chainlink 预言机集成

---

## 二、Solidity 智能合约编程

### 1. 基础语法与开发范式

#### 版本声明
```solidity
pragma solidity ^0.8.0;
```

#### 数据类型
- 基本类型：`bool`, `uint256`, `int256`, `address`, `bytes`, `string`
- 复合类型：数组、映射、结构体、枚举

#### 函数修饰符
- 可见性：`public`, `external`, `internal`, `private`
- 状态：`pure`, `view`, `payable`

#### 开发范式
- 状态机模式
- 事件驱动编程
- 模块化设计（继承、库）

### 2. 合约结构详解

#### 基本结构
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MyContract {
    uint256 public myNumber;
    constructor() { myNumber = 100; }
    function setNumber(uint256 _number) public { myNumber = _number; }
}
```

#### 状态变量
- 永久存储在区块链上
- 可通过内部/外部函数更改

#### 函数声明格式
```solidity
function <函数名>(<参数列表>)
    <可见性>
    <状态可变性>
    <修饰符列表>
    <virtual/override>
    returns (<返回值列表>)
{
    // 函数体
}
```

#### 函数可见性
- `private`：仅当前合约
- `internal`：当前及继承合约
- `public`：所有人
- `external`：仅外部调用

#### 状态修饰符
- `view`：只读
- `pure`：不读不写
- `payable`：可接收 ETH

#### 参数与返回值
- 支持多参数、多返回值、命名返回值

#### 修饰符（Modifiers）
- 权限控制、前置检查
```solidity
modifier onlyOwner() { require(msg.sender == owner, "Not the owner"); _; }
```

#### 继承与重写
- 单/多继承，`virtual`/`override` 支持函数重写

#### 接口与抽象合约
```solidity
interface IERC20 {
    function transfer(address to, uint256 amount) external returns (bool);
}
abstract contract AbstractToken {
    function totalSupply() public virtual returns (uint256);
}
```

#### 事件机制
```solidity
event Transfer(address indexed from, address indexed to, uint256 amount);
emit Transfer(msg.sender, to, amount);
```

---

## 三、安全实践

### 1. 重入攻击防护
- 问题：外部调用前未更新状态，导致重复提款
- 防护：Checks-Effects-Interactions 模式、重入锁（ReentrancyGuard）

#### 易受攻击示例
```solidity
function withdraw() external {
    uint256 amount = balances[msg.sender];
    require(amount > 0, "No balance");
    (bool success,) = msg.sender.call{value: amount}("");
    require(success, "Transfer failed");
    balances[msg.sender] = 0; // 状态更新在转账之后
}
```

#### 安全写法
```solidity
function withdraw() external {
    uint256 amount = balances[msg.sender];
    require(amount > 0, "No balance");
    balances[msg.sender] = 0;
    (bool success,) = msg.sender.call{value: amount}("");
    require(success, "Transfer failed");
}
```

#### 重入锁
```solidity
modifier noReentrant() { require(!locked, "Reentrant call"); locked = true; _; locked = false; }
```

### 2. 访问控制
- 问题：敏感函数无权限限制
- 防护：`onlyOwner`、角色权限（AccessControl）

#### 不安全示例
```solidity
function withdraw() public {
    payable(msg.sender).transfer(address(this).balance);
}
```

#### 安全示例
```solidity
function withdraw() external {
    require(msg.sender == owner, "Not owner");
    // ...
}
```

### 3. 整数溢出防护
- 旧版本无自动检查，易被利用
- 防护：Solidity 0.8+ 默认检查，逻辑限制最大值

#### 不安全示例（<0.8.0）
```solidity
counter[msg.sender] += 1; // 溢出后绕回 0
```

#### 安全方案
```solidity
require(counter[msg.sender] < MAX_ACTIONS, "limit reached");
counter[msg.sender] += 1;
```

---

## 四、最佳实践总结

- 使用最新 Solidity 版本，自动防溢出
- 关键操作前务必权限检查
- 外部调用前先更新状态（CEI模式）
- 充分利用事件机制与合约模块化设计
- 推荐使用社区成熟库（如 OpenZeppelin）

---

> 适合初学者的以太坊开发流程：环境搭建 → 合约编写 → 测试与部署 → 安全加固 → 前端集成

# 2025-08-10

## Dapp 开发与部署流程总结
1. Dapp 架构核心组成
前端（UI）：用现代框架（React/Vue）构建，集成区块链钱包（如 MetaMask），与智能合约交互。
智能合约：用 Solidity 编写，部署在区块链上，负责业务逻辑和数据安全。
数据检索器（Indexer）：监听合约事件，将链上数据写入传统数据库，供前端高效查询。
区块链与去中心化存储：区块链存储状态和交易，IPFS/Arweave等存储大文件，保证数据去中心化和持久性。
2. Dapp 开发流程
需求分析与规划：确定功能、选择区块链平台、设计用户体验。
智能合约开发：编写合约、测试、审计安全。
检索器开发：确定前端所需数据，编写事件处理和数据库写入代码，部署运维。
前端开发：选用框架，集成钱包，展示链上和数据库数据，处理交易签名。
区块链交互：用 Viem/Ethers.js/Wagmi 等库读取数据和发送交易。
3. 部署与上线
合约部署：推荐用 Hardhat 或 Foundry，部署到测试网或主网。
前端部署：去中心化平台（IPFS）或传统 Web 服务（Vercel）。
发布维护：收集用户反馈，定期更新和修复。
4. 以太坊开发环境搭建
基础工具：Node.js（建议用 nvm 管理）、npm/yarn、Git。

安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

安装 Node.js LTS
nvm install --lts
nvm use --lts

安装 yarn（可选）
npm install -g yarn

# 2025-08-07

## 区块链/Web3 岗位全景总结笔记
### 一、技术岗
1. 前端工程师

负责区块链前端应用开发，钱包连接、交易签名等交互。
技术栈：HTML/CSS/JS、React/Vue、TypeScript、Next.js、Viem。
要求：熟悉 Web3 技术、Dapp 开发经验、代码规范、团队协作。
2. 后端工程师

负责 Dapp 后端服务、链上数据交互、API 构建与优化。
技术栈：Node.js/Go/Python、Viem、RESTful/GraphQL、MySQL/PostgreSQL、Docker/K8s。
要求：高并发系统经验、智能合约集成、数据库优化、分布式架构。
3. 智能合约工程师

设计、开发、部署智能合约，关注安全与性能。
技术栈：Solidity、Remix、Foundry/Hardhat、Phalcon/Tenderly、Yul。
要求：智能合约开发与审计经验，熟悉主流链平台，安全意识强。
### 二、非技术岗
1. 产品与运营

负责产品发布、用户反馈、增长策略、数据分析。
要求：产品流程熟悉、跨部门协调、数据分析能力（SQL/Excel）。
2. 社区管理

管理 Telegram、Twitter、Discord 社区，策划活动，提升用户活跃度。
要求：Web3 社区经验、文案与沟通能力、数据分析、活动策划。
3. 研究分析

行业数据收集与分析，撰写报告，支持产品与战略决策。
技能：Excel、SPSS、Python、链上数据工具（Glassnode、Token Terminal）。
要求：区块链生态理解、定量/定性分析、报告撰写能力。
三、扩展阅读/求职平台
DeJob、SmartDeer、电鸭社区（中文）
Web3Career、CryptoJobs（英文）

# 2025-08-06

## 区块链/Web3 合规与安全风险总结笔记

---

## 一、Web3 合规性与法律风险

- **中国监管态度**：全面禁止金融属性，有限容忍技术创新。涉及 Token 发行、融资、交易、矿池等高风险场景极易被打击。
- **主要法律风险**：
  - 代币发行/交易：ICO、IEO、IDO 等均被禁止，技术人员也可能被追责。
  - 赌博/传销/洗钱：链游、NFT、DAO 等项目若涉及抽奖、返利、洗钱等，容易触法。
  - 场外交易：虚拟币作为“地下换汇”工具，极易卷入非法经营、洗钱链条。
  - 民商事争议：虚拟币相关合同大概率无效，风险自担。

- **入职风险**：
  - 用工主体不明，劳动合同、社保公积金难保障。
  - 薪酬结构（Token/USDT）存在法律与税务风险，Token 可能归零。
  - 虚拟币出金风险高，易卷入刑事案件。
  - 项目合法性需提前审查，避免被牵连。

- **刑事风险案例**：
  - 赌博平台开发、虚拟币换汇、非法吸收存款、传销、洗钱等均有真实判例，开发/运营/推广/客服等角色均可能被追责。

---

## 二、常见网络安全风险与防护

- **钓鱼攻击**：伪造邮件、网站、社交账号，诱导输入敏感信息或下载恶意软件。
- **恶意软件/木马**：伪装成面试/学习软件、剪贴板劫持、浏览器插件后门、远程控制。
- **社交工程**：冒充 HR/同学/好友，诱导操作或转账。
- **供应链攻击**：恶意插件、开源库后门、官方渠道被劫持。
- **地址污染/扫描木马**：剪贴板劫持、输入监听，导致转账资金被盗。
- **传统账号安全**：弱密码、邮箱/SIM 劫持、未开启 2FA。

---

## 三、真实案例与高级攻击

- **面试钓鱼**：伪装面试软件植入木马，窃取 Cookie、文件、账号。
- **奖学金钓鱼**：假冒空投/奖学金，诱导连接钱包签名，盗取资产。
- **Punycode 钓鱼**：利用 Unicode 域名混淆（如 trẹzor.com），极具迷惑性。
- **内部人员作恶**：人员安全是最大风险，内部风控需重视。

---

## 四、防护建议（学生/新人必读）

- **面试/实习**：只用官方会议工具，拒绝安装“专用软件”，多渠道核实邀请。
- **空投/福利**：要求输入助记词/私钥/签名的网页基本都是骗局，主钱包冷存储。
- **社交平台**：不轻信群内“官方人员”私聊和链接，遇到转账等请求多方核实。
- **软件/插件**：只从官网/官方商店下载，核查开发者和下载量，插件控制在 3 个以内。
- **账号安全**：启用 2FA，使用密码管理器，邮箱/手机号安全重于一切。
- **钱包转账**：核对地址前后位，助记词离线保存，定期检查授权。
- **遇到攻击**：第一时间断网、截屏、记录时间点，寻求专业帮助。

---

## 五、扩展阅读/工具推荐

- [区块链安全红手册](https://www.slowmist.com/redhandbook/)
- [区块链黑暗森林自救手册](https://github.com/slowmist/Blockchain-dark-forest-selfguard-handbook/blob/main/README_CN.md)
- [Revoke.cash](https://revoke.cash/)（授权管理）
- [Scam Sniffer](https://www.scamsniffer.io/)（钓鱼检测）
- [1Password](https://1password.com/)、[Bitwarden](https://bitwarden.com/)（密码管理器）
- [SlowMist Hacked](https://hacked.slowmist.io/)（区块链被黑档案库）
- [反钓鱼攻防学习平台](https://unphishable.io/)

---

**核心体会**：  
Web3 行业机会巨大，但法律与安全风险极高。务必提升合规意识与安全防护能力，防止因无知或疏忽而遭受不可挽回的损

# 2025-08-05

1. 使用dapp opensea
2. 互相发币
https://sepolia.etherscan.io/address/0xAE3934037B013755AC76E0a78a671911BCbc7577

# 2025-08-04

1. DeFi（去中心化金融）
基于区块链的金融服务体系，无需传统中介，人人可参与。
自动做市商（AMM）、流动性池、去中心化借贷、超额抵押、智能合约自动执行，公开透明，效率高。

2. NFT（非同质化代币）
代表数字资产唯一性和所有权的区块链代币。
不可复制、可验证、智能合约自动分发版税、数字艺术和收藏品、流通性依赖平台（如 OpenSea）。

3. DAO（去中心化自治组织）
无需传统管理层，通过智能合约和社区投票治理的组织形态。
公开透明、社区共治、代币持有即治理权、资金和决策链上执行，适合远程协作和集体决策。

4. MEME（模因币）
以网络文化、表情包为基础的加密代币。
社区驱动、趣味性强、技术门槛低、波动大、投机性强，风险高，常依赖名人效应和社群共识。

5. 交叉创新领域
不同赛道技术和模式的融合创新。
如 DeFi+NFT（NFT 抵押借贷）、DAO+MEME（社区治理+文化）、AI+DeFi（智能投顾）、Web3+乡建（社区货币、自治治理），推动新应用和商业模式诞生。

6. 新兴趋势
意图驱动交易（Intent-Based）、账户抽象与智能钱包、模块化区块链、AI+Web3 融合等，强调用户体验、可扩展性和智能化。


# 2025.08.01


<!-- Content_END -->
