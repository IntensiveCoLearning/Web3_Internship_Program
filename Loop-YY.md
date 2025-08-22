---
timezone: UTC+8
---

# 于海威

**GitHub ID:** Loop-YY

**Telegram:** @loop_Y

## Self-introduction

Web2从业者，转型Web3中

## Notes

<!-- Content_START -->
# 2025-08-22

# 《以太坊机制详解：交易与交易池》学习笔记

## 一、前言
本文详细介绍了以太坊交易的生命周期，从交易池的初始化到交易的构造、打包，再到交易池的重构。通过对交易池的深入分析，我们可以更好地理解以太坊交易的处理机制。

## 二、交易池初始化
交易池初始化是节点启动时的重要步骤，主要涉及以下参数的配置：
- **Locals**：本地账户地址列表，本地交易享有特权。
- **NoLocals**：是否禁用本地交易处理。
- **Journal**：本地交易的持久化文件。
- **Rejournal**：数据持久化的时间间隔。
- **PriceLimit**：交易池接受的最小 `gas` 价格。
- **PriceBump**：替换交易所需的最小 `gas` 价格提升百分比。
- **AccountSlots** 和 **GlobalSlots**：单个账户和全局可执行交易的最大容量。
- **AccountQueue** 和 **GlobalQueue**：单个账户和全局非可执行交易的最大容量。
- **Lifetime**：非可执行交易的存活时间。

交易池将交易分为两类：
- **本地交易**（`local transactions`）：享有优先级，不受 `PriceLimit` 等限制。
- **远程交易**（`remote transactions`）：分为 `pending` 队列（可能被打包）和 `queued` 队列（不太可能被打包）。

## 三、交易构造
交易构造是客户端通过 API 构建交易的过程。以 `eth_sendTransaction` 为例，交易参数包括：
- **type**：交易类型，如 `0x02` 表示 `EIP1559` 类型交易。
- **nonce**：用户的 `nonce` 值，每完成一笔交易增加 1。
- **to** 和 **from**：目标和源地址。
- **gas**：`gas limit`。
- **value**：转账的 `ETH` 数量（单位为 `wei`）。
- **input**：合约运行数据。
- **maxPriorityFeePerGas** 和 **maxFeePerGas**：`EIP1559` 交易的费用参数。
- **chainID**：链 ID。

使用 MetaMask 构建交易时，可以通过 `ethereum.request` 方法发送交易参数。需要注意的是，RPC 服务商通常不支持 `eth_sendTransaction`，而是支持 `eth_sendRawTransaction`，需要提交已签名的交易。

## 四、交易池添加交易
当交易通过 API 发送到节点时，交易池会进行以下处理：
1. **检查交易是否已存在**：如果交易哈希值已存在，则丢弃。
2. **判断是否为本地交易**：本地交易享有更高优先级。
3. **验证交易**：包括 `RLP` 编码体积、`Gas Limit`、费用限制、签名正确性等。
4. **交易分类**：根据交易池容量和交易类型，交易可能进入 `pending` 或 `queued` 队列。
5. **替换交易**：如果交易的 `gas` 价格足够高，可以替换 `pending` 队列中的旧交易。

## 五、交易重排
交易重排由 `scheduleReorgLoop` 函数调度，核心是 `runReorg` 函数。`runReorg` 的主要功能包括：
1. **处理新区块到来**：更新交易池状态，包括删除已打包的交易、提权符合条件的 `queued` 交易。
2. **处理交易提权**：将 `queued` 队列中的交易提升到 `pending` 队列。
3. **广播交易**：将新交易或替换交易广播到网络中。

`runReorg` 使用了多个 `channel` 来处理并发问题，确保交易的正确处理和同步。

## 六、区块打包
区块打包由 `miner/worker.go` 中的 `fillTransactions` 函数完成。主要步骤包括：
1. **交易分类**：将交易分为本地交易和远程交易。
2. **交易排序**：根据 `minerFee`（`maxPriorityFeePerGas` 或 `maxFeePerGas - baseFee`）对交易进行排序。
3. **交易执行**：通过 `commitTransaction` 执行交易，记录日志。
4. **区块密封**：由共识引擎完成区块的最终封装。

## 七、交易池重构
当新区块到达时，交易池需要重构：
1. **处理新区块**：通过 `reset` 函数处理新区块和旧区块的差异，更新交易池状态。
2. **删除过时交易**：删除已打包或不符合新区块要求的交易。
3. **提权交易**：将 `queued` 队列中的交易提升到 `pending` 队列。
4. **更新 `nonce`**：根据新区块状态更新交易池内的 `nonce`。

# 2025-08-21

# MetaMask 一键登录设计学习笔记

## 学习目标
- 掌握基于 MetaMask 的去中心化登录系统的实现原理和流程。
- 学会使用 Vue 和 CloudFlare Worker 实现前端和后端的相关功能。

## 一、登录流程概述
1. **前端获取用户以太坊账户地址**：通过 MetaMask 的 `eth_requestAccounts` API 获取用户的以太坊账户地址。
2. **后端生成签名内容**：后端生成一个随机的 `nonces` 值，并将其与用户的以太坊地址存储在 CloudFlare KV 中。
3. **前端请求签名内容**：前端向后端发送 `PUT` 请求，获取 `nonces`。
4. **用户签名**：前端使用 MetaMask 的 `signTypedData_v4` API，让用户对包含 `nonces` 的数据进行签名。
5. **后端验证签名**：前端将签名结果和以太坊地址发送到后端，后端使用 `recoverTypedSignature` 验证签名是否与地址匹配。
6. **生成 JWT 凭证**：如果验证通过，后端生成 JWT 作为登录凭证并返回给前端。

## 二、前端实现

### （一）检测 MetaMask 支持
- 通过 `window.ethereum.isMetaMask` 检测用户是否安装了 MetaMask 插件。
- 如果未安装，则不显示登录按钮。

### （二）获取以太坊账户地址
- 使用 `window.ethereum.request` 方法获取用户的以太坊账户地址。
- 示例代码：
  ```javascript
  window.ethereum.request({ method: 'eth_requestAccounts' }).then(
      accounts => {
          this.ethAccount = accounts[0]
          console.log(this.ethAccount);
      }
  )
  ```

### （三）请求 `nonces`
- 通过 `axios.put` 向后端发送请求，获取 `nonces`。
- 示例代码：
  ```javascript
  axios.put("http://localhost:8787", { "from": this.ethAccount }).then(res => {
      this.nonces = res.data.nonces;
  }).then(() => {
      console.log(this.nonces);
  })
  ```

### （四）签名数据结构
- 定义了符合 EIP-712 标准的签名数据结构，包括 `domain`、`message`、`primaryType` 和 `types`。
- 示例代码：
  ```javascript
  const msgParams = {
      domain: {
          chainId: window.ethereum.chainId,
          name: 'Login',
          version: '1'
      },
      message: {
          contents: 'Login',
          nonces: this.nonces,
      },
      primaryType: 'Login',
      types: {
          EIP712Domain: [
              { name: 'name', type: 'string' },
              { name: 'version', type: 'string' },
              { name: 'chainId', type: 'uint256' },
          ],
          Login: [
              { name: 'contents', type: 'string' },
              { name: 'nonces', type: 'uint256' },
          ],
      },
  };
  ```

### （五）签名操作
- 调用 `ethereum.request` 方法，使用 `eth_signTypedData_v4` 对数据进行签名。
- 示例代码：
  ```javascript
  ethereum.request({
      method: 'eth_signTypedData_v4',
      params: [from, JSON.stringify(msgParams)],
  }).then((res) => {
      this.sign = res;
      console.log(res);
  })
  ```

### （六）回传签名结果
- 将签名结果、以太坊地址和链 ID 通过 `axios.post` 发送到后端。
- 示例代码：
  ```javascript
  axios.post("http://localhost:8787", {
      "chainId": window.ethereum.chainId,
      "from": this.ethAccount,
      "signature": this.sign
  }).then(
      res => {
          if (res.data.verify) {
              localStorage.setItem("token", res.data.token);
              localStorage.setItem("expire", Date.now() + 3600000);
              localStorage.setItem("userName", this.ethAccount);
              console.log("登录成功");
          } else {
              console.log("登录失败，请重新登录");
          }
      }
  )
  ```

## 三、后端实现

### （一）环境搭建
- 使用 CloudFlare Wrangler 创建开发环境，并绑定 KV 数据库。
- 示例代码：
  ```bash
  wrangler kv:namespace create web3login
  ```

### （二）生成 `nonces`
- 后端接收前端发送的以太坊地址，生成随机 `nonces` 并存储在 KV 中。
- 示例代码：
  ```javascript
  let nonces = Math.floor(Math.random() * 1000000)
  await loginKV.put(key, nonces, { expirationTtl: 120 })
  ```

### （三）验证签名
- 使用 `recoverTypedSignature` 函数验证签名是否与用户地址匹配。
- 示例代码：
  ```javascript
  const recoveredAddr = recoverTypedSignature({
      data: msgParams,
      signature: signature,
      version: version,
  });
  ```

### （四）生成 JWT
- 如果验证通过，使用 `jose` 库生成 JWT 作为登录凭证。
- 示例代码：
  ```javascript
  const tokenLogin = await new SignJWT({ "user_id": from })
      .setProtectedHeader({ alg: 'HS256', typ: 'JWT' })
      .setExpirationTime('1h')
      .sign(Buffer.from(SECRET_KEY, "utf8"));
  ```

