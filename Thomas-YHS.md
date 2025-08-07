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
