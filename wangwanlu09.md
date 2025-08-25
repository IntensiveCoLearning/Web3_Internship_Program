---
timezone: UTC+8
---

# wanlu

**GitHub ID:** wangwanlu09

**Telegram:** @wangwanlu

## Self-introduction

我是一名初级开发，正在学习以太坊智能合约调用相关知识，比如wagmi等，相关demo已经在我的repo中，希望未来学习更多web3相关开发知识

## Notes

<!-- Content_START -->

# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
好的，这里是你要的部分整理版，专注于 **写简历 & 展示区块链经验**：

* * *

## 写简历 & 展示区块链经验

### 1\. 突出技能 + 项目经验

-   **技能**：React, Next.js, Tailwind, Solidity, Ethers.js
    
-   **项目**：用区块链实现实际功能（如 Token Claim 页面）
    

### 2\. 使用 STAR 方法描述

-   **S — Situation**：项目背景
    
-   **T — Task**：你的任务
    
-   **A — Action**：你做了什么（技术 + 设计决策）
    
-   **R — Result**：结果/成效（数字化指标更佳）
    

### 3\. 强调可迁移技能

-   **UI/UX** → 帮助更容易将设计稿转化为前端界面
    
-   **React / Next.js** → 快速开发交互页面
    
-   **API / 数据管理** → 处理链上和链下的数据交互
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
### 今日学习（O-kitchen 项目 | Demo Day 准备）

1.  **Demo Day 内容完善**
    
    -   梳理 Demo 流程（开场 → 功能演示 → 亮点展示 → Q&A）
        
    -   确定谁负责讲解 demo
        
2.  **持续集成 / 部署 (CI/CD)**
    
    -   检查当前 pipeline 状态（lint、test、build、deploy）
        
    -   确认是否需要补充单元测试或集成测试
        
3.  **项目功能检查**
    
    -   功能回归测试（核心功能能否正常使用）
        
    -   edge cases（用户输入错误、网络不稳定时的表现）
        
    -   性能检查（加载速度、响应流畅度）
        
4.  **学习 & 文档**
    
    -   记录 CI/CD 流程和遇到的问题
        
    -   写出 Demo Day 的准备文档（包括分工、演示流程、备用方案）
        
5.  **小组协作**
    
    -   Stand-up meeting：同步各自任务
        
    -   合并进度表到 Notion
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
# **学习笔记 — 今日开发流程**

## **1 评论功能开发**

* **核心组件**：

  * `CommentSection`：UI 展示评论列表、输入框、回复。
  * `useComments` Hook：提供 `addComment`、`likeComment`、`replyToComment`、`refetch` 功能。
* **功能特点**：

  * 支持 **inline 模式**，在帖子页直接显示评论。
  * 支持 **递归显示回复**。
  * 使用 `Textarea` + `Button` 提交评论或回复。
  * 支持 **回车提交**和 **shift+回车换行**。

### Hook 核心逻辑

* 初始化 `comments`、`loading`、`error`。
* `addComment`：新增评论。
* `likeComment`：点赞指定评论。
* `replyToComment`：添加回复。
* 未来上线生产环境时，只需把 mock 数据替换为实际 API 请求即可。

---

## **2 PostPage 组件改动**

* **保留**：

  * `BackButton`
  * `TooltipProvider`
  * `FeedFloatingActions`
  * `PostCard`
* **新增**：

  * 评论功能集成：`CommentSection` + `useComments`。
  * Storage API 显示：`<StorageDisplay post={post} />`。
* **可扩展性**：

  * 未来可以加 `CommentSheet`、`PostTags` 等。

---

## **3 Git/GitHub 流程**

* **分支管理**：

  * 从主仓拉取最新代码到本地。
  * 新建功能分支 `feature/post-details`。
  * 在功能分支上开发，不直接修改主分支。
* **提交与推送**：

  * 本地分支修改完成后提交。
  * 推送到远程仓库对应分支。
  * 通过 **Pull Request** 请求合并到主仓。
* **遇到问题**：

  * GitHub 显示 **Merging is blocked / Some checks failed**。
  * 原因不是缺失文件，而是 **Vercel 检查未授权或部署失败**。
  * 需要 **团队授权或 CI/CD 检查通过** 才能合并。

---

## **4 重点总结**

* **评论功能开发**：

  * 使用 Hook + UI 组件分离，便于测试和复用。
  * Mock 数据用于本地调试，生产环境可替换为 API。
* **前端页面改动**：

  * 保留导航、浮动动作按钮、PostCard。
  * 新增 Storage 和 Comment 功能，增强用户互动。
* **Git/GitHub 操作**：

  * 保持分支与主仓同步。
  * PR 合并受 CI/CD 检查和权限控制影响，不一定是文件问题。
<!-- DAILY_CHECKIN_2025-08-23_END -->


