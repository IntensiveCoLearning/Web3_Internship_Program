---
timezone: UTC+8
---

# Zachary

**GitHub ID:** zhenghaoailimiao

**Telegram:** @zachary_hugo

## Self-introduction

是个宅男，标准的程序员，学的java后端。最近刚接触到web3，想了解了解学习学习，希望能度过愉快的实习计划

## Notes

<!-- Content_START -->

# 2025-08-28
<!-- DAILY_CHECKIN_2025-08-28_START -->
今日浏览了一些求职网站，在看一些帖子，知道了找实习或工作的一些坑
<!-- DAILY_CHECKIN_2025-08-28_END -->


# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
今天仔细阅读了「面试准备与行业岗位推荐」的手册内容。

学到了Web3 求职的黄金法则是：**及时、真实、准确**。以后找Web3相关的实习或工作都要先想想这个黄金法则，这样子就不容易被骗踩坑。

还了解了技术向有哪些职位，并找到了适合自己的三个技术向职位是：**智能合约工程师（Smart Contract Engineer）、Dapp 后端开发者（Dapp Backend Developer）、Dapp 前端开发者（Dapp Frontend Developer）**，也准备去找一份实习工作了。
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
今天在公司加班开会讨论业务需求，没有办法按时完成今天的任务，又不想错失打卡，有空的时候会补上去
<!-- DAILY_CHECKIN_2025-08-23_END -->

# 2025-08-22

## Uniswap v3

### 1.xy=k
经典的AMM公式，也是uniswap v1和v2的核心公式，由于这个公式有一个很大的问题就是资金利用率不高，所以诞生了uniswap v3。
### 2.v2 的价格区间
假设有资产 X 和资产 Y，现在存在一个以 X 和 Y 资产组成的交易对池子。当我们要用资产 X 来标价 Y 的时候，***PriceY = x的数量 / y的数量***，这很容易想到。

我们在直角坐标系上，用 x 轴表示资产 X 的数量，y 轴表示资产 Y 的数量，那么将上面的式子变换可以得到 ***y = p * x***，为了简化，我们用 **p** 来表示 priceY。

于是我们可以在坐标系上添加一条直线，其斜率的倒数 *x/y*，就是价格 **p**。当价格变化的时候，就是这条直线的斜率在变化。

流动性就可以用一个矩形的面积来表示。因为 **k** 的实际意义就是用来衡量一个池子的流动性数量，而 ***x * y=k***，所以池子中 ***资产X的数量 * 资产Y的数量 = 流动性数量***。
![price changing](D:\images\p1.webp)

在上图中，资产 Y 的价格在下跌（X 的价格在上涨），绿色区域的面积是流动性数量 k，需要保持恒定不变，于是随着价格变化，矩形右上方端点（图中的红点）就能划出一条我们熟悉的双曲线。随着价格下跌，红点逐渐上移，矩形的高度(y)不断变大，而宽度(x)不断变小。当价格趋近于 0 时，红线会无限接近和 y 轴重叠，但却不可能真正重叠，因为绿色的双曲线是不会和坐标轴相交的。所以我们的池子做市的价格下限是 0，同理，价格上限是无穷大（因为双曲线和 x 轴也不会相交）。

所以在 ***x * y=k*** 的情况下，做市的价格区间是 **(0, ∞)**。
### 3.资金利用率
价格区间(0, ∞)看起来很理想，我们的资金在任何时候（任意价格点）都能为我们赚取手续费。

但我们忽略了另一个影响收益的重要因素，那就是资金的利用率。当一个用户来用我们的池子做交易时，其交易的量相比我们的流动性来说是很小的。

假设现在池子内资产 X 和 Y 都有 8 个，价格 p 为 1。

现在有一笔订单，用 1 个 X 来换取 1 个 Y，我们先不考虑滑点和手续费的影响，这一笔交易为我们带来的手续费收益是 ***fee = 1 * 0.3%***，实际参与赚取手续费的流动性就是输出的 1 个 y，这相比于总流动性是很小的，在这一笔交易中，资金利用率是大约是 ***1 / 8***。也就是说，我们只需要极少一部分流动性就能承载这一笔交易，而大部分流动性在交易过程中只是躺在那做收益的分母而已。
![liquidity rate](D:\images\p2.webp)

回到我们的坐标系上，当用户用 X 换取 Y 的时候，价格会从低点涨到高点，红点从 ***p_lower*** 移动到 ***p_upper*** 的过程中（X 的价格），实际参与交易的流动性仅仅是橙色的矩形区域。这里为了便于查看，夸大了价格的变动区间，实际交易过程中，价格变动不会这么大，所以橙色区域是极小的。

所以提高利用率的关键是既要移除那些躺在那不干活的流动性（绿色区域），又要保证这个函数模型不变。于是我们将其换成了虚拟的流动性，即 ***x_virtual*** 和 ***y_virtual***，而添加流动性时，只需要注入橙色区域的流动性即可。于是公式变成了如下模样：
### ***(x+xᵥᵢᵣₜᵤₐₗ)(y+yᵥᵢᵣₜᵤₐₗ)=k***
![real liquidity](D:\images\p3.webp)

整个图形向左下方平移了，因为价格是直线的斜率，所以平移对于实际交易是没有影响的。**前提是价格没有超出限定的区间**。

正如上图所示，如果价格上涨，超出了 **p_upper**，代表价格的点会来到 y 轴左侧，即这个时候 x 的数量为负数。这是不可能存在的情况，因为在实际情况中，为负的资产数量是没有意义的。所以当我们给池子增加了虚拟流动性之后，我们向其中注入的真实流动性，都只能在特定的价格区间做市，一旦价格超过区间，流动性就枯竭了，或者说，池中其中一种资产的数量变为 0，无法再为市场提供流动性，除非价格再次回到区间内。关于池中资产由 X 全部转换成 Y，这一特性后面我们还会展开讨论，这里先回到资金利用率的问题。

如果你的线性代数不好，可能会被平移的方向绕晕，那么这里我有一个不太恰当的比喻帮助你理解虚拟流动性。回到图 02，你可以想象虚拟流动性就是在 x 轴和 y 轴方向上，给真实的流动性（橙色区域）的两个增高垫，也就是绿色区域补足的部分，帮助我们可以用较少的资金达到原先的做市效果。

事实上，对于使用交易功能的用户来说，v3 的模型和 v2 的模型没有区别，整个价格还是在 ***xy=k*** 的曲线上运行（把***(x + x_virtual)*** 整体当成 x， y 同理）。不论 V2 还是 V3，交易过程中，实际使用的流动性都只是橙色区域，而更多的绿色区域是虚拟的还是真实的流动性，对于他们来说没有区别，观察到的仍是图 02 的模型。

而提供流动性的用户看到的样子，是图 03，在设定的价格区间内，我们解决了资金利用率的问题。
### 4.V3 的核心公式
V3 的核心公式，其实刚才已经出现了，我们只需要将虚拟流动性具体算出来就好了。为了方便后面的计算，我们将代表流动性的 **k** 换成 ***𝐿^2*** 即 L 的平方。

现在我们回到图 02，可以看出 **x_virtual** 的长度实际是 **p_upper** 点的 x，而 **y_virtual** 的长度实际上是 **p_lower** 点的 y。所以在确定价格上下区间的情况下，我们可以利用价格的公式 ***y = p * x*** 和 ***xy=L^2*** 来换算虚拟流动性的长度。(这里为了变量名称统一，将价格点统一称为 lower 和 upper，白皮书中是 a 和 b，不影响结果。)

