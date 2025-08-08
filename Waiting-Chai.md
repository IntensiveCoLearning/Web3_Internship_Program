---
timezone: UTC+8
---

# Waiting-Chai

**GitHub ID:** Waiting-Chai

**Telegram:** @chai

## Self-introduction

想要当remote数字游民， web2后端转型， 也会一点前端； 主体技术栈 golang， java， ts等；

## Notes

<!-- Content_START -->
# 2025-08-08

学习打卡， 还是在做自己的dapp

# 2025-08-07

关于ChainOath
持续关注： https://github.com/Waiting-Chai/ChainOath

学习了 react 父子组件传值；

法律方面：

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

# 2025-08-06

学习了solidity，设计了一款dapp， 并进入了开发阶段， 目前前端页面开发完成， 合约还在开发中

链接在   https://github.com/Waiting-Chai/ChainOath

# ⛓️ 四、智能合约设计（核心逻辑）

## 🧬 ChainOath 智能合约规则文档

## 1. 角色定义

| 角色 | 描述 | 是否需质押 | 是否可获奖 | 是否影响誓约状态 |
|------|------|------------|------------|------------------|
| **发起人 Creator** | 创建誓约，设定奖励池、配置规则、分配角色 | ✅（质押全部奖励） | ❌ | ✅（设定规则） |
| **守约人 Committer** | 接受任务，履行誓约，被监督签名评定 | ✅（履约押金，可配置） | ✅ | ✅（是否守约） |
| **监督者 Supervisor** | 定期进行 check 并签名，评定守约人行为 | ✅（质押金，可配置） | ✅（按 check 次数） | ✅（监督决定） |
| **查看者 Viewer** | 仅查看誓约详情与状态 | ❌ | ❌ | ❌ |

---

## 2. 创建誓约所需字段

```solidity
struct Oath {
  string title;                     // 誓约标题
  string description;               // 誓约描述
  address[] committers;            // 守约人列表
  address[] supervisors;           // 监督者列表
  address rewardToken;             // 奖励代币地址
  uint256 totalReward;             // Creator 总质押奖励金额
  uint256 committerStake;          // 每位守约人需质押金额
  uint256 supervisorStake;         // 每位监督者需质押金额
  uint256 supervisorRewardRatio;   // 监督者奖励比例（如 10 表示 10%）
  uint256 committerRewardRatio;    // 守约人奖励比例（如 90 表示 90%）
  uint256 checkInterval;           // check 间隔（单位：秒）
  uint256 checkWindow;             // check 后签名时间窗口（单位：秒）
  uint256 checkThresholdPercent;   // 判定守约成功的监督者签名比例
  uint256 maxSupervisorMisses;     // 监督者最大允许失职次数
  uint256 maxCommitterFailures;    // 守约人最大允许失约次数
  uint256 startTime;               // 誓约开始时间
  uint256 endTime;                 // 誓约结束时间
}
```

## 3. 誓约流程说明

**阶段 1：创建**

Creator 创建誓约，配置上述所有参数并质押 totalReward。

守约人和监督者分别调用质押函数，缴纳履约/监督押金。

**阶段 2：监督与履约**

每隔 checkInterval 触发一个监督周期：

监督者需在 checkWindow 内签名表达“守约”或“失约”判断。

若签名未提交 → 判定为失职，本次奖励转入守约人奖励池。

若监督者失职次数超 maxSupervisorMisses → 其质押金被没收，归守约人。

若有效签名中 守约 占比 ≥ checkThresholdPercent → 判定该周期守约成功。

若守约失败次数超出 maxCommitterFailures → 判定整体誓约失败，守约人失去奖励，其质押金被没收，返还给 Creator。

**阶段 3：结算**

守约人成功完成任务：

- 获得奖励池中 committerRewardRatio 对应金额；
- 获得监督者失职转入的奖励；
- 取回自己质押金额。

守约人失约（失败次数超限）：

- 奖励池返还给 Creator；
- 守约人质押金没收。

监督者：

- 每完成一个有效 check 签名，可领取 (supervisorRewardRatio / 总check次数) 的奖励；
- 若失职次数超限 → 所有质押金被没收。

---

## 4. 奖励计算与惩罚机制

### 奖励分配公式

