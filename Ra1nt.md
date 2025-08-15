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
# 2025-08-15

<aside>
💡

本来不是只需要搭建本地节点的吗，为什么如今要搭建整个测试网呢，有点不理解，也许是为了更好的熟悉整个流程。

</aside>

---

### 前言

docker 在本地和在 ubuntu 上不一样，我以为在本地有 docker 使用 ubuntu 运行显示没有 docker ,我的理解是 ubuntu 和 docker 都是虚拟机，应该是平行的存在，所以应该能够互相检测到？后面在 ubuntu 上安装了 docker.cli 之后就解决了。

---

先了解工具：

### Kurtosis

**Kurtosis** 是一个**基于 Docker 的测试环境编排工具**，专门用于构建复杂的分布式网络环境，例如区块链网络、微服务、数据库集群。

它的核心作用是：

1. **快速启动多节点网络**
    - 比如一个包含 Geth 节点 + Lighthouse 节点 + 交易生成器的以太坊测试网。
2. **沙盒隔离（Enclave）**
    - 每个 enclave 相当于一个独立的 Docker 网络，所有节点容器相互隔离。
3. **自动依赖管理**
    - 自动拉取 Docker 镜像、挂载卷、设置网络。
4. **可视化和监控**
    - 提供 metrics、日志收集，方便调试和性能测试。

---

### Docker

**Docker** 是一个**容器化平台**，主要功能是把应用和它的运行环境打包成一个“容器”，保证在任何环境下都能一致运行。

### 核心概念：

- **镜像（Image）**：程序和依赖打包后的模板。
- **容器（Container）**：镜像的运行实例。
- **Docker Hub**：公共镜像仓库，类似应用商店。
- **隔离性**：每个容器相互独立，类似轻量虚拟机。
- **跨平台**：Linux、Windows、Mac 都可以运行相同容器。

> **Docker 是底层容器平台**，而 **Kurtosis 是在 Docker 上的“智能管理层”**，专门帮你快速搭建和管理复杂网络环境。
> 

---

### 具体参考

