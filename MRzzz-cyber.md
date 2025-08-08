---
timezone: UTC+8
---

# Marcus

**GitHub ID:** MRzzz-cyber

**Telegram:** @Marcuszheng

## Self-introduction

一定好好学习

## Notes

<!-- Content_START -->

# 2025.08.04
今天有一些同学提交了关于 Uniswap 的解释错误的 BUG，这边我去查看了一下
<img width="1228" height="891" alt="image" src="https://github.com/user-attachments/assets/ff712140-27d2-44b0-8d28-cb3b0b482e23" />


其实介绍的没有错，这部分 X*Y=K 的数值依然是 AMM 算法的核心，这里其实还有一个没有介绍，就是无常损失

### 无常损失

什么是无常损失（Impermanent Loss）？
简单定义：无常损失是指流动性提供者（LP）在自动做市商（AMM）池中存入代币后，因代币价格波动导致其资产价值比单纯持有代币更低的现象。这种损失是“无常”的，因为如果价格回到存入时的初始比例，损失会消失（但若价格永久偏离，损失则变为永久）。

无常损失是如何产生的？
AMM（如 Uniswap）依赖恒定乘积公式（x × y = k）维持流动性池的平衡。当代币价格变动时，池中两种代币的数量会自动调整以满足公式，导致 LP 持有的代币比例变化。

<img width="1164" height="811" alt="image" src="https://github.com/user-attachments/assets/fd516a8e-4577-4cab-89a3-dff96fc8cb7d" />

<img width="1092" height="867" alt="image" src="https://github.com/user-attachments/assets/b4240a52-6263-4615-81c4-5df6e55690c0" />

简单来说，代币的波动区间越大，无常损失越高，如果 ETH 在短时间内急速拉升，你的手续费收益很难去拉平无常损失

### Uniswap V3 的核心改进
1. 集中流动性（Concentrated Liquidity）

V2 问题：流动性提供者（LP）的资金均匀分布在整个价格区间（0→∞），导致大部分资金未被利用（例如 ETH/USDC 池中，若价格长期在 1000-2000 之间波动，超出该范围的流动性几乎无用）。

V3 解决方案：LP 可自定义价格区间（如 ETH/USDC 仅在 1500-2500 提供流动性），资金集中在最可能交易的区间，提升资本效率。

影响：相同交易量下，V3 的 LP 收益可能远高于 V2（但需承担更高的无常损失风险）。

2. 多费率等级（Fee Tiers）

V3 支持 0.05%、0.30%、1% 等不同手续费等级，适应不同波动性的资产对（如稳定币对用低费率，长尾代币用高费率）。

3. 范围订单（Range Orders）

LP 可通过设置单边流动性区间（如“仅在 ETH > 2000 USDC 时卖出ETH”），实现类似限价订单的功能。

4. 改进的价格预言机

V3 提供更高效的时间加权平均价格（TWAP）预言机，降低链上数据调用成本。

V3 与 V2 的对比示例
V2 流动性：
假设 ETH/USDC 池有 10 ETH + 10,000 USDC（k=100,000），无论价格如何变动，流动性均匀分布。

V3 流动性：
LP 可选择仅在 1000-2000 USDC/ETH 区间提供 1 ETH + 1000 USDC。若价格在此区间内，资本效率是 V2 的 10 倍（因为 V2 需要 10 ETH 才能覆盖相同深度）。


# 2025.08.05
1. 听了一下晚上的安全分析课程，了解到了建立熟悉网络的攻击过程
2. 做了明天故事会分享的 PPT，再次阅读了 DPOS 制度所带来的寻租卡特尔弊端
https://docs.google.com/presentation/d/1dXkhUXQZG8BBFr8-_3GvKvlYIS3SIl7PP9eE-hWDPME/edit?slide=id.g375bec490fd_0_497#slide=id.g375bec490fd_0_497

# 2025.08.06
1. 整理了一下 BM 和 Vitalik 的往事
https://docs.google.com/presentation/d/1dXkhUXQZG8BBFr8-_3GvKvlYIS3SIl7PP9eE-hWDPME/edit?usp=sharing
<!-- Content_END -->

# 2025.08.07
今天看安全系列分享的时候，想起来了之前遇见的过一些事，Tron 助记词钱包钓鱼，具体实现原理是通过多签控制钱包的方式来进行钓鱼，只要往里面打钱就会被转走
还有就是不要轻信任何线下出 U 的人，有好好的交易所不走，为什么要线下出呢

# 2025.08.08
整理了 Web3 实习计划的 Dashboard
https://docs.google.com/spreadsheets/d/10ZdCc4qrvcl5m0nwLafzkPyv55zQcZ5wabtc2UDT1wc/edit?gid=1527940251#gid=1527940251
