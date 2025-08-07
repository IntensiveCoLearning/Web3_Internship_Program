---
timezone: UTC+8
---

# Neal Lee

**GitHub ID:** snaildarter

**Telegram:** @Neal_li

## Self-introduction

学习，使用，重复，反思，升华。

## Notes

<!-- Content_START -->
# 2025-08-07

#### 核心法律风险梳理

1. 代币发行与交易行为的法律风险
2. 赌博、传销、洗钱等刑事风险
3. 场外交易中的洗钱与非法经营风险
4. 民商争议

如果是内部人作恶，那用户就自求多福了。项目方内部的安全风控一定要特别重视人员安全，这永远是最值得花成本、花精力去建设的。人是最大的那只特洛伊木马，但却最容易被忽视。有的人安全意识实在太差，在安全上又不思进取。这种人，谁招谁倒霉。

# 2025-08-06

### 稳定币知识

#### 稳定币与区块链基础

目标： 搞懂”稳定币是什么、为什么存在、运行在什么环境上“

1. 稳定币核心概念
   _稳定币是锚定法币（如 USD）、加密资产或算法的加密货币，核心作用是解决加密货币价格波动问题，成为区块链生态的”支付媒介“”价值储备“。_

分类与代表：

- 法币抵押型：USDC（Circle 发行，1:1 锚定 USD, 有法币储备）、USDT（Tether，储备透明度较低）、TUSD（TrueUSD， 严格审计）。
- 加密资产抵押型：DAI（MakerDAO 发行，由 ETH 等加密资产超额抵押生成）、sUSD（Synthetix, 抵押 SNX 生成）。
- 算法型（非主流，风险高）：早期 UST（Terra，已崩盘）、FRAX（部分算法+部分抵押）。

前端开发中接触最多的是法币抵押型（USDC/USDT）和加密资产抵押型（DAI）。

2. 区块链基础（稳定币的”运行土壤“）
   核心概念：区块链是”去中心账本‘，稳定币的转账、余额记录都在链上。

- 公链（以太坊，BSC，Polygon、Solana 等）：不同链有自己的稳定币（如以太坊上的 USDC 和 BSC 上的 USDC 是不同资产）。
- 账户与地址：用户通过钱包地址（0x 开头的字符串）持有稳定币，前端需要处理地址格式验证（如以太坊地址是 42 位十六进制字符串）。
- 交易与 Gas：稳定币转账是“链上交易”，需要支付 Gas 费（用链上原生代币，如以太坊上用 ETH，BSC 上用 BNB），前端需要处理 Gas 估算、交易状态查询。

3. 前端视角的“稳定币与区块链”的关联
   前端开发无需深入区块链底层（如共识机制），但需要知道：稳定币的所有数据（余额、转账记录）都在链上，前端通过“区块链 API”或“钱包工具”读取/写入这些数据。

#### 稳定币的链上交互逻辑

目标：理解稳定币在链上的“数据格式”“交互规则”

1. 稳定币的标准化：ERC-20 协议

- 90%以上的稳定币（USDC、USDT、DAI 等）遵循以太坊的 ERC-20 标准（其他公链有类似标准，如 BSC 的 BEP-20、Solana 的 SPL）。
- ERC-20 定义了稳定币的核心接口（前端需要调用的函数）：
  - balanceOf(address user): 查询用户地址的稳定币余额（前端常用，如显示你的 USDC 余额）
  - transfer(address to, uint256 amount): 转账（前端实现给他人转 USDC 功能）
  - approve(address spender, uint256 amount): 授权其他合约/地址使用你的稳定币（如在 Uniswap 中用 USDC 换 ETH，需要先授权 Uniswap 合约使用你的 USDC）。
  - 这些接口是“智能合约函数”，前端通过“钱包+PRC 节点”调用（类似调用后端 API，但调用对象是链上合约）。

2. 稳定币的链上数据结构

- 余额：以最小单位存储（如 USDC 的最小单位是“分”， 1USDC = 1e6 最小单位，即 1USDC=1000000wei）。
- 从链上读取的余额是“最小单位数值”，需要转换为“用户可读单位”（如 1000000 -> 1 USDC）。
- 转账记录：链上每笔稳定币转账都会生成“交易哈希（txHash）”,可通过区块链浏览器（如 Etherscan）查询，前端可通过 txHas 跟踪交易状态（成功/失败/pending）。

3. 钱包与稳定币交互

- 钱包（如 MetaMask）是用户与链上资产交互的入口，前端需要通过钱包获取用户地址、发起交易。
- 核心流程（以转账 USDC 为例）
  1. 前端通过 window.ethereum(MeatMask 注入的对象)获取用户地址。
  2. 调用 USDC 合约的 balanceOf 方法，显示用户余额。
  3. 用户输入接收地址和金额后，前端调用 transfer 接口，钱包弹出签名框（用户确认交易）。
  4. 交易上链后，前端通过 txHash 查询结果，更新 UI。

