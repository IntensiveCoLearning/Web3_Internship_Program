---
timezone: UTC+8
---

# lynn

**GitHub ID:** Free-EntropyMXZ

**Telegram:** @lyndonma

## Self-introduction

web3初学者，做过一些学习项目，涉及defi，zkp，web3+ai，希望通过公学有更深入的钻研

## Notes

<!-- Content_START -->
# 2025-08-09

# IVX协议`swapMargin`漏洞分析

## 1. 背景
IVX是一个期权协议（适用于永续合约），允许用户存入抵押品（如USDC）开设仓位。`swapMargin`函数允许用户将抵押品从一种支持的代币（例如USDC）交换为另一种支持的代币（例如DAI），通过Uniswap执行交换。

### 漏洞核心
- **问题**：`swapMargin`未验证Uniswap交换后收到的金额（`amountOut`）是否足以维持仓位不可清算（liquidatable）。
- **攻击方式**：攻击者通过指定`amountOut=0`（接受任何输出金额）并结合前置交易（frontrunning）操纵Uniswap池价格，获取极低输出（如1 DAI），然后提取真实价值，抽走抵押品。
- **后果**：攻击者规避亏损（如-100美元PnL），抽走99%抵押品，协议的流动性池（LP）承担损失，资不抵债。

## 2. 攻击原理
攻击者利用Uniswap的滑点机制和协议验证缺失，通过前置交易和重复调用`swapMargin`，逐步抽走抵押品。

### 初始状态
- **抵押品**：1000 USDC，仓位价值1000美元（杠杆1x）。
- **PnL**：-100美元（亏损未扣除）。
- **Uniswap池**：USDC/DAI，正常价格1 USDC ≈ 1 DAI。
- **目标**：抽走990 USDC，留下无价值抵押品，协议承担亏损。

### 攻击步骤
1. **前置交易（操纵Uniswap池）**：
   - 攻击者Alice通过闪电贷借10,000 USDC（成本≈0，同一交易归还）。
   - 提交前置交易，卖10,000 USDC到USDC/DAI池，换DAI。
     - 推高USDC/DAI价格（例如1 USDC换0.01 DAI），使池中USDC过多，DAI稀缺。
   - 使用高gas费用（如100 gwei），确保前置交易在`swapMargin`前执行。

2. **调用`swapMargin`（指定`amountOut=0`）**：
   - Alice调用`swapMargin`：
     - `fromToken = USDC`
     - `toToken = DAI`
     - `amount = 100 USDC`（每次交换100 USDC，便于循环）
     - `amountOut = 0`（接受任何输出）。
   - 因池价格被操纵，100 USDC换1 DAI（极低输出）。
   - Uniswap接受（因`amountOut=0`），协议未验证1 DAI是否支持仓位，抵押品更新为900 USDC + 1 DAI。

3. **提取真实价值**：
   - Alice通过前置交易获得的DAI（约9,900 DAI）在其他池换回USDC，转移100 USDC的真实价值。
   - 协议认为抵押品包含1 DAI，未察觉价值损失。

4. **重复调用**：
   - 重复步骤1-3十次，每次交换100 USDC：
     - 前置交易推高价格，100 USDC换1 DAI。
     - 抵押品从1000 USDC变为900 USDC + 1 DAI，800 USDC + 2 DAI，...，最终10 DAI。
   - Alice抽走990 USDC（99%抵押品）。

5. **触发清算**：
   - 仓位由10 DAI支持，无法覆盖100美元亏损，变得可清算。
   - 清算时，协议无法扣除亏损，LP承担100美元损失。
   - Alice可能用闪电贷触发清算，获10%清算费用（如10美元）。

### 为何成功
- **Uniswap滑点**：`amountOut=0`允许任何输出（包括0 DAI），Uniswap不回滚。
- **协议验证缺失**：未检查`amountOut`是否足以支持仓位（如覆盖-100美元PnL）。
- **前置交易**：高gas确保操纵池价格，100 USDC换1 DAI，真实价值通过其他交易转移。
- **重复调用**：协议未限制调用频率，允许循环抽走抵押品。
- **提取宽松**：允许提取1 DAI或通过前置交易转移价值。