# 2025-08-22
<!-- DAILY_CHECKIN_2025-08-22_START -->
##完成了ERC标准的学习并输出笔记
- 笔记地址：https://www.notion.so/Ethereum-ERC-Standards-2566f5f0a7338092b860e37b57490c70
##与队友讨论休闲黑客松Demo
- 关于桥插件的开发
<!-- DAILY_CHECKIN_2025-08-22_END -->

# 2025-08-21

### 今日学习/推进计划（按你当前进度定制）

- 目标
  - 明确“逻辑与样式分离”落地方式
  - 完成按钮与卡片的暗黑模式可读性优化
  - 统一颜色来源与使用方式（避免 Hook 层带颜色）

- 关键产出
  - 颜色映射/主题变量小文档（团队约定）
  - ActionButton 颜色从 Hook 移除为 UI 层 variant 驱动的设计草案
  - 暗黑模式适配完成的组件清单与截图对比
  - 简短贡献指南：颜色与暗黑模式约定（放 README 小节）

- 任务与顺序
  1) 颜色与分层规范（30m）
     - 定义语义颜色映射：like/comment/bookmark/share 对应的文本色、填充色（明/暗两套）
     - 约定：Hook 不返回颜色，仅返回语义与 variant
  2) 提升未点击按钮可见度（30m）
     - 已完成：`src/components/post/post-action-button.tsx` 未点击态在暗黑用 gray-200、亮色用 gray-600
     - 补充：计数文本与容器边框对比度检查
  3) 容器与卡片对比度微调（45m）
     - `src/components/post/post-actions-bar.tsx` 增加 `dark:` 边框类，保证分隔线在暗黑可见
     - `src/components/post/post-card.tsx`、`src/components/post/compact-post-card.tsx` 检查文本/背景/边框的 `dark:` 类是否齐全
  4) 重构设计草案（不实装，仅设计稿，45m）
     - `src/hooks/post-actions/use-post-actions-buttons.tsx` 去掉 `strokeColor/fillColor`，新增 `variant` 的接口设计
     - `src/components/post/post-action-button.tsx` 使用 variant→颜色映射（明/暗）方案草图
  5) 回归与文档（30m）
     - Light/Dark 实机核对 Feed 列表（列表/瀑布流）、卡片、按钮
     - README 增加“颜色与暗黑模式约定”和“Hook 不含样式”的说明

- 验收清单
  - Hook 层不出现颜色常量或样式逻辑（本日先完成设计草案）
  - 暗黑模式下按钮默认态可见、悬停清晰、激活一致
  - `FeedHeader` 暗黑适配保留下划线逻辑（已就绪）
  - 主要页面前后截图对比齐全

- 风险与应对
  - 分散改动易遗漏：先完成颜色映射与分层约定，再逐页核对
  - 对比度不达标：暗黑默认灰保持 ≥ gray-200，必要时提高到 gray-100 并复检

- 今日检查点（具体文件）
  - `src/components/post/post-action-button.tsx`（已调暗黑默认灰，检查计数文本）
  - `src/components/post/post-actions-bar.tsx`（边框 `dark:`）
  - `src/components/post/post-card.tsx`、`src/components/post/compact-post-card.tsx`（文本/背景/边框对比度）
  - `src/hooks/post-actions/use-post-actions-buttons.tsx`（准备 variant 设计，先不动代码）

# 2025-08-20

系统的学习Uniswap V4理论https://www.notion.so/Uniswap-V4-2556f5f0a73380e7aaabd2ca7c426b2b这个是我的笔记。成功组队休闲黑客松，从小队项目的github上fork了代码.

# 2025-08-19

# Uniswap学习笔记
## 通过白皮书系统的学习了Uniswap V2 和 V3的演变和特性以下分别是我的两篇学习笔记
- Uniswap V2：https://www.notion.so/Uniswap-V2-2536f5f0a733801f8b31ec85b8379615
-  Uniswap V3：https://www.notion.so/Uniswap-V3-2546f5f0a73380f1a97bf9decf08da6e

# 2025-08-18

## **今日学习笔记 **

### **阶段 1：复习 & 回顾（30 分钟）**

**目标**：熟悉已有代币合约和前端调用逻辑，为 DeFi 练习打基础

**学习内容**：

1. 回顾写好的代币合约

   * 检查 ERC-20 / ERC-721 基本功能：`balanceOf`、`transfer`、`mint` 等
   * 理解合约中变量、函数、映射（mapping）使用方式

2. 理解 ERC-20 / ERC-721 标准

   * ERC-20：代币余额、转账、授权
   * ERC-721：NFT 唯一性、转移、元数据
3. 前端调用练习

   * 使用 Ethers.js / Web3.js 调用代币合约
   * 测试查询余额、转账、mint 功能

**练习建议**：

* 在本地或测试网执行一次 `transfer` 和 `mint`，确认前端显示正确
* 记录遇到的错误或疑问，方便阶段 2 结合解决

---

### **阶段 2：Staking 合约学习**

**目标**：理解质押合约逻辑，为 DeFi 功能搭建基础

