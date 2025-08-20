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
# 2025-08-20

耗時：30min

報名了Sui八月到十月的黑客松，今天是項目進度同步會，我分享了自己在做的Pumpkin項目，uvd老師提了很多建議和insight。最大的問題就是同質化太嚴重，我這個寵物創意（他們說“強迫症遊戲”，不過我覺得這個描述不合適）太大眾了，每次黑客松都有2-3個人做這個，除非我做的某方面很有新意，否則很難脫穎而出或者獲獎。

uvd老師提到了黑客松的目的，最基礎的磨練技術，此外想進階一些，奔著獲獎和拿項目方投資真正run起來賺錢的，就需要深入去看各種項目（特別是DeFi的），去了解項目方需求和用戶需求，做一個大家真正需要的產品。這樣的優秀產品就很容易獲獎，更好一些的，可以自己有賺錢的生態，以及項目方也會給你投錢，之前就有黑客松項目被投了。

这次的赞助项目方是**Bucket稳定币抵押，Scallop借贷和Navi Protocol借贷质押dex一体，都是DeFi的。**

# 2025-08-19

耗時：45min

### Lorenzo Protocol

Lorenzo Protocol像是一個“鏈上公募基金”，主要連結DeFi和CeFI，讓鏈上的代幣能夠託管到Lorenzo的幣本位Vault中，而每個Vault有對應的基金經理來做Cex的交易和投資，然後大家分享收益。

這也很好解釋了為什麼Lorenzo選擇部署在BSC上，畢竟業務很大一部分在Cex，自然要和Binance有更加緊密的聯繫。以及Gas費低，EVM兼容，對於DeFi應用很重要。

用戶質押鏈上代幣，獲取對應的LP代幣，從而可以去其他DeFi應用獲取收益，這裏的“可組合性”很妙呀，用戶可以同時享有DeFi和CeFi的收益，不過對應的，也會承受雙邊的風險。

### 中心化與去中心化

託管錢包很大部分權利都是由Lorenzo團隊掌控，所以這其實沒有那麼去中心化，同時你投資這個Vault給這個基金經理，也需要你對其足夠信任。這裏的“信任”還是很重要。與 Uniswap、Aave 等協議不同，投資 Lorenzo 的金庫不僅僅是信任代碼（Code is Law），更是**信任運行這個協議的團隊、其合作夥伴以及他們建立的一整套風控體系**。這裏區塊鏈做的事情，主要是降低了用戶參與的門檻，無門檻、無憑證同時資產流動性即時和透明，和傳統的基金又很不一樣。

區塊鏈在這裡扮演的角色是：

1. **全球化的准入層 (Global Access Layer)**
2. **資產的流動性層 (Liquidity Layer)**
3. **結果的清算層 (Settlement & Transparency Layer)**

# 2025-08-18

耗時：30min

### 技術轉型Web3之路

我是做前端出身，最近也在準備Bybit的前端面試，看了Logic和Jason兩人的經驗分享，有蠻多啟發的。對於我在校內打黑客松階段，最重要的是快速閉環，跑通整個Web3開發流程；對於我在校外實習，最重要的是在實踐中積累對代碼和業務邏輯的理解。

### ai所不能取代的前端能力

AI 解决的主要是 30% 的机械劳动——生成简单 CSS、解析错误栈、写脚手架。但它做不到：

- 拆解复杂业务需求并校验正确性；
- 处理公共样式级联、跨页面依赖；
- 替代编写技术文档、覆盖场景、拉齐多部门。

### from Julie

長期堅持產出（研報、AMA主持、內容），讓行業逐漸認識到自己，提升影響力，機會也就隨之出現了。