## 四、总结
通过上述学习，我掌握了基于 MetaMask 的去中心化登录系统的实现原理和流程。前端使用 Vue 框架实现用户交互，后端使用 CloudFlare Worker 和 KV 服务实现签名内容的生成和验证。整个系统利用以太坊的非对称加密特性，通过用户对特定数据的签名来验证身份，并生成 JWT 作为登录凭证。这种去中心化的登录方式更加安全且符合 Web3 的理念。

# 2025-08-18

---

### 基于链下链上双视角深入解析以太坊签名与验证

| **部分** | **内容总结** | **关键点** | **技术细节** |
|----------|--------------|------------|--------------|
| **概述** | 介绍以太坊签名机制，包括ECDSA公钥密码学、交易签名流程和链上签名验证。 | 重点解析ECDSA数学原理、以太坊交易签名流程及EIP-712、EIP-1271标准。 | 使用noble-secp256k1库作为案例，结合代码分析签名与验证流程。 |
| **ECDSA公钥密码学** | 讲解secp256k1椭圆曲线的公钥生成、签名与验证原理。 | 私钥通过点倍增生成公钥，签名生成r、s值，验证恢复公钥。 | 提供生成点G的坐标、noble-secp256k1代码片段，强调陷门函数的不可逆性。 |
| **公钥生成** | 描述以太坊和比特币使用的secp256k1曲线，私钥d通过点倍增生成公钥P，地址为公钥后20字节的keccak-256哈希。 | 点倍增操作直观不可逆，代码优化提高效率。 | 提供G点的具体数值和noble-secp256k1实现代码。 |
| **签名** | 详述ECDSA签名流程，生成r、s值，结合以太坊特定前缀进行哈希计算。 | 以太坊签名格式为[R][S][V]，V值包含chainID防止重放攻击（EIP-155）。 | 代码实现包括随机数k、点R计算及模n操作，展示签名格式。 |
| **验证签名** | 描述通过签名恢复公钥的流程，验证签名有效性。 | 使用ecrecover恢复公钥，结合v、r、s和哈希值计算。 | 提供验证代码，强调优化函数的使用和安全性考虑。 |
| **以太坊交易签名** | 分析go-ethereum中交易签名的实现，基于EIP-1559、EIP-2930等标准。 | Signer接口适配不同升级阶段，londonSigner支持最新签名标准。 | 展示交易哈希计算（prefixedRlpHash）和签名编码流程。 |
| **链上签名验证** | 介绍使用ecrecover预编译指令验证签名，强调安全性。 | ecrecover高效且gas消耗低，适用于标准secp256k1签名验证。 | 提供基本代码示例，推荐使用EIP-712和personal_sign。 |
| **EIP-712** | 详解EIP-712结构化数据签名标准，解决跨DApp签名盗用问题。 | 引入domainSeparator和typeHash，确保签名唯一性。 | 提供Mail结构体示例、编码规则和MetaMask signTypedData_v4实现。 |
| **验证（EIP-712）** | 展示链上验证EIP-712签名的流程，重建签名并验证。 | 使用abi.encodePacked拼接\x19\x01和domainSeparator，调用ecrecover验证。 | 提供Solidity代码示例，强调abi.encode与encodePacked的区别。 |
| **例子** | 使用OpenZeppelin库实现EIP-712签名与验证，简化开发流程。 | 提供完整合约代码和测试流程，增强安全性。 | 展示IProduct.sol接口、ProductEIP712合约及测试用例。 |
| **EIP-1271** | 介绍智能合约签名标准，允许合约验证签名。 | 合约通过isValidSignature方法检查签名，授权用户代表合约签名。 | 提供isValidSignature实现代码，引用GnosisSafe示例。 |
| **总结与拓展阅读** | 总结ECDSA、EIP-712、EIP-1271的核心内容，推荐相关资源。 | 强调签名机制在以太坊中的重要性，提供进一步学习资料。 | 列出比特币P2TR、Cloudflare椭圆曲线博客等参考资源。 |

---

### 以太坊机制详解: Gas Price计算

| **部分** | **内容总结** | **关键点** | **技术细节** |
|----------|--------------|------------|--------------|
| **概述** | 介绍以太坊London升级后EIP-1559引入的新gas计算机制。 | 分析gas price计算方式及交易手续费的构成。 | 强调EIP-1559对gas机制的改进，涉及Base Fee和燃烧机制。 |
| **概念辨析** | 区分gas、gas price和transaction fee，介绍gas作为操作基准单位。 | 交易手续费 = Gas * Gas Price，gas limit不足会导致交易失败。 | 提供操作码gas消耗表、ETH转账21,000 gas标准及失败案例。 |
| **Gas Limit获取** | 介绍通过eth_estimateGas RPC API估算交易gas消耗。 | 提供转账和合约操作的gas估算示例，结果与实际一致。 | 使用Cloudflare ETH网关，展示WETH deposit()操作的gas估算（45038）。 |
| **Gas Price计算** | 详述EIP-1559下gas price的计算，包含Base Fee、Max Priority Fee和Max Fee。 | Base Fee动态调整网络流量，Max Priority Fee影响打包优先级。 | 提供go-ethereum中CalcBaseFee函数源码，分析参数与公式。 |
| **Base Fee** | 描述Base Fee的计算逻辑，基于上一区块的gas使用量动态调整。 | Gas使用量等于目标值时Base Fee不变，大于/小于时增减12.5%。 | 提供计算示例（6.467725 Gwei），验证Etherscan数据。 |
| **Max Priority Fee** | 说明Max Priority Fee由用户设定，直接支付给矿工，影响交易优先级。 | 数值越高，交易打包速度越快，可通过mempool数据估算。 | 引用BlockNative和Etherscan GasTracker，提供MetaMask设置示例。 |
| **Max Fee** | 解释Max Fee作为交易最大gas price，防止因Base Fee上涨导致交易失败。 | Max Fee = (2 * Base Fee) + Max Priority Fee，确保交易在未来区块有效。 | 提供Binance高Max Fee示例，分析连续满区块场景。 |
| **总结** | 总结EIP-1559下gas机制，强调Base Fee的动态调整和燃烧机制。 | 提供gas、gas price、transaction fee的综合理解。 | 附带流程图，推荐进一步阅读交易与交易池相关内容。 |

---

# 2025-08-17

# 智能合约开发

| 方法名称 | 提出者/来源 | 基本原理 | 优缺点 | 实现要点 | 测试与部署 |
|----------|------------|----------|--------|----------|------------|
| **Eternal Storage** | OpenZeppelin（最早博客思想） | 将数据存储与逻辑分离；升级时新合约指向存储合约，避免数据丢失；不实现真正升级，仅数据持久。 | **优点**：简单易懂；无需附加库；部署方便。<br>**缺点**：多合约并行，无法阻止旧合约访问；地址变化需前端更新；无法增加存储变量，升级有限。 | 存储合约：用mapping存储数据，提供get/set接口。<br>逻辑合约：通过call调用存储合约；升级时部署新逻辑合约指向同一存储。<br>使用keccak256生成键。 | 测试：Forge test验证get/set和跨合约交互。<br>部署：Forge create部署存储与逻辑合约；Cast send/call测试投票/查询。 |
| **EIP-897 Proxy** | OpenZeppelin-labs | 使用代理合约fallback()通过assembly delegatecall转发calldata到逻辑合约；数据在代理存储，逻辑可升级。 | **优点**：实现真正升级；代理地址不变。<br>**缺点**：存储冲突需继承解决；assembly复杂；需注意变量顺序。 | 代理：fallback assembly delegatecall。<br>继承ProxyStorage避免slot 0冲突；逻辑继承DataLayout。<br>升级：setLogicAddress更换逻辑地址。 | 测试：call abi.encode调用set/get，验证升级后数据保留。<br>部署：Forge create多合约；Script批量部署。 |
| **EIP-1822 UUPS** | EIP标准 | 标准化Unstructured Storage；代理用assembly sstore逻辑地址到随机slot（keccak256("PROXIABLE")）；继承Proxiable确保兼容。 | **优点**：避免继承冲突；标准化升级。<br>**缺点**：需工具合约如Owned/LibraryLock防攻击；初始化用delegatecall。 | 代理：constructor sstore地址+delegatecall初始化。<br>逻辑：继承Owned/Proxiable/LibraryLock；updateCodeAddress升级。<br>随机slot防覆盖。 | 测试：call验证init/upgrade/owner限制；Forge test -vvv debug栈。<br>部署：类似Proxy，Script处理多合约。 |
| **EIP-1967** | EIP标准（UUPS标准化版） | 标准化存储槽（keccak256('eip1967.proxy.*')-1防攻击）；支持Beacon多代理共享逻辑；事件Upgraded/BeaconUpgraded/AdminChanged。 | **优点**：标准化高，Etherscan支持；Beacon降低部署gas；OpenZeppelin框架友好。<br>**缺点**：需继承管理internal函数；Beacon升级需注意初始化。 | 代理：继承ERC1967Proxy/BeaconProxy；_implementation()从slot获取。<br>Beacon：UpgradeableBeacon存储逻辑地址。<br>逻辑：Initializable防重复init。 | 测试：接口/call验证init/upgrade/multi-proxy同步。<br>部署：Forge script初始化Beacon+Proxy；upgradeTo升级Beacon。 |
| **EIP-2535 Diamonds** | EIP标准（钻石模型） | 有序存储：mapping函数选择器到切面（Facet）地址；diamondCut原子加/替/删函数；loupe查询Facet。 | **优点**：突破24KB限制；原子升级；支持多Facet。<br>**缺点**：部署复杂；存储需Diamond/AppStorage防冲突；gas高。 | Diamond：fallback delegatecall基于mapping。<br>存储：AppStorage结构体slot 0。<br>Facet：继承接口如IDiamondCut/Loupe。<br>diamondCut：add/replace/remove函数。 | 测试：接口验证loupe/Facet调用；升级后数据/函数检查。<br>部署：Script组装FacetCut[]初始化Diamond；UpdateScript diamondCut升级。

