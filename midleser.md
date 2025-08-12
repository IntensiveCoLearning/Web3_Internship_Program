---
timezone: UTC+8
---

# linhong

**GitHub ID:** midleser

**Telegram:** @hiro

## Self-introduction

六年web2前端，目前在成都，想进入web3邻域

## Notes

<!-- Content_START -->
# 2025-08-13

# 智能合约学习笔记

## 一、智能合约安全性深入理解

### 1.1 常见安全漏洞及防范

#### 1. 重入攻击（Reentrancy Attack）

**漏洞原理**：
当合约在发送以太币或调用外部合约后，攻击者可以在原函数执行完成前重新进入该函数。

**经典案例 - The DAO攻击**：
```solidity
// 有漏洞的代码
contract Vulnerable {
    mapping(address => uint) public balances;
    
    function withdraw(uint _amount) public {
        require(balances[msg.sender] >= _amount);
        
        // 危险：先发送ETH，再更新状态
        (bool success, ) = msg.sender.call{value: _amount}("");
        require(success);
        
        balances[msg.sender] -= _amount;
    }
}

// 攻击合约
contract Attacker {
    Vulnerable public vulnerable;
    
    receive() external payable {
        if (address(vulnerable).balance >= 1 ether) {
            vulnerable.withdraw(1 ether); // 重入！
        }
    }
}
```

**防范措施**：
```solidity
// 方法1：使用 Checks-Effects-Interactions 模式
contract Safe {
    mapping(address => uint) public balances;
    
    function withdraw(uint _amount) public {
        require(balances[msg.sender] >= _amount); // Checks
        
        balances[msg.sender] -= _amount; // Effects（先更新状态）
        
        (bool success, ) = msg.sender.call{value: _amount}(""); // Interactions
        require(success);
    }
}

// 方法2：使用重入锁
contract SafeWithLock {
    bool private locked;
    
    modifier noReentrant() {
        require(!locked, "No re-entrancy");
        locked = true;
        _;
        locked = false;
    }
    
    function withdraw(uint _amount) public noReentrant {
        // 函数逻辑
    }
}

// 方法3：使用 OpenZeppelin 的 ReentrancyGuard
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract SafeWithOZ is ReentrancyGuard {
    function withdraw(uint _amount) public nonReentrant {
        // 函数逻辑
    }
}
```

#### 2. 整数溢出和下溢

**问题描述**：
Solidity 0.8.0之前的版本不会自动检查整数溢出。

```solidity
// Solidity < 0.8.0 的危险代码
uint8 number = 255;
number++; // 会变成0（溢出）

uint8 zero = 0;
zero--; // 会变成255（下溢）

// Solidity >= 0.8.0 会自动检查，溢出时会revert
// 如果需要不检查的行为，使用 unchecked 块
unchecked {
    uint8 number = 255;
    number++; // 变成0，但不会revert
}
```

**使用 SafeMath（0.8.0之前）**：
```solidity
import "@openzeppelin/contracts/utils/math/SafeMath.sol";

contract Safe {
    using SafeMath for uint256;
    
    function add(uint256 a, uint256 b) public pure returns (uint256) {
        return a.add(b); // 安全的加法
    }
}
```

#### 3. 访问控制漏洞

**常见错误**：
```solidity
// 错误：忘记添加访问控制
contract Vulnerable {
    address public owner;
    
    constructor() {
        owner = msg.sender;
    }
    
    // 危险：任何人都可以调用
    function withdraw() public {
        payable(owner).transfer(address(this).balance);
    }
}

// 正确做法
contract Safe {
    address public owner;
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }
    
    function withdraw() public onlyOwner {
        payable(owner).transfer(address(this).balance);
    }
}
```

#### 4. 随机数生成漏洞

**问题**：区块链上没有真正的随机数。

```solidity
// 危险：可预测的"随机数"
function random() public view returns (uint) {
    return uint(keccak256(abi.encodePacked(block.timestamp, block.difficulty)));
}

// 更好的方案：使用 Chainlink VRF
import "@chainlink/contracts/src/v0.8/VRFConsumerBase.sol";

contract RandomNumber is VRFConsumerBase {
    bytes32 internal keyHash;
    uint256 internal fee;
    uint256 public randomResult;
    
    function getRandomNumber() public returns (bytes32 requestId) {
        return requestRandomness(keyHash, fee);
    }
    
    function fulfillRandomness(bytes32 requestId, uint256 randomness) internal override {
        randomResult = randomness;
    }
}
```

### 1.2 安全最佳实践

#### 1. 使用最新的 Solidity 版本
```solidity
pragma solidity ^0.8.19; // 使用最新稳定版本
```

#### 2. 合理使用修饰器
```solidity
contract SecureContract {
    address public owner;
    bool public paused;
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }
    
    modifier whenNotPaused() {
        require(!paused, "Contract paused");
        _;
    }
    
    modifier validAddress(address _addr) {
        require(_addr != address(0), "Invalid address");
        _;
    }
    
    function criticalFunction(address _to) 
        public 
        onlyOwner 
        whenNotPaused 
        validAddress(_to) 
    {
        // 函数逻辑
    }
}
```

#### 3. 事件日志记录
```solidity
contract Auditable {
    event Transfer(address indexed from, address indexed to, uint256 amount);
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);
    
    function transfer(address _to, uint256 _amount) public {
        // 转账逻辑
        emit Transfer(msg.sender, _to, _amount);
    }
}
```

