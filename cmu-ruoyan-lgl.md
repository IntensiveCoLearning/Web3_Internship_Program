---
timezone: UTC+8
---

# 若言

**GitHub ID:** cmu-ruoyan-lgl

**Telegram:** @ruoyan_ton

## Self-introduction

web2游戏大厂工作两年

## Notes

<!-- Content_START -->
# 2025-08-19

## solidity中的Gas优化方案

### 1. **变量声明优化**

#### 变量打包（Packing）
```solidity
// ❌ 浪费Gas：每个变量占用32字节
contract Bad {
    uint256 a; // 32 bytes
    uint256 b; // 32 bytes  
    uint256 c; // 32 bytes
}

// ✅ 节省Gas：打包到同一槽位
contract Good {
    uint128 a; // 16 bytes
    uint128 b; // 16 bytes
    uint256 c; // 32 bytes
}
```
**Gas节省**：每个槽位节省约20,000 gas（存储操作）

#### 变量类型选择
```solidity
// ❌ 浪费Gas
uint256 small = 1; // 32字节，浪费31字节

// ✅ 节省Gas
uint8 small = 1;   // 1字节，节省31字节
```
**Gas节省**：每个变量节省约5,000-15,000 gas

### 2. **循环优化**

#### 循环边界优化
```solidity
// ❌ 浪费Gas：每次循环都读取数组长度
for (uint i = 0; i < array.length; i++) {
    // 操作
}

// ✅ 节省Gas：缓存数组长度
uint length = array.length;
for (uint i = 0; i < length; i++) {
    // 操作
}
```
**Gas节省**：每次循环节省约100 gas

#### 循环展开
```solidity
// ❌ 标准循环
for (uint i = 0; i < 4; i++) {
    array[i] = i;
}

// ✅ 循环展开（适用于小循环）
array[0] = 0;
array[1] = 1;
array[2] = 2;
array[3] = 3;
```
**Gas节省**：小循环可节省约500-1,000 gas

### 3. **函数优化**

#### 函数可见性
```solidity
// ❌ 浪费Gas：public函数自动生成getter
uint256 public data;

// ✅ 节省Gas：明确指定可见性
uint256 private data;
```
**Gas节省**：每个public变量节省约2,000 gas

#### 函数参数优化
```solidity
// ❌ 浪费Gas：使用storage引用
function badFunction(uint[] storage arr) internal {
    uint[] storage temp = arr;
}

// ✅ 节省Gas：使用calldata
function goodFunction(uint[] calldata arr) external pure {
    // 直接使用arr，无需复制
}
```
**Gas节省**：使用calldata可节省约5,000-20,000 gas

### 4. **存储操作优化**

#### 批量操作
```solidity
// ❌ 浪费Gas：多次存储操作
function badBatch() external {
    data[0] = 1; // 20,000 gas
    data[1] = 2; // 20,000 gas
    data[2] = 3; // 20,000 gas
}

// ✅ 节省Gas：使用结构体批量存储
struct BatchData {
    uint256 a;
    uint256 b;
    uint256 c;
}
function goodBatch() external {
    BatchData memory batch = BatchData(1, 2, 3);
    // 一次性存储，节省gas
}
```
**Gas节省**：批量操作可节省约10,000-30,000 gas

#### 存储位置选择
```solidity
// ❌ 浪费Gas：频繁修改storage
uint256[] public storageArray;

// ✅ 节省Gas：使用memory处理后再存储
function processArray() external {
    uint256[] memory tempArray = new uint256[](100);
    // 在memory中处理
    // 最后一次性存储到storage
}
```
**Gas节省**：memory操作比storage便宜约15,000-25,000 gas

### 5. **事件优化**

#### 事件参数优化
```solidity
// ❌ 浪费Gas：过多indexed参数
event BadEvent(
    address indexed from,
    address indexed to,
    uint256 indexed value, // 第三个indexed参数
    string message
);

// ✅ 节省Gas：合理使用indexed
event GoodEvent(
    address indexed from,
    address indexed to,
    uint256 value,        // 非indexed，节省gas
    string message
);
```
**Gas节省**：减少indexed参数可节省约375 gas

### 6. **Gas成本对比表**

| 操作类型 | Gas成本 | 优化建议 |
|---------|---------|----------|
| **存储写入** | 20,000 gas | 批量操作，减少存储次数 |
| **存储读取** | 2,100 gas | 缓存变量，避免重复读取 |
| **内存分配** | 3 gas/字节 | 使用calldata，减少内存复制 |
| **函数调用** | 2,100 gas | 内联小函数，减少调用开销 |
| **循环迭代** | 100-500 gas/次 | 循环展开，缓存边界值 |
| **事件日志** | 375 gas + 数据gas | 减少indexed参数，优化数据结构 |

### 7. **实际优化示例**

```solidity
// ❌ 未优化的合约
contract Unoptimized {
    uint256 public value1;
    uint256 public value2;
    uint256 public value3;
    
    function setValues(uint256 a, uint256 b, uint256 c) external {
        value1 = a;
        value2 = b;
        value3 = c;
    }
    
    function processArray(uint256[] storage arr) external {
        for (uint i = 0; i < arr.length; i++) {
            arr[i] = arr[i] * 2;
        }
    }
}

// ✅ 优化后的合约
contract Optimized {
    // 变量打包
    uint128 public value1;
    uint128 public value2;
    uint256 public value3;
    
    // 批量设置，减少存储操作
    function setValues(uint128 a, uint128 b, uint256 c) external {
        value1 = a;
        value2 = b;
        value3 = c;
    }
    
    // 缓存数组长度，使用memory处理
    function processArray(uint256[] calldata arr) external pure returns (uint256[] memory) {
        uint256[] memory result = new uint256[](arr.length);
        uint256 length = arr.length;
        
        for (uint i = 0; i < length; i++) {
            result[i] = arr[i] * 2;
        }
        return result;
    }
}
```

**总Gas节省**：优化后的合约可节省约40,000-80,000 gas（取决于操作复杂度）

### 8. **最佳实践总结**

1. **变量声明**：合理打包，选择合适类型
2. **循环优化**：缓存边界值，考虑循环展开
3. **函数设计**：减少storage操作，使用calldata
4. **存储策略**：批量操作，减少存储次数
5. **事件优化**：合理使用indexed参数
6. **代码审查**：定期检查gas使用情况

通过这些优化，可以将合约的gas消耗降低20-40%，显著提升用户体验和成本效益。

---

## Gas费用与ETH换算关系

### 1. **基本换算公式**

```
交易费用 = Gas使用量 × Gas价格
```

其中：
- **Gas使用量**：合约执行所需的计算资源（固定值）
- **Gas价格**：用户愿意为每个Gas单位支付的ETH数量（可调整）

### 2. **Gas价格单位换算**

#### Wei（最小单位）
```solidity
1 ETH = 10^18 Wei
1 Gwei = 10^9 Wei
1 ETH = 10^9 Gwei
```

#### 常用Gas价格范围
| 网络拥堵程度 | Gas价格范围 | 说明 |
|-------------|-------------|------|
| **低拥堵** | 5-20 Gwei | 交易确认时间：1-2分钟 |
| **中等拥堵** | 20-50 Gwei | 交易确认时间：2-5分钟 |
| **高拥堵** | 50-100+ Gwei | 交易确认时间：5-15分钟 |
| **极端拥堵** | 100-500+ Gwei | 交易确认时间：15分钟以上 |

### 3. **实际费用计算示例**

