---
timezone: UTC+8
---

# jessiewang

**GitHub ID:** XXXJCSAMA

**Telegram:** @jessiewang_0914

## Self-introduction

rust solana

## Notes

<!-- Content_START -->

# 2025-08-29
<!-- DAILY_CHECKIN_2025-08-29_START -->
## **What are Real-World Assets (RWAs)?**

Real-World Assets (RWAs) refer to tangible assets that exist outside the digital realm. These can range from bonds to real estate properties, commodities, and machinery. The concept of RWAs in the blockchain context is about digital tokens that represent these physical and traditional financial assets. This includes currencies, commodities, equities, and bonds. The tokenization of RWAs is seen as one of the largest market opportunities in the blockchain industry, with a potential market size in the hundreds of trillions of dollars.

## **The Tokenization Process**

Tokenization is the process of converting real-world assets into digital tokens through blockchain technology. This process aims to enable fractional ownership and make high-value assets more accessible to a wider variety of individuals by facilitating the division of assets into smaller, more affordable units. Tokenized real-world assets, such as real estate, art, commodities, and even intellectual property, strive to offer greater liquidity, transparency, and accessibility. This digitization of physical assets is often seen as a way to modernize and democratize conventional financial markets.

## **Benefits of Tokenizing Real-World Assets**

Tokenizing real-world assets brings several benefits. Enhanced liquidity is one of the most significant advantages. Unlike traditional markets with set trading hours, the nature of blockchain technology allows for continuous engagement with these tokens, providing individuals with more flexibility. Moreover, the transparency built into blockchain technology increases investor confidence, further reducing the possibility of fraud and ownership conflicts. Tokenization also aims to lower the costs associated with asset management, such as paperwork, intermediaries, and legal fees, by removing many of the entry barriers prevalent in traditional financial markets.

## **Challenges in Tokenizing Real-World Assets**

Despite the potential benefits, tokenization of RWAs also presents challenges. Regulatory considerations that differ by jurisdiction are a significant concern. Any tokenization project must adhere to local laws and regulations. Security is another crucial issue due to the vulnerability of digital assets to fraud and hacking. Effective custody solutions and security measures are required to preserve these assets.

## **The Role of RWAs in Decentralized Finance (DeFi)**

