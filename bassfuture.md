---
timezone: UTC+8
---

# futurebass

**GitHub ID:** bassfuture

**Telegram:** @futurebassqn

## Self-introduction

24年6月加入lxdao，参加过ai黑客松 目前web2运维 move开发

## Notes

<!-- Content_START -->

# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
# Uniswap V1

## 交换 vs 转账

`ethToTokenSwap()`、`tokenToEthSwap()` 和 `tokenToTokenSwap()` 函数将购买的代币返回到买家地址。

`ethToTokenTransfer()`、`tokenToEthTransfer()` 和 `tokenToTokenTransfer()` 函数允许买家进行交易，然后立即将购买的代币转移到接收地址。

提供流动性，添加流动性需要将等值的 ETH 和 ERC20 代币存入 ERC20 代币的关联交易合约中。

第一个加入池的流动性提供者通过存入他们认为等值的 ETH 和 ERC20 代币来设定初始汇率。如果这个比率关闭，套利交易者将以牺牲初始流动性提供者的利益为代价使价格达到平衡。

所有未来的流动性提供者都使用存款时的汇率存入 ETH 和 ERC20。如果汇率不好，就有一个有利可图的套利机会来纠正价格。

## **流动性代币**

流动性代币的铸造是为了跟踪每个流动性提供者贡献的总储备的相对比例。它们具有高度可分割性，可以随时销毁，以将市场流动性的比例份额返还给提供者。

流动性提供者调用 `addLiquidity()` 函数存入储备金并铸造新的流动性代币：

```
@public
@payable
def addLiquidity():
    token_amount: uint256 = msg.value * token_pool / eth_pool 
    liquidity_minted: uint256 = msg.value * total_liquidity / eth_pool
        
        
    eth_added: uint256 = msg.value
    shares_minted: uint256 = (eth_added * self.total_shares) / self.eth_pool
    tokens_added: uint256 = (shares_minted * self.token_pool) / self.total_shares)
    self.shares[msg.sender] = self.shares[msg.sender] + shares_minted
    self.total_shares = self.total_shares + shares_minted
    self.eth_pool = self.eth_pool + eth_added
    self.token_pool = self.token_pool + tokens_added
    self.token.transferFrom(msg.sender, self, tokens_added)
```

铸造的流动性代币数量由发送到该函数的 ETH 数量决定。可以使用以下公式计算：

 将 ETH 存入储备金还需要存入等值的 ERC20 代币。这是用以下等式计算的：

## 移除流动性

流动性提供者可以随时销毁其流动性代币，以从池中提取其相应份额的 ETH 和 ERC20 代币。

ETH和ERC20代币按当前汇率（准备金率）提取，而不是按其原始投资的比例提取。这意味着市场波动和套利可能会损失一些价值。

交易期间收取的费用将添加到总流动性池中，而无需铸造新的流动性代币。因此，`ethWithdrawn`  和 `tokensWithdrawn` 包含自首次添加流动性以来收取的所有费用的一定比例份额。

## 流动性代币

Uniswap 流动性代币代表流动性提供者对 ETH-ERC20 货币对的贡献。它们本身就是 ERC20 代币，并包含 [EIP-20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md) 的完整实现。

这使得流动性提供者可以出售他们的流动性代币或在账户之间转移它们，而无需从池中移除流动性。流动性代币专属于单个的ETH⇄ERC20交易所。该项目没有一个统一的ERC20代币。
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
# Uniswap 白皮书

`Uniswap` 是一种基于以太坊协议的去中心化虚拟货币的交易平台。

普通的交易所需要交易对手，做市商通过设置买入价和卖出价，这些设置的价格形成了限价订单。

`Uniswap` 是基于算法的自动做市服务，它将每个人的流动性集中到一起，然后根据算法进行做市，让算法向用户提供兑换代币的报价。

`Uniswap` 采用的自动做市模型是“恒定乘积做市商模型”，其公式是 `X*Y=K`，其中 `X` 和 `Y` 是两种 `ERC20` 代币的数量，K是常数。

自动做市需要一个流动池，这个流动池的提供者是流动性提供商。

流动性提供者向 `Uniswap` 池中添加流动性时，它需要提供与当前市场类似的兑换比率。

流动性提供商的收入来自于交易费用，这些交易费用会按比例分配给流动性的提供商。

Uniswap  目前经历了 4 个版本，各个版本均发布了白皮书

Uniswap 是一个基于以太坊的自动化代币交换的协议。它的设计目标是易用性、gas 高效性、抗审查性和零手续费抽成。它对交易者非常有用，并且作为其他需要保证链上流动性的智能合约的组成部分，功能特别好。

