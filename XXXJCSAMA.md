---
timezone: UTC+8
---

# jessiewang

**GitHub ID:** XXXJCSAMA

**Telegram:** @jessiewang_0914

## Self-introduction

rust solana

## Notes

<!-- Content_START -->
# 2025-08-18

在 Currency.sol 的开始部分，我们就可以看到如下代码:

type Currency is address;

using {greaterThan as >, lessThan as <, greaterThanOrEqualTo as >=, equals as ==} for Currency global;
using CurrencyLibrary for Currency global;

function equals(Currency currency, Currency other) pure returns (bool) {
    return Currency.unwrap(currency) == Currency.unwrap(other);
}

function greaterThan(Currency currency, Currency other) pure returns (bool) {
    return Currency.unwrap(currency) > Currency.unwrap(other);
}

function lessThan(Currency currency, Currency other) pure returns (bool) {
    return Currency.unwrap(currency) < Currency.unwrap(other);
}

function greaterThanOrEqualTo(Currency currency, Currency other) pure returns (bool) {
    return Currency.unwrap(currency) >= Currency.unwrap(other);
}
此处我们首先使用了 type Currency is address; 为 address 类型创建别名。然后使用 using A for B 的语法为 Currency 类型增加了一些特性。需要注意的，此处使用了操作运算符的定义，所以在最后增加了 global 关键词。

之后的代码中出现了 Currency.unwrap 函数，该函数也是新版本 solidity 为用户自定义类型增加的新特性。在较新版本的 solidity 中，假如用户定义了 type C is V ，其中 C 是用户自定义类型，而 V 是 solidity 提供的原始类型，那么用户可以使用 C.wrap 将 V 类型转化为 C 类型，也可以使用 C.unwrap 将 V 类型转化为 C 类型。使用这种方法的好处是，solidity 会提供默认的类型检查，避免错误的类型转化发生。

Uniswap v4 此处的代码就显示了在新版本 solidity 下，开发者该如何定义自己的类型以及为类型重载运算符。如果读者希望进一步了解相关内容，可以参考 User-defined Value Types 部分的文档。

在 CurrencyLibrary 内部，Uniswap V4 定义了以下函数：

function toId(Currency currency) internal pure returns (uint256) {
    return uint160(Currency.unwrap(currency));
}

// If the upper 12 bytes are non-zero, they will be zero-ed out
// Therefore, fromId() and toId() are not inverses of each other
function fromId(uint256 id) internal pure returns (Currency) {
    return Currency.wrap(address(uint160(id)));
}
这两段函数较为简单，但注释指出 fromId() 和 toId() 并不是完全互逆的。这是因为假如用户给出了一个特殊的 id，比如给定 0x1000000000000000000000001f9840a85d5af5bf1d1762f925bdaddc4201f984 的 ID，那么由于 uint160(id) 会截断大于 160 bit 的部分，所以最终返回的结果会是 0x1f9840a85d5af5bf1d1762f925bdaddc4201f984 代币地址。所以存在多个 ID 对应同一种代币的情况。故此，Uniswap v4 注释内指出 fromId 和 toId 并不是完全互逆关系。

ERC 7751 和 Custom Revert
为了优化异常的抛出，Uniswap v4 引入了 ERC 7751 协议，并编写了 CustomRevert 库来处理 Revert。所谓的 ERC 7751 是一种带有上下文的错误抛出方法。一个简单的应用场景是当 Uniswap V4 出现报错后，在之前的情况下，我们不知道是 Uniswap v4 的主合约给出的报错还是因为 ERC20 代币实现给出的报错，我们只能通过 trace 的方式发现调用出错的位置。但 ERC 7751 允许我们给出报错的合约地址以及相关的上下文，这可以使得开发者不在 trace 的情况下就可以快速判断问题。

上述功能在 src/libraries/CustomRevert.sol 内的 bubbleUpAndRevertWith 函数内进行了实现:

/// @notice bubble up the revert message returned by a call and revert with a wrapped ERC-7751 error
/// @dev this method can be vulnerable to revert data bombs
function bubbleUpAndRevertWith(
    address revertingContract,
    bytes4 revertingFunctionSelector,
    bytes4 additionalContext
) internal pure {
    bytes4 wrappedErrorSelector = WrappedError.selector;
    assembly ("memory-safe") {
        // Ensure the size of the revert data is a multiple of 32 bytes
        let encodedDataSize := mul(div(add(returndatasize(), 31), 32), 32)

        let fmp := mload(0x40)

        // Encode wrapped error selector, address, function selector, offset, additional context, size, revert reason
        mstore(fmp, wrappedErrorSelector)
        mstore(add(fmp, 0x04), and(revertingContract, 0xffffffffffffffffffffffffffffffffffffffff))
        mstore(
            add(fmp, 0x24),
            and(revertingFunctionSelector, 0xffffffff00000000000000000000000000000000000000000000000000000000)
        )
        // offset revert reason
        mstore(add(fmp, 0x44), 0x80)
        // offset additional context
        mstore(add(fmp, 0x64), add(0xa0, encodedDataSize))
        // size revert reason
        mstore(add(fmp, 0x84), returndatasize())
        // revert reason
        returndatacopy(add(fmp, 0xa4), 0, returndatasize())
        // size additional context
        mstore(add(fmp, add(0xa4, encodedDataSize)), 0x04)
        // additional context
        mstore(
            add(fmp, add(0xc4, encodedDataSize)),
            and(additionalContext, 0xffffffff00000000000000000000000000000000000000000000000000000000)
        )
        revert(fmp, add(0xe4, encodedDataSize))
    }
}
上述代码使用了 yul 汇编完成，虽然看上去很复杂，但实际上很简单。上述代码本质上是抛出了以下错误的 ABI 编码版本:

