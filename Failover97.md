---
timezone: UTC+8
---

# 敢敢

**GitHub ID:** Failover97

**Telegram:** @selah f

## Self-introduction

nus 研一 web3小白 通过blockchain 课程接触web3概念  究极P人努力变J

## Notes

<!-- Content_START -->

# 2025-08-27
<!-- DAILY_CHECKIN_2025-08-27_START -->
今天看了 Binance Research 的 2025 上半年加密行业报告。最大的感受是：市场像坐过山车，前面几个月跌得挺惨，后来又涨了回来。整体算下来，今年还是小涨。原来宏观环境（比如关税、地缘局势）对加密市场影响这么大。比特币依旧是老大。虽然涨幅不算特别夸张，大概 13% 左右，但它的市场份额一度冲到 65%，四年来最高。机构也在不断加码，企业合起来居然已经囤了接近 85 万枚 BTC。感觉比特币越来越变成金融体系的一部分，而不只是投机的工具。以太坊也挺有意思，开发活跃度依然最高，还有新的升级在推进。它周边的二层网络（L2）数量越来越多，有点过度饱和的感觉，但也能看出 Base、Arbitrum 这些比较稳，zk 系列还在努力降低成本。报告提到 L2 的竞争现在更多在“费用”和“去中心化”两个维度，看上去离大规模应用更近了一点。DeFi 这半年复苏很快，月活用户同比翻了两倍多，去中心化交易所的份额一度突破 29%，真有点和中心化交易所分庭抗礼的味道。不过报告也提醒，虽然用户活跃，但系统性风险没少，比如跨链桥和清算风险。稳定币部分我觉得最震撼：整体市值突破 2500 亿美金，其中 USDT 还是最大头，但 USDC 增长很猛，从市场份额 20% 提升到 25% 以上。半年交易量超过 7 万亿美元，主战场在 Tron、ETH 和 Solana。很明显，稳定币已经不只是“美元替代”，而是整个加密生态的结算血液。还有一点有趣的是，AI 和区块链的结合开始被重点提到：像 DeFAI（去中心化 AI）、DePIN（分布式物联网）、DeSci（去中心化科研）这些新概念，逐渐成为热点。感觉下半年大家会围绕 AI × 区块链的叙事继续炒作。监管部分也挺关键。美国换政府后态度似乎友好了不少，国会还在推进稳定币立法；欧洲的 MiCA 法案开始落地，给了合规企业一个明确框架；亚洲有点两极分化，香港继续鼓励，但新加坡反而更谨慎。总体看，合规正逐渐清晰，未来机构资金可能更愿意进来。

整体感受：上半年市场在震荡中找方向，比特币和稳定币的地位越来越稳固，以太坊和 L2 在跑马圈地，DeFi 回暖，AI 叙事抬头，监管也逐渐明朗。下半年可以重点盯稳币、以太坊升级、DeFi 的可持续，以及美国的监管动向。
<!-- DAILY_CHECKIN_2025-08-27_END -->


# 2025-08-26
<!-- DAILY_CHECKIN_2025-08-26_START -->
# **阅读最新资讯记录**

稳定币进入“联邦法 + 细则博弈”阶段  
GENIUS Act 已签署成法，财政部开始就配套细则征求意见。银行业担心“交易所/平台通过第三方产品变相付息”会造成存款外流，正在游说堵漏洞。这意味着美国的稳定币发行与分销会更像传统金融牌照业务，剩下的空间在“利息/奖励、准备金、发行主体与托管”的边界上博弈。

监管信号并非一边倒  
美联储 Bowman 的讲话强调“别过度保守”，与前述立法并不冲突：框架更清晰→更容易对接合规创新。对 Web3 创业者来说，合规产品（支付、清结算、链上身份/KYC）可能是 2025 下半年的确定性赛道。

以太坊 Pectra：从“可用性”切入的账户革命  
EIP-7702 允许 EOA 在一笔交易的上下文中“临时拥有合约逻辑”，等价于给普通地址插上“智能翅膀”。它既解决 3074 潜在的授权风险，又往全面 AA（Account Abstraction）迈一步——比如一次签名批量操作、社交恢复、付费代付更顺滑。