### [**当资源有限时，您对 00 后 05 后 Web3 从业者的成长路径有哪些宝贵建议呢？**](https://web3intern.xyz/zh/julie-community-growth-expert/#_8-%E5%BD%93%E8%B5%84%E6%BA%90%E6%9C%89%E9%99%90%E6%97%B6-%E6%82%A8%E5%AF%B9-00-%E5%90%8E-05-%E5%90%8E-web3-%E4%BB%8E%E4%B8%9A%E8%80%85%E7%9A%84%E6%88%90%E9%95%BF%E8%B7%AF%E5%BE%84%E6%9C%89%E5%93%AA%E4%BA%9B%E5%AE%9D%E8%B4%B5%E5%BB%BA%E8%AE%AE%E5%91%A2)

- **“定位长板”** 先明确自己最擅长什么，并把它做到极致。
- **“持续输出”** 运营个人推特、写观察与实践笔记，如“如何理解 X 模型”、“我参与 XX 的经验”。
- **“主动连接”** 多和项目团队沟通，给反馈、做贡献；被看见的前提是先出现。
- **“保持谦逊与好奇”** Web3 最公平，真正的门槛是学习与输出的持续性，而非资源。

很真誠的建議，總結下來就是兩點：

1. 持續輸出；
2. 持續Build；

### from Bruce

從用人方的角度對與實習生的考量，什麼最重要？是“靠譜”，何為靠譜？

### 可預期、可溝通、可復盤

> “可预期”意思是你说三天能做完，那么第三天我就能看到结果或者至少知道进度。最怕的就是最后一刻才说“做不完”。“可沟通”就是遇到问题不要闷头自己扛，即使只是说“我卡在这个地方了，需要半天时间排查”也比一言不发好得多，要主动找我同步而不是我去问。“可复盘”就是做完一个任务能主动思考哪些地方做得好、哪些地方可以改进，曾经有过问题的地方未来是否还会反复出现。
> 

## 總結

1. 堅持持續輸出，打造好推特這個個人IP；
2. 在目前的工作中成為“可預期、可溝通、可復盤”的靠譜的人；
3. 持續在社區Build，對自己在社區做的工作負責；
4. 堅持工作復盤，理解代碼的同時也更加深入學習業務邏輯；

# 2025-08-17

耗時：30min

