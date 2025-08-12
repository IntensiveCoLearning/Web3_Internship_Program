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
# 2025-08-12

# Web3实战：打造属于你的NFT数字资产

Web3时代，NFT（非同质化代币）正重塑数字所有权的未来。无论是独一无二的艺术品还是虚拟资产，ERC721标准让你轻松实现NFT的创建与管理。本文通过一个完整的实战案例，带你深入Solidity智能合约开发，快速部署属于你的NFT代币，解锁Web3开发的无限可能。准备好加入这场数字资产革命了吗？

本文通过一个基于OpenZeppelin的ERC721智能合约MyERC721Token，展示了NFT开发的完整流程，包括合约编写、部署脚本、Sepolia测试网部署及验证。合约支持URI存储、可销毁、投票和EIP712签名等功能，适合Web3开发者快速上手。文章还涵盖部署问题优化与实用建议，为打造个性化NFT项目提供清晰指引。

## MyERC721Token 实操

### 合约代码

```ts
// SPDX-License-Identifier: MIT
// Compatible with OpenZeppelin Contracts ^5.0.0
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Votes.sol";

contract MyERC721Token is ERC721, ERC721URIStorage, ERC721Burnable, Ownable, EIP712, ERC721Votes {
    uint256 private _nextTokenId;

    constructor(address initialOwner)
        ERC721("MyERC721Token", "MTK721")
        Ownable(initialOwner)
        EIP712("MyERC721Token", "1")
    {}

    function safeMint(address to, string memory uri) public onlyOwner {
        uint256 tokenId = _nextTokenId++;
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, uri);
    }

    // The following functions are overrides required by Solidity.

    function _update(address to, uint256 tokenId, address auth)
        internal
        override(ERC721, ERC721Votes)
        returns (address)
    {
        return super._update(to, tokenId, auth);
    }

    function _increaseBalance(address account, uint128 value) internal override(ERC721, ERC721Votes) {
        super._increaseBalance(account, value);
    }

    function tokenURI(uint256 tokenId) public view override(ERC721, ERC721URIStorage) returns (string memory) {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId) public view override(ERC721, ERC721URIStorage) returns (bool) {
        return super.supportsInterface(interfaceId);
    }
}

```

### MyERC721Token 智能合约代码的解释

1. 头部和许可证

```solidity
// SPDX-License-Identifier: MIT
// Compatible with OpenZeppelin Contracts ^5.0.0
pragma solidity ^0.8.20;
```

- **SPDX-License-Identifier: MIT**：声明代码采用MIT许可证，允许开源使用，几乎无限制，便于社区共享和复用。
- **Compatible with OpenZeppelin Contracts ^5.0.0**：表明合约与OpenZeppelin合约库5.0.0及以上版本兼容，确保依赖的库功能一致。
- **pragma solidity ^0.8.20**：指定Solidity编译器版本为0.8.20或兼容版本，^允许小版本更新，以保证安全性和新特性支持。

2. 导入库

```ts
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/cryptography/EIP712.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Votes.sol";
```

这些行导入了OpenZeppelin的标准化、经过审计的合约库，为NFT合约扩展功能：

- **ERC721.sol**：ERC721标准的实现，提供NFT核心功能，如所有权管理、转移等。
- **ERC721URIStorage.sol**：扩展功能，用于存储每个NFT的元数据URI（如指向JSON文件的链接，包含图片、描述等）。
- **ERC721Burnable.sol**：添加“销毁”功能，允许永久删除NFT，减少流通量。
- **Ownable.sol**：实现访问控制，限制特定功能（如铸造）仅限合约拥有者调用。
- **EIP712.sol**：支持EIP-712标准，用于结构化数据签名，可实现无gas交易（如签名授权）。
- **ERC721Votes.sol**：为NFT添加治理功能，允许NFT持有者参与投票（如`DAO`治理）。

3. 合约定义和继承

```ts
contract MyERC721Token is ERC721, ERC721URIStorage, ERC721Burnable, Ownable, EIP712, ERC721Votes {
```

- 定义合约名为 MyERC721Token，通过多重继承整合多个OpenZeppelin模块：
  - ERC721：提供NFT基本功能。
  - ERC721URIStorage：支持存储和查询NFT元数据。
  - ERC721Burnable：支持销毁NFT。
  - Ownable：限制功能访问，仅限合约拥有者。
  - EIP712：支持签名验证。
  - ERC721Votes：支持NFT用于治理投票。