#### 简单转账交易
```solidity
// 标准ETH转账：21,000 gas
Gas使用量 = 21,000 gas
Gas价格 = 20 Gwei = 20 × 10^-9 ETH

交易费用 = 21,000 × (20 × 10^-9) = 0.00042 ETH
// 约等于 $0.84（假设ETH = $2,000）
```

#### 智能合约交互
```solidity
// 存储写入操作：20,000 gas
Gas使用量 = 20,000 gas
Gas价格 = 30 Gwei = 30 × 10^-9 ETH

交易费用 = 20,000 × (30 × 10^-9) = 0.0006 ETH
// 约等于 $1.20（假设ETH = $2,000）
```

#### 复杂合约操作
```solidity
// 批量操作：100,000 gas
Gas使用量 = 100,000 gas
Gas价格 = 25 Gwei = 25 × 10^-9 ETH

交易费用 = 100,000 × (25 × 10^-9) = 0.0025 ETH
// 约等于 $5.00（假设ETH = $2,000）
```

### 4. **Gas价格动态调整机制**

#### EIP-1559费用模型
```solidity
// 新费用结构
总费用 = 基础费用 + 优先费用

其中：
- 基础费用：被销毁，动态调整
- 优先费用：给矿工的小费
```

#### 基础费用计算
```solidity
// 基础费用根据区块使用率动态调整
if (区块使用率 > 50%) {
    基础费用 += 12.5%
} else if (区块使用率 < 50%) {
    基础费用 -= 12.5%
}
```

### 5. **不同网络Gas价格对比**

| 网络 | 平均Gas价格 | 特点 |
|------|-------------|------|
| **以太坊主网** | 15-50 Gwei | 安全性最高，费用较高 |
| **Polygon** | 0.1-1 Gwei | 费用极低，确认快速 |
| **BSC** | 3-10 Gwei | 费用适中，兼容性好 |
| **Arbitrum** | 0.1-0.5 Gwei | 费用低，L2解决方案 |

### 6. **Gas费用优化策略**

#### 时间选择
```solidity
// 选择低峰期执行交易
// 美国东部时间：凌晨2-6点
// 欧洲时间：凌晨3-7点
// 亚洲时间：下午2-6点
```

#### 价格设置策略
```solidity
// 1. 设置Gas价格上限
maxFeePerGas: 50 Gwei
maxPriorityFeePerGas: 2 Gwei

// 2. 使用动态Gas价格
// 根据网络拥堵程度自动调整
```

### 7. **实际开发中的费用估算**

#### 使用Hardhat估算
```javascript
// 估算Gas使用量
const gasEstimate = await contract.estimateGas.functionName(params);
console.log(`预估Gas: ${gasEstimate.toString()}`);

// 估算费用
const gasPrice = await ethers.provider.getGasPrice();
const fee = gasEstimate.mul(gasPrice);
console.log(`预估费用: ${ethers.utils.formatEther(fee)} ETH`);
```

#### 使用Web3.js估算
```javascript
// 估算Gas
const gasEstimate = await contract.methods.functionName(params).estimateGas();
console.log(`预估Gas: ${gasEstimate}`);

// 获取当前Gas价格
const gasPrice = await web3.eth.getGasPrice();
const fee = gasEstimate * gasPrice;
console.log(`预估费用: ${web3.utils.fromWei(fee, 'ether')} ETH`);
```

### 8. **费用监控工具**

#### 实时监控网站
- **Etherscan Gas Tracker**：实时Gas价格
- **GasNow**：Gas价格预测
- **ETH Gas Station**：详细费用分析

#### 开发工具集成
```javascript
// 在DApp中集成Gas价格显示
async function getGasPrice() {
    const gasPrice = await provider.getGasPrice();
    const gwei = ethers.utils.formatUnits(gasPrice, 'gwei');
    return `${gwei} Gwei`;
}
```

### 9. **总结**

1. **Gas费用 = Gas使用量 × Gas价格**
2. **1 ETH = 10^9 Gwei = 10^18 Wei**
3. **标准转账：21,000 gas × 20 Gwei = 0.00042 ETH**
4. **合约交互：20,000-100,000+ gas，费用因操作复杂度而异**
5. **选择合适时机和Gas价格可显著降低交易成本**
6. **使用工具监控和估算Gas费用，优化用户体验**

# 2025-08-18

整理开发快捷键
## Cursor

### 终端

在/Users/ruoyan/Library/Application Support/Cursor/User/keybindings.json中修改快捷键
#### 创建一个新的cusor内置终端并聚焦
```json
{
  "key": "ctrl+cmd+n",
  "command": "workbench.action.terminal.new"
}
```

#### 聚焦到cursor内置终端
```json
{
  "key": "alt+w",
  "command": "workbench.action.terminal.focus"
}
```

#### 在cursor内置的终端之间切换
```json
{
  "key": "alt+q",
  "command": "workbench.action.terminal.focusNext"
}
```

#### 关闭当前cursor内置的终端
```json
{
  "key": "alt+r",
  "command": "workbench.action.terminal.kill"
}
```

### AI CHAT

#### 打开ai chat页面，同时引用选定当前内容，如果已经打开则隐藏ai chat页面
```json
{
  "key": "cmd+l",
  "command": "aichat.newchataction"
}
```

### PAGE

#### 在打开的页面（标签）中切换
```json
{
  "key": "ctrl+tab",
  "command": "workbench.action.quickOpenNavigateNextInEditorPicker",
  "when": "inEditorsPicker && inQuickOpen"
}
```

#### 聚焦到当前页面（标签）
```json
{
  "key": "alt+e",
  "command": "workbench.action.focusActiveEditorGroup",
  "when": "terminalFocus || panelFocus || sideBarFocus"
}
```

#### 关闭当前页面（标签）
```json
{
  "key": "alt+x",
  "command": "workbench.action.closeActiveEditor",
  "when": "editorIsOpen"
}
```

#### 打开上一个页面（标签）
```json
{
  "key": "alt+cmd+left",
  "command": "workbench.action.nextEditor"
}
```

#### 打开下一个页面（标签）
```json
{
  "key": "alt+cmd+right",
  "command": "workbench.action.nextEditor"
}
```

#### 将焦点置于主侧栏
```json
{
  "key": "cmd+0",
  "command": "workbench.action.focusSideBar"
}
```

#### 查看: 聚焦到面板中
```json
{
  "key": "alt+t",
  "command": "workbench.action.focusPanel"
}
```


## foundry


### forge

**典型工作流**：
```bash
forge init my_project     # 初始化项目
forge build               # 编译合约
forge test                # 运行测试（支持模糊测试）
forge test --mt ${函数名称} -vvvvv  # 测试指定测试合约中过的函数
forge script Deploy       # 部署脚本执行
forge fmt                 # 代码格式化
forge install OpenZeppelin/openzeppelin-contracts
forge selectors find "balanceOf(address)"  # 0x70a08231
```

### cast

**常用场景**：
```bash
cast send <合约> "函数" 参数    # 发送交易
cast call <合约> "视图函数"      # 查询状态
cast block latest              # 获取最新区块
cast --to-base 0x1 hex         # 数据格式转换
cast sig "transfer(address)"   # 计算函数选择器
```

### anvil

```bash
anvil    # 本地虚拟网络
```

### make

生成如下makefile
```bash
include .env

deploy_ruoyancoin:
    @echo "deploying contract..."
    forge create src/ruoyancoin.sol:ruoyancoin --private-key ${OWNER_PRIVAYE_KEY} --broadcast --constructor-args ${OWNER_ADDRESS}

mint:
	@echo "Minting tokens..."
	cast send ${CONTRCT_ADDRESS} "mint(uint256)" ${MINT_AMOUNT} --private-key ${OWNER_PRIVATE_KEY}

```


