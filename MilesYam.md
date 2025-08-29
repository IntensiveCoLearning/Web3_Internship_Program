---
timezone: UTC+8
---

# 小六

**GitHub ID:** MilesYam

**Telegram:** @MilesYam

## Self-introduction

學習Web3，擁抱未來世界

## Notes

<!-- Content_START -->

# 2025-08-29
<!-- DAILY_CHECKIN_2025-08-29_START -->
非技术背景如何转入Web3？

循序渐进的学习路径：

第一阶段：概念理解（1-2 个月）

阅读《精通比特币》《精通以太坊》了解基本原理

使用 MetaMask 钱包，体验 DeFi 协议（Uniswap、Compound）

观看 Web3 入门视频课程，建立整体认知框架

第二阶段：技能建设（3-6 个月）

技术路线：学习 JavaScript → React → Web3.js/Ethers.js

非技术路线：产品、运营、市场、投资分析

通用技能：英语阅读、社区运营、项目管理

第三阶段：实践积累（6-12 个月）

完成 CryptoZombies 等在线教程

部署第一个智能合约（ERC-20 代币）

参与开源项目或社区贡献

撰写学习心得和技术博客

第四阶段：专业发展（持续）

确定专业方向（开发/产品/运营/投研）

建立个人品牌和行业影响力

寻找实习或全职机会

持续学习新技术和行业动态

转型建议：

优势发挥：结合原有专业背景（金融 → DeFi、游戏 → GameFi）

循序渐进：不要急于求成，扎实基础更重要

社区参与：加入 LXDAO、ETHPanda 等学习社区

导师寻找：找到行业前辈指导职业发展
<!-- DAILY_CHECKIN_2025-08-29_END -->


# 2025-08-28
<!-- DAILY_CHECKIN_2025-08-28_START -->
web3求职建议

技能准备：

作品集：GitHub 上展示完整项目（前端+合约）

技术博客：分享学习心得和技术总结

开源贡献：参与知名项目的 Issue 和 PR

证书认证：ConsenSys、Alchemy 等平台认证

求职策略：

网络建设：参加 Web3 社区活动，建立人脉

简历优化：突出区块链相关经验和项目

面试准备：熟悉常见技术问题和项目经验分享

持续学习：保持对新技术和趋势的敏感度

职业方向：

智能合约开发：Solidity/Rust，专注协议层开发

DApp 前端：Web3 集成，用户界面和体验

DevRel：开发者关系，技术布道和社区建设

安全审计：智能合约安全分析和漏洞挖掘

产品经理：Web3 产品设计和用户需求分析
<!-- DAILY_CHECKIN_2025-08-28_END -->


# 2025-08-27
<!-- DAILY_CHECKIN_2025-08-27_START -->
如何跟上Web3最新发展  
**技术动态**：

