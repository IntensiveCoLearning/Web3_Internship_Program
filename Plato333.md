---
timezone: UTC+8
---

# Polato

**GitHub ID:** Plato333

**Telegram:** @Nethpu

## Self-introduction

27. Polato 北京 大二计科在读，目前在学习solidity，对web3颇有了解，技术栈C++/JAVA,H5+JS,想认识人做一些有意思的项目，也有黑客松经验之后可以一起组队打比赛

## Notes

<!-- Content_START -->
# 2025-08-25

今天收听了大马哈鱼姐的web3职业信息分享，了解到web3行业目前求职现状，收获很多。依旧学习node.js相关知识

# 2025-08-24

今天依旧学习node.js相关，还有myfirstdapp很精彩收获很多hh。

# 2025-08-23

今天的主线依旧是学习node.j相关，然后参加分享会收获很多

# 2025-08-21

今天收听了周五例会收获很多学到了很多，然后今天也是主要在学习node.js相关的知识

# 2025-08-20

今天主要学习了node相关知识，第一次装nvm，然后听了分享会收获颇丰

# 2025-08-18

今天参加了以太坊中文周会收获了很多，目前solidity正在初步学习foundery框架。

# 2025-08-15

今天学习了有关接口合约的知识：
ERC20 代币标准：通过接口定义了 transfer、balanceOf 等函数，所有 ERC20 代币合约都实现这些函数，因此可以被交易所、钱包等统一处理。
solidity
interface IERC20 {
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address recipient, uint256 amount) external returns (bool);
}
这个合约任何实现了 IERC20 接口的代币，都能在支持 ERC20 的平台上通用。

# 2025-08-14

今天听了知识分享会收获很多，之后自己去深入了解了一下uinswapv1-v4的相关机制，然后就是本地部署了Hardhat。

# 2025-08-13

githubPR教程：
创建仓库

登录 GitHub → 右上角 “+” → New repository → 填名字、描述、选择 Public/Private → Create。

可以直接勾选 “Add README”、“.gitignore”、“LICENSE”。

上传文件 / 新建文件 / 编辑文件

进入仓库页面 → 右上 “Add file” 下拉：

Create new file：在网页编辑器里新建文件（支持直接输入代码、写 README）。

Upload files：直接把本地文件/文件夹拖拽上传（注意：超大文件可能受限）。

编辑后在页面底部填写 Commit message，选择提交到 main（直接合并）或新建分支（推荐用于改动较大或准备 PR）。

在网页上编辑现有文件

打开文件 → 点击铅笔图标（Edit）→ 修改 → Commit。非常适合修 README、小改动、修 typo。

分支 & Pull Request（完全网页操作）

在仓库页面的分支下拉可以 新建分支（Create branch）。

在你推送/提交到非 main 分支后，GitHub 会出现 Compare & pull request 按钮 → 填写说明 → Create Pull Request。

在 PR 页面可以：评论、要求改动、查看文件差异、合并（Merge）或关闭 PR。

维护者可以在网页上完成 Review、Approve、Request changes。

解决冲突（简单冲突可网页解决）

如果 PR 出现简单冲突，GitHub 会提供 Resolve conflicts 的网页编辑器，你可以在浏览器里手动修改冲突内容并提交。

复杂冲突建议用 VS Code / 本地工具处理会更直观。

Fork + PR（完全网页流程）

点击 Fork → 在你个人帐户里会生成副本 → 在你的 Fork 上直接在网页编辑或上传文件 → 推送分支后在原仓库发起 PR。

同步 Fork（网页按钮）

Fork 页面上通常会有 Fetch upstream 或 Sync fork 按钮，一键把原仓库最新合并到你 Fork 的 main（无需命令行）。

Issues / Projects / Wiki / Releases / Actions

新建 Issue、管理 Project 看板、创建 Release、启用 Actions（从模板）等都可以在网页上操作。

# 2025-08-12

今天主要学习了如何在测试网上部署自己的合约(在Remix环境中)，继续学习了一些solidity相关语法，之后又对Dapp的整个开发流程进行了了解，还有就是今晚的知识分享会也收获不少hh。

# 2025-08-11

今天主要学习了一下如何系统使用git以及github(很喜欢github上的contribute之后有小绿点这个机制，之前一直不会在github上push自己的东西)