大多数交易所都会维护一个订单簿，并促进买家和卖家之间的匹配。 Uniswap 智能合约持有各种代币的流动性储备，交易直接针对这些储备执行。价格是使用恒定乘积 (x\*y=k) 做市商机制自动设定的，这使总体储备保持相对平衡。储备金集中在流动性提供者网络之间，流动性提供者向系统提供代币，以换取一定比例的交易费用。

Uniswap 的一个重要特征是利用工厂/注册合约，为每个 ERC20 代币部署单独的交换合约。这些交易合约同时持有 ETH 和关联的 ERC20 代币构成的准备金。这可以实现两个基于相关供应的交易对之间的交易。交易合约通过注册表串联在一起，从而可以使用 ETH 作为媒介，实现 ERC20 代币之间的互相交易。

Gas基准测试：

Uniswap 由于其简约的设计而非常高效。对于 ETH 到 ERC20 的交易，它使用的 Gas 几乎比 Bancor 少 10 倍。

它可以比 0x 更高效地执行 ERC20 到 ERC20 的交易，并且与 EtherDelta 和 IDEX 等链上订单簿交易所相比，可以显著减少 Gas 费用。

创建合约交易：`uniswap_factory.vy` 是一个智能合约，既充当 Uniswap 交易所的工厂又充当注册表。公共函数 `createExchange()` 允许以太坊用户为任何还没有交换合约的 ERC20 部署交换合约。

```
exchangeTemplate: public(address)
token_to_exchange: address[address]
exchange_to_token: address[address]
    
@public
def __init__(template: address):
    self.exchangeTemplate = template

@public
def createExchange(token: address) -> address:
    assert self.token_to_exchange[token] == ZERO_ADDRESS
    new_exchange: address = create_with_code_of(self.exchangeTemplate)
    self.token_to_exchange[token] = new_exchange
    self.exchange_to_token[new_exchange] = token
    return new_exchange
```

所有代币及其相关交换合约的记录都存储在工厂中。对于所有代币或交换合约地址，函数 `getExchange()` 和 `getToken()` 可用于查找另一个。

```
@public
@constant
def getExchange(token: address) -> address:
    return self.token_to_exchange[token]

@public
@constant
def getToken(exchange: address) -> address:
    return self.exchange_to_token[exchange]
```

除了强制执行每个代币一次交易的限制之外，工厂合约在启动交换合约时不会对代币进行任何检查。用户和前端应该只与他们信任的代币交易所进行交互。

# ETH ⇄ ERC20 Trades

每个交易所合约（uniswap\_exchange.vy）都与一个单一的ERC20代币相关联，并持有ETH和该代币的流动性池。ETH与ERC20之间的汇率基于它们在合约内的流动性池的相对大小。这是通过维持eth\_pool \* token\_pool = 恒量这一关系来实现的。这个恒量在交易期间保持不变，只有在向市场添加或从市场中移除流动性时才会改变。

以下展示了ethToTokenSwap()函数的简化版本，该函数用于将ETH转换为ERC20代币：

```
eth_pool: uint256         
token_pool: uint256       
token: address(ERC20) 

@public
@payable
def ethToTokenSwap():
    fee: uint256 = msg.value / 500 
    invariant: uint256 = self.eth_pool * self.token_pool
    new_eth_pool: uint256 = self.eth_pool + msg.value
    new_token_pool: uint256 = invariant / (new_eth_pool - fee)
    tokens_out: uint256 = self.token_pool - new_token_pool
    self.eth_pool = new_eth_pool
    self.token_pool = new_token_pool
    self.token.transfer(msg.sender, tokens_out)
```

注意：对于燃气效率，eth\_pool和token\_pool不是存储变量。它们是通过self.balance和通过对外部调用self.token.balanceOf(self)来找到的。

当ETH发送到函数eth\_pool时，eth\_pool会增加。为了保持关系eth\_pool \* token\_pool = 恒量，token池会按比例减少。token\_pool减少的量是购买代币的数量。这种储备率的变化会改变ETH到ERC20的汇率，从而激励相反方向的交易。

用tokenToEthSwap()函数进行代币兑换ETH：

