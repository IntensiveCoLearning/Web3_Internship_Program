---
timezone: UTC+8
---

# Paxon

**GitHub ID:** qiaopengjun5162

**Telegram:** @Qiao4812

## Self-introduction

web3 开发者，Python、Go、Rust、Solidity 等语言经验丰富，项目开发部署实操经验丰富

## Notes

<!-- Content_START -->
# 2025-08-09

# Web3学习之DAPP开发流程与架构

集中化帮助数十亿人接入万维网，并创建了万维网赖以生存的稳定、强大的基础设施。与此同时，少数中心化实体在万维网的大片地区拥有据点，单方面决定什么应该被允许，什么不应该被允许。

Web3 就是这个困境的答案。 Web3 不是由大型科技公司垄断的 Web，而是拥抱去中心化，并由其用户构建、运营和拥有。 Web3 将权力交给个人而不是公司。在讨论 Web3 之前，让我们先探讨一下我们是如何走到这一步的。

## What is Web3?

Web3 has become a catch-all term for the vision of a new, better internet. At its core, Web3 uses blockchains, cryptocurrencies, and NFTs to give power back to the users in the form of ownership. [A 2020 post on Twitter(opens in a new tab)](https://twitter.com/himgajria/status/1266415636789334016) said it best: Web1 was read-only, Web2 is read-write, Web3 will be read-write-own.

## Web 进化过程

Web 的进化过程可以分为几个关键阶段：

1. **Web 1.0 (静态页面)**:
   - Web 1.0 是互联网的起步阶段，网页内容主要是静态的，用户主要是被动消费信息。

2. **Web 2.0 (互动和用户生成内容)**:
   - Web 2.0 的到来带来了互动和用户生成内容的概念。网站变得更加动态，用户可以参与内容的创建和分享，社交媒体、博客和在线社区开始兴起。

3. **移动优先和响应式设计**:
   - 随着移动设备的普及，Web 开发者开始采用响应式设计来确保网站在各种设备上都能良好展示和操作。

4. **Web 应用程序的兴起**:
   - 随着浏览器和技术的发展，Web 应用程序开始变得越来越强大和复杂。单页面应用程序 (SPA)、前端框架和库如React、Angular和Vue.js等的兴起推动了Web 应用的发展。

5. **增强现实和虚拟现实**:
   - 最近的发展趋势包括增强现实 (AR) 和虚拟现实 (VR)，这些技术在Web上也有了应用。WebXR API的引入使得开发者可以创建支持AR和VR体验的Web内容。

6. **Web3.0 和去中心化应用 (DApps)**:
   - Web3.0 是一个正在发展的概念，它致力于推动互联网的下一个阶段，主要关注去中心化、区块链和加密货币。去中心化应用 (DApps) 的兴起成为Web3.0的重要组成部分。

这些阶段展示了Web技术如何从静态页面发展到支持复杂的Web应用和未来的技术趋势。

#### 总结

### Evolution of the Web

| Era     | Time Period   | Features                                                     |
| ------- | ------------- | ------------------------------------------------------------ |
| Web 1.0 | 1990-2004     | Read-only                                                    |
| Web 2.0 | 2004-present  | Read (consumption), Write (user-generated content)           |
| Web 3.0 | 2014-present? | Read (consumption), Write (contributions to decentralized platforms), Own (digital assets) |

## Web3 核心

Web3 核心是通过区块链、加密货币、NFT将所有权归还用户。也就是说，Web3 核心是利用区块链技术和加密货币，通过智能合约和去中心化应用（DApps），实现用户对数字资产的所有权和控制权，推动互联网从中心化向去中心化的转变。

## 参考

- <https://zh.wikipedia.org/wiki/Web3>
- <https://ethereum.org/en/web3/>

# 2025-08-08

# 部署 NFT 合约

## 实操

### 部署脚本 
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

import {Script, console} from "forge-std/Script.sol";
import {stdJson} from "forge-std/StdJson.sol";
import {MFNFT} from "../src/MFNFT.sol";

contract MFNFTScript is Script {
    function setUp() public {}

    function run() public {
        uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
        address deployerAddress = vm.addr(deployerPrivateKey);
        console.log("Deployer Address:", deployerAddress);

        vm.startBroadcast(deployerPrivateKey);

        // 部署 MFNFT 合约
        // 使用部署者地址作为初始 signer，后续可以通过 setSigner 更改
        MFNFT mfnft = new MFNFT(deployerAddress);
        console.log("MFNFT deployed to:", address(mfnft));
        console.log("Initial signer set to:", deployerAddress);

        vm.stopBroadcast();

        // 部署成功后保存信息
        saveDeploymentInfo(deployerAddress, address(mfnft));
    }

    function saveDeploymentInfo(
        address deployerAddress,
        address mfnftAddress
    ) internal {
        string memory path = "./deployments/MFNFT.json";

        // 1. 为我们的 JSON 对象定义一个唯一的标识符（root key）
        string memory rootKey = "mfnftDeploymentInfo";
        string memory finalJson;

        // 2. 告诉 Foundry 在名为 rootKey 的对象上进行所有操作
        // 每一次 serialize 调用都会返回完整的、更新后的 JSON 字符串。
        vm.serializeAddress(rootKey, ".deploy.mfnftAddress", mfnftAddress);
        vm.serializeUint(rootKey, ".deploy.chainId", block.chainid);
        // 3. 我们只需要捕获最后一次调用的返回值即可。
        finalJson = vm.serializeAddress(
            rootKey,
            ".deploy.deployerAddress",
            deployerAddress
        );

        // 4. 将完整的 JSON 写入文件
        vm.writeJson(finalJson, path);
        console.log("Deployment info saved to: %s", path);
    }
}

/*
== Logs ==
  Deployer Address: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
  MFNFT deployed to: 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
  Initial signer set to: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
  Deployment info saved to: ./deployments/MFNFT.json

  https://sepolia.etherscan.io/address/0xe9d6e87644e4498f5d94391b6f4553d3a58d6198#code





  YuanqiGenesis on  master [✘!+?] on 🐳 v28.2.2 (orbstack) took 18.4s
➜ make deploy-sepolia CONTRACT=MFNFT
Cleaning and building all contracts...
[⠊] Compiling...
[⠑] Compiling 92 files with Solc 0.8.30
[⠢] Solc 0.8.30 finished in 9.96s
Compiler run successful!
Deploying MFNFT to Sepolia testnet and verifying...
[⠒] Compiling...
No files changed, compilation skipped
Traces:
  [132] MFNFTScript::setUp()
    └─ ← [Stop]

  [1650691] MFNFTScript::run()
    ├─ [0] VM::envUint("PRIVATE_KEY") [staticcall]
    │   └─ ← [Return] <env var value>
    ├─ [0] VM::addr(<pk>) [staticcall]
    │   └─ ← [Return] 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
    ├─ [0] console::log("Deployer Address:", 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::startBroadcast(<pk>)
    │   └─ ← [Return]
    ├─ [1602470] → new MFNFT@0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
    │   ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5)
    │   └─ ← [Return] 7525 bytes of code
    ├─ [0] console::log("MFNFT deployed to:", MFNFT: [0xE9d6E87644e4498f5d94391b6F4553D3a58d6198]) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] console::log("Initial signer set to:", 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::stopBroadcast()
    │   └─ ← [Return]
    ├─ [0] VM::serializeAddress("mfnftDeploymentInfo", ".deploy.mfnftAddress", MFNFT: [0xE9d6E87644e4498f5d94391b6F4553D3a58d6198])
    │   └─ ← [Return] "{\".deploy.mfnftAddress\":\"0xE9d6E87644e4498f5d94391b6F4553D3a58d6198\"}"
    ├─ [0] VM::serializeUint("mfnftDeploymentInfo", ".deploy.chainId", 11155111 [1.115e7])
    │   └─ ← [Return] "{\".deploy.chainId\":11155111,\".deploy.mfnftAddress\":\"0xE9d6E87644e4498f5d94391b6F4553D3a58d6198\"}"
    ├─ [0] VM::serializeAddress("mfnftDeploymentInfo", ".deploy.deployerAddress", 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5)
    │   └─ ← [Return] "{\".deploy.chainId\":11155111,\".deploy.deployerAddress\":\"0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5\",\".deploy.mfnftAddress\":\"0xE9d6E87644e4498f5d94391b6F4553D3a58d6198\"}"
    ├─ [0] VM::writeJson("{\".deploy.chainId\":11155111,\".deploy.deployerAddress\":\"0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5\",\".deploy.mfnftAddress\":\"0xE9d6E87644e4498f5d94391b6F4553D3a58d6198\"}", "./deployments/MFNFT.json")
    │   └─ ← [Return]
    ├─ [0] console::log("Deployment info saved to: %s", "./deployments/MFNFT.json") [staticcall]
    │   └─ ← [Stop]
    └─ ← [Return]


Script ran successfully.

== Logs ==
  Deployer Address: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
  MFNFT deployed to: 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
  Initial signer set to: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5
  Deployment info saved to: ./deployments/MFNFT.json

## Setting up 1 EVM.
==========================
Simulated On-chain Traces:

  [1602470] → new MFNFT@0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
    ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: 0xE91e2DF7cE50BCA5310b7238F6B1Dfcd15566bE5)
    └─ ← [Return] 7525 bytes of code


==========================

Chain 11155111

Estimated gas price: 0.000610107 gwei

Estimated total gas used for script: 2330715

Estimated amount required: 0.000001421985536505 ETH

==========================

##### sepolia
✅  [Success] Hash: 0xfad2a81b872700d168b42a871656dce3d8a03ff83ef5ac25f5855112182aeabd
Contract Address: 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198
Block: 8930361
Paid: 0.000001093731230042 ETH (1792858 gas * 0.000610049 gwei)

✅ Sequence #1 on sepolia | Total Paid: 0.000001093731230042 ETH (1792858 gas * avg 0.000610049 gwei)


==========================

ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
##
Start verification for (1) contracts
Start verifying contract `0xE9d6E87644e4498f5d94391b6F4553D3a58d6198` deployed on sepolia
EVM version: cancun
Compiler version: 0.8.30
Optimizations:    200
Constructor args: 000000000000000000000000e91e2df7ce50bca5310b7238f6b1dfcd15566be5

Submitting verification for [src/MFNFT.sol:MFNFT] 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (4 tries remaining)

Submitting verification for [src/MFNFT.sol:MFNFT] 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198.
Warning: Could not detect the deployment.; waiting 5 seconds before trying again (3 tries remaining)

Submitting verification for [src/MFNFT.sol:MFNFT] 0xE9d6E87644e4498f5d94391b6F4553D3a58d6198.
Submitted contract for verification:
        Response: `OK`
        GUID: `jpr5wsiyph1mmpsblsvdrwkd9nbsdncbljw1xfhai7fwund4us`
        URL: https://sepolia.etherscan.io/address/0xe9d6e87644e4498f5d94391b6f4553d3a58d6198
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Warning: Verification is still pending...; waiting 15 seconds before trying again (7 tries remaining)
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
All (1) contracts were verified!

Transactions saved to: /Users/qiaopengjun/Code/Solidity/YuanqiGenesis/YuanqiGenesis/broadcast/MFNFT.s.sol/11155111/run-latest.json

Sensitive values saved to: /Users/qiaopengjun/Code/Solidity/YuanqiGenesis/YuanqiGenesis/cache/MFNFT.s.sol/11155111/run-latest.json

Extracting MFNFT ABI...
ABI for MFNFT copied to abis/MFNFT.json

*/

```

# 2025-08-07

# Web3学习之ERC721

## ERC721

### 加密猫

<https://www.cryptokitties.co/>

加密猫（CryptoKitties）是一个建立在以太坊区块链上的区块链游戏和收藏品平台。它是最早将区块链技术应用于非同质化代币（NFT）的项目之一，并且对区块链游戏和NFT的普及起到了重要作用。

<https://etherscan.io/token/0x06012c8cf97bead5deae237070f9587f8e7a266d#code>

### 加密猫的关键特点

1. **NFT（非同质化代币）**：
   - 每只加密猫都是一个唯一的ERC-721代币，这意味着每只猫都有独特的属性和基因，无法被复制或替代。
   - NFT的特点使得每只猫在区块链上都是唯一的，这为数字收藏品提供了真正的稀缺性和所有权证明。

2. **繁殖和遗传**：
   - 玩家可以通过繁殖两只猫来生成新的小猫，每只小猫的基因是父母基因的组合，这使得新生的小猫具有独特的属性和外观。
   - 繁殖过程中的基因组合是随机的，这为游戏增加了趣味性和不确定性。

3. **市场交易**：
   - 玩家可以在市场上买卖加密猫，价格由市场需求决定。稀有和独特的猫通常会卖出高价。
   - 玩家也可以通过拍卖和直接交易等方式进行猫的买卖。

4. **区块链技术**：
   - 加密猫是建立在以太坊区块链上的，所有交易和所有权记录都是透明和不可篡改的。
   - 由于使用区块链技术，玩家可以放心地进行交易和收藏，因为他们的猫的所有权是安全和受保护的。

### 示例代码：创建简单的ERC-721代币

以下是一个简单的ERC-721代币合约示例，展示了如何创建一个基本的NFT合约：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract CryptoKitty is ERC721, Ownable {
    uint256 public nextTokenId;
    
    constructor() ERC721("CryptoKitty", "CK") {}

    function createKitty() external onlyOwner {
        _safeMint(msg.sender, nextTokenId);
        nextTokenId++;
    }

    function getKitty(uint256 tokenId) external view returns (address) {
        return ownerOf(tokenId);
    }
}
```

### 加密猫对区块链和NFT的影响

- **普及NFT概念**：加密猫是最早的成功NFT项目之一，使得NFT的概念被更多人理解和接受。
- **推动以太坊网络**：由于加密猫的流行，一度导致以太坊网络拥堵，这也促使开发者和社区更关注区块链的扩展性和性能问题。
- **激发更多创新**：加密猫的成功激发了许多类似的区块链游戏和NFT项目的出现，如CryptoPunks、Axie Infinity等。

### 结论

加密猫（CryptoKitties）不仅是一个有趣的区块链游戏，更是NFT和数字收藏品领域的重要推动力。通过将区块链技术应用于游戏和收藏品，它为整个行业提供了一个新的视角和可能性。

## ERC721标准

**<https://eips.ethereum.org/EIPS/eip-721>**

思考：从ERC20到ERC721的协议是怎么实现的？

转账的时候参数怎么设计

查询的时候参数怎么设计

授权批准的时候参数怎么设计

## ERC1155

<https://eips.ethereum.org/EIPS/eip-1155>

查询

交易 transfer

授权 approve

<https://www.lootproject.com/>

RWA

图床

# 2025-08-06

# Web3 学习之私钥保护

# ——将私钥导入加密密钥库

## 私钥

#### 什么是私钥？

在Web3和区块链世界中，私钥是一串唯一的数字和字母组合，用于控制和管理你的加密货币和数字资产。拥有私钥的人可以访问相应的数字资产并执行交易，因此私钥必须高度保密。

简单来说，私钥即为随机生成的复杂密码。有了私钥，您就能使用自己的数字货币。他人获知您的私钥之后，即可访问您的所有资产和币种，甚至签署和执行交易。

**为确保您的数字货币安全，妥善保管私钥至关重要**

#### 私钥的重要性

1. **访问权限**：私钥是访问你的加密钱包和数字资产的唯一凭证。没有私钥，你将无法控制或管理你的资产。
2. **安全性**：私钥应保密并安全存储。如果私钥泄露，资产可能会被盗。
3. **不可恢复**：如果私钥丢失，没有任何机构能够帮助恢复。因此，备份私钥非常重要。

#### 如何生成和管理私钥

1. **生成私钥**：私钥可以通过多种方法生成，最常见的是通过加密钱包应用程序生成。

2. **存储私钥**：私钥应以安全的方式存储，常见的存储方式包括：
    - **纸钱包**：将私钥打印或手写在纸上，并保存在安全的地方。
    - **硬件钱包**：使用专用的硬件设备来存储私钥，增加安全性。
    - **加密密钥库**：使用加密技术将私钥存储在数字文件中。以下是将私钥导入加密密钥库的示例代码：

3. **备份私钥**：始终确保有私钥的备份，最好是多个备份，存放在不同的位置以防丢失。

#### 使用私钥

私钥可以用来签名交易和验证所有权。以下是使用私钥签名交易的示例代码：

#### 结论

私钥是Web3世界中的核心概念，管理好私钥是保障数字资产安全的关键。通过学习如何生成、存储、备份和使用私钥，你可以更好地掌握和保护自己的数字资产。

## 私钥注意事项

- **私钥是保密的，不能透露给他人**。如果私钥丢了，钱就丢了。不建议把私钥放在手机或者电脑设备，联网后有机会丢失。
- **通过私钥可以反算出公钥，但通过公钥不能反算出私钥**

- 公钥和钱包地址是公开的**。如果别人要给你转钱，你把钱包地址告诉他就可以。**
- **助记词，可以用于重新生成私钥**。所以**助记词不能透露给他人**。

## 将私钥导入 encrypted keystore

### Import a private key into an encrypted keystore

#### [EXAMPLES](https://book.getfoundry.sh/reference/cast/cast-wallet-import#examples)

1. Create a keystore from a private key:

   ```sh
   cast wallet import BOB --interactive
   ```

2. Create a keystore from a mnemonic:

   ```sh
   cast wallet import ALICE --mnemonic "test test test test test test test test test test test test"
   ```

3. Create a keystore from a mnemonic with a specific mnemonic index:

   ```sh
   cast wallet import ALICE --mnemonic "test test test test test test test test test test test test" --mnemonic-index 1
   ```

### 实操

```bash

~ via 🅒 base
➜
cast wallet list

~ via 🅒 base
➜
cast wallet -h
Wallet management utilities

Usage: cast wallet <COMMAND>

Commands:
  new               Create a new random keypair [aliases: n]
  new-mnemonic      Generates a random BIP39 mnemonic phrase [aliases: nm]
  vanity            Generate a vanity address [aliases: va]
  address           Convert a private key to an address [aliases: a, addr]
  sign              Sign a message or typed data [aliases: s]
  verify            Verify the signature of a message [aliases: v]
  import            Import a private key into an encrypted keystore [aliases: i]
  list              List all the accounts in the keystore default directory
                        [aliases: ls]
  private-key       Derives private key from mnemonic [aliases: pk]
  decrypt-keystore  Decrypt a keystore file to get the private key [aliases: dk]
  help              Print this message or the help of the given subcommand(s)

Options:
  -h, --help  Print help

~ via 🅒 base
➜
cast wallet import MetaMask --interactive
Enter private key:

~ via 🅒 base took 2m 57.8s
➜
cast wallet import MetaMask --interactive
Enter private key:
Enter password:
`MetaMask` keystore was saved successfully. Address: 0x750ea21c1e98cced0d4557196b6f4a5974ccb6f5

~ via 🅒 base took 39.6s
➜
cast wallet list
MetaMask (Local)

#  The path to store the encrypted keystore. Defaults to ~/.foundry/keystores.
~ via 🅒 base
➜
ls ~/.foundry/
bin       cache     keystores share

~ via 🅒 base
➜
ls ~/.foundry/keystores/
MetaMask

#  将私钥转换为地址
~ via 🅒 base
➜
cast wallet address --keystore ~/.foundry/keystores/MetaMask
Enter keystore password:
0x750Ea21c1e98CcED0d4557196B6f4a5974CCB6f5

~ via 🅒 base took 4.3s
➜
```


## 参考

- <https://book.getfoundry.sh/reference/cli/cast/wallet/import>
- <https://learnblockchain.cn/docs/foundry/i18n/zh/reference/cast/cast-wallet.html>
- <https://book.getfoundry.sh/reference/cast/cast-wallet-import>
- <https://www.okx.com/zh-hans/learn/what-are-public-and-private-encryption-keys-crypto-wallets-explained>

# 2025-08-05

# Solana 

surfpool 之于 Solana 就像 anvil 之于以太坊：一个速度极快的⚡️内存测试网，能够立即分叉 Solana 主网。

Surfpool 提供了一个速度极快、开发者友好的 Solana 主网模拟环境，可在您的本地计算机上无缝运行。它无需高性能硬件，同时保持了真实的测试环境。

无论您是在开发、调试还是学习 Solana，Surfpool 都能为您提供一个即时、独立的网络，可以根据需要动态获取缺失的主网数据 - 无需再手动设置帐户。

### 特点

- 快速且轻量——可在任何机器上顺利运行，无需繁重的系统要求。
- 动态账户获取——在交易执行期间自动检索必要的主网账户。
- Anchor 集成 – 检测 Anchor 项目并自动部署程序。
- 教育和调试友好 - 对交易执行和状态变更提供明确的见解。
- 易于安装 - 可通过Homebrew，Snap和Direct Binaries获得。

## 实操

### 安装 surfpool

```bash
brew install txtx/taps/surfpool
```

更多详情请查看：https://docs.surfpool.run/install

### 验证安装

```bash
surfpool --version
surfpool 0.9.6
```

### 查看帮助信息

```bash
surfpool --help
Where you train before surfing Solana

Usage: surfpool <COMMAND>

Commands:
  start        Start Simnet
  completions  Generate shell completions scripts
  run          Run, runbook, run!
  ls           List runbooks present in the current direcoty
  cloud        Txtx cloud commands
  mcp          Start MCP server
  help         Print this message or the help of the given subcommand(s)

Options:
  -h, --help     Print help
  -V, --version  Print version

```

### 启动本地 Solana 网络

```bash
surfpool start                                                                                                                                         
```

#### `surfpool start`解释说明示意图

![surfpool](https://docs.surfpool.run/assets/terminal.svg)























## 总结





## 参考

- https://github.com/txtx/surfpool
- https://docs.surfpool.run/
- https://docs.surfpool.run/install

# 2025-08-04

OP Stack 链的核心权限转移记录

经验教训：部署任何类似系统时，都应遵循以下流程：

1. **拿到配置文件后，第一件事就是“权力审计”**：逐一检查文件中的每一个地址，识别出哪些是权限类地址，哪些是操作类地址。
2. **生成并分配钥匙**：为所有需要控制的地址生成新的、安全的密钥对，并做好备份。
3. **替换所有权**：将配置文件中所有默认的、未知的权限和操作地址，全部替换成你新生成的、可控的地址。
4. **带着信心部署**：在确认了蓝图上的每一个关键角色都属于自己之后，再执行最终的部署命令。


# 2025.07.29


<!-- Content_END -->
