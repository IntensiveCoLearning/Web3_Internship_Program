---
timezone: UTC+1
---

# Nascar

**GitHub ID:** Nascar131

**Telegram:** @NascarLL

## Self-introduction

hello，我是Nascar，来自ZUEL，想要学习web3技术以及运营 + 意向是研究岗+ mbti 不到+ 最近在看FHE和wordcoin

## Notes

<!-- Content_START -->
# 2025-08-21

Fund tokenization

一、关于冲突问题：Back-office 与 Management Fee 的利益冲突、收费标准改革

背景问题：

在传统基金结构中，管理费由资产管理人设定，而基金的 back-office 服务提供商（如 fund administrator）通常按照资产规模（AUM）或交易数量收取固定费用。

当基金进入 tokenization 和链上自动化的阶段，fund admin 有能力实现更多自动化（如实时估值、自动结算、智能合约执行），但费用体系仍旧是“按AUM或传统服务模块计费”的结构，可能带来以下潜在冲突：

具体冲突点：

成本下降 vs 收费不变/上升：

自动化降低了服务成本，但如果收费结构不调整，投资者反而在为“更便宜的服务”支付不合理费用。

利益冲突：

如果 fund admin 同时参与管理费收取、transfer agent、valuation，甚至平台架构部署，可能会在估值和业绩计提方面与投资人产生“判断利益冲突”。

缺乏透明度：

在多方平台、不同角色（平台方、管理人、基金托管人）重叠时，谁负责 fee disclosure？如何进行 fee audit？这对链上标准化提出更高要求。

改革方向：

向 token-as-a-service 的分层收费结构演进：如按功能计费、按链上调用次数计费、或按合规审查次数计费。

引入第三方“fee auditor”智能合约：自动追踪管理费和 admin fee 的设定和支付路径。

监管推动 fee breakdown 上链披露（如 MiCA 框架下未来的发展）。

二、美国的可比公司（US comps）

虽然目前大多数 tokenization infra 提供商仍在早期，但在美国市场，以下几类公司可以作为可比：

Securitize：

主攻合规 token 发行与交易平台，是 SEC 注册的 transfer agent。

类似 Tokeny 提供一体化解决方案，包括 KYC/AML、cap table 管理、token 发行。

商业模式类似 token-as-a-service。

Figure：

创始人来自 SoFi，专注于在 Provenance 区块链上实现贷款资产和基金资产的 tokenization。

聚焦于 credit fund 的上链流通，和传统基金有部分不同。

tZERO：

Overstock 支持的合规数字资产交易平台，曾试图将股票和债券等传统证券 token 化。

商业模式与 Tokeny 略有不同，更偏向交易场所。

Prometheum（较具争议）：

SEC 批准的数字资产 broker-dealer，被视为“监管友好”选手。

主攻链上证券交易平台，其基础设施部分略少，但方向一致。

小结对比：

公司	国家	聚焦方向	是否聚焦基金	是否拥有标准
Tokeny	欧盟	基金资产token化	是	是（ERC-3643）
Securitize	美国	证券发行、平台	是	是（DS Protocol）
Figure	美国	信贷资产、平台	偏少	自有区块链
tZERO	美国	交易所平台	偏少	无明显标准

未来展望：

欧洲在 tokenized fund 合规方面走得更前，尤其卢森堡和法国。

美国更偏重合规证券（STO）的 token 化。

Tokeny 通过标准（如 ERC-3643）和深耕基金行业，有望在 this niche market 形成明显领先优势。

# 2025-08-19

今日看了下几个Crypto

Solana：高性能公链，费用低，牺牲一定去中心化稳定性，适用于Payfi，gamefi     POH+POS
SUI: 高性能公链，TPS高，费用极低，牺牲一定稳定性，灵活性，适用于NFT,Gamefi等需要高频交易场景
XRP: 适用于跨境支付场景,中心化


Farcaster
去中心化数据存储

Zapper去中心化钱包+社交协议

# 2025-08-17

ERC-3643 标准本身并不强制要求身份信息保存在链上，它采用的是一种“链下身份验证 + 链上权限控制”的混合模型（off-chain identity verification + on-chain access control），从而在保护隐私的同时实现合规性。