error WrappedError(address target, bytes4 selector, bytes reason, bytes details);
在 bubbleUpAndRevertWith 函数内，details 只是一个 bytes4 additionalContext 类型。上述代码本质上是在完成了 WrappedError 的 ABI 编码工作。我们可以分部分来研究上述代码的功能:

第一部分是用来计算调用合约的返回值占据的字节数:

let encodedDataSize := mul(div(add(returndatasize(), 31), 32), 32)
在 WrappedError 内，我们会给出调用其他合约返回的报错内容，这部分报错内容都存储在 call 之后的返回值内，我们可以通过 returndatasize 获得这部分错误返回值的大小。由于 solidity 内都以 32 bytes 作为基础单位，所以此处我们需要将 returndatasize 计算为 32 的倍数。具体逻辑实现上，我们首先增加将 returndatasize 增加 31 ，这是为了处理 returndatasize = 1 这种情况。即使 returndatasize = 1，使用上述代码仍可以计算出 32 的大小。其他的先除(div )后乘(mul) 是一种典型的将每一个数字重整为另一个数字倍数的方法。我们可以在 Uniswap v3 内的 tickSpacingToMaxLiquidityPerTick 函数内看到类似的操作。

第二部分使用 let fmp := mload(0x40) 语句获取当前 solidity 的空闲内存地址的起点。solidity 编译器使用了动态的内存布局方案，每次使用或者释放内存后，都会修改 0x40 内的数据，将其指向当前未使用的空闲内存的起点。在大部分内存安全的代码方案内，我们都会 mload(0x40) 读取空闲内存起点，避免后续占用到已被其他函数使用到的内存造成安全问题。

第三部分用来写入 WrappedError 错误选择器以及报错的合约地址。

mstore(fmp, wrappedErrorSelector)
mstore(add(fmp, 0x04), and(revertingContract, 0xffffffffffffffffffffffffffffffffffffffff))
与 solidity 定义的 log 方法一致，solidity 内抛出错误实际上也会计算错误的选择器，然后将其作为最初的 4 bytes。此处的 wrappedErrorSelector 就首先被写入了内存，然后跳过 wrappedErrorSelector 占据的最初 4 bytes(add(fmp, 0x04) 就是跳过 wrappedErrorSelector 占据的字节)，我们接下来需要写入给出报错的地址。根据 solidity 的彬吗规范，给出报错的地址应该占据 256 bit。此处使用 and(revertingContract, 0xffffffffffffffffffffffffffffffffffffffff)) 清理了 revertingContract 变量可能存在的高位垃圾，然后将其写入内存。

此时的内存结构如下:

wrappedErrorSelector (4 bytes) + revertingContract(32 bytes)
第四部分写入出现报错的 selector 和错误函数的返回值。比如 Uniswap V4 调用 ERC20 代币的 transfer 失败，那么此处就写入 transfer 的选择器和返回的错误信息:

mstore(
    add(fmp, 0x24),
    and(revertingFunctionSelector, 0xffffffff00000000000000000000000000000000000000000000000000000000)
)
上述代码完成了 selector 的写入，因为 selector 的类型是 bytes4，所以此处直接写入内存即可，但注意 bytes4 在 ABI 编码后会转化为 uint256 。我们需要计算写入时需要跳过的内存长度。我们需要跳过 wrappedErrorSelector (4 bytes) + revertingContract(32 bytes) 的内存结构，该结构的长度为 36(0x24)，所以我们此处使用 add(fmp, 0x24) 确定了起始位置，然后写入了 revertingFunctionSelector，此处也需要注意使用与0xffffffff00000000000000000000000000000000000000000000000000000000 进行 and 操作去掉其他垃圾位。

完成上述操作后，内存结构如下:

wrappedErrorSelector (4 bytes) 
+ revertingContract(32 bytes) 
+ revertingFunctionSelector(32 bytes)
第五部分内，我们写入动态类型的 offset。因为我们最后写入的 reason 和 detail 都是 bytes 类型，该类型要求写入动态类型的起始位置。在后文内，我们称之为 reason offset 和 detail offset 。代码如下：