## react



## next.js 15

## wagmi (建议用这个构建web3前端,底层是ether.js)

## rainbowkit

# 2025-08-17

今天大概是这周最忙的一天，感受颇深，上午起来就开始忙黑客松的事，过程中越发感觉一个团队必须有真正的纪律流程体系才能做到井井有条，不然可能只是瞎忙，而且没有效率，结果也不会好。想起我工作的第一家公司，公司入职培训的第一课就是“高效能人士的7个习惯”，而第二家公司的主管一直强调“要成为一个可靠的人”，还好我一直把这个要求自己。今天又回顾了下实习手册，bruce老师说的：“靠谱”的实习生应该有“可预期、可沟通、可复盘”的特性，确实是在团队协作中不可缺少的部分，之后也要一直要求自己。
遇到的问题和感悟：
1.下午运营组沟通进度开会只有5人：
- 没有提前沟通好，距离开会的前1小时才进行约定开会
- 依靠自觉是脆弱的，没有纪律和规定不行
2.制作问答PPT的半小时“抓马”
- 沟通上出现问题，在notion中应该将重点的部分加粗，或直接分配到组员任务中
- 对使用AI工具的不熟练，应该提前学习熟悉可能用到的ai工具，以备不时之需：准备明天开始figma学习

# 2025-08-16

- 继续跟进关于休闲黑客松的各种筹备事项
- 设计休闲黑客松预热活动：规划web3知识问答环节https://www.notion.so/ethpanda/251bbd63be8780369a70e166ec146835
- 第一次用词根的办法背单词，感觉好爽，会记得比混着记强

# 2025-08-15

- [ ] 参加上午的黑客松运营组会，了解具体流程和分工细节
- [ ] 整理当前的黑客松运营分工，构建清单实现工作溯源和工作留痕
- [ ] 整理技术和运营的学习笔记，应对晚上的知识分享

# 2025-08-14

# Day 13 - 2025年08月14日 

---

## 今日学习目标

参加两会，整理学习内容
策划黑客松事项

## 学习内容

### Library

在 Solidity 中，**库（Library）** 是可复用的代码模块，用于封装常用功能（如数学运算、地址操作等）。它们类似于合约，但有以下核心区别：

#### 核心特性
1. **无状态性**：不能定义存储变量（但可操作调用合约的存储）
2. **无 ETH 管理**：不能接收或持有 ETH（除非特殊设计）
3. **部署节省**：仅部署一次，多个合约可复用
4. **调用方式**：
   - 内部函数：直接嵌入调用合约（无 Gas 开销）
   - 外部函数：通过 `DELEGATECALL` 执行（操作调用合约的存储）

---

#### 实用场景 & 代码示例

#### 1️⃣ 安全数学运算（防溢出）
```solidity
// 安全数学库
library SafeMath {
    // 加法（防溢出）
    function add(uint256 a, uint256 b) internal pure returns (uint256) {
        uint256 c = a + b;
        require(c >= a, "Overflow!");
        return c;
    }

    // 减法（防下溢）
    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
        require(b <= a, "Underflow!");
        return a - b;
    }
}

// 使用库的合约
contract Token {
    using SafeMath for uint256; // 绑定到 uint256 类型
    uint256 public totalSupply;
    
    function mint(uint256 amount) public {
        totalSupply = totalSupply.add(amount); // 安全加法
    }
}
```

#### 2️⃣ 地址工具（ETH 安全转账）
```solidity
// 地址工具库
library Address {
    // 安全发送 ETH
    function sendETH(address payable recipient, uint256 amount) internal {
        require(address(this).balance >= amount, "Insufficient balance");
        (bool success, ) = recipient.call{value: amount}("");
        require(success, "Transfer failed");
    }
}

// 使用库的合约
contract Payment {
    using Address for address; // 绑定到 address 类型
    
    function pay(address payable receiver) public payable {
        receiver.sendETH(msg.value); // 安全发送 ETH
    }
}
```

#### 3️⃣ 数据类型扩展（数组操作）
```solidity
// 数组工具库
library ArrayUtils {
    // 查找元素索引
    function findIndex(uint[] storage arr, uint value) internal view returns (int) {
        for (uint i = 0; i < arr.length; i++) {
            if (arr[i] == value) return int(i);
        }
        return -1; // 未找到
    }
}

contract DataManager {
    using ArrayUtils for uint[]; // 绑定到 uint[] 类型
    uint[] public numbers = [10, 20, 30];
    
    function getIndex(uint value) public view returns (int) {
        return numbers.findIndex(value); // 扩展数组功能
    }
}
```

---

#### 关键使用技巧
1. **绑定数据类型**  
   `using LibName for DataType;`  
   使数据类型可直接调用库函数（如 `myNumber.add(5)`）

2. **存储操作权限**  
   若库函数需修改状态，必须声明为 `internal` 且第一个参数使用 `storage` 引用：
   ```solidity
   function updateStorage(uint[] storage arr) internal {
       arr[0] = 100; // 直接修改调用合约的存储
   }
   ```

3. **Gas 优化**  
   - 内部函数：编译时嵌入代码（零调用开销）
   - 外部函数：通过 `DELEGATECALL` 调用（适合复杂逻辑）

---

#### 注意事项
⚠️ **避免循环依赖**：库不能调用合约或其他库的非内联函数  
⚠️ **版本兼容**：使用与合约相同的 Solidity 版本编译  
⚠️ **存储安全**：通过 `DELEGATECALL` 调用的库可修改调用合约的任意存储

通过合理使用库，可显著提升合约的代码复用性、安全性和可维护性。

## solidity中的自定义类型

在 Solidity 中，**自定义类型（User-Defined Value Types）** 是从 Solidity 0.8.8 版本引入的特性，允许开发者基于现有基本类型创建具有语义的新类型，增强代码可读性和类型安全性。以下是对其核心概念的详细说明：

---

### 一、自定义类型的核心作用
1. **类型安全**  
   避免相同底层类型的值被意外混用（如 `USD` 和 `ETH` 同是 `uint256` 但逻辑不同）。
2. **语义化**  
   通过类型名称显式表达数据的含义（如 `type Balance is uint256`）。
3. **零运行时成本**  
   编译后被优化为底层类型，不增加 gas 消耗。

---

### 二、语法定义
```solidity
// 使用 'type' 关键字定义新类型
type NewTypeName is UnderlyingType;

// 示例：定义表示价格的类型
type Price is uint256;
```

---

### 三、使用步骤
#### 1. 类型转换
自定义类型与底层类型需显式转换：
```solidity
// 包装（底层类型 → 自定义类型）
Price price = Price.wrap(100); 

// 解包（自定义类型 → 底层类型）
uint256 rawValue = Price.unwrap(price);
```

#### 2. 运算符重载（可选）
为自定义类型定义运算符（需通过库）：
```solidity
library PriceLib {
    // 加法操作
    function add(Price a, Price b) internal pure returns (Price) {
        return Price.wrap(Price.unwrap(a) + Price.unwrap(b));
    }
}

// 绑定运算符
using { PriceLib.add as + } for Price global;

// 使用
Price total = Price.wrap(200) + Price.wrap(300); // 直接使用 '+'
```

---