# 2025-08-14

通过分享会了解  Foundry 

1. Foundry介绍与安装简介：Foundry是基于Rust的以太坊开发工具，支持依赖管理、编译、测试和部署，优于Truffle和Hardhat的性能和速度。
安装：通过命令行安装，需解决官方文档未提及的依赖问题（如Rust版本兼容性）。
初始化：使用forge init创建项目，自动生成标准目录结构。

2. 智能合约编写环境搭建：初始化Foundry项目，导入OpenZeppelin库以使用标准ERC-20模板。
代码实现：基于OpenZeppelin的ERC20合约，配置代币名称、符号和初始供应量。
关键点：通过forge install管理依赖，确保Solidity版本兼容。

3. 智能合约测试测试工具：Foundry提供Forge测试框架，支持单元测试、模糊测试（Fuzz Testing）和区块链分叉测试。
测试方法：编写测试合约，继承forge-std/Test.sol。
使用setUp函数初始化测试环境。
测试核心功能（如transfer、approve），利用vm.prank模拟用户操作。

高级测试：模糊测试验证边界条件，vm.expectRevert检查异常情况。

4. 部署流程部署脚本：使用Forge的forge create命令，结合vm.startBroadcast部署到本地Anvil节点或测试网。
简化工作流：通过Makefile自动化编译、测试和部署。

图表：流程概览阶段
核心内容
关键命令/工具
安装与初始化
安装Foundry，初始化项目
forge init, forge install
合约编写
导入OpenZeppelin，编写ERC-20合约
OpenZeppelin ERC20, forge build
测试
单元测试、模糊测试、异常测试
forge test, vm.prank, vm.expectRevert
部署
编写部署脚本，部署到Anvil或测试网
forge create, vm.startBroadcast

核心问题解答问题：如何高效开发和测试ERC-20代币？答案：使用Foundry的快速编译和测试功能，结合OpenZeppelin的标准化合约，简化开发流程；通过模糊测试和分叉测试确保合约健壮性。

问题：Foundry相比其他工具的优势？答案：Rust-based，速度快；测试功能强大，支持模糊测试和链上模拟；命令行操作高效。

# 2025-08-13

### 以太坊测试网络搭建

| **分类**           | **内容**                                                                 | **扩展性探索**                                                                 |
|--------------------|--------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| **环境准备**       | **工具与版本**：<br>- Docker: MacOS (Desktop 4.41.1), Ubuntu (Engine 27.2.0)<br>- Kurtosis CLI: 1.10.3<br>**镜像源**：ethpandaops（Docker Hub）<br>**Kurtosis Ethereum Package**：GitHub ethpandaops/ethereum-package | - **Docker用途**：容器化以太坊节点，便于隔离和快速部署。<br>- **Kurtosis CLI**：自动化测试网络部署，简化配置管理。<br>- **潜在问题**：版本兼容性需注意，Docker Desktop在MacOS上可能因资源占用导致性能问题。<br>- **优化建议**：使用最新稳定版Docker，配置镜像加速器以提高拉取速度。 |
| **测试网络搭建**   | **配置文件**（network_params.yaml）：<br>- 1个节点（Geth执行层 + Lighthouse共识层）<br>- 网络预设：minimal<br>- 附加服务：tx_fuzz<br>**运行命令**：<br>```bash<br>kurtosis run --enclave eth-dev github.com/ethpandaops/ethereum-package --args-file network_params.yaml<br>```<br>**查看网络/容器/日志**：<br>- 查看网络：`kurtosis enclave inspect 47df29ed2499`<br>- 查看容器：`docker ps --no-trunc`<br>- 查看日志：`docker logs -f e5543b0e1280` | - **tx_fuzz作用**：模拟交易负载，测试网络稳定性。<br>- **minimal预设**：适合测试环境，降低资源需求，但可能不支持某些高级功能。<br>- **潜在问题**：单节点配置可能无法模拟真实网络的分叉或共识问题。<br>- **优化建议**：增加节点数量（count>1）以测试多节点共识，或启用更多附加服务（如区块浏览器）。 |
| **验证者客户端**   | **Lighthouse验证者参数**：<br>- 日志：info级别<br>- 配置文件目录：/network-configs<br>- 密钥目录：/validator-keys/keys, /validator-keys/secrets<br>- Beacon节点：http://172.16.0.12:4000<br>- 费用接收地址：0x8943545177806ED17B9F23F0a21ee5948eCaa776<br>- Metrics：启用，监听0.0.0.0:8080，允许所有来源 | - **用途**：验证者客户端负责验证和提议区块，需与共识层紧密协作。<br>- **潜在问题**：未加密的密钥存储可能导致安全风险；metrics开放所有来源不适合生产环境。<br>- **优化建议**：生产环境中限制metrics访问（如IP白名单），并加密密钥存储。 |
| **共识层客户端**   | **Lighthouse Beacon节点参数**：<br>- 日志：info级别<br>- 数据目录：/data/lighthouse/beacon-data<br>- 监听：0.0.0.0:9000（UDP/TCP），QUIC 9001<br>- ENR配置：禁用自动更新，IP 172.16.0.12<br>- HTTP API：0.0.0.0:4000<br>- 执行层连接：http://172.16.0.11:8551（JWT认证）<br>- Metrics：0.0.0.0:5054，允许所有来源 | - **用途**：共识层管理PoS机制，与执行层通过JWT认证通信。<br>- **潜在问题**：禁用ENR自动更新可能导致节点发现失败；开放API端口有安全隐患。<br>- **优化建议**：启用ENR自动更新以提高节点发现效率，生产环境需配置防火墙限制API访问。 |
| **执行层客户端**   | **Geth节点参数**：<br>- 初始化：`geth init --datadir /data/geth/execution-data /network-configs/genesis.json`<br>- 网络ID：3151908<br>- HTTP RPC：0.0.0.0:8545，启用admin,engine,net,eth,web3,debug,txpool<br>- WebSocket：0.0.0.0:8546<br>- 认证RPC：0.0.0.0:8551（JWT）<br>- 同步模式：full<br>- Metrics：0.0.0.0:9001<br>- P2P端口：30303 | - **用途**：执行层处理交易和状态，Geth是主流客户端。<br>- **潜在问题**：全同步模式资源消耗大；允许未保护交易和不安全解锁不适合生产环境。<br>- **优化建议**：测试环境可使用snap同步模式以加快同步，生产环境禁用不安全选项。 |
| **节点RPC访问**   | **执行层RPC**：<br>- 链ID：`curl -X POST http://127.0.0.1:52538 --data '{"method":"eth_chainId","params":[],"id":1,"jsonrpc":"2.0"}'`<br>- 区块高度：`curl -X POST http://127.0.0.1:52538 --data '{"method":"eth_blockNumber","params":[],"id":1,"jsonrpc":"2.0"}'`<br>**共识层RPC**：<br>- 同步状态：`curl http://127.0.0.1:52544/eth/v1/node/syncing`<br>- 节点版本：`curl http://127.0.0.1:32856/eth/v1/node/version` | - **用途**：RPC接口用于查询链状态、调试和监控。<br>- **潜在问题**：默认端口（如52538）可能与实际配置不符，需检查实际端口映射。<br>- **优化建议**：使用负载均衡器或反向代理管理RPC请求，记录调用日志以便调试。 |
| **可观测性**       | **配置**：<br>- 添加Prometheus和Grafana服务（network_params.yaml）<br>- Grafana仪表盘：ID 18463（go-ethereum-by-instance）<br>**访问**：Grafana仪表盘提供节点监控 | - **用途**：Prometheus收集metrics，Grafana可视化节点性能和健康状态。<br>- **潜在问题**：Grafana默认配置可能需要额外调整以适配自定义metrics。<br>- **优化建议**：添加警报规则，监控CPU、内存、同步状态等关键指标；定期备份Prometheus数据。 |
| **其他搭建方式**   | **Foundry Anvil**：<br>- 轻量级测试工具，快速启动本地以太坊节点<br>- 文档：https://getfoundry.sh/anvil/overview/ | - **用途**：Anvil适合本地开发和测试，支持快速部署和自定义链配置。<br>- **潜在问题**：不适合大规模网络测试，缺乏真实网络环境模拟。<br>- **优化建议**：结合Kurtosis用于复杂网络测试，Anvil用于快速原型开发。 |

### 总结
- **核心内容**：文档详细介绍了使用Kurtosis CLI和Docker搭建以太坊测试网络的步骤，包括环境准备（Docker、Kurtosis）、网络配置（Geth+Lighthouse）、节点启动参数（验证者、共识层、执行层）、RPC访问以及可观测性配置（Prometheus+Grafana）。还提及了Foundry Anvil作为替代工具。
- **扩展价值**：
  - **适用场景**：适合开发者测试智能合约、DApp或新功能，支持快速部署和销毁隔离网络。
  - **安全注意**：测试环境配置（如开放端口、未保护交易）不适合生产环境，需加强安全措施。
  - **优化方向**：增加节点数量模拟真实网络、配置警报提升监控能力、使用snap同步降低资源消耗。
  - **未来探索**：可结合其他客户端（如Prysm、Nethermind）测试兼容性，或集成更多工具（如区块浏览器）增强功能。
