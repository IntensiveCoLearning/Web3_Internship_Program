---
timezone: UTC+8
---

# David

**GitHub ID:** nghdavid

**Telegram:** @nghdavid

## Self-introduction

我是David, Base 新加坡, 有EVM smart contract開發經驗與auditing經驗。目前正在南洋理工研讀blockchain, 希望能透過這個活動得到實習機會

## Notes

<!-- Content_START -->
# 2025-08-10

# 第3讲(Function)
```
function <function name>(<parameter types>) {internal|external|public|private} [pure|view|payable] [returns (<return types>)]
```

### 函數可見性說明
- public => 內部外部均可見
- private => 只能由合約本身訪問，繼承的合約不能使用
- external => 只能由合約外部訪問
- internal => 只能由合約內部訪問，繼承的合約可以使用

### 函數權限
- payable => 可支付的
- pure => 不能寫入狀態變量，但不能讀取
- view => 不能寫入狀態變量，但能讀取

# 2025-08-09

# 智能合约开发

## Dapp 架构
- 前端（User Interface）: Ether.Js
- 智能合约（Smart Contracts）: Solidity
- 数据检索器（Indexer）: 将Event写入到 PostgreSQL

## Solidity
```
// SPDX-License-Identifier: MIT
```
代表軟體授權協議

```
pragma solidity ^0.8.21;
```
代表版本需 >= 0.8.21 且 < 0.9.0

# 第2讲(Solidity variable type)

1. Value type: Boolean, Integer
2. Reference type: Array, Struct
3. Mappping type: Key-value pair

## Boolean operator
1. ! => not
2. && => and
3. || => or
4. == => equal
5. != => not equal

## Integer
1. int (Including negative number)
2. uint (Only positive number)
3. uint256 (256 bytes positive number) => 0~2^256-1 

Note: uint = uint256

## Address (20 bytes)
1. payable address: 相比address, 多了transfer與send

## String
1. Fixed length string(Value type)
最多32bytes
```
bytes32 public _byte32 = "MiniSolidity";
```
若轉成16進制 => 0x4d696e69536f6c69646974790000000000000000000000000000000000000000

```
bytes1 public _byte = _byte32[0]; 
```
_byte為_byte32的第一個字節: 0x4d

2. Non Fixed length string(Reference type)

# 2025-08-07

# 安全与合规

## 中国法律禁止
- ICO、IEO、IDO
- OTC
- 交易所
  
## 监管框架
- 美国监管框架: Genius Act, Clarity Act
- 欧盟监管框架: MiCA
- 香港监管框架: VASP

## 常见网络安全风险与攻击方式
- 钓鱼攻击（Phishing）
- 恶意软件/木马（Malware & Trojan）
- 社交工程攻击（Social Engineering）
- 供应链/第三方依赖攻击
- 地址污染与扫描木马（Clipper/Scanning Bots）

# 2025-08-06

# 行业赛道全览

## DeFi
- Dex: Uniswap
- Lending: Aave, Compound
- CDP: MakerDao
  
## NFT
- Opensea
- Blur
- MAYC, CryptoPunks

## DAO
- LXDAO

## Meme
- Doge, Pepe, Trump
- Pumpfun, GmGn, Bonk

## AI
- Virtuals

## Modular
- Celestia
- EigenDA
- OP Stack

# 2025-08-05

# Bitcoin
- 歷史上第一個去中心化的貨幣
- 發行量為2100萬顆，永遠固定，不會通脹
- 出塊時間約為10分鐘

## 交易步驟
- 用户发起交易：用户通过钱包应用发起转账、智能合约调用等操作
- 交易广播：交易信息被广播到整个网络中的各个节点
- 节点验证：网络中的矿工节点验证交易的合法性（余额是否足够、签名是否正确等）
- 打包成块：通过共识机制（如工作量证明），矿工将验证过的交易打包成新的区块
- 链接上链：新区块被添加到区块链上，更新全网的账本状态
- 奖励发放：成功打包区块的矿工获得代币奖励和交易手续费