-   **GitHub 趋势**：关注 [**ethereum/EIPs**](https://github.com/ethereum/EIPs)、热门仓库
    
-   **开发者会议**：ETHGlobal、DevCon、EthCC 等
    
-   **技术博客**：Vitalik、ConsenSys、OpenZeppelin
    
-   **研究论文**：arXiv、ETH Research、学术会议
    

**行业资讯**：

-   **媒体订阅**：Bankless、The Defiant、CoinDesk
    
-   **Twitter 关注**：@VitalikButerin、@ethereum、项目官方账号
    
-   **新闻聚合**：CoinGecko、DeFiLlama
    

**社区参与**：

-   **Discord/Telegram**：项目官方群、技术讨论群
    
-   **Reddit**：r/ethereum、r/ethdev、r/CryptoCurrency
    
-   **论坛**：Ethereum Magicians、Research 论坛
    
-   **线下活动**：Meetup、黑客松、技术分享会
    

**学习方法**：

-   **项目研究**：分析成功项目的技术架构和代码
    
-   **代码阅读**：OpenZeppelin、Uniswap、Compound 等
    
-   **实践验证**：在测试网部署和测试新技术
    
-   **知识分享**：写技术博客、参与开源贡献
<!-- DAILY_CHECKIN_2025-08-27_END -->


# 2025-08-26
<!-- DAILY_CHECKIN_2025-08-26_START -->
智能合约最佳安全实践

编码规范：

使用 OpenZeppelin 标准库，避免重复造轮子

遵循 Checks-Effects-Interactions 模式防止重入攻击

启用编译器优化和严格模式（pragma solidity ^0.8.0）

使用 SafeMath 或内置溢出检查（0.8.0+）

访问控制：

实现适当的权限管理（Owner、Role-based）

避免使用 tx.origin，优先使用 msg.sender

考虑时间锁（Timelock）机制保护关键函数

实现紧急暂停（Circuit Breaker）功能

外部调用：

优先使用 call 而非 transfer/send

检查外部调用返回值

限制 Gas 使用量防止 Gas 耗尽攻击

考虑重入锁（ReentrancyGuard）

测试与审计：

单元测试：覆盖率 >90%，包含边界条件

集成测试：模拟真实交互场景

静态分析：Slither、Mythril 扫描

专业审计：CertiK、OpenZeppelin、ConsenSys

漏洞悬赏：Immunefi、HackerOne 平台
<!-- DAILY_CHECKIN_2025-08-26_END -->


# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
Web3 开发需要什么技术栈？

**前端开发者**：

-   **核心技能**：JavaScript/TypeScript、React/Vue、Web3.js/Ethers.js
    
-   **区块链交互**：钱包连接（MetaMask、WalletConnect）、智能合约调用
    
-   **工具链**：Vite、Webpack、IPFS 部署
    

**智能合约开发者**：

-   **编程语言**：Solidity（以太坊）、Rust（Solana/Near）、Move（Aptos/Sui）
    
-   **开发框架**：Hardhat、Foundry、Truffle（已停止维护）
    
-   **测试工具**：Chai、Mocha、内置测试框架
    

**全栈开发者**：

-   **后端服务**：Node.js、Python、Go + 区块链 RPC
    
-   **数据库**：MongoDB、PostgreSQL + 链上数据索引
    
-   **部署运维**：Docker、AWS/Vercel、IPFS/Arweave
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
## [**社区与资源**](https://web3intern.xyz/zh/appendix/#%E5%9B%9B%E3%80%81%E7%A4%BE%E5%8C%BA%E4%B8%8E%E8%B5%84%E6%BA%90)

### [**👥 开发者社区**](https://web3intern.xyz/zh/appendix/#%F0%9F%91%A5-%E5%BC%80%E5%8F%91%E8%80%85%E7%A4%BE%E5%8C%BA)

中文社区

-   **LXDAO**：[**Web3 公共物品建设者社区**](https://lxdao.io/)
    
-   **ETHPanda**：[**连接华语区与全球以太坊生态**](https://ethpanda.org/)
    
-   **HackQuest**：[**Web3 黑客松平台**](https://hackquest.io/)
    
-   **登链社区**：[**专业区块链技术社区**](https://learnblockchain.cn/)
    
-   **WTF Academy**：[**Web3 开源大学**](https://wtf.academy/)
    

国际社区

-   **Ethereum Magicians**：[**以太坊改进讨论**](https://ethereum-magicians.org/)
    
-   **r/ethereum**：[**Reddit 以太坊社区**](https://www.reddit.com/r/ethereum/)
    
-   **Ethereum Stack Exchange**：[**技术问答平台**](https://ethereum.stackexchange.com/)
    
-   **Discord 服务器**：各大项目官方讨论群
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
## [**开发工具与基础设施**](https://web3intern.xyz/zh/appendix/#%E4%B8%89%E3%80%81%E5%BC%80%E5%8F%91%E5%B7%A5%E5%85%B7%E4%B8%8E%E5%9F%BA%E7%A1%80%E8%AE%BE%E6%96%BD)

### [**💻 智能合约开发**](https://web3intern.xyz/zh/appendix/#%F0%9F%92%BB-%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%E5%BC%80%E5%8F%91)

开发环境

-   **Remix IDE**：[**在线 Solidity IDE**](https://remix.ethereum.org/) - 浏览器端开发调试
    
-   **VS Code + Solidity**：本地开发环境配置
    
-   **Hardhat**：[**现代化开发框架**](https://hardhat.org/) - TypeScript 支持，丰富插件
    
-   **Foundry**：[**Rust 构建的快速框架**](https://getfoundry.sh/) - 原生 Solidity 测试
    

测试网络

-   **Sepolia**：以太坊官方推荐测试网，主要用于智能合约的测试。
    
-   **Holesky**：以太坊官方推荐测试网，主要用于基础设施、验证者（验证节点）、质押等方面的测试。
    

水龙头服务

-   **Alchemy Faucet**：[**多链测试币领取**](https://www.alchemy.com/faucets)
    
-   **QuickNode Faucet**：[**快速获取测试 ETH**](https://faucet.quicknode.com/)
    
-   **Chainlink Faucet**：[**多种测试币领取**](https://faucets.chain.link/)
    

### [**🔗 节点服务与 API**](https://web3intern.xyz/zh/appendix/#%F0%9F%94%97-%E8%8A%82%E7%82%B9%E6%9C%8D%E5%8A%A1%E4%B8%8E-api)

RPC 服务商

-   **Alchemy**：[**企业级区块链 API**](https://www.alchemy.com/) - 免费额度丰富，稳定性好
    
-   **Infura**：[**ConsenSys 区块链基础设施**](https://infura.io/) - 老牌服务商
    
-   **QuickNode**：[**高性能节点服务**](https://www.quicknode.com/) - 低延迟，多链支持
    

数据索引

-   **The Graph**：[**去中心化索引协议**](https://thegraph.com/) - GraphQL API
    
-   **Moralis**：[**Web3 后端服务**](https://moralis.com/) - 一站式 Web3 开发平台
    

### [**🔐 安全工具**](https://web3intern.xyz/zh/appendix/#%F0%9F%94%90-%E5%AE%89%E5%85%A8%E5%B7%A5%E5%85%B7)

静态分析

-   **Slither**：[**Trail of Bits 静态分析器**](https://github.com/crytic/slither) - 检测常见漏洞
    
-   **Mythril**：[**符号执行分析**](https://github.com/ConsenSysDiligence/mythril) - 深度安全扫描
    
-   **Semgrep**：[**规则驱动的代码扫描**](https://semgrep.dev/) - 自定义检测规则
    

动态测试

-   **Echidna**：[**属性模糊测试**](https://github.com/crytic/echidna) - Haskell 编写的测试工具
    
-   **Foundry Fuzz**：内置模糊测试功能
    
-   **Manticore**：[**符号执行引擎**](https://github.com/trailofbits/manticore)
    

审计服务

-   **OpenZeppelin Defender**：[**智能合约安全平台**](https://openzeppelin.com/)
    
-   **ConsenSys Diligence**：[**安全审计**](https://diligence.consensys.io/)
<!-- DAILY_CHECKIN_2025-08-23_END -->


# 2025-08-22
<!-- DAILY_CHECKIN_2025-08-22_START -->
精选学习资源
建议优先使用英文第一手视频和学习资料，英语也是 Web3 的必备技能，可以一起学习。

📚 核心文档与书籍
官方文档

以太坊开发者资源：Ethereum.org 开发者门户
Solidity 官方文档：Solidity 中文文档
Vyper 官方文档：Vyper 智能合约语言
以太坊改进提案：EIPs - 以太坊改进提案
精选书籍

《精通以太坊》：Andreas M. Antonopoulos 著，区块链开发圣经
《区块链技术指南》：邹均等著，全面系统的区块链知识
《Solidity 智能合约开发》：深入浅出智能合约编程
《DeFi 实战指南》：去中心化金融协议设计与实现
在线教程

CryptoZombies：趣味化 Solidity 教程
LearnWeb3：全栈 Web3 开发课程
Alchemy University：免费区块链开发课程
中文排版模式：https://github.com/sparanoid/chinese-copywriting-guidelines
🎥 优质视频课程
Patrick Collins：完整免费课程
Dapp University：以太坊开发教程
Moralis Web3：全栈 DApp 开发
ETHGlobal：黑客松项目分享
Ethereum Foundation：以太坊开发者大会
Bankless：行业最新动态
📖 技术博客与资讯
技术博客

Vitalik Buterin 博客：以太坊创始人技术思考
ConsenSys 博客：企业级区块链解决方案
OpenZeppelin 博客：智能合约安全最佳实践
行业资讯

CoinDesk：全球加密货币新闻
The Block：专业区块链媒体
研究机构和论坛

以太坊研究院：ETH Research
Messari Research：加密市场研究
Delphi Digital：Web3 投研报告
🛠️ 实战项目与练习
入门项目

投票 DApp：智能合约 + Web3 前端
代币发行：ERC-20 代币创建与部署
NFT 集合：ERC-721 NFT 铸造平台
多签钱包：多重签名合约实现
进阶项目

去中心化交易所：AMM 机制实现
借贷协议：抵押借贷逻辑
治理 DAO：投票治理机制
跨链桥：资产跨链转移
竞赛平台

ETHGlobal 黑客松：全球以太坊黑客马拉松
<!-- DAILY_CHECKIN_2025-08-22_END -->

# 2025-08-21

附录：Web3黑话汇总-摘抄记忆一些
address:钱包/合约地址。
AMA:Ask Me Anything，项目方面向公众的问答会。
ATH:All Time High，历史最高的价格。
ATL: All Time Low,历史最低的价格。
Airdrop: 空投, 即项目方免费赠送代币给你，目的是使人们进一步了解该币种。
Block Reward: 区块奖励，矿工为验证交易和创建新区块所获得的奖励。
Bounty Program: 赏金计划一由项目方分配的一些任务，任何人参加完成都可获得奖励
ctf:Capture The Flag，网络安全技术比赛。
Contract Address: 合约地址，智能合约在区块链上的地址。
Chainlink: 一个去中心化的oracle网络，允许智能合约基于现实世界的数据来安全、可靠地运行。
DAO:Decentralized Autonomous Organization，去中心化自治组织。
Dapp:Decentralized Application，去中心化应用。
DeFi:Decentralized Finance，去中心化金融。
degen:degenerate，赌徒/梭哈的人。
DeSci:Decentralized Science，去中心化科学。
DeSoc:Decentralized Society，去中心化社会。
DeX:Decentralized Exchange，去中心化交易所。列子:可以通过 DefiLlama 找到完整的DEX列表
DID:Decentralized Identity，去中心化身份。
EOA:Externally Owned Accounts，以太坊网络中的个人用户，而非智能合约地址。
ENS: Ethereum Name Service，是以太坊上的一个去中心化域名服务。
FDV:Fully Diluted Valuation，即“完全稀释估值”。
flash loan: 闪电贷。在同一笔交易内完成借款和还款，常用于套利。
gas:燃料，表示在区块链完成交易所需的计算工作量。
gas fee:燃料费，表示在区块链完成交易所需的支付的以太坊费用。
hacked:被黑客攻击了。
HODL:hold on dear love，钻石手，长期持有。
Hard Fork: 硬分叉，是区块链上的一个重大改变，导致之前的规则与新的规则不兼容。
ICO: Initial Coin Offering 首次代币发行。
IPFS: InterPlanetary File System，是一个去中心化的文件存储和获取协议。
Keystore: 是一个加密的文件，通常用于存储用户的私钥。
Layer1: 常被称为主链或底层区块链,这一层包括了比特币、以太坊等主要的区块链网络。
Layer2: 建立在现有公链上的二层框架，通常会有更快更便宜的交易、更多的存储空间。
liquidity:流动性。
Liquidity mining: 流动性挖矿，指通过提供流动性来赚取代币的一种方式。
meme:谜因/梗，可以传播的东西。
Mempool:交易缓冲池，在交易被发出但未上链的时候，会出现在这里。
MakerDAO:去中心化稳定币平台。
Metamask: 一个流行的以太坊和ERC-20代币钱包，同时也是一个浏览器扩展。
Mainnet: 主网。一个加密货币项目的主要公共网络。
Multichain: 多链。允许在多个区块链之间创建和执行智能合约的平台。
nfa:Not Financial Advice，不是投资建议。
NFT:Non-Fungible Token，非同质化代币。
ngmi:Not ganna make it，不会成功的。
Node: 节点。在加密货币网络中，代表运行特定区块链的完整代码和数据的计算机。
OG:Original Gangster，元老。
oracle:预言机， 把信息通过去中心化的方式转到链上， 通过保证传入信息的去中心化程度和准确性， 从而保持该Dapp的去中心化程度。
OTC: Over The Counter, 场外交易。直接在两个当事人之间进行的交易，而不是在公开的交易所上。
paper hands:纸手，钻石手的反义词。
PoS:Proof of Stake，权益证明，参与者通过持仓质押维护区块链网络正常运行。
PoW:Proof of Work，工作量证明，参与者通过花费计算能力和能量维护区块链网络正常运行。
Protocol: 协议。定义网络如何进行交互的规则。
RWA (Real World Asset):真实世界资产。指那些在区块链外部存在的，如房地产、股票或其他形式的资产。在DeFi中，RWA的概念是试图将这些现实中的资产带到区块链平台，从而获得流动性或其他形式的金融操作。
Ring Signature: 环签名。一种加密签名，允许一个组的成员进行签名，但不会显示是哪个成员进行的签名。Monero加密货币使用这种技术以保持交易的匿名性。
solidity:以太坊上的编写智能合约的语言。
Staking: 质押。用户持有并锁定某些加密货币来支持区块链网络的操作，如验证交易，通常可以赚取奖励。
Stablecoin:稳定币，价值与法币或其他资产挂钩的代币，如USDT。
Swap: 在DeFi中，直接从一种加密货币兑换为另一种的操作。
Token Burn: 代币销毁。为了减少代币的供应量，永久从流通中移除某些数量的代币。
Trustless: 无需信任。在区块链环境中，参与者不需要互相信任，因为系统的设计保证了透明性和不可篡改性。
Uniswap: 一个流行的去中心化交易所（DEX），使用自动做市商（AMM）模型。
Vaporware: 空气币。
Validator: 验证者。在权益证明（PoS）和其他共识机制中，负责验证和建立新区块的参与者。
Volume: 成交量。在给定时间段内交易的资产总量。
Volatility: 波动性。表示资产价格在短时间内上下波动的程度。
wallet:钱包。
web3.0:以去中心化和数字所有权为特征的互联网，与Web1.0和Web2.0不同。
whale:鲸鱼，持有大量资金/筹码的人。
Wrapped Tokens: 封装的代币，如WETH。表示一种资产的代币化版本，常用于将非ERC20资产带到以太坊网络上。
Yield protocol: 提供质押收益的平台 列子:Convex， 可以通过 DefiLlama 找到完整的列表
zk:zero-knowledge proof，零知识证明，可以证明一个人拥有某个秘密，但不用泄露秘密本身。
ZK-Rollups: 一种Layer-2扩展解决方案，使用零知识证明来扩大以太坊的处理能力。

# 2025-08-20

今日笔记如下：
一、面试准备通用框架
1. 基础认知准备（所有岗位必备）
①、了解目标项目  ②、理解行业基础  ③、梳理个人经历
2. 通用面试技巧
①、自我介绍  ②、项目介绍  ③、技术问答  ④、反问环节
二、运营岗位面试准备
1. 核心能力要求
①、社区运营  ②、内容创作  ③、数据分析  ④、活动策划
2. 面试前准备清单
①、熟悉社区工具  ②、收集运营案例  ③、基础技能准备
3. 常见面试问题及回答思路
①、基础认知  ②、实际操作  ③、问题解决  ④、数据分析
三、技术岗位面试准备
1. 前端开发方向
①、技能梳理  ②、Web3 基础体验  ③、项目实践
2. 后端/智能合约方向
①、Solidity 基础  ②、开发环境  ③、实践项目
四、面试实战技巧
1. 技术问题应对策略：诚实承认，说明学习计划和方法
2. 非技术问题应对：展示学习能力和成长意愿
3. 面试中的加分表现：展示对公司和岗位的真正兴趣
五、面试后的跟进
1. 当天总结：记录面试过程中的问题和自己的表现
2. 24 小时内跟进：发送感谢邮件，简单回顾面试内容
3. 持续改进：补强薄弱环节的知识和技能，为下一次面试做更充分的准备
六、新手常见误区
1. 准备误区
❌ 只背概念，不做实践
❌ 追求高深技术，忽略基础
❌ 只准备技术，不关注项目
❌ 临时抱佛脚，缺乏系统性
2. 面试误区
❌ 不懂装懂，胡乱回答
❌ 只等面试官提问，不主动交流
❌ 过分紧张，无法正常表达
❌ 只关注薪资，不问发展机会
3. 正确心态
✅ 面试是双向选择，不是单向考试
✅ 展示学习能力比展示现有水平更重要
✅ 诚实表达想法，不要刻意迎合
✅ 把面试当作学习机会，无论结果如何

# 2025-08-19

一、为何 Web3 简历要“去中心化”思维
1.可验证的个人品牌形象”。总的来说有三点：
①你的价值： 取决于你持续产出的链上痕迹（运营 / 开发）和在社区中建立的信任度。
②稀缺性： 源于你独一无二的组合技能（例如 DeFi 高手 / meme 图高手 / 链上治理高手等等）。
③贴合度： 你的价值与技能与该职位需求的耦合程度。
二、简历结构示例
①个人信息  ②学历  ③详细经历  ④技能栈 & 工具  ⑤社区与 DAO 角色  ⑥奖项
三、核心观点：Web3 简历的“三板斧”
1.链上战绩 > 宏大叙事
2.参与度曲线 > 从属关系
3.技能—结果 双链路
四、关键 Web3 模块写作要领
1.明确自己的定位
2.关键贡献（Key Achievements）
3.经历段落（STAR 模型）
4.技能 & 工具
5.社区 & DAO 角色
五、量化指标参考
示例①：2025 年 5 月 1–7 日，官方群从 12,350 人涨到 13,200 人，新增 850 名成员。
示例②：Discord “Memecoin 表情包大赛”收到 380 份有效投稿，活动公告被 9,000 名成员浏览，参与率 4.2%。
六、简历模版参考
推荐使用在线简历生成器（例如：https://www.canva.com/templates/EAGO_l7bbes/）来制作简历，方便填写同时比较简洁美观。
简历要保持简洁，不要过于花哨，重点突出跟岗位相关的技能和经验，避免其他的喧宾夺主。
七、其他
总长： 建议控制在 1–2 页，确保关键信息一目了然。
重点前置： 将“关键贡献”模块放在简历首页最显眼的位置。
格式统一： 统一使用动词开头，并高亮重要的数字和量化结果。

# 2025-08-18

招聘平台与职位推荐
一、选对招聘平台：Web3 求职的黄金法则
及时、真实、准确
二、优质招聘平台推荐
①Web3.career ②DeJob ③SmartDeer
三、其他信息渠道
①LXDAO ②ETHPanda ③项目方官方 Discord / Telegram 社区 ④Twitter(X) ⑤LinkedIn ⑥Web3 行业新闻和研究报告 ⑦Web3 线下活动/线上 AMA
四、如何判断项目方是否可靠？
搜集项目方信息，建立《项目可靠性评估表》，进行综合评价
五、Web3 职业发展路径
1. 运营向 大使计划→实习生→正式成员
2. 技术向 不同于运营，在技术层面可能没有所谓的大使计划，更多的是一步到位直接从实习生开始干起，或者通过参与开源项目、黑客松等方式积累经验。Web3 技术岗位的专业性强，对技术栈要求较高。

# 2025-08-17

如何成为靠谱的 Web3 实习生
1.给靠谱下的定义：“可预期、可沟通、可复盘”
2.在任务时间评估上：“经验倍数法”，对新人特别有用。比如你觉得一个任务需要 3 天，那么要根据你的熟悉程度来调整。
3.沟通频次和方式上：如果你卡住了超过半小时还没有思路，就要及时求救。不要觉得不好意思，这样反而能让整个团队更高效。
4.技术深度和交付上：采用“分层迭代”的方式。先把核心功能跑通，然后再考虑性能优化、错误处理这些。这样做的好处是，即使时间不够了，也有一个可以交付的版本。
5.哪些软技能：清晰沟通、深度思考、反思能力
6.如何区分能力和态度问题呢：我的经验是看他是否能从错误中学习。如果同样的错误反复出现，而且他不主动反思和改进，那就可能是态度问题。但如果他的错误类型各不相同，而且能快速吸取教训，那就可能只是能力短板，可以通过额外的指导来解决。通常我会对实习生有更多的耐心。

# 2025-08-16

“Dirty Work 是加速器”新闻、Marketing、咨询、研报轮番上手；我在 2022 年主持 50+ 场中英 AMA，撰写“我们为什么投资 XXX”系列，曝光与能力同步成长。
VC 弹性大，项目方更看重 ROI”VC 可以“烧钱做品牌”，效果对 LP 负责；项目方的每一美元都要拉动真实用户或开发者，投产比被实时追踪。
受众不同导致增长逻辑完全不同”VC 面向项目方与 LP，重“品牌资产”；项目方面向用户与社区，重“留存与转化”，这也是我决定转向项目一线的核心理由。
市场也在慢慢变风向，原来看项目能不能做起来是看 VC 的背景好不好，但现在是看社区的热度够不够，做社区是一个项目最关键的部分和能力。
“选题 = 叙事 + 热点” 确保主题既能承载项目核心信息，又是当下社区想聊的。
“选对合作方” 对方愿意共同宣传、共创内容，效果事半功倍。
“做复盘” 整理 transcript、发布数据总结贴，形成二次传播。

# 2025-08-15

今日主题：技术后端向 Jason：资深 Web2 开发者的转型之路
今日笔记：Web3领域充满了机遇，但进入需要策略和准备。过实习积累经验，同时夯实计算机基础知识，这是进入的关键。技术迁移方面，前端开发者相对容易，而底层开发者则需培养产品思维。面对AI时代的挑战，理解业务和做决策的能力尤为重要。根据背景选择路径：商科擅长数据分析，理工转向Solidity或Rust，文科则可发挥市场或运营才能。在校生应通过实习发现不足，回校加强基础，形成良性循环。一句话总结：认清自身优势，找到行业缺口，学习语言只是开始，理解业务才是立足之本。Web3早期，机会多，从小处切入，快速试错，找到适合自己的方向

# 2025-08-14

技术向前端 Logic：Bybit 先锋的前端取经之路
Web3 需长期积累，代码与业务理解并重，AI 是工具而非替代品。
从简单项目和小社区开始，积累经验后迈向更大舞台。
老师对 AI 工具的看法
①AI 的作用：解决 30% 的机械劳动（如生成 CSS、解析错误栈）。
②AI 的局限：无法替代复杂业务需求拆解、样式依赖处理和文档编写。
 Web2 与 Web3 前端的差异：
①区别：Web3 需理解钱包连接、Gas、滑点等业务逻辑。
②建议：亲手在 Testnet 操作，提升代码理解。

# 2025-08-13

安全与合规
一、 Web3 合规性要求与常见法律风险
1. 核心法律风险梳理
①代币发行与交易行为的法律风险
②赌博、传销、洗钱等刑事风险
③场外交易中的洗钱与非法经营风险
④民商事争议
2. 全球监管背景与趋势
①FATF（Financial Action Task Force，金融行动特别工作组） 是一个政府间组织，成立于 1989 年，总部位于法国巴黎。它是全球反洗钱和反恐怖融资标准的制定者。
②稳定币监管的意义
③全球主要监管趋势
3. Web3 入职法律风险防范指南
①雇佣关系新形态
②薪酬结构及风险提示
③虚拟货币出金与合规风险
④项目合法性需提前审查
4. Web3 项目的刑事风险及案例介绍
①开设赌场罪、赌博罪
②非法经营罪
③非法吸收公众存款罪
④组织、领导传销活动罪
⑤洗钱罪
5. 其他风险
①虚拟货币风险
②代币发行的风险
③挖矿的风险
二、 常见网络安全风险与防护措施
1. 常见网络安全风险与攻击方式
①钓鱼攻击（Phishing）
②恶意软件/木马（Malware & Trojan）
③社交工程攻击（Social Engineering）
④供应链/第三方依赖攻击
⑤地址污染与扫描木马（Clipper/Scanning Bots）
⑥传统隐私与账号安全风险
2.防护清单（学生版）
①面试/实习相关
只用官方 Zoom/Teams/腾讯会议等公开工具，拒绝安装任何“专用面试软件”
核实面试邀请邮箱、域名、联系人，多渠道确认
遇到“提前安装软件”“下载资料包”等要求，务必警惕，先查官网/Google/知乎/Reddit 等是否有安全警告
②奖学金/空投/福利活动
任何要求“连接钱包”“签名验证”“输入助记词/私钥”的网页，99% 是骗局
空投、福利只用专门测试钱包，主钱包冷存储，绝不轻易暴露
收到“缴费”“奖学金调整”类消息时，先拨打官网热线复核；校园通知留意域名是否官方主域。
③社交平台/群聊
不轻信群内“官方人员”“管理员”“学长学姐”的私聊和链接
遇到“帮忙测试”“转发链接”“紧急转账”等请求，务必多方核实
④软件/插件安装
所有软件、插件只从官网、官方应用商店下载
安装前先查口碑、用户数、是否有安全警告
浏览器插件控制在 3 个以内，且只用官方钱包、密码管理器、广告屏蔽器
面试／竞赛前索要安装包时，要求公开 MD5 与官网对比；核查数字签名颁发者和时间戳。
开启系统防火墙、浏览器插件权限管理；对摄像头与麦克风按需授权。
⑤账号与密码安全
重要账户启用 2FA（短信易被劫持，优选谷歌验证或硬件密钥）；不要在同一浏览器里保存助记词与日常 Cookie。
使用密码管理器（如 1Password、Bitwarden），每个平台设置独立强密码
邮箱、手机号安全重于一切，定期更换密码，防止被劫持
⑥钱包与转账
转账前务必核对地址前 6 位和后 4 位，防止剪贴板被劫持
助记词/私钥只离线保存，绝不截图、上传网盘或发给任何人
定期检查钱包授权，及时取消不必要的授权（可用 Revoke.cash、Etherscan 等工具）
三、 骗局实际案例分享
①高级攻击方式其实真很多
②来自“官方”的钓鱼

# 2025-08-12

社区运营指南
一、社区运营核心职责
1. 日常内容与社群维护
2. 内容发布与互动引导
3. 活动策划与组织
4. 对外合作与社区联动
二、常见工具/平台使用
1. 社交媒体渠道
2. 社群沟通平台
3. 内容创作工具
4. 数据与行业调研工具
三、常见任务案例模板
1. 线上活动执行模板
2. 线下活动策划与执行

# 2025-08-11

智能合约开发
一、Dapp 架构和开发流程
1. Dapp 架构-①前端②智能合约③数据检索器④区块链和去中心化存储
2. Dapp 开发流程-①需求分析与规划②智能合约开发③检索器开发④前端开发⑤与区块链交互⑥部署上线
二、以太坊开发环境搭建
1. 基础环境准备
2. 以太坊本地开发链
3. 以太坊钱包和前端交互
4. 其他常用工具
三、Solidity 智能合约编程（简单介绍）
对于运营岗位来说太难了，这里只是简单了解
四、智能合约实战项目
仅作了解
五、以太坊技术基础
1. 帐户模型
2. Gas 机制
3. 交易生命周期
六、部署合约
1. 测试网
2. 领取 Sepolia 代币
3. Remix 部署到 Sepolia
七、区块链前端整合
1. 前端与合约交互工作流程概览
2. 实例操作
八、高阶内容
1. Gas 优化
2. 合约安全
3. 智能合约审计
4. 开发协作规范

# 2025-08-10

区块链岗位全景图
一、技术岗
1. 前端工程师
2. 后端工程师
3. 智能合约工程师
二、非技术岗
1. 产品与运营
2. 社区管理
3. 研究分析

# 2025-08-09

昨天有事没有跟上直播，今天查看下昨天zoom会议的故事会

# 2025-08-08

Web3 工作方式：
一、Web3 常用工具及远程办公软件
推特、Tg、Discord、小狐狸、领英、Notion、Zoom、Calendly、Figma、GitHub
二、远程协作习惯
1. OKR 写法与最佳实践
2. 远程会议注意事项
3. 职场软技能
三、行业黑话
1. 基础类
DYOR	Do Your Own Research，投资前请自行研究，项目方常用于免责
FOMO	Fear of Missing Out，害怕错过，指因贪婪而追高的情绪
WAGMI	We're All Gonna Make It，大家都会发财，社区常用打气口号
2. 技术类
L1 / L2	Layer 1（主链，如以太坊）和 Layer 2（扩容方案，如 Arbitrum、zkSync）
EVM	Ethereum Virtual Machine，以太坊虚拟机，运行智能合约的核心
Smart Contract	智能合约，自动执行合约逻辑的链上程序
3. 投资类
Pump	拉盘，代币价格快速上涨
Dump	砸盘，代币价格快速下跌
HODL	原为"Hold"打错，后来变成文化，意为坚定持币不卖
4. 社区与文化类
gm / gn	good morning / good night，Web3 社区日常打招呼方式
Anon	匿名者，社区中不透露真实身份的成员
KOL	Key Opinion Leader，意见领袖，影响力人物
5. 开发类
Hardhat / Foundry	常用的智能合约开发框架
RPC	Remote Procedure Call，链上节点访问接口
Gas	交易费用，以太坊中以 Gwei 计费

# 2025-08-07

一、DeFi（去中心化金融）
1. Uniswap：去中心化交易所（DEX）
2. Compound：去中心化借贷协议
3. MakerDAO（现已更名为 Sky）：稳定币系统

二、NFT（非同质化代币）
1. NFT 的本质：数字资产的唯一性和所有权
2. 智能合约：自动化的所有权转移和交易
3. CryptoPunks：NFT 的先锋
4. OpenSea：NFT 交易的中心

三、DAO（去中心化自治组织）
1. Nouns DAO：社区驱动的 NFT 艺术 DAO
2. LXDAO：支持 Web3 公共物品的建设者
3. ConstitutionDAO：一场疯狂的拍卖

四、MEME（模因币）
1. DOGE：MEME 币的开山鼻祖
2. PEPE：社区驱动的 MEME 币

五、交叉创新领域
1. DeFi + NFT：数字资产金融化
2. DAO + MEME：社区文化与治理融合
3. AI + DeFi：智能化金融服务
4. WEB3 + 乡建：南塘 DAO 的探索

六、2025 年新兴趋势
1. Intent-Based 交易
2. 账户抽象与智能钱包
3. 模块化区块链
4. AI + Web3 融合

七、学习建议与职业规划
对于初学者：
1.基础优先：深入理解 DeFi 核心概念，而非追逐最新热点
2.实践学习：在测试网上体验各种协议，理解用户流程
3.安全意识：了解常见攻击向量和防护措施
对于求职者：
1.专业化发展：选择 1-2 个领域深入研究
2.技术栈匹配：根据目标岗位学习相应技术
3.项目经验：参与开源项目或 Hackathon
4.行业动态：持续关注最新发展和监管变化

延伸阅读资源：
1.技术文档：Ethereum.org、各项目官方文档
2.研究报告：Messari、The Block、Delphi Digital
3.播客节目：Bankless、ETHPanda Talk

# 2025-08-05

1、区块链是一种去中心化的分布式账本技术，区块链特性：不可篡改、公开透明、匿名、快速交易。
2、分布式网络+区块链新特性：去中心化、真正的不可篡改。
3、虚拟货币为网络节点服务提供商提供奖励。不同的网络服务提供商可以得到不同的代币奖励，比如：比特币。
4、比特币的货币属性：根据比特币的设计，它仅有有限的供应量，而且可以自由转账。因此具备了货币的特性，成为了加密货币。
5、区块链的核心组成部分：去中心化的网络和区块链 和 维持网络运行的代币激励。
6、区块链根据访问权限与治理模式，大致可分为三类。按照去中心化程度从高到低排列为：公链>私链>联盟链。

# 2025-08-04

参加以太坊中文周会（了解最新行业动态）


# 2025.07.29


<!-- Content_END -->
