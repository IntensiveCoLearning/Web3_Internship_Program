---
timezone: UTC+8
---

# Rain

**GitHub ID:** Turbo-Yu

**Telegram:** @yuhaihang0

## Self-introduction

大家好，我叫海航（Rain），北京，ENFJ，一枚互联网产品经理（偶尔兼职研发），擅长Java、NodeJS，喜欢游泳、羽毛球、LOL，期望能够学习Web3知识，以及黑客松比赛

## Notes

<!-- Content_START -->

# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
了解Web3招聘市场：

1.  [**Web3.career**](http://Web3.career)
    
    -   **简介：** [Web3.career](http://Web3.career) 是一个专注于 Web3 领域的全球性招聘平台，汇集了大量来自区块链、加密货币、DeFi、NFT、元宇宙等领域的职位。其特点是职位更新及时，覆盖面广，且提供了详细的职位描述和公司信息，是 Web3 求职者的重要资源。
        
2.  [**DeJob**](https://www.dejob.top/)
    
    -   **简介：** DeJob 是一个专注于 Web3 和远程招聘的平台，旨在帮助 Web3 项目方和求职者进行匹配。该平台支持免费发布职位，且职位更新较快，尤其适合国内 Web3 项目和求职者。
        
3.  [**SmartDeer**](https://smartdeer.com/)
    
    -   **简介：** SmartDeer 是一个新兴的 Web3 人才招聘平台，专注于连接优质的 Web3 项目和专业人才。平台提供精准的岗位匹配服务，涵盖技术开发、产品运营、市场推广等多个职能领域。其特点是注重人才质量和项目可靠性，为求职者提供更有保障的就业选择。
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
将MyFirstApp Demo 项目提交到Git上
<!-- DAILY_CHECKIN_2025-08-23_END -->

# 2025-08-22

## 20:00
学习mint函数铸币方法
​合约创建者特权​：在稳定币（如 USDC）和基础代币合约中常见，铸造权高度集中。
​多角色控制​：BRC-100 协议支持三种铸造操作符：
mint：公开铸造，受最大供应量（max）限制。
mint2：管理员专用，为特定地址预留代币。
mint3：应用内转换余额，不增加流通量

function creditPersonalIDO(
        address account,
        uint256 amount
    ) external onlyRole(DISTRIBUTOR_ROLE) {
        require(amount > 0, "zero amount");
        ido.mint(account, amount);
        emit PersonalCredited(account, amount);
    }

# 2025-08-21

### 21：00
继续优化课程列表和添加课程

# 2025-08-19

## 18:00

增加了创建课程页，展示用户剩余金额，并在创建课程时，和钱包交互，给客户发放奖励

# 2025-08-18

## 23:00
参加了休闲黑客松，注册并创建了项目

1、项目目标
没地方积累项目经验？学完过段时间就忘？技术不深入？没有地方“为爱发声”？
教学相长（Teaching is the best way to learn.）， 通过教学的方式来学习，不管是讲师还是学生，不管是授课还是听课，都会有奖励。

2、核心功能
分教师端和学员端：

教师发布课程（可以是经验分享、发起项目、技能教授），只要发布的课程就会有钱包奖励，学生基于评价/打分获得不同奖励
学员端，只要上课就会收到钱包奖励，参与互动也会获得奖励
3、技术架构
目前只是暂定，如果有更好的框架可以更换

前端：Viem、React
后端：NodeJS、Express、RestFul
DB：SqlLite（暂定，后续可更换）
合约：Foundry
4、来吧！来吧！
产品、前端、后端、合约、UI、运营.... 通通欢迎，微信找我：yuhaihang0

# 2025-08-16

## 22:00
今天做了 https://www.wtf.academy/zh/course/solidity101/ValueTypes 的内容

# 2025-08-15

## 17:00
继续学习Solidity

# 2025-08-14

## 20:00
了解和学习Forge、Soldeer的使用，听老师讲解了一下Licredity，以及Gas优化和审计相关的一些要求。

# 2025-08-13

## 10:00
挑战ethernaunt

# 2025-08-12

##  20:00 学习产品知识
### Uniswap 学习：
- Uniswap 官网文档（机制详解）https://uniswap.org/
- CSDN 博客《Uniswap V3 源码深度解析》 https://mp.weixin.qq.com/s?__biz=MzI2MjY1NDExNQ==&mid=2247486602&idx=1&sn=769d9c7a84bd15df4a0d0ac19f2dabb5&chksm=eb0613ac3d68269a54baae7f6877d58fd7609d2d7dcc3675b75cb5d360de8bfa15cc53366a02#rd
- 简书《Uniswap 设计原理》  https://blog.csdn.net/qq_27146535/article/details/145849834
### Web 3.0 对比Web2的差别
- 1、可组合性
- 2、吸血鬼共计
- 3、代币标准化
- 4、治理与团队稳定性
- 5、长期主义 VS 短期主义

# 2025-08-11

## 22:00技术向
今天通过Foundry构建了本地Counter项目，并把Cursor搭建了起来。

## Foundry本地搭建
### 1、macOS 安装

```
curl -L https://foundry.paradigm.xyz | bash && source ~/.zshrc && foundryup && forge --version && cast --version && anvil --version && chisel --version | cat
```

### 2、初始化项目

```
cd blockchain-workspace
forge init Counter
```

### 3、编译项目

```
cd Counter
```

## 22:00 运营向
了解了运营的主要能力要求和大致工作内容

# Compile your contracts
forge build

# Run your test suite
forge test
```

## 2、Solidity基础
### 1、Solidity语法盒子：
### 2、基础合约项目：
https://www.thinkingsolidity.com/solidity/
### 3、固定年化金库 + 奖励代币项目：
https://github.com/crazyyuan/defi-fixed-yield-course/blob/main/docs/lessons/01-intro.md

## 3、Cursor安装
### 1、Cursor 安装及注册

 下载地址 ：https://cursor.com/cn

### 2、无限续杯

无限邮箱：https://www.2925.com/login/ 

自动破解机器码：https://github.com/yeongpin/cursor-free-vip

### 3、汉化
安装扩展：Ctrl+Shift+X： Chines

# 2025-08-10

## 22:00
今天整理了一下上周的笔记内容，并规划了下周学习、工作计划

# 2025-08-09

#23:00
同学们的笔记整理的好好，我今天搭建了Notion 知识空间，下周将针对技术、运营做知识整理。

# 2025-08-08

## 16:00
了解社区运营指南的内容，看起来Web 3.0 的一般公司规模尚未发展到足够大，产品经理不会更细分工，更多的会要求同时承担运营职责，所以需要补充学习运营相关的知识。

# 2025-08-07

## 18:00
今天按照文档了解了一下Web 3.0 的各个岗位，并参考教程，搭建了一个留言的DApp网页，部署了合约 https://sepolia.etherscan.io/tx/0x01a465d953612e0e7aa12e4ba78899311b51090655213e91bd795ad09ddf477f

# 2025-08-06

## 10:00
了解了一下Web 3.0 的岗位全景图和安全合规方面的内容。
作为曾经从事过Web 2的开发，和目前的产品经理，对于一些前、后端工程师的岗位要求还能够满足部分技能，但框架方面缺乏应用了解，合约工程师的技能则基本没有接触过。
接下来要尽可能参与项目中，首先了解目前的DApp，先从产品角度了解能做什么。

# 2025-08-05

## 12:00
看了Web3.0 的行业赛道 ，整理笔记： https://postimg.cc/0zpjpb5F

## 21:00
参加《Web3 安全分享和答疑》会议，对防钓鱼有了大致的了解，体验了几道钓鱼挑战题（比较简单）。
向同学转了0.02个Sepolia个测试币，找了找之前学习的资料，之前的学习的知识和项目，随着时间都忘了，学习还是要结合实践，不然学过了不用，就会忘掉。

# 2025-08-04

## 8:00
今天把工具都安装完了
· Zoom
· Telegram
· Discord
· Notion
· X
· Metamask
另外我根据ChainLink学习了一下链上发布合约的开发方法，还学习了一下如何接水等内容。相关测试代码： https://github.com/Turbo-Yu/blockchain_chainlink#

## 21:00
了解了一下区块链的基础概念，新的收获是Web3 和Web 3.0 竟然不是一个东西，进一步明确了Web 3.0的学习方向（React + Ethers.js + Solidity + IPFS） 。同时要辩证的结合Web2和Web 3.0，工具以及技术的根本还是要解决具体的问题。

# 2025.08.03


<!-- Content_END -->