```
@public
def tokenToEthSwap(tokens_in: uint256):
    fee: uint256 = tokens_in / 500
    invariant: uint256 = self.eth_pool * self.token_pool
    new_token_pool: uint256 = self.token_pool + tokens_in
    new_eth_pool: uint256 = self.invariant / (new_token_pool - fee)
    eth_out: uint256 = self.eth_pool - new_eth_pool
    self.eth_pool = new_eth_pool
    self.token_pool = new_token_pool
    self.token.transferFrom(msg.sender, self, tokens_out)
    send(msg.sender, eth_out)
```
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
**调研 EVM 兼容链的特色**

Evm兼容链是LAyer1 公链与以太坊不同，不是layer2 侧链 信标链不同。evm兼容链采用不同的独特共识算法 及生态治理代币

，兼容部分eth生态的dapp 钱包 defi协议。

信标链是以太坊在从工作量证明（PoW）向权益证明（PoS）过渡过程中推出的关键升级，主要作为共识层协调验证者，支持PoS机制并管理验证者网络。信标链不直接处理交易或智能合约，而是协调和监督分片链，提高以太坊未来的可扩展性和安全性，是以太坊2.0的重要组成部分。

侧链是独立的区块链，与以太坊主链并行运行，有自己的共识机制和安全性，不依赖主链的安全。侧链通过双向挂钩或桥与主链资产互通，主要目的是减轻主链负载，提高交易速度和扩展性。常见的侧链包括Polygon PoS等。

Layer2是位于以太坊主链之上的扩展解决方案，通过将大量交易在链下处理后再提交到主链，继承主链的安全性，提升交易吞吐量并降低交易费用。主要的Layer2技术包括Rollups、通道、Plasma等。

**cex的公链：**BSC币安智能链 x layer 是中心化交易所的链 为了结算等业务节省手续费 服务费存在。

**defi公链：**HyoerLiquid 第一个实现cex体验的defi基础设施，完全在链上的开放式金融系统，订单 取消 交易 清算都在链上透明进行。

还在测试阶段的evm链：

Monad：主打高tps evm兼容链 号称和solana的低gas 和交易处理速度。测试网活跃。
<!-- DAILY_CHECKIN_2025-08-23_END -->


# 2025-08-22
<!-- DAILY_CHECKIN_2025-08-22_START -->
在以太坊区块链中存在两种类型的账户：外部账户 和 合约账户，它们在以太坊上有着不同的特性和用途。
外部账户，英文为 Externally Owned Account，缩写为 EOA。
外部账户，也就是我们平常使用的用户账户，用于存储以太币 ETH 。这些账户可以向其它账户发送以太币，或者从其它账户接收以太币。
我们在钱包里管理的账户，通常就是外部账户。比如，在小狐狸钱包 Metamask 里添加或者生成的 Account 就是外部账户。
外部账户会有一个与之相关的以太坊地址，这个地址是一个以 "0x" 开头，长度为20字节的十六进制数，比如：0x876......。
外部账户都有一个对应的私钥，只有持有私钥的人才能对交易进行签名，所以，外部账户非常适用于资金管理。
我们常说的以太坊账户，在不特别指明的情况下，一般是指外部账户。
合约账户，英文为 Contract Account，缩写为 CA。
我们在以太坊区块链上部署一个智能合约后，都会产生一个对应的合约地址，这个地址称为合约账户。合约账户主要用于托管智能合约，它里面包含着智能合约的二进制代码和状态信息。
合约账户地址的格式与外部账相同：以 "0x" 开头，长度为20字节的十六进制数。
合约账户没有私钥，只能由智能合约中的代码逻辑进行控制。
它在一定条件下，也可以用来存储以太币 ETH
<!-- DAILY_CHECKIN_2025-08-22_END -->

# 2025-08-20

虚拟机 EVM
以太坊虚拟机，英文缩写为 EVM。它是以太坊上运行智能合约的环境，换句话说，智能合约是在以太坊虚拟机 EVM 中执行的。EVM 是一种完全隔离的运行时环境，意味着运行在 EVM 上的代码无法直接访问网络、文件系统或其他外部系统。这种设计确保了在全球范围内部署的以太坊节点能够在安全、确定性的环境中执行代码，保证了网络的去中心化和抗审查特性。在 EVM 上最终执行的智能合约代码，并不是文本形式的 Solidity 语言的源代码，而是一种二进制代码，称为 字节码。我们使用 Solidity 语言来编写的智能合约，编译成以太坊虚拟机支持的 字节码，就可以在 EVM 中执行了。

