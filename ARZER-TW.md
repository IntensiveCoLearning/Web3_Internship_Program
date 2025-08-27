---
timezone: UTC+8
---

# JAMES

**GitHub ID:** ARZER-TW

**Telegram:** @Lain_1022

## Self-introduction

在学学生,有区块链、solidity、密码学基础,毕业专题正在做区块链PQC,也热衷于链上交易,熟悉链上的社群文化。

## Notes

<!-- Content_START -->

# 2025-08-27
<!-- DAILY_CHECKIN_2025-08-27_START -->
看了行業前輩訪談錄。  
**從小切入口開始**：入門 Web3 不必一口氣挑戰大型 Hackathon，可以先從 Testnet 操作、做個小 DApp、貢獻 PR 或參加 DAO 社群開始，藉由快速閉環獲得正向循環。

**Web2 與 Web3 前端差異**：語法相似，但 Web3 前端更重視業務層面的理解，如錢包交互、Gas/Nonce、風險與滑點等，必須要「自己實際做過交易」才不會覺得是天書。

**核心能力**：技術之外，兩大關鍵是業務洞察（懂錢與風險的邏輯）以及品質體系（測試監控與回滾機制），比單純會 JS 語法更有價值。

**AI 的定位**：AI 能幫忙做 30% 的機械性工作（CSS、型別轉換、錯誤診斷），但仍取代不了對業務的理解、跨頁面的整合、完整文件與規範。

**成長心法**：從小專案、小社群走起，逐步參與開源與 Hackathon，自然就能累積實力並抓住更大的舞台機會。
<!-- DAILY_CHECKIN_2025-08-27_END -->


# 2025-08-26
<!-- DAILY_CHECKIN_2025-08-26_START -->
聽完了BRUCE老師的分享會後優化了自己的履歷。

參與了運營組的黑客松復盤，我雖然不是運營組但也想知道他們的運作流程，其中我認為最關鍵的是打字溝通這一部份，人與人之間的溝通並不是只有單純的文字而是有表情、語氣等種種複雜的組合，單純的訊息溝通可能有時會誤解他人的意思，自己要在這方面多去注意，不管是自己的打字還是接收他人的訊息時都須多多思考，盡量避免誤解的產生。
<!-- DAILY_CHECKIN_2025-08-26_END -->


# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
1.參與了eth周會

2.參加了kitty老師的分享會，kitty老師真的非常用心花了很多時間去回答同學們的問題。

3.今天在周會中有提到cell-level messaging這個技術，我一開始還以為是生物方面的cell-level messaging

  
在區塊鏈圈所說的 cell-level messaging 與電信領域的細胞廣播並不相同，它着重於去中心化網絡裡的「廣播型訊息」機制。這種方式可以用來同步訊息、區域分發資料或者觸發事件。通常，鏈上的訊息廣播是針對整個鏈網絡，而不是地理上的某個「cell」；但未來這一概念有望跟地理定位、分片(sharding)技術結合，應用在 Web3、物聯網或注重隱私場景上。

區塊鏈對應的運作邏輯  
整個區塊鏈網絡，其實每一筆交易或每個區塊的產生和變動，本質都是一種「多點即時同步」。也就是說，只要資料一發布，會馬上透過網路廣播發送到每個節點，讓所有參與者都能保持同步。這就是區塊鏈一致性的基礎，大家的資料版本保持一致。

如果再加上分片（sharding）或是以地理為基礎的 DApp 服務，這機制未來可以進一步做到更細緻的「定向廣播」，就像電信領域針對特定 cell 廣播訊息一樣，讓某個分片或某塊地理區域的節點收到專屬訊息。

具體應用場景  
**1\. 搭配物聯網（IoT）**  
在像是智慧建築、智慧工廠這些環境，可以透過區塊鏈廣播實時推送警示、狀態變化、操作指令等訊息，只讓區域內裝置收到。最大好處是，所有廣播內容都能被區塊鏈「紀錄留痕」，出問題時追查可靠。

**2\. 去中心化災防告警**  
Web3 世界的災防系統，會需要把緊急訊息精準推送到受影響的分片或地理節點，減少無關區用戶收到干擾訊息。

