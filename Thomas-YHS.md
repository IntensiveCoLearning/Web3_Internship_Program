---
timezone: UTC+5
---

# 岳鸿枢（Thomas）

**GitHub ID:** Thomas-YHS

**Telegram:** @Thomas_YHS

## Self-introduction

金融学硕士在读，本科CS，2年web2经验，会Solidity，参加了Arbitrum残酷共学，LXDAO成员

## Notes

<!-- Content_START -->
# 2025-08-09

## **核心概念与创新特性**

### **1. 集中流动性（Concentrated Liquidity）**

- 与 V2 不同，V3 允许流动性提供者（LP）指定一个 **价格区间** 内提供流动性，而不是覆盖整个价格曲线 (0, ∞)，极大提升资金利用率 。
- 示例说明：Alice 在 V2 中分布在整条曲线上，而 Bob 在 V3 中仅在 1,000–2,250 区间提供流动性，两人收益相同但 Bob 使用资金更少 。

### **2. Ticks 与 Tick Spacing**

- 为实现集中流动性，V3 将价格空间划分为离散的预定义点，称为 **ticks** 。
- Tick 间隔（tick spacing）取决于设置的手续费等级，不同手续费等级对应不同的最小价格间隔和流动性分布 。

### **3. 多费率池（Fee Tiers）**

- 每个交易对可以创建多个池子，支持不同的手续费率：当前常见为 0.05%、0.30% 和 1%，通过治理可添加更多，例如稳定币可低至 0.01% 。

### **4. 非同质化流动性（NFT Positions）**

- 每个 LP 在 V3 中的流动性是以 NFT（ERC-721）形式表示的，因为每个头寸都有唯一的价格区间；不再是 fungible ERC-20 LP 代币 。

### **5. 灵活手续费和治理**

- 手续费结构可在池子创建时灵活设定，治理可以管理协议手续费份额、费率等级及 tick spacing 等参数 。

### **6. 预言机升级（Oracle Improvements）**

- V3 提供更强大的 TWAP（时间加权平均价格）功能，不再依赖用户存储历史累积价格，而是内置了一个环形缓冲区记录多个历史 checkpoint，支持计算几何平均价格（减少极端值影响）和流动性累积 。

# 2025-08-08

今天分享了，自己的笔记，明天准备学习uniswap和完善我的程序

# 2025-08-07

ww.notion.so/245a9133700780bd9b4ed60dde7e268c?pvs=21)

## 什么是Foundry

Foundry 是一个智能合约开发工具链。

Foundry 管理您的依赖项，编译您的项目，运行测试，部署，并允许您通过命令行和 Solidity 脚本与链交互。

```bash
curl -L https://foundry.paradigm.xyz | bash

# Install forge, cast, anvil, chisel
foundryup
```

通过这个命令导入Foundry

## **Foundry 的核心组件**

### **1. Forge - 主要开发工具**

- **编译合约**：forge build
- **运行测试**：forge test
- **部署合约**：forge script
- **管理依赖**：forge install

### **2. Cast - 合约交互工具**

- 与已部署合约交互
- 发送交易
- 查询区块链数据

### **3. Anvil - 本地测试网络**

- 快速启动本地以太坊节点
- 预配置的测试账户
- 可配置的区块时间

## Cast命令

<aside>
🌐

Cast命令是一种与区块链高效调用的方式，非常的方便快捷好用！



---

```bash
source .env && cast balance XXXX --rpc-url $SEPOLIA_RPC_URL
```

验证账户代币含量

---

### 部署前检查清单可使用的cast命令

```bash
# 1. 验证私钥
cast wallet address $PRIVATE_KEY

# 2. 检查余额
cast balance $(cast wallet address $PRIVATE_KEY) --rpc-url $SEPOLIA_RPC_URL

# 3. 检查网络
cast chain-id --rpc-url $SEPOLIA_RPC_URL  # 应该返回11155111 (Sepolia)

# 4. 检查gas价格
cast gas-price --rpc-url $SEPOLIA_RPC_URL
```

### cast 还可以调用合约函数

```bash
# 调用合约只读函数
cast call <合约地址> "balanceOf(address)" <账户地址> --rpc-url $RPC_URL

# 发送交易
cast send <合约地址> "transfer(address,uint256)" <接收者> <数量> --private-key $PRIVATE_KEY --rpc-url $RPC_URL
```

## 合约部署

[https://www.notion.so/ThomasToken-245a9133700780b3bfe5fd9d168da894](https://www.notion.so/ThomasToken-245a9133700780b3bfe5fd9d168da894?pvs=21)

合约的部署请参考这篇文章的部署部分！

</aside>

# 2025-08-06

## Foundry框架

状态: 进行中
创建日期: 2025年8月4日
标签: Foundry
笔记类型: 技术笔记
笔记目的: 日常学习

Foundry 需要注意的问题总结

## 什么是Foundry

Foundry 是一个智能合约开发工具链。

Foundry 管理您的依赖项，编译您的项目，运行测试，部署，并允许您通过命令行和 Solidity 脚本与链交互。

```bash
curl -L https://foundry.paradigm.xyz | bash

# Install forge, cast, anvil, chisel
foundryup
```

通过这个命令导入Foundry

## **Foundry 的核心组件**

### **1. Forge - 主要开发工具**

- **编译合约**：forge build
- **运行测试**：forge test
- **部署合约**：forge script
- **管理依赖**：forge install

### **2. Cast - 合约交互工具**

- 与已部署合约交互
- 发送交易
- 查询区块链数据

### **3. Anvil - 本地测试网络**

- 快速启动本地以太坊节点
- 预配置的测试账户
- 可配置的区块时间

# 2025-08-05

今天参加了，WEB3安全会议
1. 转账之前要多确认信息
2. 剪贴板也要注意！！！
3. RPC攻击
可以使用chainlist来防止RPC攻击
下载应用的时候一定要看清楚，放慢速度。
 假zoom
DeepFeek生成假的影片。
今天写了一个ERC20的项目，模仿PEPE币，编写了自己的ThomasToken，TT币，笔记方面用Notion构建了一套包含分类的数据库，可以更好的学习还有记笔记了。
按照计划

# 2025-08-04

今天是学习第一天，主要完成了基础工具的学习，创建了Twitter发了第一条博客，MetaMask建了新账号并领取了Sepolia ETH的测试币，阅读了远程工作的相关方法和建议，受益匪浅，明天计划开始入门导读的学习。


# 2025.08.02


<!-- Content_END -->