# 公链 vs 私链 vs 联盟链
- 公链: 自由自在
- 私链: 出錯可以回滾，較中心化

# 以太坊概览
- 以太坊的核心创新在于 智能合约（Smart Contracts）
- 智能合约是存储在区块链上的可执行代码
- 能够在满足预设条件时自动执行操作
- ETH為Gas
- The merge後改成Pos

## 以太坊驗證者
- 准入门槛：质押 32 ETH 成为验证者
- 工作方式：系统随机选择验证者来提议和验证区块
- 奖励机制：验证者获得新发行的 ETH + 交易费用
- 惩罚机制：作恶者质押的 ETH 被销毁（Slashing）

## 以太坊生態
- Layer 1（L1）: 
- Layer 2（L2): Optimistic Rollup, ZK Rollup
- Side chain: Polygon

# 2025-08-04

以太坊主网自2015年上线，至2025年已走过十年，其发展兼具里程碑式的技术演进与复杂生态博弈，当前稳居全球区块链与数字资产应用的核心基础设施地位。十年内，以太坊实现了DAO分叉自救、合并升级、推广Rollup和Layer2扩展等关键突破，目前全网Layer2锁仓价值超440亿美元，日生态交易量突破4,000万笔。

**面临的四大核心挑战：**
1. **账户抽象安全**  
   2025年Pectra升级（EIP-7702）实现账户抽象后，普通钱包可临时具备合约功能，大幅改善用户体验（如免Gas费操作、批量交易、社交恢复等）。但信任模型被重构，带来如大规模钓鱼授权和安全漏洞（两周内损失超1.5亿美元）等新风险。技术界正推进冷静期和合约开源披露、机构权限加强等安全标准，但在灵活性与安全性之间仍需权衡碎片化与互操作难题**  
   Arbitrum、Optimism占据主导地位，但流动性与技术标准被分割，不同Rollup之间资产和用户交互体验割裂。Optimism的“超级链”与ZK-Rollup联盟试图实现跨链互通，但技术整合依然缓慢。碎片生态带来流动性重复、提款等待和中心化风控等问题，决定以太坊未来能否承载10亿用户[attached**MEV（最大可提取价值）的公平性与集中风险**  
   PoS以后，大型构建者（如Flashbots）主导了65%的区块构建权，普通用户在三明治攻击等套利中成为最大受损方，交易成本中有15-20%来自MEV。以太坊正推进加密内存池、收益销毁（MEV-Burn）和PBS等方案缓解，但如何平衡网络效率与公平分配仍是难题。

4. **全球监管和金融化冲击**  
   美国、欧盟、中国香港等分化监管政策推动“合规套利”加剧，开发和运营成本上升。以太坊ETF获批后，机构持有比例飙升，价格波动与宏观金融市场高度相关，价值捕获机制由链上生态主导转向ETF资金与宏观利率。如何在合规创新和去中心化理想间找到平衡，成为以太坊第二个十年的核心命题。

**以太坊十年核心数据与地位（截至2025年）：**
- 稳定币供应突破1,000亿美元，机构和传统金融巨头（如摩根大通）深度参与。
- 全网TVL超880亿美元，持续追新高。
- 日活跃主网地址58万个，生态Layer2（如Base、Arbitrum）日活跃用户超250万个。
- 日交易量主网超170万笔，全生态超5亿笔。
- 核心开发者持续领先，历史创新热潮不断，近月单日新合约部署一度破20万。
- 经济安全性创新高，质押ETH总额达1,400亿美元。
- ETH价格2025年5月后强劲反弹，成为市值前列的加密资产。

**本质挑战与未来方向：**
以太坊仍在“去中心化－安全性－可扩展性”不可能三角持续博弈，Layer2生态整合、账户抽象安全、MEV公平机制和全球合规适配成为新十年的核心课题。以太坊的价值和领导力，正体现在推动技术理想与现实需求的动态平衡与持续进化

# 2025.07.30


<!-- Content_END -->
