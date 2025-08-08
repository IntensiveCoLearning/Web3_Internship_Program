---
timezone: UTC+8
---

# segment7

**GitHub ID:** segment7

**Telegram:** @philointuit

## Self-introduction

segment7，成都，前北师大学生，现cs在读，目前使用lens protocol开发链上同人创作社交平台，项目获某小型黑客松头奖，今年9月将全奖参加新加坡山海坞，蹲蹲坞友。

## Notes

<!-- Content_START -->
# 2025-08-08

`克隆与模仿，也是学习的一环。`

### GitHub 开发协作规范
#### A. 分支 & PR 工作流

1. 分支策略：
    ```
    - 主分支 (main/master)：始终保持可部署状态，任何代码合并前必须经过测试与审查。
    - 开发分支 (develop)：用于日常功能开发，作为 feature 分支的合并目标。
    - 功能分支 (feature/xxx)：以 feature/功能描述 命名，完成后合并至 develop 分支。
    - 修复分支 (fix/xxx)：针对 bug 的修复，以 fix/bug-name 命名，优先合并到 develop 或 main。
    - 发布分支 (release/xxx)：用于发布前准备，如版本打包、测试等，合并后删除。
    ```
2. 提交信息规范

- Issue：关联的 Issue 编号（如有）
- 分类:  
  - feat: 新功能
  - fix: 修复问题
  - docs: 文档更新
  - refactor: 代码重构
  - test: 添加或修改测试
  - chore: 构建过程或辅助工具变动
  - Pull Request（PR）流程

3. 可在项目仓库中添加 `PULL_REQUEST_TEMPLATE.md` 文件，可统一 PR 描述格式，提升协作效率。例如：

    ```
    ## PR 说明
    - 变更内容：简要描述此次 PR 完成了哪些功能或修复了哪些问题。
    - 关联 Issue：填写相关 Issue 编号（如无可留空）。
    - 主要改动：列出代码改动的关键点，如新增哪些函数、修改哪些逻辑等。
    - 测试情况：说明已编写或执行了哪些测试来验证改动。
    - 影响评估：列出可能影响的模块或兼容性问题（如版本依赖）。
    ```

4. Code Review 检查清单（代码评审时可按以下要点逐项检查）：


    - 代码是否符合风格规范，命名清晰、可读性高？
    - 业务逻辑是否正确，边界情况和异常情况是否处理周全？
    - 安全性检查：是否考虑重入、整数溢出、权限校验、外部调用等风险？
    - Gas 消耗：是否避免了不必要的存储操作或大循环？
    - 是否增加了足够的注释和文档说明？是否存在未使用的代码或死代码？
    - 是否编写了充分的单元测试覆盖常见场景和极端情况？

#### B. Issue 管理   

- 描述 Issue 结构推荐：背景 + 问题 + 尝试过的方法 + 环境信息  
`示例`：

    ```
    ### 问题描述
    执行 `forge test` 时出现 `out of gas` 错误

    ### 环境
    - Foundry 版本: 0.2.0
    - Solidity: 0.8.21
    Issue 标签分类标准与自动化：
    ```
- 制定统一的 Issue 标签规则，例如使用 `bug`（缺陷）、`enhancement`（增强）、`security`（安全）、`documentation`（文档）、`question`（问题）等标签；
- 根据需求添加 `high-priority`、`low-priority`、`help wanted` 等。
- 通过 GitHub Actions 等自动化工具，还可自动为新 Issue 或 PR 添加标签（如根据文件路径、标题关键字匹配标签）、标记长期未更新的 Issue 为 `stale` 等，减少人工维护成本。

#### C. 开源协作礼仪
- 所有代码变更需配测试和文档
- 保持沟通公开透明：Prefer PR 评论而不是私信
- 使用讨论区（Discussion）提重大设计变更

# 2025-08-07

`今日普法大讲堂`  
诸如避税受贿贩毒犯罪等黑灰产所得的💰猛烈流入加密币的蓄水池，我称之为走资派乐土，洗钱天堂。

