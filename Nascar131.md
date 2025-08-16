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