#### 4. Pull over Push 模式
```solidity
// Push模式（不推荐）
contract Push {
    function sendPayments(address[] memory recipients) public {
        for(uint i = 0; i < recipients.length; i++) {
            recipients[i].transfer(1 ether); // 如果一个失败，全部失败
        }
    }
}

// Pull模式（推荐）
contract Pull {
    mapping(address => uint) public payments;
    
    function withdrawPayment() public {
        uint payment = payments[msg.sender];
        payments[msg.sender] = 0;
        payable(msg.sender).transfer(payment);
    }
}
```

## 二、ERC标准详解

### 2.1 ERC20 - 同质化代币标准

#### 标准接口
```solidity
interface IERC20 {
    // 查询总供应量
    function totalSupply() external view returns (uint256);
    
    // 查询账户余额
    function balanceOf(address account) external view returns (uint256);
    
    // 转账
    function transfer(address recipient, uint256 amount) external returns (bool);
    
    // 查询授权额度
    function allowance(address owner, address spender) external view returns (uint256);
    
    // 授权
    function approve(address spender, uint256 amount) external returns (bool);
    
    // 从授权账户转账
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);
    
    // 事件
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

#### 完整实现示例
```solidity
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyToken is ERC20, Ownable {
    uint256 public constant INITIAL_SUPPLY = 1000000 * 10**18; // 100万个代币
    
    constructor() ERC20("MyToken", "MTK") {
        _mint(msg.sender, INITIAL_SUPPLY);
    }
    
    // 增发功能（仅owner）
    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }
    
    // 销毁功能
    function burn(uint256 amount) public {
        _burn(msg.sender, amount);
    }
}
```

#### 高级功能实现
```solidity
contract AdvancedToken is ERC20 {
    // 添加暂停功能
    bool public paused;
    
    modifier whenNotPaused() {
        require(!paused, "Token paused");
        _;
    }
    
    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 amount
    ) internal override whenNotPaused {
        super._beforeTokenTransfer(from, to, amount);
    }
    
    // 添加黑名单功能
    mapping(address => bool) public blacklist;
    
    function addToBlacklist(address account) public onlyOwner {
        blacklist[account] = true;
    }
    
    function transfer(address recipient, uint256 amount) 
        public 
        override 
        returns (bool) 
    {
        require(!blacklist[msg.sender], "Sender blacklisted");
        require(!blacklist[recipient], "Recipient blacklisted");
        return super.transfer(recipient, amount);
    }
}
```

### 2.2 ERC721 - 非同质化代币(NFT)标准

#### 标准接口
```solidity
interface IERC721 {
    // 查询owner拥有的NFT数量
    function balanceOf(address owner) external view returns (uint256);
    
    // 查询tokenId的owner
    function ownerOf(uint256 tokenId) external view returns (address);
    
    // 安全转账（会检查接收方是否能处理NFT）
    function safeTransferFrom(address from, address to, uint256 tokenId) external;
    function safeTransferFrom(address from, address to, uint256 tokenId, bytes memory data) external;
    
    // 普通转账
    function transferFrom(address from, address to, uint256 tokenId) external;
    
    // 授权
    function approve(address to, uint256 tokenId) external;
    function setApprovalForAll(address operator, bool approved) external;
    function getApproved(uint256 tokenId) external view returns (address);
    function isApprovedForAll(address owner, address operator) external view returns (bool);
    
    // 事件
    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
    event Approval(address indexed owner, address indexed approved, uint256 indexed tokenId);
    event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
}
```

#### NFT实现示例
```solidity
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract MyNFT is ERC721URIStorage {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;
    
    uint256 public constant MAX_SUPPLY = 10000;
    uint256 public constant MINT_PRICE = 0.05 ether;
    
    constructor() ERC721("MyNFT", "MNFT") {}
    
    function mintNFT(string memory tokenURI) public payable returns (uint256) {
        require(_tokenIds.current() < MAX_SUPPLY, "Max supply reached");
        require(msg.value >= MINT_PRICE, "Insufficient payment");
        
        _tokenIds.increment();
        uint256 newItemId = _tokenIds.current();
        
        _safeMint(msg.sender, newItemId);
        _setTokenURI(newItemId, tokenURI);
        
        return newItemId;
    }
    
    // 批量铸造
    function batchMint(string[] memory tokenURIs) public payable {
        require(tokenURIs.length <= 10, "Max 10 NFTs per batch");
        require(msg.value >= MINT_PRICE * tokenURIs.length, "Insufficient payment");
        
        for (uint i = 0; i < tokenURIs.length; i++) {
            mintNFT(tokenURIs[i]);
        }
    }
    
    // 查询某地址拥有的所有NFT
    function tokensOfOwner(address owner) external view returns (uint256[] memory) {
        uint256 balance = balanceOf(owner);
        uint256[] memory tokens = new uint256[](balance);
        uint256 index = 0;
        
        for (uint256 i = 1; i <= _tokenIds.current(); i++) {
            if (ownerOf(i) == owner) {
                tokens[index] = i;
                index++;
            }
        }
        
        return tokens;
    }
}
```

### 2.3 ERC1155 - 多代币标准

结合了ERC20和ERC721的特性，可以在一个合约中管理多种代币。

```solidity
import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";