RWAs find a place within the [Decentralized Finance](https://www.coinbase.com/learn/crypto-basics/what-is-defi) (DeFi) ecosystem, increasing the availability of these often inaccessible financial tools and opening up new horizons of applications. The tokenization of RWAs may contribute to the broader acceptance of the [crypto](https://www.coinbase.com/learn/crypto-basics/what-is-cryptocurrency) industry. With the potential to influence global economies, the opportunity exists. Its acceptance in traditional finance is ongoing. Nonetheless, technology can change how we interact with and engage in physical assets.
<!-- DAILY_CHECKIN_2025-08-29_END -->


# 2025-08-28
<!-- DAILY_CHECKIN_2025-08-28_START -->
简单来说，Uniswap 是一个去中心化交易所 (DEX decentralized exchange)，旨在成为中心化交易所的替代品。它在以太坊上完全自动化的运行，不需要管理员、经营者或者其他具有特殊权限的用户来参与运行。

### **Market makers**

做市商 (Market makers) 是向市场提供流动性（交易资产）的实体。流动性使交易成为可能：如果您想出售某物但没有人购买，则不会进行交易。一些交易对具有高流动性（例如 BTC-USDT），但有些交易对的流动性很低或根本没有（例如一些骗局或山寨币）。

DEX(去中心化交易所)必须有足够（或大量）流动性才能运作并替代中心化交易所。通常需要 DEX 团队投入大量资金来提供足够的流动性(考虑到众多的代币种类，这是一笔庞大的投入)，并且这样会使得 DEX 团队成为唯一的做市商，拥有巨大的权力，有违 DEX 去中心化的初衷。

更好的解决方案是允许任何人成为做市商。

### **AMM**

自动化做市商 AMM(Automated market maker) 是一个通用术语，包含不同的去中心化做市商算法，Uniswap 是其中最受欢迎的算法之一。

### **Constant product market maker (恒定乘积公式的做市商)**

```
x∗y=k
```

这是 UniswapV1 的核心公式，`x` 和 `y` 分别代表两个 token(在 V1 中其中一个必定是 eth)的资产数量，两者的乘积需要保持一个恒定的值，即 `k`。当你用 ETH 交易其他 token 时，你将 ETH 存入合约并获得一定数量的 token 作为交换。Uniswap 确保在每次交易后，资产数量的乘积保持不变。

### **如何定价**

要确保 AMM 能够完全无人工干预的自动运行，本质是需要让它自己决定每次交易的输入和输出量，即它需要知道如何定价。

**简单的设想，利用汇率构建价格**

```
Px = y / x,
Py = x / y
```

`x` 和 `y` 代表两个代币的数量，`Px` 和 `Py` 代表价格。

这是一个简单且符合直觉的设想，却存在着很大的问题。

假设我们用 2000 个 token 和 1000 个 ETH 创建了一个流动性池，此时 1 个 token 等于 0.5 个 eth，1 个 ETH 等于 2 个 token。

```
Ptoken = 1000 / 2000 = 0.5,
Peth = 2000 / 1000 = 2
```

一切看起来是正确的，但是如果我们使用该池子做交易，将 2000 个 token 交换成 ETH 会发生什么？我们将获得 1000 个 eth，这可是池子里所有的 ETH 储备！

**池子被抽干了！**

我们再来看看定价公式，上述定价公式实际上组成了一个和为恒定值的函数。

```
Msum  = Mx + My = Px * x + Py * y
```

`Msum` 代表两个代币的市值总和，`Mx` 和 `My` 代表两个代币的价值，价值等于 `价格 * 数量`。

```
Msum  = (y / x) * x + (x / y) * y = y + x = x + y
```

我们把之前的价格公式带入其中，就会发现总市值实际上就是两者的数量之和 `x + y`。即 `x + y = k`, `k` 为常数，其函数图形如下：

![Image](https://mmbiz.qpic.cn/mmbiz_png/dLy8RWvt9kvdrchibtsRUARvxJYKX6TBL4snkHrYXCgz0gEOaicuREBOwkuFLaFFsZCeoSGhUzAO7PJ5iabSOFfibw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

x + y = k

x 轴和 y 轴分别代表了两个代币在池内的数量，函数穿过 x 轴和 y 轴，根据图形可以很直观的看出这个公式允许 `x` 和 `y` 其中一个为 0！

这也就解释了为何我们用 2000 个 token 交换成 eth，流动性会枯竭的原因。

**正确的定价公式**

```
x∗y=k
```

每一笔交易都会改变两个代币的数量，无论数量如何变化，`k` 都应该保持不变。

![Image](https://mmbiz.qpic.cn/mmbiz_png/dLy8RWvt9kvdrchibtsRUARvxJYKX6TBLbd7O3eVibxmjHQvMVFzCMhic8XlrsRULg0I2AZXeF8Xss0EzFxOibhHmA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

这里的意思是用 `Delta x` 数量的`token x` 交换出 `Delta y` 数量的 `token y`。所以计算 `Delta y` 的公式为：

![Image](https://mmbiz.qpic.cn/mmbiz_png/dLy8RWvt9kvdrchibtsRUARvxJYKX6TBLibheVvI20BayFpbdIj2pXibCcloT8yPambvVIa4icWmMjl5k1C9wsy74w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

请注意，我们现在得到的 `Delta y` 是数量而不是价格。计算数量的方法对应 `Exchange.getAmount()`。

![Image](https://mmbiz.qpic.cn/mmbiz_png/dLy8RWvt9kvdrchibtsRUARvxJYKX6TBLW3jZBFZ8arkE282VY9hZIrtu6QO96hV4bCOVFs4O4W2L1jD6EDsrXQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

x \* y = k

由上图看出来，乘积恒定的函数是一个双曲线，不会与 x 轴或 y 轴相交，这使得流动性近乎无限。

还有一个有趣的特征，双曲线价格函数会导致价格滑点。**购买量越大，价格滑点越高，得到的越少。**

在测试文件 `./test/Exchange.test.js` 中可以看到滑点的变化：

```
// getTokenAmount
await exchange.getTokenAmount(toWei(1)); // 1.998001998001998001
await exchange.getTokenAmount(toWei(100)); // 181.818181818181818181
await exchange.getTokenAmount(toWei(1000)); // 1000.0

// getEthAmount
await exchange.getEthAmount(toWei(2)); // 0.999000999000999
await exchange.getEthAmount(toWei(100)); // 47.619047619047619047
await exchange.getEthAmount(toWei(2000)); // 500
```

如上所示，当我们试图掏空池子的时候，却只得到预期的一半。

注意：我们最初基于汇率的定价函数没有错（恒定和公式的 AMM）。事实上，当我们交易的代币数量相对数量非常小时。但是要提供 AMM，我们需要更复杂的东西。

## **Exchange 合约实现**

V1 的 Exchange 合约包含了定价、交易、添加/移除流动性、分发 LP token 代币功能。

### **增加流动性**

```
function addLiquidity(uint256 _tokenAmount)
    public
    payable
{
    if (getReserve() == 0) {
        // 初始添加流动性，直接添加，不需要限制
        IERC20 token = IERC20(tokenAddress);
        token.transferFrom(msg.sender, address(this), _tokenAmount);
    } else {
        // 后续新增流动性则需要按照当前的数量比例，等比增加
        // 保证价格添加流动性前后一致
        uint256 ethReserve = address(this).balance - msg.value;
        uint256 tokenReserve = getReserve();
        uint256 tokenAmount = msg.value * (tokenReserve / ethReserve);

        require(_tokenAmount >= tokenAmount, "insufficient token amount");

        IERC20 token = IERC20(tokenAddress);
        token.transferFrom(msg.sender, address(this), tokenAmount);
    }
}
```

### **定价功能**

虽然是定价功能，但实际上我们传入输入数量，计算得到了输出数量。

```
// This is a low-level function, so let it be private.
function getAmount(
    uint256 inputAmount,
    uint256 inputReserve,
    uint256 outputReserve
) private pure returns (uint256) {
    require(inputReserve > 0 && outputReserve > 0, "invalid reserves");

    return (inputAmount * outputReserve) / (inputReserve + inputAmount);
}

// 传入eth数量得到能够换取的token数量
function getTokenAmount(uint256 _ethSold) public view returns (uint256)

// 传入token数量得到能够换取的eth数量
function getEthAmount(uint256 _tokenSold) public view returns (uint256)
```

### **交易功能(swap)**

现在，我们已准备好实现交易功能

```
// 使用eth购买token
function ethToTokenSwap(uint256 _minTokens) public payable {
    uint256 tokenReserve = getReserve();
    uint256 tokensBought = getAmount(
        msg.value,
        address(this).balance - msg.value,
        tokenReserve
    );

    require(tokensBought >= _minTokens, "insufficient output amount");

    IERC20(tokenAddress).transfer(msg.sender, tokensBought);
}
```

将 ETH 换成 token 意味着将一定数量的 ETH （存储在 `msg.value` 中）发送到可支付的合约函数并获得代币作为回报。请注意，我们需要将 `msg.value` 从合约的余额中减去，因为在调用该函数时，发送的 ETH 已经添加到其余额中。

这里的另一个重要变量是 `_minTokens` —— 这是用户想要用 ETH 换出 token 的最小数量。此金额在 UI 中计算，使用用户设置的最大滑点来计算，即用户发送的交易其最小成交量是 `期望的成交量 * (1 - Slippage)` 。这是一个非常重要的机制，可以保护用户免受抢跑机器人(front-running bots)的攻击。

> front-running bots 会在用户订单前面插入一个相同方向的订单，比如当用户想买时，在用户订单前面插入一个同样买入的订单，当 bots 的订单完成后，再执行用户订单，必然会导致用户承受更高的价格，而当用户订单完成后，池中价格已上涨，bot 通常会将之前的订单卖出，赚取其中的利差。而用户设置了最小成交量之后，当价格偏差太大，输出数量小于设定值，合约会直接将交易 revert，有效的避免 front-running bots 攻击

```
// 使用token购买eth
function tokenToEthSwap(uint256 _tokensSold, uint256 _minEth) public {
    uint256 tokenReserve = getReserve();
    uint256 ethBought = getAmount(
        _tokensSold,
        tokenReserve,
        address(this).balance
    );

    require(ethBought >= _minEth, "insufficient output amount");

    IERC20(tokenAddress).transferFrom(msg.sender, address(this), _tokensSold);
    payable(msg.sender).transfer(ethBought);
}
```

合约首先从用户那里转入 `_tokensSold` 数量的 token 进入合约，然后将 `ethBought` 数量的 ETH 转给用户，完成资产的交换。

### **LP token**

我们需要有一种方法来奖励流动性提供者的代币。如果他们没有得到激励，就不会有人提供流动性，没有人会白白把他们的 token 放在第三方合同中。此外，该奖励不应该由我们支付，否则我们必须获得投资或发行通胀类型的 token 来为其提供资金。

唯一好的解决方案是对每次代币交易时收取少量费用，并在流动性提供者之间分配累积费用。这似乎也很公平：用户（交易者）使用他人提供的服务（流动性），理应付费。

为了奖励的公平性，我们需要按比例奖励流动性提供者，他们的贡献，即他们提供的流动性数量。如果有人提供了 50% 的池流动性，他们应该得到 50% 的手续费。

**定义**

`LP token` (Liquidity Provider token 流动性提供者的凭证) 是向流动性提供者发行的 ERC20 代币，是一种权益证明：

1.  流动性提供者注入流动性，获得 `LP token`
    
2.  获得的 `LP token` 数量与用户在流动性池中的流动性份额成正比
    
3.  手续费根据持有的 `LP token` 数量按比例分配
    
4.  `LP token` 可以换回 流动性 + 累计分配的手续费
    

**如何制定发行数量**

首先 LP 合约需要满足两个要求

1.  由于流动性随时都可能变化，每一位用户所拥有的份额必须时刻保持正确性
    
2.  减少非常昂贵的 storage 操作（SSTORE, SLOAD），因此，不能频繁重新计算和更新份额
    

如果我们发行大量代币（比如 10 亿个）并将它们分配给所有流动性提供者。如果我们总是分发所有的代币（第一个流动性提供者得到 10 亿，第二个提供者又需要重新分配这 10 亿，第三个同样...），我们将被迫重新计算已发行的代币，这样操作是很昂贵的。如果我们最初只分配一部分代币，那么我们就有可能达到供应限制，这最终将迫使使用重新分配现有份额。

唯一好的解决方案就是不设限制，在增加新的流动性时铸造新的代币。也就是说 `LP token` 是没有发行上限的。

不过也不用担心通货膨胀，因为铸造 `LP token` 需要提供流动性（`LP token` 的数量就是衡量流动性的量化指标），而想要取回流动性，则必须销毁`LP token`。

下面是通过注入的流动性数量计算铸造 `LP token` 数量的公式：

```
amountMinted = totalAmount * (ethDeposited / ethReserve)
```

由于 V1 的交易对都含有 eth，这里只考虑 ETH 的价值和数量比例

在修改 `addLiquidity()` 之前，需要让我们的 Exchange 合约成为 `ERC20` 合约并修改构造函数

```
// 继承ERC20标准合约
contract Exchange is ERC20
{
    constructor(address _token) ERC20("Uniswap-V1-like", "UNI-V1")  {
        ...
    }
    ...
}
```

现在改造 `addLiquidity()`

```
function addLiquidity(uint256 _tokenAmount)
    public
    payable
    returns (uint256)
{
    if (getReserve() == 0) {
        ...

        uint256 liquidity = address(this).balance;
        _mint(msg.sender, liquidity);   //  ERC20._mint() 向流动性提供者发送 LP token

        return liquidity;
    } else {
        // 后续新增流动性则需要按照当前的数量比例，等比增加
        // 保证价格在添加流动性前后一致
        uint256 ethReserve = address(this).balance - msg.value;
        uint256 tokenReserve = getReserve();
        // solidity不支持浮点运算，所以运算顺序非常重要，提倡先乘后除原则
        // 如果 msg.value * (tokenReserve / ethReserve) 的写法会产生计算误差
        uint256 tokenAmount = (msg.value * tokenReserve) / ethReserve;

        // 保证流动性按照当前比例注入，如果token少于应有数量则不能执行
        require(_tokenAmount >= tokenAmount, "insufficient token amount");

        IERC20 token = IERC20(tokenAddress);
        token.transferFrom(msg.sender, address(this), tokenAmount);

        // 根据注入的eth流动性 与 合约eth数量 的比值分发 LP token
        uint256 liquidity = (totalSupply() * msg.value) / ethReserve;
        _mint(msg.sender, liquidity);   //  ERC20._mint() 向流动性提供者发送 LP token

        return liquidity;
    }
}
```

### **手续费**

在收取手续费之前先思考几个问题：

1.  我们要收取 ETH 或代币的费用吗？我们想用 ETH 还是代币向流动性提供者支付奖励？
    
2.  如何从每次交易中收取少量固定费用？
    
3.  如何根据流动性提供者的贡献将累积费用分配给他们？
    

先看看问题 2 和 3，我们可以在每次交易进行时发送额外的费用，并积累到一个基金池中，任何流动性提供者都可以从中提取相应份额的手续费。

1.  每次交易，从中扣除手续费，而不是额外收取
    
2.  我们已经有了资金（被注入池中充当流动性的资产）——这是外汇储备！储备金可用于积累费用。这意味着**储备会随着时间的推移而增长**，所以恒定乘积公式不是那么恒定！不过和储备金相比，付给 LP (Liquidity provider 流动性提供者)的费用是很小一部分，并因此也很难通过分配手续费的路径来影响储备金。
    
3.  所以，现在已经有了第一个问题的答案：费用以交易资产的货币支付(即交易对中的两种货币)
    

UniswapV1 从每次交易中收取 0.03% 的手续费。为了计算简单，我们收取 1%。向合约添加费用就像添加几个乘数一样简单 `Exchange.getAmount()`

```
// 基础公式 outputAmount = (inputAmount * outputReserve) / (inputReserve + inputAmount)
// This is a low-level function, so let it be private.
function getAmount(
    uint256 inputAmount,
    uint256 inputReserve,
    uint256 outputReserve
) private pure returns (uint256) {
    require(inputReserve > 0 && outputReserve > 0, "invalid reserves");

    // 收取1%的手续费
    // solidity 不支持浮点运算，所以分子和分母同时 × 100，提高除法运算精度
    uint256 inputAmountWithFee = inputAmount * 99;  // 100 - 1 扣除手续费
    uint256 numerator = inputAmountWithFee * outputReserve;
    uint256 denominator = (inputReserve * 100) + inputAmountWithFee;

    return numerator / denominator;
}
```

### **移除流动性 Remove liquidity**

为了移除流动性，我们可以再次使用 `LP token` ：我们不需要记住每个流动性提供者存入的金额，而是根据 `LP token` 份额计算移除的流动性数量

```
function removeLiquidity(uint256 _amount)
    public
    returns (uint256, uint256)
{
    uint256 ethAmount = (address(this).balance * _amount) / totalSupply();
    uint256 tokenAmount = (getReserve() * _amount) / totalSupply();

    // ERC20._burn() 销毁LP
    _burn(msg.sender, _amount);
    // 返还用户质押的 ETH 和 token
    payable(msg.sender).transfer(ethAmount);
    IERC20(tokenAddress).transfer(msg.sender, tokenAmount);

    return (ethAmount, tokenAmount);
}
```

当流动性被移除时，它会以 ETH 和 token 的形式返回，当然，它们的数量是平衡的（按照当时池子中两个资产的比例）。

这是造成**无常损失**(IL Impermanence Loss)的地方：随着以美元计价的价格变化，资产储备数量的比例随时间变化。当流动性被移除时，数量的比例可能与流动性存入时的数量的比例不同。这意味着您将获得不同数量的 ETH 和 token，它们的总价格可能低于您将它们放在钱包中的价格。

```
removedAmount = reserve * (amountLP / totalAmountLP)
```

### **理解无常损失**

举个栗子：

1.  假定现在 1 ETH 价值 100 DAI, 你添加了 1 ETH 和 100 DAI 作为流动性, 其资产总价值为 200 DAI `assets = 1 * 100 + 100`
    
2.  一段时间后，ETH 价格上涨到 150 DAI, 那么此时池子中两者数量的比例应为 ETH:DAI = 1:1.5 ，因为价格就是两者数量的比值
    
3.  然后利用 `x * y = k` 计算两者数量，`k` 是一个恒定的值，所以可以由第 1 步添加时的数值计算 k = 1 \* 100 = 100，然后根据第 2 步已知的比例计算两者数量，ETH 约 0.8165 个， DAI 约 122.47 个，两者的乘积应还是 100 （因为小数点精度的取舍，会有一点误差）
    
4.  这个时候你的流动性按照 DAI 来计价是 244.94 `assets = 150 * 0.816 + 122.4`，浮盈 44.94（暂时不考虑手续费的收益），看起来收获颇丰
    
5.  但是如果你一开始并没有添加流动性，而是一直持有 1 ETH 和 100 DAI，然后等到 ETH 价格涨到 150 的时候，你的资产按照 DAI 计价就是 250 了，比第 4 步还要多 5DAI 左右的利润。这部分收益的差距就是无常损失。
    

为什么会有无常损失？

向市场提供流动性，即成为做市商，实际上是和市场中的用户做对手盘。而 AMM（自动化做市商）是被动的和市场中的用户做对手盘，即当市场中大部分人看好 ETH 后市，用 DAI 买入 ETH，你的流动性池子会被动的增加 DAI 而减少 ETH.

所以，提供流动性就代表了总是和市场做反向的操作，总是倾向去持有更多的弱势资产（当 ETH 上涨为强势资产，DAI 就是弱势资产）。于是当 ETH 上涨，你的流动性会不断提前抛出 ETH，而拿到更多的 DAI，这些被提前抛出的 ETH 就成了无常损失的来源，即这个时候无常损失可以理解为你在 ETH 上的踏空损失，或者是被外部套利者拿走了池内的价值。

如果你想更深入了解无常损失，可以看这篇博文淺談無常損失 (Impermanent Loss) 及其避險方式_(_[_https://medium.com/@cic.ethan/%E6%B7%BA%E8%AB%87%E7%84%A1%E5%B8%B8%E6%90%8D%E5%A4%B1-impermanent-loss-%E5%8F%8A%E5%85%B6%E9%81%BF%E9%9A%AA%E6%96%B9%E5%BC%8F-2ec23978b767_](https://medium.com/@cic.ethan/%E6%B7%BA%E8%AB%87%E7%84%A1%E5%B8%B8%E6%90%8D%E5%A4%B1-impermanent-loss-%E5%8F%8A%E5%85%B6%E9%81%BF%E9%9A%AA%E6%96%B9%E5%BC%8F-2ec23978b767) _"淺談無常損失 (Impermanent Loss "淺談無常損失 (Impermanent Loss) 及其避險方式")_
<!-- DAILY_CHECKIN_2025-08-28_END -->


# 2025-08-27
<!-- DAILY_CHECKIN_2025-08-27_START -->
![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps1.jpg)

每次调用contrat 合约size越大需要更多的storage

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps2.jpg)

以太坊早期没有合约大小限制，这导致资源消耗（CPU、存储、内存）与 gas 收费不匹配，容易被用于构建 DoS 攻击场景。

EIP-170 引入了 24 KB 合约大小限制以降低风险，但随着协议复杂化和功能需求升级，这一限制已成为开发者面对的瓶颈。

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps3.jpg)

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps4.jpg)

EIP-7907

大幅提高合约大小上限：从 24 KB 提升至 256 KB（2.8Gb满足需求），极大地提升了智能合约的表达能力和开发灵活性。

引入动态 gas 计费机制：

对于 ≤ 24 KB 的合约，gas 花费保持不变（即原本的固定成本）。

超出部分按比例收取额外 gas——通常是 每增加 32 字节收 2 gas，以确保规模越大资源消耗越高，平衡公平性与安全性。

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps5.jpg)

协议层的核心问题：

数据库不保存合约大小

以太坊协议里的数据库（state DB）只保存账户信息：nonce、balance、storageRoot、codeHash，并不保存合约代码大小。

要知道合约的 code size，必须把 code 从数据库里读出来，这会导致：

读取完整字节码 → O(N) 操作。

没法在读取前就准确计费（charge gas）。

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps6.jpg)

解决方案：增加一个 Code Size Index

新增一个 code size index 表，单独存储合约的大小。

新函数：readCodeSize → 可以在加载合约之前直接得到大小 → 再进行 gas 计费。

好处：避免恶意合约利用“大合约 + Gas 不足”制造 DoS。

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps7.jpg)

Client 实现的难点与顾虑

多一次 state lookup

每次还要多查一次 code size index → 额外 2,000–5,000 gas。

数据库兼容性

LevelDB/RocksDB 默认并不保存 value size，必须额外维护一张表。

意味着 database migration（迁移成本高）。

实现过于灵活

EIP 没有规定唯一实现方式，不同客户端实现可能差异 → gas 定价不一致的风险。

Stateless Client 与网络带宽问题

Stateless Client 模型：每个区块需要传播所有相关数据。

如果合约增大到 140KB → 区块传播开销暴涨。

P2P 协议也有限制：单个数据包最大 256KB → 大合约可能突破这一限制，增加传输复杂度。

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps8.jpg)