功能：智能合约部署与执行；EVM 允许开发者部署智能合约到以太坊区块链上，并执行这些合约中定义的函数和操作。这是构建去中心化应用的基础。
状态管理：EVM 通过以太坊的区块链管理和维护每个智能合约的状态，包括合约的余额、存储和执行结果。
 以太币转移：智能合约可以接收、存储和发送 ETH，EVM 处理这些操作，确保资金的安全转移。
 消息调用：智能合约可以通过 EVM 发送消息给其他合约，调用其他合约的函数，实现合约间的交互和数据共享。
加密操作：EVM 提供了一组加密操作的原生支持，允许智能合约执行如哈希计算、签名验证等安全敏感的操作。通过这些特点和功能，EVM 为以太坊网络上的去中心化应用和智能合约提供了一个强大、安全和灵活的运行环境。

# 2025-08-19

去中心化应用，英文为  Decentralized Application，简称 DApp，它是一种运行在区块链网络上的应用程序，具有去中心化的特性。

DApp 利用智能合约自动执行程序逻辑，并通过区块链的技术确保数据存储的安全与透明性。

DApp 是为了摆脱传统中心化服务的控制和限制而设计的，它提供了一个更开放、可信和安全的用户体验。

DApp 可以使用任何语言编写前端代码和用户界面，前端调用后端实现实现功能。

如果一个普通互联网 App 可以表示为：App = 前端 + 后端服务器；

那么一个 DApp 可以表示为: DApp = 前端 + 智能合约。

比如：淘宝的网站和手机 App 都是 App 的表现形式，而 uniswap 就是一个不折不扣的 DApp
DApp 与App 的区别
1，去中心化：
DApp：

数据存储：DApp 的数据通常存储在区块链上，由网络上的所有节点共同维护，而不是存储在单一的服务器或数据中心。
运行环境：DApp 的后端代码（智能合约）在去中心化的区块链网络上执行，这使得任何单个点的故障或控制都不会影响到整个应用的运行。
所有权和控制：用户直接与智能合约互动，不通过中介或管理者，用户对自己的数据拥有完全的控制权。
传统App：

数据存储：传统应用通常将数据存储在中心化的服务器上，由单个公司或组织控制。
运行环境：后端代码在单一或集中的服务器上执行，这可能导致点对点的失败或被中心化的控制。
所有权和控制：用户数据的控制权通常归应用的所有者或服务提供商所有，用户必须信任这些中心化的实体来处理其数据。
2.透明度
DApp：因为智能合约的代码通常是公开的，且一旦部署后无法更改，任何人都可以验证程序的功能和数据的处理方式。这提高了应用的透明度和整体的可信度。

传统App：代码和数据处理通常是私有的，用户需要信任应用提供者不会滥用他们的数据或修改程序的运行方式。这种模式有时可能导致透明度和可信度问题。

3.依赖性
DApp：DApps 的设计减少了对外部服务的依赖，它们依赖于区块链和其他去中心化协议来提供服务和功能。

传统App：通常依赖于外部公司或服务来提供数据存储、计算能力和其他功能。

4. 更新和维护
DApp：更新智能合约可以比较复杂，需要部署新的合约并可能需要迁移数据。这通常是一个固定且透明的过程。

传统App：更新和维护由应用的开发者或公司控制，通常可以更灵活和迅速地进行，但用户往往对这个过程缺乏可见性。

# 2025-08-16

共识算法是区块链是主要技术，不同公链共识层网络算法不同；
早期公链都是工作量证明（POW），通过矿工解决复杂数学题获得记账权，安全性高但是消耗大量资源，交易速度慢，早期比特币莱特币都是pow公链。
权益证明（pos）是大量主流公链的共识算法，节点根据持有的加密货币数量和质押时间获得出块资格。节能，效率相较PoW更高，支持节点多。代表公链：以太坊（ETH）、Cardano（ADA）。
委托权益证明（DPoS，Delegated Proof of Stake），机制：通过投票选出少数代表节点进行交易验证，提高效率，中心化程度较高，节点层级明显。代表：TRX
历史证明（Proof of History，PoH），PoH 是 Solana 的独特时间序列共识算法，用于通过加密哈希生成一个可验证的、连续的时间顺序，确保交易被按正确顺序排列。Solana 通过 DPoS 机制选择验证节点（领导节点）来负责生成区块。持有 SOL 代币的用户可以将其代币委托给验证者，影响领导者的选举权重。Tower BFT 是一种基于 PoH 时间序列的高效拜占庭容错算法（BFT），通过利用 PoH 生成的时间序列来达成快速共识，保证网络安全性和去中心化。

# 2025-08-15