- 这种模块化继承方式利用OpenZeppelin的标准化代码，减少开发时间并提高安全性。

4. **状态变量**

```ts
uint256 private _nextTokenId;
```

- _nextTokenId 是一个私有变量，用于跟踪下一个可用的NFT代币ID。
- 每次铸造新NFT时，_nextTokenId 自增，确保每个NFT有唯一ID。

5. 构造函数

```ts
constructor(address initialOwner)
    ERC721("MyERC721Token", "MTK721")
    Ownable(initialOwner)
    EIP712("MyERC721Token", "1")
{}
```

- **作用**：初始化合约，设置NFT名称、符号、拥有者和EIP-712参数。
- **参数**：
  - initialOwner：指定合约的初始拥有者地址，拥有特殊权限（如铸造NFT）。
- **初始化**：
  - ERC721("MyERC721Token", "MTK721")：设置NFT名称为“MyERC721Token”，符号为“MTK721”（类似代币的简写）。
  - Ownable(initialOwner)：将合约所有权分配给 initialOwner。
  - EIP712("MyERC721Token", "1")：初始化EIP-712签名域，名称为“MyERC721Token”，版本为“1”，用于签名验证。
- **空函数体**：{} 表示构造函数仅执行初始化，无额外逻辑。

6. 铸造函数

```ts
function safeMint(address to, string memory uri) public onlyOwner {
    uint256 tokenId = _nextTokenId++;
    _safeMint(to, tokenId);
    _setTokenURI(tokenId, uri);
}
```

- **作用**：铸造新的NFT并分配给指定地址，同时设置元数据URI。
- **参数**：
  - to：接收NFT的地址。
  - uri：NFT的元数据链接（通常指向存储图片、名称等信息的JSON文件）。
- **修饰符**：
  - onlyOwner：限制仅合约拥有者可调用此函数（来自 Ownable 模块）。
- **逻辑**：
  - uint256 tokenId = _nextTokenId++：获取当前_nextTokenId 作为新NFT的ID，并自增。
  - _safeMint(to, tokenId)：调用ERC721的_safeMint 函数，铸造NFT并分配给 to（安全检查接收者是否支持ERC721）。
  - _setTokenURI(tokenId, uri)：设置NFT的元数据URI，存储在 ERC721URIStorage 中。
- **用途**：这是创建NFT的核心功能，允许拥有者铸造新代币并关联元数据。

7. 重写函数

以下函数是Solidity要求的重写，用于解决多重继承中的冲突，确保功能正确实现：

7.1 **更新函数**

```ts
function _update(address to, uint256 tokenId, address auth)
    internal
    override(ERC721, ERC721Votes)
    returns (address)
{
    return super._update(to, tokenId, auth);
}
```

- **作用**：处理NFT的转移或销毁逻辑，需同时兼容 ERC721 和 ERC721Votes。
- **参数**：
  - to：NFT转移的目标地址。
  - tokenId：NFT的ID。
  - auth：授权地址（通常为调用者）。
- **重写**：override(ERC721, ERC721Votes) 表示重写两个父合约的 _update 函数，解决继承冲突。
- **逻辑**：调用父类的 _update 方法，保持默认行为，同时更新投票权重（用于治理）。

7.2 **余额更新函数**

```ts
function _increaseBalance(address account, uint128 value) internal override(ERC721, ERC721Votes) {
    super._increaseBalance(account, value);
}
```

- **作用**：更新账户的NFT余额，兼容 `ERC721` 和 `ERC721Votes`。
- **参数**：
  - account：账户地址。
  - value：增加的余额（通常为1，表示一个NFT）。
- **重写**：解决多重继承冲突，调用父类的 _increaseBalance 方法，确保余额和投票权重同步更新。

7.3 **元数据查询函数**

```ts
function tokenURI(uint256 tokenId) public view override(ERC721, ERC721URIStorage) returns (string memory) {
    return super.tokenURI(tokenId);
}
```

- **作用**：返回指定NFT的元数据URI。
- **参数**：
  - tokenId：NFT的ID。
- **重写**：兼容 ERC721 和 ERC721URIStorage，优先使用 ERC721URIStorage 的实现（支持存储URI）。
- **逻辑**：调用父类的 tokenURI 方法，返回存储的URI。

7.4 **接口支持函数**

```solidity
function supportsInterface(bytes4 interfaceId) public view override(ERC721, ERC721URIStorage) returns (bool) {
    return super.supportsInterface(interfaceId);
}
```

