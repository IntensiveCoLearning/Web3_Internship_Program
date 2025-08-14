---
timezone: UTC+8
---

# 魏琳

**GitHub ID:** Unjourclair

**Telegram:** @无

## Self-introduction

戏剧&影视行业从业者

## Notes

<!-- Content_START -->
# 2025-08-14

笔记：ERC721.sol：ERC721标准的实现，提供NFT核心功能，如所有权管理、转移等。
ERC721URIStorage.sol：扩展功能，用于存储每个NFT的元数据URI（如指向JSON文件的链接，包含图片、描述等）。
ERC721Burnable.sol：添加“销毁”功能，允许永久删除NFT，减少流通量。
Ownable.sol：实现访问控制，限制特定功能（如铸造）仅限合约拥有者调用。
EIP712.sol：支持EIP-712标准，用于结构化数据签名，可实现无gas交易（如签名授权）。
ERC721Votes.sol：为NFT添加治理功能，允许NFT持有者参与投票（如DAO治理）。
基于OpenZeppelin的ERC721智能合约MyERC721Token
部署脚本：// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Script, console} from"forge-std/Script.sol";
import {MyERC721Token} from"../src/MyERC721Token.sol";

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
部署合约
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

# 2025-08-13

运营笔记：活动宣发/活动执行/workshop筹备/Openday筹备/黑客松筹备
活动流程
开场欢迎（0-10min）：主办方简要介绍活动背景、宗旨和黑客松流程说明
Sponsor keynote(每个3-5min)
嘉宾分享（40min）：邀请研究者、开发者根据已定方向主题分享
脑暴交流与组队（10-15min）：自我介绍、寻找队友，用telegram社群沟通
Q&A+收尾（5min）：回答现场提问，分享报名与提交流程链接，后续日程提示
运营/活动策划key point在：活动主题的确定，活动目的的明确，活动嘉宾邀请，及最后活动执行落地的督促——嘉宾按时交稿准备、活动物料及宣发的提前准备与确认，活动到场人数与反馈等。

# 2025-08-12

笔记：1.DeFi:是指基于区块链技术（尤其是智能合约平台如以太坊）构建的开放式金融生态系统。它的核心目标是用代码和算法替代传统金融中介（如银行、券商等）
核心特征：去中心化+无许可+可验证+可组合性
2.代表产品Uniswap：是以太坊上最大的去中心化交易所（DEX），通过自动化做市商（AMM）模型实现无需订单簿的代币交易。
3.Uniswap竞品SuShiSwap：一个去中心化交易所（DEX），最初是Uniswap V2的分叉，但后来发展出自己的生态系统。它采用自动化做市商（AMM）模型，允许用户交易加密货币，并提供流动性赚取收益。2020年9月，SushiSwap推出代币激励吸引Uniswap用户迁移，称为“吸血鬼攻击”。
产品启示：
1.可组合性(Composability)
√DeFi乐高:协议之间可无缝集成(如Uniswap+Aave)。
创新加速:开发者无需重复造轮子 ，直接调用现有协议。
案例:SushiSwap分叉Uniswap，并扩展借贷(Kashi) 和质押(×SUSHI)。 
2.吸血鬼攻击(Vampire Attack)
√竞争策略:通过代币激励短期掠夺对手流动性。 
风险:不可持续，依赖代币通胀(如 sUSHI创始人抛售)。
 案例:SushiSwap2020年抽干Uniswap流动性。 
3.代币标准化(Token Standards)
√ ERC-20普及:Uniswap V2支持所有ERC-20交易对，推动行业互通。 
√降低门槛:开发者只需遵循标准即呵可接入生态。
案例:DAI(ERC-20)成为DeFi基础货币。 
4.治理与团队稳定性
√去中心化≠无管理:Uniswap专业团队vs SushiSwap社区混乱。 
！教训:创始人信任危机(如 Chef Nomi抛售SUSHI)。 
5.长期主义 vs短期投机
Uniswap:专注AMM迭代(V3-V4),稳居龙头。 
×sushiswap:盲目扩张，产品分散，最终边缘化。

# 2025-08-11