## 3. 攻击后果
- **协议损失**：LP承担100美元亏损（或更多），流动性池被抽干，资不抵债。
- **攻击者获利**：
  - 抽走990 USDC，规避100美元亏损。
  - 可能获清算费用（10美元）。
  - 盈利仓位可正常关闭，亏损仓位通过漏洞规避，形同“无风险”。

## 4. 解决方案
修复需确保交换后抵押品支持仓位，防止前置交易和循环攻击。

1. **验证`amountOut`**：
   - 检查Uniswap返回的`amountOut`价值（通过预言机，如Chainlink）。
   - ```solidity
     function swapMargin(address fromToken, address toToken, uint256 amount) public {
         require(supportedCollateralTokens[fromToken] && supportedCollateralTokens[toToken], "Unsupported token");
         uint256 receivedAmount = uniswapSwap(fromToken, toToken, amount);
         uint256 receivedValue = getTokenValueInUSD(toToken, receivedAmount);
         require(receivedValue >= calculateRequiredCollateral(msg.sender), "Insufficient collateral value");
         collateralBalance[msg.sender] = receivedAmount;
     }
     ```

2. **检查清算状态**：
   - 交换后验证仓位不可清算。
   - ```solidity
     function isPositionLiquidatable(address user) internal view returns (bool) {
         uint256 collateralValue = getTokenValueInUSD(collateralToken[user], collateralBalance[user]);
         uint256 positionLoss = calculatePnL(user);
         uint256 maxLeverage = 20;
         return collateralValue < positionLoss || calculateLeverage(user) > maxLeverage;
     }
     require(!isPositionLiquidatable(msg.sender), "Position becomes liquidatable");
     ```

3. **限制滑点**：
   - 要求`amountOut`≥预言机估算的90%。
   - ```solidity
     require(minAmountOut >= getExpectedAmountOut(fromToken, toToken, amount) * 90 / 100, "Excessive slippage");
     ```

4. **防止前置交易**：
   - 用预言机价格限制滑点，或用Uniswap V3的`exactInput`模式。

5. **限制循环调用**：
   - 添加时间锁（24小时限1次`swapMargin`）或最大交换比例（每次≤10%抵押品）。

6. **防止清算获利**：
   - 禁止同一交易中使仓位可清算并触发清算。

## 5. 类似案例
- **Euler Finance**：通过“捐赠”使仓位可清算，同一交易触发清算获10%费用，类似IVX的`swapMargin`攻击。
- **永续合约**：类似漏洞可能出现在允许交换抵押品的函数，需验证`amountOut`和清算状态。

## 6. 总结
攻击者通过前置交易操纵Uniswap池价格，使`swapMargin`的100 USDC换1 DAI（因`amountOut=0`），循环10次抽走990 USDC，留下10 DAI无法覆盖100美元亏损。协议未验证`amountOut`和清算状态，导致LP损失。修复需验证金额、限制滑点、检查清算状态和防止循环攻击。

# 2025-08-08

# 去中心化永久合约：任务一设计与实现要点

以下是基于Web3安全审计课程（2025年8月7日）中任务一的笔记，聚焦于去中心化永久合约的设计、功能要求和实现要点。内容以中文整理，结合Solidity和审计视角，总结核心概念、例子和建议。

---

## 任务概述
- **目标**：构建一个简洁的去中心化永久合约协议（~200行代码），实现基础功能，培养设计和安全意识。
- **交付物**：
  - GitHub仓库：包含代码、团队成员权限、提交历史。
  - 功能：流动性提供、实时价格获取、开仓/加仓/加抵押、限制利用率。
  - README：描述系统设计、用户交互、角色职责、已知风险、数学公式。
- **截止日期**：2025年2月25日，下午1点（可调整）。
- **重要性**：模拟真实DeFi协议开发，学习权衡设计决策，识别“代码气味”（code smell）。

---

## 永久合约核心概念
- **定义**：金融衍生品，允许交易者用杠杆押注资产价格（无需持有资产）。
  - **例子**：用50 USDC抵押，借50 USDC开100 USDC BTC多头仓位（2倍杠杆）。
- **角色**：
  - **流动性提供者（LP）**：存入资金（如USDC）到金库，赚取费用，承担交易者盈亏风险。
  - **交易者（Trader）**：用抵押开仓，赚取/损失价格变动（PNL）。
  - **守护者（Keeper）/管理员**：执行清算、价格更新（可选）。
