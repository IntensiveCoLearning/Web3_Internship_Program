---
timezone: UTC+8
---

# 黄昌盛

**GitHub ID:** Ra1nt

**Telegram:** @1nt

## Self-introduction

小白学习中，爱好健身

## Notes

<!-- Content_START -->
# 2025-08-11

延续昨天的内容：
### 预防溢出

上溢和下溢

- 无论是哪种溢出，都会导致安全问题，类似时钟由23:59→0:00;
- 为了防止这些情况，OpenZeppelin 建立了一个叫做 SafeMath 的 **_库_**(***library***)，默认情况下可以防止这些问题。
- （通常情况下，总是使用 SafeMath 而不是普通数学运算是个好主意，也许在以后 Solidity 的新版本里这点会被默认实现，但是现在我们得自己在代码里实现这些额外的安全措施）。

### 注释语法

- 相对来说，为代码添加注释是好的，这有利于你的回顾，几个月后，你不必逐行阅读代码。
- // 单行注释  /* */多行注释

Solidity 社区所使用的一个标准是使用一种被称作 ***natspec*** 的格式，看起来像这样：

```solidity
/// @title 一个简单的基础运算合约/// @author H4XF13LD MORRIS 💯💯😎💯💯/// @notice 现在，这个合约只添加一个乘法contract Math {
/// @notice 两个数相乘/// @param x 第一个 uint/// @param y  第二个 uint/// @return z  (x * y) 的结果/// @dev 现在这个方法不检查溢出function multiply(uint x, uint y) returns (uint z) {
// 这只是个普通的注释，不会被 natspec 解释
    z = x * y;
  }
}

```

`@title`（标题） 和 `@author` （作者）很直接了.

`@notice` （须知）向 **用户** 解释这个方法或者合约是做什么的。 `@dev` （开发者） 是向开发者解释更多的细节。

`@param` （参数）和 `@return` （返回） 用来描述这个方法需要传入什么参数以及返回什么值。

注意你并不需要每次都用上所有的标签，它们都是可选的。不过最少，写下一个 `@dev` 注释来解释每个方法是做什么的。

## 建造Dapp

### 引入web3.js

还记得么？以太坊网络是由节点组成的，每一个节点都包含了区块链的一份拷贝。当你想要调用一份智能合约的一个方法，你需要从其中一个节点中查找并告诉它:

1. 智能合约的地址
2. 你想调用的方法，以及
3. 你想传入那个方法的参数

以太坊节点只能识别一种叫做 ***JSON-RPC*** 的语言。这种语言直接读起来并不好懂。当

## call和send

`call` 用来调用 `view` 和 `pure` 函数。它只运行在本地节点，不会在区块链上创建事务。

> 复习: view 和 pure 函数是只读的并不会改变区块链的状态。它们也不会消耗任何gas。用户也不会被要求用MetaMask对事务签名。
> 

使用 Web3.js，你可以如下 `call` 一个名为`myMethod`的方法并传入一个 `123` 作为参数：

```solidity
myContract.methods.myMethod(123).call()
```

`send` 将创建一个事务并改变区块链上的数据。你需要用 `send` 来调用任何非 `view` 或者 `pure` 的函数。

> 注意: send 一个事务将要求用户支付gas，并会要求弹出对话框请求用户使用 Metamask 对事务签名。在我们使用 Metamask 作为我们的 web3 提供者的时候，所有这一切都会在我们调用 send() 的时候自动发生。而我们自己无需在代码中操心这一切，挺爽的吧。
> 

使用 Web3.js, 你可以像这样 `send` 一个事务调用`myMethod` 并传入 `123` 作为参数：

```solidity
myContract.methods.myMethod(123).send()

```

语法几乎 `call()`一模一样。

<aside>
💡

这里的call和send在使用的时候，是合约加methods关键词加调用的方法，最后加上call或send,

</aside>

---

理解异步：

异步操作是指当前有一个操作过于耗时，将这个操作交给后台执行，暂时执行其他操作。

在 JavaScript 里，单线程一次只能做一件事。

如果一个操作特别慢（比如网络请求、读取文件、区块链交易确认），

**同步** 写法会让整个程序卡住，用户体验很差。

所以 JavaScript 引入了**异步执行**：把耗时任务交给浏览器或 Node.js 的后台线程去做，

主线程继续执行别的代码，等任务完成了再通知你。

Web3中解决异步的方式：

JavaScript 的 **Promise** 是一种用于**处理异步操作**的对象，它的作用是**承诺**将来会返回一个结果（成功或失败），并且避免了传统回调函数（callback）容易出现的“回调地狱”。

## 前端展示

使用JQuery 来做一个快速示例，展示如何解析和展示从智能合约中拿到的数据。

我们只用了 JQuery，没有任何模板引擎，所以会非常丑。不过这只是一个如何展示僵尸数据的示例而已。