1.一般用ERC720和ERC721较多
2.Solidity合约基础
每个 Solidity 文件必须以版本声明开始：pragma solidity ^0.8.0;
一个智能合约的基本结构通常由以下三部分组成：状态变量、构造函数和普通函数。
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MyContract {
    // 状态变量
    uint256 public myNumber;

    // 构造函数
    constructor() {
        myNumber = 100;
    }

    // 函数
    function setNumber(uint256 _number) public {
        myNumber = _number;
    }
}
函数可见性决定了函数在何种上下文中可以被调用
contract VisibilityExample {
    // 仅当前合约可访问
    function privateFunc() private pure returns(uint256) { return 1; }
    // 当前合约和继承合约可访问
    function internalFunc() internal pure returns(uint256) { return 2; }
    // 所有人可访问
    function publicFunc() public pure returns(uint256) { return 3; }
    // 仅外部调用
    function externalFunc() external pure returns(uint256) { return 4; }
}
函数状态修饰符（State Mutability Modifiers）
用于指明函数是否修改或读取合约状态：
contract StateModifiers {
    uint256 public count = 0;

    // view: 只读函数，不修改状态
    function getCount() public view returns(uint256) {
        return count;
    }

    // pure: 纯函数，不读取也不修改状态
    function add(uint256 a, uint256 b) public pure returns(uint256) {
        return a + b;
    }

    // payable: 可接收以太币
    function deposit() public payable {
        // msg.value 是发送的以太币数量
    }

    // 默认：可修改状态
    function increment() public {
        count++;
    }
}

# 2025-08-08

今日学习：WEB3运营内容
基础排版格式
1.中英文之间需要增加空格 如“你好 Hello 朋友，最近过得还好吗？”
2.中文与数字之间要加空格 如“我买菜花了 5000 元。”
3.数字与单位之间要增加空格
4.不重复使用标点符号
5.使用全形中文标点 数字使用半形字符
6.遇到完整的英文整句、特殊名词，其內容使用半形标点
活动任务SOP
线上活动
1. 确定活动背景与目标
2. 准备流程按时间节点拆解
明确主题、嘉宾、主持人、讨论方向、Space Flow 与问题库+对接宣发与内容
3.活动复盘
 数据回顾：首条推文浏览数；Space 在线人数峰值；小宇宙收听量：
 可优化项：嘉宾确认效率；宣发节奏是否合理；Space 节奏 & 问题设计反馈
线下活动
策划阶段
1. 明确活动主题与形式
2.设定目标与定位（活动目的，目标受众画像，预期效果（需量化），预算 & 人员分工，法律与合规（如适用））
筹备阶段
1. 活动流程设计与嘉宾邀约
2. 宣传设计与节奏安排（  - 视觉物料清单   - 社媒宣传计划  - 媒体与合作社区联动  - 以太坊生态周边活动收集 ）
3. 物资准备 & 周边采买
现场执行阶段
1. 场地布置与技术支持
2. 嘉宾接待与观众签到
3. 现场执行协调
活动复盘阶段
1. 活动 Recap 内容产出（建议 2 天内完成）
 推特 thread / 公众号文章 / 图片合集 / 小红书图文
 可将嘉宾金句 / 精彩瞬间剪辑为 15s 视频
2. 数据与效果分析
 到场人数 vs 注册人数；宣发帖曝光量、嘉宾 RT 数、社交媒体互动
 物料是否按计划发放、设备是否顺畅
3. 总结报告
  成功要素 & 待优化事项
  嘉宾反馈、观众建议
  项目合作 / PR 机会记录

# 2025-08-07

