---
timezone: UTC+8
---

# Lambert

**GitHub ID:** LambertAlpha

**Telegram:** @LambertAlpha

## Self-introduction

Bloackchian Full-stack dev

## Notes

<!-- Content_START -->
# 2025-08-07

耗時：30min

ai coding這麼厲害的時代，像語法這樣過於細節的苦力活，沒有必要糾結太多。剛學編程的時候對語法很看重，認為你只有盡可能掌握語法，才算會這個編程語言。其實不是的，不管如何，high level的語言最終總是要轉換成機器碼執行，你過多糾結於這人為設計的語法，最終寫出來的代碼其實和ai沒差，語法是一系列和機器溝通的死規則，這是ai的範疇，作為人，語法過一遍知道個大概就好了，關鍵是要知道怎麼用、怎麼優化整個體系，怎麼讓技術更好地為我的目的服務，這是ai侷限的地方。

語法我認為看視頻不如文檔高效，通過例子學習是很不錯的，推薦[Solidity by Ecample](https://solidity-by-example.org)

### Variables這一塊和web2不太一樣，需要注意一下

- **local**
    - declared inside a function
    - not stored on the blockchain
- **state**
    - declared outside a function
    - stored on the blockchain
- **global** (provides information about the blockchain)

一般來說web2是local , static, global，global, static是存儲在heap上，local是存儲在stack上。

### An array can have a compile-time fixed size or a dynamic size.

所謂compile-time對應的就是編譯時，與之相對的就是運行時，動態數組就是在運行時可以被擴容的數組。

### Data Locations

Variables are declared as either `storage`, `memory` or `calldata` to explicitly specify the location of the data.

- `storage` - variable is a state variable (stored on the blockchain)
- `memory` - variable is in memory and it exists while a function is being called
- `calldata` - special data location that contains function arguments

# 2025-08-06

耗時：30min
```jsx
forge create Counter \
  --rpc-url http://127.0.0.1:8545 \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 \
  --broadcast
```

如果加上—boadcast，会将交易实际发送到区块链网络

- **不使用 `-broadcast`**：
    - Forge 只会模拟交易执行
    - 显示预估的 gas 费用和执行结果
    - 不会在区块链上创建任何实际状态变更
    - 用于测试和验证合约部署是否会成功
- **使用 `-broadcast`**：
    - 将交易真实发送到指定的 RPC 节点
    - 消耗真实的 ETH 作为 gas 费
    - 在区块链上创建合约实例
    - 返回实际的交易哈希和合约地址

但http://127.0.0.1:8545设置的是本地的anvil测试网，所以不消耗真实的代币。

以及如果不指定rpc-url的话，默认指向本地的anvil

Remmix对新手的一大好处就是把原本要在终端查询的交易状态和变量都可视化，所以对新手很友好，但鼠标点点点后期来说就很低效。

### 生产环境下密钥的存储

开发环境中可以存到.env，生产环境不推荐这么做

But for real money, I won't do that. I will use -- interactive or a keystore file with a password once foundry adds that.

上面这句话已经给出了两个方案了，实操可以问ai的建议来一步步做。

# 2025-08-05

耗時：1h45min

由於今天是第一天學習，大部分時間耗在了找合適的視頻進度和看視頻中，說實話這個教學視頻過於基礎，教你甚至從VS Code和命令行開始。我的學習方法是快速過視頻（兩倍速），根據標題跳過已掌握的知識，然後遇到問題讓claude快速幫我總結。用ai輔助學習效果很好，它還會幫你**適當**拓展很多知識，讓視頻或者其他學習資料佐ai輔助是很高效的學習方法。通過今天1h45min的學習我已經基本學會Foundry框架並且能夠上手寫Solidity合約了。

[學習視頻](https://www.youtube.com/watch?v=i22RLgAu51g)

幾個月前看過了這個系列的前四課，講的很基礎，關於blockchian和智能合約的基礎知識，以及如何在Remix IDE部署合約，示例是一個儲存的合約，還學習了怎麼在合約裡調用另一個合約，忘得差不多了，不過快速過了一下很快又理解，大概是因為我已經掌握了Move的緣故。

今天的主要任務是學習Foundry框架。

其他主流的框架：hardhat基於js，Foundry基於Solidity，Brownie基於Python，我們這裡先學習Foundry。

### Solidity基礎語法

感覺有點像javascript，加上之前學過move，所以基本無障礙，快速過一遍語法就好了。

## Foundry

[實踐項目連結](https://github.com/LambertAlpha/solidity_practice/tree/main/foundry_1)

[文檔](https://getfoundry.sh/)

[Foundry full tutorial](https://updraft.cyfrin.io/courses/foundry/foundry-simple-storage/development-environment-setup-mac-linux)

**forge工具**

初始化項目 forge init (project name) 編譯forge compile

**anvil工具**

foundry的內置本地以太坊節點

### 如何在本地部署合約

1. 運行anvil在本地跑以太坊節點，會給你127.0.0.1:8545端口和一堆帳戶和私鑰；
2. 運行好本地節點後，通過forge命令來部署；

Tips：注意，部署失敗連不上本地節點的原因很可能是因為vpn

```jsx
forge create Counter --rpc-url http://127.0.0.1:8545 --interactive
```

一次性交互

```jsx
forge create Counter \
  --rpc-url http://127.0.0.1:8545 \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 \
  --broadcast
```

部署成功輸出：

```jsx
No files changed, compilation skipped
Deployer: 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
Deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
Transaction hash: 0xca4ba3555dced7f00fe969b48f7e797e3a6dd11b517eae71cb9d39d66982e27b
```

1. 使用cast和合約交互：

```jsx

# 读取当前 number 值
cast call <合約地址> "number()" --rpc-url http://127.0.0.1:8545

# 调用 increment 函数
cast send <合约地址> "increment()" \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 \
  --rpc-url http://127.0.0.1:8545

# 设置 number 为特定值
cast send <合约地址> "setNumber(uint256)" 42 \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 \
  --rpc-url http://127.0.0.1:8545
```

調用查詢number函數（只讀）

```jsx
cast call 0x5FbDB2315678afecb367f032d93F642f64180aa3 "number()" --rpc-url http://127.0.0.1:8545
```

返回

```jsx
0x0000000000000000000000000000000000000000000000000000000000000000
```

調用增加函數（交易 消耗gas）

```jsx
cast send 0x5FbDB2315678afecb367f032d93F642f64180aa3 "increment()" \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 \
  --rpc-url http://127.0.0.1:8545
```

返回

```jsx
blockHash            0x1b9b83bffaa6ea77ced6508c3c261801fdcd59fe84b9d553fcc2b001f98b22e3
blockNumber          2
contractAddress      
cumulativeGasUsed    43482
effectiveGasPrice    876306676
from                 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
gasUsed              43482
logs                 []
logsBloom            0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
root                 
status               1 (success)
transactionHash      0x3fbfbe28a96a0d896e88256592af6f2c34292fdf9086a5d09da8ec6890d27ef9
transactionIndex     0
type                 2
blobGasPrice         1
blobGasUsed          
to                   0x5FbDB2315678afecb367f032d93F642f64180aa3
```

再調用查詢numbert函數（只讀）

```jsx
cast call 0x5FbDB2315678afecb367f032d93F642f64180aa3 "number()" --rpc-url http://127.0.0.1:8545
```

返回

```jsx
0x0000000000000000000000000000000000000000000000000000000000000001
```

### Foundry框架下script, src和test各司其職

![螢幕截圖 2025-08-05 下午3.43.00.png](attachment:889eb4e0-f559-4555-939e-d8001d786fb7:螢幕截圖_2025-08-05_下午3.43.00.png)

# 2025-08-04

今天研究了ETH和纳斯达克指数的相关性，发现ETH可以简单分为三个阶段，第一个阶段主要是技术极客圈子，和NDX相关性近乎为0，第二个阶段是疫情时期宽松货币政策，使得ETH成为相对大众所知的风险资产，最后是24年ETF上线以及25年华尔街开始介入之后，和NDX的相关性越来越高。
我量化分析了ETH和NDX的相关性，输出了一篇研报，见链接：https://docs.google.com/document/d/1yopHMB9VXGlz4fkkZQcqzpqP5dWrKrE1tJfqL_CLRHs/edit?usp=sharing


# 2025.07.31


<!-- Content_END -->