contract GameItems is ERC1155 {
    uint256 public constant GOLD = 0;
    uint256 public constant SILVER = 1;
    uint256 public constant SWORD = 2;
    uint256 public constant SHIELD = 3;
    
    constructor() ERC1155("https://game.example/api/item/{id}.json") {
        _mint(msg.sender, GOLD, 10000, "");      // 同质化代币
        _mint(msg.sender, SILVER, 50000, "");    // 同质化代币
        _mint(msg.sender, SWORD, 1, "");         // NFT
        _mint(msg.sender, SHIELD, 1, "");        // NFT
    }
}
```

## 三、Hardhat开发环境

### 3.1 安装和配置

```bash
# 创建项目目录
mkdir my-hardhat-project
cd my-hardhat-project

# 初始化npm项目
npm init -y

# 安装Hardhat
npm install --save-dev hardhat

# 初始化Hardhat项目
npx hardhat

# 安装常用插件
npm install --save-dev @nomiclabs/hardhat-ethers ethers
npm install --save-dev @nomiclabs/hardhat-waffle
npm install --save-dev @openzeppelin/contracts
```

### 3.2 Hardhat配置文件

```javascript
// hardhat.config.js
require("@nomiclabs/hardhat-waffle");
require("@nomiclabs/hardhat-etherscan");
require("dotenv").config();

module.exports = {
  solidity: {
    version: "0.8.19",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  },
  networks: {
    hardhat: {
      chainId: 1337
    },
    goerli: {
      url: process.env.GOERLI_URL || "",
      accounts: process.env.PRIVATE_KEY ? [process.env.PRIVATE_KEY] : []
    },
    mainnet: {
      url: process.env.MAINNET_URL || "",
      accounts: process.env.PRIVATE_KEY ? [process.env.PRIVATE_KEY] : []
    }
  },
  etherscan: {
    apiKey: process.env.ETHERSCAN_API_KEY
  },
  paths: {
    sources: "./contracts",
    tests: "./test",
    cache: "./cache",
    artifacts: "./artifacts"
  }
};
```

### 3.3 开发工作流

#### 1. 编写智能合约
```solidity
// contracts/MyContract.sol
pragma solidity ^0.8.0;

