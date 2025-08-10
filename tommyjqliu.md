---
timezone: UTC+8
---

# Tommy

**GitHub ID:** tommyjqliu

**Telegram:** @tommyjqliu

## Self-introduction

你好，我本科毕业于华南理工大学软件工程，现在于澳洲修读软件工程硕士。之前主要接触web2现在希望转型为web3开发。

## Notes

<!-- Content_START -->
# 2025-08-10

# Hardhat 常用命令笔记

Hardhat 是一个以太坊智能合约开发框架，提供了便捷的工具来编译、测试、部署和调试智能合约。以下是常用的 Hardhat 命令及其说明，供快速参考。

## 1. 初始化项目
- **命令**: `npx hardhat`
- **说明**: 初始化一个新的 Hardhat 项目，会生成项目配置文件 `hardhat.config.js` 和基本目录结构。
- **示例**:
  ```bash
  npx hardhat
  ```

## 2. 编译智能合约
- **命令**: `npx hardhat compile`
- **说明**: 编译 `contracts/` 目录下的智能合约，生成编译后的 artifacts 和 cache 文件。
- **示例**:
  ```bash
  npx hardhat compile
  ```

## 3. 运行测试
- **命令**: `npx hardhat test`
- **说明**: 运行 `test/` 目录下的测试用例，支持 Mocha 和 Chai 测试框架。
- **选项**:
  - `--grep <pattern>`: 运行特定测试用例（根据描述过滤）。
  - `--no-compile`: 跳过编译直接运行测试。
- **示例**:
  ```bash
  npx hardhat test
  npx hardhat test --grep "should transfer tokens"
  ```

## 4. 启动本地开发网络
- **命令**: `npx hardhat node`
- **说明**: 启动一个本地以太坊网络（Hardhat Network），提供 20 个测试账户，监听在 `localhost:8545`。
- **示例**:
  ```bash
  npx hardhat node
  ```

## 5. 部署智能合约
- **命令**: `npx hardhat run <script>`
- **说明**: 运行 `scripts/` 目录下的部署脚本，通常用于将合约部署到网络（本地或测试网/主网）。
- **选项**:
  - `--network <network-name>`: 指定目标网络（如 `ropsten`, `mainnet`, 或 `localhost`）。
- **示例**:
  ```bash
  npx hardhat run scripts/deploy.js --network localhost
  ```

## 6. 运行控制台
- **命令**: `npx hardhat console`
- **说明**: 启动 Hardhat 的交互式 JavaScript 控制台，可用于调试合约或与网络交互。
- **选项**:
  - `--network <network-name>`: 指定网络。
- **示例**:
  ```bash
  npx hardhat console --network localhost
  ```

## 7. 清理项目
- **命令**: `npx hardhat clean`
- **说明**: 删除编译生成的 `artifacts/` 和 `cache/` 目录，清理项目。
- **示例**:
  ```bash
  npx hardhat clean
  ```

## 8. 检查合约代码
- **命令**: `npx hardhat check`
- **说明**: 检查合约代码中的潜在问题（需要安装相关插件，如 `@nomiclabs/hardhat-etherscan`）。
- **示例**:
  ```bash
  npx hardhat check
  ```

## 9. 验证合约
- **命令**: `npx hardhat verify <contract-address> [constructor-args]`
- **说明**: 在 Etherscan 上验证已部署的合约源代码（需配置 API 密钥）。
- **选项**:
  - `--network <network-name>`: 指定网络。
- **示例**:
  ```bash
  npx hardhat verify 0x5FbDB2315678afecb367f032d93F642f64180aa3 --network ropsten
  ```

## 10. 查看任务列表
- **命令**: `npx hardhat help`
- **说明**: 显示所有可用的 Hardhat 任务及其描述。
- **示例**:
  ```bash
  npx hardhat help
  ```

## 11. 运行自定义任务
- **命令**: `npx hardhat <task-name>`
- **说明**: 运行在 `hardhat.config.js` 中定义的自定义任务。
- **示例**:
  ```bash
  npx hardhat my-custom-task
  ```