```solidity
// 在合约中查找僵尸数据，返回一个对象
getZombieDetails(id)
.then(function(zombie) {
// 用 ES6 的模板语法来向HTML中注入变量// 把每一个都追加进 #zombies div
  $("#zombies").append(`<div class="zombie">
    <ul>
      <li>Name: ${zombie.name}</li>
      <li>DNA: ${zombie.dna}</li>
      <li>Level: ${zombie.level}</li>
      <li>Wins: ${zombie.winCount}</li>
      <li>Losses: ${zombie.lossCount}</li>
      <li>Ready Time: ${zombie.readyTime}</li>
    </ul>
  </div>`);
});

```

> 我觉得这样的方式还挺好的，感觉是比较简单的注入
>

# 2025-08-10

在remix上完成示例hello eth合约
<aside>
💡

solidity是一种合约语言

</aside>

今天学习的内容：

### payable修饰符

- `payable` 方法是让 Solidity 和以太坊变得如此酷的一部分 —— 它们是一种可以接收以太的特殊函数。
- *注意： 如果一个函数没标记为`payable`， 而你尝试利用上面的方法发送以太，函数将拒绝你的事务。*

<aside>
💡

当存在payable标记的时候，这意味着它可以向其他合约索要费用，有了这个，便有了交易

</aside>

### 提现以太

- 在你发送以太之后，它将被存储进以合约的以太坊账户中， 并冻结在哪里 —— 除非你添加一个函数来从合约中把以太提现。类似这样

```solidity
contract GetPaid is Ownable {
	function withdraw() external onlyOwner {
		owner.transfer(this.balance);
	}
}
```

- 有很多例子来展示什么让以太坊编程如此之酷 —— 你可以拥有一个不被任何人控制的去中心化市场。

> 感觉这写只是交互的功能，但是让交易变得可能
> 

### 随机数的实现

> 首先随机数的实现目前是不安全的，这会给节点带来隐患
> 
- 使用随机数，一般是使用keccak256函数。这个方法首先拿到 `now` 的时间戳、 `msg.sender`、 以及一个自增数 `nonce` （一个仅会被使用一次的数，这样我们就不会对相同的输入值调用一次以上哈希函数了）。然后利用 `keccak` 把输入的值转变为一个哈希值, 再将哈希值转换为 `uint`, 然后利用 `% 100` 来取最后两位, 就生成了一个0到100之间随机数了。

```solidity
// 生成一个0到100的随机数:
uint randNonce = 0;
uint random = uint(keccak256(now, msg.sender, randNonce)) % 100;
randNonce++;
uint random2 = uint(keccak256(now, msg.sender, randNonce)) % 100;
```

### modifier函数

- 以_;结束
- 同时充当require功能
- 调用时添加函数修饰符

```solidity
  modifier ownerOf(uint _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    _;
  }
    function feedAndMultiply(uint _zombieId, uint _targetDna, string _species) internal ownerOf(_zombieId) {

```

## **ERC721: 转移标准**

- 注意 ERC721 规范有两种不同的方法来转移代币：

```solidity
function transfer(address _to, uint256 _tokenId) public;

function approve(address _to, uint256 _tokenId) public;
function takeOwnership(uint256 _tokenId) public;

```

1. 第一种方法是代币的拥有者调用`transfer` 方法，传入他想转移到的 `address` 和他想转移的代币的 `_tokenId`。
2. 第二种方法是代币拥有者首先调用 `approve`，然后传入与以上相同的参数。接着，该合约会存储谁被允许提取代币，通常存储到一个 `mapping (uint256 => address)` 里。然后，当有人调用 `takeOwnership` 时，合约会检查 `msg.sender` 是否得到拥有者的批准来提取代币，如果是，则将代币转移给他。

## msg.sender

`msg.sender` 并不是单纯的“当前地址”，而是指 **当前调用合约的地址**，它可能是：

1. **外部账户（EOA）地址**
    - 例如你用 MetaMask 调用智能合约时，你的钱包地址就是 `msg.sender`。
2. **合约地址**
    - 如果一个合约调用了另一个合约，那么在被调用的那个合约里，`msg.sender` 会是调用它的**合约地址**，而不是最终发起交易的人的钱包地址。

## 代币类型

### Ethereum Request for Comments 721

- **Ethereum Request for Comments 20**
- 可以理解为通用代币

### ***ERC721 代币***

- Ethereum Request for Comments 721
- ***ERC721 代币***是**不**能互换的，因为每个代币都被认为是唯一且不可分割的。 你只能以整个单位交易它们，并且每个单位都有唯一的 ID

# 2025-08-09

继续学习sodility语言

# 2025-08-07

通过下面的网站，学习Sodlity合约
https://cryptozombies.io/zh/lesson/3/chapter/12

# 2025-08-05

完成了web3安全方面的学习，主要通过https://unphishable.io/challenges/google-spoofed-phishing上的练习

# 2025-08-04

安装好了相应的工具，完成了第一个我的nft
观看下午的会议回放


# 2025.08.01


<!-- Content_END -->