# 2025-08-10

#用合约部署合约
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract SimpleStorage {

    uint256 myFavoriteNumber;//uint默认为零 

   //uint256[] listOfFavoriteNumber;//创建一个uint256数组
   struct Person{ //创建结构体
        uint256 favoriteNumber;
        string name;
   }
//    Person public pat = Person(7,"Pat");//或者这样写也可以Person({favoriteNumber: 7, name: "Pat"})
    Person[] public listOfPeople;//[]里没有数字表示动态数组

    mapping(string => uint256) public nameToFavoriteNumber; 

    function store(uint256 _favoriteNumber) public {
        myFavoriteNumber = _favoriteNumber;
       
    }
   function retrieve() public view returns(uint256) {
        return myFavoriteNumber;
    }
    function addPerson(string memory _name, uint256 _favoriteNumber) public{
        listOfPeople.push(Person(_favoriteNumber, _name));
        //数组内置了一个名为push的函数，允许向数组内添加元素
        nameToFavoriteNumber[_name] = _favoriteNumber;
        //这段代码的意思是在映射nameToFavoriteNumber中，
        //把字符串_name映射到uint256 _favoriteNumber，
        //即任何时候输入某人的名称将自动获得对应喜欢的数字

    }
}

contract StorageFactory{//这个StorageFactory合约将部署一个SimpleStorage合约，所以需要创建一个函数来部署或创建SimpleStorage合约,
//但问题是StorageFactory如何知道SimpleStorage合约的样子呢？第一种方法是直接复制粘贴，但不希望在同一文件中存在多个合约这样会非常混乱，一般是一个文件一个合约，不过先暂定够用这种方法。
    SimpleStorage public simpleStorage;//左边是合约明成分右边是变量名称

    function createSimpleStorageContract() public {
            simpleStorage = new SimpleStorage();//new关键字会创建一个新的合约，在 Solidity 中，创建合约实例必须使用括号
            
    }

}
不过这种方式显得很麻烦，可以用import关键字来导入SimpleStorage.sol文件
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

import "./SimpleStorage.sol";

contract StorageFactory{//这个StorageFactory合约将部署一个SimpleStorage合约，所以需要创建一个函数来部署或创建SimpleStorage合约,
//但问题是StorageFactory如何知道SimpleStorage合约的样子呢？第一种方法是直接复制粘贴，但不希望在同一文件中存在多个合约这样会非常混乱，一般是一个文件一个合约，不过先暂定够用这种方法。
    SimpleStorage public simpleStorage;//左边是合约明成分右边是变量名称

    function createSimpleStorageContract() public {
            simpleStorage = new SimpleStorage();//new关键字会创建一个新的合约，在 Solidity 中，创建合约实例必须使用括号

    }

}
也可以使用命名导入(默认使用)这种方式来导入文件中特定的合约，如果一个文件中包含多个合约的话
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

import  {SimpleStorage, SimpleStorage2} from "./SimpleStorage.sol";
//想从SimpleStorage.sol文件中导入SimpleStorage, SimpleStorage2这两个合约

contract StorageFactory{//这个StorageFactory合约将部署一个SimpleStorage合约，所以需要创建一个函数来部署或创建SimpleStorage合约,
//但问题是StorageFactory如何知道SimpleStorage合约的样子呢？第一种方法是直接复制粘贴，但不希望在同一文件中存在多个合约这样会非常混乱，一般是一个文件一个合约，不过先暂定够用这种方法。
    SimpleStorage public simpleStorage;//左边是合约明成分右边是变量名称

    function createSimpleStorageContract() public {
            simpleStorage = new SimpleStorage();//new关键字会创建一个新的合约，在 Solidity 中，创建合约实例必须使用括号

    }

}

# 2025-08-09

# Memory，Storage，Calldata

- `memory` 数据由 EVM 自动管理，函数调用结束后销毁。无gas费(内存)
- `storage` 数据永久存储，需手动修改或覆盖。gas费(存储器)
- **`calldata`** 外部函数调用时，参数数据被临时存储在 **调用数据区域**（Call Data），这是一个只读的特殊区域，仅在函数调用期间存在，调用结束后数据被销毁，不可持久化，**只读**（类似 `constant`），不可修改。

  总的来说，memory和calldata都表示该变量仅存在于临时内存中，只会在函数调用期间存在，区别在于memory变量是可以进行修改的临时变量，calldata是不可修改的临时变量，而storage是不可修改的永久变量。