- **机制**：
  - **杠杆**：抵押放大仓位。
  - **PNL**：交易者盈利，LP亏；交易者亏，LP赚。
  - **金库（Vault）**：管理LP存款和交易者仓位，类似ERC-4626标准。
- **公式**：LP价值 = 总存款 - 交易者总PNL。

---

## 功能要求与实现要点
1. **流动性提供者存入/提取流动性**
   - **要求**：LP存入USDC到金库，获取vault tokens；提取时确保不影响交易者仓位。
   - **实现**：
     - 继承OpenZeppelin的ERC-4626，处理存款/铸造逻辑。
     - 存款：LP存USDC，铸造等值vault tokens（初始1:1）。
     - 提取：验证剩余流动性满足交易者需求（利用率<阈值）。
   - **安全点**：
     - 防止首次存款者攻击：初始1:1比例，覆盖`totalAssets()`返回`存款-PNL`。
     - 验证提取：LP不能提取分配给交易者的资金。
   - **例子**：LP存100 USDC得100 tokens，交易者赚10 USDC，LP价值降为90，新存款100 USDC得111 tokens（100/90 * 100）。

 Ascending: **例子**：LP存100 USDC得100 tokens，交易者赚10 USDC，LP价值降为90，新存款100 USDC得111 tokens（100/90 * 100）。
   - **审计检查**：权限控制（`onlyOwner`）、提取逻辑、首次存款者攻击防护。

2. **实时资产价格获取**
   - **要求**：从链上预言机（如Chainlink）获取资产价格（如BTC/USD）。
   - **实现**：集成Chainlink Price Feed，调用`latestRoundData()`。
   - **安全点**：
     - 验证价格新鲜度（`updatedAt`）和合理性。
     - 用`nonReentrant`防止重入。
   - **审计检查**：预言机数据可靠性，调用安全性。

3. **交易者开仓/加仓/加抵押**
   - **要求**：
     - 开仓：指定仓位大小和抵押（如50 USDC开100 USDC多头）。
     - 加仓：增加仓位大小。
     - 加抵押：增加抵押，降低杠杆率。
   - **实现**：
     - 用结构体跟踪交易者仓位（抵押、仓位大小、方向）。
     - 更新金库总开仓（open interest）和PNL。
   - **安全点**：
     - 用`require`检查最小抵押/大小，防止零值。
     - 用SafeMath或Solidity 0.8+防溢出。
   - **审计检查**：输入验证、溢出防护、PNL更新逻辑。

4. **限制利用率（Utilization Percentage）**
   - **要求**：LP存款不能100%分配给交易者，需保留部分流动性（如80%阈值）。
   - **实现**：开仓/提取时，检查`总开仓 / LP存款 < 阈值`，否则revert。
   - **安全点**：防止LP提取导致金库破产。
   - **审计检查**：利用率逻辑，极端场景（高开仓、低流动性）。

5. **README**
   - **内容**：系统设计、用户交互（LP/交易者流程）、角色职责（LP、交易者、守护者）、已知风险、公式（如PNL=仓位大小*价格变动）。
   - **重要性**：清晰文档加速审计，减少误解。
   - **建议**：用PlantUML画流程图，列出风险（如预言机操纵）。

---

## 设计与安全建议
- **设计**：
  - **精简代码**：目标200行，用OpenZeppelin的ERC4626、SafeMath。
  - **模块化**：分离金库（Vault）、仓位管理（PositionManager）、价格获取（PriceFeed）。
  - **安全**：
    - 用`nonReentrant`防止重入。
    - 检查输入（`require(amount > 0)`）。
    - 用事件记录存款/提取/开仓，方便调试。