// offset revert reason
mstore(add(fmp, 0x44), 0x80)
// offset additional context
mstore(add(fmp, 0x64), add(0xa0, encodedDataSize))
此处，我们首先确定 reason offset 的写入位置 ，我们目前的内存已占用了 4 + 32 + 32 = 68，所以使用 add(fmp, 0x44) 跳过之前已写入的数据。而关于 bytes 动态类型的起始位置计算则需要排除 wrappedErrorSelector (4 bytes)，这一点额外重要，即计算动态类型的 offset 不需要包括选择器的占用部分。所以计算获得的 reason offset 应该为 revertingContract(32 bytes) + revertingFunctionSelector(32 bytes) + reason offset(32 bytes) + details offset(32 bytes)。我们使用 mstore(add(fmp, 0x44), 0x80) 写入 reason offset。

接下来，我们需要 detail offset，该变量的写入位置为 wrappedErrorSelector (4 bytes) + revertingContract(32 bytes) + revertingFunctionSelector(32 bytes) + reason offset(32 bytes) = 0x64，而写入的数值是 add(0xa0, encodedDataSize)。该数值中的 0xa0 包含以下部分 revertingContract(32 bytes) + revertingFunctionSelector(32 bytes) + reason offset(32 bytes) + reason length(32 bytes) + detail offset(32 bytes) = 160，除此之外还包含 encodedDataSize 部分。

在具体的编码过程中，我们往往最后确认 offset 的数值，所以动态类型的 offset 并没有非常难以确认

上述代码编写完成后，内存布局如下:

wrappedErrorSelector (4 bytes) 
+ revertingContract(32 bytes) 
+ revertingFunctionSelector(32 bytes)
+ reason offset(32 bytes)
+ detail offset(32 bytes)
接下来，我们可以写入 reason 的真实内容。此处需要注意写入 bytes 类型的内容，需要在原有内容前增加长度信息。我们先完成 reason 的长度写入然后完成具体的内容写入:

// size revert reason
mstore(add(fmp, 0x84), returndatasize())
// revert reason
returndatacopy(add(fmp, 0xa4), 0, returndatasize())
此处使用 returndatacopy 将失败的调用的返回结果写入内存。读者可以自行阅读 文档 获得 returndatacopy 的参数。

完成上述操作后内存布局为:

wrappedErrorSelector (4 bytes) 
+ revertingContract(32 bytes) 
+ revertingFunctionSelector(32 bytes)
+ reason offset(32 bytes)
+ detail offset(32 bytes)
+ reason length(32 bytes)
+ return data(encodedDataSize)
最后，我们写入 detail 的内容。Uniswap v4 约定 detail 是一个 bytes4 additionalContext 参数，所以长度确定为 4。相关代码如下:

// size additional context
mstore(add(fmp, add(0xa4, encodedDataSize)), 0x04)
// additional context
mstore(
    add(fmp, add(0xc4, encodedDataSize)),
    and(additionalContext, 0xffffffff00000000000000000000000000000000000000000000000000000000)
)
此处也使用了 and 去掉垃圾位。完成上述所有步骤后，我们的内存布局如下:

wrappedErrorSelector (4 bytes) 
+ revertingContract(32 bytes) 
+ revertingFunctionSelector(32 bytes)
+ reason offset(32 bytes)
+ detail offset(32 bytes)
+ reason length(32 bytes)
+ return data(encodedDataSize)
+ detail length(32 bytes)
+ detail data(32 bytes)
完成上述所有工作后，我们直接使用 revert(fmp, add(0xe4, encodedDataSize)) 抛出报错。代码内的 0xe4 实际上就是除了 return data 外，其他固定长度部分之和。

由于上述代码最后使用 revert 结束，所以此处我们没有进行内存的清理工作，但其他函数内，开发者如果使用内联汇编操作内存，应当考虑内存的清理工作。

在 CurrencyLibrary 库内，我们使用了上述函数报错 transfer 的错误：

if (!success) {
    CustomRevert.bubbleUpAndRevertWith(
        Currency.unwrap(currency), IERC20Minimal.transfer.selector, ERC20TransferFailed.selector
    );
}
考虑到文章的长度，此处不会详细介绍 CurrencyLibrary 内给出的 transfer 函数的构造方法，读者可以自行阅读 yul 源代码。该函数最后就使用了以下代码清理内存:

// Now clean the memory we used
mstore(fmp, 0) // 4 byte `selector` and 28 bytes of `to` were stored here
mstore(add(fmp, 0x20), 0) // 4 bytes of `to` and 28 bytes of `amount` were stored here
mstore(add(fmp, 0x40), 0) // 4 bytes of `amount` were stored here