下面是具体如何实现投资者身份验证、安全性和隐私保护的机制：

🔐 核心机制：身份验证与权限管理分离

身份信息保存在链下（off-chain）

投资者的真实身份（如姓名、护照、KYC 文件）不会直接存储在区块链上。

身份验证由一个或多个「身份验证机构」（Identity Providers 或叫 Trusted Claim Issuers）完成。

这些机构对投资者进行 KYC/AML 审查，生成一个“权限证明”（claim），例如：“Haipeng 先生是一名合格投资者”。

权限令牌（claim）在链上表现为访问控制凭证

ERC-3643 合约将这些链下的验证结果映射为链上的「访问控制」，即“这个地址是否被允许购买/赎回某个代币”。

通常通过 ERC-734（身份管理）和 ERC-735（声明管理）实现这部分逻辑。

🛡️ 安全性与隐私如何保障？

身份信息仅由受信任的机构保存，采用加密存储，并遵守 GDPR/AML 等法规；

用户的钱包地址和链上操作不会泄露真实身份，除非在司法介入或监管审查中解密；

如果身份提供者被撤销授权或身份过期，链上权限会被自动撤销；

多方验证支持：“一个用户需要多个机构共同确认身份”可用于高安全场景；

支持零知识证明（ZKP）扩展：可以通过 zk 技术只验证投资者“符合要求”而无需披露身份详情（Tokeny 已探索此方向）。

🌍 类比理解

ERC-3643 的身份验证流程，就像登机前通过海关：

海关（身份机构）验证你是合法旅客（合格投资者）；

登机系统（智能合约）只检查你是否持有登机许可（权限证明）；

它不会关心你的护照号码（隐私未暴露）。

# 2025-08-16

https://blog.wssh.trade/posts/uniswap-math/

一、Uniswap 的核心机制：恒定乘积公式
公式定义：Uniswap 使用恒定乘积做市商模型（Constant Product Market Maker, CPMM），核心公式为：

xy=k
其中：

𝑥
：池中代币 A 的数量

𝑦
：池中代币 B 的数量

𝑘
：常数，表示池子的总流动性不变

含义：任何交易都必须保持 
𝑘
 不变，因此买入一种代币会导致另一种代币数量减少。

🔄 二、交易与滑点计算
交易过程：

用户输入代币 A，系统自动计算可获得的代币 B 数量。

由于 K
 恒定，代币 B 的价格会随着交易而变化。

滑点（Slippage）：

滑点是由于交易规模影响价格而产生的差异。

交易越大，滑点越明显，因为价格是非线性变化的。

📐 三、价格推导与公式
即时价格计算：

𝑃=dy/dx
实际上，Uniswap 的价格是通过池中两种代币的比例计算的：

𝑃=y/x
交易后价格变化：

假设用户输入 
Δ
𝑥
，则新的代币数量为：

𝑥
′
=
𝑥
+
Δ
𝑥
,
𝑦
′
=
𝑘
𝑥
′
用户获得的代币 B 数量为：

Δ
𝑦
=
𝑦
−
𝑦
′
💰 四、手续费机制
手续费比例：通常为 0.3%

作用：

手续费不会直接进入交易池，而是分配给流动性提供者（LP）。

实际交易公式需考虑手续费影响，导致实际交换比例略低于理论值。

🧮 五、流动性提供者（LP）的份额计算
初始加入：

LP 按照当前价格比例提供两种代币。

获得的 LP Token 表示其在池中的份额。

退出时的计算：

根据 LP Token 占总量的比例，返还对应数量的两种代币。

📊 六、价格操控与套利
价格操控风险：

由于价格由池中代币比例决定，可能被大额交易操控。

套利机制：

当 Uniswap 价格与外部市场价格不一致时，套利者会进行交易以恢复平衡。

这种机制有助于维持 Uniswap 的价格接近市场价。

📎 七、总结与思考
Uniswap 的数学模型简洁但强大，依赖于恒定乘积公式维持市场流动性。

价格机制是自动调整的，但也容易受到大额交易影响。