## 12. 配置网络
- **说明**: 在 `hardhat.config.js` 中配置网络（如测试网、主网或本地网络）。
- **示例配置**:
  ```javascript
  module.exports = {
    networks: {
      hardhat: {}, // 默认本地网络
      ropsten: {
        url: "https://ropsten.infura.io/v3/YOUR_INFURA_KEY",
        accounts: ["YOUR_PRIVATE_KEY"]
      }
    }
  };
  ```

## 13. Gas 费用估算
- **命令**: `npx hardhat gas-reporter`
- **说明**: 生成 Gas 使用报告，需安装 `hardhat-gas-reporter` 插件。
- **示例**:
  ```bash
  npx hardhat test --gas-report
  ```

## 14. 代码覆盖率
- **命令**: `npx hardhat coverage`
- **说明**: 生成测试覆盖率报告，需安装 `solidity-coverage` 插件。
- **示例**:
  ```bash
  npx hardhat coverage
  ```

## 15. 运行脚本并导出结果
- **命令**: `npx hardhat export`
- **说明**: 导出编译后的合约 ABI 和地址，需配置相关插件。
- **示例**:
  ```bash
  npx hardhat export --export-all
  ```

## 备注
- **插件支持**: Hardhat 支持丰富的插件生态（如 `hardhat-ethers`, `hardhat-waffle`），可根据需要安装以扩展功能。
- **配置文件**: 所有命令的行为可以通过 `hardhat.config.js` 进行定制。
- **调试**: 使用 `console.log` 在智能合约中调试（需导入 `hardhat/console.sol`）。

# 2025-08-08

# Web3蜜罐钱包骗局技术分析

## 概述

一种基于多重签名钱包的诈骗模式，通过虚假的"免费USDT"诱饵来获取用户转入的ETH手续费。本质上是利用区块链技术特性设计的自动化资金收割系统。

## 技术架构

### 多重签名钱包结构

```solidity
contract HoneypotMultiSig {
    mapping(address => bool) public owners;
    uint256 public required;
    
    modifier validRequirement(uint ownerCount, uint _required) {
        require(_required <= ownerCount && _required != 0);
        _;
    }
    
    function submitTransaction(address destination, uint value, bytes data)
        public
        returns (uint transactionId)
    {
        // 只有owners能够提交交易
        require(owners[msg.sender]);
        // 实际控制权在骗子手中
    }
}
```

### 监控系统

骗子部署的自动化监控脚本结构：

```javascript
// 监听钱包ETH余额变化
web3.eth.subscribe('pendingTransactions', (txHash) => {
    web3.eth.getTransaction(txHash).then(tx => {
        if (tx.to === HONEYPOT_ADDRESS && tx.value > 0) {
            // 立即执行转出操作
            executeTransfer(tx.value);
        }
    });
});
```

## 执行流程

1. **诱饵投放**
   - 在社交媒体发布助记词
   - 钱包内放置一定数量USDT作为诱饵
   - 确保ETH余额不足以支付转账gas费

2. **受害者响应**
   - 导入助记词获得钱包访问权
   - 发现USDT但无法转出（ETH不足）
   - 向钱包转入ETH以支付手续费

3. **资金收割**
   - 监控脚本检测到ETH流入
   - 自动化系统立即执行转出
   - 受害者损失转入的ETH

## 技术特征分析

### 多签配置特点

```json
{
  "owners": [
    "0x攻击者地址1",
    "0x攻击者地址2", 
    "0x受害者地址"
  ],
  "required": 2,
  "note": "攻击者控制2/3签名权"
}
```

### Gas优化策略

攻击者通常设置较高的gas price确保交易优先执行：

```javascript
const transferConfig = {
    gasPrice: web3.utils.toWei('50', 'gwei'), // 高于市场平均值
    gasLimit: 21000,
    priority: 'high'
};
```

## 检测方法

### 钱包类型识别

```javascript
// 检查是否为多签钱包
async function isMultiSigWallet(address) {
    const code = await web3.eth.getCode(address);
    // 非空code表示为合约地址
    return code !== '0x';
}
```

### 交易历史分析

正常钱包特征：
- 交易间隔不规律
- Gas价格波动正常
- 无异常快速转出模式