- **整体评价**：文档提供了清晰的搭建流程，适合以太坊开发者快速上手测试网络，但需注意生产环境的安全性和性能优化。

# 2025-08-12

参加产品和运营分享会，了解了web3产品相关知识，以及保姆级运营过程

# 2025-08-11

#### 课程01.5: DApp vs. 传统应用
- **核心概念**：比较去中心化应用（DApp）与传统Web应用的技术栈、后端架构、数据管理和用户体验。DApp基于区块链、智能合约和去中心化存储；传统应用依赖中心化服务器和数据库。
- **技术栈对比**：前端均用React/Vue/Angular，但DApp集成Web3钱包（如MetaMask）；后端DApp用Solidity合约和IPFS，传统用Node.js等API服务器。
- **数据管理**：传统：中心化数据库（ACID事务，高TPS）；DApp：区块链状态+IPFS（最终一致性，低TPS 10-1000）。
- **用户体验**：传统：邮箱/密码登录；DApp：钱包连接、私钥管理。交易确认：传统快速，DApp需签名+广播。
- **DApp优势**：去中心化、数据完整性、信任最小化。
- **挑战**：低吞吐量、钱包复杂性、私钥安全。
- **趋势**：2020 DeFi爆发、2021 Layer2发展、2023+ 用户体验优化，无具体固定收益DeFi示例。

#### 课程02: Solidity基础
- **核心概念**：Solidity用于Ethereum智能合约开发，语法类似JS/C++，支持数据类型（uint256、mapping、array）、函数可见性（public/external）和状态修饰符（view/pure/payable）。
- **关键特性**：不可变、透明、去中心化；Gas机制（EIP-1559：基础费+优先费），存储操作昂贵（首次写20,000 Gas）。
- **示例代码**：
  - 函数示例：`function deposit() external payable { _value += msg.value; }`
  - Gas优化：使用calldata、批量操作、事件代替存储。
  - 事件：`event Deposited(address indexed user, uint256 amount);` 用于日志记录，Gas低。
- **安全考虑**：防范重入攻击、选择器冲突、权限管理、签名重放、tx.origin钓鱼、时间戳操纵。
- **DeFi应用**：Gas优化策略（如Layer2）、测试工具（Gas Reporter）。

#### 课程03: Vault合约
- **核心概念**：FixedRateERC4626Vault继承ERC4626、Ownable和ReentrancyGuard，实现固定收益金库，支持存款/取款、奖励累积。
- **结构**：状态变量包括奖励代币、年利率（annualRateBps）、全局累积因子（globalAccumulatedRewardPerToken）；用户映射跟踪奖励。
- **函数**：deposit/withdraw（覆盖ERC4626，添加_accrue奖励逻辑）；_updateGlobalAccumulator更新累积因子；claim提取奖励。
- **收益机制**：全局累积因子O(1)计算奖励：用户资产 × (当前累积 - 用户结算值) / 1e18；类似Compound/Aave。
- **代码示例**：
  - 更新累积：`rewardPerToken = (annualRateBps * elapsed * 1e18) / (10_000 * ONE_YEAR);`
  - 奖励计算：`rewardAccruedByUser[user] += (assets * unpaidRewards) / 1e18;`
- **DeFi集成**：兼容ERC4626标准，支持DeFi协议如存款/奖励机制，安全使用重入防护。

#### 课程04: Hardhat部署
- 无可用内容（页面为空或无法读取）。推测可能涉及Hardhat环境设置、合约编译/测试/部署脚本、网络配置和最佳实践。

#### 课程05: 前端开发
- **核心概念**：构建DeFi DApp前端，焦点在前端-钱包-合约交互流程。
- **框架**：Next.js 14 + TypeScript、wagmi（Ethereum Hooks）、RainbowKit（钱包连接）。
- **钱包集成**：使用ConnectButton支持多钱包；架构：前端连接RainbowKit → 钱包 → Ethereum网络/合约。
- **UI组件**：VaultInterface显示股份/奖励；useVault Hook处理存款。
- **合约交互**：useReadContract读取数据（balanceOf、getPendingReward，watch: true实时更新）；useWriteContract写入（ERC20 approve + deposit，两步流程）。
- **RPC配置**：读操作用DApp RPC，写用钱包RPC；生产环境推荐Infura。
- **业务流程**：存款示例：用户输入 → 钱包确认 → 合约交互，含错误处理。

#### 课程06: Notional深度剖析
- 无可用内容（页面为空或无法读取）。推测可能涉及Notional Finance固定利率借贷、fCash代币、AMM定价、金库集成和技术细节。

### 总结

| **课程** | **主题** | **关键概念** | **工具/框架** | **焦点** |
|----------|----------|--------------|---------------|----------|
| 01.5 | DApp vs. 传统 | 去中心化 vs. 中心化；技术栈/数据/UX对比 | Web3/MetaMask/IPFS | 优势：信任最小化；挑战：低TPS |
| 02 | Solidity基础 | 数据类型/函数/Gas优化/事件 | Solidity/Gas Reporter | 安全：重入防护；DeFi Gas策略 |
| 03 | Vault合约 | 固定收益ERC4626金库；奖励累积 | OpenZeppelin/ReentrancyGuard | 函数：deposit/claim；机制：O(1)计算 |
| 04 | Hardhat部署 | 无可用内容 | Hardhat（推测） | 部署脚本/测试（推测） |
| 05 | 前端 | DApp UI/钱包集成/合约交互 | Next.js/wagmi/RainbowKit | 流程：approve+deposit；RPC分离 |
| 06 | Notional剖析 | 无可用内容 | Notional（推测） | 固定利率/fCash（推测） |

### 启发
- **DApp开发流程**：从Solidity合约（02-03）到部署（04）和前端集成（05），聚焦固定收益DeFi（如金库奖励）。
- **固定收益焦点**：Vault机制提供稳定回报，集成DeFi协议减少波动。
- **安全与UX**：强调Gas优化、重入防护和用户友好交互（wagmi Hooks）。
- 课程整体构建DeFi固定收益应用，部分内容缺失可能需检查仓库更新。

# 2025-08-10

刚转了sepolia测试币到这个地址，Transaction Hash: 0x3a8f25746832a16b2e69497d5d7ada6b60ffd2820013f4d3e6d1e40b0b167410
等会继续

# 2025-08-09

学习 Solidity 基础知识

# 2025-08-07

#今日事项
了解智能合约开发流程，以及 Solidity 基本语法，周末继续深入学习

-------------------

# 学习 Solidity 的精简指南

Solidity 是以太坊及其他 EVM 兼容区块链上智能合约开发的核心语言。以下是学习 Solidity 的精简指南，聚焦高级特性、优化、安全实践及学习路径，适合希望精通智能合约开发的开发者。

## 一、Solidity 高级特性

### 1. 复杂数据结构
| 数据类型 | 描述 | 示例 |
|----------|------|------|
| **结构体 (Struct)** | 自定义复合类型 | `struct User { address addr; uint balance; }` |
| **映射 (Mapping)** | 键值存储，节省 Gas | `mapping(address => uint) public balances;` |
| **嵌套映射** | 多级键值关系 | `mapping(address => mapping(uint => bool)) votes;` |
| **动态数组** | 可变长度数组 | `uint[] public dynamicArray;` |
| **枚举 (Enum)** | 有限状态集 | `enum State { Pending, Active, Closed }` |

- **技巧**：优先用映射替代数组以降低 Gas 成本；结构体适合封装复杂数据，但避免过度嵌套。

### 2. 函数高级用法
| 特性 | 描述 | 示例 |
|------|------|------|
| **函数重载** | 同名函数不同参数 | `function transfer(address to, uint amount) public; function transfer(address to, uint amount, bytes data) public;` |
| **Fallback/Receive** | 处理未定义调用或 ETH 接收 | `receive() external payable { }` |
| **修饰符 (Modifier)** | 封装前置检查逻辑 | `modifier onlyOwner() { require(msg.sender == owner); _; }` |
| **虚拟/重写 (Virtual/Override)** | 支持继承与多态 | `function update() public virtual; override;` |

- **注意**：
  - `receive()` 用于接收 ETH，需标记 `payable`。
  - `fallback()` 处理未匹配的函数调用，谨慎使用以防安全漏洞。

### 3. 事件与日志
- **作用**：记录状态变化，便于前端或检索器监听，降低 Gas 成本。
- **示例**：
  ```solidity
  event Transfer(address indexed from, address indexed to, uint amount);
  function transfer(address to, uint amount) public {
      emit Transfer(msg.sender, to, amount);
  }
  ```
- **优化**：使用 `indexed` 关键字（最多 3 个）提高检索效率。

### 4. 继承与接口
- **单/多继承**：
  ```solidity
  contract A { function foo() public virtual { } }
  contract B is A { function foo() public override { } }
  ```
- **接口 (Interface)**：定义函数规范，强制实现。
  ```solidity
  interface IERC20 {
      function transfer(address to, uint amount) external returns (bool);
  }
  ```
- **抽象合约**：部分函数无实现，需子类重写。
  ```solidity
  abstract contract Base {
      function implementMe() public virtual;
  }
  ```

### 5. 库 (Library)
- **作用**：复用代码，节省 Gas，部署一次多合约调用。
- **示例**：
  ```solidity
  library Math {
      function add(uint a, uint b) internal pure returns (uint) {
          return a + b;
      }
  }
  contract MyContract {
      using Math for uint;
      function sum(uint x, uint y) public pure returns (uint) {
          return x.add(y);
      }
  }
  ```
- **注意**：库函数通常标记 `internal` 或 `pure`，不存储状态。

