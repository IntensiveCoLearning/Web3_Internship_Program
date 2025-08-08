---
timezone: UTC+8
---

# K9z4g0d

**GitHub ID:** Kgzsgod

**Telegram:** @kgzsgod

## Self-introduction

大家好，我是K9z4g0d，山东马上大四，会点js和python，目前在学习solidity、ethers.js，对web3有着浓厚的兴趣，希望可以就此机会，找到相关的工作

## Notes

<!-- Content_START -->
# 2025-08-08

差不多算是走了一遍ethers.js了！以后如果用到的话，碰到不会的可以在学，查漏补缺！

- MerkleTree脚本
- 数字签名脚本
- 监听Mempool
- 解码交易详情

之前学完了wtf的solidity的0x101和0x102,打算后面复习一下，然后学习一下进阶0x103

# 2025-08-07

# 8.7

ethers.js 依旧/.

- StaticCall
- 识别ERC721合约
- calldata
- 批量生成钱包
- 批量转账

# 2025-08-06

依旧ether.js
依旧wtf的ethers101
- 合约交互
- 部署合约
- 检索事件
- 监听合约事件
- 事件过滤


之前在没有学ethers.js的情况下，直接跟着chainlink的frank老师学hardhat框架，发现学起来有点困难，所以打算从0开始学一学

# 2025-08-05

# ethers.js

https://www.wtf.academy/zh/course/ethers101/ReadContract

基于wtf的学习网站，使用ethers.js学习了以下内容

- 查询链上的基本信息
- provider
- 读取合约信息
- 发送eth

同时还复习了会javascript，做了两道算法题

# 2025-08-04

# 区块链基础概念

https://web3intern.xyz/zh/blockchain-basic/

根据发的文档重温区块链的相关概念，主要围绕不易理解的点做一个简要笔记

### 区块信息

一个区块包含二个主要数据：前一个区块的哈希，交易记录信息

在所有交易记录完成后，将所有的交易记录和前一个区块的哈希做一个运算并打包，得到当前区块的hash，这个hash也是下一个区块的pre hash

### 链

一条链只会连接两个区块，多个区块就组成了区块链，按照产生区块的顺序依次串联起来

> 数据结构中的单链表

### 区块链特性

- 不可篡改：篡改一个区块中的数据，就必须要修改后面所有的区块信息
- 公开透明：每个交易都在区块链浏览器中可以查到，只要知道一个地址，顺着交易信息进行查找，可以知道地址持有的金额
- 匿名：每个地址代表一个人的身份，没有人知道地址后的人是谁
- 便捷交易：无论两人身处何地，都可以快速的进行交易，比传统的跨国转账方便的多

### 公链、联盟链、私链

- 公链：所有用户都可以自由进出，没有任何权限，所有人都是平等的，符合区块链的去中心化理念
- 联盟链：几个人达成合作，想要进入需要这几个人的同意，常用于商业协作
- 私链：由一个人控制，想要进入的人必须经过这个人的同意，常用于企业内部管理，类似于web2中的银行内网

# 安全转账

https://unphishable.io/challenges

做完了初级的安全挑战，开始只为了拿学分，做的时候发现这个里面的安全知识挺重要的，其中有两个比较有意思的我也记录下来

### 剪贴板钓鱼

https://unphishable.io/challenges/clipboard-phishing?lang=zh-hans

这个打开的页面是不是不可以上传图片呀？

**场景描述**：您需要转账 1 ETH 到朋友的钱包。他们已经分享了他们的钱包地址，您正在使用加密货币转账界面进行转账。

**与朋友聊天**：嘿！这是我的钱包地址，用于接收你承诺的 ETH：0x...

这里有意思的来了，在这串地址旁边有一个复制按钮，出于方便的原理，大部分人(包括我)肯定会选择单机这个复制按钮，而不是拖动鼠标全选地址然后复制

这个复制按钮就是攻击者写好了的攻击脚本，实际复制的已经变成了攻击者要替换的地址了

通过这个小练习，以后要转账一定不要偷懒，用鼠标拖动复制地址！！！

### USDC Permit 钓鱼模拟

https://unphishable.io/challenges/permit-phishing

这个模拟主要展示了钓鱼者如何使用签名请求usdc授权

在使用自己的地址进行签名授权时，不要完全信任陌生的地址

```javascript
{
  "domain": {
    "name": "USD Coin",
    "version": "2",
    "chainId": 17000,
    "verifyingContract": "0x74A4A85C611679B73F402B36c0F84A7D2CcdFDa3"
  },
  "types": {
    "Permit": [
      {
        "name": "owner",
        "type": "address"
      },
      {
        "name": "spender",
        "type": "address"
      },
      {
        "name": "value",
        "type": "uint256"
      },
      {
        "name": "nonce",
        "type": "uint256"
      },
      {
        "name": "deadline",
        "type": "uint256"
      }
    ]
  },
  "message": {
    "owner": "0x17d96F5E966874FB944931Eb05feFE0FC0BFbE1e",
    "spender": "0x1234567890123456780012345678901234567890",
    "value": "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
    "nonce": 93759,
    "deadline": 1756891785
  },
  "signature": "0x3b94fbf86aa2df2b8559761c21d8531e419e42e4ed3d66bf56fc3da0abef57f44bfc0fab7554c938bf3fd3332289eee60e5d6dfd37a78ecfdb83d3f6fd30dd561b",
  "r": "0x3b94fbf86aa2df2b8559761c21d8531e419e42e4ed3d66bf56fc3da0abef57f4",
  "s": "0x4bfc0fab7554c938bf3fd3332289eee60e5d6dfd37a78ecfdb83d3f6fd30dd56",
  "v": 27
}
```

如果用自己存有usdc的地址签名了这个地址，0x1234567890123456780012345678901234567890这个地址会一直调用owner也就是刚签名的地址的usdc余额

所以在签名陌生的地址的时候，建议先使用没有u或者u很少的地址尝试一下，别直接用自己经常使用的地址签名


# 2025.07.30


<!-- Content_END -->