# 2025-08-16

借贷平台 (DeFi Lending Platform) 项目需求文档
构建一个基于Solidity的智能合约借贷平台，为用户提供安全、透明、无需信任中介的借贷服务。

功能需求
MyERC20Token合约, 发布两个币种，用于模拟一个借贷另一个：一个 PETH ，一个 PUSDT

PETH：仿ETH
PUSDT：仿USDT（稳定币）
LPSwap合约：用于模拟 PETH-PUSDT 之间的价格变化

DefiLendingDapp合约: 用于实现一个借贷另一个

MyERC20Token合约
合约 MyERC20Token.sol 可以直接实例化出来两个合约：1）PETH：（"Pseudo-ETH", "PETH"）；2）PUSDT：（"Pseudo-USDT", "PUSDT"） 对应 contracts/MyERC20Token.sol 文件 执行：npx hardhat compile

mint：铸造，owner可用

burn：燃烧，owner可用

transfer：转账

balancesof：查看余额

LPSwap合约
添加流动性

移除流动性（要移除的流动性百分比(1-100)）

得到价格

交换

得到价格

DefiLendingDapp合约
存款

还款

抵押贷款

还贷款取回抵押

清算：当借款人的抵押资产价值下降到不足以覆盖其借款金额时，系统自动触发强制出售抵押资产以偿还债务的过程。

当抵押物价值低于清算阈值时触发
清算人可获得奖励
项目部署
本地部署脚本（测试环境）
对应 deploy/deploy_MyErc20Token.js 文件

执行：npx hardhat deploy
测试脚本
对应 test/staging/04_test_DefiLendingDapp.js 文件

执行：npx hardhat test
测试内容

验证存取逻辑
验证借款还款逻辑
验证借款清算逻辑
验证借款价格改变清算逻辑
线上部署脚本（生产环境）
对应 deploy 中的文件

执行：npx hardhat deploy --network sepolia

输出部署合约地址：

PETHToken： 0x0642a3a85EE7F9dD52B82caa7C3aE6d8F712427c
PUSDTToken： 0x1a1Dd7994A1bA16BD2a58cd076EbeA69266587D6
LPSwap： 0xa09B5a5DdED094Ba656d6416976F1DF62aA889e4
DefiLendingDapp： 0xb8026085CfcD6D0F3D06023e541466A28D5deb9B

# 2025-08-15

安全问题：面试钱包助记词被盗
让你npm install 他们的github代码，然后会在你本地的电脑上留下后门程序，攻击你的电脑。
详细推文：
A community member recently reached out after interviewing with a Web3 team claiming to be from Ukraine. In the first round, he was asked to clone a GitHub repo locally — he wisely refused.🧑‍💻

🔍Our analysis revealed the repo contains a backdoor: github[.]com/EvaCodes-Community/UltraX

💥If cloned & executed, it would load malicious code, install a malicious dependency rtk-logger@1.11.5 (created 2025-08-08), harvest sensitive browser & wallet data (e.g., Chrome extension storage, possible seed phrases, session tokens) and📤exfiltrate them to the attacker’s server.

⚠️This is a job-offer-as-a-trap scam. Stay vigilant — never run unverified code from unknown sources.

# 2025-08-14

（一）公链/Layer1项目BD案例公司类型：中型区块链技术研发公司
岗位职责：
1. 生态合作：
• 对接开发者社区，主导Grant计划资助DApp
开发团队（如Solana生态建设）。
• 推动跨链桥技术集成（如以太坊与Layer2的互操作性合作）。
2. 资源整合：
•招募验证节点运营商，设计Staking奖励模型。
• 联合举办全球黑客松活动，吸引开发者入驻。
技能侧重：
• 技术理解力：精通智能合约、跨链通信协议。
• 开发者运营：熟悉GitHub等开源社区协作工具。
交易所BD案例
公司类型：头部加密货币交易所
岗位职责：
1. 上币谈判：
• 评估代币经济模型，主导项目上线审核（如
Memecoin流动性风险把控）。
•对接做市商设计流动性挖矿方案。
2. 合规风控：
•监控SEC监管动态，调整上币策略（如应对
MiCA法案）。
技能侧重：
• 金融知识：熟悉订单簿机制、衍生品合约设计。
• 多语言能力：英语流利，可处理跨国法律文件。
（三） NFT/GameFi®项目BD案例公司类型：元宇宙平台
岗位职责：
1. IP合作：
• 谈判知名IP授权（如潮牌联名NFT发行）。
• 策划KOL营销活动，设计空投奖励机制。
2. 平台入驻：
• 推动游戏项目接入元宇宙土地系统（如
Decentraland地块合作）。
技能侧重：
• 创意策划：结合社交媒体热点设计病毒式传播方案。
• 资源网络：积累动漫、时尚领域KOL资源库。