**3\. 強化隱私的通知方式**  
如果私隱很重要，可以利用這種廣播的方式來發訊息，不必登錄用戶資料也不需建立名單，單純把訊息丟到區域，對應的節點自動收到，這對隱私保護型 DApp 很實用。

## 技術延伸

現在許多公鏈和 DApp 都已經用「全網廣播」來同步交易和區塊資料，只要節點在線上，就會自動接受到所有更新資料。這是點對點（P2P）網絡設計的優勢之一。

隨技術演進，Layer 2 和分片（sharding）有機會推進 cell-level messaging 讓資訊分發精確到小區域，未來可以用於精細告警、跨鏈同步等場景。

區塊鏈的 cell-level messaging，本質是去中心化廣播和分區域訊息同步；不只能幫助 IoT 區域告警，也能實現分片的定向通知，還有分布式隱私推播，這些都是未來 Web3 實際發展的重要應用
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
參與了黑客松DEMO DAY，把所有其他同學的項目都逐一看完了了解到了自己的許多不足之處，我是最後一個分享的感謝給我這個半殘的項目這個機會，也謝謝所有評審跟運營組的同學能把整個黑客松跟DEMO DAY辦的這麼好，我當初構思了很多項目但都感覺不夠好，所以最後換成了記憶碎片這個項目，但這個項目要會的東西實在有點多，我原本的SOLIDITY就只是算基礎等級而以，我只在一堂學校的區塊鏈課的期末作業寫過一個簡單版POLYMARKET，結果一下跳級挑戰這麼難的項目實在有點吃不消，不過過程中的確也學到了很多東西，例如PINATA這個ipfs API，我在做這個項目之前完全不知道ipfs是做什麼用的，也算是邊做邊學吧。仔細想想也的確是自己之前太好高騖遠了，我一開始的想法是做一個介紹不同鏈錢包的DAPP但感覺這東西只要WEB2就夠了沒必要搞成DAPP，所以做到一半又砍掉重練。這次黑客松真的學到很多不只是技術面還有心態面的問題，希望下次能更好吧。
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
做黑客松的項目，差點忘記打卡，先提交筆記紀錄一下。
<!-- DAILY_CHECKIN_2025-08-23_END -->


# 2025-08-22
<!-- DAILY_CHECKIN_2025-08-22_START -->
1.參加了黑客松OPEN DAY，聽了很多其他同學的項目分享，幸運的拿到盲盒。

2.參加了周會，分享了格基密碼學。

https://www.notion.so/2503180e0b7480858088fd566768ae9c?source=copy_link
<!-- DAILY_CHECKIN_2025-08-22_END -->

# 2025-08-21

***

## 今日任務進度與心得總結

### 1. Gas 優化案例及筆記

- 實際分析並優化 Solidity 合約的 gas 花費，嘗試了常見手法如選用較小的變數型態、調整儲存/運算順序，或減少 storage 操作。
- 通過 Remix 實測優化前後，記錄 gas 數據變化，理解不同設計對 gas 成本的影響。
- 撰寫了詳細的優化過程說明與個人筆記，也歸納出合約效能最佳化設計的重點心得。

***

### 2. 智能合約漏洞修復案例及筆記

- 針對 Solidity 合約常見的重入攻擊（Reentrancy）漏洞，實作了有問題的範例與安全修正版。
- 採用了「先改狀態、後互動」的安全模式，搭配 OpenZeppelin `ReentrancyGuard` 工具進行額外防護。
- 利用 Remix 進行漏洞測試對比，驗證修修後效果，並撰寫了一份條理分明的漏洞修復操作紀錄與安全心得。

***

### 3. 本地區塊鏈節點搭建與交互