- **作用**：检查合约是否支持特定接口（如ERC721或扩展接口）。
- **参数**：
  - interfaceId：接口的标识符（基于ERC-165标准）。
- **重写**：兼容 ERC721 和 `ERC721URIStorage`，确保正确报告支持的接口。
- **逻辑**：调用父类的 `supportsInterface` 方法，返回是否支持指定接口。

8. 功能总结

- **核心功能**：
  - 铸造NFT（safeMint）：仅限拥有者铸造，设置元数据URI。
  - 元数据管理：通过 `ERC721URIStorage` 存储和查询NFT元数据。
  - 销毁NFT：通过 `ERC721Burnable` 支持销毁功能。
  - 访问控制：通过 `Ownable` 限制关键操作。
  - 治理支持：通过 `ERC721Votes` 允许NFT用于投票。
  - 签名支持：通过 `EIP712` 启用结构化数据签名。
- **设计亮点**：
  - 使用`OpenZeppelin`的模块化库，代码安全且易于扩展。
  - 支持多种高级功能（如投票、签名），适用于复杂NFT项目。
  - 通过重写函数解决多重继承冲突，保证功能一致性。

9. **潜在用例**

- **NFT市场**：可用于数字艺术、收藏品或游戏资产的NFT创建。
- **去中心化治理**：NFT持有者可通过 `ERC721Votes` 参与DAO投票。
- **元数据管理**：通过URI存储NFT的动态内容（如图片、属性）。
- **签名授权**：支持EIP-712的签名机制，可实现无gas交易或授权。

10. **注意事项**

- **权限管理**：onlyOwner 限制了 safeMint，需确保拥有者地址安全。
- **Gas成本**：多重继承和扩展功能可能增加部署和调用成本，需优化。
- **元数据存储**：URI通常指向IPFS或中心化服务器，需保证链接的持久性。
- **测试网验证**：如文章所示，合约已在Sepolia测试网部署，建议在主网部署前充分测试。

MyERC721Token 是一个功能全面的ERC721 NFT智能合约，集成了铸造、销毁、元数据管理、治理投票和签名验证等功能。通过OpenZeppelin的标准化库，代码安全可靠，适合Web3开发者快速构建NFT项目。理解其结构和逻辑，有助于深入掌握Solidity开发和Web3生态的NFT应用。

### 部署脚本

```ts
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Script, console} from "forge-std/Script.sol";
import {MyERC721Token} from "../src/MyERC721Token.sol";

contract MyERC721TokenScript is Script {
    MyERC721Token public my721token;

    function setUp() public {}

    function run() public {
        vm.startBroadcast();

        my721token = new MyERC721Token(msg.sender);
        console.log("MyERC721Token deployed to:", address(my721token));

        vm.stopBroadcast();
    }
}

```

### 部署合约

```bash
NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base took 1m 18.6s
➜ source .env

NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base
➜ forge script --chain sepolia script/MyERC721Token.s.sol:MyERC721TokenScript --rpc-url $SEPOLIA_RPC_URL --broadcast --account MetaMask --verify -vvvv

[⠊] Compiling...
[⠔] Compiling 1 files with Solc 0.8.20
[⠒] Solc 0.8.20 finished in 1.44s
Compiler run successful!
Enter keystore password:
Traces:
  [2119248] MyERC721TokenScript::run()
    ├─ [0] VM::startBroadcast()
    │   └─ ← [Return]
    ├─ [2072840] → new MyERC721Token@0xC39B0eE94143C457449e16829837FD59d722933C
    │   ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: DefaultSender: [0x1804c8AB1F12E6bbf3894d4083f33e07309d1f38])
    │   └─ ← [Return] 10002 bytes of code
    ├─ [0] console::log("MyERC721Token deployed to:", MyERC721Token: [0xC39B0eE94143C457449e16829837FD59d722933C]) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::stopBroadcast()
    │   └─ ← [Return]
    └─ ← [Stop]


Script ran successfully.

== Logs ==
  MyERC721Token deployed to: 0xC39B0eE94143C457449e16829837FD59d722933C

## Setting up 1 EVM.
==========================
Simulated On-chain Traces:

  [2072840] → new MyERC721Token@0xC39B0eE94143C457449e16829837FD59d722933C
    ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: DefaultSender: [0x1804c8AB1F12E6bbf3894d4083f33e07309d1f38])
    └─ ← [Return] 10002 bytes of code


==========================

Chain 11155111

Estimated gas price: 10.85383427 gwei

Estimated total gas used for script: 2990587

Estimated amount required: 0.03245933566801649 ETH

==========================

##### sepolia
✅  [Success]Hash: 0x8e5b0e3a9df4e5231b88d28af9c0e6e903bf7afac027a2ee54bf5faaf67b40c0
Contract Address: 0xC39B0eE94143C457449e16829837FD59d722933C
Block: 6326900
Paid: 0.012441733790006772 ETH (2301162 gas * 5.406717906 gwei)

✅ Sequence #1 on sepolia | Total Paid: 0.012441733790006772 ETH (2301162 gas * avg 5.406717906 gwei)


==========================

ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
##
Start verification for (1) contracts
Start verifying contract `0xC39B0eE94143C457449e16829837FD59d722933C` deployed on sepolia

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0xC39B0eE94143C457449e16829837FD59d722933C.

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0xC39B0eE94143C457449e16829837FD59d722933C.

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0xC39B0eE94143C457449e16829837FD59d722933C.
Submitted contract for verification:
        Response: `OK`
        GUID: `q1v8v6kswcqvnzfdifksth4hdk1ss7ukejzxxfuktumivdrr5e`
        URL: https://sepolia.etherscan.io/address/0xc39b0ee94143c457449e16829837fd59d722933c
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
All (1) contracts were verified!

Transactions saved to: /Users/qiaopengjun/Code/solidity-code/NFTMarketHub/broadcast/MyERC721Token.s.sol/11155111/run-latest.json

Sensitive values saved to: /Users/qiaopengjun/Code/solidity-code/NFTMarketHub/cache/MyERC721Token.s.sol/11155111/run-latest.json


```