四）DeFi协议BD案例公司类型：去中心化借贷协议
岗位职责：
1. 技术集成：
•主导与MetaMask°等钱包的API对接，优化用户授权流程。
• 设计流动性激励模型（如动态调整APY吸引
LP）。
2. 安全合作：
•协调Certik®等审计机构完成智能合约漏洞排查。
技能侧重：
• 经济模型设计：精通代币释放曲线、滑点控制机制。
• 合约安全：理解重入攻击等常见风险点。
（五）DAO社区BD案例
公司类型：去中心化自治组织Q
岗位职责：
1. 治理推进：
• 组织社区投票，推动多签钱包管理方案落地。
• 调配国库资金支持生态提案（如开发者赏金
划）。
2. 资源协调：
• 管理全球志愿者团队，协调时区会议。
技能侧重：
• 共识构建：熟练使用Snapshot、Discord等治理工具。
• 抗压能力：应对代币价格波动导致的社区分歧。

# 2025-08-13

关于kolbd
“On board KOL” 是一个营销领域的术语，通常指品牌或企业将某位 **关键意见领袖（Key Opinion Leader, KOL）** 纳入其营销活动中，使其成为品牌推广的一部分。具体含义和场景如下：

---

### 1. **“On board” 的含义**
   - 直译为“登上（船、飞机等）”，在商业中引申为“加入”或“合作”。
   - **“On board KOL”** 即品牌与KOL达成合作，邀请其参与产品推广、内容创作或品牌代言。

---

### 2. **常见的合作形式**
   - **产品推广**：KOL通过社交媒体（如小红书、抖音、微博等）发布评测、种草内容。
   - **品牌代言**：KOL成为品牌长期合作伙伴，甚至担任形象大使。
   - **活动参与**：KOL出席品牌发布会、直播活动等。
   - **内容共创**：与品牌联合制作定制化内容（如短视频、图文）。

---

### 3. **为什么品牌需要 “on board KOL”？**
   - **精准触达目标群体**：KOL拥有垂直领域的粉丝，能高效传递品牌信息。
   - **提升信任度**：KOL的推荐比传统广告更易获得用户信任。
   - **扩大声量**：借助KOL的流量，快速提升品牌曝光。

---

### 4. **相关术语区分**
   - **KOL（关键意见领袖）**：在特定领域有影响力的人物（如头部博主、行业专家）。
   - **KOC（关键意见消费者）**：粉丝量较少但互动率高的真实用户，更贴近普通消费者。
   - **Influencer（网红）**：广义的社交媒体影响者，可能包括KOL/KOC。

---

### 5. **使用场景举例**
   - “我们已on board了10位美妆KOL，下月集中推广新品。”
   - “这次营销活动的关键是on board头部KOL，带动话题热度。”

---

如果需要进一步了解KOL营销策略或合作模式，可以补充具体场景哦！

# 2025-08-12

FT的英式拍卖 英式拍卖（English Auction）是一种公开拍卖方式，拍卖品从起始价格开始，由拍卖师通过不断提高价格来逐步升高，直到最后出价最高的竞标者将赢得拍卖品并支付出价。
拍卖
1. NFT的卖家部署这个合约。
2. 拍卖持续7天。
3. 参与者可以通过存入比当前最高出价更高的ETH来竞标。
4. 所有竞标者如果不是当前最高出价者，都可以撤回他们的竞标。
拍卖结束后
1. 最高出价者成为NFT的新拥有者。
2. 卖家获得ETH的最高出价。
English Auction例子合约
- ERC721 NFT 的接口
interface IERC721 {
    function safeTransferFrom(address from, address to, uint tokenId) external;

    function transferFrom(address, address, uint) external;
}
- 合约中的变量和事件
event Start();
event Bid(address indexed sender, uint amount);
event Withdraw(address indexed bidder, uint amount);
event End(address winner, uint amount);

