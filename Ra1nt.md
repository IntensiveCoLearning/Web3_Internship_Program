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