### 浏览器查看合约

<https://sepolia.etherscan.io/address/0xc39b0ee94143c457449e16829837fd59d722933c>


### 部署问题修改

```bash
NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base
➜ source .env

NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base
➜ forge script --chain sepolia MyERC721TokenScript --rpc-url $SEPOLIA_RPC_URL --broadcast --verify -vvvv

[⠊] Compiling...
[⠔] Compiling 1 files with Solc 0.8.20
[⠒] Solc 0.8.20 finished in 1.46s
Compiler run successful!
Traces:
  [2120084] MyERC721TokenScript::run()
    ├─ [0] VM::envUint("PRIVATE_KEY") [staticcall]
    │   └─ ← [Return] <env var value>
    ├─ [0] VM::envAddress("ACCOUNT_ADDRESS") [staticcall]
    │   └─ ← [Return] <env var value>
    ├─ [0] VM::startBroadcast(<pk>)
    │   └─ ← [Return]
    ├─ [2072840] → new MyERC721Token@0x7eA36391c7127A7f40E5c23212A8016d6E494546
    │   ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: 0x750Ea21c1e98CcED0d4557196B6f4a5974CCB6f5)
    │   └─ ← [Return] 10002 bytes of code
    ├─ [0] console::log("MyERC721Token deployed to:", MyERC721Token: [0x7eA36391c7127A7f40E5c23212A8016d6E494546]) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] VM::stopBroadcast()
    │   └─ ← [Return]
    └─ ← [Stop]


Script ran successfully.

== Logs ==
  MyERC721Token deployed to: 0x7eA36391c7127A7f40E5c23212A8016d6E494546

## Setting up 1 EVM.
==========================
Simulated On-chain Traces:

  [2072840] → new MyERC721Token@0x7eA36391c7127A7f40E5c23212A8016d6E494546
    ├─ emit OwnershipTransferred(previousOwner: 0x0000000000000000000000000000000000000000, newOwner: 0x750Ea21c1e98CcED0d4557196B6f4a5974CCB6f5)
    └─ ← [Return] 10002 bytes of code


==========================

Chain 11155111

Estimated gas price: 53.318074191 gwei

Estimated total gas used for script: 2990587

Estimated amount required: 0.159452339540640117 ETH

==========================

##### sepolia
✅  [Success]Hash: 0x77190a6bbe59f4b98dc06b1219ca34fcf5cc1ace40f6998bb26568fcb93e5380
Contract Address: 0x7eA36391c7127A7f40E5c23212A8016d6E494546
Block: 6355525
Paid: 0.057633696474121704 ETH (2301162 gas * 25.045475492 gwei)

✅ Sequence #1 on sepolia | Total Paid: 0.057633696474121704 ETH (2301162 gas * avg 25.045475492 gwei)


==========================

ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
##
Start verification for (1) contracts
Start verifying contract `0x7eA36391c7127A7f40E5c23212A8016d6E494546` deployed on sepolia

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0x7eA36391c7127A7f40E5c23212A8016d6E494546.

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0x7eA36391c7127A7f40E5c23212A8016d6E494546.

Submitting verification for [src/MyERC721Token.sol:MyERC721Token] 0x7eA36391c7127A7f40E5c23212A8016d6E494546.
Submitted contract for verification:
        Response: `OK`
        GUID: `bgjivlpsttkmre2wq8etbj9gbv6txntfhwwe3rnh3zzduq12pm`
        URL: https://sepolia.etherscan.io/address/0x7ea36391c7127a7f40e5c23212a8016d6e494546
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
All (1) contracts were verified!

Transactions saved to: /Users/qiaopengjun/Code/solidity-code/NFTMarketHub/broadcast/MyERC721Token.s.sol/11155111/run-latest.json

Sensitive values saved to: /Users/qiaopengjun/Code/solidity-code/NFTMarketHub/cache/MyERC721Token.s.sol/11155111/run-latest.json


NFTMarketHub on  main [!?] via ⬢ v22.1.0 via 🅒 base took 1m 27.4s
➜
```

