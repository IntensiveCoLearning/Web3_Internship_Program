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
# 2025-08-18

参加今天链上社交分享会 学习了js的相关语法 学成待续

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