- 使用 Hardhat 工具建立本地的以太坊測試鏈，獲得 20 組預設帳號、每個帳戶都擁有大量測試用 ETH。
- 撰寫並部署自訂的 Solidity 合約（Greeter），自動產生合約地址與交易記錄，真實還原主網部署流程。
- 制作與執行腳本，與本地合約進行自動化互動測試，順利完成讀取、修改狀態等一系列區塊鏈操作。
- 驗證節點日誌與交易紀錄內容，比對合約功能與鏈上數據，確認所有過程皆在本機環境下正確無誤，為後續進階開發打下良好基礎。
- 深刻理解本地鍊的開發環境建置、資金流程、安全性與靈活性，方便未來與前端結合或更進一步的自動測試與演練。

***

# 2025-08-20

---

# DAPP 基本架構四層解析

## 1. 區塊鏈層  
- **區塊鏈**  
  負責記錄所有交易與狀態，保障資料不可竄改及公開透明，是DAPP運作的信任基石。

***

## 2. 基礎設施層  
- **節點服務商 (RPC)**
  - 代表：Infura、Alchemy、QuickNode  
  - 提供節點連接服務，讓前端能與區塊鏈溝通。
- **IPFS服務**
  - 代表：Pinata、Infura  
  - 負責去中心化檔案存儲與分發，例如儲存NFT圖片或資料。
- **索引服務商 (Indexer)**
  - 代表：The Graph、Moralis  
  - 負責資料查詢與整理，協助DAPP快速獲取鏈上資料。
- **預言機 (Oracle)**
  - 代表：ChainLink  
  - 提供智能合約鏈外資訊，如價格、天氣等。

***

## 3. 抽象服務層  
- **智能合約**  
  DAPP所有核心邏輯撰寫於此，確保規則自動執行且不可更改。
- **後端服務**  
  提供輔助功能，如加速查詢、縮短等待時間、優化使用體驗（部分DAPP選配）。

***

## 4. 用戶交互層  
- **前端介面（網站/APP）**  
  使用者操作入口，負責互動與顯示。
  - **錢包鏈接組件（如MetaMask）**  
    負責用戶身份驗證、資產管理、簽署及提交交易。
  - **合約/鏈交互庫**  
    讓前端可直接呼叫智能合約、查詢鏈上資料。
  - **區塊鏈瀏覽器（如Etherscan）**  
    提供所有鏈上記錄與合約狀態查詢功能，資訊更透明。

***

## 學習心得
- 參與兩場分享會，獲得DAPP及其架構的全面認識。
- 理解四層架構之分工與常見工具，有助日後開發和技術交流。
- 觀摩HARDMAN老師展示的DAPP小DEMO

# 2025-08-19

1.參加了篝火晚會，聽到很多同學的分享讓我深受啟發。

2.初步確定了黑客松要做的內容:

# 教學導覽錢包專案說明

***

## 項目概念說明

本專案旨在讓用戶深入了解區塊鏈錢包的基本原理，結合理論與實作，透過互動式學習體驗掌握錢包生成、簽名驗證及安全防護，並減少常見誤區的發生。

***

## 主要功能設計

- **助記詞/私鑰生成教學**  
  用戶可在操作頁面依序產生 BIP39 助記詞、私鑰、公鑰與對應地址，並透過動畫或說明，理解生成過程中的密碼學原理。
  
- **安全簽名與交易驗證 Demo**  
  內建模擬場景，讓用戶可用自己的私鑰簽名訊息或模擬交易，並即時顯示簽章驗證原理與過程。安全環境下可實際體驗 ECDSA 等主流簽名演算法。

- **學習型 UI/UX**  
  視覺主題強調每一步都「可視化」，協助用戶掌握抽象的密碼學運作。系統會針對重要節點即時提醒知識要點與常見風險（如私鑰丟失、複製黏貼風險）。
  
- **多簽原理體驗**  
  提供簡化多簽合約互動（Multisig），用戶可理解「多重授權」及私鑰分權保護的基本概念。

***

## 操作流程及教學內容

### 1. 助記詞/私鑰生成教學

- 用戶於頁面按步操作：
  - 根據 BIP39 隨機生成助記詞。
  - 由助記詞導出私鑰。
  - 由私鑰推導公鑰。
  - 由公鑰計算生成錢包地址。
- 每一步均配有動畫或詳細分解說明，讓密碼學流程具象化與可追蹤。