IERC721 public nft;//ERC721 资产的接口。uint public nftId;//ERC721 资产的 ID。address payable public seller;//卖家的地址。uint public endAt;//拍卖结束的时间戳。bool public started;//标记拍卖是否已经开始。bool public ended;//标记拍卖是否已经结束。address public highestBidder;//当前最高出价者的地址。uint public highestBid;//当前最高出价。mapping(address => uint) public bids;//每个出价者的出价。
- 构造函数。 传入 ERC721 资产的地址、ID 和起始出价。
constructor(address _nft, uint _nftId, uint _startingBid) {
    nft = IERC721(_nft);
    nftId = _nftId;

    seller = payable(msg.sender);
    highestBid = _startingBid;
}
- 开始拍卖 只有卖家可以调用。 将 ERC721 资产转移到合约地址，并标记拍卖已开始。
function start() external {
    require(!started, "started");
    require(msg.sender == seller, "not seller");

    nft.transferFrom(msg.sender, address(this), nftId);
    started = true;
    endAt = block.timestamp + 7 days;

    emit Start();
}
- 出价 只有在拍卖已开始且未结束时可以调用。 要求出价高于当前最高出价。 如果当前最高出价者不为空，则将其出价退回给其余出价者。
function bid() external payable {
    require(started, "not started");
    require(block.timestamp < endAt, "ended");
    require(msg.value > highestBid, "value < highest");

    if (highestBidder != address(0)) {
        bids[highestBidder] += highestBid;
    }

    highestBidder = msg.sender;
    highestBid = msg.value;

    emit Bid(msg.sender, msg.value);
}
- 撤回出价 只有在拍卖已开始但未结束时可以调用。 将出价退回给出价者。
function withdraw() external {
    uint bal = bids[msg.sender];
    bids[msg.sender] = 0;
    payable(msg.sender).transfer(bal);

    emit Withdraw(msg.sender, bal);
}
- 结束拍卖 只有在拍卖已开始且已结束时可以调用。 将 ERC721 资产转移给最高出价者，将出价支付给卖家。 如果没有出价，则将 ERC721 资产返回给卖家。
function end() external {
    require(started, "not started");
    require(block.timestamp >= endAt, "not ended");
    require(!ended, "ended");

    ended = true;
    if (highestBidder != address(0)) {
        nft.safeTransferFrom(address(this), highestBidder, nftId);
        seller.transfer(highestBid);
    } else {
        nft.safeTransferFrom(address(this), seller, nftId);
    }

    emit End(highestBidder, highestBid);
}

# 2025-08-11

智能合约1. 取款逻辑缺陷：合约的withdraw（提款）函数，在代码执行顺序上存在缺陷：先给用户转账ETH，然后再更新该用户的内部账本余额。
2. 恶意合约的构造：攻击者创建了一个恶意的智能合约，并用它向 The DAO投资，然后发起提款请求。
3. 触发fallback函数：当The DAO合约向攻击者的恶意合约转账ETH时，会触发攻击者合约中的fallback 函数（一个在接收 ETH
时自动执行的函数）。
4. 递归调用（“重入”）：攻击者在其fallback
函数的代码里，再次调用了 The DAO的withdraw 函数。
5. 状态更新延迟：由于 The DAO合约此时还没来得及更新攻击者的余额，它错误地认为攻击者仍然有钱可提，于是再次向其转账。
6. 循环盗取：这个“转账->重入->再转账”的过程被递归地重复执行，直到耗尽Gas或达到攻击目标，从而盗取了大量资金。

# 2025-08-10

做了一些dapp

# 2025-08-09

智能合约solidity 
https://x.com/jessiewang0914/status/1953989361869197383

# 2025-08-08

是区块链的核心机制，决定了网络如何达成状态一致性和安全性。不同场景（如公链、联盟链、DeFi专用链）需要权衡去中心化、安全性、性能三大特性。以下是针对不同场景的共识算法详解，重点包括PoW/PoS的取舍和DeFi专用链（如Tendermint）的设计逻辑：

---
1. 公链场景：PoW vs PoS 的取舍
(1) PoW（Proof of Work，工作量证明）
- 代表链：比特币、以太坊（早期）。
- 核心机制：
  - 节点通过计算哈希难题（如SHA-256）竞争出块权，消耗大量算力。
  - 最长链原则：恶意节点需掌握51%算力才能篡改历史。
- 适用场景：
  - 高安全性需求：适合无需许可的公链，抗Sybil攻击能力强。
  - 去中心化优先：矿工分布广泛，无质押门槛。
- 缺点：
  - 能耗高：比特币年耗电量超挪威全国。
  - 吞吐量低：比特币仅7 TPS，确认慢（6区块确认≈1小时）。
(2) PoS（Proof of Stake，权益证明）
- 代表链：以太坊2.0、Cardano。
- 核心机制：
  - 节点通过质押代币获得出块权，恶意行为会导致质押金被罚没（Slashing）。
  - 随机选择验证者（如以太坊的RANDAO+VDF）。
- 适用场景：
  - 高性能需求：以太坊2.0目标TPS可达10万（分片后）。
  - 节能环保：无算力竞争。
- 缺点：
  - 富者愈富：持币大户更容易获得奖励。
  - 长程攻击风险：需通过检查点（Checkpoint）或弱主观性防御。
(3) PoW vs PoS 的取舍
暂时无法在飞书文档外展示此内容

---
2. DeFi专用链场景：Tendermint（BFT共识）
(1) 为什么DeFi需要专用共识？
- 需求：DeFi应用需要快速最终性（Finality）和高确定性（交易顺序不可逆），避免MEV抢跑或分叉风险。
- 问题：PoW/PoS的概率最终性（如比特币6区块确认）可能导致双花或重组攻击。
(2) Tendermint（BFT类共识）
- 代表链：Cosmos Hub、Binance Chain。
- 核心机制：
  - PBFT（实用拜占庭容错）变体：验证者轮流出块，需2/3以上节点签名确认区块。
  - 即时最终性：一旦区块被提交，不可回滚（无分叉）。
  - 轻客户端友好：可通过默克尔证明快速验证状态。