今天主要在看[實習手冊文檔](https://web3intern.xyz/zh/logic-frontend-journey/#_6-%E5%8A%A0%E5%85%A5-bybit-%E7%9A%84%E8%BF%87%E7%A8%8B%E6%98%AF%E6%80%8E%E6%A0%B7%E7%9A%84-%E7%8E%B0%E5%9C%A8%E8%B4%9F%E8%B4%A3%E5%93%AA%E4%BA%9B%E4%B8%9A%E5%8A%A1)，裡面好多前輩的經驗和金玉良言，頗多收穫。

智能合約篇章已經很詳細地描述了整個流程和技術棧，不過還是需要在實踐中學習。

還有很多前輩的經驗，值得好好學習和筆記。

# 2025-08-16

耗時：30min

![螢幕截圖 2025-08-16 下午10.58.50.png](attachment:f98e8c7d-de6e-4477-9296-bfa0174cef67:螢幕截圖_2025-08-16_下午10.58.50.png)

这次课讲的是比较high level的看法和建议，明天可以学习一下一些具体活动的[运营](https://www.bilibili.com/video/BV1Ahb8z1Eiv?spm_id_from=333.788.videopod.sections&vd_source=a54874e051337084165c34046ee7a48f)，然后25Fall在学校办这个活动，以及尽可能有更多的拉新。

开的三倍速看的，知识吸收刚刚好，一个理想的方式就是先听一遍，然后笔记自己学到了什么。总结我学到的启示，应该先确定好我的目的，我并不会把运营作为我的本职工作，我对这个也没有很感兴趣，不过是视为一个必须的能力，我更期待的是我能够成为领航员。所以，我的目的其实就是跑通社区运营的闭环，让自己有这个能力。

由此，我能够有如下启示：

1. 运营的目的是什么？更多的用户，更高的知名度。我现在的品牌和社区，就是0xCUHKSZ链协；
2. 一个好的社区，是能够让大家对这里有归属感；
3. 作为领航员，和别人共事的时候，要考虑他们的动机和利益，做好能够一致；
4. 社区运营、办一场好的活动，是需要学习和经验积累的；
5. 项目周期很短，所以能够顺利推进很重要，让项目负责人去推进，我自己也要推进，我总是容易忘事情，以及拖延；

# 2025-08-15

耗時：40min

學習[yield-course示例代碼](https://github.com/crazyyuan/defi-fixed-yield-course/blob/main/contracts/RewardToken.sol)

主要是合約部分以及前端交互的部分。

Solidity的合约还是比Move的可读性更高一些，也更简单一些，很像Javascript。其中，有好多现成的库可以用，大大降低了开发的难度，生态还是很完善呀，如下：

```solidity
import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
```

就有铸币的方法ERC20( ), mint( )等等，很方便。

这个yield产品合约的精妙之处就在于自动实时更新的RewardToken，关键在于view类型函数几乎不耗Gas地读取链上数据，比如Timestamp，就可以实时计算利息。这是是我之前做Sui链上宠物自动更新hp的一个难点，现在有了处理的灵感了。有时间衰减和事件驱动等机制。

## **前端与链上交互的完整架构**

**技术栈组成**

**核心库：**

- **Wagmi**：React Hooks for Ethereum，提供与以太坊交互的React hooks
- **Viem**：TypeScript接口，用于与以太坊节点通信
- **RainbowKit**：钱包连接UI组件
- **React Query**：数据获取和缓存管理

ABI是智能合约的**接口规范，**定义了：

- 合约有哪些函数
- 函数的参数类型和名称
- 函数的返回值类型
- 函数的状态可变性（view/pure/payable等）

类似于REST API的接口文档

### 核心库架构

前端应用
├── Wagmi (React Hooks for Ethereum)
├── Viem (TypeScript Ethereum Client)
├── RainbowKit (钱包连接UI)
└── React Query (数据管理)

### 区块链数据读取和交易写入

Wagmi + Viem

### 钱包连接

RainbowKit + Wagmi

![螢幕截圖 2025-08-15 下午11.16.02.png](attachment:24e1ae7d-78d8-4da1-89fd-d726b7c2f4e3:螢幕截圖_2025-08-15_下午11.16.02.png)

# 2025-08-14

耗時：40min

可以花大概2h细读里面的代码并且自己尝试部署一下。

如何看ERC系列的介紹？內容看eip.fun，代碼看OpenZeppelin的代碼庫。

hardhat框架是用ts來寫測試的，Foundry使用Solidity，還是先學著用Foundry吧。

測試是特別重要的，特別對於DeFi而言，測試應該覆蓋所有可能的場景。以及真實上線的話，測試環境最好是fork主網，這樣模擬更加真實一些。

### 如何用ai？

ai輔助審計、寫測試是可以的，但是像很強的規則性的東西以及合約的主邏輯最好還是自己寫。

不要用私钥去部署？那么用Foundry和Hardhat怎么部署呢？

部署完之后，一个合约会返回一个地址，粘贴到前端的.env中，

获取实时利息的代码，原来可以通过查询block.timestamp区块时间来看现在的时间，这样的话我那个Pumpkim宠物的饿肚子机制可以做了，之前一直困在怎么自动更新状态和时间上。

![螢幕截圖 2025-08-14 下午10.49.11.png](attachment:48f3005d-b61e-45b9-8945-fa5b9fce6313:螢幕截圖_2025-08-14_下午10.49.11.png)

### ETH通用的前端/合约交互SDK

RainbowKit

Wagmi Hook

viem

![螢幕截圖 2025-08-14 下午10.51.25.png](attachment:ff8b743b-861b-4899-80fc-2a97980ebf88:螢幕截圖_2025-08-14_下午10.51.25.png)

### 合约数据交互

读取走DApp配置的RPC

交易走钱包的RPC

![螢幕截圖 2025-08-14 下午10.52.32.png](attachment:9cf163ac-100a-449a-88c6-664fa6482686:螢幕截圖_2025-08-14_下午10.52.32.png)

ABI:合约供前端调用的接口 有点像interface类型定义

# 2025-08-13

耗时：30min

想作为一个好的PM，要多看项目，多去思考背后产品设计的核心原理。

![螢幕截圖 2025-08-13 下午10.11.00.png](attachment:20877046-10c7-4aae-a957-032bb2aa1f8e:螢幕截圖_2025-08-13_下午10.11.00.png)

稳定币DAI和UST，Luna的信任危机连续清算风险。

其实听了两倍速这个分享会下来，没有什么信息增量，这是比较小白向的🤣

# 2025-08-12

耗時：30min

Aave是一個借貸協議，用戶可以提供流動性賺取利息，也可以超額抵押來借錢同時支付利息。

GraphQL是一個值得學習的技術，經常看見這個技術棧。

### 特殊機制：閃電貸

Aave 還提供一種名為「閃電貸」的特殊借貸方式。這種貸款允許用戶在無需提供任何抵押品的情況下借款，但條件是必須在同一個區塊交易內完成借款和還款。[[**3**](https://www.google.com/url?sa=E&q=https%3A%2F%2Fvertexaisearch.cloud.google.com%2Fgrounding-api-redirect%2FAUZIYQEO9gHX_er8pPj4boTad7osVNBGxhVHAA59OUhmnc5-WhIYhnhKhI7zHSj2rFKwRT1Po9od-za-XCspyYS_oWFs1GiW0AfMsJu1bYD_Vphal9tJFPfKR1SShb9r0VxDbGPaQkThBL0SplvYTdNmk9MCA0NAwyUXcE9_pEUb73dTgVklJmt8wXSu2T4cwGiP9Hnl-A%3D%3D)][[**5**](https://www.google.com/url?sa=E&q=https%3A%2F%2Fvertexaisearch.cloud.google.com%2Fgrounding-api-redirect%2FAUZIYQHHSeSkhJtKdWALweeGRMRdM3qaR3Whziu8w8QNJhLgU7jrcb5hP72letdIYx3jPye-YtzcZnwuXYPyl8-dsA7l_xuMTNt0RCuqDvbmsfyBJ_1tZZFzT_2Jzh50XvGtZxe9ekh8ht9D93QueeEojvWiT74d7as1rdQs_ksThlzltgIh0R19Dw%3D%3D)] 閃電貸不收取傳統的利息，而是收取一筆固定的費用，通常為借款金額的 0.09%。[[**5**](https://www.google.com/url?sa=E&q=https%3A%2F%2Fvertexaisearch.cloud.google.com%2Fgrounding-api-redirect%2FAUZIYQHHSeSkhJtKdWALweeGRMRdM3qaR3Whziu8w8QNJhLgU7jrcb5hP72letdIYx3jPye-YtzcZnwuXYPyl8-dsA7l_xuMTNt0RCuqDvbmsfyBJ_1tZZFzT_2Jzh50XvGtZxe9ekh8ht9D93QueeEojvWiT74d7as1rdQs_ksThlzltgIh0R19Dw%3D%3D)] 這種功能主要針對開發者，用於套利、再融資等進階操作。[[**3**](https://www.google.com/url?sa=E&q=https%3A%2F%2Fvertexaisearch.cloud.google.com%2Fgrounding-api-redirect%2FAUZIYQEO9gHX_er8pPj4boTad7osVNBGxhVHAA59OUhmnc5-WhIYhnhKhI7zHSj2rFKwRT1Po9od-za-XCspyYS_oWFs1GiW0AfMsJu1bYD_Vphal9tJFPfKR1SShb9r0VxDbGPaQkThBL0SplvYTdNmk9MCA0NAwyUXcE9_pEUb73dTgVklJmt8wXSu2T4cwGiP9Hnl-A%3D%3D)]

### Aave Vault金庫

簡歷在Aave Market（借貸協議）基礎上的應用，相當於基金經理，幫存入的用戶自動做Yield Farming。常見策略有：

- **循環借貸 (Looping)**：如果抵押資產和借出資產是同一種（或掛鉤資產，如 stETH 和 ETH），Vault 會將借來的資產再次存入 Aave 作為抵押品，從而可以借出更多的資產。這個過程重複多次，形成槓桿，放大了基礎存款的收益（同時也放大了風險）。
- **流動性挖礦 (Liquidity Mining)**：將借來的資產投入到其他去中心化交易所 (DEX) 的流動性池中，以賺取交易手續費和平台獎勵代幣。

用户存款会获得ERC-4626協議，可以看作股東權益代幣。
• **ERC-4626 標準**：這是以太坊上的一個代幣標準，專門為「可賺取收益的金庫 (yield-bearing vaults)」而設計。把它想像成一個**萬用插頭**，任何符合這個標準的 Vault 都能輕易地與其他 DeFi 協議對接，增強了其通用性。

### Market的清算機制

- **健康因子 (Health Factor)**：這是一個數字，用來評估您倉位的安全程度。數字越高越安全。如果您的抵押品價值相對於您的借款金額大幅下降，您的健康因子就會降低。**當健康因子低於 1 時，您的倉位就處於危險之中。**
- **清算閾值 (Liquidation Thresholds)**：這是為每種抵押品設定的特定百分比。如果您的抵押品價值相對於借款的比例觸及了這個閾值，清算就會被觸發。清算閾值通常在 80% 到 85% 之間，也就說，你存入1個價值10000u的ETH，一般只會取出5000-6000u才安全。
- **清算獎勵/罰金 (Liquidation Bonus/Penalty)**：這也是一個百分比，通常在 5% 到 10% 之間。這是給予清算人的**獎勵**，也是對被清算人的**懲罰**。清算人可以用折扣價購買您的抵押品。
- **清算 (Liquidation)**：一旦健康因子低於1，任何人（通常是稱為「清算人」的機器人或專業用戶）都可以幫您償還部分或全部的債務。作為回報，清算人會以一個折扣價拿走您的抵押品。這是協議保護貸方資金的自動化強制手段。

所以這一套機制下，清算人總是賺錢的，沒有控制好風險的用戶會虧錢。

## Aave V3的**Efficiency Mode**和**Isolation Mode**

對應解決兩個問題。一般來說借貸價值比 (LTV) 是 85%左右，但是如果你質押的是穩定幣，借出穩定幣，這個LTV的效率顯然很低了。

所以**Efficiency Mode下，有些很穩定的交易對LTV可以提升至97%**

對應另一種場景，如果Aave上線一種新的、高風險代幣，LTV和能夠借出的幣又要調整了。

1. **隔離上市**：當一個新資產（比如叫 $NEWCOIN）以隔離模式被上架。
2. **有限的借貸能力**：您可以使用 $NEWCOIN 作為抵押品，但您**只能**用它來借入 Aave 官方指定的一籃子安全資產（通常只有幾種主流穩定幣）。您不能用它來借 ETH 或 WBTC 等。
3. **債務上限 (Debt Ceiling)**：協議會對這種隔離資產設置一個總的「債務上限」。例如，整個市場最多只能因為抵押 $NEWCOIN 而借出總計 100 萬美元的穩定幣。一旦達到這個上限，就沒有人能再用它來借款了。

### 總結來說，DeFi產品的核心關鍵要素就是“安全性”和“效率”

### [靈感](https://x.com/0xArabesque/status/1955270754435547150)

看DeFi項目，感慨每次協議的升級繞不開兩個點：“安全”和“效率”。Uniswap V3和Aave V3的升級都是“安全”和“效率”驅動的。

同時這兩個詞不僅適用於DeFi本身的金融屬性層面，還適用於智能合約的技術層面。

今天又學到了一個新詞，“可組合性”，指的是去中心化應用程式（DApps）可以像樂高積木一樣，無需許可地互相連接、互動和整合，從而創造出比單個應用更強大、更複雜的金融產品或服務。

你想怎麼拼、想怎麼組合都可以，因為這些應用是“去中心化”的。你之所以可以像積木那樣隨意拼，是因為各種標準化接口ERC-20 ERC-721 ERC-4626等等。

我找回了最初進入區塊鏈行業的快樂。從產品設計、技術創新中，領略獨有的美感和啟迪。

“**安全性**是地基，**效率**是建築的結構，而**可組合性**則是賦予這座建築無限可能性的能力，讓它可以是城堡、太空船或任何你能想像到的東西。它是 DeFi 生態系統能夠如此快速、野蠻生長的核心驅動力。”

# 2025-08-11

耗时：1h

## Uniswapv2-Swap客户端调用

```
address private constant UNISWAP_V2_ROUTER =
    0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D;
    
IUniswapV2Router private router = IUniswapV2Router(UNISWAP_V2_ROUTER);
IERC20 private weth = IERC20(WETH);
IERC20 private dai = IERC20(DAI);

```

一开头看到了一个很奇怪的代码，Router，我是做前端出身的，对这个Router的出现自然很疑惑。前端的Router是管理用户UI的导航，那么智能合约中的呢？智能合约中的Router是一个协议路由合约，它是已经部署在EVM上的，会暴露出一个固定地址。而我们在客户端合约可以调用这个地址的function，就像前端调用后端暴露出来的api接口一样，而这里的后端，就是运行在EVM的Uniswap v2的合约。

```jsx
interface IUniswapV2Router {
    function swapExactTokensForTokens(
        uint256 amountIn,
        uint256 amountOutMin,
        address[] calldata path,
        address to,
        uint256 deadline
    ) external returns (uint256[] memory amounts);

    function swapTokensForExactTokens(
        uint256 amountOut,
        uint256 amountInMax,
        address[] calldata path,
        address to,
        uint256 deadline
    ) external returns (uint256[] memory amounts);
}

interface IERC20 {
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address recipient, uint256 amount)
        external
        returns (bool);
    function allowance(address owner, address spender)
        external
        view
        returns (uint256);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint256 amount)
        external
        returns (bool);
}
```

这里的interface也一样，就是api的接口的类型说明书，第一个就是来自Uniswap v2的那个Router，后者呢是ERC20通用代币协议，是一套公认的标准，所以不用额外声明Router

这就是套利机器人提交执行交易的核心代码。

## 用Foundry来做测试

在 Foundry 中，任何以 test 开头的函数都会被自动识别并执行为一个独立的测试用例。每个测试都遵循经典的 **"Arrange-Act-Assert" (AAA)** 模式。

**Arrange (准备工作 / 安排)、Act (执行操作)、Assert (验证结果 / 断言)**，为了让这个测试更加清晰易读、结构更加一致，特别对于复杂又极重安全的智能合约来说，AAA很重要。

**测试是如何在没有真实货币的情况下与真实的 Uniswap 交互的？**

这就是 Foundry 的强大之处：主网分叉 (Mainnet Forking)。

当你运行这个测试时，Foundry 会在你的电脑上瞬间创建一个以太坊主网的“克隆体”。这个克隆体包含了主网上所有的数据和已部署的合约（比如真实的 Uniswap Router 和 WETH 合约）。然后，它在这个“克隆世界”里部署你自己的 UniswapV2SwapExamples 合约 (uni)，并执行测试。

### Foundry还有什么用？

可以把它想象成一个包含了**后端开发、测试、部署和链上交互**所有功能的命令行“集成开发环境”(IDE)。

特别全能，由**Forge**, **Anvil**, **Cast**, 和 **Chisel**四大模块组成。

[详细功能](https://www.notion.so/24cde3038518808b926dd2876c0079e8?pvs=21)

Forge是核心引擎，Anvil是本地以太坊节点，Cast是命令行合约交互工具，Chisel是Solidity Shell

# 2025-08-10

耗時：30min

[原文链接](https://eips.ethereum.org/EIPS/eip-721)

A standard interface for non-fungible tokens, also known as deeds.

比特币、美元等代币都是同质化的，ERC-721提出了非同质化代币NFT，并且为开发者提供了一套统一的接口和函数（像MCP那样）。

 ERC-721标准的核心是提供了追踪（ownerOf）和转移（transferFrom）NFT所有权的基础功能。**操作员 (Operators)，**考虑到了一个场景，即NFT的所有者可以授权一个第三方（如OpenSea这样的交易市场）来代为操作或转移他们的NFT，这增加了资产的流动性和可用性。

EIP-20不足以用于追踪NFT，因为每个NFT资产都是独特的（非同质化的），而ERC-20代币中的每一个都是相同的（同质化的）。

EIP-20協議有待學習。

為什麼會有ERC-721這個協議？協議的目的就是為了達成標準化共識。

标准化的主要动机是实现“互操作性”。这意味着一个支持ERC-721标准的应用（如钱包或市场）可以自动识别和处理任何遵循该标准的NFT，而无需为每个新的NFT项目单独编写代码。

**与ERC-20的区别：** 这是理解ERC-721的关键。

- **ERC-20 (同质化):** 就像人民币一样，你的一块钱和我的一块钱价值完全相同，可以互换。ERC-20代币是可互换、可分割的。
- **ERC-721 (非同质化):** 就像两幅不同的画作，即使尺寸相同，其价值和意义也完全不同，无法互换。ERC-721代币是独一无二、不可分割的。 CryptoKitties游戏中的每只虚拟猫都是一个ERC-721代币，你不能拥有“半只猫”。[[**5**](https://www.google.com/url?sa=E&q=https%3A%2F%2Fvertexaisearch.cloud.google.com%2Fgrounding-api-redirect%2FAUZIYQGySOAFVl4Cpa_KdeX0J6LKygUFkJEtJKgF8cU0GmwTPw1E1W9-nvmwiPDSf_6Eq0r4HyJjwmH9j932w-vfTW3K4Dq4CFNmlaomWinhMeF-p6UYBrLA3dG8oluW-2QcMzBl-KAdAA7-lz5A4Sv9LYahGjokSb_evQ%3D%3D)]

核心接口-**ERC721接口:** 这是ERC-721标准本身定义的核心接口，包含了如ownerOf、transferFrom等NFT的基本操作函数。

輔助接口-**ERC165接口: 作用是“接口检测”。**

# 2025-08-09

耗时：45min
今天和ai一起梳理学习了Sonic的白皮书，写了一篇分析其技术核心、架构选择和经济模型的文章，见推特：
https://x.com/0xArabesque/status/1954196856881475868

# 2025-08-08

### Function

Getter functions can be declared `view` or `pure`.

`View` function declares that no state will be changed.

`Pure` function declares that no state variable will be changed or read.

### Error

An error will undo all changes made to the state during a transaction.

做Error处理能够有效回滚交易，节省gas同时更加安全，而非Error处理的报错可能会有错误操作和安全隐患。

You can throw an error by calling `require`, `revert` or `assert`.

- `require` is used to validate inputs and conditions before execution.
- `revert` is similar to `require`.
- `assert` is used to check for code that should never be false. Failing assertion probably means that there is a bug.

### Function Modifier

这个功能对于安全特别重要

Modifiers are code that can be run before and / or after a function call.

Modifiers can be used to:

- Restrict access
- Validate inputs
- Guard against reentrancy hack

### Event

这个很重要，有点像console.log( )，不过更加持久，也要消耗gas

`Events` allow logging to the Ethereum blockchain. Some use cases for events are:

- Listening for events and updating user interface
- A cheap form of storage

### 智能合约向外界传达信息的方式：

![螢幕截圖 2025-08-08 下午11.08.58.png](attachment:11a8d6ad-e374-4e3d-a1ec-1fadb9341d67:螢幕截圖_2025-08-08_下午11.08.58.png)

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