### 2. 加密簽名與交易驗證 Demo

- 用戶可自行輸入/選擇訊息，使用私鑰進行簽名後，系統自動演示驗證過程，詳細展示 ECDSA 流程及簽名與驗證的加解密意義。
- 可模擬交易簽名，了解區塊鏈核心安全邏輯。

### 3. 學習型操作介面

- 操作畫面直觀且每一步皆有可視化提示（如助記詞安全警告、私鑰複製提示等）。
- 關鍵環節設有互動式警告，如「請勿截圖/複製/外洩私鑰」等提醒，強化用戶資安意識。

### 4. 多簽原理

- 用戶可自訂多個私鑰組合，模擬簡單多簽流程，理解「多數決」授權交易與私鑰分權如何提升資產安全。

***

## 適用場景

- 教學課程、培訓工作坊
- 新用戶入門區塊鏈應用
- 資安意識提升與防呆演練
- 開發者理解底層錢包機制

***

## 目標

- 讓區塊鏈錢包的加密與安全原理，從「黑盒」化為「透明」可視化過程。
- 降低新手常見失誤，提升資安防護基礎知識。
- 輕鬆體驗多簽等進階功能，建立分權與分級保護觀念。

# 教學導覽錢包專案說明

***

## 項目概念說明

本專案旨在讓用戶深入了解區塊鏈錢包的基本原理，結合理論與實作，透過互動式學習體驗掌握錢包生成、簽名驗證及安全防護，並減少常見誤區的發生。

***

## 主要功能設計

- **助記詞/私鑰生成教學**  
  用戶可在操作頁面依序產生 BIP39 助記詞、私鑰、公鑰與對應地址，並透過動畫或說明，理解生成過程中的密碼學原理。
  
- **安全簽名與交易驗證 Demo**  
  內建模擬場景，讓用戶可用自己的私鑰簽名訊息或模擬交易，並即時顯示簽章驗證原理與過程。安全環境下可實際體驗 ECDSA 等主流簽名演算法。

- **學習型 UI/UX**  
  視覺主題強調每一步都「可視化」，協助用戶掌握抽象的密碼學運作。系統會針對重要節點即時提醒知識要點與常見風險（如私鑰丟失、複製黏貼風險）。
  
- **多簽原理體驗**  
  提供簡化多簽合約互動（Multisig），用戶可理解「多重授權」及私鑰分權保護的基本概念。

***

## 操作流程及教學內容

### 1. 助記詞/私鑰生成教學

- 用戶於頁面按步操作：
  - 根據 BIP39 隨機生成助記詞。
  - 由助記詞導出私鑰。
  - 由私鑰推導公鑰。
  - 由公鑰計算生成錢包地址。
- 每一步均配有動畫或詳細分解說明，讓密碼學流程具象化與可追蹤。

### 2. 加密簽名與交易驗證 Demo

- 用戶可自行輸入/選擇訊息，使用私鑰進行簽名後，系統自動演示驗證過程，詳細展示 ECDSA 流程及簽名與驗證的加解密意義。
- 可模擬交易簽名，了解區塊鏈核心安全邏輯。

### 3. 學習型操作介面

- 操作畫面直觀且每一步皆有可視化提示（如助記詞安全警告、私鑰複製提示等）。
- 關鍵環節設有互動式警告，如「請勿截圖/複製/外洩私鑰」等提醒，強化用戶資安意識。

### 4. 多簽原理

- 用戶可自訂多個私鑰組合，模擬簡單多簽流程，理解「多數決」授權交易與私鑰分權如何提升資產安全。

***

## 適用場景

- 教學課程、培訓工作坊
- 新用戶入門區塊鏈應用
- 資安意識提升與防呆演練
- 開發者理解底層錢包機制

***

## 目標

- 讓區塊鏈錢包的加密與安全原理，從「黑盒」化為「透明」可視化過程。
- 降低新手常見失誤，提升資安防護基礎知識。
- 輕鬆體驗多簽等進階功能，建立分權與分級保護觀念。

# 2025-08-18

***



