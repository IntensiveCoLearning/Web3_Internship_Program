---
timezone: UTC+8
---

# Kiwigo

**GitHub ID:** KKisacat

**Telegram:** @Kiwigo123

## Self-introduction

大家好，我是Kiwigo，是Web3的初學者，想學習區塊鏈相關技術，很高興認識大家~

## Notes

<!-- Content_START -->
# 2025-08-13

## Solidity基本語法
```solidity=
// SPDX-License-Identifier: MIT    //標示軟體的授權資訊
pragma solidity ^0.8.0;    //所需的編譯器版本

contract MyContract {
    // 狀態變數
    uint256 public myNumber;

    // 建構函數
    constructor() {
        myNumber = 100;
    }

    // 函數
    function setNumber(uint256 _number) public {
        myNumber = _number;
    }
}
```

### 資料位置
Solidity 中常見的資料位置有：

* storage → 儲存在區塊鏈的永久儲存區，狀態變數（合約外層定義）預設就是 storage
* memory → 暫存記憶體（函式內的臨時變數用這個）。函式執行結束即銷毀。
* calldata → 函式外部呼叫時的唯讀參數（只讀、gas 便宜）。函式執行結束即銷毀。
* 函式參數或區域變數 → 必須指定 memory / calldata。

### 資料型別 

| 類型  | 必須指定資料位置嗎？  | 範例   |
| ---------------- | ------------- | ----------- |
| **值型別（value types）** <br>`uint`, `bool`, `address`                 | ❌ 不需要（因為直接複製值）                          | `uint x = 5;`        |
| **引用型別（reference types）** <br>`string`, `bytes`, `array`, `struct` | ✅ 需要（`storage` / `memory` / `calldata`） | `string memory name` 、`function foo(string calldata input) external { ... }`|

例子：
```solidity=
pragma solidity ^0.8.0;

contract DataLocationExample {
    string public name = "Alice"; // 預設 storage

    // calldata 版本（省 gas，唯讀）
    function setNameCalldata(string calldata newName) external {
        name = newName; // 從 calldata 複製到 storage
    }

    // memory 版本（可修改，但不會存鏈上）
    function getUpperName() public view returns (string memory) {
        string memory tempName = name;
        // 可以對 tempName 做處理
        return tempName;
    }
}
```


### 可見性修飾詞 (Visibility Modifiers)
決定「誰能呼叫這個函數 / 存取這個變數」。

| 修飾詞        | 說明                     |
| ---------- | ---------------------- |
| `public`   | 任何人都可以呼叫（外部 + 內部）。     |
| `external` | 只能從合約外部呼叫（Gas 會更便宜一點）。 |
| `internal` | 只能在本合約或繼承它的合約中呼叫。      |
| `private`  | 只能在本合約內呼叫。             |



### 狀態可變性修飾詞 (State Mutability Modifiers)
描述「這個函數對鏈上資料的存取行為」。

| 修飾詞       | 說明                                  |
| --------- | ----------------------------------- |
| `pure`    | **不能讀也不能改**區塊鏈上的狀態變數（只能用函數內部變數或參數）。 |
| `view`    | **可以讀，但不能改**狀態變數。                   |
| （無修飾）     | 預設情況，可以**讀寫**狀態變數。              |
| `payable` | 可以接收 ETH（必須加在能收款的函數上）。           |

```solidity=
contract StateModifiers {
    uint256 public count = 0;

    // view: 只读函数，不修改状态
    function getCount() public view returns(uint256) {
        return count;
    }

    // pure: 纯函数，不读取也不修改状态
    function add(uint256 a, uint256 b) public pure returns(uint256) {
        return a + b;
    }

    // payable: 可接收以太币
    function deposit() public payable {
        // msg.value 是发送的以太币数量
    }

    // 默认：可修改状态
    function increment() public {
        count++;
    }
}
```
### 多個返回值 & 呼叫函數
```solidity
// 多个返回值
function getPersonInfo() public pure returns(string memory name, uint256 age) {
    name = "Alice";
    age = 25;
}

function calculate(uint256 a, uint256 b) public pure returns(uint256 sum, uint256 product) {
    sum = a + b;
    product = a * b;
    // 自动返回命名变量
}

// 呼叫带多返回值的函数
function callExample() public pure {
    (string memory name, uint256 age) = getPersonInfo();
     // 呼叫 getPersonInfo()，它會回傳兩個值
    (, uint256 productOnly) = calculate(5, 3);
    // 呼叫 calculate()，只要第二個返回值，第一個用逗號跳過
}
```