将前者代入后者，得到 ***p * x^2 = L^2***, 进而就能得到如何用 **L** 和 根号价格 **√p** 表达 **x** 和 **y**：
### ***x=L/√p***&emsp;***y=L∗√p***
于是两个虚拟流动性就可以写为：
### ***xᵥᵢᵣₜᵤₐₗ = L/√pᵤₚₚₑᵣ***&emsp;***yᵥᵢᵣₜᵤₐₗ = L/√pᵤₚₚₑᵣ***
核心公式就呼之欲出了：
### ***(x+L/√pᵤₚₚₑᵣ)∗(y+L∗√√pₗₒ𝚠ₑᵣ)=L^2
可以看到，公式中是将 **p_upper** 和 **p_lower** 作为已知的变量，所以在 V3 中添加流动性，是需要用户自己设置需要做市的价格区间的。V3 中创建者不同或者价格区间不同（或手续费水平不同，后面展开讨论）都是不同的流动性头寸 **position**。

需要注意的是，当池子中的交易价格，移动到做市的价格区间之外，我们注入的流动性将不再赚取手续费，也就是未激活状态，而当价格再次回到区间内时，流动性再次变为激活状态。
### 5.手续费
在 V2 中，因为大家提供的流动性都是统一的手续费标准（0.3%），统一的价格区间(0, ∞)，所以大家的资金都是均匀分布在整个价格轴之上的，因此在每一笔交易的过程中，大家收取手续费的权重计算比例也应该是相等的。即每一笔手续费的收益分配，只基于用户提供的流动性相对于总流动性的比例来分配，出资多的得到的收益多，这很好理解。

然而 V3 的价格区间做市机制，让 V2 的分配机制不再公平，因为流动性都有不同的做市区间，也就是说不同的交易价格，使用的流动性是不同的，那么这个时候，再用出资比例来分配，是不合适的。

正确的方式应该是记录每一笔交易，都使用了哪些流动性头寸 **position**，再将这么些头寸汇总，按比例分配。

这样在理论上来说是合理的，如果是中心化的网络这么干的成本可能承受的起，但放到区块链的运行环境中，频繁的进行高昂的读写操作是不可行的，会为用户带来极高的 gas 费用，使得交易的摩擦成本飙升，变得不划算。

我们需要一个节省 gas 的实施方案。

### V3 手续费的完整流程
1.首先我们需要记录池子内全局的每单位流动性收取手续费的数量的累加变量 **feeGrowthGlobal**

2.在每个有流动性的 tick 上记录外侧的手续费数量 **feeGrowthOutside**

3.对于任意区间，利用公式计算出区间内的手续费总量 **feeGrowthInside**
### ***feeGrowthInside=feeGrowthGlobal−feeGrowthOutsidepₗₒ𝚠ₑᵣ−feeGrowthOutsideᵤₚₚₑᵣ***
4.该区间内会存在不同用户注入的不同流动性头寸 **position**，在 **position** 流动性数量发生变化时，累加距离上次变化到现在这段时间的手续费数量增量到 **tokensOwed** 变量（该 positioin 总共可回收的流动性数量）
### ***tokensOwed+=feeGrowthInside∗liquidityₚₒₛᵢₜᵢₒₙ(position的流动性数量)***
5.用户收取手续费，从 **tokensOwed** 中扣除，其他变量不受影响。
``` text
V3 的 position 实际上有两种，一种是 Pool 合约中的（属于底层合约，一般不与用户直接交互），
只要价格区间相同，就是同一种 position；而另一种是 PositionManager 合约中的，属于用户交互合约，
该合约的 position 会区分不同用户。这里为了描述逻辑的连贯性并没有对两种 position 做区分。
```
``` text
V2 的手续费计算流程是隐式的，合并在交易和移除流动性的流程中，逻辑比较简单。
首先因为所有流动性都有相同的价格区间，再者 V2 没有考虑做市时长的因素，
也就是说后注入的流动性会摊薄之前流动性的手续费收益。
```

# 2025-08-21

### ERC-20
#### 1.可选函数
ERC-20标准接口中有三个可选函数，主要是返回代币的基本信息，所以也可以称为元数据。其智能合约中的代码如下：
``` javascript
// IERC20Metadata.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./IERC20.sol";

interface IERC20Metadata is IERC20 {

    function name() external view returns (string memory);
    function symbol() external view returns (string memory);
    function decimals() external view returns (uint8);
}
```
- *name()*：返回的是代币的名称，例如：MyToken
- *symbol()*：返回的是代币的代号，例如：MTK
- *decimals()*：返回的是代币的使用的小数位数，例如8（意味着将代币总量除以100000000来获取表现的形式）
#### 2.必要函数
智能合约中标准定义的ERC20的必要函数如下：
``` javascript
// IERC20.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

interface IERC20 {

    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address recipient, uint256 amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
}
```
- *totalSupply()*：返回该ERC20代币在区块链上创建的总数量。
- *balanceOf(address account)*：返回地址拥有的代币数量。
- *transfer(address recipient, uint256 amount)*：将代币数量amount从调用者地址(msg.sender)转移到接收者地址，函数必须触发事件Transfer。
- *allowance(address owner, address spender)*：查询owner授权给spender的额度。
- *approve(address spender, uint256 amount)*：允许spender地址可以最多转移的代币数量为amount。
- *transferFrom(address sender, address recipient, uint256 amount)*：从sender向recipient地址转移amount数量的代币，函数必须触发事件Transfer。
- *Transfer*：当有代币转移时，必须触发Transfer事件。
- *Approval*：approve()函数成功执行时，必须触发Approval事件。
#### 3.避免与USDT合约交互陷阱
#### 3.1 现象：
有个业务场景，需要用户将ERC20版的USDT转入一个合约，然后满足一定条件时通过该合约将转入的USDT转回给用户。
在测试网上测试，一切正常。合约审查，完全没问题。顺利主网上线!
测试用户将USDT转入合约，满足条件后等待合约将USDT转回。这时候问题出现了，转入的USDT竟然转不出来了，转不出来了!
#### 3.2 原因：
ERC20 标准接口的 transfer 方法，都是有返回值定义的，**returns (bool)**;
比如前面提到的：
``` javascript
function transfer(address to, uint256 value) external returns (bool);
function transferFrom(address from, address to, uint256 value) external returns (bool);
```
但是USDT合约对transfer方法的具体实现，是**没有返回值**的。虽然代码里还写了两个return语句，实际上这两个return后面的方法调用都是没有定义返回结果的，否则编译器会报错，不可能部署成功。
USDT合约transfer方法如下：
``` javascript
 function transfer(address _to, uint _value) public whenNotPaused {
        require(!isBlackListed[msg.sender]);
        if (deprecated) {
            return UpgradedStandardToken(upgradedAddress).transferByLegacy(msg.sender, _to, _value);
        } else {
            return super.transfer(_to, _value);
        }
    }
```
#### 3.3 解决方法：
1.在自己合约代码里引用的ERC20接口中transfer方法中的返回值定义去掉。

2.使用SafeERC20接口调用SafeTransfer方法。

# 2025-08-20

## Uniswap v2里的swap操作

### uniswap v2的关键合约结构
```
v2-core/               #其中一个github仓库
├── UniswapV2Factory.sol
└── UniswapV2Pair.sol

v2-periphery/          #另一个github仓库
└── UniswapV2Router02.sol
```
#### 1.用户想要完成一次ERC20代币（如DAI->USDT）的交换涉及的步骤：
前端和UniswapV2Router02.sol进行交互，路由合约（Router02）去找到想要交换的两种ERC20代币的Pair完成交换。
简单理解成：
```
用户→UniswapV2Router02.sol→Pair[DAI/USDT]
```
 ① swapExactTokensForTokens/swapTokensForExactTokens：根据用户需要，会先调用路由合约（Router02）的这个方法

 ② transferFrom：用户的钱给Pair
 
 ③ swap：根据AMM的公式，在Pair内部完成一个交换
 
 ④ transfer：在Pair完成交换之后，将币转给用户