### 四、实际应用案例
#### 场景：防止货币单位混淆
```solidity
type USD is uint256;
type ETH is uint256;

contract PaymentProcessor {
    USD public usdBalance;
    ETH public ethBalance;

    // 存款 USD
    function depositUSD(USD amount) external {
        usdBalance = USD.wrap(USD.unwrap(usdBalance) + USD.unwrap(amount));
    }

    // 存款 ETH（类型安全：禁止传入 USD 类型）
    function depositETH(ETH amount) external {
        ethBalance = ETH.wrap(ETH.unwrap(ethBalance) + ETH.unwrap(amount));
    }
}
```
- **类型错误将被编译器捕获**：  
  `depositETH(USD.wrap(100))` 会编译失败，因为参数类型不匹配。

---

### 五、重要注意事项
1. **底层类型限制**  
   只能基于**值类型**（如 `uint`、`address`、`bytes32`）定义，不支持复杂类型。
2. **全局唯一性**  
   同一作用域内类型名不可重复。
3. **ABI 透明性**  
   在合约 ABI 中会显示为底层类型（外部调用时无类型信息）。
4. **运算符需手动定义**  
   默认不支持算术运算（如 `+`、`-`），需通过库扩展。

---

### 六、对比其他方案
| **特性**         | 自定义类型             | 结构体（Struct）       |
|------------------|-----------------------|-----------------------|
| 存储成本         | 同底层类型（0 开销）   | 额外存储空间          |
| 运行时安全性     | 编译时检查             | 需手动验证            |
| 运算符支持       | 需显式重载             | 不支持                |
| 语义表达         | 轻量级                 | 适合复杂数据结构      |

---

### 总结
Solidity 的自定义类型是提升合约安全性和可读性的高效工具，尤其适合需要**区分逻辑含义相同底层类型**的场景（如货币单位、度量单位）。通过编译时类型检查，它能有效预防低级错误，且无需牺牲 gas 效率。

## solidity中的gas优化方案

在 Solidity 中优化 Gas 消耗是智能合约开发的核心技能，以下是经过实践验证的 15+ 省 Gas 技巧，附带详细说明和代码示例：

---

### 一、存储优化（存储操作是最昂贵的）
1. **状态变量打包**
   ```solidity
   // 优化前：3个存储槽
   uint256 a; // 槽0
   uint256 b; // 槽1
   bool c;    // 槽2 (浪费31字节)

   // 优化后：1个存储槽
   uint128 a; // 槽0 (前16字节)
   uint128 b; // 槽0 (后16字节)
   bool c;    // 槽0 (最后1字节)
   ```
   - 原则：将 <32 字节的变量连续声明，自动打包进同一存储槽

2. **使用 `immutable` 和 `constant`**
   ```solidity
   address immutable owner;  // 部署时设置，存储于字节码
   uint256 constant FEE = 500; // 直接编译进字节码
   ```

3. **最小化存储写入**
   ```solidity
   // 避免重复写入相同值
   function update(uint256 newVal) external {
       if (value != newVal) { // 检查是否变化
           value = newVal;
       }
   }
   ```

---

### 二、内存与 Calldata 优化
4. **优先使用 `calldata`**
   ```solidity
   // 比 memory 节省拷贝成本
   function process(uint[] calldata arr) external {
       uint len = arr.length;
       // 直接读取 calldata
   }
   ```

5. **内存变量缓存状态读取**
   ```solidity
   function calculate() external {
       // 高成本：多次 SLOAD
       uint total = value1 + value1 * value2; 
       
       // 优化：1次 SLOAD
       uint v1 = value1;
       uint total = v1 + v1 * value2;
   }
   ```

---

### 三、函数与操作优化
6. **短路模式排序**
   ```solidity
   // 将最可能失败的检查放前面
   require(msg.sender == owner, "Not owner");
   require(balance[msg.sender] >= amount, "Insufficient balance");
   ```

7. **循环优化**
   ```solidity
   for(uint i=0; i<arr.length; i++) { 
       // 高成本：每次循环读取 arr.length
   }

   // 优化：缓存长度
   uint len = arr.length;
   for(uint i=0; i<len; i++) {
       // 只读1次长度
   }
   ```

8. **使用 `external` 替代 `public`**
   ```solidity
   // public：参数拷贝到 memory
   function process(uint[] memory arr) public 
   
   // external：直接使用 calldata
   function process(uint[] calldata arr) external
   ```

---

### 四、数据类型优化
9. **小整数打包**
   ```solidity
   struct User {
       uint64 id;      // 8字节
       uint32 score;   // 4字节
       address wallet;// 20字节
   } // 总32字节 = 1存储槽
   ```

10. **使用 `bytes32` 替代 `string`**
    ```solidity
    bytes32 name; // 固定大小，低Gas操作
    string name;  // 动态类型，操作成本高
    ```

---

### 五、高级优化技巧
11. **汇编打包读写**
    ```solidity
    function readSlot(uint slot) view returns (bytes32 val) {
        assembly {
            val := sload(slot)
        }
    }
    ```

12. **EIP-2929 热地址优化**
    - 对已访问过的存储槽/地址，后续操作 Gas 更低
    - 设计：将频繁访问的数据放在固定存储位置

13. **Gas Refund 机制**
    ```solidity
    // 清空存储槽可获得 Gas 返还
    delete storageSlot; // 返还 15,000 Gas
    ```

---

### 六、部署与架构优化
14. **代理合约模式**
    - 通过代理合约升级逻辑，避免重复部署
    - 使用 OpenZeppelin TransparentProxy

15. **合约大小优化**
    - 使用库合约分离逻辑
    - 避免过长的错误信息
    ```solidity
    require(condition, "Long error message..."); // 消耗更多部署Gas
    require(condition); // 使用默认错误
    ```

---

### 七、Gas 消耗对比表
| 操作                | Gas 成本      | 优化建议               |
|---------------------|--------------|-----------------------|
| SSTORE (新值)       | 20,000-22,100| 减少存储写入次数       |
| SSTORE (修改值)     | 2,900-5,000  | 避免冗余修改           |
| SLOAD               | 2,100        | 用内存变量缓存         |
| CALLDATALOAD        | 3            | 优先用 calldata        |
| 内存数组写入        | 3-10/元素    | 控制数组大小           |
| Keccak256 哈希      | 30-70/字节   | 避免大数据哈希         |

---

### 实战示例：转账优化
```solidity
// 优化前
function transfer(address to, uint amount) public {
    require(balances[msg.sender] >= amount);
    balances[msg.sender] -= amount;
    balances[to] += amount; // 可能触发新存储槽（高成本）
}

// 优化后：使用 OpenZeppelin SafeMath 库
import "@openzeppelin/contracts/utils/math/SafeMath.sol";

function safeTransfer(address to, uint amount) public {
    balances[msg.sender] = balances[msg.sender].sub(amount);
    balances[to] = balances[to].add(amount);
}
```

---

### 最佳实践总结：
1. **黄金法则**：减少存储写入（SSTORE）
2. 使用 Solidity 0.8+ 编译器并启用优化器：`solc --optimize --runs=200`
3. 对热路径代码（高频调用函数）重点优化
4. 使用 Gas 跟踪工具：Hardhat Gas Reporter、Etherscan Gas Tracker
5. 优先使用经过审计的优化库（OpenZeppelin、Solmate）


## 学习笔记


## 问题与思考


## 总结


---

# 2025-08-13

# DEX

## AMM（Automated Market Maker）

- Market Maker**（控制币对的交易价格比较平滑，避免出现价格从2美元突然降到1美元）**
- Liquidity
- Liquidity Provider

## 去中心化交易所核心要素

