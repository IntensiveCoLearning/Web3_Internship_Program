---
timezone: UTC+8
---

# seven

**GitHub ID:** jorgenlou

**Telegram:** @seven909

## Self-introduction

前图像算法工程师，现计划转型WEB3远程办公，同时为WEB3事业添砖加瓦

## Notes

<!-- Content_START -->
# 2025-08-15

## 基础开发环境搭建

- **Node.js**


  [GitHub - nvm-sh/nvm: Node Version Manager - POSIX-compliant bash script to manage multiple active node.js versions](https://github.com/nvm-sh/nvm)

  全称 Node Version Manager，用于管理 Node.js 版本的命令行工具，可以在同一台机器上安装、切换和管理多个 Node.js 和 npm 版本。

  **特点**

  - 多版本共存
  - 通过命令可快速切换 Node 版本
  - 跨平台支持， Linux、macOS，Windows
  - npm 与 Node.js 版本绑定管理
  - 按需安装，可精确指定版本
  - 根据不同项目需求进行适配

  ```bash
  # 推荐使用 nvm 进行版本管理
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
  
  # 安装 Node.js LTS
  nvm install --lts
  nvm use --lts
  
  # 安装 yarn（可选）
  npm install -g yarn
  ```

- Git

# 以太坊开发环境

## Hardhat

[Getting started with Hardhat 3 | Ethereum development environment for professionals by Nomic Foundation](https://hardhat.org/docs/getting-started)

Hardhat官方教程

一个基于 JS/TS 的以太坊智能合约开发框架，提供编译、部署、测试、本地链等完整工具链，并拥有丰富的插件生态。

### 快速开始

**特点**

- **主流框架**：社区大、教程多、生态成熟
- **插件丰富**：支持 Ethers.js、部署验证、Gas 分析等
- **内置本地链**：Hardhat Network，方便调试和测试。
- **跨平台**：支持Windows、macOS、Linux。
- **易集成**：与 JS/TS 项目无缝结合

```bash
# 创建项目目录
mkdir hardhat-example
cd hardhat-example

# 初始化项目模板
# 如果执行时未安装 Hardhat，npm会进行自动安装
# 安装过程中需要
# 1.选择hardhat安装版本
# 2.选择项目初始化目录
# 3.选择项目初始化类型
npx hardhat --init

# 构建项目
npx hardhat build

# 测试项目
npx hardhat test
```

---

## Foundry

[foundry - Ethereum Development Framework](https://getfoundry.sh/introduction/getting-started/)

Foundry官方教程

一个用 Rust 编写的高性能 Solidity 原生开发框架，支持快速编译、测试、部署，并自带本地节点 Anvil。

### 快速开始

**特点**

- **高性能**：编译和测试速度远超 JS 框架。
- **Solidity 原生测试**：测试代码直接用 Solidity 编写。
- **轻量化**：无外部运行时依赖，安装简单。
- **现代化设计**：支持高级调试和脚本功能。
- **内置本地节点**：Anvil，启动和执行极快。

**forge & anvil 命令**

```bash
# 下载 Foundry 安装脚本
curl -L https://foundry.paradigm.xyz | bash
# 执行 Foundry 安装命令
foundryup

# 初始化项目
forge init PorjectName

### 构建项目
# 首先进入项目目录，这里以刚建的 PorjectName 为例
cd PorjectName

# 开始构建
forge build

#测试项目
forge test

# 启动本地开发节点
anvil

### 部署合约
# 设置私钥
export PRIVATE_KEY="0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80"
# 部署到本地节点
forge script script/Counter.s.sol --rpc-url http://127.0.0.1:8545 --broadcast --private-key $PRIVATE_KEY

## 补充知识

# 分叉主网络状态
anvil --fork-url https://reth-ethereum.ithaca.xyz/rpc

# 在主链上以分叉的形式进行测试
forge test --fork-url https://reth-ethereum.ithaca.xyz/rpc
```

更多教程：[forge-overview](https://getfoundry.sh/forge/overview)，[anvil-overview](https://getfoundry.sh/anvil/overview)

- **cast 命令**

  通过命令行方式与合约进行交互

  ```bash
  ### 读取数据 ###
  # 检查余额
  cast balance vitalik.eth --ether --rpc-url https://reth-ethereum.ithaca.xyz/rpc
  
  # 通过合约函数读取数据
  cast call 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2 \
  "balanceOf(address)" 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045 \
  --rpc-url https://reth-ethereum.ithaca.xyz/rpc
  
  ### 发起交易 ###
  # 设置私钥
  export PRIVATE_KEY="0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80"
  
  # 发送 ETH 到指定地址
  cast send 0x70997970C51812dc3A010C7d01b50e0d17dc79C8 --value 10000000 --private-key $PRIVATE_KEY
  
  ### 与 JSON-RPC 交互 ###
  # 调用 RPC 函数
  cast rpc eth_getHeaderByNumber $(cast 2h 22539851) --rpc-url https://reth-ethereum.ithaca.xyz/rpc
  
  # 获取最新区块编号
  cast block-number --rpc-url https://reth-ethereum.ithaca.xyz/rpc
  ```

  更多教程：[cast-overview](https://getfoundry.sh/cast/overview)

- **chisel 命令**

  一个快速、实用但冗长的 Solidity REPL，用于快速原型设计和调试。适合测试 Solidity 片段和交互式探索合约行为。

  ```bash
  # 启动 chisel
  chisel
  
  ### 交互式开发 solidity ###
  ➜ uint256 a = 123;
  ➜ a
  Type: uint256
  ├ Hex: 0x7b
  ├ Hex (full word): 0x000000000000000000000000000000000000000000000000000000000000007b
  └ Decimal: 123
   
  // Test contract functions
  ➜ function add(uint256 x, uint256 y) pure returns (uint256) { return x + y; }
  ➜ add(5, 10)
  Type: uint256
  └ Decimal: 15
  ```

  更多教程：[chisel-overview](https://getfoundry.sh/chisel/overview/)

---

## Kurtosis

[Kurtosis OSS](https://www.kurtosis.com/)

一个区块链网络和测试环境编排工具，可快速部署和管理多链、多节点环境，支持 EVM、Solana、Cosmos 等生态。

### **安装**

**特点**

- 多链支持，可同时运行 EVM、Solana、Cosmos 等不同区块链。
- **复杂网络编排**：支持多节点、跨链桥、预言机等组件部署。
- **适合集成测试**：方便验证多系统间交互。
- **CI/CD 集成**：可在流水线中自动化搭建测试环境。
- **语言无关**：可测试不同链和不同语言编写的合约。

Kurtosis 依赖 Docker 运行，安装 Kurtosis 前需先安装 Docker ，安装教程：[Docker install](https://docs.docker.com/get-started/get-docker/)

```bash
# 添加 apt 源
echo "deb [trusted=yes] https://apt.fury.io/kurtosis-tech/ /" | sudo tee /etc/apt/sources.list.d/kurtosis.list

# 更新并安装 kurtosis
sudo apt update
sudo apt install kurtosis-cli
```

### 快速开始

```bash
# 运行 github 上的基础包
kurtosis run github.com/kurtosis-tech/basic-service-package --enclave quickstart

```

# 2025-08-14

## X Space运营活动准备  

邀请访谈嘉宾老师时，根据不同人群设计提问不同的问题
### 通用

- 可以讲讲您当初是怎么接触并进入 Web3 的吗
- 当初选择 Web3 是哪一点吸引了您
- 您在选择进入 Web3 后，大概用了多久才对 Web3 有了比较清晰的认识呢
- 您是通过什么方式了解的呢，网络搜索还是身边朋友或者其它方式

旁白：我们都知道，和互联网比起来，web3还很年轻，应该还处于发展的中早期，同时 Web3 的关注度也在不断提高，有越来越多的人想进入 Web3。

- 您对现在想要进入 Web3 行业的从业者有什么建议吗

### 开发

- 您觉得现在 Web3 行业的应用比较广泛的技术是什么
- 如果传统互联网的程序员想转行 Web3 ，在技术路线和选择上，您有什么建议吗
- 对即将进入 Web3 行业的技术人员，有什么需要特别提醒的地方吗
- 如果非技术人员想进入 Web3 做开发，您有什么建议吗

### 针对运营

- 您觉得对于没有运营经验的小白，该如何快速起号，快速提升名气，比如多参加活动？每天发一定量的帖子？
- 您觉得传统行业的运营和 Web3 行业运营有什么相同和不同之处吗
- 对于传统行业运营转入 Web3 运营您有什么建议和提醒吗

### 创业者

- 是什么原因让您从传统行业转入了 Web3 来创业
- 是什么让您坚持到了现在

### VC

- 是什么吸引了您，最后选择了 Web3
- 您一般从哪些指标来评价一个项目的好坏
- 具我了解 Web3 行业风险还是挺高的，你在寻找项目过程中，是如何减小风险带来的损失呢

# 2025-08-13

##  部署合约练习  
虽然按照实习手册指导，使用Foundry完成了合约的部署，但并没有理解到实际内容，然后在B站上找了些solidity开发的教程，后面跟着教程边做边学吧  
然后晚上参加了技术和运营分享会，技术会全程懵懵懂懂的，运营会注意介绍了筹划黑客松的相关事宜，这个过程中需要管理github的相关人员，平时用git比较多准备提交申请，先从一些边角开始做起吧

# 2025-08-12

## 小结  
又来水笔记了，实在是不太会记录，这次就简单做个小结吧。本来一直在smart deer招聘群潜水，在7月28号左右看到了残酷共学的相关信息，刚好后面准备进入web3行业，就第一时间报名了，也是巧了，月底31号又赶上搬家，而开营仪式是在4号周一，刚好给了我两天时间，搬完第二天就抓紧把桌子电脑网络给弄好了，避免耽误开营，后面就一边慢慢收拾一边学。到现在已经加入共学一周零两天，经过上一周的接触和学习，对web3的安全问题，合规问题以及Web3与现有互联网的区别有了初步的认识，同时也接触到了Web3的第一个DAO组织“LXDAO”，然后周日参加LXDAO周会时，老师说可以做自我介绍然后加入LXDAO，本来没这个计划的，但不知道为什么就介绍了一下，然后就加入了我们的LXDAO组织，哈哈哈，可能这就是看对眼了吧。昨天和今天本来是想实现一些智能合约的开发demo，但infp总是会被奇怪的思绪给勾走，到目前只是参考手册使用conda搭建了一个nodejs开发环境，虽然手册推荐nvm，但因为之前做图像方向的深度学习相关工作，用conda更顺手一些。今天就先水到这吧，后面得想办法屏蔽奇怪的思绪，加快速度赶上来。雄起！！

# 2025-08-11

## 参加以太坊周会

## 参加”DApp 架构与从 0 到部署“晚会

# 2025-08-10

## 重新整理优化Web3常见网络风险与安全措施笔记  
https://www.notion.so/Web3-246cba8d667980259ec8c160c80c592d?source=copy_link

# 2025-08-08

## 今天没学什么，单纯打个卡

# 2025-08-07

## 会议记录

### 常见罪名

- 非法集资和诈骗
    - 详情
        - 易涉及的公司
            - 交易所
            - 虚拟货币基金
        - 易涉及的业务
            
            ICO：虚拟货币的发行和募资
            
            代表案例：光锥币、大圣币
            
        
        随着各国对虚拟货币牌照的收紧，ICO越来越困难，稳妥起见应避免此类业务的项目。如果无法避免则应检查资金汇集风险点：
        
        - 项目是否涉及法币（这里主要指人民币）
        - 项目是否有受众限制，即是否任何人都可以参与
            
            这里主要需要禁止中国大陆用户
            
            常见方式：封锁大陆IP，禁止大陆手机号注册等
            
        - 项目在海外的合规性是否完备（AML和KYC）
        
        > 一句话总结：
        避免涉及风险项目，需要检查项目是否有合规校验，如 KYC， AML，禁止大陆用户参与，是否拥有牌照，是否涉及法币。
        > 
- 非法吸收公众存款
- 组织、领导传销活动
    
    需要注意项目扩张过程中的合规，要避免多级返佣或团队层级激励，返佣层级最多不能超过3层
    
- 开设赌场
    
    非典型案例：国内某地将交易所合约交易定性为赌博，但专业律师并不支持此判决
    
- 非法经营
    
    货币结算业务中较常见，结算业务还可能碰到：洗钱与外汇管制问题
    
    期间可能成为洗钱链条中的一环或外汇兑换的媒介，由于非管理层难以知晓资金的来龙去脉，因此比较难发现自己是否涉及违法活动，只能提高自身警惕，在遇到可疑情况时，向有经验的人寻求咨询和帮助。
    

### 项目/项目方合规检查项

- 是否明确的在受监管的司法辖区内注册
- 是否取得合规资质，并经过第三方专业机构的审计或安全测试
- 是否具备完善的 AML（反洗钱）与 KYC（用户身份识别）制度
- 是否对外公开项目负责人、团队背景、资金来源等基本信息
- 是否具备高风险模块（如混币器，需特别注意）
- 是否存在高风险的激进扩张方式（多级返佣，类似传销拉人头模式）

### 责任风险

当发生问题时，责任越大，风险越大：社区/宣传负责人 ≥ 核心管理层 > 核心链条 > 纯粹技术

### **知识点：**

<aside>

法律定罪中的知情推断原则，满足任意一点都可被定罪

- 事实知情
- 推定知情
- 应当知情

即使是纯粹的技术提供者，也必须保持审慎判断，发现可疑行为一定要及时询问，若出现违规行为，应立即终止合作并退出项目。

</aside>

## 一段话总结

> 为了最大限度保证自身安全，从求职到正式入职都应持续进行合规审查：1.检查项目是否在受监管的合法辖区注册；2. 是否拥有合规牌照，具备完备的AML和KYC制度；3. 是否禁止中国大陆等限制区域用户；4. 是否公开项目方、资金来源、代币分配机制；5. 是否涉及返利、拉人头、炒币等激进展业行为；
>

# 2025-08-06

## 完成全部钓鱼攻防挑战

# 2025-08-05

## 完成MetaMask转账操作  
https://sepolia.etherscan.io/tx/0x471f6ec4419a42b6be8c0e5bea2ed69659ed7276e7fbfdca3447393779a3f70a  

## 了解NFT相关知识  
https://www.notion.so/Web3-NFT-246cba8d667980869760d2b27c4e5c3a?source=copy_link  

## 学习web3安全知识  
https://www.notion.so/Web3-246cba8d667980259ec8c160c80c592d?source=copy_link

# 2025-08-04

## 学习内容  
1. 学习区块链基础知识  
  了解什么是区块链，即一种去中心化的分布式账本技术，用于在网络节点之间安全、透明且不可篡改地记录事务数据。
2. 学习 web2.0，web3.0，web3之间的区别  
   web2.0是当前互联网版本，所有数据都集中化管理，数据决策权由科技巨头掌握；web3.0是语义网，是基于传统互联网进行的数据组织形势的升级；web3是去中心化互联网，数据属于用户自己掌握，不会因为某个组织或个体导致数据丢失
3.参加以太坊中文周会，了解web3最新前沿资讯
4.重新创建小狐狸钱包

# 2025.07.30


<!-- Content_END -->