### 浏览器查看

<https://sepolia.etherscan.io/address/0x7ea36391c7127a7f40e5c23212a8016d6e494546#code>


## 总结

通过本次实战，我们成功开发并部署了一个功能强大的ERC721 NFT合约MyERC721Token，在Sepolia测试网上实现数字资产的创建与管理。结合OpenZeppelin的标准化工具和Foundry的部署流程，Web3开发者可以高效构建安全可靠的NFT项目。未来，你可以扩展合约功能，接入NFT市场或探索跨链应用，在Web3的浪潮中打造属于自己的数字资产帝国。

## 参考

- <https://eips.ethereum.org/EIPS/eip-712>
- <https://eips.ethereum.org/EIPS/eip-2612>
- <https://www.openzeppelin.com/contracts>
- <https://github.com/AmazingAng/WTF-Solidity/blob/main/37_Signature/readme.md>
- <https://github.com/AmazingAng/WTF-Solidity/blob/main/52_EIP712/readme.md>
- <https://github.com/jesperkristensen58/ERC712-Permit-Example>
- <https://book.getfoundry.sh/tutorials/testing-eip712>

# 2025-08-11

# solidity智能合约的 pure 与 view 使用原理及场景
## pure 与 view 原理
pure：不读取更不修改区块上的变量，使用本机的CPU资源计算我们的函数。所以不消耗任何的资源这是很容易的理解的。

view: 但是 view 既然要读取区块链上的值，为什么也不用消耗 gas 呢？

其实很简单，因为作为一个全节点来说，会同步保存所有的信息，保存在本地中。

那么我们要查看区块链上的资源，同样可以直接在一个全节点之上查询数据即可。

我不需要全世界的节点都知道。都去同时的处理这笔事务。我也不需要将调用这笔函数的信息记录在区块链上。

所以 view 仍然不消耗 gas。

view: 可以自由调用，因为它只是“查看”区块链的状态而不改变它

pure: 也可以自由调用，既不读取也不写入区块链

# 2025-08-10

# Solana 开发学习之Solana 基础知识

## Install the Solana CLI

### 相关链接

- <https://docs.solanalabs.com/cli/install>
- <https://solanacookbook.com/zh/getting-started/installation.html#%E5%AE%89%E8%A3%85%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7>
- <https://www.solanazh.com/course/1-4>
- <https://solana.com/zh/developers/guides/getstarted/setup-local-development>

### 实操

- 安装

```bash
sh -c "$(curl -sSfL https://release.solana.com/v1.18.2/install)"

downloading v1.18.2 installer
  ✨ 1.18.2 initialized
Adding
export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH" to /Users/qiaopengjun/.profile
Adding
export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH" to /Users/qiaopengjun/.zprofile
Adding
export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH" to /Users/qiaopengjun/.bash_profile

Close and reopen your terminal to apply the PATH changes or run the following in your existing bash:

export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH"

```

- 配置环境变量

```bash
vim .zshrc

# 复制并粘贴下面命令以更新 PATH
export PATH="/Users/qiaopengjun/.local/share/solana/install/active_release/bin:$PATH"
```

- 通过运行以下命令确认您已安装了所需的 Solana 版本：

```bash
solana --version

# 实操
solana --version
solana-cli 1.18.2 (src:13656e30; feat:3352961542, client:SolanaLabs)
```

- 切换版本

```bash
solana-install init 1.16.4
```

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