#### 2.用户想要完成多个TOKEN的的交换（如DAI->USDT->MKR）涉及的步骤：
 ① swapExactTokensForTokens/swapTokensForExactTokens：根据用户需要，会先调用路由合约（Router02）的这个方法

 ② transferFrom：用户的钱给Pair
 
 ③ swap：通过路由合约，进行两次交换，第一次是DAI->USDT，第二次是USDT->MKR

 ④ transfer：在Pair完成交换之后，将币转给用户

## Uniswap v2里的手续费机制
#### 1.为什么随着交易产生越来越多，流动性会越来越大？
- 这是因为每笔交易会收取0.3的手续费。
- 手续费不会被提取，而是直接保留在流动性池中。
- 手续费增加了池中代币的总储备量。

#### 2.如何让LP（Liquidity provider）受益于手续费？
- 手续费收益按LP在池中所占份额进行分配，持有的LP token代表其在池中的份额。
- 由于手续费增加在了流动性池中，LP会按照份额比例分配同样比例的手续费。

#### 3.protocol fee：在LP手续费中分一杯羹（1/6）
~~~ javascript
// if fee is on, mint liquidity equivalent to 1/6th of the growth in sqrt(k)
    function _mintFee(uint112 _reserve0, uint112 _reserve1) private returns (bool feeOn) {
        address feeTo = IUniswapV2Factory(factory).feeTo();
        feeOn = feeTo != address(0);
        uint _kLast = kLast; // gas savings
        if (feeOn) {
            if (_kLast != 0) {
                uint rootK = Math.sqrt(uint(_reserve0).mul(_reserve1));
                uint rootKLast = Math.sqrt(_kLast);
                if (rootK > rootKLast) {
                    uint numerator = totalSupply.mul(rootK.sub(rootKLast));
                    uint denominator = rootK.mul(5).add(rootKLast);
                    uint liquidity = numerator / denominator;
                    if (liquidity > 0) _mint(feeTo, liquidity);
                }
            }
        } else if (_kLast != 0) {
            kLast = 0;
        }
    }
~~~
其中feeOn是控制是否开启收取protocol fee的一个开关，它是由feeTo决定的：
~~~ javascript
address feeTo = IUniswapV2Factory(factory).feeTo();
feeOn = feeTo != address(0);
~~~
也就是说当交易的contract的feeTo地址为零的时候，则让feeOn处于关闭状态。

# 2025-08-18

学习并总结了一些uniswap v2的内容：

**Uniswap一些核心内容：主要来源于官方文档**

1.Uniswap的交易对是任意ERC20交易对，增加流动性（提供相应的两种ERC20币）会获得流动性代币，你也可以随时使用流动性代币赎回你的ERC20代币。增加流动性会减小交易时的价格滑点。

2.Uniswap采用**恒定乘积**自动做市，因此大额交易和小额交易相比，其执行时的比率（价格）会指数级变得更差，当然就是这样设计的。

3.由于套利行为的存在，Uniswap价格始终趋向于结算价。

4.交易时，Uniswap收取0.3%的手续费，支付给所有流动性提供者。这实质上会使恒定乘积的K变大。

5.Uniswap生态参与者分为三类：流动性提供者，交易者，开发者。
- **流动性提供者**又分为四类：被动型，专业型，代币项目方和DeFi创新者。
- **交易者**也分为几类：投机者，套利者，Dapp用户，智能合约。（其中套利者和投机者的区别是套利者利用不同平台的价格差获利，投机者通过交易代币获利）。
- **开发者**。也可以分为下面几种：
  
    ①开源特性为其它项目使用Uniswap功能提供了可能。你可以在主要的Defi项目上发现Uniswap的函数。
    
    ②钱包集成了交易代币和流动性供给功能。

    ③DEX（去中心化交易所）可以拉取Uniswap的流动性，从而将流动性和交易分离开来。对这些项目来说，Uniswap是最大的单一去中心化流动性源。

6.Uniswap V2的智能合约系统由两部分组成：**核心合约**，它为Uniswap上所有交易对提供基础的安全保证。周边合约，用来和核心合约交互，方便用户使用，但并不是核心合约的一部分。

7.**核心合约由一个单例factory和多个交易对pairs组成**。factory用来创建和索引pairs。核心合约设计的很短小简洁，这样减小复杂性，更不易出错，但是相对的，对用户有时就不友好了。实质上，并不推荐直接和核心合约交互，而是使用周边合约交互。

8.**factory合约保存了启用pairs的字节码**。它的主要工作是为唯一的交易对创建唯一的智能合约。它同时也控制着开发团队招手续费收取开关。

9.**pairs交易对主要提供了两个功能**：一是提供自动做市并追踪交易池中的代币余额；二是暴露相关数据给外部以作为去中心化价格预言机。

10.**周边合约**是一套合约的集合，它们设计用来支持和核心合约作特定领域的交互。

11.**Library库合约**。Uniswap V2中的库提供了大量便利工具用来获取数据和价格。

12.**Router路由合约**。用于和前端交互的合约，用来实现交易和流动性管理。它还原生支持多交易对交易，将ETH作为第一类公民，同时提供移除流动性时使用元数据交易的支持。

13.**提前发送代币的交易方式**。通常，代币如果通过第三方合约进行转移，需要授权后才能操作。Uniswap V2采用了一种不同的设计，不使用授权，而是在交易前需要先发送代币到交易对。交易时，合约会检查当前保存的代币余额和合约实际代币余额，以这两者之间的差额作为输入代币的数量。因此在调用交易对中任何需要代币的方法时必须先将相应数量的代币发送到交易对（除了Flash Swaps先借后还交易这个例外）。

14.**WETH**。Uniswap V2并不支持ETH/ERC20交易对，因此必须使用WETH模拟。用户可以忽略其中细节，只是简单的使用周边合约来包装和解包装ETH。

15.**最小流动性**。为了消除截断影响和增加流动性供给理论上的最小滴答，交易对燃烧了最初的最小流动性。它只发生在第一次提供流动性期间，对绝大部分交易对而言，这个值是微不足道，可以忽略不计的。

# 2025-08-16

今日编写了前端重要代码：
Welcome.tsx
~~~ tsx
import { useContext } from "react";
import { AiFillPlayCircle } from "react-icons/ai";
import { SiEthereum } from "react-icons/si";
import { BsInfoCircle } from "react-icons/bs";

import { TransactionContext } from "../context/TransactionContext";
import { Loader } from ".";

const commomStyles = "min-h-[70px] sm:px-0 px-2 sm:min-w-[120px] flex justify-center items-center border-[0.5px] border-gray-400 text-sm font-light text-white";