**学习内容**：

1. 学习简单 Staking 合约示例

   * 用户存入代币 → 记录数量和时间
   * 计算奖励（可按时间或比例）
   * 用户可随时领取奖励

2. 理解事件触发

   * 例如 `Deposit`、`Withdraw`、`Claim`
   * 前端可以监听事件更新 UI

3. 在 Remix 中实践

   * 编写一个简化的 Staking 合约
   * 测试 Stake、Unstake 和 Claim 功能
   * 观察合约状态变化和事件输出

# 2025-08-17

# 学习笔记

## AMM（Automated Market Maker，自动做市商）

* **定义**：去中心化交易机制，不依赖订单簿，而是通过智能合约里的流动性池（Liquidity Pool）完成交易。
* **原理**：

  * 用户存入代币对（如 ETH/USDC），成为流动性提供者 (LP)。
  * 交易者直接与池子交互，用一种代币换另一种。
  * 价格由算法公式决定（最经典的是恒定乘积公式：x \* y = k）。
* **优势**：

  * 去中心化、随时可交易
  * 人人都能成为 LP，赚取手续费
* **劣势**：

  * 无常损失（Impermanent Loss）：价格波动可能导致 LP 损失
  * 滑点（Slippage）：低流动性或大订单会造成价格偏离预期
* **直观理解**：

  * 流动性池越大 → 价格越稳定 → 滑点越低
  * 类比：大水桶倒水，水位变化小；小水桶倒水，水位变化大

## 滑点与“被夹”

* **滑点**：成交价格偏离预期，是交易量改变池子比例导致的价格变化。
* **被夹**：交易感受上的说法，通常指价格瞬间大幅波动造成亏损。
* **关系**：

  * 大滑点容易造成“被夹”的情况
  * 高流动性池可以降低滑点，从而减少被夹风险

## Uniswap V3 与 V4

* V3：

  * 集中流动性，LP 可选择价格区间，提高资本效率
  * 多费率（0.05%、0.3%、1%）
  * 每个池单独合约，部署成本较高
  * 功能固定，主要是 AMM + LP 管理
* V4：

  * 延续 V3 集中流动性
  * 引入 Hooks，可自定义交易前后逻辑
  * 单一合约管理所有池，Gas 成本低
  * 原生支持限价单、动态手续费、自动化策略
* **总结**：

  * V3 更像做市商工具，操作直接，功能固定
  * V4 更像 DeFi 平台，可编程性高，灵活度大

## 个人理解

* V3 更适合传统做市，易上手
* V4 提供更多可编程功能，能实现自动化策略和自定义逻辑，更灵活

# 2025-08-16

## 今天学习笔记 - Cheetos Token DApp

### 今日目标

* 完成 **Cheetos Token DApp** 开发
* 解决前端问题
* 部署项目到 Vercel
* 学习钱包集成

### 项目概述

* 名称: Cheetos Token DApp
* 功能: 代币空投/领取
* 技术栈: Next.js, React, TypeScript, Foundry, Solidity
* 核心功能:

  * 用户连接钱包领取 10 CHE
  * 每个地址只能领取一次
  * 需要 ≥0.01 ETH
  * 总领取次数限 1000

### 技术问题

* 前端报错: `motion` 导入与 `NavBar className`
* 页面布局: 移动端覆盖、间距问题
* 样式统一: ConnectWallet 按钮

### 国际化

* 页面文本、按钮、提示信息英文化

### 更新组件

* `page.tsx`, `ConnectWallet.tsx`, `ClaimToken.tsx`, `TokenInfo.tsx`

### 部署