今日笔记：核心红线与风险要点：资金汇集
交易所与虚拟货币基金
是否涉及法币
海外的合规性是否完备（AML与KYC）
技术上是否排除中国大陆用户
1. 代币发行与交易行为的法律风险
2. 当前，中国法律明令禁止任何单位或个人通过 ICO、IEO、IDO 等方式进行融资活动。不论代币是否命名为“积分”“凭证”或“治理 Token”，只要具备融资功能或可流通性，即可能构成非法金融行为。
3. 例如，一些项目通过“空投”或“白名单”的方式分发代币，实质上仍是面向公众筹资。一旦筹资规模扩大，项目方将可能被追究非法吸收公众存款罪。
4. 技术人员若参与代币模型设计、空投逻辑配置、合约部署等环节，也会被视为“共同行为人”，无法以“只是写代码”为由免责。
海外的合规性是否完备：AML&KYC
KYC（Know Your Customer）
· 目的：确认客户身份、评估风险等级、防止匿名或虚假账户。
· 关键动作：
– 身份核验：护照、驾照、人脸识别、活体检测等。
– 地址验证：近期水电账单、银行对账单等。
– 资金来源与用途：了解客户资金从哪来、用来干什么。
– 持续监控：账户行为发生异常时重新评估风险。
AML（Anti-Money Laundering）
· 目的：发现并阻止洗钱、恐怖融资等金融犯罪。
· 关键动作：
– 客户风险评级（CDD/EDD）。
– 交易监控：对大额、频繁、异常交易实时预警。
– 制裁名单筛查：与OFAC、EU、UN 等制裁名单实时比对。
– 可疑交易报告（SAR）：一旦触发阈值，必须向监管机构报告。
– 员工培训与内部审计：确保制度长期有效。
“误入”传销与赌场（赌博、传销、洗钱等刑事风险）
项目扩张过程的合规：回避多级返佣与团队奖励
多级返佣：A邀请朋友B玩某平台，获得奖励￥；B再邀请朋友C玩某平台，A再次获得奖励，即为多级返佣。
非典型案例：合约交易
NFT的合规玩法设计
Web3 项目的设计与经济激励模型直接影响风险等级。例如，部分链游存在“充值—抽奖—提现”的闭环结构，玩家投入法币或 USDT 购买盲盒、转盘机会，随机获得稀有 NFT 或 Token，后续可在平台提现或转售。这种“下注—随机收益—兑现”的机制容易被认定为开设赌场罪。
NFT 项目、DAO 社群、挖矿平台中常见的“邀请返利”“算力挂靠”“多级推广”模式，一旦涉及团队计酬、多层级返佣，可能触犯组织、领导传销活动罪。
难以判断：洗钱与外汇管制（场外交易中的洗钱与非法经营风险）
洗钱链条的一环or外汇兑换的媒介
难点：资金的来龙去脉对于非管理层来说难以知晓
出现不清楚的情况时，提高自身警惕
虚拟货币在场外交易环节常被用作规避监管的“地下换汇”工具。境内个人先用人民币购买 USDT、ETH 等虚拟币，再通过链上或境外平台兑换美元、欧元等外币，形成跨境资金流转链条。此过程不仅绕过外汇监管，还使虚拟货币成为规避外汇管制的“桥梁资产”。根据《外汇管理条例》和刑法，未经许可反复组织撮合人民币与外币的虚拟币交易，可能构成非法经营外汇业务。
更为严重的是，部分交易对手或资金来源与电信诈骗、赌博、毒品交易等犯罪活动相关，参与者极有可能被控洗钱罪、掩饰隐瞒犯罪所得罪，甚至在不知情的情况下被卷入“协助洗钱”的刑事链条。因此，在参与虚拟币兑换时必须谨慎，对交易对手的信息、背景、资金来源进行审核，避免成为非法资金链条中的一环
判断的经验之谈
项目是否在明确、受监管的司法辖区内注册
是否为了取得合规资质，经过专业机构的第三方审计或安全测试
是否具备KYC、SML等反洗钱与用户身份识别制度
是否对外公开项目负责人、团队背景、资金来源路径等基本信息
是否具备高风险模块
是否存在高风险扩张方式
项目合法性提前审查：入职前还应重点审查项目是否合法。即便员工非主导方，若整体项目涉嫌非法金融活动，也可能被牵连调查。建议主动要求查阅项目白皮书、Token 分发机制、收益模型、是否面向中国大陆用户、是否含有返利结构、投资承诺等。
岗位人员风险排序
核心管理层＞宣传负责人or社区负责人（针对谁进行宣传、谁为核心用户，不能说不知道来摆脱干系）＞核心链条＞纯粹技术
知情推断
①事实知情 ②推定知情 ③应当知情
以上三种知情不能因为不知情而免除处罚。
即使是纯粹的技术提供者，也必须保持审慎判断。
明知可疑而不询问，知情推断。
免责的前提是尽力防范。

# 2025-08-06