蜜罐钱包特征：
- ETH流入后几秒内必定流出
- 转出交易gas价格异常高
- 多次重复相同模式

## 风险评估模型

```python
def honeypot_risk_score(wallet_address):
    score = 0
    
    # 检查合约类型
    if is_contract(wallet_address):
        score += 30
    
    # 分析交易模式
    transactions = get_transaction_history(wallet_address)
    rapid_outflows = count_rapid_outflows(transactions)
    score += min(rapid_outflows * 10, 50)
    
    # 检查余额配置
    eth_balance = get_eth_balance(wallet_address)
    token_balance = get_token_balance(wallet_address)
    
    if token_balance > 0 and eth_balance < gas_required:
        score += 20
    
    return min(score, 100)
```

## 防护策略

### 技术验证

1. 合约代码审计
2. 交易历史分析
3. 多签配置检查
4. 自动化行为检测

### 操作建议

- 小额测试原则（<$1）
- 延时观察（24小时以上）
- 第三方验证工具使用
- 社区经验查询

## 总结

这类攻击本质上是利用信息不对称和技术复杂性实现的自动化诈骗。攻击者通过精心设计的多签钱包结构和监控系统，将传统的社会工程学攻击转化为可规模化的技术手段。

理解其技术原理有助于识别类似模式，但最根本的防护仍然是对"免费资产"保持理性怀疑。

# 2025-08-07

# 虚拟货币相关法律风险与合规要点

## 一、常见涉罪罪名
- **集资诈骗罪**（如光锥币、大圣币等ICO案例）
- 非法吸收公众存款罪
- 组织、领导传销活动罪
- 开设赌场罪
- 非法经营罪

## 二、核心红线：虚拟货币发行与募资
- **ICO（Initial Coin Offering）**  
  ▶︎ 国内明确禁止虚拟货币发行融资  
  ▶︎ 海外持牌成本高且合规复杂  

## 三、关键风险识别要点
### （一）资金汇集场景
1. 交易所与虚拟货币基金运作模式  
2. 是否涉及法币兑换（人民币出入金）  
3. 海外合规性审查：  
   - AML（反洗钱）与KYC（用户识别）制度是否完备  
   - 技术层面是否屏蔽中国大陆用户  

### （二）传销与赌博风险
- **项目扩张合规性**：  
  ▶︎ 避免多级返佣、团队计酬等传销特征  
  ▶︎ 非典型风险案例：合约交易、NFT玩法设计  

### （三）洗钱与外汇管制
- 潜在风险角色：  
  ▶︎ 成为洗钱链条环节  
  ▶︎ 充当外汇兑换媒介  
- **难点**：非管理层难以追踪资金路径  

## 四、合规判断经验准则
1. **主体资质**：  
   - 是否在受监管司法辖区注册  
   - 是否通过第三方审计/安全测试  
2. **风控措施**：  
   - 完备的KYC/AML制度  
   - 公开团队背景、资金路径等信息  
3. **业务模式**：  
   - 是否存在高风险模块（如杠杆、赌博机制）  
   - 是否采用层级返利等高风险扩张方式  

## 五、责任层级与法律盲区
### 责任等级（由高到低）：
1. 核心管理层  
2. 宣传/社区负责人  
3. 业务核心环节参与者  
4. 纯技术提供者  

### 知情推定原则：
- 技术提供者需主动尽调，**"明知可疑而不询问"可能被推定知情**  
- 免责前提：已尽合理防范义务

# 2025-08-06

# EOS的失败

1. **过度中心化**：21个超级节点被交易所和资本垄断，贿选、互投成风，违背区块链去中心化原则。
2. **经济模型崩盘**：资源租赁（RAM/CPU）复杂昂贵，高通胀+抛压致币价暴跌95%，生态资金滥用。
3. **性能虚假宣传**：号称百万TPS，实际仅数千且依赖节点协作，拥堵时用户体验极差。
4. **开发环境恶劣**：C++合约门槛高，工具链混乱，开发者流失。
5. **生态泡沫化**：依赖博彩DApp，无可持续应用；社区基金腐败，优质项目枯竭。
6. **团队失信**：Block.one募资41亿美元后消极开发，高管离职，社区分裂。