- 任何人都可以添加流动性，成为 LP，并拿到 LP Token
- LP 在任意时间可以移除流动性并销毁 LP Token，拿回自己的 Token
- 用户可以基于交易池来进行交易
- 交易时收取一定手续费，并且分配给 LP

## Constant Product Automated Market Maker（CPAMM，恒定乘积自动做市商）

- 添加流动性：注入流动性/初始价格确定
- 交易：$x*y=(x+\Delta x)*(y-\Delta y)=k$，滑点
- 移除流动性：无偿损失等

**这里我们要注意，swap 的时候会出现滑点，而在添加/移除流动性时则会出现无常损失，要区分好**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/49dfc960-b5ce-4462-be6c-aa0cb64627ca/Untitled.png)

## 公式推演

$x*y=k$，$(x+\Delta x)*(y+\Delta y)=k$，知$\Delta x$，求$\Delta y$

1. 交换

$$
\begin{aligned}
x*y &=(x + \Delta x) * (y - \Delta y) \\
&= x*y + \Delta x * y - x *  \Delta y - \Delta x * \Delta y
\end{aligned}
$$

由上面可得，

$$
x*\Delta y + \Delta x*\Delta y = \Delta x * y 
\Rightarrow 
\Delta y = \frac{\Delta x * y}{x + \Delta x}
$$

1. 添加流动性

$$
\frac{x}{y} = \frac{x+\Delta x}{y+\Delta y}
$$

由上面可得，

$$
x*y + x*\Delta y = x*y + \Delta x*y 
\Rightarrow 
\Delta y = \frac{\Delta x*y}{x}
\Rightarrow
\frac{\Delta x}{\Delta y} = \frac{x}{y}
$$

- 为什么在 $x*y=k$中，$k$ 实际取 $k = \sqrt{x * y}$，而不是取 $x * y$?

这里是因为想要让 k 与流动性保持一种线性的关系，而不是像 $y = x ^ 2$ 在后面随着 x 的增加，y 的数值会急剧增加

$L_0$：添加之前的 Liquidity，设为 T

$L_1$：添加之后的 Liquidity，设为 T+S，其中 S 为添加的流动性

$$
\frac{L_0}{L_1} = \frac{T}{T+S}
$$

由上面可得，

$$
\begin{aligned}
S &= \frac{(L_1-L_0)*T}{L_0} \\
  &= \frac{\sqrt{(x + \Delta x)*(y + \Delta y)} - \sqrt{x*y}}{\sqrt{x*y}} * T \\
  &= \frac{\sqrt{(x + \Delta x)*(y + \frac{\Delta x * y}{x})} - \sqrt{x*y}}{\sqrt{x*y}} * \frac{\sqrt{x}}{\sqrt{x}} * T \\
  &= \frac{\sqrt{x^2 * y + \Delta x *x*y + \Delta x *x*y + \Delta x^2 *y} - x * \sqrt{y}}{x * \sqrt{y}} (消掉\sqrt{y})\\
  &= \frac{\sqrt{x^2 + 2*\Delta x*x + \Delta x^2} - x}{x} \\
  &= \frac{(x + \Delta x) - x}{x} \\
  &= \frac{\Delta x}{x} * T = \frac{\Delta y}{y} * T(同理)
\end{aligned}
$$

1. 移除流动性

S ：share的数量  T：移除流动性之前 Liquidity 的总量  L：Liquidity 的数量

$$
\begin{aligned}
&\frac{\sqrt{\Delta x * \Delta y}}{\sqrt{x*y}}=\frac{S}{T} \\
&\sqrt{\Delta x * \Delta y} = \sqrt{x*y} * \frac{S}{T} \\
&\Delta x * \sqrt{\frac{y}{x}} = \sqrt{x*y} * \frac{S}{T}(消掉\sqrt{y}，并把\sqrt{x}化简) \\
&\Delta x = x * \frac{S}{T} \\
&\Delta y = y * \frac{S}{T}(同理)
\end{aligned}
$$

---

# Uniswap V2

## 手续费的计算机制

1. 手续费全给项目方

**因为项目方在池子中并没有 share，所以需要进行增发即 $S_m$ 部分**

$S_1$：LP 提供流动性得到的 share 数量   $S_m$：系统按手续费比例增发的 share 数量

$\sqrt{k_1}$：添加流动性对应的 k 值    $\sqrt{k_2}$：添加流动性加上增发的 share 所对应的 k 值

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/381e5453-b993-4092-acca-28f03b189227/Untitled.png)

由上面可得，

$$
\begin{aligned}
\frac{S_m}{S_1+S_m}&=\frac{\sqrt{k_2}-\sqrt{k_1}}{\sqrt{k_2}} \\
S_m*\sqrt{k_2}&=S_1*\sqrt{k_2}+S_m*\sqrt{k_2}-S_1*\sqrt{k_1}-S_m*\sqrt{k_1}(化简) \\
S_m&=\frac{\sqrt{k_2}-\sqrt{k_1}}{\sqrt{k_1}}*S_1
\end{aligned}
$$

1. 手续费全给 Liquidity Provider

**LP 的手续费并不是给 LP 增发新的 share 即 $S_m=0$，而是仍然是 $S_1$ 的数量，但随着手续费的累积，k 值会变大（此时可以理解为，LP 的 share 数量没有变化，在没有手续费收入的时候只共享 $S_1$ 价值，在有手续费收入的时候则共享 $S_1+手续费$ 价值 ）**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/92756e00-9acd-4b72-b185-e87dafb958a4/Untitled.png)

所以，LP 为 0，项目方为 0

例子：最开始有100 DAI：1 ETH (k 值为 $\sqrt{100}$)，经过一系列的交换，此时池子中有 96 DAI：1.5 ETH (k 值为 $\sqrt{144}$)

1. 项目方拿取一定比例（用 $\phi$ 表示，为 $S_m$ 占整个增发部分的比例）的手续费

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d7feb827-17bb-4040-bd94-765c847ce3bb/Untitled.png)

由上面可得，

$$
\frac{S_m}{S_m+S_1}=\frac{\sqrt{k_2}-\sqrt{k_1}}{\sqrt{k_2}}*\phi
$$

$\frac{S_m}{S_m+S_1}$ 表示的是项目方增发的比例，$\frac{\sqrt{k_2}-\sqrt{k_1}}{\sqrt{k_1}}$ 表示的是 LP 增加的比例

由此可以推导出

$$
\begin{aligned}
S_m*\sqrt{k_2} &= S_m*\sqrt{k_2}*\phi + S_1*\sqrt{k_2}*\phi - S_m*\sqrt{k_1}*\phi - S_1*\sqrt{k_1}*\phi \\
S_m &= \frac{(\sqrt{k_2}-\sqrt{k_1})*S_1}{(\frac{1}{\phi}-1)\sqrt{k_2}+\sqrt{k_1}}
\end{aligned}
$$

当 $\phi=1$ 时，则为手续费全给项目方时所得到的公式

## Spot Price

在使用 x 交换成 y 的时候，显示的价格为 $P_0=\frac{y}{x}$，但实际成交价格为 $P_1=\frac{\Delta y}{\Delta x}$，$P_0$ 和 $P_1$ 之间的差值就是所谓的滑点

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4fb3d862-5dfe-4657-9bcb-03c4e64326b5/Untitled.png)

$$
\begin{aligned}
x*y &= (x+\Delta x)*(y-\Delta y) \\
\frac{\Delta y}{\Delta x} &= \frac{y}{x+\Delta x}
\end{aligned}
$$

