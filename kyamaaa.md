---
timezone: UTC+8
---

# 杨玉妍

**GitHub ID:** kyamaaa

**Telegram:** @WeChat：wysmshcnxhbc_f

## Self-introduction

最常出现的位置：假期在广东东莞，非假期在广东茂名

## Notes

<!-- Content_START -->

# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
\*\*钱包连接与状态管理\*\*。

处理用户身份（账户）的认证、授权、状态同步以及整个应用的生命周期

\### **Web3 前端核心知识深度总结：钱包连接与状态管理**

\#### **一、 核心概念：为什么这是一个难题？**

在 Web2 中，用户状态通常由服务端的 Session 或客户端的 Cookie/JWT 管理，流程简单统一。在 Web3 中，状态管理的核心变成了\*\*用户的钱包\*\*，这带来了全新的挑战：

1\. **无中心化认证机构**：用户的身份由他们的私钥证明，应用不再负责密码验证，而是请求钱包\*\*签名\*\*来验证所有权。

2\. **动态且不可靠的连接**：用户可能随时连接、断开钱包、切换账户、甚至切换链。前端应用必须能实时响应这些变化。

3\. **多钱包提供商**：用户可能使用 MetaMask、WalletConnect、Coinbase Wallet、Phantom 等数十种钱包。应用需要兼容不同的注入模式。

4\. **状态复杂性**：应用需要同时管理“连接状态”（是否已连接？）、“链状态”（是否在正确的网络上？）和“账户状态”（当前账户是哪个？）。

\#### **二、 技术标准：EIP-1193 与 Provider 抽象**

为了解决多钱包兼容性问题，社区制定了核心标准：

\* **EIP-1193 (以太坊提供商 JavaScript API)**：

\* 它规定了钱包（如 MetaMask）应如何向 `window` 对象注入一个通用的 `window.ethereum` Provider 对象。

\* 这个对象提供了一套标准方法（如 `request({ method: 'eth_requestAccounts' })`) 和事件（如 `accountsChanged`, `chainChanged`）。

\* **重要性**：EIP-1193 是连接各种钱包的\*\*通用接口\*\*`ethers.jsweb3.js` 等库在底层都基于它与钱包通信。

\#### **三、 连接流程深度解析**

**步骤 1：检测 Provider**

// 检查浏览器是否安装了以太坊钱包

// 检查标准的 EIP-1193 provider

// 检查某些老版本钱包或特定环境

// 处理无Provider情况，引导用户安装钱包

**步骤 2：发起连接请求（请求授权）**

// 这是一个关键的用户交互点

// 这会弹出MetaMask窗口，请求用户授权该网站访问其账户

// 授权成功后，返回第一个账户地址

// 用户拒绝了连接请求  
// 将错误抛给上层处理

`eth_requestAccounts`_：_\*会触发授权弹窗\*\*，请求用户明确许可。

`eth_accounts`_：_\*不会触发弹窗\*\*，仅返回之前已被授权访问的账户列表。用于页面加载时静默获取连接状态。

**步骤 3：监听关键事件（核心中的核心）**

仅仅连接成功是远远不够的，必须监听变化以确保状态同步。

// 1. 监听账户切换

// 用户已断开账户连接（如在MetaMask中锁定了钱包或切换了账户）

// 更新应用状态：清空用户数据，提示断开连接

// 用户切换到了新账户

// 更新应用状态：用新账户重新获取余额、NFT等数据

// 2. 监听链切换

// 注意：chainId是十六进制字符串，例如 '0x1'（主网）, '0x5'（Goerli测试网）

// 为了解决某些钱包的bug，最佳实践是直接刷新页面

// 因为旧的链ID可能已被缓存，可能导致交易错误

// 更优雅的做法（不刷新）：

// - 用新的chainId更新所有网络相关的状态

// - 重新初始化合约实例（因为链变了，合约地址可能也不同）

});

// 3. 监听连接断开（某些WalletConnect场景）

// 执行完整的清理工作

});

}

// 重要：在组件卸载时移除监听器，防止内存泄漏

\#### **四、 状态管理架构模式**

在 React、Vue 等现代前端框架中，需要将上述逻辑抽象到全局状态中。

**1\. 自定义 Hook 模式 (React)**

这是当前最流行和灵活的方式。