- **测试**：
  - **单元测试**：覆盖每个函数（如`openPosition

# 2025-08-06

# 今日学习笔记：Web3智能合约测试与模糊测试

**日期**：2025年8月6日  
**主题**：基于transcript的Web3安全审计课程，聚焦智能合约测试，特别是集成测试和模糊测试（fuzz testing），以Pool Sharks协议为例。

---

## 1. 核心概念：智能合约测试的重要性
- **为什么重要**：Web3中，bug可能导致数亿资产损失（如误发交易到零地址）。测试比Web2更关键，是防止漏洞的第一道防线。
- **测试类型**：
  - **单元测试**：测试单一函数（如算术逻辑），早期捕获bug，易调试。
  - **集成测试**：验证用户交互流程和跨合约逻辑，捕获复杂bug（如状态不一致）。
  - **模糊测试**：用随机输入覆盖边缘场景，高效发现关键漏洞。
- **代码覆盖率局限性**：
  - 100%覆盖率 ≠ 无漏洞。示例：票务合约覆盖100%，但因缺少`onlyOwner`修饰符，任何人可提取资金。
  - 需测试路径依赖场景（path-dependent），如用户操作顺序、价格波动。

---

## 2. Pool Sharks协议简介
- **功能**：一个在Fuel Network上的自动化做市商（AMM），支持单向流动性（Directional Liquidity）和价格-时间优先级队列，优化交易效率，减少无常损失。
  - **单向流动性**：仅在价格上升时执行交易（如买入ETH），下降时订单保留（不取消）。
  - **场景**：代币交换、期权交易、网格交易。
- **测试重点**：
  - 验证“全局流动性不溢出”（`totalLiquidity >= 0`）。
  - 测试路径依赖场景（如价格上升执行、下降不取消）。

---

## 3. 集成测试与路径依赖
- **定义**：集成测试模拟用户交互和跨合约逻辑，验证协议整体行为。
- **路径依赖**：
  - **路径无关**：如ERC20转账，顺序不影响结果。
  - **路径依赖**：如AMM中，Alice先买推高价格，Bob后买得更少代币；反序结果不同。
  - Pool Sharks例子：价格上升时执行买入，下降时保留订单，需测试不同顺序（如Alice先提供流动性 vs Bob先交易）。
- **设计方法**：
  - 模拟多用户（用`vm.prank`切换用户）。
  - 测试不同顺序和边缘场景（如零输入、最大杠杆）。
  - 用`assert`验证状态（如`totalLiquidity`）。
- **Solidity示例**：
  ```solidity
  function testPathDependentLiquidity() public {
      vm.prank(alice);
      pool.provideLiquidity(alice, 100, 1900);
      assertEq(pool.totalLiquidity(), 100);

      vm.prank(bob);
      pool.executeTrade(bob, 50, 2500); // 价格上升
      assertEq(pool.totalLiquidity(), 50);
  }
  ```

---

## 4. 模糊测试与Forge Test
- **模糊测试**：用随机输入（如`uint256 amount`）测试边缘场景，高效发现漏洞（如溢出）。
- **Pool Sharks测试优秀之处**：
  - **路径依赖测试**：覆盖价格上升/下降场景，验证单向流动性逻辑。
  - **不变性验证**：用Echidna或Forge检查`totalLiquidity >= 0`，300次随机输入（`fuzz.runs = 300`）。
  - **自动化反例**：失败时自动保存调用序列，方便修复。
- **Forge Test行为**：
  - 命令：`forge test --match-contract CalcTest -Vv`
    - `--match-contract CalcTest`：只运行`CalcTest`合约。
    - `-Vv`：详细日志，显示模糊测试输入和反例。
    - `fuzz.runs = 300`（在`foundry.toml`）自动触发模糊测试，运行300次随机输入。
  - 函数需接受随机参数（如`test_Fuzz_Liquidity(uint256 amount)`）。
  - 用`vm.assume`限制输入范围（如`amount <= type(uint256).max`）。
- **Solidity示例**：
  ```solidity
  function test_Fuzz_Liquidity(uint256 amount, uint256 minPrice) public {
      vm.assume(amount > 0 && amount <= type(uint256).max - pool.totalLiquidity());
      vm.assume(minPrice <= pool.currentPrice());
      pool.provideLiquidity(address(this), amount, minPrice);
      assertGe(pool.totalLiquidity(), amount);
  }
  ```

---

## 5. 审计视角
- **检查点**：
  - 测试套件是否覆盖路径依赖场景（如多用户顺序、价格波动）。
  - 模糊测试是否用`vm.assume`限制输入，验证不变性（如流动性不溢出）。
  - 日志（`-Vv`）是否输出清晰反例，方便调试。
- **建议**：
  - 运行`forge coverage`检查覆盖率，关注未测分支。
  - 验证`foundry.toml`的`fuzz.runs`（如300次）是否足够。
  - 模拟攻击场景（如sandwich攻击）测试协议鲁棒性。

---

## 6. 总结
今日学习了Web3智能合约测试的核心：单元测试打基础，集成测试验证用户流程，模糊测试覆盖边缘场景。Pool Sharks的测试通过路径依赖（价格上升执行、下降不取消）和不变性（流动性不溢出）高效发现漏洞。`forge test`结合`fuzz.runs = 300`自动运行模糊测试，`-Vv`提供详细调试信息。理解这些对Web3安全审计至关重要。

**后续计划**：
- 练习用Foundry写模糊测试，模拟Pool Sharks场景。
- 学习Echidna配置，结合Forge运行不变性测试。
- 审计时重点检查路径依赖和反例生成。

# 2025-08-05

# Resupply Protocol 攻击事件深度分析

## 1. 协议简介

**Resupply Protocol (ResupplyFi)** 是一个去中心化稳定币协议，旨在为用户提供稳定币资产的再融资和收益最大化策略。其核心机制基于**抵押债仓（CDP）**，允许用户通过抵押稳定币资产，如 `crvUSD` 和 `frxUSD`，来铸造其原生稳定币 **reUSD**。

Resupply 协议的运作依赖于外部 DeFi 生态，特别是 **Curve LlamaLend**。其借贷市场（`Market`）的抵押品不是直接的稳定币，而是 **ERC-4626 Vault** 发行的份额代币，例如 `cvcrvUSD`。

## 2. 攻击核心漏洞

本次攻击的核心在于 Resupply 协议的**价格预言机（Price Oracle）**和**健康检查机制**存在严重缺陷。攻击者利用了一个多环节的逻辑漏洞链，包括：

1.  **ERC-4626 Vault 价格操纵**：利用新创建的、流动性极低的 Vault，通过“捐赠攻击”人为地夸大其每股价格。
2.  **整数除法向下舍入**：Resupply Market 用来计算借贷健康系数的 `_exchangeRate` 公式存在缺陷，即 `_exchangeRate = 10^36 / price`。当被操纵的 `price` 大于 $10^{36}$ 时，Solidity 的整数除法会使 `_exchangeRate` 结果为 `0`。
3.  **健康检查失效**：`_exchangeRate = 0` 使得协议的健康检查 (`_isSolvent` ) 逻辑被绕过，允许借款人借出无抵押的资金。

## 3. 攻击复盘：五个核心步骤

攻击者通过一笔闪电贷（Flash Loan）完成了所有关键步骤，确保了攻击的原子性和成功：

1.  **准备资金**：通过闪电贷借入 4,000 USDC 并兑换为 3,999 `crvUSD`。

2.  **执行捐赠攻击**：将 **2,000 `crvUSD`** 捐赠给 `Controller 0x8970`（`Vault 0x0114` 的底层资产存储合约）。这使得 `Vault` 的 **`total_assets`** 激增，但 **`totalSupply`** 仍然极小，造成了资产与份额的严重不平衡。

3.  **铸造被操纵的抵押品**：将剩余的 **2 `crvUSD`** 存入 `Vault 0x0114`。由于其每股价格被操纵得极高，攻击者只获得了极少量的 `cvcrvUSD` 份额代币（即 1 unit）。

4.  **提供抵押品**：将这 `1 unit` 的 `cvcrvUSD` 抵押到 `Market 0x6e90`。此时，`Market` 错误地将这极少量的抵押品估值为天价。

5.  **借出巨额资金**：从 `Market 0x6e90` 借出 **10,000,000 reUSD**。在借款前的健康检查中，被操纵的 `cvcrvUSD` 价格导致 `_exchangeRate` 被计算为 `0`，从而绕过了安全检查。

## 4. 攻击结果与影响

* **巨额损失**：Resupply Protocol 遭受了约 **960 万美元**的资金损失。
* **资金洗白**：被盗资金通过去中心化交易所兑换后，利用 **Tornado Cash** 等隐私协议进行了混淆。
* **行业警示**：该事件再次凸显了 DeFi 协议在**价格预言机、整数计算、新市场部署和合约审计**方面存在的潜在风险。特别是对于依赖复杂数学逻辑和外部合约交互的协议，必须进行更严格的审查和测试。

## 5. 技术总结

本次攻击的核心在于一个新部署的 ERC-4626 Vault 在其初始化阶段容易受到价格操纵。攻击者通过捐赠行为使得 `total_assets` 远大于 `totalSupply`，从而将每股价格抬高到超出合约处理能力的范围。最终，一个简单的整数除法错误成为了突破口，导致协议的借贷安全机制完全失效，造成了重大损失。

# 2025-08-04

# Web3 安全审计笔记：智能合约设计最佳实践

以下是基于课程transcript整理的Web3安全审计核心要点，聚焦智能合约设计原则和外部调用风险，旨在帮助开发者构建安全合约并指导审计实践。内容涵盖减少代码量、避免循环、限制输入、处理所有情况、避免平行数据结构，以及外部调用中的重入、DOS、返回值和gas风险。

1\. 智能合约设计核心原则

### 1.1 减少代码量（Less Code is Better）

- **原则**：代码越少，bug越少。研究表明，每1000行代码约17个bug。智能合约中，bug可能是严重漏洞（如资金窃取）。
  - 例：GMX V2（10,000+行）仍有50+ critical和30+ high漏洞。
  - 关系：bug数量与代码行数呈抛物线增长。
- **实现**：
  - **精简存储变量**：避免冗余存储。
    - 例：永久合约中，`orderToPositionId`映射重复订单结构体中的位置ID，导致不同步风险。解决：嵌入ID，省50-60行代码，降低审计成本（按行收费）。
  - **链外逻辑**：不必要计算（如流动性百分比转换）移到前端。
    - 例：集中流动性协议中，链上转换增加gas和bug风险（&gt;100%输入）。移除省50-60行。
- **审计建议**：检查存储变量冗余，搜索`mapping`和`struct`，确保单一数据源。

### 1.2 避免循环（Avoid Loops）

- **原则**：循环易导致DOS攻击或gas超限（EVM块gas上限）。
  - 例：不要循环计算总利息，用聚合变量（如总开仓利息）代替。
- **例外**：必要循环（如交换路径）需限长度，确保gas恒定。
- **审计建议**：搜索`for`关键字，检查是否可用累加器优化。

### 1.3 限制预期输入（Limit Expected Inputs）

- **原则**：禁止无意义/恶意输入，用`require`早切断。
  - 例：GMX允许零大小/零抵押位置，导致rounding操纵。解决：加最小大小阈值。
- **权衡**：多`require`安全但gas贵，可用`modifier`或custom errors优化。
- **审计建议**：检查`require`覆盖协议特定输入（如无重复市场）。

### 1.4 处理所有情况（Handle All Cases）

- **原则**：别假设理想情况（如USDC=1美元，keeper及时清算）。处理极端场景（如破产清算：损失&gt;抵押）。
  - 例：抵押$1000，损失$1100，不处理导致underflow。解决：限损失为抵押额。
- **审计建议**：模拟价格闪崩、keeper失败场景。

### 1.5 避免平行数据结构（Avoid Parallel Data Structures）

- **原则**：别用多结构（如mapping+array）跟踪同一数据，易不同步。
  - 例：Minimal Finance中，声明者映射+数组未更新索引，锁定资金。
- **替代**：用OpenZeppelin的`EnumerableMap`支持迭代。
- **审计建议**：检查多结构跟踪同一数据，搜索`mapping`和`array`。

## 2. 外部调用风险与防护

### 2.1 重入攻击（Reentrancy）

- **风险**：外部调用可能被攻击者重入，窃取资金。
  - 例：跨合约重入（系统多合约）或只读重入（操纵Curve池价格）。
- **防护**：
  - 用“检查-效果-交互”（CEI）模式：先检查、更新状态、最后交互。
  - 用OpenZeppelin的`ReentrancyGuard`（`nonReentrant` modifier）。
  - GMX V2用全局nonReentrant，跨合约保护。
- **审计建议**：检查所有`call`，确保CEI顺序和`nonReentrant`。

### 2.2 DOS攻击（Denial of Service）

- **风险**：外部调用失败（如恶意回调、不接受ETH的合约）导致关键功能瘫痪。
- **防护**：用`try-catch`捕获失败，只发事件，不revert主逻辑。
- **审计建议**：模拟调用失败，检查主逻辑是否继续。

### 2.3 返回值处理（Return Values）

- **风险**：不可信合约返回任意字节，可能导致解析失败或逻辑操纵。
  - 例：GMX V2解析错误数据（`bytes memory errorData`）可能revert。
- **防护**：用低级调用（`assembly`）设返回值大小为0，避免解析。
- **审计建议**：检查`bytes memory`，确保不解析不可信数据。

### 2.4 Gas消耗（Gas Considerations）

- **风险**：
  - 返回值加载到内存：EVM内存分配成本随数据大小二次增长，可能OOM。
    - 例：攻击者返回1MB数据，耗尽gas，revert交易。
  - 转发所有gas（`gasleft()`）：不可信合约可耗尽gas，DOS或增加keeper成本。
  - 例：GMX V2回调若不限gas，可能延迟市场订单。
- **防护**：
  - **指定gas限额**：用`call{gas: X}`，X为预期量。
  - **低级调用**：用`assembly`设返回值大小为0。
    - 例：

      ```solidity
      function safeCall(address recipient, uint256 amount) external {
          bool success;
          assembly {
              success := call(100000, recipient, amount, 0, 0, 0, 0)
          }
          if (!success) emit CallFailed(recipient);
      }
      ```
  - **Try-Catch**：捕获失败，保护主逻辑。
  - **区分上下文**：用户调用自担gas，keeper调用需严格限。
- **审计建议**：
  - 搜索`call`/`gasleft()`，检查gas限额。
  - 用Foundry模拟高gas场景（如1MB返回数据）。
  - 验证keeper路径的gas经济性。

### 2.5 邮寄检查（Post-Checks）

- **原则**：函数结束时验证不变性（如余额&gt;=预期），防止意外资金流出。
  - 例：GMX V2检查市场代币余额，但未更新抵押额导致正常行为revert。
- **防护**：确保检查覆盖所有场景，不引入DOS。
- **审计建议**：检查`assert`/`require`，模拟未更新状态场景。

## 3. 文档建议

- **优秀README**：包含协议摘要、组件描述、交互流程图、执行示例、预言机细节。
  - 例：Blueberry Bank的README有用户执行流程图，助审计和团队协作。
- **建议**：用PlantUML画流程图，审计时先读README。

## 4. GMX V2案例分析

- **背景**：GMX V2允许用户指定任意回调合约（`callbackContract`），用于第三方集成（如Dolomite）。
- **安全实现**：
  - **顺序**：回调在订单逻辑后（CEI）。
  - **重入防护**：全局nonReentrant，跨合约保护（订单/存款/提款处理器共享数据存储）。
  - **DOS**：try-catch捕获失败，只发事件。
  - **返回值**：不解析不可信数据，用低级调用设大小为0。
  - **Gas**：限gas转发，保护keeper。
- **审计**：花6个月验证回调后逻辑不可游戏化（non-gameable）。
- **学习建议**：阅读GMX V2的`OrderUtils.sol`，分析回调逻辑。

## 5. 审计实践清单

- **减少代码量**：检查存储变量冗余，搜索`mapping`/`struct`。
- **循环**：搜索`for`，验证是否可用累加器。
- **输入**：检查`require`覆盖协议特定输入。
- **所有情况**：模拟价格闪崩、keeper失败。
- **平行结构**：检查多结构跟踪同一数据。
- **外部调用**：
  - 搜索`call`/`delegatecall`，确保CEI和`nonReentrant`。
  - 检查`bytes memory`，避免解析不可信数据。
  - 验证gas限额，模拟高gas场景。
  - 检查`try-catch`，确保失败不影响主逻辑。
- **邮寄检查**：验证`assert`/`require`覆盖所有场景。
- **工具**：用Foundry/Hardhat测试，阅读EVM黄皮书了解`CALL` opcode。

## 6. 学习资源与建议

- **实践**：审计GMX V2或Uniswap V3代码，重点检查回调和gas。
- **工具**：Foundry模拟攻击场景，PlantUML画流程图。
- **深入**：学习EVM内存模型和gas成本（黄皮书）。
- **代码示例**（测试gas攻击）：

  ```solidity
  contract MaliciousCallback {
      function malicious() external {
          uint256[] memory data = new uint256[](1000000);
          for (uint256 i = 0; i < data.length; i++) {
              data[i] = i;
          }
      }
  }
  contract Test {
      event CallFailed(address);
      function testCallback(address callback) external {
          (bool success, ) = callback.call{gas: 100_000}(abi.encodeWithSignature("malicious()"));
          if (!success) emit CallFailed(callback);
      }
  }
  ```


# 2025.07.29


<!-- Content_END -->