```sql
监督者总奖励 = totalReward × (supervisorRewardRatio / 100)
守约人总奖励 = totalReward × (committerRewardRatio / 100)
每次 check 奖励 = 监督者总奖励 / 总 check 次数
```

### 惩罚规则

| 行为 | 惩罚结果 |
|------|----------|
| 监督者未 check | 当次奖励归守约人，记录失职一次 |
| 监督者累计失职超限 | 没收全部质押金，奖励归守约人 |
| 守约人未完成任务次数超限 | 没收质押金，失去奖励，奖励归 Creator |

---

## 5. 状态管理与签名结构（示例）

```solidity
enum OathStatus { Pending, Active, Completed, Breached, Cancelled }
enum CheckResult { Pending, Success, Failed }

struct Check {
  uint256 slotId;
  address supervisor;
  CheckResult result;
  bytes32 reasonHash;
  bool rewarded;
}
```

---

## 6. 安全建议

- 所有转账使用 pullPayment 模式，防止重入；
- 签名采用 EIP-712 标准，确保链下交互安全；
- 支持链下存储 description 和 证明材料 到 IPFS，引用哈希；
- 未来可引入 仲裁者角色 处理争议情况（如监督者失联等）。

---

## 7. 示例配置参考

| 字段 | 示例值 |
|------|--------|
| totalReward | 100 ETH |
| supervisorRatio | 10% |
| committerRatio | 90% |
| committerStake | 2 ETH |
| supervisorStake | 1 ETH |
| checkInterval | 每 5 天 |
| checkWindow | 2 天 |
| maxSupervisorMisses | 2 次 |
| maxCommitterFailures | 1 次 |


---

# 2025-08-05

# 区块链基础

- **定义**  
  去中心化分布式账本技术，通过按时间顺序将交易记录打包成区块并链接成链，保证数据安全、透明、且不可篡改。

- **核心特性**  
  - **不可篡改**：每个区块包含前一区块的哈希，篡改需重写所有后续区块。  
  - **公开透明 & 匿名**：链上交易记录对外可查，但地址与真实身份不直接关联。  
  - **去中心化**：节点分布式维护账本，无单点控制；抵御故障和审查。  
  - **快速交易**：无需中介，全球节点可直接参与，交易确认高效。

---

# 以太坊概览

- **定位**  
  “区块链 2.0”平台，既是加密货币（ETH），也是支持智能合约的全球共享计算机。

- **智能合约**  
  存储在链上的可执行代码，条件满足时自动触发，支撑去中心化应用（DApp）、DeFi、NFT、DAO 等生态。

- **共识机制演进**  
  1. **PoW 阶段**：矿工通过算力竞争打包，能耗高、TPS ≈ 30。  
  2. **The Merge（2022）**：切换至 PoS，能耗降低 >99%，并引入分片与 Layer 2 扩容方案。

- **生态分层**  
  - **Layer 1（主网）**：EVM 执行层 + PoS 共识层。  
  - **Layer 2**：Optimistic Rollup、ZK Rollup 等批量处理方案。  
  - **侧链**：如 Polygon PoS、xDAI，通过桥接与主网交互。

---

# 行业赛道全览

- ## DeFi（去中心化金融）  
  - **Uniswap（AMM DEX）**：基于 x×y=k 自动定价，流动性提供者赚取交易费。  
  - **Compound（借贷协议）**：存款获 cToken，借款需超额抵押，动态利率、自动清算。  
  - **MakerDAO → Sky（稳定币）**：超额抵押生成 DAI/USDS，通过稳定费率与清算保持与美元挂钩。

- ## NFT（非同质化代币）  
  - **唯一性 & 所有权**：为数字资产赋予唯一标识与可验证的所有权。  
  - **智能合约 & 版税**：自动执行转移并可设定原创版税分配。  
  - **案例**：CryptoPunks、OpenSea。

- ## DAO（去中心化自治组织）  
  通过代币和智能合约进行社区治理与决策，无需传统管理层。

- ## Web3 + 乡建示例  
  - **南塘 DAO**：安徽阜阳乡村发行 NT 代币，用于社区贡献量化、治理投票与本地服务兑换。

# 2025-08-04

1. 学习solidity语法完结done；
2. 寻找sepolia的测试水龙头：  https://cloud.google.com/application/web3/faucet/ethereum/sepolia


# 2025.07.29


<!-- Content_END -->