contract MyContract {
    string public message;
    
    constructor(string memory _message) {
        message = _message;
    }
    
    function updateMessage(string memory _newMessage) public {
        message = _newMessage;
    }
}
```

#### 2. 编写测试
```javascript
// test/MyContract.test.js
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("MyContract", function () {
  let myContract;
  let owner;
  
  beforeEach(async function () {
    [owner] = await ethers.getSigners();
    
    const MyContract = await ethers.getContractFactory("MyContract");
    myContract = await MyContract.deploy("Hello, Hardhat!");
    await myContract.deployed();
  });
  
  it("Should return the initial message", async function () {
    expect(await myContract.message()).to.equal("Hello, Hardhat!");
  });
  
  it("Should update the message", async function () {
    await myContract.updateMessage("New message");
    expect(await myContract.message()).to.equal("New message");
  });
});
```

#### 3. 部署脚本
```javascript
// scripts/deploy.js
async function main() {
  const [deployer] = await ethers.getSigners();
  
  console.log("Deploying contracts with account:", deployer.address);
  console.log("Account balance:", (await deployer.getBalance()).toString());
  
  const MyContract = await ethers.getContractFactory("MyContract");
  const myContract = await MyContract.deploy("Hello, Blockchain!");
  
  await myContract.deployed();
  
  console.log("Contract deployed to:", myContract.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

### 3.4 常用Hardhat命令

```bash
# 编译合约
npx hardhat compile

# 运行测试
npx hardhat test

# 启动本地节点
npx hardhat node

# 部署到本地
npx hardhat run scripts/deploy.js --network localhost

# 部署到测试网
npx hardhat run scripts/deploy.js --network goerli

# 验证合约
npx hardhat verify --network goerli CONTRACT_ADDRESS "Constructor arg1"

# 控制台交互
npx hardhat console --network localhost
```

## 四、实战项目：NFT市场合约

### 4.1 市场合约设计

```solidity
// contracts/NFTMarketplace.sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/IERC721.sol";
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract NFTMarketplace is ReentrancyGuard, Ownable {
    struct Listing {
        address seller;
        address nftContract;
        uint256 tokenId;
        uint256 price;
        bool active;
    }
    
    uint256 public listingCounter;
    uint256 public platformFee = 250; // 2.5%
    
    mapping(uint256 => Listing) public listings;
    mapping(address => mapping(uint256 => uint256)) public nftToListingId;
    
    event ListingCreated(
        uint256 indexed listingId,
        address indexed seller,
        address indexed nftContract,
        uint256 tokenId,
        uint256 price
    );
    
    event ListingSold(
        uint256 indexed listingId,
        address indexed buyer,
        uint256 price
    );
    
    event ListingCancelled(uint256 indexed listingId);
    
    // 创建挂单
    function createListing(
        address _nftContract,
        uint256 _tokenId,
        uint256 _price
    ) external {
        require(_price > 0, "Price must be greater than 0");
        
        IERC721 nft = IERC721(_nftContract);
        require(nft.ownerOf(_tokenId) == msg.sender, "Not the owner");
        require(
            nft.isApprovedForAll(msg.sender, address(this)) || 
            nft.getApproved(_tokenId) == address(this),
            "Marketplace not approved"
        );
        
        listingCounter++;
        listings[listingCounter] = Listing({
            seller: msg.sender,
            nftContract: _nftContract,
            tokenId: _tokenId,
            price: _price,
            active: true
        });
        
        nftToListingId[_nftContract][_tokenId] = listingCounter;
        
        emit ListingCreated(
            listingCounter,
            msg.sender,
            _nftContract,
            _tokenId,
            _price
        );
    }
    
    // 购买NFT
    function buyNFT(uint256 _listingId) external payable nonReentrant {
        Listing storage listing = listings[_listingId];
        require(listing.active, "Listing not active");
        require(msg.value >= listing.price, "Insufficient payment");
        
        listing.active = false;
        
        // 计算平台费用
        uint256 fee = (listing.price * platformFee) / 10000;
        uint256 sellerProceeds = listing.price - fee;
        
        // 转移NFT
        IERC721(listing.nftContract).safeTransferFrom(
            listing.seller,
            msg.sender,
            listing.tokenId
        );
        
        // 支付给卖家
        (bool success, ) = payable(listing.seller).call{value: sellerProceeds}("");
        require(success, "Transfer to seller failed");
        
        // 退还多余的ETH
        if (msg.value > listing.price) {
            (bool refundSuccess, ) = payable(msg.sender).call{
                value: msg.value - listing.price
            }("");
            require(refundSuccess, "Refund failed");
        }
        
        emit ListingSold(_listingId, msg.sender, listing.price);
    }
    
    // 取消挂单
    function cancelListing(uint256 _listingId) external {
        Listing storage listing = listings[_listingId];
        require(listing.seller == msg.sender, "Not the seller");
        require(listing.active, "Listing not active");
        
        listing.active = false;
        delete nftToListingId[listing.nftContract][listing.tokenId];
        
        emit ListingCancelled(_listingId);
    }
    
    // 更新价格
    function updatePrice(uint256 _listingId, uint256 _newPrice) external {
        Listing storage listing = listings[_listingId];
        require(listing.seller == msg.sender, "Not the seller");
        require(listing.active, "Listing not active");
        require(_newPrice > 0, "Price must be greater than 0");
        
        listing.price = _newPrice;
    }
    
    // 查询挂单信息
    function getActiveListing(address _nftContract, uint256 _tokenId) 
        external 
        view 
        returns (Listing memory) 
    {
        uint256 listingId = nftToListingId[_nftContract][_tokenId];
        require(listings[listingId].active, "No active listing");
        return listings[listingId];
    }
    
    // Owner功能：更新平台费率
    function setPlatformFee(uint256 _fee) external onlyOwner {
        require(_fee <= 1000, "Fee too high"); // 最高10%
        platformFee = _fee;
    }
    
    // Owner功能：提取平台费用
    function withdrawFees() external onlyOwner {
        uint256 balance = address(this).balance;
        require(balance > 0, "No fees to withdraw");
        
        (bool success, ) = payable(owner()).call{value: balance}("");
        require(success, "Withdrawal failed");
    }
}
```

### 4.2 市场合约测试

```javascript
// test/NFTMarketplace.test.js
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("NFT Marketplace", function () {
  let marketplace;
  let nft;
  let owner, seller, buyer;
  const TOKEN_ID = 1;
  const LISTING_PRICE = ethers.utils.parseEther("1");
  
  beforeEach(async function () {
    [owner, seller, buyer] = await ethers.getSigners();
    
    // 部署NFT合约
    const NFT = await ethers.getContractFactory("MyNFT");
    nft = await NFT.deploy();
    await nft.deployed();
    
    // 部署市场合约
    const Marketplace = await ethers.getContractFactory("NFTMarketplace");
    marketplace = await Marketplace.deploy();
    await marketplace.deployed();
    
    // 给seller铸造一个NFT
    await nft.connect(seller).mintNFT("https://example.com/token/1");
    
    // 授权市场合约
    await nft.connect(seller).approve(marketplace.address, TOKEN_ID);
  });
  
  describe("Listing", function () {
    it("Should create a listing", async function () {
      await expect(
        marketplace.connect(seller).createListing(
          nft.address,
          TOKEN_ID,
          LISTING_PRICE
        )
      ).to.emit(marketplace, "ListingCreated");
      
      const listing = await marketplace.listings(1);
      expect(listing.seller).to.equal(seller.address);
      expect(listing.price).to.equal(LISTING_PRICE);
      expect(listing.active).to.be.true;
    });
    
    it("Should fail if not owner", async function () {
      await expect(
        marketplace.connect(buyer).createListing(
          nft.address,
          TOKEN_ID,
          LISTING_PRICE
        )
      ).to.be.revertedWith("Not the owner");
    });
  });
  
  describe("Buying", function () {
    beforeEach(async function () {
      await marketplace.connect(seller).createListing(
        nft.address,
        TOKEN_ID,
        LISTING_PRICE
      );
    });
    
    it("Should complete a purchase", async function () {
      await expect(
        marketplace.connect(buyer).buyNFT(1, { value: LISTING_PRICE })
      ).to.emit(marketplace, "ListingSold");
      
      expect(await nft.ownerOf(TOKEN_ID)).to.equal(buyer.address);
      
      const listing = await marketplace.listings(1);
      expect(listing.active).to.be.false;
    });
    
    it("Should distribute payments correctly", async function () {
      const sellerBalanceBefore = await seller.getBalance();
      const platformFee = LISTING_PRICE.mul(250).div(10000); // 2.5%
      const sellerProceeds = LISTING_PRICE.sub(platformFee);
      
      await marketplace.connect(buyer).buyNFT(1, { value: LISTING_PRICE });
      
      const sellerBalanceAfter = await seller.getBalance();
      expect(sellerBalanceAfter.sub(sellerBalanceBefore)).to.equal(sellerProceeds);
      
      expect(await ethers.provider.getBalance(marketplace.address)).to.equal(platformFee);
    });
  });
});
```

## 五、今日学习总结

### 5.1 关键知识点回顾

1. **智能合约安全性**
   - 重入攻击及防范（CEI模式、重入锁）
   - 整数溢出处理（Solidity 0.8+自动检查）
   - 访问控制（modifier的正确使用）
   - 随机数安全（使用预言机）

2. **ERC标准深入理解**
   - ERC20：同质化代币，适用于货币、积分等
   - ERC721：非同质化代币，每个token独一无二
   - ERC1155：混合标准，一个合约管理多种代币

3. **Hardhat开发环境**
   - 项目初始化和配置
   - 测试驱动开发（TDD）
   - 部署和验证流程
   - 本地开发最佳实践

4. **实战项目经验**
   - NFT市场合约设计
   - 手续费机制实现
   - 安全的资金处理
   - 完整的测试覆盖

# 2025-08-11

# DApp学习笔记

## 一、DApp架构

### 1.1 传统Web应用 vs DApp架构对比

**传统Web应用：**
```
用户 → 前端 → 后端服务器 → 数据库
```

**DApp架构：**
```
用户 → 前端 → Web3.js/Ethers.js → 智能合约 → 区块链
     ↓
   钱包（MetaMask）
```

### 1.2 DApp的核心组件详解

#### 1. 智能合约层
- **作用**：业务逻辑、数据存储、状态管理
- **特点**：不可篡改、自动执行、公开透明
- **限制**：Gas费用、存储成本高、执行速度慢

#### 2. 前端交互层
- **技术栈**：React/Vue/Angular + Web3.js/Ethers.js
- **功能**：展示UI、与合约交互、处理用户操作
- **关键点**：需要处理异步操作、交易确认等待

#### 3. 钱包连接层
- **常用钱包**：MetaMask、WalletConnect、Coinbase Wallet
- **作用**：管理私钥、签名交易、支付Gas费

## 二、Web3.js 基础使用

### 2.1 安装和初始化

```javascript
// 安装
npm install web3

// 初始化Web3
import Web3 from 'web3';

// 检查是否有钱包注入
if (typeof window.ethereum !== 'undefined') {
    // 使用MetaMask的provider
    const web3 = new Web3(window.ethereum);
} else {
    // 使用远程节点
    const web3 = new Web3('https://mainnet.infura.io/v3/YOUR_PROJECT_ID');
}
```

### 2.2 连接钱包

```javascript
// 请求用户授权连接钱包
async function connectWallet() {
    try {
        // 请求账户访问
        const accounts = await window.ethereum.request({ 
            method: 'eth_requestAccounts' 
        });
        
        console.log('连接成功，账户地址：', accounts[0]);
        return accounts[0];
    } catch (error) {
        console.error('用户拒绝连接：', error);
    }
}

// 监听账户变化
window.ethereum.on('accountsChanged', (accounts) => {
    console.log('账户已切换：', accounts[0]);
});

// 监听网络变化
window.ethereum.on('chainChanged', (chainId) => {
    console.log('网络已切换：', chainId);
    window.location.reload(); // 建议刷新页面
});
```

### 2.3 与智能合约交互

```javascript
// 合约ABI（应用二进制接口）- 描述合约的接口
const contractABI = [
    {
        "inputs": [],
        "name": "getMessage",
        "outputs": [{"name": "", "type": "string"}],
        "type": "function"
    },
    {
        "inputs": [{"name": "newMessage", "type": "string"}],
        "name": "setMessage",
        "outputs": [],
        "type": "function"
    }
];

// 合约地址
const contractAddress = '0x1234...';

// 创建合约实例
const contract = new web3.eth.Contract(contractABI, contractAddress);

// 读取数据（不需要Gas）
async function readMessage() {
    const message = await contract.methods.getMessage().call();
    console.log('当前消息：', message);
    return message;
}

// 写入数据（需要Gas）
async function writeMessage(newMessage) {
    const accounts = await web3.eth.getAccounts();
    
    try {
        const result = await contract.methods.setMessage(newMessage).send({
            from: accounts[0],
            gas: 150000 // 估算的Gas限制
        });
        
        console.log('交易成功：', result.transactionHash);
    } catch (error) {
        console.error('交易失败：', error);
    }
}
```

## 三、简单DApp实战：留言板

### 3.1 智能合约

```solidity
// MessageBoard.sol
pragma solidity ^0.8.0;

contract MessageBoard {
    struct Message {
        address sender;
        string content;
        uint256 timestamp;
    }
    
    Message[] public messages;
    
    event NewMessage(address indexed sender, string content, uint256 timestamp);
    
    function postMessage(string memory _content) public {
        messages.push(Message({
            sender: msg.sender,
            content: _content,
            timestamp: block.timestamp
        }));
        
        emit NewMessage(msg.sender, _content, block.timestamp);
    }
    
    function getMessages() public view returns (Message[] memory) {
        return messages;
    }
    
    function getMessageCount() public view returns (uint256) {
        return messages.length;
    }
}
```

### 3.2 前端代码（React示例）

```jsx
import React, { useState, useEffect } from 'react';
import Web3 from 'web3';

function MessageBoard() {
    const [web3, setWeb3] = useState(null);
    const [account, setAccount] = useState('');
    const [contract, setContract] = useState(null);
    const [messages, setMessages] = useState([]);
    const [newMessage, setNewMessage] = useState('');
    const [loading, setLoading] = useState(false);

    // 初始化Web3和合约
    useEffect(() => {
        async function init() {
            if (window.ethereum) {
                const web3Instance = new Web3(window.ethereum);
                setWeb3(web3Instance);
                
                // 合约信息
                const contractABI = [...]; // 你的合约ABI
                const contractAddress = '0x...'; // 部署的合约地址
                
                const contractInstance = new web3Instance.eth.Contract(
                    contractABI, 
                    contractAddress
                );
                setContract(contractInstance);
            }
        }
        init();
    }, []);

    // 连接钱包
    async function connectWallet() {
        try {
            const accounts = await window.ethereum.request({ 
                method: 'eth_requestAccounts' 
            });
            setAccount(accounts[0]);
            loadMessages(); // 连接后加载消息
        } catch (error) {
            alert('请安装MetaMask！');
        }
    }

    // 加载所有消息
    async function loadMessages() {
        if (contract) {
            const msgs = await contract.methods.getMessages().call();
            setMessages(msgs);
        }
    }

    // 发送新消息
    async function sendMessage() {
        if (!contract || !account) return;
        
        setLoading(true);
        try {
            await contract.methods.postMessage(newMessage).send({
                from: account,
                gas: 200000
            });
            
            setNewMessage('');
            await loadMessages(); // 重新加载消息
            alert('消息发送成功！');
        } catch (error) {
            alert('发送失败：' + error.message);
        }
        setLoading(false);
    }

    return (
        <div className="message-board">
            <h1>区块链留言板</h1>
            
            {!account ? (
                <button onClick={connectWallet}>连接钱包</button>
            ) : (
                <p>已连接账户：{account}</p>
            )}
            
            <div className="new-message">
                <input
                    type="text"
                    value={newMessage}
                    onChange={(e) => setNewMessage(e.target.value)}
                    placeholder="输入你的留言..."
                />
                <button 
                    onClick={sendMessage} 
                    disabled={loading || !account}
                >
                    {loading ? '发送中...' : '发送留言'}
                </button>
            </div>
            
            <div className="messages">
                <h2>所有留言</h2>
                {messages.map((msg, index) => (
                    <div key={index} className="message">
                        <p><strong>发送者：</strong>{msg.sender}</p>
                        <p><strong>内容：</strong>{msg.content}</p>
                        <p><strong>时间：</strong>
                            {new Date(msg.timestamp * 1000).toLocaleString()}
                        </p>
                    </div>
                ))}
            </div>
        </div>
    );
}
```

# 2025-08-08

# 智能合约开发

## 一、Solidity

- 定义：Solidity 是一种专门为编写智能合约 (Smart Contracts) 而设计的高级编程语言。

- 用途：它主要用于在以太坊（Ethereum）以及其他兼容的区块链平台上创建和部署智能合约。

- 合约示例：

```solidity
// 指定 Solidity 编译器版本
pragma solidity ^0.8.0;

// 定义一个名为 HelloWorld 的合约
contract HelloWorld {
    
    // 定义一个 string 类型的状态变量来存储信息
    string private message;

    // 这是一个构造函数，当合约被部署到区块链上时，它会自动执行一次
    constructor() {
        message = "Hello, Web3!";
    }

    // 一个公开的函数，任何人都可以调用它来读取 message 的值
    function getMessage() public view returns (string memory) {
        return message;
    }

    // 一个公开的函数，允许我们修改 message 的值
    function setMessage(string memory newMessage) public {
        message = newMessage;
    }
}
```

### 基础语法

1. 基础数据类型

```solidity
    // 数值类型
    uint256 public number = 42;
    int256 public signedNumber = -42;

    // 布尔类型
    bool public flag = true;

    // 地址类型
    address public owner = 0x1234...;

    // 字符串和字节
    string public name = "Ethereum";
    bytes32 public hash;

    // 数组
    uint256[] public dynamicArray;
    uint256[5] public fixedArray;

    // 映射
    mapping(address => uint256) public balances;

    // 结构体
    struct User {
        string name;
        uint256 age;
        address wallet;
    }
```

2. 函数修饰符

```solidity
contract Modifiers {
    // 状态变量，用于存储合约所有者的地址。
    address public owner;

    constructor() {
      // 将 `msg.sender` (即部署合约的账户地址) 赋值给 `owner` 变量，将合约的部署者设置为所有者。
        owner = msg.sender;
    }

    modifier onlyOwner() {
        // 检查调用者是否是所有者
        require(msg.sender == owner, "Not owner");
        _; // 如果上面的 require 通过，则继续执行函数本身的代码
    }
    // 验证传入的地址是否为有效地址
    modifier validAddress(address _addr) {
        // 如果地址是零地址，则回滚交易。
        require(_addr != address(0), "Invalid address");
        _;
    }

    function restrictedFunction() 
        public  // public: 任何人都可以尝试调用此函数 (但会被修饰符拦截)
        onlyOwner  // 首先检查调用者是否是 owner
        validAddress(msg.sender) // 接着检查调用者地址是否有效
    {
        // 只有owner可以调用
    }
}
```

### 常见合约模式

1. ERC20代币合约

```solidity
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor(
        string memory name,
        string memory symbol,
        uint256 totalSupply
    ) ERC20(name, symbol) {
        _mint(msg.sender, totalSupply);
    }
}
```

2. 多重签名钱包

```solidity
contract MultiSig {
    address[] public owners;
    uint256 public requiredConfirmations;

    struct Transaction {
        address to;
        uint256 value;
        bytes data;
        bool executed;
        uint256 confirmations;
    }

    Transaction[] public transactions;
    mapping(uint256 => mapping(address => bool)) public isConfirmed;

    modifier onlyOwner() {
        require(isOwner(msg.sender), "Not owner");
        _;
    }

    function submitTransaction(
        address _to,
        uint256 _value,
        bytes memory _data
    ) public onlyOwner {
        // 提交交易逻辑
    }

    function confirmTransaction(uint256 _txIndex) public onlyOwner {
        // 确认交易逻辑
    }
}
```

## 二、 Dapp（Decentralized Application）去中心化应用

- 前端：一个看起来和普通网站或 App 差不多的界面。它也是用我们熟悉的技术（如 HTML, JavaScript, React）构建的。

- 后端：不是中心服务器，而是部署在区块链上的智能合约（就是我们之前说的用 Solidity 写的代码）。当你点击“交换代币”时，你的请求直接发送给区块链上的智能合约。

- 控制权：没有任何一个实体能单独控制。程序的规则（智能合约）公开透明地写在区块链上，只要满足条件就会自动执行，谁也无法篡改或阻止。

### DApp 的核心组成部分

1. 前端 (Frontend)：用户能看到和交互的界面。
2. 智能合约 (Smart Contracts)：这就是 DApp 的**“后端灵魂”**。所有核心的业务逻辑、规则和状态都记录在这里，并由 Solidity 等语言编写，运行在以太坊等区块链上。
3. 资产管理：你的加密货币、NFT 等资产都存放在钱包地址里。

# 2025-08-07

# web3学习笔记

## 法律与监管框架

### 法律框架与安全

法律环境概览

- 禁止 ICO（首次代币发行）
- 禁止虚拟货币交易所运营与提供撮合服务；
- 打击以虚拟货币为载体的非法集资、传销、洗钱、赌博等活动
- 境内使用人民币购买USDT,ETH等虚拟币，通过链上或境外平台兑换美元等外币，绕过了外汇监管，在不知情的情况下被卷入“协助洗钱”的刑事链条，交易必须谨慎

监管背景与趋势

- 分国家/地区监管政策对比

| 国家/地区    | 核心监管机构 | 总结 |
| ----------- | ----------- | ----------- |
| 美国      | SEC, CFTC, OCC       | 将比特币和以太坊视为商品、 将大部分代币视为证券进行监管、允许银行提供加密货币托管服务     |
| 欧盟   | ESMA        | MiCA（加密资产市场法规）      |
| 香港   | VASP、稳定币监管        | 要求平台实施严格的 KYC/AML 措施、要求稳定币发行商获得香港金管局许可      |

### 法律风险防范

1. 许多项目方在中国境内无注册公司，无法依法缴纳五险一金，难以依照《劳动合同法》享受合法保障。
2. 薪酬结构“人民币 + Token”或“全 USDT”，特别注意注意，用自发 Token 支付薪资的项目，其代币价值波动剧烈，可能归零。
3. 虚拟货币出金与合规风险，虚拟货币兑换为人民币为出金，出金过程中极易卷入刑事风险，务必通过可信渠道进行出金。

## 典型攻击案例与安全防范

1. 攻击类型分类

- 智能合约漏洞 (Smart Contract Vulnerabilities):
 重入攻击、整数溢出
- 协议逻辑错误 (Protocol Logic Errors):
  闪电贷攻击
- 外部安全风险 (External Security Risks):
  私钥泄露、钓鱼攻击、Rug Pull

2. 防护清单

- 只用官方 Zoom/Teams/腾讯会议等公开工具
- 任何要求“连接钱包”“签名验证”“输入助记词/私钥”的网页，99% 是骗局
- 不轻信群内“官方人员”“管理员”“学长学姐”的私聊和链接
- 重要账户启用 2FA（短信易被劫持，优选谷歌验证或硬件密钥）；不要在同一浏览器里保存助记词与日常 Cookie。

# 2025-08-06

# Web3 前端工程师的岗位职责

1. 核心技能与职责
构建用户界面 (UI)：设计和实现直观、响应式的用户界面，确保用户能够轻松地与 DApp 交互。
状态管理与数据流：处理 DApp 中的复杂状态，包括用户钱包连接状态、智能合约数据、交易状态等。与区块链交互：通过特定的库（如 Ethers.js 或 Web3.js）与智能合约进行通信，发送交易、读取链上数据。
钱包集成：集成主流的加密货币钱包（如 MetaMask、WalletConnect），使用户能够连接自己的钱包并授权交易。
安全性考量：在前端层面也要考虑安全性，防止常见的攻击，并确保用户资产的安全。
2. 常用前端技术栈React：
作为目前最流行的前端框架之一，React 因其组件化、声明式编程和高效的虚拟 DOM 而成为 DApp 开发的首选。
3. 了解常用的区块链网络理解不同区块链网络的特性对于选择合适的开发平台至关重要。

以太坊 (Ethereum)：智能合约平台：第一个支持智能合约的区块链，拥有最成熟的开发者生态系统。EVM 兼容：许多其他区块链（如 Polygon, Binance Smart Chain）都与以太坊虚拟机 (EVM) 兼容，这意味着在以太坊上开发的智能合约和工具可以轻松迁移。
Gas 费用：交易需要支付 Gas 费，费用会根据网络拥堵程度波动。
PoS 机制：已从工作量证明 (PoW) 转向权益证明 (PoS)，提高了效率和可扩展性。

Solana：高性能：以其极高的交易吞吐量和低廉的交易费用而闻名。
Rust 语言：智能合约主要使用 Rust 语言编写，与以太坊的 Solidity 不同。
生态发展：近年来生态系统迅速发展，吸引了大量开发者和项目。

4. DApp 开发经验者优先具备 DApp 开发经验意味着你对整个 Web3 应用的生命周期有实践经验，
包括：钱包集成与交互：熟练使用 Web3 库连接钱包并调用智能合约方法。
链上数据处理：能够从区块链上获取和解析数据，并在前端进行展示。
交易签名与发送：理解交易的生命周期，包括签名、广播和确认。
去中心化思维：理解去中心化应用的独特挑战和优势。
智能合约开发智能合约是运行在区块链上的代码，它们定义了 DApp 的核心业务逻辑。

Solidity 编程语言以太坊智能合约语言：Solidity 是编写以太坊兼容区块链（如以太坊、Polygon、BNB Chain 等）上智能合约的主要语言。
面向合约：它是一种静态类型、面向合约的高级语言，语法类似于 JavaScript。
关键概念：学习其数据类型、函数、修饰符、事件、错误处理、继承等核心概念。
安全性：智能合约一旦部署就无法更改，因此安全性至关重要。学习常见的安全漏洞（如重入攻击、整数溢出）及防范措施。

 需求分析在编写智能合约之前，进行彻底的需求分析是必不可少的。
 明确业务逻辑：理解 DApp 的核心功能、用户角色和交互流程。
 定义合约功能：确定智能合约需要实现哪些函数、存储哪些数据、触发哪些事件。考虑可扩展性与升级：规划合约未来的升级路径（如果需要）。
 
 智能合约开发开发环境：使用 Remix IDE、Hardhat 或 Foundry 等工具进行开发和测试。
 编写合约代码：根据需求分析编写 Solidity 代码，实现 DApp 的核心逻辑。
 测试：编写单元测试和集成测试，确保合约在各种场景下都能按预期工作，并且没有安全漏洞。
 优化 Gas 消耗：由于链上操作需要 Gas 费，编写高效的合约代码可以降低用户成本。
 
 前端界面开发，链接钱包，与区块链交互这部分是前端和智能合约的结合点。
 集成 Web3 库：在前端项目中使用 Ethers.js 来与区块链网络和智能合约进行通信。
 连接钱包：通过库提供的 API，实现用户钱包（如 MetaMask）的连接功能。
 调用合约方法：读取数据 (Call)：调用合约的 view 或 pure 函数，这些操作不修改链上状态，不需要 Gas 费。
 发送交易 (Send)：调用合约的 payable 或修改状态的函数，这些操作会修改链上状态，需要用户签名并支付 Gas 费。
 监听事件：订阅智能合约发出的事件，以便在前端实时更新 UI。
 
 部署上线选择网络：决定将合约部署到哪个区块链网络（测试网如 Sepolia、主网如 Ethereum Mainnet）。部署工具：使用 Hardhat、Foundry 或 Remix 等工具将编译好的合约字节码部署到区块链上。验证合约：在 Etherscan 等区块链浏览器上验证合约代码，提高透明度和用户信任。

# 2025-08-05

以太坊和BTC的区别：
1.以太坊有图灵完备的编程语言，可开发复杂智能合约；BTC只支持脚本开发。
2.从工作量证明（PoW）转向权益证明（PoS）以前是矿工将新交易打包到新的区块，现在是从所有质押 ETH 的验证者中，随机选择一位验证者来创建下一个区块，该验证者负责将网络中的新交易打包成一个区块，并将其广播到网络中，其他验证者会检查这个区块是否有效，再将区块添加到链上
3.以太坊使用一种叫做 Solidity 的编程语言来编写智能合约，智能合约本质上就是一段能自动执行协议的，就像一个自动售货机，按照写好的程序来执行交易

# 2025-08-04

了解去中心化的思想，比特币的原理：区块主要数据就是一系列交易，Merkle Hash是每个交易的hash组合而来，篡改一个交易就要计算后续所有的hash，所以不可篡改。工作量证明：就是先给定一个难度值，不断计算，找到一个比给定难度值低的Hash。共识算法: 如果两个矿工在同一时间各自找到了有效区块,出现分叉，有的会在分叉1继续挖矿，有的会在分叉2继续挖矿，最终总有一个先挖到矿，最长分叉的共识算法，会废弃一个分叉，所有的矿工又继续在最长的链上挖矿。
交易原理：比特币只支持脚本编程，当a给b支付比特币时，是a创建一个锁定脚本，该脚本引入了b的地址，所以只有b能创建正确的解锁脚本，钱包软件创建交易实际上就是脚本编程，编写符合条件的脚本。
智能合约：当一个预先编好的条件被触发时，就如创建锁定脚本，输入解锁脚本，自动执行相应的程序，自动完成数字资产的转移


# 2025.08.01


<!-- Content_END -->