* Vercel 部署
* 生产地址: [Token DApp](https://token-dapp-opal.vercel.app)

### 钱包集成

* 支持: MetaMask, RainbowKit, OKX钱包
* 代币查看: 自动检测、手动添加合约地址、区块浏览器
* 测试网: Sepolia

### 项目配置

* 合约地址: `0x712e4F191Fa3516CA6f15a3F040f6be9BEaD5155`
* 代币符号: CHE
* 总供应量: 10,000 CHE
* 每次领取: 10 CHE
* 最大领取次数: 1,000

### UI/UX 改进

* 响应式设计、移动端适配
* 连接钱包前后不同界面
* 实时状态反馈、交易状态跟踪

### 问题排查经验

* 常见问题: 依赖缺失、类型错误、样式问题、环境变量错误
* 调试技巧: 浏览器开发者工具、控制台、网络、合约地址验证

### 学习收获

* 技术技能: Next.js, React, TypeScript, Tailwind CSS, 区块链交互, 钱包集成, 部署
* 项目经验: 完整 DApp 开发流程、前端与合约集成、UX设计、错误处理、维护

### 下一步计划

**短期**

* 测试网站功能
* 优化移动端体验
* 添加更多钱包支持
* 完善错误处理

**长期**

* 添加更多代币功能
* 优化 gas 费用
* 数据分析与社区功能

# 2025-08-15

## **Cheetos Token DApp 学习笔记**

### 1. **项目概述**

* **名称**：Cheetos Token DApp
* **技术栈**：

  * **后端**：Solidity + Foundry（智能合约、测试、部署）
  * **前端**：Next.js + TypeScript + Tailwind CSS
  * **Web3集成**：Wagmi + Viem + RainbowKit
* **当前状态**：本地开发和测试完成，准备部署到 **Sepolia 测试网**

---

### 2. **智能合约部分**

* **主合约**：`src/Cheetos.sol`
* **代币信息**：

  * 名称：Cheetos (CHE)
  * 最大供应量：10,000 CHE
  * 每次领取：10 CHE
  * 总领取次数上限：1000次
  * 领取条件：持有至少 0.01 ETH
* **关键机制**：

  * ERC20 标准实现
  * 防重复领取（`hasClaimed`）
  * 限量发行（`remainingClaims`）
  * 合约所有权管理
* **部署脚本**：

  * `script/DeployLocal.s.sol` → 本地部署（Anvil）
  * `script/DeployCheetos.s.sol` → 测试网部署
* **测试脚本**：

  * `script/TestClaim.s.sol` → 领取功能、资格验证测试

---

### 3. **测试流程**

* **单元测试**：`forge test`（模拟EVM，速度快，无真实Gas消耗）
* **本地测试**：

  * 使用 Anvil 启动本地 Sepolia 模拟环境（链ID: 11155111）
  * 执行部署脚本 + 前端交互
* **测试通过**：

  * 部署成功
  * 领取条件验证正常
  * 多账户测试无异常

---

### 4. **前端开发**

* **框架**：Next.js 14 + App Router
* **核心组件**：

  * `ConnectWallet.tsx` → 钱包连接
  * `TokenInfo.tsx` → 代币信息展示
  * `ClaimToken.tsx` → 领取功能
* **Web3功能**：

  * 钱包连接（MetaMask / OKX）
  * 网络切换提示（本地 / 测试网）
  * 实时余额 & Token 数据
  * 交易状态追踪
* **UI**：

  * Tailwind CSS
  * 响应式设计
  * 用户提示和错误处理

---

### 5. **当前阶段**

* **目标**：部署到 Sepolia 测试网
* **待办**：

  1. 创建 `.env` 文件（存储API密钥和私钥）
  2. 获取 Infura/Alchemy API 密钥
  3. 获取 Etherscan API 密钥（用于验证源码）
  4. 从水龙头获取 Sepolia ETH
  5. 执行部署脚本
  6. 更新前端配置（合约地址、RPC等）

---

### 6. **下一步计划**

* **短期**：

  * 部署到测试网
  * 前端对接真实网络
  * 完整功能验证
* **中期**：

  * 优化用户体验
  * 安全审计和Gas优化
* **长期**：

  * 主网部署（可选）
  * 社区推广

---

### 7. **技术亮点**

* 现代化全栈 DApp 技术栈
* 完整的测试覆盖（单元 + 集成）
* 本地模拟测试网（Anvil）
* 简洁直观的前端 UI
* 实时链上交互

---

### 8. **经验总结**

1. **先本地跑通 → 再部署测试网**，可以节省测试币和部署时间。
2. **Anvil模拟Sepolia** 能在本地调试前端和合约交互，等功能成熟后直接切换到真实网络。
3. **脚本化部署**（Foundry `script/`）比手动部署安全且可复用。
4. 前端要 **随网络切换动态更新** 合约地址，避免调用错误。
5. `.env` 管理敏感信息（私钥、API Key）是必须的，不能写死在代码里。

# 2025-08-14

# **学习笔记 — Cheetos ERC20 Claim 合约 (2025-08-14)**

## 1. **目标**

* 实现一个 ERC20 代币 `Cheetos (CHE)`，允许用户 **Claim** 代币。
* 每个地址 **只能 Claim 一次**。
* 当前只要求用户持有 **Sepolia ETH** 即可 Claim。
* 部署者不持有初始代币，避免大额集中。

---

## 2. **合约设计**

### **常量**

```solidity
uint256 public constant CLAIM_AMOUNT = 10 * 10 ** 18;    // 每次 claim 数量
uint256 public constant MAX_TOTAL_SUPPLY = 10000 * 10 ** 18; // 最大总供应量
uint16 public constant MAX_CLAIMS = uint16(MAX_TOTAL_SUPPLY / CLAIM_AMOUNT); // 最大 claim 次数
uint256 public constant MIN_ETH_REQUIRED = 1; // 至少 1 wei Sepolia ETH
```

### **状态变量**

```solidity
address public immutable owner;               // 合约部署者
mapping(address => bool) public hasClaimed;  // 记录地址是否已 claim
uint16 public claimCount;                     // 已 claim 总次数
```

### **自定义错误**

* `AlreadyClaimed()`：重复 Claim
* `NoSepoliaETH()`：ETH 不足
* `ExceedsMaxClaims()`：超过最大 claim 次数

---

## 3. **核心函数**

### **Eligibility 检查**

```solidity
function isEligible(address account) public view returns (bool) {
    return account.balance >= MIN_ETH_REQUIRED;
}
```

* 仅检查用户持有 Sepolia ETH。
* 可扩展为 ERC20 或其他条件，但当前版本保持简单。

### **Claim 函数**

```solidity
function claim() external {
    if (hasClaimed[msg.sender]) revert AlreadyClaimed();
    if (claimCount >= MAX_CLAIMS) revert ExceedsMaxClaims();
    if (msg.sender.balance < MIN_ETH_REQUIRED) revert NoSepoliaETH();

    hasClaimed[msg.sender] = true;
    unchecked { ++claimCount; }
    _mint(msg.sender, CLAIM_AMOUNT);
}
```

* **顺序检查**：重复 Claim → 超过总量 → ETH 不足。
* **Gas 优化**：

  * 使用 `uint16` 控制 `claimCount`，节省存储 Gas。
  * 使用自定义错误比 `require` 字符串更省 Gas。
  * `unchecked` 增量计数。

### **辅助函数**

```solidity
function remainingClaims() external view returns (uint16) {
    return MAX_CLAIMS - claimCount;
}

function maxTotalSupply() external pure returns (uint256) {
    return MAX_TOTAL_SUPPLY;
}

function allTokensClaimed() external view returns (bool) {
    return claimCount >= MAX_CLAIMS;
}

function minETHRequired() external pure returns (uint256) {
    return MIN_ETH_REQUIRED;
}
```

---

## 4. **Gas 分析**

| 项目         | Gas (Avg)  | 说明                           |
| ---------- | ---------- | ---------------------------- |
| 部署成本       | 683,490    | 约 3.1 KB，低于复杂 eligibility 版本 |
| claim      | 69,764     | 包含 mint + mapping 标记 + 状态更新  |
| isEligible | 3,017      | 仅 ETH 检查，非常轻量                |
| 常量/变量读取    | 200\~2,600 | 标准 ERC20 查询操作                |

✅ 优化点：

* 去掉复杂 ERC20/struct eligibility
* 不 mint 给部署者
* 使用 uint16 + 自定义错误节省 Gas

---

## 5. **测试思路（参考之前的 Forge 测试）**

1. **基础 Claim 测试**

   * 有 ETH 的地址可 claim
   * 无 ETH 的地址不可 claim
   * 重复 claim 会失败

2. **最大 Claim 限制**

   * 模拟 1000 个地址 claim
   * 超过 MAX\_CLAIMS 会失败
   * `remainingClaims()` 返回 0

3. **边缘情况**

   * Claim 后余额变化导致不再符合资格
   * fuzz 测试随机用户 claim 行为
   * 查询辅助函数返回值是否正确

---

## 6. **总结与经验**

* 简化 eligibility 条件可以大幅降低 Gas 消耗。
* 自定义错误 + `uint16` 存储 + `unchecked` 操作是 Gas 优化良好实践。
* 通过 mapping 记录 Claim 状态，保证每个地址只能 claim 一次。
* 当前版本非常适合 **测试链（Sepolia）空投**，生产链可扩展 ERC20 条件。
* 部署者不持有代币，降低操控风险。

# 2025-08-13

## 今日学习内容总结（Foundry + Solidity + WSL 环境）

### 一、环境搭建

1. **安装 WSL（Windows Subsystem for Linux）**

   * 在 Windows 11 上安装 WSL2 并选择 Ubuntu 作为 Linux 发行版。
   * 设置 Linux 用户名和密码完成初始化。

2. **安装 Foundry**

   * 在 WSL 的 Ubuntu 终端执行：

     ```bash
     curl -L https://foundry.paradigm.xyz | bash
     source ~/.bashrc
     foundryup
     ```
   * 验证安装：

     ```bash
     forge --version
     anvil --version
     ```
   * 成功安装 `forge`、`anvil`、`cast`、`chisel`。

3. **VS Code 配合 WSL**

   * 安装 VS Code 的 WSL 扩展，实现直接在 Windows 编辑 Linux 文件，并在 WSL 终端运行命令。
   * 项目可以放在 Windows 文件系统中，通过 WSL 访问。

### 二、Foundry 项目初始化

1. 创建新项目：

   ```bash
   cd /mnt/c/Projects/Defi
   forge init MyFirstFoundryProject
   ```
2. 安装依赖：

   ```bash
   cd MyFirstFoundryProject
   git init
   forge install OpenZeppelin/openzeppelin-contracts
   ```

   * 成功安装 OpenZeppelin 合约库，为后续合约编写做准备。

### 三、Solidity 合约准备

* 学习了 Solidity 的基本语法：

  * 文件头：

    ```solidity
    // SPDX-License-Identifier: UNLICENSED
    pragma solidity ^0.8.20;
    ```
  * 可以创建简单合约，如 `Counter`：

    ```solidity
    contract Counter {
        uint256 public number;

        function setNumber(uint256 newNumber) public {
            number = newNumber;
        }

        function increment() public {
            number++;
        }
    }
    ```
* 了解了 ERC20 合约引用方式（OpenZeppelin）：

  ```solidity
  import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";
  ```
* 可以创建一个最基础的代币合约骨架：

  ```solidity
  contract MyToken is ERC20 {
      constructor(string memory name, string memory symbol, uint256 initialSupply) ERC20(name, symbol) {
          _mint(msg.sender, initialSupply);
      }
  }
  ```

### 四、总结

* 今天主要掌握了 **开发环境搭建**、**Foundry 项目初始化** 以及 **Solidity 合约基础编写**。
* 尚未进入测试和前端交互阶段，为后续开发打下基础。
* 下一步计划：完成合约逻辑后再进行本地测试和前端连接。

# 2025-08-12

### 今天学习内容总结

1. **DeFi 合约基础**

   * 了解了 ERC4626 Vault 合约是什么，及其作为标准化金库的基本作用
   * 理解了 Solidity 合约中的 `import` 语法和接口定义（interface）
   * 学习了常用的合约工具库：`Ownable`（权限控制）、`ReentrancyGuard`（防重入攻击）

2. **Solidity 智能合约基础**

   * 写了简单的存取款合约示例，理解了变量、函数、事件、权限控制等基本概念
   * 学习了如何在本地使用 Hardhat 或 Ganache 部署和测试智能合约

3. **前端与智能合约的关系**

   * 明白了前端调用智能合约的基本流程和关键点，如合约接口、交易签名、钱包连接
   * 了解了使用 Scaffold-ETH 框架搭建前端项目的基本步骤

4. **Git 远程仓库操作**

   * 解决了远程仓库地址冲突的问题，掌握了如何删除旧远程地址并添加新远程地址

# 2025-08-11

#提前预习什么是DApp
---

### 1. DApp 基础介绍

* **DApp**：基于区块链的去中心化应用，无中心服务器，数据和逻辑由链和智能合约保障。
* **区别**：传统应用依赖中心服务器，DApp依赖区块链网络。
* **核心组成**：前端界面、智能合约、区块链网络。
* **去中心化优势**：安全透明、抗审查、用户掌控数据。

### 2. DApp 架构设计

* **智能合约层**：链上业务逻辑代码。
* **前端应用**：用户交互界面（网页/移动端）。
* **区块链节点连接**：RPC节点，钱包集成。
* **数据存储**：链上存储有限，链下数据用IPFS等去中心化存储。
* **安全设计**：合约安全审计、交易签名验证。

### 3. 开发环境搭建

* **工具**：Solidity、Hardhat、Truffle、Remix。
* **本地链**：Ganache、Hardhat Network。
* **前端框架**：React + web3.js/ethers.js。

### 4. 智能合约开发

* **合约编写**：Solidity语言。
* **编译与部署**：自动化脚本（Hardhat、Truffle）。
* **测试调试**：单元测试，安全审计。
* **优化**：Gas优化、漏洞修复。

### 5. 前端开发

* **链交互**：调用合约方法，监听事件。
* **钱包连接**：MetaMask、WalletConnect。
* **交易处理**：签名、发送、确认。
* **UI设计**：用户友好、安全提示。

### 6. 集成与测试

* **测试网部署**：Ropsten、Rinkeby、Polygon Mumbai。
* **集成测试**：合约与前端联调。
* **性能与异常**：优化响应，处理失败交易。

### 7. 部署上线

* **主网部署**：智能合约发布到主链。
* **前端托管**：Netlify、Vercel、IPFS + ENS。
* **版本管理**：代理合约升级策略。

### 8. 运维与监控

* **节点维护**：自己跑节点或用第三方节点。
* **交易监控**：跟踪交易状态，数据分析。
* **用户反馈**：收集改进，版本迭代。

# 2025-08-10

今日学习内容包括两部分：
1.	NFT 图片资源的存储与访问
了解了如何将 NFT 图像文件上传至去中心化存储网络（如 IPFS），并通过生成的 CID（Content Identifier）获得唯一可访问的 imageURI。
2.	基于 ECDSA 签名的铸造授权机制
掌握了在智能合约中通过指定 _signer 公钥来验证签名的流程。签名由项目方使用其对应私钥离线生成，包含 imageURI 等数据。合约在 mint 时使用 _verify 方法校验签名有效性，从而确保只有获得项目方授权的资源可被铸造为 NFT，并且 NFT 最终归属于发起交易的 msg.sender 地址。

# 2025-08-09

前两天Scaffold-ETH版本装成了旧版。今日改为新版，有了最新的Next+wagmi+viem等，今日正式开始全面搭建NFT.

# 2025-08-08

# 使用 Scaffold-ETH + Next.js + wagmi + viem + RainbowKit 开发 DApp

---

## 一、背景和方案选择

* **目标**：用 Scaffold-ETH 提供的 Hardhat 环境写智能合约，部署到测试网（如 Sepolia）。
* **前端**：放弃 Scaffold-ETH 自带的 React-app，使用全新 Next.js 框架，搭配 wagmi、viem 和 RainbowKit 实现钱包连接和合约交互。
* **原因**：

  * Scaffold-ETH 提供了标准的合约开发和部署工具链，方便快速迭代智能合约。
  * Next.js 是更现代和流行的 React 框架，支持 SSR/ISR，有更好的性能和开发体验。
  * wagmi + viem 提供轻量且强大的以太坊交互 Hooks 和工具。
  * RainbowKit 提供美观且易用的钱包连接 UI。

---

## 二、开发环境准备

### 1. Scaffold-ETH 合约环境搭建

* 初始化项目（可用官方模板）。

* **安装依赖**：

  * 遇到 `gluegun`、`concat-stream` git 依赖无法拉取的问题，需在 `package.json` 里添加 `"resolutions"`，指定正确版本：

    ```json
    "resolutions": {
      "gluegun": "^5.2.0",
      "concat-stream": "^2.0.0"
    }
    ```
  * 解决 node 版本兼容问题，Hardhat 2.11.2 要求 Node 版本 14/16/18，当前用 Node 22 会报错，建议切换到符合版本。

* 部署合约需要：

  * `.env` 配置测试网 RPC 地址（如 Alchemy 或 Infura 提供的 Sepolia RPC）。
  * 私钥（钱包的私钥）用于部署。

* 运行 `yarn deploy` 或 `npx hardhat deploy --network sepolia` 部署合约。

### 2. OpenZeppelin 依赖安装

* 在合约包目录（如 `packages/hardhat`）用 yarn 添加：

  ```
  yarn workspace @scaffold-eth/hardhat add @openzeppelin/contracts
  ```
* 确保依赖成功安装且能导入。

---

## 三、前端开发环境搭建（Next.js + wagmi + viem + RainbowKit）

### 1. 新建 Next.js 项目

* 在 Scaffold-ETH 项目外（或任意目录）运行：

  ```
  npx create-next-app@latest my-nft-frontend
  ```
* 选择配置（ESLint、Tailwind、App Router等按需选择）。

### 2. 安装关键依赖

```
cd my-nft-frontend
yarn add wagmi viem @rainbow-me/rainbowkit ethers
```

### 3. 基本配置

* 在 `_app.tsx` 或 `app/layout.tsx` 里配置 RainbowKit 和 wagmi Provider：

```tsx
import { WagmiConfig, createClient, configureChains, mainnet, sepolia } from 'wagmi';
import { publicProvider } from 'wagmi/providers/public';
import { RainbowKitProvider, getDefaultWallets } from '@rainbow-me/rainbowkit';
import '@rainbow-me/rainbowkit/styles.css';

const { chains, provider } = configureChains(
  [sepolia, mainnet],
  [publicProvider()]
);

const { connectors } = getDefaultWallets({
  appName: 'My NFT DApp',
  chains,
});

const wagmiClient = createClient({
  autoConnect: true,
  connectors,
  provider,
});

function MyApp({ Component, pageProps }) {
  return (
    <WagmiConfig client={wagmiClient}>
      <RainbowKitProvider chains={chains}>
        <Component {...pageProps} />
      </RainbowKitProvider>
    </WagmiConfig>
  );
}

export default MyApp;
```

### 4. 调用合约示例

* 使用 `viem` 和 `wagmi` hooks 调用部署好的合约的 mint 函数或读取数据。

---

## 四、部署与验证

* 合约部署到 Sepolia 测试网，确保 `.env` 配置正确。
* 通过 Etherscan 测试网查看部署和交易状态。
* 用 Next.js 前端连接钱包，调用合约，完成 Mint 流程。
* 可以上传 NFT 元数据及图片至 IPFS，确保 tokenURI 有效。

---

## 五、常见问题及解决方案

| 问题                         | 解决方案                                            |
| -------------------------- | ----------------------------------------------- |
| `gluegun` 依赖 GitHub 仓库找不到  | 在 package.json 用 resolutions 强制指定版本，或清除缓存重新安装   |
| Hardhat node 版本不兼容         | 切换 Node 版本到 14/16/18                            |
| yarn add 报错 workspace root | 使用 `yarn workspace <name> add <package>` 指定包内安装 |
| PowerShell 删除文件夹命令错误       | 使用 `Remove-Item -Recurse -Force <folder>`       |
| 合约部署 RPC 配置                | 使用 Infura/Alchemy 提供的测试网 RPC URL 和私钥配置在 .env    |

# 2025-08-07

## **NFT Mint Demo 流程概念版**

---

### **1. 初始化开发环境**

* 选择 **Hardhat** 作为开发框架，用于编译、部署和测试智能合约。
* 安装 **OpenZeppelin** 合约库，避免从零写标准 ERC-721 逻辑。

---

### **2. 编写 NFT 智能合约**

* 使用 Solidity 创建合约，继承 **ERC721URIStorage**（用于支持 NFT 元数据 URI）。
* 添加一个 `mintNFT` 函数，允许合约所有者铸造新的 NFT。
* 每个 NFT 绑定一个 `tokenURI`（通常指向 IPFS 上的 JSON 文件，描述图片和属性）。

---

### **3. 上传 NFT 资源**

* NFT 图片和元数据不能直接放链上（成本高），通常上传到 **IPFS**。
* **IPFS 文件结构**：

  * 图片文件 → 生成 IPFS 哈希。
  * JSON 元数据文件（包含图片链接） → 生成 IPFS 哈希。

---

### **4. 部署合约到测试网**

* 使用 **Hardhat 脚本**调用部署逻辑，将合约部署到测试网（如 Sepolia）。
* 需要配置测试网 RPC（Alchemy/Infura）+ 部署者钱包私钥。

---

### **5. 前端交互（DApp）**

* 用 **React + Wagmi + RainbowKit** 搭建前端。
* 功能：

  * 连接钱包（MetaMask）。
  * 点击按钮 → 调用合约的 `mintNFT` 函数 → 发送交易。

---

### **6. 验证结果**

* 在 **Etherscan 测试网**查看交易成功。
* 在 **OpenSea Testnet**（或类似平台）查看 NFT 是否显示。

---

## **核心知识点**

* **Hardhat**：智能合约开发框架（编译、部署、测试）。
* **OpenZeppelin**：安全、标准的合约库（ERC-20、ERC-721、ERC-1155）。
* **ERC-721**：NFT 标准（每个 token 独一无二）。
* **IPFS**：分布式文件存储，NFT 元数据常用方案。
* **前端交互**：通过 wagmi（Web3 Hooks）+ RainbowKit（钱包 UI）连接以太坊。

# 2025-08-06

## 使用 Scaffold-ETH 2 部署 NFT 项目

### **今日目标**

* 掌握 Scaffold-ETH 2 快速搭建和部署 Web3 应用的流程
* 在以太坊测试网（Sepolia）部署 NFT 智能合约
* 将前端 DApp 部署到 Vercel，生成可访问的公开链接

---

### ** 核心学习内容**

#### **1. Scaffold-ETH 2 的作用**

Scaffold-ETH 2 是一个 Web3 全栈开发框架，集成了：

* **智能合约开发（Hardhat）**
* **前端框架（Next.js）**
* **自动合约接口生成**
* **钱包集成和测试工具**

它的核心优势是让开发者 **从编写合约到部署前端一站式完成**。

---

#### **2. 项目部署流程**

1. **初始化 Scaffold-ETH 项目**

   * 自动生成 `hardhat` 和 `nextjs` 两个包。
   * 前端和合约集成度高，减少手动配置。

2. **生成部署钱包**

   * 使用 `yarn generate` 创建新钱包，并设置密码加密。
   * 支持导入到 MetaMask，或使用 Scaffold-ETH 的 Burner Wallet。

3. **配置 Sepolia 测试网**

   * 在 `hardhat.config.ts` 配置 RPC URL 和私钥。
   * Sepolia 是以太坊官方测试网，适合部署练习。

4. **部署智能合约**

   ```bash
   yarn deploy --network sepolia
   ```

   部署完成后输出合约地址，并同步到前端。

   本次部署的合约地址：

   ```
   0x297fFCAf15eAAB709b875ad74a7ED36B4591F895
   ```

   可以在 [Sepolia Etherscan](https://sepolia.etherscan.io/) 查询。

5. **启动前端应用**

   * 前端自动集成合约信息，支持连接钱包。
   * 本地调试：

     ```bash
     yarn start
     ```

6. **部署前端到 Vercel**

   * 使用 `yarn vercel` 进行上线，生成可访问的 URL。

   本次项目前端地址：
   [https://nft-deployment.vercel.app/](https://nft-deployment.vercel.app/)

---

#### **3. 学到的知识**

* Scaffold-ETH 2 提供一体化开发体验，省去了 ABI 地址手动绑定的繁琐操作。
* 明确了 **Web3 项目标准流程**：钱包 → 测试网 → 合约部署 → 前端集成 → 上线。
* 学会使用 Vercel 快速上线 Next.js DApp。
* 知道了 API Key 的作用（Alchemy RPC、Etherscan 验证）。

# 2025-08-05

学习了安全相关知识，对面试出现的恶意软件保持警惕，因最近案列太多。以前有关注漫雾后面还会持续关注。合约部署研究中。

# 2025-08-04

完成了全部web3入门导读，加入LXDAO Discord, NFT mint仍然有问题，但整个流程已经读完。


# 2025.07.30


<!-- Content_END -->