block里面所有的数据，MAC contract size提升到140的kB 那每个contract load就导致你传播那个block的时候 你要把那个140个KB加进去 然后这会让protocol bandwidth变得很大 所以你想把这个block里面的数据传播到stabless client的时候 他们就会有一点networking的问题

peer-to-peer protocol EVM不是 不光是blocks嘛 他们还有这个 peer-to-peer protocol.u.的协议 就是然后这个peer-to-peer protocol里面也有一些限制, 然后就是相当说block里面所有的数据 然后如果你这个MAC contract size提升到140的kB 那每个contract load就导致你传播那个block的时候 你要把那个140个KB加进去 然后这会让protocol bandwidth变得很大 所以你想把这个block里面的数据传播到stabless client的时候 他们就会有一点networking的问题 还有什么问题呢 就是这个peer-to-peer protocol EVM不是 不光是blocks嘛 他们还有这个 peer-to-peer protocol.u.的协议 就是然后这个peer-to-peer protocol里面也有一些限制

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps9.jpg)

Gas 计价与冷/热状态问题

参考 EIP-2929（cold/warm state）：

第一次访问合约 → 需额外加载 code，gas 较高。

之后在同一交易中再次访问 → 价格下降（warm）。

好处：避免每次都重复高额 gas 开销。