引用类型必须指明类型 `memory`、`storage` 或 `calldata`，默认`memory`。

```solidity
// 引用类型示例
uint256[] memory arr = new uint256[](3); // 动态数组（memory）
string memory text = "hello";            // 字符串（动态长度）
struct Person { string name; uint256 age; } // 结构体
mapping(address => uint256) balances;    // 映射（默认 storage）
```

# 映射
// SPDX-License-Identifier: MIT
pragma solidity  0.8.18; //声明solidity版本

contract SimpleStorage {

    uint256 myFavoriteNumber;//uint默认为零 

   //uint256[] listOfFavoriteNumber;//创建一个uint256数组
   struct Person{ //创建结构体
        uint256 favoriteNumber;
        string name;
   }
//    Person public pat = Person(7,"Pat");//或者这样写也可以Person({favoriteNumber: 7, name: "Pat"})
    Person[] public listOfPeople;//[]里没有数字表示动态数组

//姓名 -> 喜欢的数字
    mapping(string => uint256) public nameToFavoriteNumber; 

    function store(uint256 _favoriteNumber) public {
        myFavoriteNumber = _favoriteNumber;
       
    }
   function retrieve() public view returns(uint256) {
        return myFavoriteNumber;
    }
    function addPerson(string memory _name, uint256 _favoriteNumber) public{
        listOfPeople.push(Person(_favoriteNumber, _name));
        //数组内置了一个名为push的函数，允许向数组内添加元素
        nameToFavoriteNumber[_name] = _favoriteNumber;
        //这段代码的意思是在映射nameToFavoriteNumber中，任何时候输入某人的名称将自动获得对应喜欢的数字

    }
}

# 2025-08-08

# 众筹合约

  传统众筹是 “中心化平台 + 信任中介” 的模式，而区块链众筹合约是 “代码即法律 + 自动执行” 的模式。它不仅解决了传统众筹的信任痛点，更通过可编程性创造了全新的经济模型（如代币经济、DAO 治理），让普通人真正成为项目的 “所有者” 而非单纯的 “支持者”。这正是 Web3“去中心化、用户共创” 精神的核心体现。

```solidity
//首先明确这个合约要实现的功能:
//1.从用户那里获取资金
//2.并将资金提取
//3.设定一个以美元为单位的最小资助值

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract FundMe{
    //这是我们的FundMe主要进行交互的两个函数，将要实现的函数比这个多
    function fund() public{}

    function withdraw() public{}
    
}
```

### 通过函数发送ETH

```solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract FundMe{
   //允许用户发送代币
   //有一个最低发送额
   //首先第一个问题：如何向这个合约发送以太币，当用户调用fundme函数时以太币如何自动发送到合约当中
   //部署模块中的vaule字段表示的是每个交易中发送的本地区块链加密货币的数量
   //为了让solidity中的函数能够接受区块链货币，将函数设置为payable，就像钱包持有资金一样合约也可以持有资金
   function fun() public payable{
        //如果想要希望用户在使用fund函数时至少花费一个以太币，这样写 
   require(msg.value > 1e18, "didn't send enough ETH");
   msg。value是用来访问交易的金额,require作用类似于一个检查器，检查ether是否大于1，若不是则回滚此交易,""里面是回滚消息
   1e18 Wei = 1 ETH = 1000000000000000000 Wei= 1 * 10 ** 18 = 1 * 10 ** 9 Gwei，双星号是幂运算符

   }
   
}
```

### 回滚

回滚会撤销之前已执行的所有操作并将与该交易相关联的剩余gas退还，但是如果发送了一个被回滚的交易，仍然会消耗gas费

```solidity
	
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract FundMe{

   uint256 public myValue = 1;
   
   function fun() public payable{
 
      myValue += 2; 
   require(msg.value > 1e18, "didn't send enough ETH");
   

   }
   
}
```

我们发送的每一笔交易都有这些字段:

Noce，Gas Price, Gas Limit, To, Value, Data, v,r,s

# 2025-08-07

### 两个函数sfStore()和sfGet():

# sfStore()**关键点**