- 优势：
  - 低延迟：出块时间1-3秒，适合订单簿DEX（如dYdX）。
  - 确定性：交易顺序固定，减少MEV套利窗口。
- 缺点：
  - 节点数限制：通常100-150个验证者，牺牲去中心化。
  - 活跃性风险：需2/3节点在线，否则网络停滞。
(3) DeFi链的共识优化案例
- Osmosis（Cosmos SDK）：
  - 通过阈值加密隐藏交易内容，减少前端跑。
  - 区块空间拍卖：优先处理高Gas费交易，对抗MEV。
- Sei Network：
  - 并行化执行：基于交易依赖关系加速AMM结算。

---
1. 其他场景的共识选择
(1) 联盟链：PBFT/Raft
- 特点：节点身份已知，容忍1/3拜占庭节点。
- 用例：Hyperledger Fabric（金融机构间结算）。
(2) 高性能链：DAG类共识
- 代表：Solana的Tower BFT（PoH+PoS混合）。
- 优势：5万+ TPS，但需中心化硬件（如SSD服务器）。
(3) 隐私链：ZKP共识
- 代表：Mina的Ouroboros Samasika（递归零知识证明）。
- 特点：轻节点可验证整个链状态（仅22KB）。

---
2. 共识算法的关键权衡
3. 以太坊的混合共识：Casper FFG + LMD-GHOST
(1) 为什么需要混合共识？
- 问题：纯PoS可能导致长程攻击（Long-range Attack）——攻击者购买旧私钥重构历史链。
- 解决方案：结合最终确定性工具（Casper FFG）和分叉选择规则（LMD-GHOST）。
(2) Casper FFG（Friendly Finality Gadget）
- 核心机制：
  - 两阶段投票：验证者对周期（Epoch）内的区块进行prepare和commit投票。
  - 确定性条件：当2/3验证者对区块B的commit投票达成时，B被最终确定。
- 
- def is_finalized(block):    prepare_votes = get_votes(block, stage='prepare')    commit_votes = get_votes(block, stage='commit')    return len(prepare_votes) > 2/3 * total_stake and len(commit_votes) > 2/3 * total_stake  
- 惩罚条件（Slashing）：
  - 双重投票（同一高度投票给两个区块）。
  - 违反“非环绕投票”规则（投票的源区块必须递增）。
(3) LMD-GHOST（Latest Message Driven Greediest Heaviest Observed SubTree）
- 作用：在未最终确定的区块中选择“最重”的分支。
- 规则：
  - 从创世块开始，递归选择被最多验证者最新投票的子区块。
  - 权重计算：weight(block) = Σ(stake of validators whose latest vote is for block or its descendants)
(4) 攻击与防御
- 平衡攻击（Balancing Attack）：
  - 攻击者控制少量质押者，在分叉间交替投票以延迟确定性。
  - 防御：增加诚实节点的同步假设（如≥66%节点在线）。

---

# 2025-08-07

dex uniswap理解
https://x.com/jessiewang0914/status/1953437426573025775
dex：假如某个项目想要发行自己的资产供用户交易。在传统的中心化交易所，这可能需要与交易所签订繁琐的合同，并让研发人员与系统进行对接，整个流程复杂且不可靠。然而在去中心化交易所中，两步即可。1.将资产通过智能合约发布到链上；2.把资产加入去中心化交易所的流动性池，这样用户就能进行交易了。
实现去中心化交易所的关键考量
要实现一个去中心化交易所，除了用户操作页面外，智能合约是重中之重。智能合约承担着处理用户交易请求、保障交易安全与透明的重任。对于负责交易处理的智能合约而言，最关键的就是如何在合适的时间以合适的价格，确保各种资产安全完成交易。
像 Blur 这类专注于 NFT 交易的交易所，交易逻辑相对简单。卖方可以指定一个价格，授权合约进行出售，买方随后授权合约购买；或者买家出价，卖家出售，整个交易过程由智能合约保障，无需三方担保，由智能合约管理资金和资产，这就是订单簿交易所的模式。
然而，这种模式对于 FT（同质化代币）交易来说就有些力不从心了。因为 FT 交易量大，对流动性的要求极高，需要更高效的交易模式。通过订单簿进行交易，无论是存储还是撮合等操作都需要执行合约，这会消耗大量 Gas（即区块链上执行合约所需的手续费）。
所以，针对去中心化代币交易所，需要更高效的交易模式，也就是 AMM（自动化做市商）。从技术层面讲，AMM 就是智能合约要实现的交易逻辑，它负责处理用户交易请求、管理资金池、计算价格，并确保有足够的流动性来支撑交易。
简单来讲，可以通过引入流动性提供者（Liquidity Provider）来实现。这些 LP 会将一对资产存入资金池，并给出初始定价。交易者无需像 NFT 交易那样等待买家，可直接在流动性池中随时买卖，而资产价格会随着持续的交易行为而变动。LP 在这个过程中能够获得相应激励。