## 二、Gas 优化技巧
| 技巧 | 描述 | Gas 节省 |
|------|------|----------|
| **最小化存储操作** | 缓存存储到内存，减少 `SLOAD` (2100 Gas) 和 `SSTORE` (20000 Gas) | 高 |
| **位压缩** | 用 `uint128` 或结构体压缩存储 | 中 |
| **短路求值** | 用 `&&` 或 `||` 提前终止条件检查 | 低 |
| **external 修饰符** | 仅外部调用函数，减少栈深度 | 低 |
| **避免动态数组循环** | 缓存数组长度，减少重复访问 | 中 |

- **示例**：
  ```solidity
  // 非优化
  for (uint i = 0; i < array.length; i++) { ... }
  // 优化
  uint len = array.length;
  for (uint i = 0; i < len; i++) { ... }
  ```

## 三、安全实践
| 漏洞 | 防护措施 | 示例 |
|------|-----------|-------|
| **重入攻击** | 使用 CEI 模式或 `ReentrancyGuard` | `balances[msg.sender] = 0; msg.sender.call{value: amount}("");` |
| **整数溢出** | 用 Solidity 0.8+ 或 `SafeMath` | `uint a = b + c; // 0.8+ 自动检查` |
| **权限控制** | 用 `onlyOwner` 或 `AccessControl` | `require(msg.sender == owner, "Not owner");` |
| **预言机操纵** | 用 Chainlink 或 TWAP | `Chainlink.getLatestPrice();` |
| **未初始化代理** | 确保初始化函数调用 | `initialize() onlyOwner;` |

- **关键**：遵循 Checks-Effects-Interactions (CEI) 模式，优先更新状态再调用外部合约。

## 四、开发与审计工具
| 工具 | 用途 | 使用方式 |
|------|------|-----------|
| **Foundry** | 快速开发、测试、模糊测试 | `forge init`, `forge test` |
| **Hardhat** | 现代开发框架，插件丰富 | `npx hardhat`, `npx hardhat node` |
| **Slither** | 静态分析，检测漏洞 | `slither MyContract.sol` |
| **MythX** | 云端安全扫描 | `mythx analyze MyContract.sol` |
| **Remix** | 在线 IDE，快速原型 | `https://remix.ethereum.org` |

- **审计流程**：
  1. 静态分析（Slither/Mythril）
  2. 动态测试（模糊测试）
  3. 人工审查逻辑漏洞
  4. 生成审计报告