1. **完成智能合約開發**
   - 撰寫了基本的 Solidity 智能合約（MyStorage），具備 set/get 功能，可儲存和讀取 uint 整數。
   - 使用 Remix IDE 編寫、編譯並在本地 JS VM 成功部署與互動。

2. **部署合約到 Sepolia 測試網**
   - 使用 Remix 結合 MetaMask，把合約部署到 Sepolia 測試鏈。
   - 取得合約 address 與 ABI，準備進行實際前端串接。

3. **完成一個簡單 DApp（包括前端）**
   - 用 React（搭配 ethers.js v5）建立 DApp 前端。
   - 前端能連接 MetaMask 錢包，顯示錢包地址。
   - 實作從 DApp 讀取（get）合約上的儲存值，以及更新（set）合約內的數值，與鏈上智能合約互動均正常。
   - 成功排除 node/套件問題，讓前端伺服器正常啟動並與合約通訊。

4. **參加ETH中文周會**

4. **參加分享會"0xLuo - 链上社交：Farcaster"**

***

# 2025-08-17

晚上剛好有事可惜沒有參與到黑客松預熱的活動。

查看了並整理了ETHGlobal 黑客松近期的代表項目

ZKVoiceKey：用ZKP和語音生物特徵實現加密錢包恢復，提高身份安全及便利性。

BeamPay：以EIP-7702標準打造多鏈ERC20支付解決方案。

Industry AI：AI為核心的Web3代理平台，推動智能合約自動執行。

Octoplorer：自然語言查詢區塊鏈資料，結合AI提升使用者體驗。

DAOGenie：提升DAO治理工具鏈，促進社群自治和資源協商。

POAPrivacy、PrivyCycle：聚焦個人隱私和女性健康數據管理，妻女安全與資料加密。

GameFi 專案 (LootGO, BubbleWars, Autonomous Game of Life)：結合鏈遊、社交與激勵，創新鏈上遊戲基礎。

# 2025-08-16

1.參與了同學舉辦的TWITTER SPACE，問題準備的很用心謝謝運營組的同學。

2.深入了解了什麼是黑客松並開始想具體的項目

3.閱讀了QRL白皮書

# 2025-08-15

1.今天參加了這禮拜的周會，原本也想參與分享的但是忘了找LUNA老師報名了，附上我整理的分享筆記:
https://www.notion.so/2503180e0b7480858088fd566768ae9c?source=copy_link

我有篩選過內容，這算是比較簡單的密碼學知識，運營向的同學應該也能吸收，下周分享會我會記得報名並連同本周的內容一起分享。

2.完成任務:阅读 https://nft.myfirst.io/ 并且 Mint 获取自己的第一个 NFT → +20

# 2025-08-14

1.完成研討會摘要

Abstract

The rapid development of quantum computing has posed a “harvest-now, decrypt-later” threat to the ECDSA digital signature scheme underpinning blockchain transaction verification, making the adoption of Post-Quantum Cryptography (PQC) an urgent necessity. This study focuses on the EPERVIER Falcon signature extension with address recovery capability, evaluating its feasibility and migration costs as a potential replacement for ECDSA within the Ethereum Virtual Machine (EVM) environment.
The work employs engineering optimizations present in the EPERVIER implementation, including: (1) a gas-optimized SHAKE256 hash function to reduce computation costs; (2) forward Number Theoretic Transform (Forward-NTT) to avoid inverse transformation overhead; and (3) constant-time operations and randomized sampling to mitigate side-channel attacks. Testing in a smart contract environment shows that the verification cost is approximately 1.9M gas, representing a ~12.6× efficiency improvement over the baseline Falcon implementation’s 24M gas. Although the signature size is 2,120 bytes—larger than ECDSA’s 65 bytes—it remains practical in Layer 2 scenarios while offering quantum resistance advantages.
This study conducted an end-to-end empirical validation on the Optimism Sepolia testnet. The validation covered the entire process, including local signature generation, transaction submission, on-chain storage, and contract-side verification with address recovery. We also proposed a hybrid deployment strategy that mandates PQC verification for critical operations while retaining an ECDSA-compatible path, thereby mitigating the complexity and risks of system migration. The results demonstrate the technical feasibility of deploying the EPERVIER Falcon signature scheme with address recovery in a blockchain environment. Although this scheme introduces higher computational and storage overhead compared to traditional ECDSA, it provides quantum-safe protection by leveraging EVM optimizations and the cost advantages of Layer 2 solutions. This is achieved while maintaining compatibility with the existing ecosystem, offering a concrete technical solution and empirical evidence for the blockchain's transition into the post-quantum era.