当 $\Delta x$ 较小时，我们可以理解为是在计算 $\lim_{\Delta x \to 0}\frac{y}{x+\Delta x}$

## Price Oracle

TWAP (Time Weighted Average Price) 时间权重的平均价格

$P_n$ 是在 $t_n$ 时间点的价格

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6f70a6ef-a1c8-4b10-89e6-29ece68ed260/Untitled.png)

由上面可得，

$$
\sum_{i=0}^{n-1}P_i*(T_{i+1}-T_i)
$$

假如我们想要从 $t_k$ 计算，而不是从 0 计算

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/654bca94-e3fb-47c0-8127-6f4d168f7317/Untitled.png)

由上面可得，

$$
\begin{aligned}
P &= \frac{\sum_{i=k}^{n-1}P_i*(T_{i+1}-T_i)}{T_n-T_k} \\
  &= \frac{\sum_{i=0}^{n-1}P_i*(T_{i+1}-T_i) - \sum_{i=0}^{k-1}P_i*(T_{i+1}-T_i)}{T_n-T_k}
\end{aligned}
$$

通过这个公式，我们可以计算比如一个代币在一小时里的平均价格

## 如何计算 Uniswap V2 的无常损失

**无常损失是出现在添加/移除流动性的情况下，而滑点是出现在两个代币交换的情况下**

假设我们有一个初始 LP 为：100DAI：1ETH，此时 K = 100，$P_E=\frac{100}{1}=100$，两个代币总价值为 100 + 100 = $200

- 当 ETH 涨价时，LP 为：120DAI：0.83ETH，此时 K 不变，$P_E=\frac{120}{0.83}=144.58$，两个代币总价值为 120 + 120 = $240，但如果我们并没有添加流动性而是拿住最开始的 100DAI 和 1ETH，两个代币总价值为 100 + 1 * 144.58 = $244.58，那么 244.58 与 240 的差值就是无常损失的值
- 当 ETH 降价时，LP 为：80DAI：1.25ETH，此时 K 不变，$P_E=\frac{80}{1.25}=64$，两个代币总价值为 80 + 80 =$160，但如果我们并没有添加流动性而是拿住最开始的 100DAI 和 1ETH，两个代币总价值为 100 + 1 * 64 = $164，那么 164 与 160 的差值就是无常损失的值

即在添加流动性所产生的无常损失会导致 ETH 涨价时相比拿住赚得更少，ETH 降价时相比拿住亏得更多

通过上面的例子我们可以抽象出更通用的模型，我们可以列出下面这三个公式，$P_i$ 表示在 i 时刻某个代币的价格，d 表示价格变化的因素 **(当 $0 \lt d \lt 1$ 时表示降价，$d=1$ 时表示价格不变，$d \gt 1$ 时表示涨价)**

$$
\begin{align}
P_1 &= P_0*d \\
x*y &= k \\
P &= \frac{y}{x}
\end{align}
$$

由 (2) (3) 可以计算得到 x 和 y 用价格P 和流动性k 的表达式

$$
\begin{align}
x=\frac{k}{y} \Rightarrow \frac{k}{y}=\frac{y}{P} \Rightarrow y^2 &= k*P \Rightarrow y = \sqrt{k}*\sqrt{P} \\
x &= \frac{y}{P}=\frac{\sqrt{k}*\sqrt{P}}{P}=\frac{\sqrt{k}}{\sqrt{P}}
\end{align}
$$

假设一开始为 $t_0$、$x_0$、$y_0$、$P_0=\frac{y_0}{x_0}$

添加流动性（无手续费）之后为 $t_1$、$x_1$、$y_1$、$P_1=\frac{y_1}{x_1}$

拿住为 $t_{hodl}$、$x_0$、$y_0$、$P_1=\frac{y_1}{x_1}$

将无常损失与价格变化之间的关系函数设为 $f(d)$，V 表示为代币的 value，则

$$
f(d)=\frac{做LP的损失}{拿住之后的价值}=\frac{V_1-V_{hodl}}{V_{hodl}}=\frac{V_1}{V_{hodl}}-1
$$

由 (4) (5) 可以计算出 $V_1$，$V_{hodl}$

$$
\begin{aligned}
V_1=y_1+x_1*P_1 = \sqrt{k_1}*\sqrt{P_1}+\frac{\sqrt{k_1}}{\sqrt{P_1}}*P_1 &= 2*\sqrt{k_1}*\sqrt{P_1} \\
V_{hodl} = y_0+x_0*P_1=\sqrt{k_0}*\sqrt{P_0}+\frac{\sqrt{k_0}}{\sqrt{P_0}}*P_0*d &= (1+d)*\sqrt{k_0}*\sqrt{P_0}
\end{aligned}
$$

所以

$$
\begin{aligned}
f(d) &= \frac{2*\sqrt{k_1}*{\sqrt{P_1}}}{(1+d)*\sqrt{k_0}*{\sqrt{P_0}}}-1 \\
&= \frac{2*\sqrt{k_1}*{\sqrt{P_0*d}}}{(1+d)*\sqrt{k_0}*{\sqrt{P_0}}}-1(因为无手续费，所以k_1和k_0相同) \\
&=\frac{2*\sqrt{d}}{1+d}-1
\end{aligned}
$$

Uniswap 官方文档中给出的[无常损失与价格变化的关系曲线](https://docs.uniswap.org/contracts/v2/concepts/advanced-topics/understanding-returns)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/108e667d-c16a-4853-808a-8c5211f5156b/Untitled.png)

## Flash Swap

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/58c08e10-b85f-421f-912e-7bda2385851d/Untitled.png)

传统的借贷需要用户先超额抵押才能借出代币

**闪电交换是指用户无需质押一分钱即可借出一定数量的代币，例如当你发现有一个套利机会但没有足够的资金去进行超额抵押借贷时，我们可以通过闪电借贷借出 100 个 DAI，然后将这些 DAI 进行一系列的投资等去获取收益如变为 110 个 DAI，此时我们在把借出的 100 个 DAI 及其利息归还，剩下的收益就是自己的也就是 7 个 DAI**

## 使用 Flash Swap 加杠杆

用户持有 3 个 ETH，每个 ETH 的价格为 200 DAI，抵押率为 150%。用户想要 2 倍杠杆

传统借贷

1. 添加 3 ETH 到 Maker Vault
2. 借出 400 DAI 出来
3. 在 Uniswap 把 DAI 换成 ETH
4. 重复 1-3 步

闪电借贷

1. 跟 Uniswap 借 3 个 ETH
2. 把用户的 3 个 ETH 和借的 3 个 ETH 抵押到 Maker Vault
3. 借出 800 个 DAI 出来
4. 还给 Uniswap 600 DAI

## Uniswap V2 代码结构

![Untitled](images/image.png)

Uniswap V2 的核心合约就只有三个，分别是 Router、Factory 和 Pair 合约

图中的三个操作分别为 ① add liquidity ② swap ③ remove liquidity

**用户通过前端页面即页面右边的长方块进行上述的三种操作时，会通过 Router 合约来判断执行的操作并调用相关合约的函数，在路由时合约会判断当前用户执行操作所对应的 Pair 是否已创建，若没有则会先创建一个 Factory 合约来创建相关的 Pair 合约，若有则直接调用对应 Pair 合约的函数**

## Uniswap V2 总结

一个核心：CPAMM

三个操作：添加流动性 / 加密资产交易 / 移除流动性

几个概念：手续费 / Price Oracle / TWAP / Falsh Swap / 无常损失 / 滑点等

