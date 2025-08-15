---
timezone: UTC+8
---

# Girasol

**GitHub ID:** janebirkey

**Telegram:** @LewisCAD

## Self-introduction

我是Girasol，目前是大二专业是区块链工程，目前在一家交易所担任BD与HR实习生，主要是想学习web3产品相关的知识，之后也是想从事产品岗位，希望能够有所收获

## Notes

<!-- Content_START -->
# 2025-08-15

实践案例：如何通过 Layer 2 将文章内容上链



这是一个典型的“链上+链下”结合的解决方案，完美平衡了成本、效率和去中心化。

**流程概览：**

1. **链下处理 (客户端/服务器端)**

   - 将文章原文上传到**去中心化存储网络**，如 **IPFS** 或 Arweave，获取其内容标识符 (CID)。
   - 使用 `SHA-256` 算法计算文章原文的**哈希值**，作为文章的“数字指纹”。

   ```python
   import hashlib
   # 假设已将内容上传到 IPFS 并获得 CID
   # ipfs_cid = "Qm..." 
   
   article_content = "This is my article content."
   hash_object = hashlib.sha256(article_content.encode('utf-8'))
   article_hash = "0x" + hash_object.hexdigest()
   ```

2. **部署 Layer 2 智能合约**

   - 在 Layer 2 网络上（如 Arbitrum）部署一个智能合约，该合约用于记录文章的元数据。

   ```solidity
   // SPDX-License-Identifier: MIT
   pragma solidity ^0.8.0;
   
   contract ArticleRegistry {
       struct Article {
           address author;
           string ipfsCID;
           uint256 timestamp;
       }
   
       // 映射：文章哈希 -> 文章信息
       mapping(bytes32 => Article) public articles;
   
       event ArticlePublished(address indexed author, bytes32 articleHash, string ipfsCID);
   
       function publishArticle(bytes32 _articleHash, string memory _ipfsCID) public {
           require(articles[_articleHash].author == address(0), "Article already exists.");
   
           articles[_articleHash] = Article({
               author: msg.sender,
               ipfsCID: _ipfsCID,
               timestamp: block.timestamp
           });
   
           emit ArticlePublished(msg.sender, _articleHash, _ipfsCID);
       }
   }
   ```

3. **客户端调用合约**

   - 用户通过前端 DApp（使用 Ethers.js 等库）连接钱包，调用 L2 智能合约的 `publishArticle` 函数，传入第一步生成的 `article_hash` 和 `ipfs_cid`。
   - 这笔交易在 L2 上被快速确认，Gas 费极低。

**最终结果：**

- **链下 (Off-chain)**：存储了文章原文，解决了高成本和存储容量问题。
- **链上 Layer 2 (On-chain L2)**：以极低的成本记录了文章的**所有权证明**（作者地址）、**内容完整性证明**（哈希值）和**存在性证明**（时间戳和 IPFS 地址）。
- **链上 Layer 1 (On-chain L1)**：L2 会定期将其状态根锚定到 L1，使得 L2 上的这条记录最终获得了 L1 级别的安全性，不可篡改。

# 2025-08-14

今天参加了两个晚会，同时了解了一下产品经理必会的prd文档，以及借助ai模拟了一个产品经理的工作流程常用的工具等等

# 2025-08-12

今天参加了两个晚会，了解做产品如何入门，以及一些产品失败的原因，运营的话，了解了常见运营工具的使用吗，telegram的机器人，twitter的space，活动召开相关

# 2025-08-11

8.11参加两个分享会，回顾dapp部署的流程，同时了解了一些web3与web2运营的区别

# 2025-08-10

复习了以前的solidity的学习笔记，尤其是gas优化方面，以及ethers.js的用法，如何和小狐狸交互等

# 2025-08-08

复习了一下同时整理了笔记

# 2025-08-07

通过https://layer2.myfirst.io/#content深入了解了一下layer2
同时参加晚上律所的讲座了解一些web3常见的违规的地方

# 2025-08-06

学习了notion和figma的基本使用方式，同时听直播了解了一些web3的故事，同时去了解其他的web3故事

# 2025-08-05

了解了一些行业赛道，以及一些工具的详细使用

# 2025-08-04

了解区块链的基本原理，以及和web2之间的区别

# 2025.07.30


<!-- Content_END -->