## 五、学习路径
| 阶段 | 内容 | 资源 |
|------|------|------|
| **基础** | 掌握 Solidity 语法、数据类型、函数、事件 | [Solidity 文档](https://docs.soliditylang.org), [CryptoZombies](https://cryptozombies.io) |
| **进阶** | 学习继承、库、Gas 优化、安全实践 | [OpenZeppelin 合约](https://github.com/OpenZeppelin/openzeppelin-contracts), [Solidity by Example](https://solidity-by-example.org) |
| **实战** | 开发 Dapp（如 ERC-20、NFT），部署测试网 | [Remix IDE](https://remix.ethereum.org), [Hardhat 教程](https://hardhat.org) |
| **高级** | 审计、Layer 2 开发、跨链交互 | [Slither 文档](https://github.com/crytic/slither), [zkSync 指南](https://zksync.io) |

## 六、实战建议
- **项目**：开发 ERC-20 代币、NFT 市场或简单 DAO，部署至 Sepolia 测试网。
- **社区参与**：加入 GitHub 开源项目，贡献代码或测试用例。
- **安全意识**：学习历史漏洞案例（如 The DAO），练习审计开源合约。
- **工具熟练**：掌握 Foundry/Hardhat，定期用 Slither 扫描代码。

## 七、推荐资源
- **文档**：Solidity 官方文档、OpenZeppelin 合约库
- **教程**：CryptoZombies、Dapp University YouTube
- **社区**：Ethereum Stack Exchange、Discord（Foundry, Hardhat）
- **案例**：Uniswap、Compound、Aave 合约源码

## 八、注意事项
- **安全第一**：智能合约不可修改，部署前必须审计。
- **测试网优先**：用 Sepolia/Holesky 测试，避免主网损失。
- **持续学习**：跟踪 EVM 升级、Layer 2 方案（如 zkSync、Arbitrum）。

通过系统学习和实践，结合工具与社区资源，你可以逐步掌握 Solidity 并成为专业的智能合约开发者。

-----------------

# 智能合约开发笔记

## 一、Dapp 架构与开发流程

### Dapp 架构
| 组件 | 描述 | 技术栈 |
|------|------|--------|
| **前端** | 用户交互界面，连接钱包，调用合约 | HTML, CSS, JS (React/Vue), Viem, MetaMask |
| **智能合约** | 业务逻辑，运行于区块链 | Solidity, EVM |
| **数据检索器** | 捕获链上事件，存入数据库 | Ponder, Subgraph, TypeScript, PostgreSQL |
| **区块链/存储** | 存储合约状态与数据 | Ethereum, IPFS, Arweave |

### 开发流程
1. **需求分析**：明确功能、选择区块链（如以太坊、Solana）、设计 UX。
2. **智能合约开发**：用 Solidity 编写、测试、审计，优化 Gas。
3. **检索器开发**：用 Ponder/Subgraph 捕获事件，存入数据库。
4. **前端开发**：React/Vue 构建 UI，集成 MetaMask，调用合约。
5. **交互**：通过 Viem/Wagmi 读写链上数据，处理交易签名。
6. **部署**：用 Hardhat/Foundry 部署合约至测试网/主网，前端上 IPFS/Vercel。

## 二、以太坊开发环境搭建

| 工具 | 用途 | 安装/使用 |
|------|------|-----------|
| **Node.js** | 运行环境 | `nvm install --lts`, `npm install -g yarn` |
| **Foundry** | 快速开发测试 | `curl -L https://foundry.paradigm.xyz | bash`, `forge init` |
| **Hardhat** | 现代开发框架 | `npm install --global hardhat`, `npx hardhat` |
| **MetaMask** | 钱包交互 | 浏览器插件，连接测试网 |
| **Remix** | 在线 IDE | `https://remix.ethereum.org` |

## 三、Solidity 编程要点

### 基础语法
- **版本声明**：`pragma solidity ^0.8.0;`
- **数据类型**：
  - 基本：`bool`, `uint256`, `address`, `bytes32`, `string`
  - 复合：数组 (`uint[]`), 映射 (`mapping(address => uint)`), 结构体, 枚举
- **函数修饰符**：
  - 可见性：`public`, `external`, `internal`, `private`
  - 状态：`pure`, `view`, `payable`

### 合约结构
| 部分 | 描述 | 示例 |
|------|------|-------|
| **状态变量** | 存储区块链数据 | `uint256 public totalSupply;` |
| **构造函数** | 初始化合约 | `constructor() { owner = msg.sender; }` |
| **函数** | 执行逻辑 | `function transfer(address to, uint amount) public { ... }` |
| **事件** | 记录状态变化 | `event Transfer(address indexed from, address to, uint amount);` |

### 安全实践
| 风险 | 防护措施 |
|------|-----------|
| **重入攻击** | 使用 CEI 模式或 `ReentrancyGuard` |
| **访问控制** | 用 `onlyOwner` 或 `AccessControl` 限制权限 |
| **整数溢出** | 用 Solidity 0.8+ 或 `SafeMath` |

## 四、实战：链上留言板
- **环境**：Remix IDE
- **功能**：用户留言，存储并查询链上记录
- **代码**：
  ```solidity
  // SPDX-License-Identifier: MIT
  pragma solidity ^0.8.0;
  contract MessageBoard {
      mapping(address => string[]) public messages;
      event NewMessage(address indexed sender, string message);
      constructor() { messages[msg.sender].push("Hello ETH Pandas"); }
      function leaveMessage(string memory _msg) public { messages[msg.sender].push(_msg); emit NewMessage(msg.sender, _msg); }
      function getMessage(address user, uint256 index) public view returns (string memory) { return messages[user][index]; }
      function getMessageCount(address user) public view returns (uint256) { return messages[user].length; }
  }
  ```
- **部署**：用 Remix 连接 MetaMask，部署至 Sepolia 测试网
- **验证**：通过 Etherscan 查看合约地址、交易、事件日志

## 五、以太坊技术基础

### 账户模型
| 类型 | 控制 | 功能 |
|------|-------|------|
| **EOA** | 私钥签名 | 发起交易，支付 Gas |
| **合约账户** | 代码逻辑 | 执行合约，需 EOA 触发 |

### Gas 机制
- **Gas**：EVM 指令消耗单位
- **费用**：`maxFee = baseFee + priorityFee`
- **生命周期**：签名 → 广播 → 打包 → 确认（12 块概率确认，PoS 约 12 分钟完全终结）

## 六、测试网部署
- **测试网**：
  - Sepolia：稳定，生产模拟
  - Holesky：验证者测试
- **获取测试币**：通过 Sepolia 水龙头（如 `https://sepolia-faucet.pk910.de`）
- **部署步骤**：
  1. Remix 连接 MetaMask（Sepolia 网络）
  2. 编译合约
  3. 部署并确认交易
  4. 用 Etherscan 验证地址、交易、事件

## 七、前端整合
- **流程**：连接钱包 → 实例化合约 → 调用函数 → 签名交易 → 更新 UI
- **技术栈**：HTML/JS/CSS, Viem/Wagmi, MetaMask, React Context
- **示例代码**：
  ```javascript
  async function connectWallet() {
      const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
      web3 = new Web3(window.ethereum);
      account = accounts[0];
  }
  const contract = new web3.eth.Contract(contractABI, address);
  async function leaveMessage() {
      await contract.methods.leaveMessage("Hello").send({ from: account });
  }
  ```

## 八、高阶内容

### Gas 优化
- 减少存储操作：缓存到内存
- 位压缩：用 `uint128` 节省空间
- 循环优化：缓存数组长度
- 选择 `external` 修饰符

### 合约安全
| 漏洞 | 防护 |
|------|-------|
| **重入** | CEI 模式，`ReentrancyGuard` |
| **预言机操纵** | 用 Chainlink，TWAP 算法 |
| **未初始化代理** | 确保初始化函数调用 |

### 审计工具
| 工具 | 功能 |
|------|------|
| **Slither** | 静态分析，检测漏洞 |
| **MythX** | 云端安全扫描 |
| **Foundry** | 模糊测试 |

### 开发协作
- **GitHub 流程**：主分支稳定，功能分支开发，PR 审查
- **Issue 管理**：清晰描述，统一标签，自动化处理
- **礼仪**：代码配测试/文档，公开沟通

### Layer 2 解决方案
| 类型 | 项目 | 特点 |
|------|------|------|
| **Optimistic Rollup** | Arbitrum, Base | EVM 兼容，成本低，提现慢10天延迟 |
| **ZK Rollup** | zkSync, Starknet | 高安全性，快速提现，开发复杂 |

## 建议
- 熟练掌握 Solidity 语法，注重安全设计。
- 使用测试网（如 Sepolia）验证合约功能。
- 参与开源项目，积累实战经验。

# 2025-08-06

# Web3 安全与合规指南

## 一、Web3 合规性与法律风险

### 中国监管政策
- **禁止**：ICO、虚拟货币交易所、支付工具使用，境外平台服务境内用户。
- **打击**：非法集资、传销、洗钱、赌博。
- **基调**：封堵金融属性，容忍技术创新。

### 核心法律风险
| 风险类型 | 描述 | 法律后果 |
|----------|------|----------|
| **代币发行与交易** | ICO/IEO/IDO、空投等融资行为 | 非法吸收公众存款罪，技术人员可能被视为共犯 |
| **赌博/传销/洗钱** | 链游抽奖、邀请返利、场外交易 | 开设赌场罪、传销罪、洗钱罪 |
| **民商事争议** | 虚拟货币买卖、委托代投 | 合同无效，法院不受理，民事赔偿风险 |

### 入职风险防范
- **雇佣关系**：境外注册项目无劳动合同、社保，需评估灵活用工影响。
- **薪酬风险**：Token/USDT 薪资波动大，可能无效，影响社保基数。
- **出金风险**：C2C 交易易涉洗钱，建议保留法币收入，审查交易对手。
- **项目审查**：查白皮书、Token 机制，避开非法金融活动。

### 刑事风险案例
| 案例 | 罪名 | 细节 | 判决 |
|------|------|------|------|
| **BigGame 平台** | 开设赌场罪 | EOS 链上赌博，2 万用户，3.3 亿 EOS 赌资 | 主犯 4.5-5.75 年徒刑，从犯缓刑 |
| **TW711 平台** | 非法经营罪 | 虚拟币换汇，2.2 亿人民币 | 3-5 年徒刑，罚金 5-20 万 |
| **AIP 平台** | 非法吸收公众存款罪 | 矿机投资，1.16 亿存款 | 副院长参与宣传，罪名成立 |
| **PlusToken** | 传销罪 | 层级返利，虚假宣传 | 组织者被判刑 |
| **陈某某** | 洗钱罪 | 虚拟币跨境兑换 | 依法惩治，金额以实际支付计算 |

## 二、网络安全风险与防护

### 常见攻击方式
| 攻击类型 | 手段 | 后果 |
|----------|------|------|
| **钓鱼攻击** | 伪造邮件/网站、假客服 | 资产被盗，信息泄露 |
| **恶意软件** | 伪装面试软件、剪贴板劫持 | 钱包被盗，电脑被控 |
| **社交工程** | 冒充 HR/好友、群聊钓鱼 | 信息泄露，资产损失 |
| **供应链攻击** | 恶意插件、开源库后门 | 大量用户中招，损失严重 |
| **地址污染** | 剪贴板劫持、输入监听 | 资金转错，难以追回 |
| **传统隐私风险** | 弱密码、邮箱劫持 | 全盘账号失守 |

### 典型案例
- **面试钓鱼**：假软件植入木马，窃取 Cookie 和文件。
- **奖学金钓鱼**：签名验证导致钱包资产被盗。
- **剪贴板木马**：替换转账地址，资金直接损失。
- **假冒好友**：Telegram 伪装学长，诱导填写表单。
- **插件后门**：假插件替换转账地址，损失 0.8 万美元。
- **邮箱劫持**：钓鱼邮件导致全账号失控。

### 防护清单
| 场景 | 措施 |
|------|------|
| **面试/实习** | 用官方工具（如 Zoom），核实邀请信息，警惕“专用软件”。 |
| **空投/福利** | 用测试钱包，拒绝签名验证，核实官方域名。 |
| **软件/插件** | 仅从官网下载，检查口碑，控制插件数量。 |
| **账号安全** | 启用 2FA，使用密码管理器，定期更换密码。 |
| **钱包/转账** | 核对地址，离线保存助记词，定期检查授权。 |

## 三、骗局案例
- **高级钓鱼**：伪装文档（如 Stablecoin 风险）植入木马，窃取凭证、键盘记录。
- **假官方钓鱼**：Trezor 用户收到 trezor.us 邮件（非官方 trezor.io），Punycode 域名（如 trẹzor）诱导点击。
- **内部作恶**：项目方需加强人员安全管理，避免内部人员泄露或作恶。

## 四、建议
- **合规**：审查项目合法性，保留资金合法记录，协商法币薪资。
- **安全**：警惕钓鱼、木马，核实信息来源，使用安全工具，保护钱包与账号。
- **行动**：遇攻击立即断网、记录证据，寻求专业帮助。

# 2025-08-05

# 参加 Web3 安全分享和答疑 会议，get 安全防护大门钥匙
----------------
# Web3 核心概念与案例总结

## 一、DeFi：去中心化金融
DeFi 基于区块链提供无需中介的金融服务，如借贷、交易、支付。

### 典型案例
| 项目 | 类型 | 核心机制 | 特点 |
|------|------|----------|------|
| **Uniswap** | DEX | AMM（恒定乘积公式：x * y = k） | 流动性池，LP 赚手续费（0.3%），无需中介，24/7 交易 |
| **Compound** | 借贷 | 动态利率，超额抵押，cToken | 存入资产赚利息，借贷需超额担保，自动清算机制 |
| **Sky (原 MakerDAO)** | 稳定币 | 超额抵押生成 USDS（原 DAI） | 稳定费率调节供需，清算机制确保稳定性 |

- **Uniswap**：通过流动性池自动定价，LP 提供资金赚手续费。
- **Compound**：存入资产获 cToken 利息，借贷需超额抵押，价格波动可能触发清算。
- **Sky**：抵押资产生成 USDS，稳定费率与清算机制维持 1 美元锚定。

## 二、NFT：数字所有权
NFT 通过区块链确保数字资产唯一性与所有权，智能合约实现自动化交易与版税。

### 案例
| 项目 | 特点 | 应用 |
|------|------|------|
| **CryptoPunks** | 10,000 个像素头像 NFT | 收藏品，记录所有权与交易历史 |
| **OpenSea** | 全球最大 NFT 交易平台 | 支持买卖、创建 NFT，基于以太坊 |

- **CryptoPunks**：NFT 先驱，独特的像素头像形成收藏文化。
- **OpenSea**：去中心化 NFT 交易市场，简化买卖流程。

## 三、DAO：去中心化自治组织
DAO 通过智能合约与社区投票实现去中心化治理。

### 案例
| 项目 | 特点 | 应用 |
|------|------|------|
| **Nouns DAO** | 每日生成 NFT，收益入金库 | 社区投票决定资金用途 |
| **LXDAO** | 支持 Web3 公共物品 | 任务奖励，社区提案 |
| **ConstitutionDAO** | 众筹购买《美国宪法》 | 筹集 4700 万美元，展现 DAO 潜力 |

- **Nouns DAO**：NFT 拍卖与社区治理结合。
- **LXDAO**：资助 Web3 开源项目，奖励贡献者。
- **ConstitutionDAO**：众筹失败但展示 DAO 大规模协作潜力，需改进隐私保护。

## 四、MEME 币：文化与投机
MEME 币以网络文化为背景，靠社区共识驱动，投机性高。

### 案例
| 项目 | 特点 | 风险 |
|------|------|------|
| **DOGE** | 狗狗表情包，名人效应 | 价格波动大，集中化风险 |
| **PEPE** | 悲伤青蛙，社区驱动 | 市值高，缺乏实际价值 |

### 投资建议
- 警惕无价值炒作，拒绝盲目跟风。
- 检查链上数据透明度，控制仓位（<10%）。

## 五、交叉创新
| 领域 | 项目 | 创新 |
|------|------|------|
| **DeFi + NFT** | BendDAO, Sudoswap | NFT 抵押借贷，AMM 交易 NFT |
| **DAO + MEME** | FriendTech, ShibaSwap | 社交金融化，社区治理 |
| **AI + DeFi** | Yearn Finance, 1inch | AI 优化收益，智能交易路由 |
| **Web3 + 乡建** | 南塘 DAO | 南塘豆治理，PGS + 区块链认证农产品 |

- **南塘 DAO**：结合 Web3 与乡村振兴，发行南塘豆用于社区治理与交易。

## 六、2025 年趋势
| 趋势 | 核心技术 | 项目 |
|------|----------|------|
| **Intent-Based 交易** | Solver 网络，跨链聚合 | UniswapX, 1inch Fusion |
| **账户抽象** | Gas 代付，社交恢复 | Safe, Argent |
| **模块化区块链** | 分离执行、共识等 | Celestia, Polygon CDK |
| **AI + Web3** | 去中心化 AI，预测市场 | Fetch.ai, SingularityNET |

## 七、学习与职业建议
- **初学者**：学 DeFi 核心概念，测试网实践，关注安全。
- **求职者**：专注 1-2 领域，参与开源项目，跟踪行业动态。
- **资源**：Ethereum.org、Messari、Bankless 播客。

**提醒**：Web3 发展迅速，关注官方渠道，避免过时信息。

------------------
# Web3 岗位类型与技能要求

## 一、技术岗

| 岗位 | 主要职责 | 技能要求 | 常用技术栈 |
|------|----------|----------|------------|
| **前端工程师** | 开发 Dapp 前端界面，支持钱包连接、交易签名，优化用户体验 | 精通 HTML/CSS/JS、React/Vue，熟悉 Viem/Metamask，了解以太坊/Solana | HTML5, CSS3, JavaScript (ES6+), React/Vue, TypeScript, Next.js, Viem |
| **后端工程师** | 构建 Dapp 后端服务，集成智能合约，优化高并发 API | 精通 Node.js/Go/Python，熟悉 Viem/RESTful/GraphQL，了解数据库与容器化 | Node.js, Go, Python, Viem, RESTful/GraphQL, MySQL/PostgreSQL, Docker/Kubernetes |
| **智能合约工程师** | 编写、部署、审计 Solidity 智能合约，优化 Gas 费用 | 熟练 Solidity，熟悉以太坊/Polygon，掌握 Foundry/Hardhat，了解安全审计 | Solidity, Remix, Foundry/Hardhat, Phalcon/Tenderly, Yul |

### 重点
- **前端**：注重 UI 开发与 Web3 交互，Viem 取代 Ethers.js/Web3.js。
- **后端**：支持高并发、链上数据处理，需熟悉异步架构。
- **智能合约**：强调安全性与 Gas 优化，需掌握常见攻击防护。

## 二、非技术岗

| 岗位 | 主要职责 | 技能要求 | 常用工具/平台 |
|------|----------|----------|--------------|
| **产品与运营** | 协调产品发布，执行增长策略，分析运营数据 | 熟悉产品上线流程，掌握 SQL/Excel，具备跨部门协调能力 | SQL, Excel |
| **社区管理** | 管理 Telegram/X/Discord 社区，组织 AMA/活动，分析社区健康度 | 了解 Web3/DAO，擅长文案与活动策划，熟练数据分析 | Telegram, X, Discord |
| **研究分析** | 分析市场/用户数据，撰写研究报告，支持经济模型设计 | 熟悉区块链/DeFi，掌握 Excel/Python/Glassnode，具备报告撰写能力 | Excel, SPSS, Python, Glassnode, Token Terminal |

### 重点
- **产品与运营**：数据驱动，跨部门协作，关注用户增长。
- **社区管理**：社区互动与品牌粘性，需快速响应用户。
- **研究分析**：链上数据分析，洞察市场趋势，支持战略决策。

## 建议
- **技术岗**：深入学习 Viem/Solidity，参与开源项目，关注区块链网络特性。
- **非技术岗**：熟悉 Web3 生态，掌握数据分析与沟通技能，跟踪行业动态。
- **实践**：通过测试网或 Hackathon 积累经验，关注安全与最新技术趋势。

# 2025-08-04

# 安装工具，注册账号
按官网指引安装常用 Web3 工具 ，完成了Twitter ， Telegram，MetaMask ，  Notion ， Zoom 和 Calendly 安装注册， Discord 和 LinkedIn 要求手机号验证，暂时还没找到靠谱接码渠道，需要再探索一下。完成发布推文、加入社群、创建钱包任务。
# 会议
第一次参与社区会议

# 学习笔记
# 基础（区块链基础概念）
区块链基础概念

##### 1.1 什么是区块链？
- **定义**：区块链是一种分布式、去中心化的数字账本技术，记录交易数据并存储在多个计算机节点上。每个节点拥有相同的数据副本，确保系统无单一控制点或故障点。[](https://www.mckinsey.com/featured-insights/mckinsey-explainers/what-is-web3)
- **核心特点**：
  - **去中心化**：没有中央机构控制，数据由网络中的节点共同维护。
  - **不可篡改**：数据以“区块”形式按时间顺序链接，一旦记录，无法更改。
  - **透明性**：所有交易公开可见，增强信任。
  - **安全性**：通过密码学保护数据安全，防止未经授权的访问或篡改。
- **关键组件**：
  - **区块**：包含交易数据、时间戳和前一区块的哈希值。
  - **链**：区块按时间顺序连接形成链。
  - **共识机制**：如工作量证明（PoW）或权益证明（PoS），确保节点间数据一致性。[](https://web3-resources.xyz/pages/blog.html)

##### 1.2 区块链与 Web3 的关系
- **Web3 定义**：Web3 是互联网的下一代，基于区块链技术，强调去中心化、用户控制和数据所有权。[](https://ethereum.org/en/web3/)
- **Web1 vs Web2 vs Web3**：
  - **Web1（1990-2005）**：只读互联网，静态网页，开放协议，价值归于用户和开发者。
  - **Web2（2005-2020）**：读写互联网，由大公司控制，数据集中在中心化平台（如 Google、Facebook）。
  - **Web3（当前）**：读写拥有互联网，用户通过区块链拥有数据和平台控制权，强调去中心化金融（DeFi）、非同质化代币（NFT）和去中心化自治组织（DAO）。[](https://guidetoweb3.xyz/web3-basics)[](https://ethereum.org/en/web3/)
- **Web3 的核心原则**：
  - **去中心化**：数据和控制权分布在用户和社区中，而非中心化实体。
  - **无需许可**：任何人都可以参与，无需中介批准。
  - **原生支付**：使用加密货币（如以太坊的 ETH）进行交易，绕过传统银行系统。[](https://ethereum.org/en/web3/)

##### 1.3 区块链的关键技术
- **智能合约**：
  - 定义：自动执行的程序代码，当满足预设条件时自动运行，部署在区块链上，不可更改。[](https://www.mckinsey.com/featured-insights/mckinsey-explainers/what-is-web3)
  - 示例：以太坊上的智能合约可用于自动化交易、票务验证或去中心化应用（DApp）。
  - 常用语言：Solidity（以太坊的主要智能合约语言）。[](https://www.useweb3.xyz/tags/smart%2520contracts)
- **数字资产和代币**：
  - 类型：包括加密货币（如比特币、ETH）、稳定币、NFT 和代币化资产（如艺术品或门票）。
  - 功能：NFT 可证明数字资产的唯一性，用于身份验证、版权保护等。[](https://www.mckinsey.com/featured-insights/mckinsey-explainers/what-is-web3)
- **以太坊虚拟机（EVM）**：
  - 定义：以太坊的运行环境，允许开发者部署智能合约和 DApp。
  - 特点：支持多种编程语言（如 Solidity），实现复杂计算和资产代币化。[](https://web3-resources.xyz/pages/blog.html)

##### 1.4 区块链的实际应用
- **金融**：去中心化金融（DeFi）允许用户直接交易，无需银行或中介。[](https://en.wikipedia.org/wiki/Web3)
- **票务系统**：通过区块链验证票据真伪，防止欺诈（如假票）。[](https://www.mckinsey.com/featured-insights/mckinsey-explainers/what-is-web3)
- **数据存储**：如 Filecoin，提供去中心化的存储网络，替代传统的云服务（如 Google Drive）。[](https://www.coindesk.com/learn/what-are-web3-cryptos)
- **身份管理**：用户拥有自己的数字身份，可选择性地共享数据，增强隐私保护。[](https://consensys.io/blog/what-is-web3-here-are-some-ways-to-explain-it-to-a-friend)
- **内容创作**：Web3 平台（如 Mirror）允许创作者通过 NFT 直接与粉丝建立关系，众筹资金或出售作品。[](https://consensys.io/blog/what-is-web3-here-are-some-ways-to-explain-it-to-a-friend)

# 突破（稳定币类型、借贷协议收益模型、Uniswap V3 vs V4）


### 关键点
- **稳定币类型**：研究表明，稳定币主要分为四种：法币抵押（如 USDC）、加密货币抵押（如 DAI）、商品抵押（如金本位稳定币）和算法稳定币（如 Ampleforth），每种类型有不同的稳定机制，法币抵押和加密货币抵押较为常见。
- **借贷协议收益模型**：证据显示，DeFi 借贷协议的收益模型通常基于供需动态调整利率，包括固定和浮动利率，涉及收益耕作和超额抵押等策略，存在智能合约风险。
- **Uniswap V3 vs V4**：资料显示，Uniswap V4 在 V3 的集中流动性基础上增加了钩子（Hooks）和单例合约，降低了成本并提升了灵活性，V4 更适合开发者定制。

### 稳定币类型
稳定币是为了减少加密货币波动而设计的一种数字资产，通常与美元或其他资产挂钩。常见类型包括：
- **法币抵押**：如 USDC 和 USDT，由银行账户中的法币储备支持。
- **加密货币抵押**：如 DAI，通过锁定其他加密货币（如以太坊）作为抵押。
- **商品抵押**：较少见，如金本位稳定币，价值与黄金挂钩。
- **算法稳定币**：如 Ampleforth，通过算法调整供应量保持价格稳定，但存在去锚风险。

### 借贷协议收益模型
DeFi 借贷协议允许用户借出或借入加密资产，收益模型主要依赖以下机制：
- 利率基于供需动态调整，借入需求高时利率上升。
- 提供两种利率选择：固定利率和浮动利率。
- 通过收益耕作（Yield Farming）赚取利息，超额抵押确保贷款安全。
- 闪电贷款（Flash Loans）无需抵押，但需在同一交易块内偿还。

### Uniswap V3 与 V4 比较
Uniswap 是一个去中心化交易所，V3 和 V4 各有特色：
- **V3**：引入集中流动性，允许流动性提供者（LPs）选择价格范围，费用层级为 0.05%、0.3%、1%，更高效但成本较高。
- **V4**：基于 V3 增加钩子功能，允许开发者定制池逻辑；采用单例合约降低创建池成本；支持原生 ETH 交易，费用层级更灵活。

---

# 详细学习笔记

以下是关于稳定币类型、DeFi 借贷协议收益模型和 Uniswap V3 与 V4 比较的详细学习笔记，旨在为 Web3 新手提供全面的入门指导。这些内容基于 2025 年 8 月 4 日的最新研究和市场资料，涵盖了每个主题的背景、技术细节和实际应用。

## 稳定币类型

### 背景与定义
稳定币是一种旨在保持稳定价值的加密货币，通常通过与法币（如美元）、商品（如黄金）或其他加密资产挂钩来减少价格波动。它们在 DeFi、跨境支付和交易中扮演重要角色，特别是在 2025 年，稳定币的总市值已超过 2000 亿美元（根据 CoinMarketCap 数据）。

### 主要类型与机制

稳定币的分类基于其稳定机制，以下是四种主要类型及其特点：

| 类型               | 描述                                                                 | 示例         | 优点                                   | 风险                                   |
|-------------------|--------------------------------------------------------------------|-------------|---------------------------------------|---------------------------------------|
| 法币抵押稳定币     | 由银行账户中的法币储备 1:1 支持，需定期审计以确保储备充足             | USDC, USDT  | 稳定性高，透明度较好，广泛接受           | 审计风险，中心化机构信任问题           |
| 加密货币抵押稳定币 | 通过锁定其他加密货币（如 ETH）作为超额抵押，确保价值稳定，通常由智能合约管理 | DAI         | 去中心化，适合 DeFi 生态                | 抵押资产价格波动风险，需高额抵押       |
| 商品抵押稳定币     | 价值与实物商品（如黄金）挂钩，较少见，验证实物储备较困难               | Pax Gold    | 资产多样化，潜在避险功能                | 实物储备验证难度，市场接受度低         |
| 算法稳定币         | 通过算法调整供应量（如增发或销毁）来平衡供需，保持价格稳定，无需实物储备 | Ampleforth, Terra (UST, 已失败) | 去中心化成本低，无需抵押               | 去锚风险高，算法复杂，历史案例失败率高 |

### 应用场景
- **交易与支付**：如 USDT 用于加密货币交易，降低波动风险。
- **DeFi 生态**：如 DAI 作为借贷和收益耕作的稳定资产。
- **跨境汇款**：稳定币因交易速度快、成本低，适合国际转账。

### 当前趋势与争议
截至 2025 年 7 月，超过 90% 的稳定币与美元挂钩（根据 Chainalysis 数据），法币抵押稳定币占主导，但算法稳定币因 Terra 崩溃（2022 年）引发信任危机，监管机构（如 SEC 和 ECB）正加强对稳定币的监管。

## DeFi 借贷协议收益模型

### 背景与定义
DeFi 借贷协议是去中心化金融（DeFi）的重要组成部分，允许用户通过区块链上的智能合约借出或借入加密资产，无需传统金融机构中介。收益模型决定了贷款利率和收益分配，吸引了大量资本流入，2025 年 DeFi 借贷的总锁定价值（TVL）超过 320 亿美元（根据 DefiLlama 数据）。

### 收益模型的关键机制

以下是 DeFi 借贷协议收益模型的主要特点：

- **利率动态调整**：基于供需模型，借入需求高时利率上升，供给高时下降。例如，Aave 和 Compound 使用算法利率模型，利率随市场变化实时调整。
- **利率类型**：提供固定利率（稳定）和浮动利率（随市场波动），用户可根据风险偏好选择。
- **收益耕作（Yield Farming）**：用户将资产存入借贷池，赚取利息，通常以原生代币或奖励形式发放，收益率可能高于传统储蓄账户。
- **超额抵押**：借款人需提供高于贷款价值的抵押（如 150% 的 ETH 抵押以借 DAI），保护贷款人免受价格波动影响。
- **闪电贷款（Flash Loans）**：无需抵押的短期贷款，必须在同一交易块内偿还，常用于套利或交易策略。
- **治理代币**：如 Aave 的 AAVE 代币，持有者可投票决定利率模型、抵押资产类型等，增强社区参与。

### 风险与安全
- **智能合约风险**：协议可能存在漏洞，需定期审计（如 Aave 由第三方审计公司审核）。
- **市场波动**：抵押资产价格下跌可能触发清算，影响借款人。
- **监管不确定性**：2025 年，DeFi 借贷协议面临日益严格的监管，需关注合规性。

### 主要平台示例

| 平台      | 特点                                                                 | TVL (2025 年 8 月) | 利率模型                     |
|-----------|--------------------------------------------------------------------|-------------------|-----------------------------|
| Aave      | 支持多链，固定/浮动利率，闪电贷款功能                                | 约 80 亿美元       | 算法动态调整，供需驱动       |
| Compound  | 使用 cTokens 代表借贷池份额，专注于 ETH 生态                        | 约 50 亿美元       | 基于利用率（Utilization Rate）|
| MakerDAO  | 发行 DAI 稳定币，通过超额抵押 ETH 借贷                              | 约 40 亿美元       | 固定利率，治理决定           |

### 应用场景
- **个人理财**：用户存入稳定币赚取利息，或借入资产进行投资。
- **企业融资**：初创公司通过 DeFi 借贷获得资金，无需传统银行审批。
- **套利交易**：利用闪电贷款进行跨平台套利，赚取价差。

## Uniswap V3 与 V4 比较

### 背景与定义
Uniswap 是一个去中心化交易所（DEX），基于自动做市商（AMM）模型，允许用户交换代币并提供流动性。V3 和 V4 是其主要版本，V4 于 2025 年初上线，旨在提升效率和灵活性。

### Uniswap V3 的特点

Uniswap V3 引入了以下关键功能：
- **集中流动性**：流动性提供者（LPs）可选择特定价格范围提供流动性，提高资本效率，潜在收益提高 54%（根据 SoSoValue 数据）。
- **多级费用**：提供 0.05%、0.3%、1% 的费用层级，LPs 可根据资产波动性选择，优化收益。
- **改进的预言机**：使用时间加权平均价格（TWAP）提高价格准确性，适合波动市场。

### Uniswap V4 的创新

Uniswap V4 在 V3 基础上进行了以下升级：
- **钩子（Hooks）**：允许开发者通过外部合约添加自定义逻辑，如动态费用、自动流动性管理等，截至 2025 年 8 月，已有超过 150 个钩子开发（根据 Uniswap 博客）。
- **单例合约**：所有流动性池由单一 PoolManager 合约管理，创建池成本降低 99%，显著减少燃气费。
- **闪存会计（Flash Accounting）**：支持在单次交易中执行多操作，仅在结束时结算，降低燃气成本。
- **无限费用层级**：开发者可自定义费用，灵活适应市场条件。
- **原生 ETH 支持**：直接交易 ETH，无需包装为 WETH，简化用户体验。

### 比较表

| 特性               | Uniswap V3                              | Uniswap V4                              |
|-------------------|-----------------------------------------|-----------------------------------------|
| 流动性管理         | 集中流动性，价格范围选择                | 钩子增强自定义，自动化策略               |
| 费用结构           | 固定层级（0.05%、0.3%、1%）             | 无限自定义，动态调整                     |
| 合约架构           | 工厂模式，每个池独立合约，成本较高       | 单例合约，降低创建池成本                 |
| 燃气效率           | 较高，频繁状态更新增加费用               | 闪存会计降低费用，支持复杂操作           |
| ETH 支持           | 需要 WETH，增加摩擦                     | 原生 ETH 支持，简化交易                  |
| 开发者灵活性       | 有限，内置功能为主                      | 高，钩子支持创新，如限价订单、波动预言机 |

### 影响与趋势
- **开发者吸引力**：V4 的钩子和低成本设计使其成为 DeFi 开发者的首选，预计将吸引更多项目迁移。
- **流动性迁移**：根据 Keyrock 的预测，V4 推出后，部分流动性可能从 V3 迁移，特别是在高费用和定制需求场景。
- **社区反馈**：V4 的治理更新增强了社区参与，但钩子的复杂性可能对新手 LPs 构成学习曲线。

### 总结与建议
对于 Web3 新手，建议从法币抵押稳定币（如 USDC）开始熟悉，逐步探索 DeFi 借贷协议的收益耕作，关注 Aave 和 Compound 等成熟平台。Uniswap V3 适合初次提供流动性的用户，而 V4 更适合有开发经验的用户，关注钩子和单例合约的潜力。


# 2025.07.31


<!-- Content_END -->