Source:https://blog.csdn.net/weixin_51306597/article/details/132067959
## Solidity 中，`memory`、`storage`、`calldata` 和 `indexed`详解

在 Solidity 中，`memory`、`storage`、`calldata` 和 `indexed` 是关键概念，分别涉及数据存储位置和事件日志优化。以下是详细解释：

---

### 1. **`memory`**
- **作用**：声明**临时变量**，仅在函数执行期间存在（类似 RAM）。
- **特点**：
  - 数据在函数调用结束后被清除。
  - 用于处理函数内的**引用类型**（如数组、结构体、字符串）。
  - 可读写。
- **Gas 成本**：读写操作相对便宜（但大数组可能昂贵）。
- **示例**：
  ```solidity
  function process(uint[] memory input) public pure returns (uint) {
      uint[] memory temp = new uint[](input.length); // 临时数组
      temp[0] = input[0]; // 可修改
      return temp[0];
  }
  ```

---

### 2. **`storage`**
- **作用**：指向**合约状态变量**的永久存储（类似硬盘）。
- **特点**：
  - 数据存储在区块链上，永久存在。
  - 用于修改合约的状态变量。
  - 可读写。
- **Gas 成本**：读写操作昂贵（尤其是写入）。
- **示例**：
  ```solidity
  contract Example {
      uint[] public data; // 状态变量（默认在 storage）

      function updateStorage() public {
          uint[] storage ref = data; // 指向 storage 的引用
          ref.push(10); // 直接修改状态变量
      }
  }
  ```

---

### 3. **`calldata`**
- **作用**：**只读**的临时数据位置，用于存储**函数参数**。
- **特点**：
  - 仅适用于**外部函数**（`external`）的参数。
  - 数据来自交易调用（`msg.data`），不可修改。
  - 最省 Gas 的选择（避免复制数据）。
- **示例**：
  ```solidity
  function readOnly(uint[] calldata arr) external pure returns (uint) {
      return arr[0]; // 只能读取，不能修改
  }
  ```

---

### 4. **`indexed`**
- **作用**：标记**事件参数**，使其可被索引（用于日志过滤）。
- **特点**：
  - 一个事件最多 3 个 `indexed` 参数。
  - 索引参数存入日志的 `topics`（而非 `data`），便于链下检索。
  - 适合过滤地址、标识符等简单类型（复杂类型会哈希）。
- **示例**：
  ```solidity
  event Transfer(
      address indexed from,    // 可索引（前端可过滤）
      address indexed to,      // 可索引
      uint value               // 非索引（存储在 data 中）
  );

  // 前端过滤（如 ethers.js）：
  contract.on("Transfer", (from, to, value) => { ... });
  contract.queryFilter("Transfer", { from: someAddress }); // 按 from 过滤
  ```

---

### 关键对比
| 特性          | `memory`         | `storage`       | `calldata`      | `indexed`（事件专用） |
|---------------|------------------|-----------------|-----------------|----------------------|
| **生命周期**  | 函数执行期间     | 永久（链上存储）| 函数执行期间    | 事件日志存储         |
| **读写权限**  | 读写             | 读写            | **只读**        | 只读（日志）         |
| **适用场景**  | 函数内部临时变量 | 状态变量        | 外部函数参数    | 事件参数             |
| **Gas 成本**  | 中等             | 高（尤其写入）  | **最低**        | 增加日志 Gas 成本    |
| **修改影响**  | 不影响状态       | 修改合约状态    | 无影响          | 无直接影响           |

---

### 最佳实践
1. **优先用 `calldata`**：  
   外部函数的参数（如数组）使用 `calldata` 节省 Gas。
2. **避免 `storage` 复制**：  
   直接操作状态变量时，用 `storage` 引用减少复制开销。
3. **`indexed` 用于高频过滤字段**：  
   如地址、交易 ID 等需快速检索的参数。

# 2025-08-12

参加技术和运营组会
整理前端常用命令
## Cursor

### 终端

在/Users/ruoyan/Library/Application Support/Cursor/User/keybindings.json中修改快捷键
#### 创建一个新的cusor内置终端并聚焦
```json
{
  "key": "ctrl+cmd+n",
  "command": "workbench.action.terminal.new"
}
```

#### 聚焦到cursor内置终端
```json
{
  "key": "alt+w",
  "command": "workbench.action.terminal.focus"
}
```

#### 在cursor内置的终端之间切换
```json
{
  "key": "alt+q",
  "command": "workbench.action.terminal.focusNext"
}
```

#### 关闭当前cursor内置的终端
```json
{
  "key": "alt+r",
  "command": "workbench.action.terminal.kill"
}
```

### AI CHAT

#### 打开ai chat页面，同时引用选定当前内容，如果已经打开则隐藏ai chat页面
```json
{
  "key": "cmd+l",
  "command": "aichat.newchataction"
}
```

### PAGE

#### 在打开的页面（标签）中切换
```json
{
  "key": "ctrl+tab",
  "command": "workbench.action.quickOpenNavigateNextInEditorPicker",
  "when": "inEditorsPicker && inQuickOpen"
}
```

#### 聚焦到当前页面（标签）
```json
{
  "key": "alt+e",
  "command": "workbench.action.focusActiveEditorGroup",
  "when": "terminalFocus || panelFocus || sideBarFocus"
}
```

#### 关闭当前页面（标签）
```json
{
  "key": "alt+x",
  "command": "workbench.action.closeActiveEditor",
  "when": "editorIsOpen"
}
```

#### 打开上一个页面（标签）
```json
{
  "key": "alt+cmd+left",
  "command": "workbench.action.nextEditor"
}
```

#### 打开下一个页面（标签）
```json
{
  "key": "alt+cmd+right",
  "command": "workbench.action.nextEditor"
}
```

#### 将焦点置于主侧栏
```json
{
  "key": "cmd+0",
  "command": "workbench.action.focusSideBar"
}
```

#### 查看: 聚焦到面板中
```json
{
  "key": "alt+t",
  "command": "workbench.action.focusPanel"
}
```


## foundry


### forge

**典型工作流**：
```bash
forge init my_project     # 初始化项目
forge build               # 编译合约
forge test                # 运行测试（支持模糊测试）
forge test --mt ${函数名称} -vvvvv  # 测试指定测试合约中过的函数
forge script Deploy       # 部署脚本执行
forge fmt                 # 代码格式化
forge install OpenZeppelin/openzeppelin-contracts
forge selectors find "balanceOf(address)"  # 0x70a08231
```

### cast

**常用场景**：
```bash
cast send <合约> "函数" 参数    # 发送交易
cast call <合约> "视图函数"      # 查询状态
cast block latest              # 获取最新区块
cast --to-base 0x1 hex         # 数据格式转换
cast sig "transfer(address)"   # 计算函数选择器
```

### anvil

```bash
anvil    # 本地虚拟网络
```

### make

生成如下makefile
```bash
include .env

deploy_ruoyancoin:
    @echo "deploying contract..."
    forge create src/ruoyancoin.sol:ruoyancoin --private-key ${OWNER_PRIVAYE_KEY} --broadcast --constructor-args ${OWNER_ADDRESS}

mint:
	@echo "Minting tokens..."
	cast send ${CONTRCT_ADDRESS} "mint(uint256)" ${MINT_AMOUNT} --private-key ${OWNER_PRIVATE_KEY}

```


## react



## next.js 15

## wagmi (建议用这个构建web3前端,底层是ether.js)

## rainbowkit

# 2025-08-11