\`\`\`javascript

// hooks/useWeb3.js

// 页面加载时，静默检查是否已连接

const checkConnection = async () => {

if (window.ethereum) {

const accounts = await window.ethereum.request({ method: 'eth\_accounts' });

if (accounts.length > 0) {

const chainId = await window.ethereum.request({ method: 'eth\_chainId' });

setAccount(accounts\[0\]);

setChainId(chainId);

setIsConnected(true);

}

}

};

checkConnection();

// 设置事件监听

if (window.ethereum) {

const handleAccountsChanged = (accounts) => { /\* ... \*/ };

const handleChainChanged = (newChainId) => { /\* ... \*/ };

window.ethereum.on('accountsChanged', handleAccountsChanged);

window.ethereum.on('chainChanged', handleChainChanged);

// 清理函数

return () => {

window.ethereum.removeListener('accountsChanged', handleAccountsChanged);

window.ethereum.removeListener('chainChanged', handleChainChanged);

};

}

}, \[\]);

return { account, chainId, isConnected, connect, error };

};

\`\`\`

**2\. 状态管理库集成 (Zustand, Redux)**

对于更复杂的应用，可以将 Web3 状态集成到全局 Store 中。

4\. **错误处理人性化**：将底层的 RPC 错误代码（如 `4001`, `-32603`）转换为用户能看懂的消息。

5\. **支持多钱包**：使用 `Web3Modal` 或 `Web3Onboard` 等库可以轻松集成数十种钱包，为用户提供选择，而不是硬编码支持 MetaMask。

\#### **六、 总结**

钱包连接与状态管理是 Web3 前端的基石，其核心在于：

\* **理解 EIP-1193 Provider 模型**。

\* **实现一个能处理动态变化（账户、网络）的响应式系统**。

\* **将 Web3 状态优雅地集成到前端框架的状态管理体系中**。
<!-- DAILY_CHECKIN_2025-08-23_END -->


# 2025-08-22
<!-- DAILY_CHECKIN_2025-08-22_START -->
### **Web3 前端核心知识深度总结：与智能合约交互**

#### **一、 核心概念：为什么交互方式不同？**

与传统 Web2 前端调用后端 API（如 RESTful、GraphQL）不同，Web3 前端与智能合约的交互本质上是与区块链网络（如 Ethereum）的节点进行通信。

1.  **无状态 vs 有状态**：API 调用通常是无状态的，而合约交互是在一个全球共享的、有状态的数据库（区块链）上操作。
2.  **读写分离**：
    *   **读取数据（Call）**：查询合约状态（如用户余额、NFT 所有权）。这是**免费的**、**本地的**，不需要消耗 Gas，也不需要签名，仅仅是与一个节点通信。
    *   **写入数据（Send/Transact）**：改变合约状态（如转账、铸造 NFT）。这需要创建一笔**交易**，经过签名后广播到网络，由矿工/验证者打包，并消耗 Gas。这是一个异步且昂贵的过程。
3.  **Provider 和 Signer**：
    *   **Provider**：提供与区块链网络的只读连接。例如：Infura、Alchemy 或用户自己的节点。它无法发送交易。
    *   **Signer**：一个包含了 Private Key 的 Provider，代表一个以太坊账户，可以对交易进行签名。例如：用户的 MetaMask 钱包。

2.  **合约 ABI (Application Binary Interface)**
    *   **是什么**：一个 JSON 文件，定义了如何与合约进行交互。它就像是合约的“接口文档”或“说明书”，包含了所有可调用的函数、它们的输入输出参数类型、事件等。
    *   **如何获取**：由 Solidity 编译器（如 Hardhat、Truffle）在编译后生成，通常位于 `artifacts/` 或 `build/` 目录下的 `*.json` 文件中。
    *   **重要性**：没有 ABI，前端代码就无法知道如何正确地编码调用数据和解码返回结果。

#### **三、 交互模式深度解析**

##### **模式 1：读取数据 (Call)**

```javascript
// 以 ethers.js v6 为例
import { ethers } from 'ethers';

// 1. 创建 Provider (以连接以太坊主网为例)
const provider = new ethers.JsonRpcProvider('https://mainnet.infura.io/v3/YOUR_PROJECT_ID');

// 2. 合约地址和 ABI
const contractAddress = "0x...";
const abi = [ /* 你的合约 ABI 数组 */ ];

// 3. 创建合约实例（连接到一个Provider，所以是只读的）
const contract = new ethers.Contract(contractAddress, abi, provider);

// 4. 调用只读函数
async function getBalance(userAddress) {
  try {
    // 直接调用合约方法，就像调用一个普通的异步函数
    const balance = await contract.balanceOf(userAddress);
    console.log(`Balance: ${ethers.formatEther(balance)} ETH`);
    return balance;
  } catch (error) {
    console.error('Error reading contract:', error);
  }
}
```

##### **模式 2：写入数据 (Send Transaction)**

```javascript
// 假设用户已安装了 MetaMask，它向 window.ethereum 注入了对象
async function sendTransaction() {
  // 1. 请求用户连接账户
  const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
  const userAddress = accounts[0];

  // 2. 将浏览器注入的 provider 包装成 ethers 的 BrowserProvider
  const provider = new ethers.BrowserProvider(window.ethereum);

  // 3. 获取 Signer（代表用户，有权限签名）
  const signer = await provider.getSigner();

  // 4. 创建合约实例（连接到一个Signer，所以可以发送交易）
  const contractWithSigner = new ethers.Contract(contractAddress, abi, signer);

  // 5. 调用需要写入的函数（例如：mint一个NFT）
  try {
    console.log('Sending transaction...');
    
    // 这是一个异步操作，会弹出MetaMask让用户确认和签名
    const transactionResponse = await contractWithSigner.mintNFT(1, {
      value: ethers.parseEther("0.01") // 发送 0.01 ETH 作为 mint 费用
    });

    console.log(`Tx Hash: ${transactionResponse.hash}`);
    console.log('Waiting for confirmation... (this may take a few minutes)');

    // 6. 等待交易被确认（挖矿完成）
    const receipt = await transactionResponse.wait();

    // receipt 包含交易收据，如区块号、状态等
    if (receipt.status === 1) {
      console.log('Transaction successful!');
      // 更新UI，例如显示新铸造的NFT
    } else {
      console.log('Transaction failed!');
    }

  } catch (error) {
    // 用户可能拒绝交易（Rejected）或 Gas 不足（Insufficient funds）
    console.error('User rejected or error:', error.message || error);
  }
}
```

#### **四、 事件监听 (Events)**

智能合约通过事件来日志记录。前端可以监听这些事件以实现响应式 UI。

```javascript
// 使用合约实例（连接了Provider或Signer均可）
contractWithSigner.on("Transfer", (from, to, tokenId, event) => {
  // 当任何 Transfer 事件发生时，这个回调函数会被触发
  console.log(`NFT #${tokenId} transferred from ${from} to ${to}`);
  // 实时更新UI，例如刷新用户的余额显示
});

// 更精细的监听：过滤特定地址的转账
const filter = contract.filters.Transfer(null, userAddress); // 监听所有转入 userAddress 的转账
contract.on(filter, (from, to, tokenId) => { /* ... */ });