1. **合约选择**：
    - `_SimpleStorageIndex` 参数指定要操作的合约索引（如 `0` 对应第一个部署的合约）。(_SimpleStorageIndex代指索引数字)
2. **跨合约调用**：
    - `mySimpleStorage.store(...)` 相当于向目标合约发送一条消息，触发其 `store()` 函数。
    - 类似现实中 “通过楼号找到某栋楼，并在该楼的登记簿上写入数据”。

# sfGet()**关键点**

1. **只读操作**：
    - `view` 修饰符表示该函数不修改区块链状态，仅读取数据。
2. **数据返回**：
    - 返回值来自目标合约的 `retrieve()` 函数，即 `SimpleStorage` 中存储的 `myFavoriteNumber`。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

import {SimpleStorage} from "./SimpleStorage.sol";

contract StorageFactory{
    //listOfSimpleStorageContracts 数组存储所有已创建的合约地址
    SimpleStorage[] public listOfSimpleStorageContracts;

    function createSimpleStorageContract() public {
            SimpleStorage newSimpleStorageContract = new SimpleStorage();//new关键字会创建一个新的合约，在 Solidity 中，创建合约实例必须使用括号
            listOfSimpleStorageContracts.push(newSimpleStorageContract);//将新合约的地址添加到数组中
    }

		//通过索引选择一个 SimpleStorage 合约，并调用其 store() 函数更新数据。
    function sfStore(uint256 _SimpleStorageIndex, uint256 _newSimpleStorageNumber) public {//sf代表StorageFactory
				     // 1. 从数组中获取指定索引的合约地址
            SimpleStorage mySimpleStorage = listOfSimpleStorageContracts[_SimpleStorageIndex];
             // 2. 调用该合约的 store() 函数，更新其内部数据
            mySimpleStorage.store(_newSimpleStorageNumber);
    }

    //通过索引选择一个 SimpleStorage 合约，并调用其 retrieve() 函数读取数据。
    function sfGet(uint256 _SimpleStorageIndex) public view returns(uint256){
				 // 1. 从数组中获取指定索引的合约地址    
        SimpleStorage mySimpleStorage = listOfSimpleStorageContracts[_SimpleStorageIndex];
        // 2. 调用该合约的 retrieve() 函数，读取其内部数据
        return mySimpleStorage.retrieve();

    }

}
```

# 2025-08-06

### D.零知识证明

**例子 ：保险箱密码证明**

**场景**

- 佩琪对保险箱有密码，想向维克托证明她确实知道密码，却不想泄露密码本身。

**协议**

1. 保险箱有两层门：外层（无锁，仅装饰）和内层（需要密码）。
2. 维克托在房间外，让佩琪到房间内打开箱子，并把一个标有随机数字的小纸条放入箱内，然后关好门。
3. 佩琪输入密码打开内层门，取出小纸条并贴到箱门外的白板上，证明她确实能打开内层。
4. 重复多次，用不同随机纸条——每一次维克托都信服佩琪知道密码，但他从贴出来的数字里无法反推出密码本身。

> 关键：每轮贴出来的都是随机挑战，维克托只确认“佩琪能开锁”，但密码始终保密。
> 

 **现代应用场景**

1. **隐私保护的加密货币**
    - **Zcash**： 用 zk-SNARK 隐藏交易金额和双方地址，实现完全匿名转账。
2. **区块链扩容（zk-rollups）**
    - Layer 2 将大量交易打包，生成一个小巧的零知识证明提交到主链，既保证数据可用性，又大幅降低 Gas 费。
3. **身份与访问控制**
    - 用户无须暴露真实身份信息，就能证明自己年满 18 岁或持有某资格证书。
4. **合规审计与数据共享**
    - 企业可在不泄露敏感财务数据的前提下，向审计方或监管机构证明合规性。

### E.私钥与公钥

一对密钥（私钥和公钥），就像银行账户的密码和账号。

**对比**

| 概念 | 区块链公钥/私钥 | 银行账号/密码 |
| --- | --- | --- |
| **可见性** | 公钥公开、私钥保密 | 账号公开（可告知收款人）、密码保密 |
| **用途** | 公钥用来验证签名或加密；私钥用来签名或解密 | 账号用来指定收款目标；密码用来登录或授权操作 |
| **安全性依赖** | 私钥的随机性和长度保证无法被穷举破解 | 密码的复杂度和保密性决定账户安全 |
| **单向性** | 从私钥能算出公钥；但从公钥无法算出私钥 | 无法从账号算出密码，也不会从密码算出账号 |
| **权限控制** | 拥有私钥就拥有对对应地址全部资产的控制权 | 知道密码就能登录账户并进行转账、消费等操作 |
| **恢复机制** | 一般通过助记词（Mnemonic）或密钥备份恢复私钥 | 银行有客服和身份验证流程，可找回或重置密码 |
- 私钥→公钥 的运算是单向的：容易计算，难以反推；
- 密码学强度基于大整数分解、离散对数或椭圆曲线离散对数等难题，远超常规密码策略的安全边界。

### F.NFT

> NFT图片与截屏的的区别
> 

说白了NFT能交易，自己截的屏没有交易价值。

# 2025-08-05

## 2.以太坊概览

### A.以太坊概述

  

![ethereum-C_anRFu0.jpg](attachment:3957ff4d-215a-4ebf-909d-9fcd84bab727:ethereum-C_anRFu0.jpg)

   以太坊的核心创新在于 **智能合约（Smart Contracts）** 。智能合约是存储在区块链上的可执行代码，能够在满足预设条件时自动执行操作，无需人工干预。这一特性使得以太坊不仅是数字货币的载体，更是构建去中心化应用（Dapps）、去中心化金融（DeFi）、非同质化代币（NFT）等生态系统的基础设施。截至 2025 年，以太币（ETH）仍是全球市值排名第二的加密货币，仅次于比特币。

                                   ETH

  由于区块链是去中心化的，因此在区块链上的操作都需要支付手续费给网络服务提供商。这个手续费通常称为燃料费（Gas Fee），正如你开车到达目的地时需要消耗汽油一样。

### B.[**Ethereum 与 Bitcoin 的差异**](https://web3intern.xyz/zh/overview-of-ethereum/#%E4%BA%8C%E3%80%81ethereum-%E4%B8%8E-bitcoin-%E7%9A%84%E5%B7%AE%E5%BC%82)

| **维度** | **比特币（Bitcoin）** | **以太坊（Ethereum）** |
| --- | --- | --- |
| **目标与定位** | 去中心化的数字货币，强调安全、稳定和稀缺性（总量 2100 万枚） | 去中心化平台，支持智能合约和 Dapps，定位为“区块链 2.0” |
| **编程能力** | 脚本语言有限，仅支持简单的交易验证逻辑 | 图灵完备的编程语言（如 Solidity），可开发复杂智能合约 |
| **共识机制** | 工作量证明（PoW），矿工通过算力竞争记账权 | 从 PoW 转向权益证明（PoS），通过 The Merge 实现能源效率优化 |
| **交易速度** | 每 10 分钟生成一个区块，交易确认较慢 | 区块时间约 12 秒，交易确认更快，适合高频应用 |
| **经济模型** | 总量固定，强调抗通胀属性 | 供应灵活，通过 EIP-1559 等机制可能呈现通缩趋势 |

以太坊的灵活性使其能够支持更多应用场景，例如 DeFi（借贷、交易）、NFT（数字艺术品）、DAO（去中心化自治组织）等，而比特币则更专注于作为“数字黄金”存储价值。

### C.Pos机制详解

**验证者如何工作**：

- **准入门槛**：质押 32 ETH 成为验证者
- **工作方式**：系统随机选择验证者来提议和验证区块
- **奖励机制**：验证者获得新发行的 ETH + 交易费用
- **惩罚机制**：作恶者质押的 ETH 被销毁（Slashing）

**相比 PoW 的优势**：

- **能耗降低 99.95%**：无需大量电力和硬件
- **经济安全性**：攻击成本约需控制全网 67% 的质押 ETH（价值数百亿美元）
- **最终确定性**：区块确认更快、更可靠

# 2025-08-04

https://www.notion.so/web3-245ee8f5eaa180dd8125f6e52079ae79?source=copy_link
https://postimg.cc/gallery/WTXkbJJ
上面第一个链接是Notion笔记链接复制，不知道能不能访问，如果不能的话第二个可以直接看(大概率是不能的吧我也不清楚hh)


# 2025.07.30


<!-- Content_END -->