# Q

去中心化和高性能，应该优先选择谁？
公链的不同治理模型有哪些，对比他们的异同？
应该优先激励哪些生态用户（用户，开发者）？
公链如何管理资金来获取用户信任？

### **共识算法对比总结**

### **PoW（工作量证明）**

- **代表项目**：比特币、莱特币
- **工作原理**：矿工竞争算力解题，最快解出者出块
- **安全性**：极高（51%算力攻击成本高）
- **去中心化**：高（但矿池集中化问题）
- **TPS**：3-7（比特币）
- **能耗**：极高
- **适用场景**：价值存储、高安全性区块链

### **PoS（权益证明）**

- **代表项目**：以太坊2.0、Cardano
- **工作原理**：按持币量和时间随机选择验证者
- **安全性**：高（51%持币攻击成本高）
- **去中心化**：中（富者愈富效应）
- **TPS**：100-1,000+
- **能耗**：极低
- **适用场景**：智能合约平台、DeFi

### **DPoS（委托权益证明）**

- **代表项目**：EOS、TRON
- **工作原理**：持币者投票选超级节点，节点轮流出块
- **安全性**：中（依赖代表诚信）
- **去中心化**：低（节点寡头化）
- **TPS**：1,000-4,000+
- **能耗**：极低
- **适用场景**：高性能DApp、企业链

### **PBFT（实用拜占庭容错）**

- **代表项目**：Hyperledger Fabric
- **工作原理**：节点轮流提案，需2/3以上投票通过
- **安全性**：高（可容错1/3恶意节点）
- **去中心化**：低（需许可链）
- **TPS**：1,000-10,000
- **能耗**：低
- **适用场景**：联盟链、金融机构

### **Tendermint（BFT+PoS）**

- **代表项目**：Cosmos
- **工作原理**：PoS选出验证者，BFT共识快速确认
- **安全性**：高（即时最终性）
- **去中心化**：中（验证者选举制）
- **TPS**：1,000-10,000
- **能耗**：极低
- **适用场景**：跨链枢纽、需快速确认的公链

### **PoH（历史证明）**

- **代表项目**：Solana
- **工作原理**：时间戳序列化交易，减少节点同步时间
- **安全性**：中（依赖时钟同步）
- **去中心化**：中（验证者集中化）
- **TPS**：50,000+
- **能耗**：低
- **适用场景**：高频交易、Web3应用

### **总结特点**

- **最高安全性**：PoW > PoS/Tendermint/PBFT > DPoS > PoH
- **最高去中心化**：PoW > PoS > Tendermint > PoH > DPoS > PBFT
- **最高TPS**：PoH > DPoS > PBFT/Tendermint > PoS > PoW
- **最低能耗**：PoS/DPoS/Tendermint/PoH > PBFT > PoW

# 2025-08-05

# Token Approval

Check token approvals to revoke unwanted one

[Token Approvals | Etherscan](https://etherscan.io/tokenapprovalchecker) 

[Token Approvals | Arbitrum One](https://arbiscan.io/tokenapprovalchecker?search=0xafd35749459860f490325858cd7b3ad3606b07cc)

## Special cases

Permit2 (used by Uniswap) manages a internal approval table, and the approvals on it can not be revoke through normal scan.

# Sign Contract

• Always verify the actual function call and parameters of transactions

# 2025-08-04

# Wallet Security Best Practices
## 1. Protect Your Recovery Phrase
Never leak or share your recovery phrase (seed phrase).

Prevent copying to clipboard to avoid accidental exposure.

Never provide it to anyone, even if they claim to be support.

## 2. Token Approvals & DApps
Never approve tokens for unknown or untrusted DApps.

Revoke unused approvals periodically using tools like Etherscan or Revoke.cash.

## 3. Always Double-Check
Recipient address – Verify before sending.

Token address – Confirm legitimacy (avoid fake tokens).

Website URL – Ensure it’s the official site (check for typos or HTTPS).


# 2025.08.01


<!-- Content_END -->