客户端多样化是公链韧性的底座（这篇看不太懂）  
Solana 的 Firedancer 不只是“更快”，更重要是客户端多样性，能降低单一实现的系统性风险；官方与社区报告都在推动 2025 年的分阶段主网上线。对开发者的启发：性能不是唯一指标，“多客户端 + 安全回滚预案 + Bug bounty” 才是工程化上线的关键。数据侧：AA 与 L2 进入“规模化可用”的拐点  
ERC-4337 的真实采用量级取决于口径，但“从几十万到百万级智能账户”的趋势明确；Base 的月活地址级别很高，说明用户不一定需要感知“加密”，就能在链上完成活动（Onchain Summer、应用补贴、法币入口等组合拳）。
<!-- DAILY_CHECKIN_2025-08-26_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
**重放攻击**

**原理**：攻击者在另一条链上（例如分叉链）重复广播合法交易。ETH 与 ETC 分叉后曾出现过跨链重放问题。**防御**：

使用 nonce（防止同一笔交易多次生效）。引入链 ID（EIP-155，签名中包含链 ID，使不同链的交易不兼容）。

**重入攻击**

**原理**：合约在发送 ETH（如 `call.value()`）时，攻击者合约可以在接收函数中再次调用原合约函数 → 导致状态还没更新前，逻辑被反复执行。

Checks-Effects-Interactions 模式（先修改状态，再进行外部调用）。

使用 `reentrancyGuard` 修饰器（OpenZeppelin 提供）。

避免直接用 `call.value()`，尽量使用 `transfer` 或 `send`。

**闪电贷攻击**

闪电贷允许用户在同一笔交易内借入巨额资金，只要在交易结束前偿还 → 攻击者利用这一点进行价格操纵、清算套利。

**防御**：价格预言机（Oracle）去中心化（Chainlink 等），避免依赖单一流动性池价格。引入时间加权平均价格（TWAP），降低单笔操纵风险。增加交易复杂度与验证机制，降低套利空间。

**溢出/下溢攻击**

uint 类型变量超出最大值后回绕，可能导致错误的余额计算。**防御**：使用 Solidity 0.8+ 自带的安全检查。

或使用 SafeMath 库（老版本常用）。

**前置交易攻击**

**原理**：攻击者监控交易池（mempool），抢先提交更高 Gas 的交易 → 抢跑套利。

**防御**：使用提交-揭示（Commit-Reveal）机制。使用私有交易通道（Flashbots, MEV-boost）。

**预言机攻击**

**原理**：如果合约依赖单一数据源，攻击者可操纵价格（如在低流动性池中砸盘）。

**防御**：使用去中心化预言机（如 Chainlink 多数据源）。引入数据聚合和延时更新。
<!-- DAILY_CHECKIN_2025-08-23_END -->

# 2025-08-21

稳定币 = 区块链世界里的“人民币/美元替身”，价格相对稳，不像 BTC、ETH 那么疯涨疯跌。

为啥重要？因为大家都需要一个 “链上结算单位”，要不然做 DeFi、交易、支付都很难。

分类小记

法币抵押：USDT / USDC → 后面有银行账户撑腰，但就是中心化，要信它的储备金。

加密抵押：DAI → 用 ETH 等加密资产抵押，去中心化一些，但风险=波动大时容易被清算。

算法稳定币：像 Terra UST → 理想很美好，靠算法调节供需，结果一崩就是死亡螺旋（血的教训）。

我觉得有意思的点

套利机制：当价格不是 1 美元，聪明人就会“买便宜卖贵”，价格自然被拉回去。

清算机制：感觉像是股票里“保证金爆仓”的链上版本，抵押不够就会被强平。

UST 崩盘：说明光靠算法，遇到恐慌还是 hold 不住。

# 2025-08-19

Uniswap V2
https://www.yuque.com/g/failover/pzucvi/naci9gfmr3gpsrlv/collaborator/join?token=dVBG4RMPu3rjeQP0&source=doc_collaborator# 《Uniswap V2》

# 2025-08-18

参加今天链上社交分享会 学习了js的相关语法 学成待续

# 2025-08-17