这可是口味正宗的金融化资本主义或者新自由主义实践，毕竟，在资本主义的世界就算是反抗资本主义这种行为本身也是可以拿来卖的，不管是被动还是主动我就不说了。
# 亲亲，我们家子涵说要全面制裁！🥰
![1](https://github.com/segment7/Web3-Internship-Program/blob/a00dc941bccb188f40776fd9c368dc26aa885c21/segment7/0807-1.jpg)
![2](https://github.com/segment7/Web3-Internship-Program/blob/a00dc941bccb188f40776fd9c368dc26aa885c21/segment7/0807-2.jpg)

# 2025-08-06

## 1. ETH签名数据库
由于哈希算法 `keccak256` 是不可逆的，无法反向解码，可用 公开数据库 查询常见函数选择器名称

**常用公开数据库：**  
- Ethereum Signature Database - [4byte.directory](https://www.4byte.directory/)  
- [openchain.xyz](https://openchain.xyz/)
- [Universal Calldata Decoder](https://calldata.swiss-knife.xyz/decoder)
## 2. 警惕代币精度设置
![0806-1](https://github.com/segment7/Web3-Internship-Program/blob/82e8e7413a8fb63944eb4dc19caaf6f0cafa3584/segment7/0806-1.png)

## 3. 警惕 `aggregate` & `multicall` 函数
始终仔细核实签名请求，特别是捆绑交易  
对像 aggregate 或 multicall 的函数保持警惕，因为它们可能隐藏恶意操作

# 2025-08-05

## 1. 警惕Permit2代币授权
Permit2 的工作方式与传统代币授权不同：

- 传统模式：您直接将代币授权给特定合约（例如 Uniswap）
- Permit2 模式：您将代币授权给 Permit2 合约，然后由 Permit2 在内部管理权限

这创建了一个两层授权系统：

1. 代币对 Permit2 的授权（在区块链浏览器中可见）
2. Permit2 内部的权限（在标准区块链浏览器中不可见）

**漏洞：** 如果您只撤销了代币对 Permit2 的授权，但没有撤销内部权限，一旦您再次授权 Permit2，攻击者仍然可以窃取您的代币。

**如何保护自己：**
- 对签名请求保持谨慎，特别是那些请求**无限制数量**的请求
- 验证签名请求中的接收者地址
- 使用显示详细签名信息的硬件钱包
- (可选)使用像 ScamSniffer 的 Permit2 授权管理这样的工具来查看和撤销 Permit2 内部权限

## 2. 警惕恶意 RPC 提供商
恶意 RPC 提供商可以：

- 拦截并修改您的交易，将资金重定向到攻击者的地址
- 显示虚假的代币余额，让您认为您收到了不存在的付款
- 提供虚假的交易状态，让您认为交易失败并诱使您重试
- 收集您的交易历史和钱包活动数据

# 2025-08-04

#### 识别真假域名
Punycode 是一种编码系统，允许将非 ASCII 字符（如 [西里尔字母](https://en.wikipedia.org/wiki/Cyrillic_script) 、中文等）转换为 ASCII 字符，以便在域名系统中使用。攻击者经常利用视觉上相似的字符创建看似合法的域名。     
例如，某些特殊字符看起来与拉丁字母几乎相同，但它们是不同的字符： 可以使用 [Punycoder](https://www.punycoder.com/) 来转换 Unicode 和 Punycode 域名。

![0804-1](https://github.com/segment7/Web3-Internship-Program/blob/8bc93cf51b30d0c6371be197ba637ba484010f5c/segment7/0804-1.png)

#### 以太坊的一个节点接入超级计算机/量子计算机会发生什么？

| 计算机类型        | 可能的使用场景                               | 对以太坊的影响                                |
| -------------------- | ------------------------------------------------ | ------------------------------------------------- |
| **Supercomputer**   **超级计算机**   | Faster block processing, MEV search, simulations更快的区块处理、MEV 搜索、网络模拟 | Improves performance, no protocol-level risk提升性能，但不会对协议层造成风险      |
| **Quantum Computer**    **量子计算机** | Breaks ECDSA (in theory), fakes signatures理论上可破解 ECDSA、伪造签名       | **Critical threat**, but only when quantum scales**严重威胁**，但仅在量子计算能力足够强时才会发生 |


# 2025.07.30


<!-- Content_END -->