- [ ] 回顾肖臻老师的比特币原理讲解
- [ ] Deploy myFirstNft
- [ ] 编写针对运营/BD方向的简历
- [ ] 参加以太坊中文周会，了解最新行业动态
https://cmu-ruoyan-lgl.github.io/Web3_Internship_Program_Notes/StudyNotes/day10

# 2025-08-10

部署在线简历
- github page
- 针对技术和运营方向
- 中英文版本i18n
- 申请新的邮箱

# 2025-08-09

# Day 8 - 2025年08月09日 

---

## 今日学习目标

- [ ] 深度研究web3前端开发主流框架
- [ ] 学习前端知识
- [ ] 整理相关框架笔记

## 明日学习目标 
- [ ] 黑客松组队设计
- [ ] 设计NFT交易市场项目： ERC721，IPFS，contract， wagmi， viem@2.x， react， tailwind css， gasp 实现加入到市场的nft能够滚动显示， 要求实现NFT定价代理出售， 撤回代理， 购买市场的NFT等功能

## 学习内容

[Build an Ethereum DApp with Next.js, Wagmi, RainbowKit, and Pinata to Mint NFTs on IPFS](https://www.youtube.com/watch?v=2BWdIrfJ3FI&t=60s)


## 学习笔记


---

### 🧩 **一、开源项目与模板（可快速复用）**  
1. **The Stripes NFT 铸造应用**  
   - **特点**：基于 React.js 的高度可配置铸造平台，支持自定义主题色、背景图和多链部署（如 Polygon）。  
   - **技术栈**：React.js + 智能合约集成，通过修改 `config.json` 即可对接自定义合约。  
   - **适用场景**：个人艺术家发行 NFT 或游戏道具确权平台，适合展示合约交互能力。  

2. **The-Weirdos-NFT-Website-Starter-Code**  
   - **特点**：响应式 NFT 集合展示页，含动态效果（打字机动画、彩带特效）和移动端适配。  
   - **技术栈**：React + styled-components + GSAP 动画库，Create React App 脚手架快速启动。  
   - **亮点**：社区活跃，被提名 GitHub 顶级 NFT 开源项目，提供详细视频教程。  

3. **Open Source NFT Marketplace（社区驱动型）**  
   - **特点**：开源 NFT 市场模板，支持 AR 预览 3D 作品，专注跨平台兼容性。  
   - **技术栈**：ReactJS + 以太坊钱包集成，未来计划扩展智能合约后端。  
   - **创新点**：增强现实技术提升用户体验，适合沉浸式艺术展示场景。  

---

### 💼 **二、商业模板（设计成熟，可直接购买）**  
1. **Xhibiter**  
   - **版本**：提供 **HTML 模板**（Tailwind CSS + Webpack）和 **React NextJS 模板**（v13.2）。  
   - **优势**：  
     - 极速加载（500ms 内），Google 评分 97，支持暗黑模式。  
     - 含 13 个主页变体、实时拍卖倒计时、Metamask 集成。  
   - **适用**：高并发交易市场，适合求职 DeFi/NFT 公司时展示性能优化能力。  

2. **Netstorm**  
   - **特点**：多页式 React 模板（17+ 内页），专为数字藏品竞拍设计，含现场拍卖过滤器和作者面板。  
   - **技术栈**：ReactJS + Bootstrap，支持 REST API 调用。  
   - **亮点**：CSS3 动画流畅，内置 1500+ 图标库，适合复杂业务场景前端架构参考。  

3. **Niopy**  
   - **特点**：Vue.js 构建的 15 合 1 模板，含用户面板和明暗模式切换，侧重创作者仪表盘设计。  
   - **技术栈**：Vue JS + Bootstrap 5 + SASS。  
   - **适用**：艺术家个人品牌或收藏品管理平台，突出用户中心功能。  

4. **NFTPO**  
   - **特点**：轻量级登陆页模板，专为 NFT 项目预热设计，含半透明导航栏与渐变按钮。  
   - **技术栈**：NextJS + TailwindCSS，优化 SEO 与图片加载速度。  
   - **优势**：适合快速搭建 MVP，强调第一视觉冲击力。  

---

### ✨ **三、设计亮点与趋势参考**  
- **视觉动效**：  
  - **GSAP 动画**（如 The-Weirdos 的滚动特效）提升用户参与感。  
  - **微交互设计**：NFTPO 的渐变按钮、Netstorm 的 CSS3 悬停动画。  
- **AR 与 3D 集成**：开源 NFT 市场的 AR 预览功能，适合结合 three.js 实现虚拟画廊。  
- **暗黑模式**：Xhibiter 和 Niopy 的双模式切换，适配加密用户偏好。  
- **模块化布局**：Xhibiter 的组件库（图表、下拉菜单）可复用性高。  

---

### ⚙️ **四、技术栈参考（2025 主流选择）**  
| **需求**         | **推荐技术**                     | **案例参考**              |  
|------------------|----------------------------------|---------------------------|  
| 多链兼容         | Wagmi + Viem                     | Xhibiter 多链资产展示     |  
| 动画与交互       | GSAP + Framer Motion             | The-Weirdos 动态效果      |  
| 样式管理         | Tailwind CSS + styled-components | NFTPO 渐变设计            |  
| 性能优化         | NextJS 服务端渲染                | Xhibiter React 版         |  
| 移动端优先       | 响应式框架 + 触控优化            | Niopy 跨设备适配          |  

---

### 💡 **实用建议**  
1. **优先学习开源项目**：克隆 [The-Weirdos 代码库](https://github.com/codebucks27/The-Weirdos-NFT-Website-Starter-Code) 修改组件，理解状态管理与动画实现。  
2. **商用模板二次开发**：购买 Xhibiter React 版（约 $60），在其模块化基础上扩展拍卖逻辑或 DAO 治理功能。  
3. **融合创新点**：  
   - 在铸造平台中增加 **AR 预览**（参考开源 NFT 市场）。  
   - 为交易市场添加 **Gas 优化提示**（类似 Netstorm 的实时过滤器）。  

根据目标公司调整设计重点：应聘艺术类平台侧重 3D/AR 能力；金融类 NFT 项目突出交易流程与数据可视化。


## 问题与思考


## 总结


---
https://cmu-ruoyan-lgl.github.io/Web3_Internship_Program_Notes/StudyNotes/day8

# 2025-08-08

- [ ] 复习react
- [ ] 研究以太坊官网文档，后续白皮书 + EPF wiki + EVM图解
- [ ] 研究设计dex实现
https://cmu-ruoyan-lgl.github.io/Web3_Internship_Program_Notes/StudyNotes/day7

# 2025-08-07

deploy myFirstNft
完成前端项目
复习react
https://cmu-ruoyan-lgl.github.io/Web3_Internship_Program_Notes/StudyNotes/day6

# 2025-08-06

- [ ] 参加会议
- [ ] 研究deploy myFirstNft
- [ ] 完成前端项目
https://cmu-ruoyan-lgl.github.io/Web3_Internship_Program_Notes/StudyNotes/day5

# 2025-08-05

参加web3安全分享会
写完solana monitor api代码
开始deploy myfirstnft
https://cmu-ruoyan-lgl.github.io/Web3_Internship_Program_Notes/StudyNotes/day4

# 2025-08-04

体验一次eth中文周会
学习成都信息工程大学梁培利老师的uniswapv1到v3的原理
设计agent去做NFT市场交换前端项目的工作流
学习笔记收录在https://cmu-ruoyan-lgl.github.io/Web3_Internship_Program_Notes/StudyNotes/day3


# 2025.07.31


<!-- Content_END -->