// 重要：在组件卸载时取消监听，避免内存泄漏
// (例如在 React 的 useEffect 清理函数中)
function cleanup() {
  contract.removeAllListeners('Transfer');
}
```

#### **五、 最佳实践与常见陷阱**

1.  **错误处理**：
    *   必须用 `try...catch` 包裹所有异步调用。
    *   区分用户拒绝（`ACTION_REJECTED`）、RPC 错误、合约执行失败（`revert`）等不同情况，给用户友好的提示。

2.  **状态管理**：
    *   交易发送后，在等待确认期间，UI 应显示“等待中”的状态。
    *   交易成功后，应及时更新本地应用状态（如 React 的 `useState`），而**不要**立即从链上重新读取所有数据（以减少不必要的 RPC 调用）。

3.  **Gas 优化**：
    *   对于写入操作，可以让用户选择 Gas Price（或使用 `maxPriorityFeePerGas`）。
    *   可以使用 `estimateGas` 方法预估交易消耗，提前告知用户可能的手续费。

4.  **性能优化**：
    *   对于频繁读取的数据，可以考虑使用 **SWR** 或 **React Query** 进行缓存和重复数据删除。
    *   使用 **Multicall**（通过一个合约批量调用多个视图函数）来减少网络请求次数。

5.  **安全考虑**：
    *   **合约地址和 ABI**：确保前端引用的合约地址是正确的（主网 vs 测试网），ABI 与部署的合约版本匹配。
    *   **源验证**：在 Etherscan 等浏览器上验证合约源码，增加透明度。
    *   **防呆设计**：在用户操作前进行预检查，例如检查用户的 ETH 余额是否足够支付 Mint 费用和 Gas。

#### **六、 总结**

智能合约要求开发者理解区块链的基本原理（交易、Gas、状态），并熟练使用 `ethers.js` 等库提供的 `Provider`、`Signer` 和 `Contract` 抽象。

能处理各种边缘情况（如交易失败、网络切换、钱包断开连接），提供流畅、安全、用户友好的体验，并运用缓存、批量查询等技巧来优化性能
<!-- DAILY_CHECKIN_2025-08-22_END -->

# 2025-08-20

Provider (提供者): 一个只读的连接以太坊网络的抽象。它用于读取区块链状态（如余额、区块号、合约数据），但无法修改状态或发送交易。例如：JsonRpcProvider, AlchemyProvider, InfuraProvider。

Signer (签名者): 一个可以签名交易和消息的抽象，通常代表一个以太坊账户（私钥、助记词或连接的钱包如 MetaMask）。它用于写入操作（发送交易、与合约交互）。例如：Wallet, JsonRpcSigner（来自 provider.getSigner()）。

Contract (合约): 一个与部署在区块链上的智能合约交互的抽象。它需要合约地址（address）和应用程序二进制接口（ABI）来创建。

核心关系： Signer = Provider + 操作权限（私钥）。一个 Signer 本身就包含一个 Provider 的功能。

# 2025-08-18

1.学习虚拟渲染并运用在订单列表上 2.了解PostCSS、css-modules、less，实现生成唯一类名的流程 3.继续调整样式 4.学习grid布局 
构思web3项目：打算选择接单平台或者学习教育平台

# 2025-08-17

DApp 创意与学习笔记（新增前端方向内容）
一、DApp 创意：去中心化的音乐版权交易平台
（一）创意背景
在传统音乐产业中，音乐版权交易存在诸多问题，如版权归属不清晰、交易流程繁琐、中间环节过多导致创作者收益受损等。区块链技术的出现为解决这些问题提供了新的思路，通过创建一个去中心化的音乐版权交易平台 DApp，可以实现音乐版权的透明化管理和高效交易。
（二）功能设计
版权登记：音乐人可以在平台上上传自己的音乐作品，并通过智能合约进行版权登记。智能合约会记录作品的详细信息，包括创作者信息、创作时间、作品内容哈希值等，确保版权归属明确且不可篡改。
版权交易：版权所有者可以在平台上设定版权的出售或授权条款，包括价格、使用范围、使用期限等。其他用户或音乐机构可以浏览版权市场，选择感兴趣的版权进行购买或授权。交易过程通过智能合约自动执行，资金和版权权益的转移即时完成。
收益分配：当音乐作品被使用产生收益时（如播放分成、广告收入等），平台会根据智能合约中设定的收益分配规则，自动将收益分配给版权所有者和相关参与者。例如，创作者可以设定自己与制作人、混音师等的分成比例。
音乐播放与推广：平台集成音乐播放功能，用户可以在平台上收听经过授权的音乐作品。同时，引入激励机制，鼓励用户分享和推广音乐，分享者可以获得一定的奖励（如平台代币）。
版权追溯与维权：由于所有版权信息和交易记录都存储在区块链上，一旦发生版权纠纷，可以方便地追溯版权的流转历史和交易细节，为维权提供有力证据。
（三）技术实现要点
区块链选择：以太坊区块链
智能合约编写：使用 Solidity 语言编写版权登记、交易、收益分配等核心智能合约。确保智能合约的安全性和准确性，进行充分的测试和审计。
存储方案：对于音乐作品的存储，可以结合 IPFS（星际文件系统），将音乐文件存储在分布式网络中，通过哈希值在区块链上关联和索引，确保数据的长期保存和可用性。
二、DApp 开发学习笔记
（一）智能合约基础
Solidity 语言：
Solidity 是以太坊智能合约的主要编程语言，是一种面向对象的静态类型语言。对于有编程经验，特别是熟悉 TS、Java 等基于类的编程语言的人来说，有一定的相似性，便于学习。
例如，contract关键字类似于class，用于定义合约，一个合约相当于一个类。状态变量用于存储合约内的数据，如同类的成员变量。函数可以定义在合约内部（类似类方法）或外部（普通工具函数）。
访问修饰符private（私有）、public（公开）语义与其他语言相同，不过没有protected，取而代之的是internal（内部），还有一个external表示仅供外部调用。
函数修饰符类似于 TS 装饰器或 Java 注解，可用于面向切面编程（AOP），且函数与函数修饰符可被衍生合约覆盖。
Solidity 支持枚举（enum，有限选项集合）和映射（mapping，无限选项），都可看作是 ES 中的对象，但使用场景不同。同时，它支持多重继承与函数多态性，有助于代码的组合复用，不过由于合约开发受 ERC 驱动，多重继承的副作用相对较小。
ERC 标准：
在以太坊中，“ERC” 全称为 “Ethereum Request for Comments”，是 EIP（Ethereum Improvement Proposal）的一种类型，定义了智能合约应用程序的标准和约定。它对于保障智能合约应用程序的互操作性至关重要。
常见的基本 ERC 标准有 ERC - 20（同质化代币）和 ERC - 721（非同质化代币，即 NFT）。ERC - 20 可作为类金融系统的基础设施，如虚拟货币、贡献积分等；ERC - 721 可作为身份系统的基础设施，如勋章、证书、门票等。开发者可将 ERC 看作权威的 API 文档，在开发相应代币合约时遵循其标准。
（二）开发工具
Hardhat：
contracts：存放智能合约源码。
artifacts：通过hardhat compile命令生成的编译后文件。
ignition：基于 Hardhat Ignition 部署智能合约时使用，这里生成的编译后文件与目标链绑定，存放在对应链 ID 的文件夹下。
test：用于编写智能合约功能测试代码。
在hardhat.config.js文件中可自定义测试网并进行部署，通过--network testnet指定网络。还可添加插件来扩展功能，如require('hardhat - abi - explore')，安装并配置后，使用npx hardhat abi:explore命令可生成 ABI 目录。
Remix IDE：
Remix 是以太坊官方提供的在线 IDE，支持编写合约、部署合约（可在正式网络、测试网和本地测试网络进行）、合约 Debug 以及合约测试。
其默认环境是 VM（Virtual Machine）环境，若要使用公链测试网络，需借助钱包
（三）测试驱动开发
概念：测试驱动开发（Test Driven Development，TDD）是一种不同于传统开发流程的方法。它要求在编写功能代码之前先编写测试代码，然后编写仅使测试通过的功能代码，通过测试来推动整个开发过程。
优势：
降低开发者负担：在编写代码前明确代码的预期行为，减少开发过程中的不确定性。
保护网：确保代码在后续修改和扩展时，原有的功能不受影响，起到保护代码的作用。
提前澄清需求：在编写测试用例的过程中，能更深入理解需求，避免需求理解偏差导致的开发错误。
快速反馈：开发者能快速得知代码是否符合预期，及时调整开发方向。
实施要点：
分析问题并拆分：将复杂问题分解为一个个可操作的小任务，便于逐个实现和测试。
代码设计：对功能的实现进行规划和设计，确定代码结构和逻辑。
编写测试预期结果：使用测试框架（如在 Hardhat 项目中使用 Mocha 和 Chai），通过expect(xx).to.equal("??")等方式明确每个功能点的预期输出。
（四）NFT 交易市场合约开发要点
开发准备：可借助 ERC 标准智能合约生成巫师（如https://docs.openzeppelin.com/contracts/5.x/wizard ）来生成基础的 NFT 合约。同时，了解solcjs和hardhat在管理 Solidity 编译版本上的区别，solcjs是独立的 Solidity 编译器的 JavaScript 包装器，需手动安装和管理版本；hardhat是开发框架，集成了 Solidity 编译器，可通过配置文件轻松管理版本，更适合大型项目。
合约设计考虑：在设计 NFT 交易市场合约时，需思考诸多问题，如市场中的 NFT 列表是否分页，不分页在 NFT 数量多时影响性能和用户体验，分页则会有翻页延迟问题；NFT 的图片 URL 存储位置，若存于 NFT 合约，获取列表时频繁外部调用影响性能，存于市场合约又可能存在数据一致性问题；是否在 NFT 合约中维护 “谁拥有哪些代币” 的列表，有则数据冗余，无则难以直观知晓代币归属情况。
测试与优化：在 Remix 部署合约后，先利用其手动测试按钮测试基本功能，再使用 Hardhat 结合 js 代码进行更全面的测试。还可使用一些工具进行代码优化，如安装并使用truffle - flattener将合约及其依赖代码合并到一个文件中，便于管理和部署。
UI 组件库：Ant Design、Element UI 
钱包连接模块：
 Ethers.js 提供的 API 检测用户是否安装钱包
连接成功后，获取用户的账户地址、余额等信息，并在界面上展示。同时，监听钱包账户切换、网络切换等事件，及时更新界面状态。
示例代码（使用 Ethers.js 与 MetaMask 连接）：
音乐上传与版权登记模块：
设计上传表单，允许音乐人选择本地音乐文件，并填写作品名称、创作者信息、版权说明等内容。
实现文件分片上传至 IPFS 的功能，可使用ipfs-http-client库，将文件上传后获取文件的哈希值。
将作品信息和 IPFS 哈希值通过智能合约进行版权登记，调用合约的registerCopyright函数，传入相关参数，并等待交易确认。
上传过程中显示进度条，提升用户体验，交易确认后给予成功提示。
版权交易模块：
展示版权市场列表，包括音乐作品名称、创作者、价格、授权期限等信息，可实现分页、筛选、排序等功能。
点击购买或授权按钮时，调用智能合约的交易函数，传入版权 ID、价格等参数，使用用户钱包签名交易并发送至区块链。
交易过程中显示加载状态，交易成功后更新界面的交易状态和用户的版权持有情况。
音乐播放模块：
集成音乐播放器组件，如react-audio-player（React）或vue-audio（Vue），通过 IPFS 哈希值构建音乐文件的 URL，实现音乐的播放、暂停、进度调整等功能。
只有获得授权的用户才能播放相应的音乐作品，前端需验证用户的版权持有情况，未授权用户则隐藏播放按钮或提示购买授权。

# 2025-08-16

## 同学们的分享

一位同学做的稳定币笔记
https://github.com/IntensiveCoLearning/Web3_Internship_Program/blob/main/Sacultor.md

分享个播客，关于web3行业/事件/商业人文的，内容质量不错
​https://web3101.fireside.fm/

https://www.bilibili.com/video/BV1154y1t7Jd/?buvid=Y14ADA123A7FEFFB4BA086AA94AAD1182721&from_spmid=search.search-result.0.0&is_story_h5=false&mid=JGUEnNi2wMDvHJTD1ILz4w%3D%3D&p=5&plat_id=116&share_from=ugc&share_medium=iphone&share_plat=ios&share_session_id=5DDE7736-0231-4D06-B29A-2BD80E687217&share_source=WEIXIN&share_tag=s_i&spmid=united.player-video-detail.0.0&timestamp=1754628669&unique_k=0h9SqfN&up_id=133578883
区块链技术



昨天看了下 debugger 相关的，比较高阶，送给有兴趣的朋友 https://github.com/IntensiveCoLearning/Web3_Internship_Program/blob/main/brucexu-eth.md#forge-debugger


整理了一个我的学习仓库，基本是一些学习记录，还有b站肖臻老师课程的笔记整理，有个roadmap可以看看，看完应该能大概有个学习方向，希望对大家有点点帮助：https://github.com/B1u-e/My-Web3-Journey

# 2025-08-14

一、Gas 优化技巧学习与实践
（一）常见 Gas 优化技巧
数据类型优化：尽量使用较小的数据类型，例如在不需要大数值时，使用 uint8 而非 uint256，减少存储和计算的 Gas 消耗。
存储优化：将频繁访问的状态变量设为 memory 类型，减少对 storage 的读取；合理安排状态变量的存储顺序，利用存储槽的打包机制，减少存储槽的使用数量。
函数优化：避免在循环中进行复杂的操作和外部调用；将重复的代码逻辑提取为内部函数，减少代码冗余。
修饰符与可见性：合理使用函数可见性修饰符（如 private、internal），限制函数的访问范围，降低 Gas 消耗；谨慎使用复杂的修饰符，避免增加额外的 Gas 成本。
（二）Gas 消耗对比记录
优化前：以一个简单的代币转账合约为例，未进行任何优化时，一次转账操作的 Gas 消耗约为 45000。
优化后：
使用 uint128 代替 uint256 存储余额，Gas 消耗降至约 43000。
调整状态变量存储顺序，使多个变量打包到一个存储槽，Gas 消耗进一步降至约 41000。
优化函数逻辑，减少不必要的计算，Gas 消耗最终降至约 39000。
二、部署 DApp 到 Vercel 和 Sepolia 测试网
（一）部署到 Vercel
确保项目已初始化 Git 仓库，并关联到 GitHub 等代码托管平台。
登录 Vercel 账号，导入项目仓库。
配置项目参数，如构建命令、输出目录等（对于前端项目，通常构建命令为 npm run build，输出目录为 build）。
点击部署按钮，Vercel 会自动构建并部署项目，部署完成后可获得一个访问域名。
（二）部署到 Sepolia 测试网
准备工作：获取 Sepolia 测试网的 ETH（可通过测试网 faucet 领取）；配置 Hardhat 网络参数，添加 Sepolia 测试网的节点信息（如 Alchemy 或 Infura 提供的 API 密钥）。
在项目根目录下运行部署命令：npx hardhat run scripts/deploy.js --network sepolia。
部署完成后，记录合约地址等信息，以便在前端项目中进行调用。
三、编写项目文档和使用说明
技术栈：前端框架（React）、智能合约开发框架（Hardhat）、区块链网络（Sepolia 测试网）
合约说明：详细描述智能合约的功能、主要函数、事件和状态变量。
前端架构：说明前端项目的目录结构、组件设计和状态管理方式。
（二）使用说明内容
环境要求：浏览器版本、需要安装 MetaMask 钱包插件。
操作步骤：
如何连接钱包到 DApp。
如何进行核心功能操作（如转账、 mint 代币等）。
四、可选：DApp 运营方案设计（非 Demo 类型）
（一）目标用户定位
明确 DApp 的目标用户群体，如加密货币爱好者、NFT 收藏者、去中心化金融参与者等。
（二）推广策略
社交媒体推广：在 Twitter、Discord、Telegram 等区块链相关社群发布 DApp 的介绍和使用教程，吸引用户关注。
合作伙伴合作：与其他区块链项目、钱包厂商或媒体合作，进行联合推广，扩大影响力。
激励活动：开展早期用户激励活动，如赠送测试网代币、NFT 等，吸引用户体验 DApp。
（三）用户留存与活跃度提升
功能迭代：根据用户反馈，持续优化 DApp 的功能和用户体验，增加用户粘性。
社区建设：建立用户社区，鼓励用户交流和分享，组织线上线下活动，增强用户的参与感。
经济模型设计：设计合理的经济模型，激励用户积极参与 DApp 的生态建设，如通过使用 DApp 获得奖励等。

这个是我的大致规划，但是只完成到部署测试网

# 2025-08-13

一、智能合约安全基础学习
（一）常见漏洞及复现
重入攻击
原理：攻击者利用合约调用外部合约时的漏洞，在外部合约中再次调用原合约的函数，从而重复获取资产。
复现过程：模拟一个简单的转账合约，当合约向外部地址转账时，未先更新状态变量，攻击者创建恶意合约，在接收转账的 fallback 函数中再次调用原合约的转账函数，导致多次转账。
整数溢出 / 下溢
原理：当整数达到其数据类型的最大值或最小值时，继续进行增减操作会导致数值异常。
复现过程：编写一个使用 uint8 类型的计数器合约，当计数器达到 255 时，再进行加 1 操作，数值变为 0，体现整数溢出；当计数器为 0 时，进行减 1 操作，数值变为 255，体现整数下溢。
权限管理漏洞
原理：合约中未合理设置访问权限，导致未授权用户可以执行敏感操作。
复现过程：创建一个带有修改重要参数函数的合约，但该函数未设置只有管理员可调用的权限，普通用户调用后成功修改参数。
（二）最佳安全实践
采用最小权限原则，为不同的函数和操作设置严格的访问权限，只允许必要的角色进行操作。
避免在合约中使用复杂的逻辑和不必要的外部调用，减少攻击面。
对用户输入进行严格验证和过滤，防止恶意数据注入。
定期对合约进行安全审计和漏洞扫描，及时发现和修复安全问题。
二、OpenZeppelin 合约学习与引入
（一）OpenZeppelin 合约简介
OpenZeppelin 是一个开源的智能合约库，提供了一系列经过安全审计和测试的合约，包括 ERC20、ERC721 等标准代币合约，以及安全相关的合约如 ReentrancyGuard、Ownable 等。
（二）引入 OpenZeppelin 合约到项目
在项目中安装 OpenZeppelin 库，可通过 npm 命令：npm install @openzeppelin/contracts。
在自己的合约中通过 import 语句引入所需的 OpenZeppelin 合约，例如：import "@openzeppelin/contracts/token/ERC20/ERC20.sol";。
继承引入的合约，从而使用其提供的功能，例如：contract MyToken is ERC20 {...}。
三、编写测试用例
（一）测试工具选择
使用 Hardhat 作为开发环境，搭配 Chai 断言库进行测试用例的编写。
（二）测试用例内容
测试代币转账功能：创建代币合约实例，进行转账操作，验证转账前后账户余额的变化是否正确。

    // 转账100个代币从owner到addr1
    await token.transfer(addr1.address, 100);
    expect(await token.balanceOf(addr1.address)).to.equal(100);

    // 转账50个代币从addr1到addr2
    await token.connect(addr1).transfer(addr2.address, 50);
    expect(await token.balanceOf(addr1.address)).to.equal(50);
    expect(await token.balanceOf(addr2.address)).to.equal(50);
  });
});

测试权限管理功能：验证只有管理员可以执行特定操作，普通用户无法执行。

  // 管理员执行操作成功
  await expect(contract.adminOperation())
    .to.emit(contract, "OperationPerformed")
    .withArgs(owner.address);

  // 普通用户执行操作失败
  await expect(contract.connect(addr1).adminOperation())
    .to.be.revertedWith("Not authorized");
});

测试防重入功能：模拟重入攻击场景，验证引入 ReentrancyGuard 后的合约是否能有效防止重入攻击。

  // 向易受攻击的合约存入一些以太币
  await owner.sendTransaction({ to: vulnerableContract.address, value: ethers.utils.parseEther("1.0") });

  // 攻击者尝试重入攻击
  await expect(attackerContract.attack())
    .to.be.revertedWith("ReentrancyGuard: reentrant call");
});

四、使用 Slither 进行安全检查及修复
（一）Slither 工具安装
通过 pip 命令安装 Slither：pip install slither-analyzer。
（二）使用 Slither 进行检查
在项目根目录下运行命令：slither .，Slither 会对合约进行静态分析，输出安全问题报告。
（三）修复安全问题
根据 Slither 的报告，对发现的安全漏洞进行修复。例如，若发现存在重入漏洞，则引入 OpenZeppelin 的 ReentrancyGuard 合约，并在相关函数上添加 nonReentrant 修饰符。

# 2025-08-12

完成前端页面与合约的交互，并将代码上传到 Git 仓库，以此完善整个小 Demo 的开发流程。
二、创建简单的前端页面与合约交互
（采用 HTML、CSS 和 JavaScript 进行简单的前端页面开发，同时使用 Ethers.js 库来实现与智能合约的交互。Ethers.js 是一个轻量级的以太坊库，对于前端与智能合约交互非常友好。
（二）搭建前端基础页面
创建一个新的文件夹frontend作为前端项目目录，在该目录下创建index.html文件。
在index.html中编写基础的页面结构，包括一个显示合约返回信息的区域和一个用于触发交互的按钮，代码如下：
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello World DApp</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-top: 50px;
        }
        #greeting {
            margin: 20px 0;
            padding: 10px;
            border: 1px solid #ccc;
        }
        button {
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <h1>Hello World DApp</h1>
    <div id="greeting"></div>
    <button onclick="fetchGreeting()">Get Greeting</button>

    <script src="https://cdn.ethers.io/lib/ethers-5.2.umd.min.js"></script>
    <script src="app.js"></script>
</body>
</html>

（三）编写与合约交互的 JavaScript 代码
在frontend目录下创建app.js文件。
在app.js中编写代码，实现连接到测试网、加载合约实例以及调用合约方法的功能，代码如下：
// 合约地址（需要替换为你部署到测试网的HelloWorld合约地址）
// 合约ABI（从Foundry编译后的合约文件中获取）
    // 检查浏览器是否安装了MetaMask等钱包
            // 请求用户授权连接钱包
            // 加载合约实例
            // 调用合约的greeting方法
            // 在页面上显示结果
ABI 可以从 Foundry 项目的 out 目录下对应的合约 JSON 文件中提取。
（四）测试前端与合约交互
将 HelloWorld 合约部署到测试网（使用 Foundry 的forge create命令进行部署，具体命令需根据测试网配置调整）。
替换app.js中的合约地址为部署后的实际地址。
在浏览器中打开index.html文件，确保已安装 MetaMask 并切换到对应的测试网，点击 “Get Greeting” 按钮，授权后即可看到合约返回的 “Hello, World!” 信息，说明前端与合约交互成功。
三、创建 Git 仓库并上传代码
在项目根目录（hello-world）下打开终端，执行git init命令，初始化一个新的 Git 仓库。
.gitignore 文件
node_modules/
out/
cache/
.DS_Store
.env

执行git add .命令，将项目中的所有文件添加到暂存区。
执行git commit -m "Initial commit: Hello World DApp with Foundry and frontend"命令，提交暂存区的文件到本地仓库。
在 GitHub 等代码托管平台创建一个新的远程仓库。
按照平台提示，在终端执行关联远程仓库的命令，例如：git remote add origin https://github.com/yourusername/hello-world-dapp.git。
执行git push -u origin main命令，将本地仓库的代码上传到远程仓库。

掌握了 DApp 的基本开发流程，包括智能合约编写、开发环境搭建、前端与合约交互以及代码管理

# 2025-08-11

今日开始学习 web3intern.xyz 上关于智能合约开发的技术方向手册，重点关注与前端相关的智能合约交互部分。通过手册初步了解到，智能合约开发与前端开发存在紧密联系，前端需要与智能合约进行数据交互以实现 DApp 的功能。​

二、Solidity 语法学习​
（一）基本概念​
Solidity 是一种用于编写智能合约的高级编程语言，语法类似于 JavaScript，它是面向合约的，运行在以太坊虚拟机（EVM）上。​
（二）关键语法点​
合约声明：使用contract关键字声明一个合约，例如contract HelloWorld {}。​​
其中memory关键字表示参数在函数执行结束后会被销毁。​
4. 数据类型：包括整数型（int/uint，有不同位数）地址（address）等。地址类型比较特殊，用于表示以太坊账户。​
三、开发环境搭建（Foundry）​
（一）Foundry 简介​
Foundry 是一个用于以太坊应用开发的工具链，包含编译器、测试框架、部署工具等，对于开发和测试智能合约很方便。​
（二）安装步骤​
打开终端，执行安装命令：
curl -L https://foundry.paradigm.xyz | bash​
​
安装完成后，运行foundryup来更新到最新版本，确保工具链是最新的。​
验证安装​ forge --version
四、DApp 的架构和运行方式初步了解​
（一）DApp 架构​
DApp 主要由前端界面和后端智能合约两部分组成。前端负责与用户交互，展示数据和接收用户操作；智能合约部署在区块链上，负责处理核心业务逻辑和数据存储。前端通过 RPC（远程过程调用）与区块链节点进行通信，从而与智能合约进行交互。​
（二）运行方式​
用户在前端进行操作后，前端将操作转化为对智能合约的调用请求，通过区块链节点发送到区块链网络。智能合约在区块链上执行相应的逻辑，处理结果会被记录在区块链上，前端再从区块链节点获取处理结果并展示给用户。​
五、实践：使用 Foundry 编写第一个 Hello World 合约​
（一）创建项目​
在终端进入想要创建项目的目录，执行forge init hello-world，创建一个名为 hello - world 的 Foundry 项目。​
（二）编写合约​
进入项目的 src 目录，创建HelloWorld.sol文件，编写如下合约代码：​
​
// SPDX-License-Identifier: MIT​
pragma solidity ^0.8.0;​
​
contract HelloWorld {​
    string public greeting = "Hello, World!";​
}​
​
// SPDX-License-Identifier: MIT指定了许可证；
pragma solidity ^0.8.0指定了 Solidity 的版本；
合约中定义了一个公共的字符串状态变量greeting，并初始化为 “Hello, World!”。​
（三）编译合约​
在项目根目录下，执行forge build命令编译合约，会在 out 目录下生成编译后的合约文件。

# 2025-08-09

规划前端技术栈和方向

# 2025-08-08

- #### 前端找到intern需要准备的技术
		- 我需要学的技术栈：
			- React   HTML5、CSS3、JavaScript（ES6+）
			- 基于 GraphQL 或 RESTful API 获取链上和链下数据
			- Viem 与智能合约进行交互
			- DApp开发经验
		- 学习路线：
			- 区块链基础概念
			- 以太坊开发工具链
				- viem库的学习
					- 学习连接钱包、读取链上数据、发送交易
					- 读取和写入智能合约
				- 开发环境
					- Hardhat 或 Foundry
					- Alchemy/Infura 节点服务
				- 工具
					- Etherscan API
					- 区块浏览器使用
			- 项目实践：打算做出以下三个项目并且发布到GitHub上
				- 代币余额查看器
					- React + Viem
					- 用etherscan api 实现交易历史展示
				- 开发一个 NFT 展示和转账 DApp
					- Next.js + Viem + TailwindCSS
					- 理解 nft 标准差异
					- 写一个简单的 NFT 合约（用 Hardhat）并与之交互
				- 构建一个 DeFi 协议前端 (如简单的借贷或交换界面)
					- React + Viem + Hardhat
				- DApp开发
					- Next.js + Viem + GraphQL
			- **避坑提示**：遇到 `ethers.providers.Web3Provider` 或 `web3.eth` 这类代码的仓库直接跳过，它们已经过时。

# 2025-08-07

- #### 行业赛道
		- DeFi（decentralized finance）
			- 协议
				- 借贷
					- Aave 
				- DEX - 去中心化交易所
					- Uniswap
						- 代币定价 - 依赖恒定乘积公式
					- Compound
						- 借贷
					- MakerDAO (Sky) 
						- 稳定币系统，抵押
		-   ---------------DeFi + NFT---数字资产金融化------------
		-   ---------------DeFi + AI-----智能化金融服务------------
		-   ---------------Web3 + 乡建-南塘DAO-------------------
		- NFT
			- 智能合约
				- 一种自执行的协议，意味着合约中的条款在满足特定条件时会自动执行，而无需第三方中介
				- 为NFT在转售时，原作者收版税的过程中，不需要第三方收额外的钱
				- CryptoPunks
					- 由Larva Labs创建
			- OpenSea
				- NFT交易平台
		- DAO —— 去中心化自治组织
			- Nouns DAO
				- 通过贩卖NFT，让买家的钱成为组织的启动资金，并且让买家投票决定资金的用处的组织
			- LXDAO
				- 工会 加 奖励平台
			- ConstitutionDAO
				- 拍卖会


		- 我需要学的技术栈：
			- React   HTML5、CSS3、JavaScript（ES6+）
			- 基于 GraphQL 或 RESTful API 获取链上和链下数据
			- Viem 与智能合约进行交互
			- Dapp开发经验

# 2025-08-06

- #### 行业赛道
		- DeFi（decentralized finance）
			- 协议
				- 借贷
					- Aave 
				- DEX - 去中心化交易所
					- Uniswap
						- 代币定价 - 依赖恒定乘积公式
					- Compound
						- 借贷
				- 衍生品
					- dYdX
				- RWA
	- NFT
		- OpenSea
		- 社交、游戏资产，如 Axie
	- Layer2扩容方案
		- 烂大街 雪崩  空投
	- 基础设施
		- 跨链桥 Wormhole D。。
	- 外部竞争/公链
		- Solana
		-  雪崩
	- 稳定币
		- USDT
		- USDC
		- DAI
	- 去中心化金额
		- 组池子，放贷，空投，链上测试LP，借贷
	- 闪电贷
		- ==很有意思，不成功就会回滚==
	- uniswapp交互
		- 推荐okx而不是metamask
		-  闪电贷套利  
			发布土狗合约  
			闪兑
		- v3和v4的差别
			- v4提出brollprotical
			- 所有流动性变成一个池子，
		- 做测试网
		- 熊链

# 2025-08-05

- #### 以太坊
		- 和比特币的区别：
			- 比特币是区块链1.0，货币属性
			- 以太坊是2.0，智能合约和可编程性
				- Solidity开发智能合约
			- 区块时间由10min -> 12sec
			- ==通胀 -> 通缩==
			- The Merge：PoW -> PoS
				- PoW 依靠计算机算力挖矿，消耗电力
				- PoS   32ETH - 质押 - 验证者
		- 特点：灵活
		- 升级方向：
			- 分片
				- Layer2扩容 - 数据分片 - EIP-4844
					- L2使用常规交易，**EIP-4844引入Blob交易类型**，数据存储成本降低
				- 全面分片预计 **2025-2026 年** 启动，重点是 Proto-Danksharding
			- ZK-Rollup
				- 证明
			- 其他：
				- Verkle树技术：优化状态存储结构，减少节点同步所需的数据量
				- 执行环境优化：提升 EVM 性能，支持更复杂的智能合约应用
					- EVM 以太坊虚拟机
		- 生态概览：
			- 应用层 Application Layer
				- 用户直接交互
				- **DeFi 应用**：Uniswap（去中心化交易所）、Aave（借贷协议）、Compound（借贷协议）
				- **NFT 平台**：OpenSea、Foundation、SuperRare
				- **钱包应用**：MetaMask、Coinbase Wallet、Rainbow
				- **DAO 工具**：Snapshot、Aragon、Colony
			- 协议层 Protocol Layer
				- **共识层客户端**：Prysm、Lighthouse、Nimbus、Teku
				- **执行层客户端**：Geth、Nethermind、Erigon、Besu
				- **核心协议**：EVM、状态管理、Gas 机制
			- 扩展层 Scaling Layer  
				- 提升性能、降低成本
					- **Layer 2 Rollups**：Arbitrum、Optimism、Polygon zkEVM、zkSync Era
					- **侧链**：Polygon PoS、xDAI（Gnosis Chain）
					- **状态通道**：Lightning Network for Ethereum
		- 核心机制
			- 账户系统
				- 私钥控制 EOA
				- 智能合约控制 CA
			- Gas 模型
				- 激励矿工/验证者
				- ==矿工就是验证者吗？==
				- 基础费用用来帮助ETH通缩，会消失
				- ==怎么理解通缩？==
			- 以太坊虚拟机 EVM
				- 运行智能合约的虚拟计算机

# 2025-08-04

# 学习内容：
#### 简介：1、看了入门导读之区块链的基础概念，做了笔记    2、下载并注册了web3常用的应用程序，使用metamask实现了链上代币测试   

#### 相关图片佐证：
https://postimg.cc/T5gY5rBv
https://postimg.cc/jD50GHvq
https://postimg.cc/kRgPqW9y

#### 关于图片的笔记：
交易哈希：
- 区块链上这笔交易的唯一ID
区块：
- 这个交易所在的区块高度
Gas价格：
- 交易中每个执行单元的花费（用ether和gwei做单位），Gas价格越高，被写到区块中的机会越大。

笔记：
- ### 概念
	- #### 区块
		- 每一个区块只有有限的存储容量。
		- 每个区块会在一定时间内打包， 10 分钟打包生成一个区块。
		- **核心特性**
			- 去中心化
			- 公开透明（但匿名）
				- 钱包是随机生成的
			- 快速交易
			- 不可篡改，其实可以篡改，但是很难很难，因为区块链是依照由节点串联起来的，若有人想要去篡改，被修改记录的节点不超过 51%，这个改动将不会被认可。
				- 网络节点服务提供商可以得到比特币的奖励
					- 比特币的特性：
						- 白皮书设计，价格由供需关系决定：
						- 不通胀，不稳定，价格高
			- 智能合约
				- 以太坊 V神  (ethereum)
			- 共识机制
				- ![[Pasted image 20250804195248.png]]
				- PoW 
				- PoS
					- 单独质押  ethereum.org/zh/staking/solo/
						- 32个以太币 验证者  节点记账
					- 服务质押
						- 第三方机构背书
					- 联合质押
				- EOS
				- ==？？：所以这几个之间有什么关系==
		- **核心组成：
			- 节点通过计算验证交易获得代币，无数节点提供的服务统称为挖矿，维持服务的人叫矿工
			- 步骤：
				1. **用户发起交易**：用户通过钱包应用发起转账、智能合约调用等操作
				2. **交易广播**：交易信息被广播到**整个网络中的各个节点**
				3. **节点验证**：网络中的矿工节点验证**交易的合法**性（余额是否足够、签名是否正确等）
				4. **打包成块**：通过共识机制（如工作量证明），矿工将验证过的交易**打包成新的区块**
				5. **链接上链**：新区块被添加到区块链上，更新全网的账本状态
				6. **奖励发放**：成功打包区块的矿工获得代币奖励和交易手续费
	- #### 公链 vs 私链 vs 联盟链
		- 不同点：成为节点的方法 - 管理数据的模式
		- 公链
			- 去中心化，共识机制，透明，效率低成本高
		- 联盟链
			- 多中心化，半公开，节点邀请制，成员权限分级
		- 私链
			- 中心化
	- #### Web3 vs Web3.0 vs Web2
		- Web3.0    语义网驱动
			- 知识图谱、语义搜索
			- RDF、OWL 描述数据关系
			- 区块链混合存储，部分开放
		- Web3        区块链驱动
			- 去中心化金融
			- 智能合约
			- 区块链存储 IPFS
			- ![[Pasted image 20250804220323.png]]
			- 技术需求
				- React + Ethers.js + Solidity/Rust + IPFS
	- 以太坊
	- 以太坊生态赛道
	- DeFi（finance）
		- 协议
			- 借贷
				- Aave 
			- DEX
				- Uniswap 交易所  代币
				- Curve
				- h
			- 衍生品
				- dYdX
			- RWA
	- NFT
		- OpenSea
		- 社交、游戏资产，如 Axie
	- Layer2扩容方案
		- 烂大街 雪崩  空投
	- 基础设施
		- 跨链桥 Wormhole D。。
	- 外部竞争/公链
		- Solana
		-  雪崩
	- 稳定币
		- USDT
		- USDC
		- DAI
	- 去中心化金额
		- 组池子，放贷，空投，链上测试LP，借贷
	- 闪电贷
		- ==很有意思，不成功就会回滚==
	- uniswapp交互
		- 推荐okx而不是metamask
		-  闪电贷套利  
			发布土狗合约  
			闪兑
		- v3和v4的差别
			- v4提出brollprotical
			- 所有流动性变成一个池子，
		- 做测试网
		- 熊链

# 2025.07.31


<!-- Content_END -->