Keywords: Post-Quantum Cryptography, Falcon, address Recovery (Ecrecover), EVM, Optimism Sepolia, Layer 2

2.今天參加了兩場分享會，其中讓我比較深刻的是吳老師的分享，其中的內容不管運迎向或技術向都非常實用，老師講解了很多頁內相關知識例如:行業現狀、進入web3須具備的能力、非技術崗位等等，雖然我個人是偏技術向，但這就是我當初想加入web3實習計畫的最主要目的:與行業接軌，老師後續QA所回答的信息源獲取、確定熱點、培養敏感度也都是我之前打鏈上有疑惑的內容，非常感謝實習計畫讓我有這個機會聽到這個分享會。

# 2025-08-13

1.晚自習

2.今天參加了兩場分享會一場技術向一場運營向，運迎向的內容算是讓我比較印象深刻
，了解了一場黑客松從籌備到結束的整個過程。

3.統整ETHFALCON GITHUB項目中三種方案的差別
 
原版FALCON:
完全符合NIST FALCON標準
使用SHAKE哈希算法

原版FALCON：標準化 - 為了NIST兼容性
ETHFALCON：性能化 - 為了EVM極致性能
EPERVIER：功能化 - 為了地址恢復創新
選擇依據不是版本新舊，而是具體需求：
支持外部NTT合約
Gas成本較高 (7M)
性能不是最優

ETHFALCON:
哈希算法優化：keccak256比SHAKE快70%
內存訪問優化：批量處理，緩存友好
EVM字節碼優化：使用assembly直接操作
代碼簡潔：邏輯清晰，易於維護
Gas成本優化：1.9M (相比原版7M)

EPERVIER:
獨特功能：從簽名直接恢復地址
無需預存公鑰：簡化部署流程
增強隱私性：減少公鑰信息暴露
相同Gas成本：1.9M
NIST標準兼容：使用SHAKE算法

原版FALCON：標準化 - 為了NIST兼容性

ETHFALCON：性能化 - 為了EVM極致性能

EPERVIER：功能化 - 為了地址恢復創新

# 2025-08-12

1.參加了8/12的綜合項產品分析分享會

2.晚自習

3.重看了一次昨天的DAPP從0到部屬，老實講昨天並沒有完全吸收。

4.嘗試了解Foundry

# 2025-08-11

1晚自習
2ETH周會
3技術會

# 2025-08-10

1晚自習
2大改專題方向，不要執著於在ETH上實現FALCON，嘗試從協議層開始修改創造新鏈。

# 2025-08-09

1.晚自習
2.準備明天MEETING
3.提前閱讀技術方向手冊

# 2025-08-08

1.參加了實習計畫的例會，每個人分享的內容都不太一樣讓我理解到了我舒適區外的知識。我也分享了些密碼學知識。
2.晚自習，完成了些這禮拜還沒完成的任務EX:釣魚攻防網站，發布學習總結TWITTER。

# 2025-08-07

1.參加法律分享會
2.完善區塊鏈密碼學相關筆記
3.有點久沒寫SOLIDITY了，回去複習了一下語法

# 2025-08-06

1晚自習
2參與了故事會，感覺馬老師對行業的理解非常深刻，收穫頗豐。故事會最後的提問也非常有意思，我會深入研究這幾個問題。

# 2025-08-05

1參加知識分享會
2晚間自習

# 2025-08-04

1參加中文週會
2複習了ETH交易流程
3稍微推進畢業專題進度
4由於之前TALLY延遲無法登記學分，補登記了所有已完成學分


# 2025.08.02


<!-- Content_END -->