#### 前端开发工具和稳定币功能实现

目标：掌握前端与稳定币交互的工具链，能独立开发稳定币相关功能（查余额、转账、授权等）。

1. 核心工具与库
   钱包交互：检测是否安装钱包，获取 Provider（链上交互的入口）。
   钱包时间监听：用户切换链/地址时，前端需要同步更新数据（accountsChanged、chainChanged 事件）。
   链上数据交互：（viem，ethers.js web3.js）封装了与区块链交互的方法，直接调用 ERC-20 合约接口。
   链 ID 与合约地址：不同链的稳定币合约地址不同，前端需要维护“链 ID-合约地址”映射表。
   开发环境：

   - Hardhat：本地开发和测试环境，模拟链上交互。
   - 区块链浏览器：Etherscan（以太坊）、BscScan（BSC），查询真实合约地址、交易记录、辅助调试。

2. 核心功能实现案例

- 稳定币余额查询工具
  功能：用户连接钱包后，显示其在当前链上的 USDC、USDT、DAI 余额。
  关键点：处理不同稳定币的 decimals（USDC/USDT 是 6，DAI 是 18），统一转换为可读格式。
- 稳定币转账 DApp
  功能：用户输入接收地址和金额，发起稳定币转账，显示交易状态。
  关键点：
  验证接收地址格式（用 isAddress）；
  处理用户拒绝交易（钱包签名框被取消）；
  监听交易上链状态（provider.waitForTransaction(txHash)）。
- 与 DeFi 协议交互（如 Aave 存稳定币）
  功能：用户将 USDC 存入 Aave 获取利息。
  关键点：
  先调用 USDC 的 approve 授权 Aave 合约使用用户的 USDC；
  再调用 Aave 的 deposit 函数存入 USDC（需理解 Aave 合约的接口）。

#### 稳定币的进阶场景与前端优化

目标：应对复杂场景，解决前端开发中的实际问题。

1. 跨链稳定币交互
   同一稳定币可能在多链发行（如 USDC 在以太坊、Polygon、Avalanche 均有版本），前端需支持：
   检测用户当前链，显示对应链的稳定币数据；
   引导用户切换链（如用户在 Polygon 链，想转以太坊 USDC，需提示切换链）；
   跨链转账功能（调用跨链桥合约，如 Polygon Bridge，前端需处理跨链交易的状态跟踪）。
2. 稳定币的 “合规与安全” 前端处理
   合规：部分场景需验证稳定币的 “合规性”（如只支持受监管的 USDC，不支持匿名稳定币），前端可通过合约地址白名单实现。
   安全：
   不存储用户私钥 / 助记词（前端只通过钱包交互，私钥由钱包管理）；
   验证合约地址（防止用户输入假 USDC 合约地址，可通过链上注册表查询官方地址）；
   处理重入攻击（前端无需深入，但需调用经过审计的合约，避免与恶意合约交互）。
3. 前端性能优化
   链上数据查询延迟：用 provider.getBalance 等接口查询余额时，可能因链上区块同步延迟返回旧数据，前端可：
   结合区块链浏览器 API（如 Etherscan API）做二次验证；
   监听区块事件（provider.on("block", ...)），区块更新后自动刷新数据。
   大量稳定币数据展示：如显示用户在 10 条链上的 5 种稳定币余额，可通过批量查询（multicall 合约）减少链上请求次数。

#### 实践与深入

目标：通过真实项目巩固知识，理解稳定币生态的深层逻辑。

1. 实战项目
   开发一个 “稳定币资产管理 DApp”：支持多链稳定币余额查询、跨链转账、DeFi 收益聚合（存到 Aave/Compound）。
   参与开源项目：如在 Uniswap 前端（开源）中添加 “稳定币兑换” 快捷功能，或在 MetaMask 插件中优化稳定币显示逻辑。
2. 深入稳定币生态
   理解稳定币的 “发行与赎回”：如 USDC 的发行流程（用户给 Circle 转 USD，Circle 在链上 mint USDC）、DAI 的生成（抵押 ETH 到 MakerDAO，生成 DAI），前端可开发 “DAI 生成计算器”（根据抵押 ETH 数量计算可生成 DAI 上限）。
   关注稳定币风险：如 UST 崩盘的原因（算法型稳定币的 “死亡螺旋”）、USDC 的储备风险（Circle 是否真有 1:1 法币储备），前端可在 DApp 中添加 “稳定币风险评级” 提示。