![](file:///C:\Users\lenovo\AppData\Local\Temp\ksohtml32212\wps10.jpg)

什么从 Fusaka 升级中移除

Core Devs 的主要顾虑：

 未来扩展：计划提升到 1 亿 block gas limit，担心过大合约读取让客户端“跟不上”。

Out-of-protocol index：新加的 index 表属于协议之外的设计，工程上复杂，迁移难度大。

未来的 Code Chunking：

未来可能采用 “code chunking” 技术（合约分块加载，按需取用 32–512 字节）。

如果现在强行加入 code size index，将来可能阻碍 chunking 的实现。
<!-- DAILY_CHECKIN_2025-08-27_END -->


# 2025-08-26
<!-- DAILY_CHECKIN_2025-08-26_START -->
**ZK + PARTH: An Architecture for Building Horizontally Scalable Blockchains，**the key barriers which have thus far hindered attempts at scaling blockchains securely are:The inverse correlation between TPS and Security.On traditional blockchains, any increase in parallelization/scalability leads to a proportional decrease in decentralization/security as increasing scale also increases the barrier of entry for running full nodes.Traditional smart contract state models risk race conditions unless we execute our VM in serial.Recall the token contract example from the previous post.If we try to scale a blockchain with many low TPS rollups, users are necessarily fragmented across different rollups, and we end up with more rather than less on chain transactions because we can't directly call smart contracts on rollup A from rollup B.in the past, many blockchains have tried and failed to overcome these barriers in the pursuit of massively parallel transaction processing, let's see if we can crack the code.
<!-- DAILY_CHECKIN_2025-08-26_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
## 互操作性趋势，

25年提的没有很多，行业内讨论主要集中在23年24年，互操作性目前的应用主要是桥，桥这个概念刚出来的时候业界把它大致分了三类：一是外部验证人（也就是第三方桥，以 Multichain 为代表），二是轻客户端（Cosmos 为代表），三是流动性网络+原子交换（Celer 为代表）。

-   所解决的问题： 1、链与链之间不通 区块链原生是封闭系统，无法直接访问其他链的状态或资产。
    
-   2、用户资产和体验碎片化 用户要跨链操作（比如将资产从以太坊转到 BNB Chain）必须依赖中心化平台或复杂步骤。
    
-   3、开发者构建多链应用门槛高 没有互操作性时，每条链都需要单独部署逻辑、独立运维。
    
-   2.2 互操作性发展历程
    
    -   经典**跨链互操作性协议**解决方案：跨链桥（Bridges）、轻节点（如 IBC、XCM）、侧链（Sidechains）、Rollup 假设桥等
        
    -   新型方案：共享排序层（Shared Sequencer，算不算是桥这点有争议，但肯定算互操作，是专门针对特定 L2 通过 Sequencer 方式来实现互通的）、流动性共享层的一些协议 DAMM（还没研究）、[\*\*链抽象](https://www.panewslab.com/zh/articles/jlm2688em11d) （\*\*EIP7702，以及最近kaito上火的 Anoma 意图驱动）
        
-   基本跨链桥的发展历程围绕易用性和安全性来展开。第三方桥的安全事件很多，但是确实方便，成本低；ZK bridge理论更安全，但是贵且慢。例子就是 Polyhedra 算是不错的zk bridge，用了创新型超快速的 Devirgo 协议+递归证明功能，目前貌似只能做到 10 秒内可以生成绝大多数区块链的区块头 Snark 证明。
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
区块链互操作性指的是**不同区块链系统之间能够相互通信、共享数据和资产，或者协同执行操作**的能力

区块链就像是没有联网的计算机，本身无法与其他区块链或[链下API](https://blog.chain.link/understanding-how-data-and-apis-power-next-generation-economies/)通信。这个问题也被称为[预言机问题](https://blog.chain.link/what-is-the-blockchain-oracle-problem/)（区块链希望通过预言机访问链下数据，然而不同的节点向外部网络发出相同的请求，很可能会得到不同的数据，从而引发安全问题），不仅导致区块链无法与传统系统交互，而且还导致链与链之间无法实现互操作性。随着我们不断朝着多链的世界发展，区块链互操作性协议成为了链与链之间（即跨链-通过某些特定的技术手段，能让价值跨过链与链之间的障碍进行直接交互，从而实现不同区块链之间的资产流通和价值转移。）交换数据和通证不可或缺的基础设施。

**为了深入了解跨链，可先从单链场景延伸，从应用层与技术层拆解互操作性的核心逻辑：**

## 一：应用层的互操作性

在区块链系统中，上层应用能够与不同的底层区块链平台进行无缝对接和切换，主要解决上层应用与底层链紧密耦合的问题。区块链互操作性的基础是跨链消息传输协议，这类协议能让区块链面向其他区块链读写数据。

### [跨链消息传输协议可以支持创建](https://chain.link/education-hub/blockchain)[跨链去中心化应用(dApp)](https://blog.chain.link/cross-chain-smart-contracts/)

一个dApp可以在不同区块链上部署[智能合约](https://chain.link/education/smart-contracts)。跨链dApp与多链dApp的不同之处在于，多链dApp通常在多个区块链上部署同样的应用，但是每条链上部署的智能合约都是相互独立的，与其他区块链没有关联。

![](https://xw4pe0eed67.feishu.cn/space/api/box/stream/download/asynccode/?code=OTNkYmY2MTNjYWI0MTA2YzQxN2Q1YzJlMzhkZThiNjRfOEFPZW9MOGh6NFJ3NWFwTEwwdmQyUkJDcVZGNnNPTERfVG9rZW46RW9kcGI0N05rb2NhWWZ4czJwZGNQSHR5blZiXzE3NTU5NTk5NDY6MTc1NTk2MzU0Nl9WNA)

跨链dApp如果利用跨链消息传输协议，则功能会受限。比如通证桥只能将一条区块链上的通证转移到另一条区块链上。然而，如果使用可以传输任意数据的消息传输协议，则能实现更加丰富的跨链功能和更加复杂的dApp，比如跨链[去中心化交易平台（DEX）](https://blog.chain.link/dex-decentralized-exchange-zh/)、跨链[去中心化货币市场](https://chain.link/education-hub/decentralized-money-markets)、跨链[去中心化自治组织（DAO）](https://blog.chain.link/daos-zh/)以及各种类型的[模块化应用](https://en.wikipedia.org/wiki/Modular_programming)。

## 二：链间互操作性

在不同区块链网络之间能够相互通信、交换数据和资产， **主要解决** “链级孤岛” 问题。

### 跨链互操作性协议（CCIP）

如今，独立的区块链层出不穷，每条区块链都有自己的优势和地域市场，这一趋势推动了多链生态愈发壮大。在这样的多链世界中，用户要能在一个应用中无缝使用各条区块链上独特的功能和资产，这将极大推动跨链智能合约的开发。当[去中心化预言机服务](https://blog.chain.link/what-is-chainlink/)出现并连通链下数据和安全的链下计算资源时，也同样推动了[DeFi](https://blog.chain.link/analyzing-the-defi-ecosystem-and-the-many-ways-chainlink-can-accelerate-adoption/)、[NFT](https://chain.link/education/nfts)和[链上游戏](https://chain.link/education-hub/play-to-earn)经济的蓬勃发展。

然而众所周知，由于现有跨链基础架构存在瓶颈，因此开发跨链应用非常困难。其一，通证桥和消息传递协议解决方案高度分化，大多数都仅服务于某两条链之间的应用。另外，许多通证桥都比较中心化，安全性较弱，而且也缺少透明或可靠的节点运营商，因此推高了终端用户的成本和处理时间。这些限制和漏洞导致了价值几千亿美元的用户资金损失，并阻碍了跨链创新。

为了应对区块链生态对跨链解决方案与日俱增的需求，我们很高兴地宣布发布[跨链互操作性协议](https://chain.link/use-cases/cross-chain)（下文简称CCIP）。CCIP是跨链通信新的开源标准，目的是在几百个公链和私有链网络之间建立通用的连接，让本来孤立的通证在所有链上生态之间流通，并实现跨链应用。

![](https://xw4pe0eed67.feishu.cn/space/api/box/stream/download/asynccode/?code=OTY3MGFmOTVhZWM3NDAzN2RmMWE4MDMzZjVlNzhkYTlfWWJjZzZoVDl5WU4wb2VObVJGRURoNlVrWnZhVXI0RWFfVG9rZW46WHZjUWJXenRIb0puZ1h4VzdZM2NqOUxubkpiXzE3NTU5NTk5NDY6MTc1NTk2MzU0Nl9WNA)

CCIP为智能合约开发者提供了具有计算能力的通用基础架构，能够跨越各个区块链网络传输数据和智能合约指令。CCIP将成为各种跨链服务的底层协议，其中包括Chainlink的可编程通证桥，用户可以将通证安全高效地转移到任何区块链网络中，并具有可扩展性。

## 三：链下数据互操作性

区块链网络与非区块链网络之间进行安全通信，主要解决链上链下数据的安全可信交互。技术层面：各区块链之间通过**跨链桥、跨链通信协议、跨链智能合约、跨链互操作平台**等机制互联。如：IBC（Cosmos）、LayerZero、Wormhole，允许链之间传输资产或消息。

### Cosmos 跨链通信协议 IBC

互联网促进了世界上不同地区不同类型计算机之间的相互通信，TCP/IP 的简单性和灵活性使它成为了标准 Internet 通信协议，被用于计算机、服务器、手机，甚至是小型物联网设备。Cosmos 被誉为「区块链互联网」，IBC 就是区块链互联网中的「TCP/IP」协议。它提供了一种无需许可的方式在区块链之间中继数据包，实现区块链之间的相互通信

![](https://xw4pe0eed67.feishu.cn/space/api/box/stream/download/asynccode/?code=MzgyMTJkZmY5MDQxYmUzYmU3NmNkMGU1NjFkZDJiNjhfTWh1OWN5QWZzcE1yV1FvdEZSRjZkbHlDRXYxSnNNUjhfVG9rZW46UnRydGI2QUZhb3VSR3V4Q0tHN2NLZm5pbmRoXzE3NTU5NTk5NDY6MTc1NTk2MzU0Nl9WNA)

IBC（Inter-Blockchain Communication）是一种通用的互操作性协议，它支持两个不同的区块链互相通信，而无需信任中间的任何人。IBC 不仅可以用于基于 Cosmos SDK 开发的区块链，也可以用于其他区块链，如以太坊、Polkadot 等。

## IBC 解决了什么问题？

IBC 解决了「跨链通信」的问题。互联网使信息可以在世界各地轻松流动。同样，不同区块链之间的信息也需要跨多个平台的自由访问。当用户想要使用区块链 A 的稳定币，通过区块链 B 的去中心化交易所 (DEX) 的流动性池 (LP) 产生收益。这就需要链之间的互操作性来实现。

IBC 不仅解决了互操作性问题，而且以信任最小化、安全、可扩展和通用的方式实现了跨区块链进行任意数据传输。「任意数据」包括资产的跨链和信息的跨链，比如代币和 NFT 资产的转移，也可以在使用一条区块链的同时管理另一条链上的账户，还可以从其他链上查询信息，等等。

## IBC 是如何工作的？

IBC 协议的独特之处是它采用分层设计，传输层和应用层来实现整个工作流程。传输层 (TAO：transport layer) 提供必要的基础设施来建立安全连接和验证区块链之间的数据包。应用层（application layer）建立在传输层之上，它定义了数据包应该如何被发送链打包、以及如何被接收链解包。

一个容易理解的类比： IBC 的工作原理类似邮件传递系统。当你通过邮政服务向某人发送一封信，这个邮政服务会把装有信件的信封存入收件人的邮箱。然后收件人打开信封并阅读你的信。IBC 的传输层可以被认为是邮政服务。邮政服务根本不关心信的内容是什么。它只执行从 A 点收取信封并发送到 B 点的动作。信封本身可以看作是从一条链发送到另一条链的 IBC 数据包。在这个信封上，你写了收件人的地址，这相当于 IBC 的数据包里包含发件人和收件人信息。最后，收件人（应用程序）收到信（数据包）打开它并阅读其中的内容。

![](https://xw4pe0eed67.feishu.cn/space/api/box/stream/download/asynccode/?code=NDk0MDNlZWFkMmFkNDg5NGIxNmFjYmNkYTQ1MjZmMjZfZW5oemNhakJFeDN4YzBHRnduUkJ5aFFTTzl2NUxGem5fVG9rZW46WklXaWI2TE9RbzU2WWp4TFVkYmNQZ0I2bnRqXzE3NTU5NTk5NDY6MTc1NTk2MzU0Nl9WNA)

_两个区块链之间 IBC 数据包流，图源：the-interchain-foundation_

![](https://xw4pe0eed67.feishu.cn/space/api/box/stream/download/asynccode/?code=OTFiNWE3M2Y1NGNjY2VlNWQ3MDViNTk0MDYwNTNlY2RfZUhEbXFid243eVFsTWVZYUdtRnpMYXJWZHNoWHFHbDJfVG9rZW46QU5hc2JlVUZpb0xpWUx4UjUzUGN3cXlabkljXzE3NTU5NTk5NDY6MTc1NTk2MzU0Nl9WNA)

_IBC 各组件间工作流程，图源：the-interchain-foundation_

### 传输层

需要跨链的消息被打包在数据包（packet）中，传输层负责传输、验证和排序这些数据包。在传输层，不关心数据包里面是什么，也不关心接收链怎么解码这个数据包。从传输层的角度来看，数据包中的信息只是随机字节。

传输层的关键组件是轻客户端、中继器、连接和通道。

**轻客户端**

负责验证数据包中的消息证明。轻客户端是运行完整节点的轻量级替代方案。与完整节点不同，它不存储所有区块数据也不执行交易。相反，他们只验证区块头。IBC 轻客户端实际上是某个区块链中的一种验证算法，用于跟踪另一个区块链的状态变化（时间戳、根哈希、下一个验证节点集哈希），这节省了空间并提高了处理共识状态更新的效率。

也就是说，使用 IBC 交互的两条独立区块链 A 和 B 具有彼此交易对手链的轻客户端。比如，链 A 上有个链 B 的轻客户端，当链 A 想与链 B 通信某个消息 X 的时候，它会将包含消息 X 的区块的块头和消息证明的数据包发送给链 B。然后链 B 使用接收到的数据包进行加密验证以确定链 A 执行了消息 X。反之亦然。

IBC 安全模型是基于轻客户端的而不是链。也就是说，IBC 协议并不关心链的信息，只要 IBC 轻客户端保持着有效的共识更新可以对 Merkle 证明进行验证就可以。这类似 IP 地址和 DNS，其中 IP 地址是 IBC 的 clientID，而 DNS 是 chainID。

**中继器**

在 IBC 中，区块链彼此间不会通过网络直接互相传递消息，而是依赖中继器进行通信。中继器是链下进程，负责监视运行 IBC 协议的每条链的状态，并将更新的数据包中继给交易对手链。如上一段的例子，当 A 向 B 发送消息 X，A 会在其状态机中提交或存储包含消息 X 的数据包的哈希值。当中继器看到 A 在状态机中提交了一条打算发送给 B 的消息 X 时，他们只需要拿起这条消息 X 并将其传递给 B。

中继器负责来回发送数据包，不能修改数据包，也不对数据包进行任何验证，因此不需要被信任。在建立连接和通道握手的时候，也需要使用中继器。当连接的某一端的链试图分叉或者其他恶意行为，中继器也可以提交不当行为作为证据。

依赖中继器通信存在一个缺陷：想象一下，如果每一对区块链之间都运行中继器，这将是非常复杂的，也及其浪费资源。所以 Cosmos Hub 为此而生，作为区块链之间传输数据的枢纽。只需要在区块链和 Cosmos Hub 之间运行一个中继器，这个区块链就可以与其他已经连接到 Cosmos Hub 的区块链彼此之间传输数据。

目前，链下中继器的运营方式短期内是可行的，但长期是不可持续的。对此，IBC 在跨链标准 ICS-29 中，提出了链上中继激励方法，包括三种不同的方式，费用中间件、费用补助、预算模块。目的是希望为中继者提供一个可持续的收入模式。

### LayerZero

LayerZero 是一个全链互操作协议，它通过一种创新的、无需信任的方式，实现了不同区块链之间的消息传递和数据通信，从而解决了加密生态中的流动性碎片化问题，为跨链应用提供了底层支持。它通过在各链上部署超轻节点（ULN），并借助 [预言机](https://www.google.com/search?sca_esv=c8be1566c129d7a0&cs=1&sxsrf=AE3TifO9s8xEFWAUeTtiRXwLWI1cuMQk3A%3A1755846160630&q=%E9%A2%84%E8%A8%80%E6%9C%BA&sa=X&ved=2ahUKEwix1Y3V7J2PAxV-SGwGHWQwFFsQxccNegQIBBAB&mstk=AUtExfCBcMHvcVp3RsK8wb_KtzKyW-p0VVF0AupwdCRCKxpkmQhdLAS-uGsIffyfnEBJwvOpDZbslzSQZMppe0vTLGHmcb06XkPzUH06zLLCHJDYydqljPTJV_grs-OyS9gufxG63JRZnxmjf_f71c_Uul5SRuVZcR0-XwrKBMh_F5uyduyaG0nlfaZO4dShWM_5X0yPvEc810Iw2IC0ctV-ZlJTvDU5kuneLbKcCpdp_864ZLrD6NF8kD9h8P-mfkhGLz59W4BmFnWFGs_TFWpkAIFyvv5SJoFfR7C0DIY6SEaJtC1DZwiGbzBHxPRe517kpxxiuqctaOdswg4WFM7L5Bl6rV9O1b9B85jmFojQq6fPbPjzBLzOcGY0hdhTUmJnPmBEHhsk-9EVRvlyVHTvI0ImdBr7VxMRShjoCeDQym8&csui=3)（Oracle）和 [中继器](https://www.google.com/search?sca_esv=c8be1566c129d7a0&cs=1&sxsrf=AE3TifO9s8xEFWAUeTtiRXwLWI1cuMQk3A%3A1755846160630&q=%E4%B8%AD%E7%BB%A7%E5%99%A8&sa=X&ved=2ahUKEwix1Y3V7J2PAxV-SGwGHWQwFFsQxccNegQIBBAC&mstk=AUtExfCBcMHvcVp3RsK8wb_KtzKyW-p0VVF0AupwdCRCKxpkmQhdLAS-uGsIffyfnEBJwvOpDZbslzSQZMppe0vTLGHmcb06XkPzUH06zLLCHJDYydqljPTJV_grs-OyS9gufxG63JRZnxmjf_f71c_Uul5SRuVZcR0-XwrKBMh_F5uyduyaG0nlfaZO4dShWM_5X0yPvEc810Iw2IC0ctV-ZlJTvDU5kuneLbKcCpdp_864ZLrD6NF8kD9h8P-mfkhGLz59W4BmFnWFGs_TFWpkAIFyvv5SJoFfR7C0DIY6SEaJtC1DZwiGbzBHxPRe517kpxxiuqctaOdswg4WFM7L5Bl6rV9O1b9B85jmFojQq6fPbPjzBLzOcGY0hdhTUmJnPmBEHhsk-9EVRvlyVHTvI0ImdBr7VxMRShjoCeDQym8&csui=3)（Relayer）两个链下实体来验证和传输数据，从而实现了安全高效的跨链通信，让资产和数据能在不同链之间自由流动。

核心功能与原理

-   **全链互操作性**：
    
-   LayerZero 的核心目标是实现所有区块链的无缝互联，让数据和资产能够轻松跨链移动，构建一个统一的区块链生态系统。
    
-   **无需信任的通信**：
    
-   该协议的创新之处在于，它通过预言机和中继器相互验证的方式，避免了对单一中心化中介的依赖，提高了协议的去中心化程度和安全性。
    
-   **核心组件**：
    
    -   **超轻节点（ULN）**：:部署在各支持区块链上的轻量级智能合约，作为跨链通信的端点。
        
    -   **预言机（Oracle）**：:一个链外实体，负责获取链上交易的区块头数据并将其传输到另一条链。
        
    -   **中继器（Relayer）**：:另一个链外实体，负责独立获取指定交易的证明并传输到目标链。
        
-   **模块化设计**：
    
-   LayerZero 的模块化和可扩展性允许它轻松支持新的区块链，并为开发人员提供了构建多样化跨链应用（如跨链DEX、借贷协议等）的基础。
    

解决了什么问题

-   **流动性碎片化**：
    
-   在现有的加密市场中，流动性分散在不同的区块链上，LayerZero 通过促进资产在各链间的自由流动，打破了这种孤立状态。
    
-   **传统跨链桥的限制**：
    
-   相比传统的跨链桥，LayerZero 提供了一种更安全、更高效的“消息传递”解决方案，尤其适合复杂的数据交互。
    

![](https://xw4pe0eed67.feishu.cn/space/api/box/stream/download/asynccode/?code=ZTA3NDExNTJhM2FmZTA0MTNlNjk5NWM5MmJhYzFiMjNfZmxpNW1hREJaRWdwa205dXhvYnh0cFNxU3lJV0dDRHhfVG9rZW46UnE0eGJzUERrb05iM0d4Wm1ndGNCY1k5bkliXzE3NTU5NTk5NDY6MTc1NTk2MzU0Nl9WNA)

## 互操作性趋势，

25年提的没有很多，行业内讨论主要集中在23年24年，互操作性目前的应用主要是桥，桥这个概念刚出来的时候业界把它大致分了三类：一是外部验证人（也就是第三方桥，以 Multichain 为代表），二是轻客户端（Cosmos 为代表），三是流动性网络+原子交换（Celer 为代表）。

-   所解决的问题： 1、链与链之间不通 区块链原生是封闭系统，无法直接访问其他链的状态或资产。
    
-   2、用户资产和体验碎片化 用户要跨链操作（比如将资产从以太坊转到 BNB Chain）必须依赖中心化平台或复杂步骤。
    
-   3、开发者构建多链应用门槛高 没有互操作性时，每条链都需要单独部署逻辑、独立运维。
    
-   2.2 互操作性发展历程
    
    -   经典**跨链互操作性协议**解决方案：跨链桥（Bridges）、轻节点（如 IBC、XCM）、侧链（Sidechains）、Rollup 假设桥等
        
    -   新型方案：共享排序层（Shared Sequencer，算不算是桥这点有争议，但肯定算互操作，是专门针对特定 L2 通过 Sequencer 方式来实现互通的）、流动性共享层的一些协议 DAMM（还没研究）、[\*\*链抽象](https://www.panewslab.com/zh/articles/jlm2688em11d) （\*\*EIP7702，以及最近kaito上火的 Anoma 意图驱动）
        
-   基本跨链桥的发展历程围绕易用性和安全性来展开。第三方桥的安全事件很多，但是确实方便，成本低；ZK bridge理论更安全，但是贵且慢。例子就是 Polyhedra 算是不错的zk bridge，用了创新型超快速的 Devirgo 协议+递归证明功能，目前貌似只能做到 10 秒内可以生成绝大多数区块链的区块头 Snark 证明。
<!-- DAILY_CHECKIN_2025-08-23_END -->

# 2025-08-21

区块链中的现实世界资产（RWAs）是代表实物和传统金融资产（如货币、商品、股票和债券）的数字通证。

现实世界资产（RWA）的通证化是区块链行业中最大的市场机遇之一，其潜在市场规模可达数百万亿美元。理论上，任何有价值的物品都可以通证化并上链。

这就是为什么通证化的 RWA 在数字资产行业中是一个不断增长的细分市场，越来越多的项目寻求将各种资产通证化，包括现金、商品、房地产等。

什么是通证化的现实世界资产（RWAs）？
通证化的现实世界资产（RWAs）是基于区块链的数字通证，代表实物和传统金融资产，如现金、商品、股票、债券、信贷、艺术品和知识产权。RWA 的通证化标志着这些资产的获取、交换和管理方式的重大转变，在区块链驱动的金融服务，以及由密码学和去中心化共识支持的广泛非金融应用场景中，创造了一系列新机遇。

将现实世界资产通证化
将现实世界资产通证化是指将资产的所有权以链上通证的形式表示。在此过程中，创建了底层资产的数字表现，实现了资产所有权的链上管理，有助于弥合实物资产与数字资产之间的鸿沟。

通证化资产的好处
流动性：通证化的现实世界资产（RWAs）可以使流动性较差的传统资产更易于被交易。通过将这些资产放到可以相互交互的区块链网络上，全球投资者可以更轻松地交易这些资产。这种全球可访问性可以提高这些市场的整体流动性。 

透明性：由于通证化资产在链上呈现，确保了透明性和可审计的资产管理，这减少了整个系统的系统性风险，因为整个系统中的杠杆和风险可以更准确地监控。

可访问性：通过区块链基础应用实现的更易访问性，以及通过将资产的所有权碎片化使更广泛的用户能够使用原本无法获得的资产类型，通证化 RWAs 可以扩大某些资产类型潜在的用户群。

如何通证化现实世界资产？
通证化现实世界资产的过程涉及多个步骤：

资产选择：确定要通证化的现实世界资产。

通证规范：确定通证的类型（同质化或非同质化）、要使用的代币标准（如ERC20、ERC721或ERC1155）以及其他代币的基本特征。 

区块链选择：选择在哪个公共或私有区块链网络上发行通证。集成Chainlink跨链互操作性协议（CCIP）有助于将代币化的RWA在任何区块链上实现可用。

链下连通：大多数通证化资产需要来自安全可靠预言机服务的高质量链下数据。使用可信的验证机制来确认支持 RWA 代币的资产对于保持用户透明度至关重要。这通常涉及使用信誉良好的服务，如Chainlink。

发行：在选定的网络上部署智能合约、铸造通证并使其可供使用。

第五步（发行）不在本次训练营的范围内。

通证化资产的分类
通证化资产可以根据三个关键特征进行分类：

资产位置：

链上资产 👈 这些不会被视为现实世界的资产。
如 ERC-20 通证和 NFT 的数字资产，本身位于区块链上。它们被创建、存储和转移都在区块链上。

链下资产
如房地产、股票或大宗商品的现实世界资产，本身位于区块链外，但通过通证化可使其在链上以数字形式表示。

抵押品位置：

链上抵押品
抵押品直接存储在链上。它们可以是加密货币或作为担保持有的通证化资产，存储在智能合约中。

链下抵押品
抵押品涉及本身存放在区块链外但通过链上通证与其相关联的现实世界资产。其可能由托管方持有或存放在传统金融机构。

支持类型：

直接支持
通证化资产直接代表对底层资产的所有权或权利主张。每个代币对应现实世界资产的具体部分。

间接支持, 又称 合成支持
通证的价值来源于底层资产，但并不直接代表所有权，而是追踪资产价值以反映其价值。

# 2025-08-20

ABI (Application Binary Interface，应用二进制接口)是与以太坊智能合约交互的标准。数据基于他们的类型编码；并且由于编码后不包含类型信息，解码时需要注明它们的类型。

函数参数编码为字节码

接受一个合约地址和一段字节码， 调用该合约并传递字节码，如果调用成功则不做任何操作，否则抛出异常。

function test(address _contract, bytes calldata data) external {
    (bool ok, ) = _contract.call(data);
    require(ok, "call failed");
}
接受一个地址和一个整数，返回一个ABI编码后的字节数组， 该字节数组包含了一个名为"transfer"的函数签名，以及传递给该函数的地址和整数参数。

function encodeWithSignature(
    address to,
    uint amount
) external pure returns (bytes memory) {
    // 拼写错误未被检查 - "transfer(address, uint)"
    return abi.encodeWithSignature("transfer(address,uint256)", to, amount);
}
该字节数组使用 IERC20.transfer.selector 作为函数选择器，将 to 地址和 amount 数量编码为参数。 这个函数的目的是编码一个 ERC20 转账操作的 ABI。

function encodeWithSelector(
    address to,
    uint amount
) external pure returns (bytes memory) {
    // 类型错误未被检查 - (IERC20.transfer.selector, true, amount)
    return abi.encodeWithSelector(IERC20.transfer.selector, to, amount);
}
函数的目的是返回一个 bytes 类型的值，这个值是通过调用 abi.encodeCall 函数来生成的。 abi.encodeCall 函数将一个函数调用打包成一个字节数组，可以用于在以太坊上进行外部函数调用。

function encodeCall(address to, uint amount) external pure returns (bytes memory) {
    // 拼写错误和类型错误将无法编译
    return abi.encodeCall(IERC20.transfer, (to, amount));
}

# 2025-08-19

定义了一组通用接口，使得钱包、交易所、DApp 等可以统一地与代币交互。
标准函数
totalSupply() 返回代币总供应量
balanceOf(address account) 查询某地址的代币余额
transfer(address to, uint256 value) 发送代币到另一个地址
approve(address spender, uint256 value) 授权第三方地址可以花费自己的代币
allowance(address owner, address spender) 查看第三方地址还可以花费多少
transferFrom(address from, address to, uint256 value) 第三方地址代表 from 向 to 转账（前提是已授权）
标准事件
event Transfer(address indexed from, address indexed to, uint256 value) 代币转账事件
event Approval(address indexed owner, address indexed spender, uint256 value)
本节实战的内容与ERC20的关系

ERC20是一个代币协议，他已经被官方实现了，在真实项目中我们基本上都是继承他来拿到我们自己的代币就可以了；
本节实战的目的是在不使用ERC20协议的情况些，自己写代码实现ERC20的基本功能。
重点内容：

除了原生代币之外的其他币种转账，基本都是对合约内部的balances进行地址的加减而已
原生币（ETH、BNB）使用 transfer / send / call 发送
合约代币ERC20 / BEP20 等调用合约的 transfer 函数即可（内部其实就是加减balances）
授权余额的mapping组织形式是这样的：
mapping (address => mapping (address => uint256)) allowances;
解释：代币所有人 => 被批准使用人 => 使用代币数量
题目1
编写 ERC20 token 合约, 实现以下功能：

设置 Token 名称（name）："BaseERC20"

设置 Token 符号（symbol）："BERC20"

设置 Token 小数位decimals：18

设置 Token 总量（totalSupply）:100,000,000

允许任何人查看任何地址的 Token 余额（balanceOf）

允许 Token 的所有者将他们的 Token 发送给任何人（transfer）；转帐超出余额时抛出异常(require),并显示错误消息 “ERC20: transfer amount exceeds balance”。

除了原生代币之外的其他币种转账，基本都是对合约内部的balances进行地址的加减而已；
允许 Token 的所有者批准某个地址消费他们的一部分Token（approve）

代币所有人 => 被批准使用人 => 使用代币数量
允许任何人查看一个地址可以从其它账户中转账的代币数量（allowance）

允许被授权的地址消费他们被授权的 Token 数量（transferFrom）；

转帐超出余额时抛出异常(require)，异常信息：“ERC20: transfer amount exceeds balance”

转帐超出授权数量时抛出异常(require)，异常消息：“ERC20: transfer amount exceeds allowance”。

# 2025-08-18

在 Currency.sol 的开始部分，我们就可以看到如下代码:

type Currency is address;

using {greaterThan as >, lessThan as <, greaterThanOrEqualTo as >=, equals as ==} for Currency global;
using CurrencyLibrary for Currency global;

function equals(Currency currency, Currency other) pure returns (bool) {
    return Currency.unwrap(currency) == Currency.unwrap(other);
}

function greaterThan(Currency currency, Currency other) pure returns (bool) {
    return Currency.unwrap(currency) > Currency.unwrap(other);
}

function lessThan(Currency currency, Currency other) pure returns (bool) {
    return Currency.unwrap(currency) < Currency.unwrap(other);
}

function greaterThanOrEqualTo(Currency currency, Currency other) pure returns (bool) {
    return Currency.unwrap(currency) >= Currency.unwrap(other);
}
此处我们首先使用了 type Currency is address; 为 address 类型创建别名。然后使用 using A for B 的语法为 Currency 类型增加了一些特性。需要注意的，此处使用了操作运算符的定义，所以在最后增加了 global 关键词。

之后的代码中出现了 Currency.unwrap 函数，该函数也是新版本 solidity 为用户自定义类型增加的新特性。在较新版本的 solidity 中，假如用户定义了 type C is V ，其中 C 是用户自定义类型，而 V 是 solidity 提供的原始类型，那么用户可以使用 C.wrap 将 V 类型转化为 C 类型，也可以使用 C.unwrap 将 V 类型转化为 C 类型。使用这种方法的好处是，solidity 会提供默认的类型检查，避免错误的类型转化发生。

Uniswap v4 此处的代码就显示了在新版本 solidity 下，开发者该如何定义自己的类型以及为类型重载运算符。如果读者希望进一步了解相关内容，可以参考 User-defined Value Types 部分的文档。

在 CurrencyLibrary 内部，Uniswap V4 定义了以下函数：

function toId(Currency currency) internal pure returns (uint256) {
    return uint160(Currency.unwrap(currency));
}

// If the upper 12 bytes are non-zero, they will be zero-ed out
// Therefore, fromId() and toId() are not inverses of each other
function fromId(uint256 id) internal pure returns (Currency) {
    return Currency.wrap(address(uint160(id)));
}
这两段函数较为简单，但注释指出 fromId() 和 toId() 并不是完全互逆的。这是因为假如用户给出了一个特殊的 id，比如给定 0x1000000000000000000000001f9840a85d5af5bf1d1762f925bdaddc4201f984 的 ID，那么由于 uint160(id) 会截断大于 160 bit 的部分，所以最终返回的结果会是 0x1f9840a85d5af5bf1d1762f925bdaddc4201f984 代币地址。所以存在多个 ID 对应同一种代币的情况。故此，Uniswap v4 注释内指出 fromId 和 toId 并不是完全互逆关系。

ERC 7751 和 Custom Revert
为了优化异常的抛出，Uniswap v4 引入了 ERC 7751 协议，并编写了 CustomRevert 库来处理 Revert。所谓的 ERC 7751 是一种带有上下文的错误抛出方法。一个简单的应用场景是当 Uniswap V4 出现报错后，在之前的情况下，我们不知道是 Uniswap v4 的主合约给出的报错还是因为 ERC20 代币实现给出的报错，我们只能通过 trace 的方式发现调用出错的位置。但 ERC 7751 允许我们给出报错的合约地址以及相关的上下文，这可以使得开发者不在 trace 的情况下就可以快速判断问题。

上述功能在 src/libraries/CustomRevert.sol 内的 bubbleUpAndRevertWith 函数内进行了实现:

/// @notice bubble up the revert message returned by a call and revert with a wrapped ERC-7751 error
/// @dev this method can be vulnerable to revert data bombs
function bubbleUpAndRevertWith(
    address revertingContract,
    bytes4 revertingFunctionSelector,
    bytes4 additionalContext
) internal pure {
    bytes4 wrappedErrorSelector = WrappedError.selector;
    assembly ("memory-safe") {
        // Ensure the size of the revert data is a multiple of 32 bytes
        let encodedDataSize := mul(div(add(returndatasize(), 31), 32), 32)

        let fmp := mload(0x40)

        // Encode wrapped error selector, address, function selector, offset, additional context, size, revert reason
        mstore(fmp, wrappedErrorSelector)
        mstore(add(fmp, 0x04), and(revertingContract, 0xffffffffffffffffffffffffffffffffffffffff))
        mstore(
            add(fmp, 0x24),
            and(revertingFunctionSelector, 0xffffffff00000000000000000000000000000000000000000000000000000000)
        )
        // offset revert reason
        mstore(add(fmp, 0x44), 0x80)
        // offset additional context
        mstore(add(fmp, 0x64), add(0xa0, encodedDataSize))
        // size revert reason
        mstore(add(fmp, 0x84), returndatasize())
        // revert reason
        returndatacopy(add(fmp, 0xa4), 0, returndatasize())
        // size additional context
        mstore(add(fmp, add(0xa4, encodedDataSize)), 0x04)
        // additional context
        mstore(
            add(fmp, add(0xc4, encodedDataSize)),
            and(additionalContext, 0xffffffff00000000000000000000000000000000000000000000000000000000)
        )
        revert(fmp, add(0xe4, encodedDataSize))
    }
}
上述代码使用了 yul 汇编完成，虽然看上去很复杂，但实际上很简单。上述代码本质上是抛出了以下错误的 ABI 编码版本:

error WrappedError(address target, bytes4 selector, bytes reason, bytes details);
在 bubbleUpAndRevertWith 函数内，details 只是一个 bytes4 additionalContext 类型。上述代码本质上是在完成了 WrappedError 的 ABI 编码工作。我们可以分部分来研究上述代码的功能:

第一部分是用来计算调用合约的返回值占据的字节数:

let encodedDataSize := mul(div(add(returndatasize(), 31), 32), 32)
在 WrappedError 内，我们会给出调用其他合约返回的报错内容，这部分报错内容都存储在 call 之后的返回值内，我们可以通过 returndatasize 获得这部分错误返回值的大小。由于 solidity 内都以 32 bytes 作为基础单位，所以此处我们需要将 returndatasize 计算为 32 的倍数。具体逻辑实现上，我们首先增加将 returndatasize 增加 31 ，这是为了处理 returndatasize = 1 这种情况。即使 returndatasize = 1，使用上述代码仍可以计算出 32 的大小。其他的先除(div )后乘(mul) 是一种典型的将每一个数字重整为另一个数字倍数的方法。我们可以在 Uniswap v3 内的 tickSpacingToMaxLiquidityPerTick 函数内看到类似的操作。

第二部分使用 let fmp := mload(0x40) 语句获取当前 solidity 的空闲内存地址的起点。solidity 编译器使用了动态的内存布局方案，每次使用或者释放内存后，都会修改 0x40 内的数据，将其指向当前未使用的空闲内存的起点。在大部分内存安全的代码方案内，我们都会 mload(0x40) 读取空闲内存起点，避免后续占用到已被其他函数使用到的内存造成安全问题。

第三部分用来写入 WrappedError 错误选择器以及报错的合约地址。

mstore(fmp, wrappedErrorSelector)
mstore(add(fmp, 0x04), and(revertingContract, 0xffffffffffffffffffffffffffffffffffffffff))
与 solidity 定义的 log 方法一致，solidity 内抛出错误实际上也会计算错误的选择器，然后将其作为最初的 4 bytes。此处的 wrappedErrorSelector 就首先被写入了内存，然后跳过 wrappedErrorSelector 占据的最初 4 bytes(add(fmp, 0x04) 就是跳过 wrappedErrorSelector 占据的字节)，我们接下来需要写入给出报错的地址。根据 solidity 的彬吗规范，给出报错的地址应该占据 256 bit。此处使用 and(revertingContract, 0xffffffffffffffffffffffffffffffffffffffff)) 清理了 revertingContract 变量可能存在的高位垃圾，然后将其写入内存。

此时的内存结构如下:

wrappedErrorSelector (4 bytes) + revertingContract(32 bytes)
第四部分写入出现报错的 selector 和错误函数的返回值。比如 Uniswap V4 调用 ERC20 代币的 transfer 失败，那么此处就写入 transfer 的选择器和返回的错误信息:

mstore(
    add(fmp, 0x24),
    and(revertingFunctionSelector, 0xffffffff00000000000000000000000000000000000000000000000000000000)
)
上述代码完成了 selector 的写入，因为 selector 的类型是 bytes4，所以此处直接写入内存即可，但注意 bytes4 在 ABI 编码后会转化为 uint256 。我们需要计算写入时需要跳过的内存长度。我们需要跳过 wrappedErrorSelector (4 bytes) + revertingContract(32 bytes) 的内存结构，该结构的长度为 36(0x24)，所以我们此处使用 add(fmp, 0x24) 确定了起始位置，然后写入了 revertingFunctionSelector，此处也需要注意使用与0xffffffff00000000000000000000000000000000000000000000000000000000 进行 and 操作去掉其他垃圾位。

完成上述操作后，内存结构如下:

wrappedErrorSelector (4 bytes) 
+ revertingContract(32 bytes) 
+ revertingFunctionSelector(32 bytes)
第五部分内，我们写入动态类型的 offset。因为我们最后写入的 reason 和 detail 都是 bytes 类型，该类型要求写入动态类型的起始位置。在后文内，我们称之为 reason offset 和 detail offset 。代码如下：

// offset revert reason
mstore(add(fmp, 0x44), 0x80)
// offset additional context
mstore(add(fmp, 0x64), add(0xa0, encodedDataSize))
此处，我们首先确定 reason offset 的写入位置 ，我们目前的内存已占用了 4 + 32 + 32 = 68，所以使用 add(fmp, 0x44) 跳过之前已写入的数据。而关于 bytes 动态类型的起始位置计算则需要排除 wrappedErrorSelector (4 bytes)，这一点额外重要，即计算动态类型的 offset 不需要包括选择器的占用部分。所以计算获得的 reason offset 应该为 revertingContract(32 bytes) + revertingFunctionSelector(32 bytes) + reason offset(32 bytes) + details offset(32 bytes)。我们使用 mstore(add(fmp, 0x44), 0x80) 写入 reason offset。

接下来，我们需要 detail offset，该变量的写入位置为 wrappedErrorSelector (4 bytes) + revertingContract(32 bytes) + revertingFunctionSelector(32 bytes) + reason offset(32 bytes) = 0x64，而写入的数值是 add(0xa0, encodedDataSize)。该数值中的 0xa0 包含以下部分 revertingContract(32 bytes) + revertingFunctionSelector(32 bytes) + reason offset(32 bytes) + reason length(32 bytes) + detail offset(32 bytes) = 160，除此之外还包含 encodedDataSize 部分。

在具体的编码过程中，我们往往最后确认 offset 的数值，所以动态类型的 offset 并没有非常难以确认

上述代码编写完成后，内存布局如下:

wrappedErrorSelector (4 bytes) 
+ revertingContract(32 bytes) 
+ revertingFunctionSelector(32 bytes)
+ reason offset(32 bytes)
+ detail offset(32 bytes)
接下来，我们可以写入 reason 的真实内容。此处需要注意写入 bytes 类型的内容，需要在原有内容前增加长度信息。我们先完成 reason 的长度写入然后完成具体的内容写入:

// size revert reason
mstore(add(fmp, 0x84), returndatasize())
// revert reason
returndatacopy(add(fmp, 0xa4), 0, returndatasize())
此处使用 returndatacopy 将失败的调用的返回结果写入内存。读者可以自行阅读 文档 获得 returndatacopy 的参数。

完成上述操作后内存布局为:

wrappedErrorSelector (4 bytes) 
+ revertingContract(32 bytes) 
+ revertingFunctionSelector(32 bytes)
+ reason offset(32 bytes)
+ detail offset(32 bytes)
+ reason length(32 bytes)
+ return data(encodedDataSize)
最后，我们写入 detail 的内容。Uniswap v4 约定 detail 是一个 bytes4 additionalContext 参数，所以长度确定为 4。相关代码如下:

// size additional context
mstore(add(fmp, add(0xa4, encodedDataSize)), 0x04)
// additional context
mstore(
    add(fmp, add(0xc4, encodedDataSize)),
    and(additionalContext, 0xffffffff00000000000000000000000000000000000000000000000000000000)
)
此处也使用了 and 去掉垃圾位。完成上述所有步骤后，我们的内存布局如下:

wrappedErrorSelector (4 bytes) 
+ revertingContract(32 bytes) 
+ revertingFunctionSelector(32 bytes)
+ reason offset(32 bytes)
+ detail offset(32 bytes)
+ reason length(32 bytes)
+ return data(encodedDataSize)
+ detail length(32 bytes)
+ detail data(32 bytes)
完成上述所有工作后，我们直接使用 revert(fmp, add(0xe4, encodedDataSize)) 抛出报错。代码内的 0xe4 实际上就是除了 return data 外，其他固定长度部分之和。

由于上述代码最后使用 revert 结束，所以此处我们没有进行内存的清理工作，但其他函数内，开发者如果使用内联汇编操作内存，应当考虑内存的清理工作。

在 CurrencyLibrary 库内，我们使用了上述函数报错 transfer 的错误：

if (!success) {
    CustomRevert.bubbleUpAndRevertWith(
        Currency.unwrap(currency), IERC20Minimal.transfer.selector, ERC20TransferFailed.selector
    );
}
考虑到文章的长度，此处不会详细介绍 CurrencyLibrary 内给出的 transfer 函数的构造方法，读者可以自行阅读 yul 源代码。该函数最后就使用了以下代码清理内存:

// Now clean the memory we used
mstore(fmp, 0) // 4 byte `selector` and 28 bytes of `to` were stored here
mstore(add(fmp, 0x20), 0) // 4 bytes of `to` and 28 bytes of `amount` were stored here
mstore(add(fmp, 0x40), 0) // 4 bytes of `amount` were stored here

# 2025-08-16

借贷平台 (DeFi Lending Platform) 项目需求文档
构建一个基于Solidity的智能合约借贷平台，为用户提供安全、透明、无需信任中介的借贷服务。

功能需求
MyERC20Token合约, 发布两个币种，用于模拟一个借贷另一个：一个 PETH ，一个 PUSDT

PETH：仿ETH
PUSDT：仿USDT（稳定币）
LPSwap合约：用于模拟 PETH-PUSDT 之间的价格变化

DefiLendingDapp合约: 用于实现一个借贷另一个

MyERC20Token合约
合约 MyERC20Token.sol 可以直接实例化出来两个合约：1）PETH：（"Pseudo-ETH", "PETH"）；2）PUSDT：（"Pseudo-USDT", "PUSDT"） 对应 contracts/MyERC20Token.sol 文件 执行：npx hardhat compile

mint：铸造，owner可用

burn：燃烧，owner可用

transfer：转账

balancesof：查看余额

LPSwap合约
添加流动性

移除流动性（要移除的流动性百分比(1-100)）

得到价格

交换

得到价格

DefiLendingDapp合约
存款

还款

抵押贷款

还贷款取回抵押

清算：当借款人的抵押资产价值下降到不足以覆盖其借款金额时，系统自动触发强制出售抵押资产以偿还债务的过程。

当抵押物价值低于清算阈值时触发
清算人可获得奖励
项目部署
本地部署脚本（测试环境）
对应 deploy/deploy_MyErc20Token.js 文件

执行：npx hardhat deploy
测试脚本
对应 test/staging/04_test_DefiLendingDapp.js 文件

执行：npx hardhat test
测试内容

验证存取逻辑
验证借款还款逻辑
验证借款清算逻辑
验证借款价格改变清算逻辑
线上部署脚本（生产环境）
对应 deploy 中的文件

执行：npx hardhat deploy --network sepolia

输出部署合约地址：

PETHToken： 0x0642a3a85EE7F9dD52B82caa7C3aE6d8F712427c
PUSDTToken： 0x1a1Dd7994A1bA16BD2a58cd076EbeA69266587D6
LPSwap： 0xa09B5a5DdED094Ba656d6416976F1DF62aA889e4
DefiLendingDapp： 0xb8026085CfcD6D0F3D06023e541466A28D5deb9B

# 2025-08-15

安全问题：面试钱包助记词被盗
让你npm install 他们的github代码，然后会在你本地的电脑上留下后门程序，攻击你的电脑。
详细推文：
A community member recently reached out after interviewing with a Web3 team claiming to be from Ukraine. In the first round, he was asked to clone a GitHub repo locally — he wisely refused.🧑‍💻

🔍Our analysis revealed the repo contains a backdoor: github[.]com/EvaCodes-Community/UltraX

💥If cloned & executed, it would load malicious code, install a malicious dependency rtk-logger@1.11.5 (created 2025-08-08), harvest sensitive browser & wallet data (e.g., Chrome extension storage, possible seed phrases, session tokens) and📤exfiltrate them to the attacker’s server.

⚠️This is a job-offer-as-a-trap scam. Stay vigilant — never run unverified code from unknown sources.

# 2025-08-14

（一）公链/Layer1项目BD案例公司类型：中型区块链技术研发公司
岗位职责：
1. 生态合作：
• 对接开发者社区，主导Grant计划资助DApp
开发团队（如Solana生态建设）。
• 推动跨链桥技术集成（如以太坊与Layer2的互操作性合作）。
2. 资源整合：
•招募验证节点运营商，设计Staking奖励模型。
• 联合举办全球黑客松活动，吸引开发者入驻。
技能侧重：
• 技术理解力：精通智能合约、跨链通信协议。
• 开发者运营：熟悉GitHub等开源社区协作工具。
交易所BD案例
公司类型：头部加密货币交易所
岗位职责：
1. 上币谈判：
• 评估代币经济模型，主导项目上线审核（如
Memecoin流动性风险把控）。
•对接做市商设计流动性挖矿方案。
2. 合规风控：
•监控SEC监管动态，调整上币策略（如应对
MiCA法案）。
技能侧重：
• 金融知识：熟悉订单簿机制、衍生品合约设计。
• 多语言能力：英语流利，可处理跨国法律文件。
（三） NFT/GameFi®项目BD案例公司类型：元宇宙平台
岗位职责：
1. IP合作：
• 谈判知名IP授权（如潮牌联名NFT发行）。
• 策划KOL营销活动，设计空投奖励机制。
2. 平台入驻：
• 推动游戏项目接入元宇宙土地系统（如
Decentraland地块合作）。
技能侧重：
• 创意策划：结合社交媒体热点设计病毒式传播方案。
• 资源网络：积累动漫、时尚领域KOL资源库。

四）DeFi协议BD案例公司类型：去中心化借贷协议
岗位职责：
1. 技术集成：
•主导与MetaMask°等钱包的API对接，优化用户授权流程。
• 设计流动性激励模型（如动态调整APY吸引
LP）。
2. 安全合作：
•协调Certik®等审计机构完成智能合约漏洞排查。
技能侧重：
• 经济模型设计：精通代币释放曲线、滑点控制机制。
• 合约安全：理解重入攻击等常见风险点。
（五）DAO社区BD案例
公司类型：去中心化自治组织Q
岗位职责：
1. 治理推进：
• 组织社区投票，推动多签钱包管理方案落地。
• 调配国库资金支持生态提案（如开发者赏金
划）。
2. 资源协调：
• 管理全球志愿者团队，协调时区会议。
技能侧重：
• 共识构建：熟练使用Snapshot、Discord等治理工具。
• 抗压能力：应对代币价格波动导致的社区分歧。

# 2025-08-13

关于kolbd
“On board KOL” 是一个营销领域的术语，通常指品牌或企业将某位 **关键意见领袖（Key Opinion Leader, KOL）** 纳入其营销活动中，使其成为品牌推广的一部分。具体含义和场景如下：

---

### 1. **“On board” 的含义**
   - 直译为“登上（船、飞机等）”，在商业中引申为“加入”或“合作”。
   - **“On board KOL”** 即品牌与KOL达成合作，邀请其参与产品推广、内容创作或品牌代言。

---

### 2. **常见的合作形式**
   - **产品推广**：KOL通过社交媒体（如小红书、抖音、微博等）发布评测、种草内容。
   - **品牌代言**：KOL成为品牌长期合作伙伴，甚至担任形象大使。
   - **活动参与**：KOL出席品牌发布会、直播活动等。
   - **内容共创**：与品牌联合制作定制化内容（如短视频、图文）。

---

### 3. **为什么品牌需要 “on board KOL”？**
   - **精准触达目标群体**：KOL拥有垂直领域的粉丝，能高效传递品牌信息。
   - **提升信任度**：KOL的推荐比传统广告更易获得用户信任。
   - **扩大声量**：借助KOL的流量，快速提升品牌曝光。

---

### 4. **相关术语区分**
   - **KOL（关键意见领袖）**：在特定领域有影响力的人物（如头部博主、行业专家）。
   - **KOC（关键意见消费者）**：粉丝量较少但互动率高的真实用户，更贴近普通消费者。
   - **Influencer（网红）**：广义的社交媒体影响者，可能包括KOL/KOC。

---

### 5. **使用场景举例**
   - “我们已on board了10位美妆KOL，下月集中推广新品。”
   - “这次营销活动的关键是on board头部KOL，带动话题热度。”

---

如果需要进一步了解KOL营销策略或合作模式，可以补充具体场景哦！

# 2025-08-12

FT的英式拍卖 英式拍卖（English Auction）是一种公开拍卖方式，拍卖品从起始价格开始，由拍卖师通过不断提高价格来逐步升高，直到最后出价最高的竞标者将赢得拍卖品并支付出价。
拍卖
1. NFT的卖家部署这个合约。
2. 拍卖持续7天。
3. 参与者可以通过存入比当前最高出价更高的ETH来竞标。
4. 所有竞标者如果不是当前最高出价者，都可以撤回他们的竞标。
拍卖结束后
1. 最高出价者成为NFT的新拥有者。
2. 卖家获得ETH的最高出价。
English Auction例子合约
- ERC721 NFT 的接口
interface IERC721 {
    function safeTransferFrom(address from, address to, uint tokenId) external;

    function transferFrom(address, address, uint) external;
}
- 合约中的变量和事件
event Start();
event Bid(address indexed sender, uint amount);
event Withdraw(address indexed bidder, uint amount);
event End(address winner, uint amount);

IERC721 public nft;//ERC721 资产的接口。uint public nftId;//ERC721 资产的 ID。address payable public seller;//卖家的地址。uint public endAt;//拍卖结束的时间戳。bool public started;//标记拍卖是否已经开始。bool public ended;//标记拍卖是否已经结束。address public highestBidder;//当前最高出价者的地址。uint public highestBid;//当前最高出价。mapping(address => uint) public bids;//每个出价者的出价。
- 构造函数。 传入 ERC721 资产的地址、ID 和起始出价。
constructor(address _nft, uint _nftId, uint _startingBid) {
    nft = IERC721(_nft);
    nftId = _nftId;

    seller = payable(msg.sender);
    highestBid = _startingBid;
}
- 开始拍卖 只有卖家可以调用。 将 ERC721 资产转移到合约地址，并标记拍卖已开始。
function start() external {
    require(!started, "started");
    require(msg.sender == seller, "not seller");

    nft.transferFrom(msg.sender, address(this), nftId);
    started = true;
    endAt = block.timestamp + 7 days;

    emit Start();
}
- 出价 只有在拍卖已开始且未结束时可以调用。 要求出价高于当前最高出价。 如果当前最高出价者不为空，则将其出价退回给其余出价者。
function bid() external payable {
    require(started, "not started");
    require(block.timestamp < endAt, "ended");
    require(msg.value > highestBid, "value < highest");

    if (highestBidder != address(0)) {
        bids[highestBidder] += highestBid;
    }

    highestBidder = msg.sender;
    highestBid = msg.value;

    emit Bid(msg.sender, msg.value);
}
- 撤回出价 只有在拍卖已开始但未结束时可以调用。 将出价退回给出价者。
function withdraw() external {
    uint bal = bids[msg.sender];
    bids[msg.sender] = 0;
    payable(msg.sender).transfer(bal);

    emit Withdraw(msg.sender, bal);
}
- 结束拍卖 只有在拍卖已开始且已结束时可以调用。 将 ERC721 资产转移给最高出价者，将出价支付给卖家。 如果没有出价，则将 ERC721 资产返回给卖家。
function end() external {
    require(started, "not started");
    require(block.timestamp >= endAt, "not ended");
    require(!ended, "ended");

    ended = true;
    if (highestBidder != address(0)) {
        nft.safeTransferFrom(address(this), highestBidder, nftId);
        seller.transfer(highestBid);
    } else {
        nft.safeTransferFrom(address(this), seller, nftId);
    }

    emit End(highestBidder, highestBid);
}

# 2025-08-11

智能合约1. 取款逻辑缺陷：合约的withdraw（提款）函数，在代码执行顺序上存在缺陷：先给用户转账ETH，然后再更新该用户的内部账本余额。
2. 恶意合约的构造：攻击者创建了一个恶意的智能合约，并用它向 The DAO投资，然后发起提款请求。
3. 触发fallback函数：当The DAO合约向攻击者的恶意合约转账ETH时，会触发攻击者合约中的fallback 函数（一个在接收 ETH
时自动执行的函数）。
4. 递归调用（“重入”）：攻击者在其fallback
函数的代码里，再次调用了 The DAO的withdraw 函数。
5. 状态更新延迟：由于 The DAO合约此时还没来得及更新攻击者的余额，它错误地认为攻击者仍然有钱可提，于是再次向其转账。
6. 循环盗取：这个“转账->重入->再转账”的过程被递归地重复执行，直到耗尽Gas或达到攻击目标，从而盗取了大量资金。

# 2025-08-10

做了一些dapp

# 2025-08-09

智能合约solidity 
https://x.com/jessiewang0914/status/1953989361869197383

# 2025-08-08

是区块链的核心机制，决定了网络如何达成状态一致性和安全性。不同场景（如公链、联盟链、DeFi专用链）需要权衡去中心化、安全性、性能三大特性。以下是针对不同场景的共识算法详解，重点包括PoW/PoS的取舍和DeFi专用链（如Tendermint）的设计逻辑：

---
1. 公链场景：PoW vs PoS 的取舍
(1) PoW（Proof of Work，工作量证明）
- 代表链：比特币、以太坊（早期）。
- 核心机制：
  - 节点通过计算哈希难题（如SHA-256）竞争出块权，消耗大量算力。
  - 最长链原则：恶意节点需掌握51%算力才能篡改历史。
- 适用场景：
  - 高安全性需求：适合无需许可的公链，抗Sybil攻击能力强。
  - 去中心化优先：矿工分布广泛，无质押门槛。
- 缺点：
  - 能耗高：比特币年耗电量超挪威全国。
  - 吞吐量低：比特币仅7 TPS，确认慢（6区块确认≈1小时）。
(2) PoS（Proof of Stake，权益证明）
- 代表链：以太坊2.0、Cardano。
- 核心机制：
  - 节点通过质押代币获得出块权，恶意行为会导致质押金被罚没（Slashing）。
  - 随机选择验证者（如以太坊的RANDAO+VDF）。
- 适用场景：
  - 高性能需求：以太坊2.0目标TPS可达10万（分片后）。
  - 节能环保：无算力竞争。
- 缺点：
  - 富者愈富：持币大户更容易获得奖励。
  - 长程攻击风险：需通过检查点（Checkpoint）或弱主观性防御。
(3) PoW vs PoS 的取舍
暂时无法在飞书文档外展示此内容

---
2. DeFi专用链场景：Tendermint（BFT共识）
(1) 为什么DeFi需要专用共识？
- 需求：DeFi应用需要快速最终性（Finality）和高确定性（交易顺序不可逆），避免MEV抢跑或分叉风险。
- 问题：PoW/PoS的概率最终性（如比特币6区块确认）可能导致双花或重组攻击。
(2) Tendermint（BFT类共识）
- 代表链：Cosmos Hub、Binance Chain。
- 核心机制：
  - PBFT（实用拜占庭容错）变体：验证者轮流出块，需2/3以上节点签名确认区块。
  - 即时最终性：一旦区块被提交，不可回滚（无分叉）。
  - 轻客户端友好：可通过默克尔证明快速验证状态。
- 优势：
  - 低延迟：出块时间1-3秒，适合订单簿DEX（如dYdX）。
  - 确定性：交易顺序固定，减少MEV套利窗口。
- 缺点：
  - 节点数限制：通常100-150个验证者，牺牲去中心化。
  - 活跃性风险：需2/3节点在线，否则网络停滞。
(3) DeFi链的共识优化案例
- Osmosis（Cosmos SDK）：
  - 通过阈值加密隐藏交易内容，减少前端跑。
  - 区块空间拍卖：优先处理高Gas费交易，对抗MEV。
- Sei Network：
  - 并行化执行：基于交易依赖关系加速AMM结算。

---
1. 其他场景的共识选择
(1) 联盟链：PBFT/Raft
- 特点：节点身份已知，容忍1/3拜占庭节点。
- 用例：Hyperledger Fabric（金融机构间结算）。
(2) 高性能链：DAG类共识
- 代表：Solana的Tower BFT（PoH+PoS混合）。
- 优势：5万+ TPS，但需中心化硬件（如SSD服务器）。
(3) 隐私链：ZKP共识
- 代表：Mina的Ouroboros Samasika（递归零知识证明）。
- 特点：轻节点可验证整个链状态（仅22KB）。

---
2. 共识算法的关键权衡
3. 以太坊的混合共识：Casper FFG + LMD-GHOST
(1) 为什么需要混合共识？
- 问题：纯PoS可能导致长程攻击（Long-range Attack）——攻击者购买旧私钥重构历史链。
- 解决方案：结合最终确定性工具（Casper FFG）和分叉选择规则（LMD-GHOST）。
(2) Casper FFG（Friendly Finality Gadget）
- 核心机制：
  - 两阶段投票：验证者对周期（Epoch）内的区块进行prepare和commit投票。
  - 确定性条件：当2/3验证者对区块B的commit投票达成时，B被最终确定。
- 
- def is_finalized(block):    prepare_votes = get_votes(block, stage='prepare')    commit_votes = get_votes(block, stage='commit')    return len(prepare_votes) > 2/3 * total_stake and len(commit_votes) > 2/3 * total_stake  
- 惩罚条件（Slashing）：
  - 双重投票（同一高度投票给两个区块）。
  - 违反“非环绕投票”规则（投票的源区块必须递增）。
(3) LMD-GHOST（Latest Message Driven Greediest Heaviest Observed SubTree）
- 作用：在未最终确定的区块中选择“最重”的分支。
- 规则：
  - 从创世块开始，递归选择被最多验证者最新投票的子区块。
  - 权重计算：weight(block) = Σ(stake of validators whose latest vote is for block or its descendants)
(4) 攻击与防御
- 平衡攻击（Balancing Attack）：
  - 攻击者控制少量质押者，在分叉间交替投票以延迟确定性。
  - 防御：增加诚实节点的同步假设（如≥66%节点在线）。

---

# 2025-08-07

dex uniswap理解
https://x.com/jessiewang0914/status/1953437426573025775
dex：假如某个项目想要发行自己的资产供用户交易。在传统的中心化交易所，这可能需要与交易所签订繁琐的合同，并让研发人员与系统进行对接，整个流程复杂且不可靠。然而在去中心化交易所中，两步即可。1.将资产通过智能合约发布到链上；2.把资产加入去中心化交易所的流动性池，这样用户就能进行交易了。
实现去中心化交易所的关键考量
要实现一个去中心化交易所，除了用户操作页面外，智能合约是重中之重。智能合约承担着处理用户交易请求、保障交易安全与透明的重任。对于负责交易处理的智能合约而言，最关键的就是如何在合适的时间以合适的价格，确保各种资产安全完成交易。
像 Blur 这类专注于 NFT 交易的交易所，交易逻辑相对简单。卖方可以指定一个价格，授权合约进行出售，买方随后授权合约购买；或者买家出价，卖家出售，整个交易过程由智能合约保障，无需三方担保，由智能合约管理资金和资产，这就是订单簿交易所的模式。
然而，这种模式对于 FT（同质化代币）交易来说就有些力不从心了。因为 FT 交易量大，对流动性的要求极高，需要更高效的交易模式。通过订单簿进行交易，无论是存储还是撮合等操作都需要执行合约，这会消耗大量 Gas（即区块链上执行合约所需的手续费）。
所以，针对去中心化代币交易所，需要更高效的交易模式，也就是 AMM（自动化做市商）。从技术层面讲，AMM 就是智能合约要实现的交易逻辑，它负责处理用户交易请求、管理资金池、计算价格，并确保有足够的流动性来支撑交易。
简单来讲，可以通过引入流动性提供者（Liquidity Provider）来实现。这些 LP 会将一对资产存入资金池，并给出初始定价。交易者无需像 NFT 交易那样等待买家，可直接在流动性池中随时买卖，而资产价格会随着持续的交易行为而变动。LP 在这个过程中能够获得相应激励。

# 2025-08-06

https://x.com/jessiewang0914/status/1952980918219751870
Uniswap v3 core
Uniswap v3 是为以太坊虚拟机实现的非托管自动化做市商，相较于早期版本，它在资本效率、流动性提供者的控制程度、价格预言机的准确性和便利性以及费用结构灵活性等方面都有提升。核心：Concentrated Liquidity，Flexible Fees:Protocol Fee Governance:Improved Price OracleLiquidity Oracle。集中流动性（Concentrated Liquidity）：允许流动性提供者（LPs）将流动性限制在任意价格范围内（称为 “头寸”），仅需维持该范围内交易所需的储备，相当于在该范围内拥有更大的 “虚拟储备”，大幅提升资本效率。当价格超出范围时，头寸流动性不再活跃且不产生费用，仅由单一资产构成；若价格重回范围，流动性恢复活跃。LPs 可创建多个不同价格范围的头寸，灵活分配流动性。
灵活费用结构：不再固定 0.30% 的交易费率，每个资产对可对应多个池，每个池的费率在初始化时设定，初始支持 0.05%、0.30%、1% 三个等级，UNI 治理可添加更多等级。协议费用治理：UNI 治理可灵活设定协议收取的交易费用比例，且可按池单独设置。
改进的价格预言机：无需用户在计算时间加权平均价格（TWAP）时，精确记录时间段起始和结束时的累加器值，通过内置累加器 checkpoint 实现链上直接计算；同时改用对数价格累加，计算几何平均 TWAP，提升精度并减少存储需求。
流动性预言机：新增时间加权平均流动性预言机，追踪 的累加值，助力外部合约实现流动性挖矿等功能。

# 2025-08-05

https://intensivecolearn.ing/programs/Web3_Internship_Program
常见网络安全风险与攻击方式：钓鱼攻击（Phishing） 钓鱼攻击是最常见、最有效的网络攻击手段之一。攻击者通过伪造官方网站、社交账号、邮件、短信等，诱导受害者点击恶意链接、输入敏感信息（如助记词、私钥、账号密码），或下载恶意软件。伪造邮件/网站： 攻击者会仿冒知名企业、学校、项目方的邮箱或网站，发送“面试通知”“奖学金发放”“账号异常”等紧急信息，诱导你点击钓鱼链接。
社交平台钓鱼： 在微信群、QQ 群、Telegram、Discord 等社群中，冒充官方人员、HR、发布虚假招聘、空投
假冒客服/好友：冒充信任的人，诱导你转账或泄露信息

# 2025-08-04

区块链基础知识：
https://x.com/jessiewang0914/status/1952239481773514932
a blockchain is a distributed database
区块：存储交易记录     链：存储上一个区块的哈希
去中心化，不可篡改，公开透明
去中心化的网络由无数节点提供服务来维持网络运行，这些操作统称为挖矿。维持这些服务的人一般称之为矿工。区块链生态系统的运行包含以下几个关键步骤：
用户发起交易：用户通过钱包应用发起转账、智能合约调用等操作
交易广播：交易信息被广播到整个网络中的各个节点
节点验证：网络中的矿工节点验证交易的合法性（余额是否足够、签名是否正确等）。打包成块：通过共识机制（如工作量证明），矿工将验证过的交易打包成新的区块
链接上链：新区块被添加到区块链上，更新全网的账本状态
奖励发放：成功打包区块的矿工获得代币奖励和交易手续费。公链 vs 私链 vs 联盟链
公链（Public Blockchain） = 公共公园。公园里没有管理员，所有规则由大家共同制定。成为节点的方法：
无需申请，自由进出。所有人可见，去中心化（共识机制）。缺点：因为人太多，决策效率低（交易确认慢），维护成本高（比如电费、人力）联盟链（Consortium Blockchain） = 多公司联合的董事会。成为节点的方法：需要邀请或申请。权限分级：董事会成员可能分为两类：决策者（联盟核心成员）：比如银行 A、银行 B，可以修改数据库规则。
观察者（联盟普通成员）：比如物流公司，只能查看数据但不能修改。共同管理数据的模式：半公开数据：数据库里的信息（比如客户信用评分）只有董事会成员能看到，外人无法访问。 联合决策：如果要修改数据库规则（比如增加新字段），需要董事会成员投票通过。 优点：效率比公链高（因为成员少），隐私比公链好（数据不对外公开），但不如私链灵活（需要多方协调）。

# 2025.07.30


<!-- Content_END -->