预言机是defi的基础设施，为区块链上的应用智能合约提供可靠的真实数据，下面是收集列举部分项目：
Chainlink是预言机的龙头 已发币
RedStone 是跨链数据预言机
TEllor 去中心化预言机协议 
Pyth network  预言机解决方案
Band Network 跨链数据预言机平台
APRO Oracle 去中心化预言机网络

# 2025-08-14

市值排名前20的数字货币分别是BTC\ETH\USDT\BNB\SOL\STETH\XRP\USDC\ADA\DOGE\SHIB\AVAX\DOT\TRX\LINK\UNI\WBTC\MATIC(POLYGON)\TON\BCH.
BTC市值最大，是区块链第一应用。纯粹靠共识获取价值，公认的数字黄金。
ETH是第一个运行智能合约的平台，defi dao ganfi等都依靠它，二层网络分流了部分用户。
USDT是流通最大应用最广的稳定币
BNB是最大中心化交易所币安的平台币
SOL是号称以太坊杀手的高性能链，链上土狗币活跃，mev情况严重。
STETH是质押的eth，1steth=1eth。质押生态代币。
xrp是流行的即时清算代币，老牌的银行项目。
usdc是第二大稳定币，母公司circle已在美上市。
ADA是17年的学术链，最早的pos链。
DOGE SHIB都是meme代币，没有实用价值，靠马斯克喊单带火。
AVAX独创了雪崩协议，通过X链 C链 P链来应对不同场景，实现高tps
DOT波卡链代币，一种异构链NPos共识算法。
TRX波场链代币，孙哥抄袭eth，稳定币流通质押多。
LINK预言机龙头代币。
UNI是uniswap的治理代币，引起defi热潮。eth上最大的dex。
Wbtc是跨链代币，解决了btc与eth之间的交互问题。
Matic现更名POLYGON，以太坊的ly2
Ton由Telegram发起的项目，提出了无限分片的技术，由于主创在法国扣押热度减小
BCH是btc的分叉版本，通过提高比特币区块大小限制提升交易量。

# 2025-08-13

eth再质押项目Eigenlayer
再质押机制（Restaking）:
参与者（包括以太坊验证者和流动性质押资产持有者）将已经质押在以太坊网络的ETH或流动性质押代币（LST，如stETH、rETH等）重新质押到EigenLayer。这使得单一的质押资本能为多个网络和协议提供安全保障，从而提高资本利用效率。
主动验证服务（AVS）和安全共享:
EigenLayer通过再质押的ETH等资产为第三方协议提供安全保障，这些协议称为主动验证服务（AVS），包括跨链桥、排序器、预言机、数据可用性层及Layer 2扩容方案等。通过共享以太坊的安全基础设施，AVS无需自己建立庞大的验证节点网络，大幅降低了启动和运营成本
双重收益模式：
质押者在EigenLayer不仅从以太坊本身获得验证奖励，还能从支持的AVS项目获得额外收入，提供双重收益激励
该项目上线主网

# 2025-08-11

#解析ENA 以太坊再质押协议

USde核心机制：合成美元的稳定与收益平衡

Delta中性对冲：USDe通过Delta中性策略对冲加密货币价格波动。实现与美元的问答挂钩，用户铸造USDe时候需要提供ETH等Crypto作为抵押品，协议同步开始永续合约空头头寸，通过现货与期货动态平衡维持Delta值接近0
收益生成机制：USDe的收益来源与主要包括加密资产质押奖励与衍生品市场的资金费率与基差收益，usde收益代币实现年化18%平局收益率，70%收益分配给用户，30%由协议收回保留用于回购与生态建设。
OES机制：USDe采用场外结算机制，将抵押资产与交易所头寸隔离。避免传统cex用户资产与平台自有资金混合风险，确保抵押品资产始终覆盖USDe发行总量。

# 2025-08-10

周日休息

# 2025-08-08

学习mev 
#了解什么是mev 
mev的危害与治理

# 2025-08-07

#参与法律分享会
小结：技术开发前端风险较低 合约 后端 设计经济模型均有风险
项目团队均需要做KYC amm 
境内不允许 ico
 cex在境内属于非法经营 开设赌场 洗钱 资产外逃 年初某蓝色cex招募校园大使推广业务是高风险行为
工作尽量出海 就近理想工作地点为香港

# 2025-08-06

全程参与直播并且提问互动
提问：xrp为什么项目这么老市值却大

# 2025-08-05

学习web3安全知识 参加zoom会议

# 2025-08-04

完成基础工具安装zoom rabby x ，测试网获取代币 推特关注bruce ethpanda lxado


# 2025.07.29


<!-- Content_END -->