- [https://lxdao.notion.site/24bdceffe40b80148b07fa28483662cf](https://www.notion.so/24bdceffe40b80148b07fa28483662cf?pvs=21)

> 总体来说，我对于这一部分的知识还是不知甚解，搭建了这个测试网，类似于主网，是为了更好的开发。这里涉及很多新的概念和工具。
> 

> 什么是执行层，验证层，共识层然后还有交互
geth , lighthouse , grafana
> 

---

运行一下命令来创建一个虚拟环境 (或者说隔离环境）：

```python
kurtosis run --enclave eth-dev github.com/ethpandaops/ethereum-package --args-file network_params.yaml
```

一些常用的命令：

```bash
kurtosis enclave ls //列出正在运行的环境
//当然，当你不熟悉的时候，可以使用 enclavre --help 来获取相应的函数，即查手册
```

在当前环境使用完毕后，你可以选择停止该环境或者销毁该环境：

```python
kurtosis enclave stop eth-dev//停止，eth-dev 是指正在运行的环境
kurtosis enclave destroy eth-dev//删除

```

搭建好了，但是我不知道目前是个什么东西。

# 2025-08-14

# 一些思考和疑问

看到 blue 助教的笔记，哇，感觉 web3 好多新的东西

首先，以太坊和 chainlink 不一样的，然后还有合约，以太，比特币，区块链的概念等等。现在有点乱了，来整理一下，顺便再次加深理解。感觉概念太多了，着实有点混乱了。

### 什么是web3

- Web3 是“去中心化互联网”的概念，核心思想是把 **数据与控制权从中心化公司转回用户手中**。
- 在 web2 中，数据主要存储在中心化数据库中，由单一实体控制，如银行的交易记录就存储在其服务器中；而 web3 中，数据存储在去中心化数据库中，分布式账本是一个存储在多个节点上的共享数据库。它通过去中心化的方式记录数据，确保数据信息的透明性和不可篡改性。
    - **wbe2**：中心化、利益独享、平台中心化治理
    - **web3**：数据主权回归用户所有，去中心化治理，所有人都可以在平台上进行共建和共享。

---

### 以太坊

- 第二代区块链，不仅支持加密货币（ETH），还可以运行 **智能合约（Smart Contracts）**
- **智能合约**：
    - 程序化合约，自动执行约定好的逻辑。
    - 不依赖第三方机构。
    - 举例：假设你和别人约好“如果我付 1 ETH，你自动得到数字资产 X”，以太坊上的智能合约就可以自动执行。
- **区别于比特币**：比特币主要是“钱”，以太坊是“钱+程序”。

---

### Chainlink（LINK）

- **本质**：去中心化预言机网络（Decentralized Oracle）。
- **作用**：
    - 把链外数据（现实世界数据）安全可靠地传输到区块链上。
    - 举例：智能合约要根据天气数据或股价触发交易，Chainlink 提供可信的数据源。
- **区别于以太坊**：以太坊是平台/区块链，Chainlink 是链上数据与链外世界的桥梁。

---

### 智能合约（Smart Contract）

- **本质**：部署在区块链上的程序。
- **特点**：
    - 自动执行：满足条件就执行，无需人为干预。
    - 不可篡改：部署后不能随意修改。
    - 可组合：可以调用其他智能合约，实现复杂逻辑。
- **举例**：
    - 去中心化交易所（DEX）上的交易逻辑。
    - NFT 铸造逻辑。
    - DeFi 借贷协议。

---

### 以太（ETH）

- **本质**：以太坊网络的原生代币。
- **用途**：
    - 支付交易手续费（Gas）。
    - 激励矿工/验证者维护网络。
    - 可以作为资产投资。

---

> 更多的基本概念就不在这里赘述了，在 blue 助教的 github 上有着不错的介绍
大家可以自行前往 [https://github.com/B1u-e/My-Web3-Journey/blob/main/基础知识速通版/区块链基础知识速通.md#web2-与-web3-的区别](https://github.com/B1u-e/My-Web3-Journey/blob/main/%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86%E9%80%9F%E9%80%9A%E7%89%88/%E5%8C%BA%E5%9D%97%E9%93%BE%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86%E9%80%9F%E9%80%9A.md#web2-%E4%B8%8E-web3-%E7%9A%84%E5%8C%BA%E5%88%AB)
> 

### EVM

**EVM = Ethereum Virtual Machine（以太坊虚拟机）**

它是 **运行以太坊智能合约的核心运行环境**。

- **位置**：存在于每个以太坊节点里（全网一致）
- **作用**：接收交易 → 执行智能合约代码 → 更新区块链状态
- **语言**：最终执行的是 **EVM 字节码**（类似计算机 CPU 执行机器码）

💡 类比：

如果 **以太坊区块链** 是一台全球共享的计算机，

那么 **EVM** 就是它的“CPU + 操作系统”，专门运行部署在上面的程序（智能合约）。

# 2025-08-13

创建dashboard
## 添加Bot

这一块比较简单，自己创建一个both还是比较容易的

值得注意的是，添加一个自定义的bot，然后获取 api

### 创建 python 虚拟环境

为了方便管理，养成一个项目一个 python 环境的好习惯

这里使用 venv 来管理虚拟环境，使用步骤如下（ ubuntu 中）：

```python
python3 -m venv venv//创建虚拟环境
source venv/bin/activate//激活
deactivate//退出
遗憾的是，venv没有统一的查看所有虚拟环境的函数，建议使用conda
```

![image.png](attachment:67fdaac0-85a0-4051-ba7a-870860245ca4:image.png)

# 2025-08-12

https://mica-antimony-f2d.notion.site/OpenZeppelin-24c098ef862280c68d2bca20e7c647b8

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
