---
timezone: UTC+8
---

# Elorze

**GitHub ID:** Elorze

**Telegram:** @Elorze

## Self-introduction

充满好奇心，保持热情

## Notes

<!-- Content_START -->

# 2025-08-29
<!-- DAILY_CHECKIN_2025-08-29_START -->
今天是这一期的最后一天咯！真是学到了不少东西，感谢这次活动的主办方呀。看了一下之后的学习路径，打算多看一些全栈开发！希望能往web3这条路走！
<!-- DAILY_CHECKIN_2025-08-29_END -->


# 2025-08-28
<!-- DAILY_CHECKIN_2025-08-28_START -->
听讲座。哎呀好多公司呀，真不错！想看看技术岗。

感觉 BGA 的 chainforgood挺好的，记录一下：

About BGA

Launched in 2024 by Bybit, the Blockchain for Good Alliance (BGA) is a long-term collaborative non-profit initiative with the main goal to contribute to societal good by using blockchain technology to solve real-world problems.

Our goal is to position blockchain as a tool with the capabilities to address some of the most pressing global challenges, aligned with the 17 Sustainable Development Goals of the United Nations.

BGA Deck | [https://tinyurl.com/BGA-introduction](https://tinyurl.com/BGA-introduction)

BGA Videos | [https://youtu.be/JC7CEC2yceE](https://youtu.be/JC7CEC2yceE)

BGA website | [www.chainforgood.org](http://www.chainforgood.org)

BGA X | @ChainForGood

BGA Linkedin | [https://www.linkedin.com/company/blockchainforgoodalliance](https://www.linkedin.com/company/blockchainforgoodalliance)

BGA TG Group | [https://t.me/+u-PglgK9dEswN2I1](https://t.me/+u-PglgK9dEswN2I1)

比特鹰：所有岗位都可以实习，没有截止时间，长期招聘。可惜base广州，单休。微信：Btc100998

了解到了很多公司……
<!-- DAILY_CHECKIN_2025-08-28_END -->


# 2025-08-27
<!-- DAILY_CHECKIN_2025-08-27_START -->
听了昨天的实习讲座。

几份好的简历：

[https://www.abhisheksah.dev](https://www.abhisheksah.dev)

[https://ahmadgill-portfolio.netlify.app](https://ahmadgill-portfolio.netlify.app)

要突出自己的技术栈！最多两页，要精简一点。

要多写技术博客。

听了今天的运营讲座。也很注重前期积累。
<!-- DAILY_CHECKIN_2025-08-27_END -->


# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
今天听了一期区块链的博客，学了几个双语术语：

| English Term (英文术语) | Chinese Translation (中文翻译) | Context / Explanation (上下文/解释) |
| --- | --- | --- |
| Cryptocurrency | 加密货币 | Digital or virtual currency that uses cryptography for security. |
| SEC (Securities and Exchange Commission) | 美国证券交易委员会 | The U.S. federal agency responsible for enforcing securities laws and regulating the industry. |
| Macro (Macroeconomics) | 宏观经济 | The branch of economics dealing with large-scale factors (interest rates, national productivity, etc.). |
| FOMC Meeting | 联邦公开市场委员会会议 | The Federal Open Market Committee meeting where U.S. monetary policy, like interest rates, is set. |
| Hawkish | 鹰派 | A term for policymakers who favor higher interest rates to control inflation. Opposite of Dovish (鸽派). |
| Rate Cut | 降息 | When a central bank (like the Fed) lowers interest rates to stimulate the economy. |
| Jobs Report | 就业报告 | A key economic indicator, like the U.S. Non-Farm Payrolls report, showing the health of the job market. |
| Portfolio | 投资组合 | A collection of financial investments held by an individual or institution (stocks, crypto, etc.). |
| Asset | 资产 | An item of value owned, like Bitcoin, Ethereum, or a stock. |
| NFT (Non-Fungible Token) | 非同质化代币 | A unique digital token representing ownership of a specific item, like CryptoPunks. |
| CryptoPunk | 加密朋克 | A specific, highly valuable collection of NFTs (profile pictures). |
| Token | 代币 | A digital asset issued on a blockchain. |

| airdrop | 空投 | The distribution of free tokens or coins to wallet addresses, usually to promote a project. |
| Blockchain | 区块链 | A decentralized, distributed digital ledger that records transactions. |
| On-chain | 链上 | Activity that occurs on the blockchain and is recorded on the public ledger. |
| DeFi (Decentralized Finance) | 去中心化金融 | Financial services built on blockchain without traditional intermediaries like banks. |
| GM / GN | 早安 / 晚安 | Common crypto Twitter/Twitter slang for "Good Morning" / "Good Night." |
| Trader | 交易员 | A person who buys and sells financial instruments. |
| Seller | 卖家 | In this context, the person or entity selling assets, causing the price to drop. |
| DYOR (Do Your Own Research) | 做好你自己的研究 | A common disclaimer reminding people to investigate before investing. |
| Not Financial Advice (NFA) | 非财务建议 | A standard disclaimer stating that the information shared is not a recommendation to buy/sell. |
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
继续听 my first dapp 讲座。

usechain的hook，实际上不是获取用户当前链接的钱包，而是应该用isonsepolia的判断。

usereadcontract。参数需要一个地址，说明这个nft是mint给谁的。通过这个hooks拿到balance的值返回到前端。

读了这篇文章：[https://www.ddmckinnon.com/2024/01/25/on-climbing-the-stat-arb-cex-dex-leaderboard-comparative-advantage-and-careers-and-my-future-in-crypto/](https://www.ddmckinnon.com/2024/01/25/on-climbing-the-stat-arb-cex-dex-leaderboard-comparative-advantage-and-careers-and-my-future-in-crypto/) 感想是加密货币市场的统计套利实在是太卷了……
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
今天听了 My first DAPP 讲座回放。

mint函数，mint资产。safemint。继承了ERC721的模版，定义了一些具体的行为。tokenuri，返回URI的链接。查询函数。

部署合约，填好链接，运行

```
source.env && forge script script/DeploySimpleNFT.s.sol --rpc-url SEPOLIA..
```

ABI：可以类比普通的后端，定义了一些接口，比如balanceof。打开前端的项目，建立一个abi文件。

前端集成的话要关注的：安装一个钱包插件，

```
npm install @rainbow-me/ranbowkit wagmi..
```

要复制一些rainbow上的配置。

useReadContract：读取单个合约的数据

useWriteContract:向合约里写入数据

这个没听完，明天继续听。把 最佳远程&数字游民 那一期听完了。远程工作果然还是很诱人啊……运营岗和宣传岗一个人可以打好几份工……不过确实是，应该用创造的价值去衡量劳动力，而不是时间，坐班有的时候压根没活儿，太痛苦了……远程工作要规划好自己的时间安排，感觉可以选取自己精力最好的时间工作，用最少的时间实现做大的价值。

告诉钱包我们对接的合约里有哪些接口。

mint按钮：绑定了handleMint函数，里面是mint()函数，通过useMint()生成，用到了useWriteContract里的writeContract，可以写合约的地址，abi，调用的合约函数。

点了这个按钮后会发起一个写入的请求，会要求支付一些gas费，完成写入的操作。区块链是一个链状结构。进入到一个待打包的区块。
<!-- DAILY_CHECKIN_2025-08-23_END -->

# 2025-08-22

听了讲座。

一个DAO的设计。良心道采取一人一票的制度，通过slapshot来进行资金发放、共识之类的。如何分配角色：怎么样算加入一个DAO了。我是一个开发者，我是一个产品，我是一个设计师，我怎么协作。现在区块链里面会遇到很多攻击。DAO的分配机制里要考虑准入机制，许可制还是有币就行；女巫攻击；如何分配贡献。   第四个是如何管理资金。资金管理应该是透明的。实现高效化。国库的资金分配，常见的资金管理模式，用safe多签的管理。slapshot提案通过分配资金，多签是轮换制度，声誉强就可以参与多签国库的选举。   良心道专注于研发做了二十多个产品；公共物品的支持者。   经验教训：最开始是核心团队+社区，一些像基金会的角色，问题是新加入DAO的成员参与意愿很低，去中心化的程度低，会有核心的利益冲突。像OPTIMISM就产生利益分配不均的问题   改良工作组+项目组，社区的人可以加入工作组和项目组去推动具体的事物。问题：不同专业背景的成员协作方式会很有问题，怎样达成效率。   第三阶段：自己登记贡献，然后大家投票。没有不同的年限什么的限制，就是相互投票。问题：大家在后面的阶段会确实目标；协作方面会产生摩擦，因为没有路线图去驱动这个DAO组织。    第四阶段，还在过渡。更多的路线图推动。主路线图分了很多方向，不同的方向提出小的pode，如果可持续，会升级成一个项目。可以高效执行、灵活治理；权力下放了。     pod机制：小型知识团队，3-5人就可以组成，可以申请资金，需要有明确的交付物。快速完成，快速推进，最终达成路线图核心方向的完成。    清迈东京曼谷办过公共物品的活动。良心DAO开放了一些实习的任务，参与可以获得学分和USDT的奖励。     贿选和治理攻击。     贿选，抵抗制度：举报+相互投票。设置一些反捕获委员会。选择退出分叉，EOS。

# 2025-08-21

今天听了学英语的 twitter 博客。关注了 rug radio 这个youtube账号，看了一条视频，记录一下英语术语：

| 英文                      | 中文                              |
| ----------------------- | ------------------------------- |
| Volume Mirror           | 成交量镜像（衡量真实成交的指标）                |
| Myriad                  | Myriad（文中提到的预测市场平台）             |
| Pre-IPO Market          | 上市前预测市场                         |
| Mention Market          | “提及”预测市场（押注某人/事件是否被提到）          |
| Pump ICO                | 快速抢购型首次代币发行                     |
| Free-Money Bet          | 白送钱的赌注（高确定性套利盘口）                |
| Meta-Market             | 元市场（对预测市场本身再做预测的市场）             |
| On-Chain Reputation     | 链上声誉（不可篡改的预测记录）                 |
| Creator Fee Share       | 创作者手续费分成                        |
| Soft-Core Gambling      | 轻度博彩/软核博彩                       |
| Zero-DTE Options        | 零日到期期权                          |
| Robinhood Integration   | 罗宾汉集成（券商嵌入预测市场）                 |
| Forecasting Terminal    | 专业预测交易终端                        |
| Liquidity Farming       | 流动性挖矿（为盘口提供深度赚奖励）               |
| Pre-Market Prediction   | 盘前预测（代币正式交易前的价格预测）              |
| Abstract (platform)     | Abstract（文中提到的预测市场/交易平台）        |
| Plinko Giveaway         | Plinko 游戏空投/抽奖                  |
| FOMO Hour               | FOMO 时段（直播中的高波动时段）              |
| KBM / Rec Radio         | KBM 电台节目/Rec Radio（文中提到的加密直播频道） |
| ETH 4,100 / 3,400 Level | ETH 关键心理价位（文中多次作为示例盘口）          |
| Jackson Hole Bet        | 杰克逊霍尔会议押注（宏观事件预测）               |

# 2025-08-18

今天听了晚上的讲座，下载了很多的应用。tako，farcaster，buluesky。觉得去中心化的这种社交平台挺不错的。基本上用户注册时账号会上链，不过信息是不上链的，成本太高。从推广的角度，这些app不会以【用户能自己保有数据】作为营销点，更多是吸引一些web3相关领域的人，之后会在app上发布一些金融相关的新产品。目前farcaster还没盈利，每月还要倒贴钱用户激励hhh。可以在app里转账，app会收取低廉的服务费。

听周六的【新人小白如何快速融入web3职场】，法律社保问题果然绕不开hhh。

# 2025-08-17

github.com/crazyyuan/defi-fixed-yield-course/blob/main/docs/lessons

remix可以部署合约，这样就不用写私钥了。

hardhat的配置。部署到sepolia，11155111。

合约部署完后会有个地址。前端代码一样的配置。三个合约，vault， underlying, reward。

etherscan可以看到交易的详情。

配置，主要是由rpc网络。合约读取：走dapp配置的RPC。交易签名/广播：走钱包的RPC。

合约代码分装部分。读合约数据。

ABI：可以理解为一个接口，前端可以用哪些。合约部署完以后会有专门的ABI。如果前端要集成，就拷贝下来VAULT_ABI。

lido.fi

RUST比较难，没必要可以不学。

读了一下github链接。

| **特性**   | **P2P同步**   | **C/S同步**   |
| -------- | ----------- | ----------- |
| **控制方式** | 去中心化，节点自治   | 中心化服务器主导    |
| **容错性**  | 高，无单点故障     | 依赖服务器稳定性    |
| **扩展性**  | 随节点增加而增强    | 受服务器带宽限制    |
| **延迟**   | 可能较高（需多跳传播） | 通常较低（直连服务器） |

# 2025-08-15

gas机制
总费用=gas used*(base fee+priority fee)。gas used 实际消耗的计算量：有操作复杂度决定的固定值。base fee 网络基础费用：网络自动调整 被销毁：使用率大于百分之五十，上涨12.5，使用率小于百分之五十，下降12.5。priority fee 优先级小费：用户设置给验证者。


日志，通过写入文字，可以拿下数据。链上数据获取最重要的一个途径。因为上链很昂贵。通过事件日志就可以拿下来，再结构化，拿到前端。对dapp做更好的优化。

index可以做索引，获得日志数据。

solidity学习资料：github: defi-fixed-yield-course/resource/solidityinonepicture.png

合约：

ERC4626：标准化金库接口，提供deposit/withdraw等功能。
ERC20：稳定币。
ERC721
ERC1155
以太坊生态出现的，统称为以太坊改进计划。
eip.fun 可以查到这些协议地定义作用。这是最直接地方式。可以了解一下这个合约。
或者直接看 openzeppelin。合约标准的实现。

精度合约。往合约里存代币，会给一些线性奖励，可以累积。合约可以累计记录算出来清清楚楚。
最核心的是BPS。定义了存的利率，百分之五的年化。初始化。ERC4626是ERC20继承过来的。

shares，股份，质押一些token。是另外一种类型的token。

withdraw取款。claim。getpendingreward实时计息。

# 2025-08-14

昨天忘记打卡了hhh。听了两个会。搭建自己的测试网，这个之前残酷共学的时候我尝试过，可惜没成功。

今天补听了之前的Dapp架构。

dapp的特色是和钱包、区块链交互。后端把智能合约当作纯后端，和传统的后端差异比较大。链上数据比较轻量，与传统数据库存储差异较大。数据一致性，冲突的解决（共识算法，从pow到pos），数据同步（p2p点对点网络），性能。

与钱包的链接，私钥管理。交易的确认：传统靠后端的代码和认证，最终数据存储下来；区块链靠链的状态，永久存储下来，不可篡改。

现在主流的dapp是传统技术和去中心化技术相融合。只有前端+合约的应用不多，都是比较小型的。

以太坊所有的智能合约部署上去会同步到所有的节点。智能合约本身是不能自己执行的，是需要被调用才会执行它的逻辑。




晚上听技术讲座：
在solidity中，有“冷”和“热”，调用合约的基础成本warm100gas，冷2100gas。v4,2100+100就行，都在一个pool里。

单体架构，做项目的时候权衡。

不要用view。

资产负债抵消，统一计算。callback不友好。ABC递归的方法实现，在上一个操作的结尾展开一个新的山下文，引用delta逻辑，会更友好。

flash loan原子性。v4不收手续费。

外围合约。把输入给核心合约。核心合约会call data。优化：call data不进内存，因为它非常大，进去的是calldata,出去还是calldata。solidity是写不了的。

审计：
uni有这样一个市场，会有很多审计机构报价，要不停做沟通。报价之前会开个会，会说报价和说审计工程师是谁，有些不提供工程师名字。我们一般关注审计项目和我们的项目的相似度。有附加服务，形式化证明：挺贵的；bug bounty：被黑掉了能拿到多少钱，默认带一到两个月。会有很多品牌，经济品牌高端品牌，报价是不一致的。

听着懵懵的！有时间读读老师的博客：https://blog.wssh.trade/ 。 

AI做审计的前提是rag，checklist给AI，让AI查。

solx编译器为什么会省gas，但是可能不太安全；编译器有bug后面被黑了很麻烦。solidity的官方编译器是安全；可以自己编译省gas。

ERC20->SAFE->AAVE V3/uniswap v2/uniswap v3（第一个很难）。

solidity能读懂v3的知识结构。v4的非数部分。defi的逻辑又是不太一样，想入职借贷公司可以看morpho，代码很简单，排名前几的借贷协议。

ide是vs code。ai内联汇编还是不太懂，会给出点错误的东西，debug很麻烦。老师vscode安装的插件：Name: solidity
Id: JuanBlanco.solidity
Description: Ethereum Solidity Language for Visual Studio Code
Version: 0.0.185
Publisher: Juan Blanco
VS Marketplace Link: https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity,注意不要下载到盗版。mapping会容易报错……

https://t.me/web3list。老师的tg，里面有很多合约大佬。zk和智能合约编程方向。

# 2025-08-12

今天参加了技术和运营的会议。
技术会议印象比较深的是说到web3很多是开源的，想做产品可以看看别人的内容、再结合自己新的思路。可以多看官方的doc，要找工作的时候也可以看对方的doc是不是写得清楚明了。
运营会议学到了tg bot的简单使用。

读了thirdweb的SDK和API说明，尝试把昨天铸造的NFT在网页上展示出来。写了ts和vue文件，但是vue读取不到我设置的环境变量，这个问题还没解决，明天再看看。

# 2025-08-11

今天在 thirdweb 尝试铸造NFT。
因为钱包是 safe account，研究了一下决定：
### **部署时用 Owner Account，后续 mint 用 Safe**

- 合约部署（必须用 EOA，因为 Safe 在 Dashboard 上直接部署可能报错）
- 部署好后，把合约的 **MINTER 权限** 或管理员权限赋予 Safe 地址
- 之后 mint NFT 就用 Safe 签名发交易（可以通过 Safe App、Safe SDK、账户抽象等方式）
- Gas 会从 Safe 支付

今天完成了合约部署和第一个 NFT 的铸造。
看了一下 thirdweb 提供的 getOwnedNFTs 和 NFTProvider 的使用说明，明天尝试一下在网页上展示出今天铸造的NFT。

看了一点白皮书上solidity 的语法。

# 2025-08-10

今天了解了safe account:
Safe，前身为 Gnosis Safe，是一个基于智能合约的多重签名钱包，也是一个可组合的智能账户框架。它主要用于增强数字资产的安全性，并提供更灵活的账户管理功能。简单来说，Safe 允许用户通过设置多个签名者来管理资产，从而避免了单一私钥泄露的风险，并支持更高级的交易设置和访问管理。﻿
以下是Safe 的一些主要特点和优势：
多重签名安全:
Safe 使用多重签名机制，需要多个签名者批准交易才能执行，大大提高了安全性，防止了单点故障。﻿
智能合约钱包:
作为智能合约钱包，Safe 具有可编程性，支持更高级的交易逻辑，如批量交易和模块化功能，如限制单日交易额。﻿
灵活的访问管理:
Safe 允许用户添加具有特定功能的模块，如限制单日交易额，这对于去中心化自治组织(DAO) 非常有用。﻿
可组合性:
Safe 的智能账户框架允许与其他DeFi 和区块链应用进行集成，提供更广泛的功能和应用场景。﻿
生态系统建设:
Safe 不仅是一个钱包，更是一个生态系统，提供开源工具和基础设施，帮助用户和开发者向智能账户过渡。﻿
抗量子威胁:
Safe 的智能账户设计还考虑了未来量子计算的威胁，为用户提供更长期的安全性.﻿
总而言之，Safe 是一个功能强大且安全的智能账户解决方案，适用于个人、机构和DAO，用于管理数字资产和参与DeFi 和其他区块链应用

遇到了问题：
用私钥将我的 safe account 导入 metamask，发现metamask上显示的是 owneraddress ,同时我的代币也没显示，都是0。从水龙头获取测试币都是用 safeaddress的。目前还在研究 safeaddress 和 owneraddress 的区别。

解决：
导入的是 Safe 的 owner 私钥 → MetaMask 显示的是 owner 地址；
测试币是发到 Safe 的合约地址（safe address）；
所以 MetaMask 里看不到这些代币，因为那不是 owner 地址的余额。
✅ 正确的做法（查询 Safe 钱包的余额）：
使用 Safe App 官网
打开 https://app.safe.global
连接钱包（MetaMask 登录 owner 地址）
输入 Safe 地址（safe address）
就能看到 Safe 钱包里的余额和交易记录

继续做了几个钓鱼攻击。

# 2025-08-07

今天听了法律安全知识的讲座，学到了一定要做好背景调查！不能一不小心把自己弄进去了……还是要谨慎呀！涉及到交易所的项目要格外小心。要小心的：返佣多于一级就算传销了，团队也不行；赌博，输入是钱，中间不确定，输出是可能变多的钱，这种活动就容易被定为赌博，所以一般会在这三个阶段做些别的什么。有些地方会把“对赌”也判定为赌博，不过案子基本是两年前的了。这个行业法律更新的好快呀，要实时跟进，不能触碰法律红线……很多平台技术上要对【中国公民】有限制，比如说ip地址识别、护照信息（但一般只要有国外签证就可以，因为面向国外华侨）、中国的手机号不能注册；宣传对象不能是中国人，比如网页用外语写，而不是简体中文。平台要进行反洗钱调查，个人要有反洗钱意识。

# 2025-08-06

今天参加了MARCUS老师的分享会，听了故事。很多名词还不是很明白，打算之后慢慢了解。
比较有感触的记一条笔记： 

DPoS的治理危机在于“过度中心化”，主要表现为：

少数节点权力过大：少数超级节点掌握了网络运营和治理的绝大部分权力。
投票权集中：代币量决定投票权，大户可操控选举，形成“富者愈富”。
串谋寻租风险：少数节点间可能串通，利用权力谋取私利。
普通用户参与度低：多数用户不参与投票，使治理权更集中。

做了 https://unphishable.io/challenges/fake-extension-phishing 钓鱼挑战的beginner部分。学习到了很多，巩固了昨天讲座上的内容。

# 2025-08-05

今天阅读了手册的 【行业全览】和【工作方式】。在远程协作习惯这节收获颇多。
在 https://cloud.google.com/application/web3/faucet/ethereum/sepolia 上领取了测试币，部署了safe account，向同学转账，[userOperation hash]: 0x10136f27b999df74536d31e55cf9860a09e8dff9108c7fc1542185d55b8d365a。

# 2025-08-04

今天看了文档的第一第二篇。

## 比特币为什么不会通胀？

核心原因是它的**总量被死死固定了，而且 “印钱” 速度越来越慢，绝不会多印**。

1. **总量有限，就那么多**比特币的白皮书（类似设计说明书）规定：**总共只能有 2100 万个比特币**，多一个都不会有。就像世界上黄金总量有限，挖完就没了，比特币 “挖” 到 2100 万个后，再也不会新增，所以不会像纸币那样被无限印刷。
2. **“印钱” 速度固定且递减，不会突然变多**比特币是通过 “挖矿” 产生的，但产量不是随便定的：
    - 刚开始，每 10 分钟能挖出 50 个比特币；
    - 每过 4 年，产量就会减半（比如从 50 个减到 25 个，再减到 12.5 个……）；
    - 按照这个规则，大约到 2140 年，2100 万个比特币就会被全挖出来，之后再也没有新的比特币产生。
    
    这种 “固定且递减” 的产出规则，保证了比特币不会像法币（比如人民币、美元）那样，因为央行随意印钱而导致总量暴增，自然就不会出现通胀。
    

## **2140 年后的比特币会怎样？**

2140 年并非比特币的 “终点”，而是其进化的新起点：

### **1. 完全依赖手续费的经济模型**

当最后一枚比特币被挖出后，矿工收入将**100% 来自交易手续费**。此时，手续费的定价机制将更加市场化 —— 用户为了加速交易确认，会主动支付更高费用，而矿工则优先打包高手续费交易。这种 “按需付费” 模式在互联网服务（如快递加急、云存储）中已被验证可行。

### **2. 网络安全性的质变**

随着比特币价值持续增长，攻击网络的成本将呈指数级上升。例如，2025 年攻击比特币网络需投入数百亿美元算力，而到 2140 年，这一成本可能达到天文数字。同时，**量子计算威胁**（如 Shor 算法破解椭圆曲线加密）也会被抗量子加密技术（如 BIP-360）逐步化解，确保网络长期安全。

### **3. 从货币到基础设施的进化**

比特币可能从单一货币形态演变为**全球金融基础设施**。例如，闪电网络已实现秒级跨境支付，而 Stacks 等侧链技术支持智能合约开发。这些创新将拓展比特币的应用场景，吸引更多企业和个人使用，进而增加链上交易需求，形成 “使用量增加→手续费增长→矿工积极性提升” 的良性循环。

## **RDF：给数据关系 “搭骨架”**

RDF（资源描述框架）的核心是用一种**简单统一的格式**描述 “谁和谁有什么关系”，就像给数据的关系画 “主谓宾” 句子。

它的基本结构是 “三元组”：**主体（谁）+ 谓词（什么关系）+ 客体（和谁）**。比如：

- 主体：苹果
- 谓词：属于
- 客体：水果

用这种结构，不管是中文、英文还是其他数据，都能统一成 “骨架”。比如 “Apple is a fruit”，在 RDF 里就是：主体：Apple谓词：is a客体：fruit

这样一来，电脑至少能明确：“苹果” 和 “水果” 有 “属于” 关系，“Apple” 和 “fruit” 有 “is a” 关系 —— 先把关系结构化，方便后续统一理解。

## **OWL：给数据关系 “定规则”**

光有骨架还不够。比如电脑知道 “苹果属于水果”“香蕉属于水果”，但它不知道 “水果都能吃”—— 这时候就需要 OWL（Web 本体语言）来补全 “逻辑规则”。

OWL 相当于给数据关系建一个 “知识框架”，定义更复杂的分类、属性和推理规则。比如：

1. **定义分类**：“水果是植物的果实，且可以直接食用”；“苹果、香蕉都是水果的子类”。
2. **定义属性**：“能吃” 是 “水果” 的必备属性（所有水果都能吃）。
3. **定义推理规则**：如果 “X 属于水果”，那么 “X 能吃”。

有了 OWL 的规则，电脑就能像人一样 “推理” 了：

- 已知 “苹果属于水果”（RDF 描述的关系），
- 加上 OWL 规则 “水果都能吃”，
- 电脑就能自动得出 “苹果能吃”—— 这就是智能理解的过程。

了解了pow和pos的区别。pos确实环保许多，对象参与的普通人更友好……


# 2025.07.30


<!-- Content_END -->