### Function Modifiers（函數修飾符）
修飾符是一種可以套用到函數上的條件檢查模板。它們可以在不改動主邏輯的情況下，為多個函數添加統一的前置條件檢查，像是權限驗證、合約狀態檢查等。
```solidity=
contract ModifierExample {
    address public owner;   // 記錄合約擁有者
    bool public paused = false;  // 是否暫停合約的旗標

    constructor() {
        owner = msg.sender; // msg.sender 是呼叫該函數的使用者地址
    }
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not the owner");
        // require(條件, "錯誤訊息");
        _;    // 表示執行主函數剩下的內容
    }
    
    modifier whenNotPaused() {
        require(!paused, "Contract is paused");
        _;
    }
    
    // 修飾符的使用
    function togglePause() public onlyOwner {    // 只能 擁有者 執行
        paused = !paused;
    }

    // 使用多個修飾符
    function criticalFunction() public onlyOwner whenNotPaused {
        // 兩個條件都通過後，執行函數邏輯
    }
}

```


## 以太坊測試網路搭建
[以太坊測試網路搭建教學](https://lxdao.notion.site/24bdceffe40b80148b07fa28483662cf)

什麼情況下會需要自訂測試網環境？
1. 多節點模擬
你想模擬多個礦工、驗證者、全節點、輕節點的互動、測試共識協議的行為（例如 PoS 節點掉線、網路分裂）。

2. 跨鏈或多鏈開發
3. 模擬真實網路延遲與故障
想測試 DApp 在高延遲、節點不同步、節點崩潰時的表現。

4. 私有測試網 & 團隊協作
5. NFT / DeFi / 區塊鏈遊戲的壓力測試
測試你的 NFT 合約在短時間內大量交易、多人同時鑄造時會不會 gas 爆炸或失敗，不用花錢在主網或測試網上測試。

# 2025-08-12

##  Dapp 開發流程
1. 需求分析與規劃
2. 智能合約開發
3. 檢索器開發：檢索器是獲取鏈上資料的核心，負責捕獲智慧合約釋放的事件並將其存入資料庫
4. 前端開發
5. 與區塊鏈交互：讀取資料、發送交易
6. 部署與上線：部署智慧合約(Hardhat或Foundry)、前端部署

## 開發環境搭建
**!!!先執行2. 以太坊本地開發鏈的安裝WSL 2!!!**
### 1. 基礎環境準備(Linux)
1. Node.js（建議用nvm管理）
2. npm: Node.js 內建的套件管理工具(Node Package Manager)，用來安裝套件ex: OpenZeppelin
3. Git
* 安裝nvm (管理Node.js版本的工具)
最新版本：https://github.com/nvm-sh/nvm/releases
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```
```
nvm --version  //檢查安裝版本&是否成功
```
* 安裝Node.js LTS (讓你在電腦上直接執行 JavaScript 的環境)
```
nvm install lts
nvm use lts
```
* 檢查是否安裝成功
```
node -v
npm -v
```
### (補充)基礎環境準備(Windows)
1. Node.js（建議用nvm管理）
2. npm: Node.js 內建的套件管理工具(Node Package Manager)，用來安裝套件ex: OpenZeppelin
3. Git
* nvm (管理Node.js版本的工具)
到 GitHub 官方頁面下載最新版本：
https://github.com/coreybutler/nvm-windows/releases
找到最新版本（最上面那個），下載 nvm-setup.exe。
```
nvm --version  //檢查安裝版本&是否成功
```
* Node.js LTS (讓你在電腦上直接執行 JavaScript 的環境)
```
nvm install lts
nvm use lts
```
* 檢查是否安裝成功
```
node -v
npm -v
```

### 2. 以太坊本地開發鏈
Foundry 和 Hardhat 都是以太坊的 本地開發框架，讓你在自己的電腦上快速搭建一條「模擬以太坊」的鏈，方便寫、測試、部署智能合約，而不用一直花 Gas Fee 上真鏈。
#### Hardhat
Hardhat 官方會建議 Windows 使用者安裝 WSL 2（Windows Subsystem for Linux 2），WSL 2讓你在 Windows 裡面直接跑一個真正的 Linux 系統，而且不用像傳統虛擬機(VisualBox)那樣笨重
##### 安裝WSL 2
根據window版本差異，安裝後版本有些是WSL1有些是WL2
先嘗試：
1. 在PowerShell（管理員）執行：`wsl --install`
2. 下一步安裝Ubuntu：`wsl --install -d Ubuntu`
3. 安裝完第一件事就是更新套件：`sudo apt update && sudo apt upgrade -y`
4. 確認WSL版本`wsl --list --verbose`

如結果是WSL 1 再進行下列升級流程：
[舊版 WSL 的手動安裝步驟](https://learn.microsoft.com/zh-tw/windows/wsl/install-manual)
1. 確認 Windows 版本（必須是 Windows 10 19041+ 或 Windows 11）`winver`
2. 啟用必要的 Windows 功能（虛擬機平台 + WSL）
```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
3. 重新啟動電腦
4. 下載 Linux 核心更新套件
5. 將 WSL 2 設定為預設版本`wsl --set-default-version 2`
6. 將已安裝的 Ubuntu 升級到 WSL 2

# 2025-08-10

[Web3釣魚安全挑戰](https://unphishable.io)

* 永遠不要向任何人透露您的助記詞，無論他們聲稱是誰
* 提防Permit簽名請求，檢查授權地址(Spender)和授權金額(Value)
* 注意spending cap(支出上限)
* 注意域名真偽。域名補充：[網域是什麼？網址與網域名稱的基本介紹](https://www.tsg.com.tw/blog-detail4-183-1-domain.htm)
* 始終檢查 URL 是否為官方域名（teams.microsoft.com）。
* 只從官方來源下載應用程序
* 對任何異常的安裝過程或請求保持警惕。
* 詐騙者經常使用零值轉賬來創建與偽造地址的虛假交易歷史。
* 始終驗證完整地址，而不僅僅是開頭和結尾
* 始終仔細檢查 Seaport 訂單中的對價部分
* 驗證 startAmount 和 endAmount 是否與顯示的價格相符
* 切勿從未知來源複製和粘貼命令
* 始終仔細閱讀 MetaMask 中顯示的實際簽名訊息內容
* 使用專門的治理平台（如 Snapshot）進行 DAO 投票
* ?方法選擇器 0x0900f010 代表upgrade(bool,uint256)
* Tornado Cash 網域是 tornado.cash
* 切勿將不受信任網站上的按鈕或鏈接拖入 Discord 或您的書籤欄，當使用該書籤時，它會執行惡意 JavaScript，可以竊取令牌並危害賬戶。
* Permit2釣魚
> Permit2 的工作方式與傳統代幣授權不同：
> 
> 傳統模式：您直接將代幣授權給特定合約（例如 Uniswap）
> Permit2 模式：您將代幣授權給 Permit2 合約，然後由 Permit2 在內部管理權限
> 
> 這創建了一個兩層授權系統：
> 1. 代幣對 Permit2 的授權（在區塊鏈瀏覽器中可見）
> 2. Permit2 內部的權限（在標準區塊鏈瀏覽器中不可見）
> 
> 漏洞：如果您只撤銷了代幣對 Permit2 的授權，但沒有撤銷內部權限，一旦您再次授權 Permit2，攻擊者仍然可以竊取您的代幣。
> 
> 如何保護自己：
> 1. 使用像 ScamSniffer 的 Permit2 授權管理這樣的工具來查看和撤銷 Permit2 內部權限
> 2. 對簽名請求保持謹慎，特別是那些請求無限制數量的請求
> 3. 驗證簽名請求中的接收者地址
> 4. 使用顯示詳細簽名信息的硬件錢包

*  DeFi 代理合約釣魚攻擊：
> 1. 攻擊者偽裝成合法的 DeFi 平台發送更新請求
> 2. 交易看似是無害的"安全更新"，但實際上是調用 setOwner 函數
> 3. 如果用戶簽署了交易，攻擊者將獲得代理合約的所有權
> 4. 一旦攻擊者控制了代理合約，他們可以提取所有資金
> 
> 保護措施：
> 
> 1. 始終驗證交易的實際調用函數和參數
> 2. 對於可疑交易，直接拒絕簽署
> 3. 使用硬件錢包增加額外的安全層
> 4. 定期檢查您的合約權限設置

* 僅使用知名且受信任的 RPC 提供商
> 惡意 RPC 提供商如何運作
> 惡意 RPC 提供商可以：
> 
> 1. 攔截並修改您的交易，將資金重定向到攻擊者的地址
> 2. 顯示虛假的代幣餘額，讓您認為您收到了不存在的付款
> 3. 提供虛假的交易狀態，讓您認為交易失敗並誘使您重試
> 4. 收集您的交易歷史和錢包活動數據
> 
> 如何保護自己
> 
> 1. 僅使用知名且受信任的 RPC 提供商
> 2. 驗證您的錢包連接到的 RPC URL
> 3. 使用硬體錢包進行額外的安全保障
> 4. 考慮運行您自己的節點以獲得最大安全性

* Token 精度釣魚攻擊原理：
> 1. 攻擊者誘導受害者將代幣的 Decimals（小數位數）從正確值（通常是 18）改為 0
> 2. Decimals 決定了代幣的最小可分割單位的數量，它決定了代幣在顯示時的精度
> 3. 當 Decimals 設為 0 時，錢包會將極小數量的代幣顯示為整數
> 4. 攻擊者轉入極小數量的代幣（如 0.00000000000089589 USDT），但在受害者錢包中顯示為 89589 USDT
> 5. 受害者看到大量資金「已追回」，就會相信攻擊者，並按照要求提供私鑰或轉入資金

# 2025-08-08

## 安全與合規
以下針對台灣對於虛擬資產（加密貨幣）的法律與監管措施做筆記。
### 1. 虛擬資產不是法定貨幣
金管會已聲明：「虛擬資產不是貨幣，不能作為支付工具或交易對價」，所以不能用比特幣/USDT去商店直接付款（除非對方願意接受，但不受法律保障）。
### 2. ICO/代幣發行 ≒ 發行證券，可能違法證交法
金管會 2019 年明確指出，若發行的 Token 具有：
* 可轉讓性
* 投資性
* 可預期報酬

➜ 可能構成「證券型代幣（STO）」，適用《證券交易法》
若未經主管機關核准就發行/募資，可能觸犯非法募集資金罪、詐欺罪。
#### ICO 介紹
ICO = Initial Coin Offering（首次代幣發行），是一種向大眾募資的方式，但發的是「代幣」而非股票。
流程：
* 項目方發行一種新代幣（Token），例如 $ABC
* 投資人用 ETH 或 USDT 來買這個代幣
* 項目方拿到資金繼續開發產品
* 投資人期待代幣未來會升值。

風險：沒有監管、沒擔保，項目跑路機率高（2017 年幣圈爆發過很多），因此台灣和很多國家都把 ICO 視為「可能涉及非法證券發行」。
#### 證券定義
在法律上，證券是一種「投資工具」，投資人出資，希望能獲利。常見的證券有：
* 股票
* 債券
* 基金
* 證券型代幣（Security Token）

台灣《證券交易法》第6條：
若一項產品符合以下條件，就可能被認定為「證券」：
* 投資人投入金錢
* 投資的是他人經營的事業
* 投資人期待從中獲利（利潤來自他人努力）

滿足這些要素，無論是股票、代幣、契約，法律上都可能是「證券」，要受金管會監管。

### 3. 虛擬通貨平台需遵守反洗錢規定（AML）
2021 年 7 月起，金管會正式將虛擬通貨平台（如交易所）納入《洗錢防制法》規範。
平台義務內容：
* 用戶實名制（KYC，Know Your Customer）
* 客戶審查與風險控管
* 可疑交易申報
* 保存交易紀錄
* 需向金管會指定單位申報營運資訊（目前是「財政部洗錢防制辦公室」）
未依規定者，可能遭罰鍰或勒令停業。

> 台灣目前合法交易所：
>MaiCoin / MAX、BitoPro（幣託）、ACE

### 4. 金融機構不能參與虛擬資產相關業務
銀行、證券商、保險公司等金融機構 不得經營或投資加密貨幣相關產品，包括：幫客戶買幣、幫發行 Token、推出虛擬資產基金等。

### 5. 稅務與會計處理仍在逐步建立
虛擬幣若產生獲利或被視為財產轉讓，依法應申報所得稅。但實務操作如：怎麼估價、何時認列所得，目前還不算非常明確，部分公司採用自律準則。

# 2025-08-07

(接續前一天)
#### 2. Compound：去中心化借貸協議
Compound 是一個去中心化的借貸平台，允許用戶借入或借出加密資產。

#### 3. MakerDAO（現已更名為Sky）：穩定幣系統
MakerDAO 是一個建立在以太坊上的去中心化穩定幣協議，發行與管理穩定幣 DAI，其價值目標是始終接近 1 美元。

> 2024 年品牌升級：MakerDAO 已正式更名為Sky，並推出了新的代幣體系。 DAI 穩定幣升級為USDS（Sky Dollar）

* 原理：
    1. **超額抵押（Over-collateralization）**：防止價格波動時，DAI 沒有資產支撐
        你想鑄造 100 DAI，不能只抵押 $100 的 ETH
        通常你要抵押 $150 或更多(150%~300%)的資產才能鑄造這 100 DAI
        若市場下跌，抵押率不夠 → 協議會清算抵押品，強制回收 DAI
        確保系統內的 DAI 永遠都有足額資產背書
    2. **清算與拍賣機制**：當抵押資產價值跌太多時，強制賣掉資產來保護系統
        抵押品價格波動時，如果抵押率太低
        系統會「自動清算」你的倉位，把抵押品拿出來拍賣
        收回 DAI，保障 DAI 系統總資產 >= 負債
    3. **穩定費（Stability Fee）**：
        鑄造 DAI 是「借錢」的概念，你要付利息（稱為穩定費）
        如果 DAI 價格偏離 1 美元（例如低於 $1），協議可以：
        提高利率 → 借 DAI 成本變高 → 減少供應 → 拉回價格
        或設置儲蓄率 DSR，吸引人鎖定 DAI → 減少流通
    4. **市場套利（自然力量）**：
        (1.) 如果 DAI < $1，套利者可以：
        以便宜價買入 DAI（例如 $0.98）
        用來償還債務，釋放抵押品（賺回正價的資產）
        買壓拉高 DAI 回到 $1
        (2.) 如果 DAI > $1，套利者可以：
        抵押資產鑄造新的 DAI
        高價賣出 DAI（例如 $1.02）
        賺價差 → 供應上升 → 價格回落
        →市場自己會用套利手段幫助穩定價格
        
### 二、NFT（Non-Fungible Token，非同質化代幣）
是一種存在於區塊鏈上的獨一無二的數位資產證明，用來代表「某個特定的東西」，像是圖片、音樂、影片、遊戲物品、會員資格等。
> 同質化（Fungible）：像 ETH、USDT，每一個單位都一樣、可以互換。
> ex：你手上 1 ETH 和我手上 1 ETH 沒有差別
> 非同質化（Non-Fungible）：每個代幣都是獨一無二的，不能隨便互換。
> ex：一張畫、一張門票、房契、收藏卡，每張都有獨特性

* 特性：

| 特性       | 說明                       |
| -------- | ------------------------ |
| **唯一性**  | 每個 NFT 都有獨特的 ID，無法被複製或取代 |
| **可證明性** | 所有權寫在區塊鏈上，可以公開查證誰是擁有者    |
| **可轉讓性** | 可以用錢包自由買賣轉移，不需要平台或中介     |
| **可編程性** | 透過智能合約賦予額外功能（如稀有度、會員權益）  |

* 代幣標準：大多數 NFT 使用的是以太坊的 ERC-721 或 ERC-1155 標準
* 儲存方式：
NFT 本身存在區塊鏈上的是「擁有權紀錄 + 指向內容的連結（URI）」
真正的圖片/音檔可能存在去中心化儲存服務，如 IPFS

* 如何防止盜版?
NFT 是一個擁有權憑證，不是圖片本體，任何人都可以把同一張圖上傳發行另一個 NFT。：
    1. 平台審查與認證（Web2 機制）：
    OpenSea、Blur、Magic Eden 等平台會做藝術家驗證機制，有藍勾勾、官方認證標章的 NFT Collection 表示是正本，模仿品常會被平台下架。
    2. 社群聲譽辨認：
    真正的藝術家有社群、推特、Discord、官網等聯結，會公開地址，NFT 收藏者會去確認「這張 NFT 是不是從正確的地址鑄造的」。
    3. 智能合約地址辨認：
    每個 NFT Collection 都有對應的「合約地址」，如果圖片相同，但不是這個地址，就可能是假冒品。
    
### 三、DAO 去中心化自治組織
全名是Decentralized Autonomous Organization，是一種運行在區塊鏈上的「無需傳統公司或管理層」的組織形式，用 智能合約 + 社群投票 來做決策與管理。

# 2025-08-06

## 業界賽道全覽
### 一、DeFi
DeFi，全稱為Decentralized Finance（去中心化金融），是基於區塊鏈技術建立的金融體系。以下是DeFi 領域的三個典型案例：
1. Uniswap
Uniswap 是一個建立在以太坊區塊鏈上的去中心化交易所（DEX，Decentralized Exchange），讓你可以直接用錢包跟智能合約互動、交換代幣的自動做市平台，不像中心化交易所需要註冊帳號或人工審核。
* 原理：Uniswap 沒有買賣訂單簿，而是使用叫做「自動做市商（AMM）」的機制來定價。
* 交易流程：
(1.) 每一組代幣對（如 ETH/USDC）都有一個「流動性池（Liquidity Pool）」。
(2.) 你交易時，是跟這個池子交換，不是跟人。
(3.) 價格由一個公式自動決定：x * y = k。
* 普通人可以把兩種代幣存進池子，稱為 流動性提供者（LP），可以賺到交易手續費（通常 0.3%），這就是 DeFi「錢生錢」 的來源之一
* ex: 你要用 1 ETH 換 USDC：Uniswap 的 ETH/USDC 池子目前有：10 ETH / 20,000 USDC。
你加入 1 ETH，根據公式 x * y = k，ETH 變成 11，所以 USDC 數量會減少，你就會得到一些 USDC（但會滑價），手續費 0.3% 給流動性提供者
* Uniswap 本身是一組部署在以太坊鏈上的智能合約協議，前端只是介面。任何人都可以：建立自己的 UI，或從程式直接調用 Uniswap 合約進行交易（像 API）

# 2025-08-05

## 以太坊概覽
可以把 Ethereum 想成一台全球共享的「世界電腦」，每個節點都執行相同的區塊鏈資料和程式碼。

### 名詞解釋

| 組件名稱          | 功能說明                                         |
| ------------- | -------------------------------------------- |
| 區塊鏈層        | 儲存交易、區塊、狀態（帳戶餘額、合約資料）的資料庫                    |
| EVM（虛擬機）   | Ethereum Virtual Machine，執行智能合約的虛擬環境         |
| 智能合約       | 用 Solidity 編寫的程式，部署到鏈上自動執行，像區塊鏈 App          |
| 帳戶模型       | Ethereum 有兩種帳戶：EOA（使用者帳戶）、CA（合約帳戶）           |
| 狀態樹        | Ethereum 使用 Merkle Patricia Trie 儲存帳戶狀態與合約資料 |
| 節點與 P2P 網路 | 所有節點互聯，共同同步區塊與狀態，維護網路運行                      |
| 共識機制       | 目前是 PoS（權益證明），決定誰負責產區塊與驗證交易                  |

### 架構
1. 資料層 / 網路層 (Data + P2P)： 區塊資料、帳戶狀態、P2P 網路
2. 共識層 (Consensus Layer)：PoS 共識機制、驗證者
3. 執行層 (Execution Layer)：EVM、智能合約執行
4. 協定層 (Protocol Layer)：Uniswap(自動做市協議)、Aave(借貸協議) 等 DeFi 協議規則
5. 應用層 (Application Layer)：給使用者使用的 DApp(ex: MetaMask)

#### 以太坊區塊練基礎架構
1. Layer 1（主鏈）
* 涵蓋上面架構的 第 1～3 層
* 交易速度慢、手續費貴

2. Layer 2（擴容層）
* 架在 Layer 1 上的並行鏈，幫助它變快變便宜
* 常見的類型：1. Rollup(Arbitrum, Optimism): 把多筆交易「打包」後一起送給主鏈。2. zk-Rollup	(zkSync, StarkNet)：使用零知識證明，保證正確性且隱私。

# 2025-08-04

## 區塊鏈基礎概念
### 特點 
1. 去中心化
2. 不可竄改
3. 智能合約

### 公鏈 / 私鏈 / 聯盟鏈
#### 公鏈
1. 任何人都可以自由地：讀取資料（查帳）、寫入資料（發交易、部署合約）、參與共識（成為節點或礦工）
2. 優點：自由度高，公開透明安全性高。缺點：效率低，維護成本高
3. ex: 比特幣、以太坊
#### 聯盟鏈
1. 由多個機構共同管理的一種區塊鏈，每個節點由特定成員控制
2. 優點：效率比公鏈高，隱私比公鏈好（資料不對外公開）。缺點：不如私鏈靈活（需要多方協調）
3. ex: 銀行聯盟之間的跨行結算、Hyperledger Fabric
#### 私鏈
1. 特定機構或個人可以控制加入和讀寫
2. 優點：效率高。缺點：缺乏安全透明
3. ex: 銀行內部帳務系統、公司內部資料共享

### 共識機制
#### PoW：工作量證明（Proof of Work）
1. 原理：誰的電腦最先「解出一道數學題」，誰就能獲得記帳權( 打包交易 → 新增區塊 → 拿到獎勵) 
2. 特點：
公平：人人可參與（只要有電腦）。
安全：攻擊成本高（要有 51% 算力才可能作弊）。
缺點：浪費能源、速度慢。
3. ex: 比特幣、以前的以太坊（Ethereum 也曾是 PoW，2022 改為 PoS）
#### PoS：權益證明（Proof of Stake）
1. 原理：不是靠算力，而是靠「你質押了多少幣」來決定誰有機會記帳。越多幣、持有越久的人，中選機率越高。被選中者可打包區塊、獲得獎勵，但如果亂來就會被懲罰（扣幣）。
2. 特點：
節能：不需要高耗電設備。
快速：更高效、適合商業應用。
缺點：有可能造成「幣越多權越大」的現象、富者愈富。
3. ex: 現在的 Ethereum（ETH）、Cardano、Polkadot、Solana、Avalanche
#### 其他共識機制
除了 PoW 和 PoS，還有像：
DPoS（委託權益證明）：EOS 用。
PBFT（實用拜占庭容錯）：私鏈/聯盟鏈常用。
PoA（權威證明）：由可信節點掌控（如 BNB Chain）。


# 2025.07.29


<!-- Content_END -->