总结
前端开发学习稳定币的核心逻辑是：“用前端技术连接用户与链上稳定币数据”。从 “是什么” 到 “怎么交互”，再到 “怎么优化交互”，最终通过实践将稳定币功能融入 DApp。重点关注 ERC-20 接口、钱包交互、链上数据处理，利用 viem 等工具将前端技能与区块链场景结合，就能快速上手。

# 2025-08-05

### 安全

1. 重入攻击
   利用外部合约在 fallback 中重新调用原函数。历史上最著名的 The Dao 事件便因重入漏洞导致合约 6000 万美元 ETH 被盗，最终造成以太坊社区分裂（形成 ETH/ETC 链）。
   防护方法：先更新状态，在转账。

```solidity
function withdraw() public {
    require(balance[msg.sender] > 0);
    (bool sent,) = msg.sender.call{value: balance[msg.sender]}("");
    require(sent);
    balance[msg.sender] = 0;
}


// good
function withdraw() public {
    uint256 amount = balance[msg.sender];
    balance[msg.sender] = 0;
    (bool sent,) = msg.sender.call{value: amount}("");
    require(sent);
}
```

2. 预言机操纵
   依赖外部价格源的不可信更新。
   解决方法：

   - 使用 Chainlink 等权威价格源。
   - 增加时序约束和多源验证。
   - 使用 twap 等加权算法。

3. 整数溢出/下溢
   使用 unchecked {} 时需要确保逻辑安全。
   推荐使用 Solidity 0.8+ 的内建溢出检查或 SafeMath.

4. 权限控制缺失
   所有管理函数引用 onlyOwner 或 AccessControl 修饰符保护。
5. 未初始化代理

- 基于代理模式的合约未正确初始化函数，可能被任意人初始化并接管合约。
- 著名的例子包括 Harvest Finance 其在使用 Uniswap V3 做市策略的 Vault 合约中存在未初始化漏铜，如果被利用攻击者可销毁实现合约。该团队曾为此漏洞支付高额赏金修复。

6. 前置交易/三明治攻击

- 攻击者在交易执行前后分别发送交易，以不利滑点或套利为目的。
- 例如 2025 年 3 月，一名用户在 Uniswap V3 的稳定币兑换中遭遇三明治攻击，约 21.5 完美元的 USDC 兑换几乎被抢跑，损失 98%的资金。

# 2025-08-04

## Dapp 架构

### 前端

界面 React/Vue
数据管理 Zustand/Redux
工具库 Typescript、Tailwind
了解钱包的库：Viem、wagmi、Rainbow

### 智能合约

solidity： hardhat

### 数据检索器

智能合约通常以 Event 形式释放日志事件，比如释放代表 NFT 转移的 Transfer 事件，数据检索器会检索这些数据并将其写入到 PostgreSql 等传统数据库中。
Dapp 在前端进行数据展示时需要检索器内的数据。一个简单的示例是某个 NFT 项目需要展示用户持有的所有 NFT，但是 NFT 合约并不会提供通过输入地址参数返回该地址下的所有 NFT 的函数，此时我们可以运行检索器将 Transfer 事件读取后写入传统数据库内，前端可以在传统数据库内检索用户持有的 NFT 数据。

### 区块链和去中心化存储

区块链用于存储智能合约的状态数据以及交易记录。去中心化存储如 IPFS 或 Arweave，用于存储大规模的非结构化数据（如图片、文档等），确保数据不易丢失和篡改。
通过使用去中心化存储，Dapp 确保所有数据在多个节点上备份，保证数据的持久性和去中心化的特性。

### 常见优化技巧

#### 减少存储操作

- 读取存储第一次需 2100 gas（后续 100 gas），而内存读取仅 3 gas。推荐多次访问同一存储数据时，将其缓存到内存以减少 SLOAD 次数
- 每次写入 storage 的成本高达 20,000 gas；优先使用 memory。
- 示例：

```solidity
// ❌ 非优化写法
mapping(address => uint256) public balances;
function deposit() public payable {
    balances[msg.sender] += msg.value;
}

// ✅ 优化写法（一次读，一次写）
function deposit() public payable {
    uint256 current = balances[msg.sender];
    balances[msg.sender] = current + msg.value;
}
```

#### 使用位压缩（Bit Packing）

- 将多个变量压缩到一个 uint256 中以节省存储空间。
- 示例：

```solidity
struct Packed {
    uint128 a;
    uint128 b;
}
```

#### 循环优化

- 减少不必要的运算，如 array.length 缓存到变量中。
- 示例

```solidity
// ❌ 非优化
for (uint256 i = 0; i < arr.length; i++) {
    ...
}
// ✅ 优化
uint256 len = arr.length;
for (uint i = 0; i < len; ++i) {
    ...
}
```

#### 函数可见性选择

external 比 public 更节省 gas，适用于仅背外部调用的函数。


# 2025.07.29


<!-- Content_END -->