# 2025-08-06

https://x.com/jessiewang0914/status/1952980918219751870
Uniswap v3 core
Uniswap v3 是为以太坊虚拟机实现的非托管自动化做市商，相较于早期版本，它在资本效率、流动性提供者的控制程度、价格预言机的准确性和便利性以及费用结构灵活性等方面都有提升。核心：Concentrated Liquidity，Flexible Fees:Protocol Fee Governance:Improved Price OracleLiquidity Oracle。集中流动性（Concentrated Liquidity）：允许流动性提供者（LPs）将流动性限制在任意价格范围内（称为 “头寸”），仅需维持该范围内交易所需的储备，相当于在该范围内拥有更大的 “虚拟储备”，大幅提升资本效率。当价格超出范围时，头寸流动性不再活跃且不产生费用，仅由单一资产构成；若价格重回范围，流动性恢复活跃。LPs 可创建多个不同价格范围的头寸，灵活分配流动性。
灵活费用结构：不再固定 0.30% 的交易费率，每个资产对可对应多个池，每个池的费率在初始化时设定，初始支持 0.05%、0.30%、1% 三个等级，UNI 治理可添加更多等级。协议费用治理：UNI 治理可灵活设定协议收取的交易费用比例，且可按池单独设置。
改进的价格预言机：无需用户在计算时间加权平均价格（TWAP）时，精确记录时间段起始和结束时的累加器值，通过内置累加器 checkpoint 实现链上直接计算；同时改用对数价格累加，计算几何平均 TWAP，提升精度并减少存储需求。
流动性预言机：新增时间加权平均流动性预言机，追踪 的累加值，助力外部合约实现流动性挖矿等功能。

# 2025-08-05

https://intensivecolearn.ing/programs/Web3_Internship_Program
常见网络安全风险与攻击方式：钓鱼攻击（Phishing） 钓鱼攻击是最常见、最有效的网络攻击手段之一。攻击者通过伪造官方网站、社交账号、邮件、短信等，诱导受害者点击恶意链接、输入敏感信息（如助记词、私钥、账号密码），或下载恶意软件。伪造邮件/网站： 攻击者会仿冒知名企业、学校、项目方的邮箱或网站，发送“面试通知”“奖学金发放”“账号异常”等紧急信息，诱导你点击钓鱼链接。
社交平台钓鱼： 在微信群、QQ 群、Telegram、Discord 等社群中，冒充官方人员、HR、发布虚假招聘、空投
假冒客服/好友：冒充信任的人，诱导你转账或泄露信息

# 2025-08-04

区块链基础知识：
https://x.com/jessiewang0914/status/1952239481773514932
a blockchain is a distributed database
区块：存储交易记录     链：存储上一个区块的哈希
去中心化，不可篡改，公开透明
去中心化的网络由无数节点提供服务来维持网络运行，这些操作统称为挖矿。维持这些服务的人一般称之为矿工。区块链生态系统的运行包含以下几个关键步骤：
用户发起交易：用户通过钱包应用发起转账、智能合约调用等操作
交易广播：交易信息被广播到整个网络中的各个节点
节点验证：网络中的矿工节点验证交易的合法性（余额是否足够、签名是否正确等）。打包成块：通过共识机制（如工作量证明），矿工将验证过的交易打包成新的区块
链接上链：新区块被添加到区块链上，更新全网的账本状态
奖励发放：成功打包区块的矿工获得代币奖励和交易手续费。公链 vs 私链 vs 联盟链
公链（Public Blockchain） = 公共公园。公园里没有管理员，所有规则由大家共同制定。成为节点的方法：
无需申请，自由进出。所有人可见，去中心化（共识机制）。缺点：因为人太多，决策效率低（交易确认慢），维护成本高（比如电费、人力）联盟链（Consortium Blockchain） = 多公司联合的董事会。成为节点的方法：需要邀请或申请。权限分级：董事会成员可能分为两类：决策者（联盟核心成员）：比如银行 A、银行 B，可以修改数据库规则。
观察者（联盟普通成员）：比如物流公司，只能查看数据但不能修改。共同管理数据的模式：半公开数据：数据库里的信息（比如客户信用评分）只有董事会成员能看到，外人无法访问。 联合决策：如果要修改数据库规则（比如增加新字段），需要董事会成员投票通过。 优点：效率比公链高（因为成员少），隐私比公链好（数据不对外公开），但不如私链灵活（需要多方协调）。

# 2025.07.30


<!-- Content_END -->