手续费和 LP 机制确保了参与者的激励和系统的稳定性。

# 2025-08-15

以太坊
能运行智能合约的区块链平台
抗审查的去中心化网络
完整经济系统

质押以太币就要作为validator
签名投票的方式参与共识

执行层EL,共识层CL，验证层

# 2025-08-14

今天研究了下U卡平台，Fiat24
大部分u卡发行商 都是二级三级发卡商，吃不到万事达visa 的返现，
Q某某:
所以能赚钱东西只有充值费

Q某某:
但是充值费只要有个平台免费 其他平台就gg了

Q某某:
所以我认为u卡 只能作为敲门砖



和Q老师交流了web3余额宝产品
类似aave mropho 这类的协议
去中心化要解决合规和合约接口问题

# 2025-08-12

roll up

stage0 中心化 
stage1 limitied training wheel security council
stage2 智能合约

withdraw一小时之内，zk
l2继承l1安全性


智能合约
相同输入，相同结果，代码不会修改
合约升级:数据访问调整
数据永久存储最贵 gas优化(安全)

eip fun，以太坊改进计划查询
openzeppelin
ai测安全性，审计

https://github.com/crazyyuan/defi-fixed-yield-course/blob/main/resource/SolidityInOnePicture.png

https://github.com/crazyyuan/defi-fixed-yield-course/tree/main/docs/lessons

https://github.com/crazyyuan/defi-fixed-yield-course/blob/main/docs/lessons/03-vault-contract.md

私钥部署不安全
remixai

Foundry会好点  Hardhat需要会node


运营
galax 银行上面发布任务
monad
moderator社区答疑

ethresear.ch



defi
去中心化+可组合+无许可+可验证

uniswap 
v3提供流动性，v4hook定制化改造合约
susiswap吸血鬼攻击
治理代币协议手段

makerdao 超额抵押
质押协议+借贷协议
dai去中心化稳定币，超额抵押150%
ust算法调控，无抵押(信心崩溃，抛售，脱锚)
去中心化不等于无管理团队

永远搞清楚谁提供收益

web3.career

人民币增速 08-25 12.9%，usd 7.5%
eth会通缩

aave 用户参与制定安全协议

dex交易量最大 uniswap
defi aave tvpl最多

小交易所做市商少

nft银行，海外银行卡上链 fiat24

dao去中心化治理，提案，投票
多签国库

竞品doc文档，不清晰就是垃圾公司

# 2025-08-10

复看web3故事会

不可能三角：安全心，去中性化，高性能
POW, POS, DPOS
资金流动透明


算法稳定币
市场调节机制，大于1.05所有人发币 AMPL
Luna


未来不是零和博弈

Polymarket,预测实验(治理)

区块链游戏，playtoearn

Gamefi2.0，金融属性太强，游戏要好玩，产品第一性

Solana扶持开发者

POW矿机证明，POS股权证明（ETH）

Fantastor（Farcaster）

DefiLLama


Layer2把layer1打包，Optisim机制

# 2025-08-09

学习同学优秀笔记

了解FHE，全加密，看ZAMA

# 2025-08-07

法律知识
高风险：无目的性募集资金，不向特定客户募集

资金通过无法监管的方式汇集

排除中国大陆用户，AML,KYC

IP封锁，需要当地护照

回避多级返佣和团队奖励

税务部门不对U所得征税，不想承认法律意义

# 2025-08-06

实用工具：Rootdata， Scansniffer

复习Web3安全分享，恶意RPC 用chainlist

提前预习实习资料技术部分，了解智能合约语言Solidity

研究LXDAO，学习邪恶收入理论

# 2025-08-05

学习web3安全常识，测试接受发送Sepolia测试币

大额 100w+

学习了web3行业赛道

收获工具
Delphi Digital - 加密市场研究

# 2025-08-04

web3最新动态 融资：Subzero labs, GAIB, Billions, Stable, 圆币科技

以太坊十周年 defense+offense 
L2饮鸩止渴，减缓Defi离开（Gas贵）--EVM

Bitcoin交易取决于打包区块的速度 10分钟

# 2025.07.30


<!-- Content_END -->
