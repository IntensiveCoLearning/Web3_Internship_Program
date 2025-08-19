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
# 2025-08-19

1參加了篝火晚會，聽到很多同學的分享讓我深受啟發。

2確定了黑客松要做的內容:

去中心化安全錢包是原型一個結合密碼學基礎教育與實用性的新手友好項目，核心目標是設計一個能「幫助用戶學會並實踐錢包安全與加密技術」的小型應用，讓參與者和用戶在操作過程中了解區塊鏈安全的基本機制。

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