Dapp笔记：https://postimg.cc/WqMNvtZV
今日实践任务：铸造第一个NFT+完成防诈骗https://unphishable.io/challenges/usdt-approval-phishing网站任务
防诈骗笔记：①种子短语陷阱
正规的钱包服务商或客服人员绝不会索要你的助记词。助记词是恢复钱包的私钥，任何拥有它的人都可以完全控制你的资金。
如何保护自己：
- 永远不要向任何人透露你的种子短语，无论他们自称是谁。
- 将您的种子短语离线存储在安全的位置。
- 使用硬件钱包来增加一层安全性。
- 如果您收到一条要求您输入种子短语的消息，那么这几乎肯定是一次网络钓鱼尝试。
- 如有疑问，请结束对话并直接联系官方渠道。
②恶意签名陷阱
如果签署了，会发生什么？
这个签名可以让攻击者控制你的 USDC 代币！使用 EIP-2612 许可签名，攻击者可以：
- 获得所有 USDC 的完全访问权限
- 未来可随时转移您的代币
- 无需您的进一步批准即可使用您的资金
这种攻击特别危险，因为它不需要链上交易，只需要签名。
钱包接口安全性分析
支出上限请求
此站点正在请求使用您的令牌的权限。
⚠️许可证类型
EIP-2612 代币授权签名
风险：此签名可以授予完整的令牌使用权限
🚨消费者（授权地址）
0x1234567890123456780012345678901234567890
风险：授权地址未知，可能存在恶意
🚨价值（授权金额）
无限MAX_UINT256（115,792,089,237...）
风险：请求无限制的代币使用权限
合同0x74a4a85c611679b73f402b36c0f84a7d2ccdfda3
到期时间2025年5月15日，06:31
取消 确认
🛡️ 安全检查站
- 检查许可证类型并了解授权范围
- 验证 Spender 地址是否来自可信来源
- 注意授权金额，谨防无限授权
- 确认网站来源的可信度
③恶意交易陷阱
🎁独家代币空投🎁
恭喜！您已被选中，可获得免费代币！
警告：这是一次网络钓鱼尝试！
该交易实际上是向合约 0xbe535a82f2c3895bdaceb3ffe6b9b80ac2f832a0 发送 0.5 ETH，而不是索取任何代币。
函数选择器 0x5fba79f5 调用一个名为 SecurityUpdate() 的函数，该函数可能会将您的资金转移给攻击者。
在实际情况下，如果不明白交易的作用，就不要签署交易！
在新版本的 MetaMask 中，您可以点击右上角查看方法，默认情况下可能不可见。
MetaMask 交易请求示例
请注意，钓鱼网站如何使用“安全更新”作为方法名称来诱骗用户相信这是一个钱包更新操作。
交易请求
请求来自我  ⚠️ HTTP 127.0.0.1:8080
与……互动我 ⚫0xBe535...832a0
方法我       安全更新
数量          0.5 ETH

# 2025-08-05

Web3防诈骗笔记
1.钱包复制陷阱
2.广告诈骗
3.恶意RPC商
工具：chainlist.org
chainlist上没有的RPC千万不要连！
4.恶意插件提供
假冒metamask
deepfake假zoom线上会议
5.虚拟交易（一般要进入钓鱼网站才会被诈骗）
学习以太坊概览：https://postimg.cc/94cYpM6C
下载Scam Sniffer

# 2025-08-04

今日份学习笔记：区块链基本内容+比特币的概念，笔记整理+二次复习
区块链基本内容：https://postimg.cc/VJQYfDMx
比特币的基本概念
作为奖励：网络节点服务提供商（以下简称为网络服务提供商）可以得到奖励。不同的网络服务提供商可以得到不同的代币奖励，比如：比特币。
货币属性：根据比特币的设计，它仅有有限的供应量，而且可以自由转账。因此具备了货币的特性，成为了加密货币。
比特币的特点：它具有货币的属性，按照白皮书的设计，不会通胀。同时根据区块链的特性它还具备以下特点：账户匿名、交易公开透明、不可篡改、完全在虚拟网络中，相比跨境转账结算等场景要方便许多。因此具备了一定的使用价值。
比特币的价格：它的价格由供需决定，与大白菜、股票、货币没有区别。只不过没有监管和限制，容易受到热点新闻的影响出现大幅度波动。
比特币的缺点：匿名和自由有利也有弊。弊端在于难以追踪和限制，所以经常被黑产利用，进行洗钱等违法犯罪活动。由于每打包一个区块需要约 10 分钟，因此也会影响交易的实时性。每个区块的存储数据也是有限的。不过，越来越多的新区块链技术正在优化和解决这些问题。
区块链如何实现交易公开透明且保护隐私：在整个网络中，你的交易将通过一个随机生成的钱包地址完成，与这个钱包地址相关的交易等在网络上全部是公开透明的。匿名是因为别人不知道你跟这个钱包有关联。如果你在社交媒体发布过这个钱包地址，那么就可以将其跟你联系在一起。
实践任务：1.使用sepolia进行挖矿
2.注册discord、twitter、metamask、zoom、notion等web3常用软件，并体验转测试币给学习同伴
3.学习并聆听以太坊周会


# 2025.07.30


<!-- Content_END -->