const Input = ({ placeholder, name, type, value, handleChange }: any) => (
    <input
        placeholder={placeholder}
        type={type}
        step="0.0001"
        value={value}
        onChange={(e) => handleChange(e, name)}
        className="my-2 w-full rounded-sm p-2 outline-none bg-transparent border-none text-white text-sm white-glassmorphism"
    />
);
const Welcome = () => {
    const { connectWallet, currentAccount, formData, sendTransaction, handleChange } = useContext(TransactionContext);

    const handleSubmit = (e: { preventDefault: () => void; }) => {
        const { addressTo, amount, keyword, message } = formData;

        e.preventDefault();

        if (!addressTo || !amount || !keyword || !message) return;

        sendTransaction(currentAccount, addressTo, amount, message, keyword);
    };

    return (
        <div className="flex w-full justify-center items-center ">
            <div className="flex md:flex-row flex-col items-start justify-betwwen md:p-10 py-2 px-4">
                <div className="flex flex-1 justify-start flex-col md:mr-10">
                    <h1 className="text-3xl sm:text-5xl text-white text-gradient py-1">
                        Send Crypto <br /> across the world
                    </h1>
                    <p className="text-left mt-5 text-white font-light md:w-9/12 w-11/12 text-base">
                        Explore the crypto world. Buy and sell cryptocurrencies easily on Krypto.
                    </p>
                    {!currentAccount && (<button
                        type="button"
                        onClick={connectWallet}
                        className="flex flex-row justify-center items-center my-5 bg-[#2952e3] p-3 rounded-full cursor-ponter
                        hover:bg-[#2546bd]"
                    >
                        <p className="text-white text-base font-semibold">Connect Wallet</p>
                    </button>)}
                    <div className="grid sm:grid-cols-3 grid-cols-2 w-full mt-10">
                        <div className={`rounded-tl-2xl ${commomStyles}`}>
                            Reliability
                        </div>
                        <div className={commomStyles}>
                            Security
                        </div>
                        <div className={`rounded-tr-2xl ${commomStyles}`}>
                            Ethereum
                        </div>
                        <div className={`rounded-bl-2xl ${commomStyles}`}>
                            Web 3.0
                        </div>
                        <div className={commomStyles}>
                            Low fees
                        </div>
                        <div className={`rounded-br-2xl ${commomStyles}`}>
                            Blockchain
                        </div>
                    </div>
                </div>

                <div className="flex flex-col flex-1 items-center justify-start w-full md:mt-0 mt-10">
                    <div className="p-3 justify-end items-start flex-col rounded-xl h-40 sm:w-72 w-full my-5
                    eth-card white-glassmorphism">
                        <div className="flex justify-between flex-col w-full h-full">
                            <div className="flex justify-between items-start">
                                <div className="w-10 h-10 rounded-full border-2 border-white flex justify-center items-center">
                                    <SiEthereum fontSize={21} color="#fff" />
                                </div>
                                <BsInfoCircle fontSize={17} color="#fff" />
                            </div>
                            <div>
                                <p className="text-white font-light text-sm">
                                    Adress
                                </p>
                                <p className="text-white font-semibold text-lg mt-1">
                                    Ethereum
                                </p>
                            </div>
                        </div>
                    </div>

                    <div className="p-5 sm:w-96 w-full flex flex-col justify-start items-center blue-glassmorphism">
                        <Input placeholder="Adress To" name="addressTo" type="text" handleChange={handleChange} />
                        <Input placeholder="Amount（ETH）" name="amount" type="number" handleChange={handleChange} />
                        <Input placeholder="Keyword（Gif）" name="keyword" type="text" handleChange={handleChange} />
                        <Input placeholder="Enter Message" name="message" type="text" handleChange={handleChange} />

                        <div className="h-[1px] w-full bg-gray-400 my-2" />

                        {false ? (
                            <Loader />
                        ) : (
                            <button
                                type="button"
                                onClick={handleSubmit}
                                className="text-white w-full mt-2 border-[1px] p-2 border-[#3d4f7c] rounded-full cursor-pointer"
                            >
                                Send Now
                            </button>
                        )}
                    </div>
                </div>
            </div>
        </div>
    )
};

export default Welcome;
~~~

# 2025-08-15

**今天继续编写智能合约及其交互代码：**

*Transaction.sol*
~~~ rust
// SPDX-License-Identifier: MIT 
pragma solidity ^0.8.0;

contract Transactions {
    uint256 transactionCount;

    event Transfer(address from, address receiver, uint amount, string message, uint256 timestamp, string keyword);

    struct TransferStruct {
        address sender;
        address receiver;
        uint amount;
        string message;
        uint256 timestamp;
        string keyword;
    }

    TransferStruct[] transactions;

    function addToBlockchain(address payable receiver, uint amount, string memory message, string memory keyword) public {
        transactionCount += 1;
        transactions.push(TransferStruct(msg.sender, receiver, amount, message, block.timestamp, keyword));
    
        emit Transfer(msg.sender, receiver, amount, message, block.timestamp, keyword);
    }
    
    function getAllTransactions() public view returns (TransferStruct[] memory) {
        return transactions;
    }
    
    function getTransactionCount() public view returns (uint256) {
        return transactionCount;
    }
    

}
~~~
*TransactionContext.tsx*
~~~ typescript
import React, { useEffect, useState, type ReactNode } from "react";
import { ethers } from "ethers";

import { contractAddress, contractABI } from "../utils/constants";

export const TransactionContext = React.createContext<any>(null);

// 扩展Window接口
declare global {
  interface Window {
    ethereum: any;
  }
}

const { ethereum } = window;

const getEthereumContract = async () => {
  const provider = new ethers.BrowserProvider(ethereum);
  const signer = await provider.getSigner();
  const transactionContract = new ethers.Contract(
    contractAddress,
    contractABI,
    signer
  );

  return transactionContract;
};

interface TransactionProviderProps {
  children: ReactNode;
}
export const TransactionProvider = ({ children }: TransactionProviderProps) => {
  const [currentAccount, setCurrentAccount] = useState("");
  const [formData, setFormData] = useState({
    addressTo: "",
    amount: "",
    keyword: "",
    message: "",
  });
  const [isLoading, setIsLoading] = useState(false);
  const [transactionCount, setTransactionCount] = useState(localStorage.getItem("transactionCount"));

  const handleChange = (e: { target: { value: any; }; }, name: any) => {
    setFormData((prevState) => ({
      ...prevState,
      [name]: e.target.value,
    }));
  };
  const checkIfWalletIsConnected = async () => {
    try {
      if (!ethereum) {
        return alert("Make sure you have metamask!");
      }
      const accounts = await ethereum.request({ method: "eth_accounts" });
      if (accounts.length) {
        setCurrentAccount(accounts[0]);
      } else {
        console.log("No accounts found");
      }
    } catch (error) {
      console.log(error);
      throw new Error("No ethereum object");
    }

  };

  const connectWallet = async () => {
    try {
      if (!ethereum) return alert("Please install metamask!");
      const accounts = await ethereum.request({ method: "eth_requestAccounts" });
      setCurrentAccount(accounts[0]);

    } catch (error) {
      console.log(error);
      throw new Error("No ethereum object");
    }
  };


  const sendTransaction = async () => { 
    try {
      if (!ethereum) return alert("Please install metamask!");
      const { addressTo, amount, keyword, message } = formData;
      const transactionContract = await getEthereumContract();
      const parsedAmount = ethers.parseEther(amount);

      await ethereum.request({
        method: "eth_sendTransaction",
        params: [
          {
            from: currentAccount,
            to: addressTo,
            gas: "0x5208",
            value: parsedAmount,
          },
        ],
      });

      const transactionHash = await transactionContract.addToBlockchain(
        addressTo,
        amount,
        keyword,
        message
      );

      setIsLoading(true);
      console.log(`Loading - ${transactionHash.hash}`);
      await transactionHash.wait();
      setIsLoading(false);
      console.log(`Success ${transactionHash.hash}`);

      const transactionCount = await transactionContract.getTransactionCount();
      setTransactionCount(transactionCount.toNumber());
    } catch (error) {
      console.log(error);
      throw new Error("No ethereum object");
    }
  };

  useEffect(() => {
    checkIfWalletIsConnected();
  }, []);

  return (
    <TransactionContext.Provider value={{ connectWallet, currentAccount, formData, setFormData, handleChange, sendTransaction }}>
      {children}
    </TransactionContext.Provider>
  );
};
~~~

# 2025-08-14

今日继续根据视频教程写实用Dapp的智能合约代码，并且看了肖臻老师的《区块链技术与应用》的“BTC-密码学原理”，“BTC-数据结构”和“BTC-协议”的讲解视频

# 2025-08-13

今天通过了Ethernaut前 3 关，完成了减少gas的案例并做了笔记。
# Gas 优化案例笔记
## 1. Gas 优化基础概念
**什么是 Gas？**
- Gas 是以太坊网络中执行智能合约操作的计量单位
- Gas Price 是每单位 gas 的价格（通常以 Gwei 为单位） 
- Gas Limit 是交易允许消耗的最大 gas 数量
- 总费用 = Gas Used × Gas Price

**为什么需要 Gas 优化？**
- 降低用户交易成本
- 提高合约执行效率
- 减少区块空间占用
- 提升用户体验
2. 常见 Gas 优化技巧
   2.1 状态变量优化
   打包存储变量
## 2. 常见 Gas 优化技巧
### 2.1 状态变量优化
**打包存储变量**
~~~ text
// 不优化的写法
contract BadExample {
    uint256 a;  // 32字节
    uint8 b;    // 32字节（浪费24字节）
    uint256 c;  // 32字节
    uint8 d;    // 32字节（浪费24字节）
}

// 优化后的写法
contract GoodExample {
    uint256 a;  // 32字节
    uint256 c;  // 32字节
    uint8 b;    // 32字节（与下面变量打包）
    uint8 d;    // 32字节（与上面变量打包）
}
~~~
**使用合适的变量类型**
~~~ text
// 避免使用过大的数据类型
contract OptimizationExample {
    // 如果只需要存储0-100的数值，不要用uint256
    uint8 smallNumber;    // 1字节，适合0-255
    uint16 mediumNumber;  // 2字节，适合0-65535
    
    // 使用bool而不是uint(0/1)
    bool isActive;        // 1字节
    // uint8 isActive;    // 不推荐
}
~~~
### 2.2 函数优化
**使用 view 和 pure 修饰符**
~~~ text
// 只读函数使用view修饰符
function getBalance(address user) public view returns (uint256) {
    return balances[user];
}

// 不修改状态的纯函数使用pure修饰符
function calculateSum(uint256 a, uint256 b) public pure returns (uint256) {
    return a + b;
}
~~~
**减少外部调用**
~~~ text
// 避免重复的外部调用
contract BadExample {
    function badFunction() public {
        uint256 balance = token.balanceOf(msg.sender);
        if (token.balanceOf(msg.sender) > 0) {  // 重复调用
            // 执行操作
        }
    }
}

// 优化后的写法
contract GoodExample {
    function goodFunction() public {
        uint256 balance = token.balanceOf(msg.sender);
        if (balance > 0) {  // 使用缓存的值
            // 执行操作
        }
    }
}
~~~
### 2.3 循环优化
**避免昂贵的循环操作**
~~~ text
// 不推荐：在循环中进行状态修改
contract BadExample {
    mapping(address => uint256) public balances;
    
    function badTransferAll(address[] memory recipients) public {
        for (uint256 i = 0; i < recipients.length; i++) {
            balances[recipients[i]] += 100;  // 每次都是状态写入
            balances[msg.sender] -= 100;     // 每次都是状态写入
        }
    }
}

// 优化后的写法
contract GoodExample {
    mapping(address => uint256) public balances;
    
    function goodTransferAll(address[] memory recipients) public {
        uint256 totalAmount = recipients.length * 100;
        balances[msg.sender] -= totalAmount;  // 只写入一次
        
        for (uint256 i = 0; i < recipients.length; i++) {
            balances[recipients[i]] += 100;   // 状态写入
        }
    }
}
~~~
### 2.4 数据结构优化
**使用 mapping 而不是 array（当适用时）**
~~~ text
// 如果只需要根据地址查找值，使用mapping
contract OptimizationExample {
    // 推荐：O(1)查找时间
    mapping(address => uint256) public userBalances;
    
    // 不推荐：如果只是查找，array需要O(n)时间
    // address[] public users;
    // uint256[] public balances;
}
~~~
**使用短路规则**
~~~ text
// 利用逻辑运算符的短路特性
function checkConditions() public view returns (bool) {
    // 如果第一个条件为false，不会检查第二个条件
    return isCondition1() && isCondition2();
    
    // 如果第一个条件为true，不会检查第二个条件
    return isCriticalCondition() || isExpensiveCheck();
}
~~~

# 2025-08-12

今日学习了
#### 1.Solidity语法
#### 2.前端HTML+CSS+Web3.js实现连接MetaMask钱包、获取合约地址、将消息发送到区块链上并且通过合约地址获取用户的消息记录的功能。
#### 3.设计一个实用的Dapp应用可以进行钱包转账的功能。目前还在开发中，还未来得及提交到github上。

# 2025-08-11

今日学习了Dapp的架构和开发流程，solidity智能合约编程的基本语法，并根据学习手册编写了智能合约、部署了合约，还参加了技术向的Dapp从0到部署。下面是Dapp的架构和开发流程的笔记内容：
### 1.Dapp架构：主要由三个核心部分组成
#### 1.1 前端（User Interface）：
- 前端是 Dapp 与用户交互的界面，通常由 HTML、CSS 和 JavaScript（如 React、Vue 等框架）构建。与传统 Web 应用不同，Dapp 前端会连接区块链来调用智能合约，呈现数据和执行交易。
- 前端还需要集成区块链钱包（如 MetaMask）来进行身份验证和签署交易，确保用户的隐私和安全。
#### 1.2 智能合约（Smart Contracts）：
- 智能合约是 Dapp 的核心，它定义了应用的业务逻辑，并部署在区块链上。智能合约通过执行自动化的规则来确保交易和操作的透明性与不可篡改性。
- 在以太坊平台上，智能合约通常使用 Solidity 编程语言编写，并通过 Ethereum Virtual Machine (EVM) 执行。
#### 1.3 数据检索器（Indexer）：
- 智能合约通常以 Event 形式释放日志事件，比如释放代表 NFT 转移的 Transfer 事件，数据检索器会检索这些数据并将其写入到 PostgreSQL 等传统数据库中。
- Dapp 在前端进行数据展示时需要检索器内的数据。
#### 1.4 区块链和去中心化存储（Blockchain & Decentralized Storage）：
- 区块链用于存储智能合约的状态数据及交易记录。去中心化存储如 IPFS（InterPlanetary File System）或 Arweave，用于存储大规模的非结构化数据（如图片、文档等），确保数据不易丢失和篡改。
- 通过使用去中心化存储，Dapp 确保所有数据在多个节点上备份，保证数据的持久性和去中心化特性。
### 2.Dapp开发流程：需求分析与规划→智能合约开发→前端开发→与区块链交互→部署与上线
#### 2.1 需求分析与规划
- **确定功能需求**：需要定义用户可以进行的操作，比如转账、查询余额、创建投票等。
- **选择区块链平台**：决定在哪个平台上构建 Dapp（如以太坊、Solana、Polygon 等），这通常取决于目标用户群、交易成本、可扩展性等因素。
- **设计用户体验（UX）**：定义 Dapp 的界面设计和交互流程，确保用户能够轻松使用应用并与区块链交互。
#### 2.2 智能合约开发
- **编写智能合约**：使用 Solidity 或其他智能合约语言编写合约，确保合约的功能满足需求分析中定义的要求。
- **编写测试用例**：为智能合约编写单元测试，确保合约逻辑正确、无漏洞。
- **审计和优化**：对合约进行安全审计，确保合约的安全性，避免常见漏洞（如重入攻击、整数溢出等）。
#### 2.3 检索器开发
- **确定功能需要的数据内容**：前端使用的数据大部份都直接来自检索器，所以开发者需要确定前端工程师所需要的数据。
- **编写检索器程序**：目前主流的检索器框架，如 ponder 和 subgraph 都是用了 TypeScript 语言作为检索器的程序编写语言，开发者主要编写事件数据清理以及事件数据写入数据库的代码。
- **部署和运维**：编写程序完成后，一般使用 Docker 部署到云服务器中，当然目前很多检索器框架也提供 SaaS 服务，同时检索器作为一个常规的数据库应用需要运维。
#### 2.4 前端开发
- **选择前端框架**：可以使用现代前端框架（如 React、Vue）来构建 UI。前端将通过 JavaScript 与智能合约进行交互。
- **连接钱包**：通过集成 MetaMask 等 Web3 钱包，用户可以连接到 Dapp，并授权其与智能合约交互。
- **显示区块链数据**：前端需要从区块链和检索器内获取数据（如账户余额、交易记录），并通过用户界面展示。
- **处理交易签名与确认**：当用户发起交易时，前端需要与钱包进行交互，获取用户的签名并将交易发送到区块链。
#### 2.5 与区块链交互：前端和智能合约通过 Viem（推荐）、Ethers.js 或 Wagmi 等现代化库进行交互。
- **读取数据**：前端通过智能合约的公共函数读取区块链上的状态数据（如余额、合约信息）。
- **发送交易**：当用户发起交易时，前端需要通过钱包签署交易并发送到区块链，执行合约中的某个功能（如转账）。
#### 2.6 部署与上线：一旦开发完成，Dapp 进入部署阶段。
- **部署智能合约**：推荐使用 Hardhat 或 Foundry（现代化开发工具）将智能合约部署到测试网（如 Sepolia、Holesky）或主网。
- **前端部署**：将前端应用部署到去中心化平台（如 IPFS）或传统的 Web 服务（Vercel）。
- **发布和维护**：将 Dapp 上线，进行用户反馈收集，定期更新合约和前端，修复潜在问题。

# 2025-08-09

### 1. React 三大属性 - ref

#### 1.1 概念
ref 是 React 提供的用于访问 DOM 元素或组件实例的属性。通过 ref，可以直接操作 DOM 或获取组件内部方法。

#### 1.2 用法说明
ref 通常有三种使用方式：
1. 字符串 ref（已废弃）
2. 回调函数 ref
3. React.createRef（推荐）

#### 1.3 案例代码
```jsx
// 函数组件中使用 useRef
import React, { useRef } from 'react';

function InputFocus() {
	const inputRef = useRef(null);
	const handleClick = () => {
		inputRef.current.focus();
	};
	return (
		<div>
			<input ref={inputRef} type="text" />
			<button onClick={handleClick}>聚焦输入框</button>
		</div>
	);
}
```

```jsx
// 类组件中使用 createRef
import React from 'react';

class MyComponent extends React.Component {
	constructor(props) {
		super(props);
		this.myRef = React.createRef();
	}
	focusInput = () => {
		this.myRef.current.focus();
	};
	render() {
		return (
			<div>
				<input ref={this.myRef} type="text" />
				<button onClick={this.focusInput}>聚焦输入框</button>
			</div>
		);
	}
}
```

---

### 2. React 三大组件属性 - state

#### 2.1 概念
state 是组件内部用于保存和管理数据的对象。state 的变化会触发组件重新渲染。

#### 2.2 用法说明
1. 函数组件通过 useState 管理 state。
2. 类组件通过 this.state 和 this.setState 管理 state。

#### 2.3 案例代码
```jsx
// 函数组件中使用 useState
import React, { useState } from 'react';

function Counter() {
	const [count, setCount] = useState(0);
	return (
		<div>
			<p>当前计数：{count}</p>
			<button onClick={() => setCount(count + 1)}>加一</button>
		</div>
	);
}
```

```jsx
// 类组件中使用 state
import React from 'react';

class Counter extends React.Component {
	constructor(props) {
		super(props);
		this.state = { count: 0 };
	}
	handleAdd = () => {
		this.setState({ count: this.state.count + 1 });
	};
	render() {
		return (
			<div>
				<p>当前计数：{this.state.count}</p>
				<button onClick={this.handleAdd}>加一</button>
			</div>
		);
	}
}
```

---

# 2025-08-08

今日学习了React的JSX语法和组件化开发以及事件绑定机制

### 1.JSX语法
JSX（JavaScript XML）是一种 JavaScript 的语法扩展，允许在代码中直接编写类似 HTML 的结构。它最终会被 Babel 转换为 React.createElement 调用。  
**特点：**
- 可以在 JavaScript 代码中直接写标签结构。
- 标签必须闭合，属性采用驼峰命名。
- 支持表达式嵌入，使用 `{}` 包裹变量或表达式。
- 可以与 JavaScript 逻辑混合使用。
**示例：**
```jsx
const element = <h1>Hello, {userName}!</h1>;
```
### 2.组件化开发
组件是 React 的核心思想之一。每个组件都是一个独立的功能单元，可以复用和组合。  
**分类：**
- 函数组件：使用函数定义，推荐使用。
- 类组件：使用 ES6 class 定义，支持生命周期方法。

**组件的优点：**
- 复用性高，易于维护。
- 结构清晰，便于协作开发。

**示例：**
```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
### 3.事件绑定机制
React 的事件绑定采用合成事件（SyntheticEvent），兼容所有浏览器。  
**特点：**
- 事件名采用驼峰命名，如 `onClick`。
- 事件处理函数直接传递函数引用，不用加括号。
- 可以通过箭头函数或 bind 绑定 this。

**示例：**
```jsx
function MyButton() {
  function handleClick() {
    alert('按钮被点击了！');
  }
  return <button onClick={handleClick}>点击我</button>;
}
```

# 2025-08-07

# 一. 合规性要求与法律风险
## 1. 核心法律风险
### (1) 代币发行与交易行为的法律风险：
- 中国全面禁止代币融资，任何形式的代币发行和交易都可能构成非法金融活动。
- 参与者需承担相应的法律责任。
### (2) 赌博、传销、洗钱等刑事风险：
- Web3项目的经济激励模型直接决定法律风险等级。
- 涉及赌博性质或传销特征的模式均可能触犯刑法。
### (3) 场外交易中的洗钱与非法经营风险：
- 虚拟货币场外交易存在非法经营、洗钱等多重法律风险。
- 参与者需对交易背景和资金来源进行严格审查。
### (4) 民商事争议：
- Web3虚拟货币交易存在合同无效风险。
- 委托投资需严格规范操作，双方均需承担相应法律责任。
## 2. Web3入职法律风险
### (1) 雇佣关系新形态法律风险：
- Web3项目劳动关系存在法律空白，员工权益保障不足，入职前需谨慎评估风险。
- 即使签署了书面合同，也可能因主体资格缺失被认定为无效，成为“白纸黑字”。
### (2) 薪酬结构及风险提示：
- Web3企业混合薪酬结构违反《劳动法》货币支付规定。
- 员工面临支付无效和代币价值波动双重风险。
### (3) 虚拟货币出金与合规风险：
- 虚拟货币出金易卷入洗钱等刑事风险，导致资金冻结和法律责任。
- 建议协商保留法币收入，通过可信渠道出金并留存资金来源记录。
### (4) 项目合法性需提前审查：
- 员工需主动审查项目合规性，防范因项目非法被牵连调查风险。
- 应要求查阅项目核心文件，重点关注面向用户和返利结构等关键信息。
## 3. Web3项目的刑事法律风险
### (1) 开设赌场罪、赌博罪：
- 参与虚拟货币赌博平台开发运营构成开设赌场罪，技术开发人员也需承担刑事责任。
- 主从犯区分明显，主犯面临重刑重罚，从犯也需承担相应刑罚责任。
### (2) 非法经营罪：
- 利用虚拟货币进行跨境外汇兑换和支付结算构成非法经营罪，涉案金额大则刑罚重。
- 明知他人非法买卖外汇仍提供虚拟货币服务的，构成非法经营罪共犯或帮助信息网络犯罪活动罪。
### (3) 非法吸收公众存款罪：
- 以"挖矿"、购买矿机等名义吸收公众虚拟货币投资构成非法吸收公众存款罪。
- 技术开发、运营维护、宣传推广等各类参与者均可能被认定为共同犯罪并承担刑责。
### (4) 组织、领导传销活动罪：
- 以区块链、虚拟货币为名，采用层级返利模式发展用户的链游项目涉嫌传销犯罪。
- 技术开发人员明知软件用于传销仍提供支持的，可被认定为组织、领导传销活动罪共犯。
### (5) 洗钱罪：
- 虚拟货币被利用为跨境洗钱新工具，协助转换和转移犯罪所得构成洗钱罪。
- 洗钱罪是独立犯罪，即使上游犯罪未依法裁判也不影响其认定和追诉。
## 4. 其它风险
### (1) 虚拟货币风险：
- 中国对虚拟货币采取全面禁止态度，否定其货币地位和合法性。
- 严厉打击虚拟货币相关非法金融活动，禁止各类机构提供相关服务。
### (2) 代币发行的风险：
- 《关于防范代币发行融资风险的公告》规定 ICO 为未经批准非法公开融资的行为，明确了代币的非货币属性，并禁止各类代币交易所的兑换、买卖、定价和信息中介服务。
### (3) 挖矿的风险：
- 虚拟货币“挖矿”活动属于高能耗、高排放、低贡献的淘汰类产业，被全面禁止。
- 虚拟货币相关全部业务活动均属于非法金融活动。境外交易所向境内居民提供服务同样非法。
# 二. 典型攻击案例
## 1. 钓鱼攻击（Phishing）
### (1) 伪造邮件/网站：
- 攻击者会仿冒知名企业、学校、项目方的邮箱或网站，发送“面试通知”“奖学金发放”“账号异常”等紧急信息，诱导你点击钓鱼链接。
### (2) 社交平台钓鱼：
-  在微信群、QQ 群、Telegram、Discord 等社群中，冒充官方人员、HR、学长学姐，发布虚假招聘、空投、福利活动。
### (3) 假冒客服/好友：
- 通过盗号、伪造头像昵称等方式，冒充你信任的人，诱导你转账或泄露信息。
## 2. 恶意软件/木马（Malware & Trojan）
### (1) 伪装成面试/学习软件：
- 攻击者将木马程序伪装成“面试专用软件”“学习资料”“破解工具”等，诱导学生下载安装。
### (2) 剪贴板劫持：
- 木马常驻后台，监控剪贴板内容，一旦检测到钱包地址，自动替换为攻击者地址。
### (3) 浏览器扩展/插件后门：
- 通过伪装成热门插件，窃取浏览器中的敏感数据。
### (4) 远程控制：
- 木马获取系统权限后，可远程操控电脑，窃取文件、录屏、键盘记录等。
## 3. 社交工程攻击（Social Engineering）
### (1) 冒充 HR/导师/同学：
- 通过社交平台、邮件、电话等方式，冒充你信任的人，获取信任后诱导你操作。
### (2) 群聊钓鱼： 
- 在微信群、QQ 群、Telegram、Discord 等群聊中，冒充管理员、官方人员，发布钓鱼链接或虚假活动。
### (3) “好友”求助： 
- 盗取你好友账号后，以“急需用钱”“帮忙测试”“转发链接”等理由诱导你转账或点击恶意链接。
## 4. 供应链/第三方依赖攻击
### (1) 恶意浏览器插件/扩展：
- 攻击者在 Chrome 商店等平台上传带有后门的插件，诱导用户安装。
### (2) 开源库后门：
- 攻击者在常用开源库、依赖包中植入恶意代码，影响大量下游用户。
### (3) 官方渠道被劫持：
- 即使是“正规商店”下载的软件，也可能因被攻击而批量感染。
## 5. 地址污染与扫描木马（Clipper/Scanning Bots）
### (1) 剪贴板劫持：
- 木马监控剪贴板，一旦检测到钱包地址，自动替换为攻击者地址。
### (2) 输入框监听：
- 恶意软件监听浏览器或输入法，实时获取输入内容。
## 6. 传统隐私与账号安全风险
### (1) 弱密码/密码复用：
- 多个平台使用相同密码，一旦某个平台泄露，其他账号也被攻破。
### (2) 邮箱/SIM 卡劫持：
- 通过社工、运营商漏洞等手段，劫持你的邮箱或手机号，重置所有账号密码。
### (3) 双因素认证缺失：
- 未开启 2FA，账号易被盗。

# 2025-08-06

# 岗位职责

## 一、技术岗
### 1. 前端工程师
#### 岗位职责：
- 设计和开发基于区块链技术的前端应用，支持去中心化平台的交互。
- 通过 React、Vue 等框架实现高效的用户界面，支持钱包连接、交易签名、信息验证等功能。
- 集成并优化智能合约的前端交互，确保链上数据与用户界面的无缝连接。
- 与后端团队协作，基于 GraphQL 或 RESTful API 获取链上和链下数据。
- 持续优化前端性能，提升用户体验，确保在不同设备和浏览器上的兼容性。
- 关注 Web3 技术趋势，参与技术评审与分享，不断优化产品架构与代码质量。
#### 招聘要求：
- 本科及以上学历，计算机科学或相关专业，具备扎实的计算机基础。
- 精通 HTML、CSS、JavaScript，熟悉 React、Vue 等前端框架，能够独立构建复杂的 UI 界面。
- 熟悉 Web3.js、Viem、Metamask 等 Web3 技术栈，能够与智能合约进行交互。（现在 Ethers.js / Web3.js 已经不怎么使用了，大家现在基本上都是用的 Viem）
- 了解常用的区块链网络，如以太坊、Solana 等，具备 Dapp 开发经验者优先。
- 熟悉 Git 版本管理工具，具备良好的代码编写规范，注重代码可维护性。
- 良好的沟通能力和团队协作精神，能够在快速发展的环境中高效工作。
- 具有良好的问题解决能力，能在面对复杂的技术难题时，提出创新的解决方案。
- 有开源项目或 Web3 相关项目的经验优先。
### 2. 后端工程师
#### 岗位职责：
- 设计、开发和维护去中心化应用（Dapp）的后端服务，包括链上数据交互、智能合约集成和事务处理。
- 与前端团队合作，确保前后端数据交互的高效性和稳定性，支持多种 Web3 钱包（如 Metamask）的集成。
- 基于 Node.js、Python 等技术栈构建高性能的 RESTful 或 GraphQL API，以支持 Web3 平台的需求。
- 与智能合约团队合作，确保智能合约与后端服务的无缝连接，优化链上数据的读取和写入效率。
- 优化后端性能，确保系统的高可用性、高吞吐量和低延迟，满足高并发访问需求。
- 定期进行系统架构和代码的评审，不断提升代码质量与技术标准。
- 参与 Web3 技术的前沿研究，保持对新兴区块链技术的学习和应用，推动公司技术迭代。
#### 职位要求：
- 本科及以上学历，计算机科学或相关专业，具备扎实的计算机基础。
- 精通 Node.js、Go、Python 等后端开发语言，具有构建高并发、分布式系统的经验。
- 熟悉 Viem、Web3.js、Ethers.js 等 Web3 工具，能够与区块链进行交互并处理链上数据。
- 具备 RESTful API 或 GraphQL 开发经验，能够设计高效的 API 服务，支持前端与区块链的交互。
- 熟悉数据库技术，具备 MySQL、PostgreSQL 或 NoSQL 数据库的开发与优化经验。
- 对智能合约有一定的了解，具备与智能合约交互、读取链上数据等相关经验。
- 熟悉消息队列（如 RabbitMQ、Kafka）及事件驱动架构，能够处理异步事务。
- 熟悉容器化技术（如 Docker、Kubernetes），具备 CI/CD 经验者优先。
- 良好的代码编写规范与文档写作能力，具备良好的团队协作精神和沟通能力。
- 有Web3 项目开发经验或开源贡献者优先。
### 3. 智能合约工程师
#### 岗位职责：
- 设计、开发和部署智能合约，确保合约在区块链网络上的安全性、可靠性和高效性。
- 使用 Solidity 编写智能合约，涵盖各类去中心化应用的需求，如 NFT、DeFi、DAO 等。
- 与团队合作，定义智能合约的功能需求，并根据需求设计合适的智能合约架构。
- 部署智能合约至区块链网络（如以太坊、Polygon、Arbitrum、Base 等），并确保合约的高效执行。
- 编写智能合约的单元测试，进行代码审计与安全性测试，确保合约代码无漏洞，避免潜在的安全风险。
- 优化智能合约性能，减少 Gas 费用。
- 研究和应用最新的区块链技术和智能合约最佳实践，推动技术的持续进步。
- 与前端和后端开发团队紧密协作，确保智能合约与其他系统组件的顺畅集成。
- 为团队成员提供智能合约相关的技术支持和指导，推动团队的技术提升。
#### 招聘要求：
- 本科及以上学历，计算机科学或相关专业，具备扎实的计算机基础。
- 3 年以上智能合约开发经验，熟练使用 Solidity 或类似的智能合约开发语言。
- 熟悉 Ethereum、Polygon、Arbitrum、Base 等主流区块链平台，能够在这些平台上部署和维护智能合约。
- 了解智能合约开发的安全性问题，具备智能合约审计和漏洞修复经验，熟悉常见的攻击模式（如重入攻击、溢出、权限管理等）。
- 熟悉区块链的基本原理，理解 Gas 费用、交易确认、区块链共识机制等概念。
- 熟练使用 Foundry、Hardhat、Remix 等智能合约开发框架，具备项目开发、测试与部署经验。
- 具备一定的 Viem、Web3.js、Ethers.js 等 Web3 工具使用经验，能够与前端或其他系统进行无缝集成。
- 熟悉 IPFS、NFT、Token 标准（ERC-20、ERC-721、ERC-1155 等）及去中心化身份（DID）等 Web3 相关技术。
- 具有良好的代码编写规范，注重代码的可读性和可维护性。
- 良好的沟通能力和团队协作精神，能够在快速发展的环境中有效工作。
## 二、非技术岗
### 1. 产品与运营
#### 岗位职责：
- 在 Web3 产品生命周期中负责协调产品发布、用户反馈收集和持续改进流程，以提升用户体验和产品迭代效率。
- 执行以用户获取、留存和参与度提升为目标的增长战略，并监控实施效果。
- 与产品、技术、市场及合规团队紧密合作，确保产品上市（go-to-market）策略与各部门需求保持一致。
- 持续分析运营数据，跟踪关键绩效指标（KPIs），并根据数据提出优化建议。
#### 招聘要求：
- 熟悉产品上线（Go-to-market）全流程，擅长跨部门资源协调与项目推进。
- 具备扎实的数据分析能力，能熟练使用 SQL、Excel 或其他数据工具进行数据统计和洞察提炼。
### 2. 社区管理
#### 岗位职责：
- 构建并管理 Telegram、Twitter（X）、Discord 等社交平台的社区，实现持续的用户互动与增长；
- 组织线上 AMA（问答）、活动、竞赛等形式多样的社区互动，以提升用户活跃度和品牌粘性。
- 跟踪社区健康度指标和情感分析，定期向管理层汇报洞察与优化建议。
- 与营销团队协作，制定并执行内容日历，发布社区公告和运营手册。
#### 招聘要求：
- Web3、DAO 或 NFT 社区管理经验，深刻理解去中心化应用生态。
- 出色的文案撰写与沟通能力，能够有效引导社区讨论并快速响应用户反馈。
- 熟练使用社区数据分析工具，能够监测并解读用户行为与舆情动态。
- 具备活动策划与执行能力，能够独立组织线上及线下社区活动。
### 3. 研究分析
#### 岗位职责：
- 收集、整理并分析 Web3 行业市场与用户数据，编写可行性研究报告，为产品与运营提供决策支持。
- 跟踪区块链协议技术演进及生态动态，撰写深度研究报告或白皮书；
- 进行竞争对手分析，评估市场趋势与用户行为模式，为战略规划提供数据驱动的建议。
- 支持项目的加密经济模型设计与博弈论分析，以保证项目的经济激励合理性。
#### 招聘要求：
- 熟练使用 Excel、SPSS 或 Python 等数据分析工具，具备定量和定性研究方法经验。
- 深入了解区块链生态、DeFi 协议及加密经济学原理。
- 优秀的报告撰写与演示能力，能够清晰传达研究结论与建议。
- 精通链上数据分析工具（Glassnode、Token Terminal）。

# 2025-08-05

一、以太坊介绍
基本概念
定义：开源去中心化区块链平台
创始人：维塔利克·布特林（Vitalik Buterin）于2013-2014年提出
目标："下一代加密货币与去中心化应用平台"
核心创新：智能合约（Smart Contracts）
ETH（以太币）
作用：以太坊原生代币，用于支付网络手续费（Gas Fee）
地位：全球市值第二大的加密货币
通胀情况：The Merge后呈轻微通缩趋势，年通胀率0.2%-0.8%
二、以太坊与比特币的差异
维度
比特币（Bitcoin）
以太坊（Ethereum）
目标与定位
去中心化数字货币
去中心化平台，支持智能合约
编程能力
有限脚本语言
图灵完备编程语言
共识机制
工作量证明（PoW）
权益证明（PoS）
交易速度
每10分钟一个区块
约12秒一个区块
经济模型
总量固定2100万
供应灵活
三、以太坊的定位与演进
1. 以太坊1.0（PoW阶段）
机制：工作量证明（PoW）
问题：能耗高、扩展性差（约30 TPS）
2. 以太坊2.0与The Merge
时间：2022年9月15日完成
过程：
2020年12月：信标链启动
2022年9月：主网与信标链合并
成果：能耗降低99.95%，转向PoS机制
3. 未来升级路线图
分片技术：从执行分片到数据分片（预计2025-2026年）
EIP-4844：Cancun升级，降低Layer 2成本70%-90%
ZK-Rollup：批量验证技术，提升效率和安全性
四、以太坊生态概览
分层架构
Layer 1（L1）：以太坊主网，负责最终安全性与共识
Layer 2（L2）：扩展解决方案，包括Rollup、侧链等
应用层：
DeFi应用：Uniswap、Aave、Compound
NFT平台：OpenSea、Foundation、SuperRare
钱包应用：MetaMask、Coinbase Wallet、Rainbow
DAO工具：Snapshot、Aragon、Colony
五、以太坊文化与价值观
核心价值观
去中心化治理：无单一控制者
无需许可与开放性：开源透明
抗审查性：不受政府或机构干预
密码朋克精神：代码即法律
公共物品导向：优先考虑生态系统整体利益
可持续发展：环境责任与长期主义
六、以太坊核心机制
1. 账户系统
外部账户（EOA）：由私钥控制，可主动发起交易
合约账户（CA）：由代码驱动，被动响应交易
2. Gas模型
计算方式：Gas费用 = Gas Limit × Gas Price
EIP-1559升级后：
基础费用（Base Fee）：自动计算并销毁
小费（Tip）：给验证者的奖励
3. 以太坊虚拟机（EVM）
功能：运行智能合约的虚拟计算机
特点：图灵完备、全球同步、隔离安全
七、一般流程与类比
交易流程
用户通过EOA发起交易
交易附带Gas参数，验证者选择打包
EVM执行合约代码，修改存储
扣除Gas费用，保障资源合理使用

# 2025-08-04

今天学习了区块链的概念，区块链的特性是不可篡改、公开透明、快速交易性。
学到了比特币的数据结构是哈希指针，还有Merkle树，学习了其挖矿机制。
还了解了公链、私链和联盟链的区别，以及web2-web3.0-web3的发展和区别


# 2025.07.30


<!-- Content_END -->