报错	错误原因
ParserError: Expected identifier but got '('
 constrctor( ){	constructor拼写错误
TypeError: Data location must be "memory" or "calldata" for parameter in function, 
but none was given.
  --> Lab2.sol:12:22:
   |
12 |     function add_Qty(uint256[] arr) public{
   |	局部数组变量必须申明是storage还是memory；对于简单的类型（unit,bool,address）不需要指定memory；对于复杂的引用类型（struct、array、string、bytes)必须指定存储位置是memory或者storage，否则编译会报错
DeclarationError: Undeclared identifier. "sum" is not (or not yet) visible at this point.
  --> Lab1exercise.sol:19:25:
   |
19 |             uint256 sum=sum+sgd[i]*qty[i];
   |                         ^^^	在solidity中循环变量必须在循环体外申明
TypeError: Member "numberOfSides" not found or not visible after argument-dependent lookup in uint256.
  --> tests/Lab2_problemsampleanswer/Lab2_p1/Dice1.sol:85:29:
   |
85 |         if(newNumber==dices[diceId.numberOfSides]){
   |                             ^^^^^^^^^^^^^^^^^^^^	dices mapping类型语法错误  应该是  dices[diceID].numberOfSides
ERC20 e = new ERC20();	创建了一个新的ERC20合约叫e,e所在的合约地址是新的合约地址由new ERC20()看出
	注意：solidity中的private和mapping变量不能直接从外部读取，所以需要用getter的方法，创建一个函数

# 2025-08-16

web3
初入职场技巧：真诚、敢于试错
工作方式：远程或现场  远程的话生活和工作融合在一起 扁平化 主动性
薪资：前者正常有合同 后者通过发U支付(全凭良心）  工资不交税 大公司对于法律保护
项目：短期项目以part-time 好项目再以full-time
筛选项目：tg\twitter\discord\官网\粉丝量\rotedata\项目方详聊内容判断\需要看看有没有刷粉嫌疑\官网白皮书\多级返佣机制和空投快跑

# 2025-08-14

https://www.notion.so/Web3-24f7fcc97cdf80c8b4d9cc6ac8ac2d02?source=copy_link
DeFi内容以及产品宣讲会

# 2025-08-13

今天学习了测试功能的js 以下是一些学习笔记
 在 JavaScript 里，某些操作（比如读取文件、网络请求、数据库查询、区块链交互等）需要一定的时间才能完成。JavaScript 采用异步编程（异步的意思指的是好几个耗时线程可以并行操作，不会被block)来避免阻塞主线程。await 让代码 按顺序执行，避免出现未完成的任务导致错误。  默认会把 I/O 操作（如数据库、网络请求、区块链调用） 设为异步   
什么是合约工厂？
合约工厂（Contract Factory）是用于部署新智能合约的对象。在 Hardhat + Ethers.js 中，你不能直接创建合约实例，而是需要先获取合约工厂，然后再部署合约。
这和现实中的“工厂”很类似：
● 合约文件 (DiceMarket.sol) 就像蓝图。
● 合约工厂 (getContractFactory("DiceMarket")) 就像一个工厂，可以根据蓝图生产多个合约实例。
● 部署后的合约 (DiceMarket.deploy(...)) 就像最终生产出来的产品。
关键字
async
● 申明一个异步函数，返回值始终是Promise
● 如果函数内部返回的是普通值，JavaScript会自动封装成Promise.resolve(返回值）
async function hello() {
    return "Hello, world!";
}

hello().then(console.log); // 输出: Hello, world!由于关键字的作用，所以需要用.then()处理

await
该关键字只能在async function内部使用，
它会暂停函数的执行，直到Promise解析（resolve),然后返回Promise解析之后的值
function delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

async function run() {
    console.log("开始");
    await delay(2000); // 等待 2 秒
    console.log("2 秒后执行");
}

run();

代码里的语法：
new Promise((resolve,reject)=>{.....}
resolve是一个函数，表示成功时候调用
reject也是一个函数，表示失败时候调用
setTimeout(resolve,ms)
setTimeout是JavaScript的定时器函数，作用是等待一段时间后执行某个操作
setTimeout(resolve,ms)代表等待ms毫秒后执行resolve()
console
在 JavaScript 中，console 是一个全局对象，主要用于打印调试信息，帮助开发者查看代码的执行情况。最常用的方法是 console.log()，但 console 还有很多其他功能。
console.log("Hello, JavaScript!");
console的其他用法


变量声明和数据类型
变量声明的三种方式
// let - 推荐使用,可以重新赋值
let age = 25;
age = 26; // 允许

// const - 用于常量,不能重新赋值
const PI = 3.14;
// PI = 3.15; // 这会报错

// var - 旧版本使用,现在不推荐
var name = "John";
基本数据类型
// 数字 (Number)
let count = 42;        // 整数
let price = 99.99;     // 小数
let infinity = Infinity;//特殊用法，表示无穷大
let notANumber = NaN;//not a number

// 字符串 (String)
let firstName = "Alice";
let greeting = 'Hello';
let message = `Hello ${firstName}`; // 模板字符串

// 布尔值 (Boolean)
let isActive = true;
let isLoggedIn = false;

// 空值
let empty = null;
let undefined;

// 数组 (Array);❤这里的数组跟python里面的列表特别像
let colors = ['red', 'green', 'blue'];
let numbers = [1, 2, 3, 4, 5];

// 对象 (Object) 对象非常像C语言里面的结构体
let person = {
    name: "Alice",
    age: 25,
    isStudent: true
};
类型转换
// 转换为数字
let strNumber = "123";
let num1 = Number(strNumber);    // 123
let num2 = parseInt(strNumber);  // 123
let num3 = parseFloat("123.45"); // 123.45  
let bigNum=BigInt("123455677766");//bigint专门用于大整数

// 转换为字符串
let number = 123;
let str1 = String(number);      // "123"
let str2 = number.toString();   // "123"

// 转换为布尔值
let bool1 = Boolean(1);         // true
let bool2 = Boolean("");        // false
let bool3 = Boolean("hello");   // true
NaN
not a number；通常用于用户的输入校验
// 1. 验证用户输入是否为有效数字
function validateUserInput(input) {
    let number = parseFloat(input);
    if (isNaN(number)) {
        return "请输入有效的数字";
    }
    return "输入有效";
}

// 2. 数学计算错误检查
function divideNumbers(a, b) {
    let result = a / b;
    if (isNaN(result)) {
        return "计算出错";
    }
    return result;
}
注意校验NaN不需要用==号，可以直接用isNaN
Hoisting 
function关键字定义的函数会被完整的提升，可以在定义前调用
hello(); // 输出: Hello, world!

function hello() {
    console.log("Hello, world!");
}
/*其实就相当于*/
function hello() { // 函数声明被提升
    console.log("Hello, world!");
}

hello(); // 调用

箭头函数
箭头函数的语法
const 函数名 = (参数) => { 函数体 }
const add = (a, b) => {
    return a + b;
};
console.log(add(2, 3)); // 输出: 5

隐式返回
如果函数体只有一行return语句，可以省略{}和return
const multiply = (a, b) => a * b;
console.log(multiply(3, 4)); // 输出: 12
参数规则

# 2025-08-11

https://blog.csdn.net/fxz315/article/details/150229557?spm=1001.2014.3001.5502 solidity 笔记
参加知识分享会内容 ERC20正在学习中

# 2025-08-09

今天好像没有玩上游戏 做了一下unphishable 明天苦命留子要敢去开学了 不打卡 哈哈哈哈哈哈哈哈哈哈（低能量老鼠人苦笑）

# 2025-08-08

回顾之前的知识点并且整理笔记  会看昨天法律的知识分享会

# 2025-08-06

今天听了Marcus老师的知识分享会  
笔记https://blog.csdn.net/fxz315/article/details/149916421?spm=1001.2014.3001.5501

# 2025-08-05

尝试转了一下测试币 其他待续 听了安全讲座 over

# 2025-08-04

区块链    区块链+分布式网络 =去中心化、公开透明、不可篡改  通过消耗算力（proof of work）来获得区块奖励  ---比特币
以太坊转向proof of stake,支持DApp开发、智能合约执行、NFT、DeFi.Gas是以太坊的燃料
OKA objective  key result ;以目标导向代替工作量导向；以实际进度为导向；明确性和量化是团队远程工作的重要特点；


# 2025.07.31


<!-- Content_END -->
