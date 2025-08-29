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

# 2025-08-29
<!-- DAILY_CHECKIN_2025-08-29_START -->
\# 隐私池（Privacy Pool）ZK 学习总结

\## 核心概念

\- **note**`{ value, secret, nullifier }`（用户私密见证）

\- **commitment**`Poseidon(value, secret, nullifier)`，作为 Merkle 树叶

\- **nullifierHash**`Poseidon(nullifier)`，提现时公开用于防双花

\- **Merkle Root**：承诺当前“有效承诺集合”的全局承诺

\## 存款（Deposit）流程

\- 生成 `note`（随机 `secret/nullifier`）与 `commitment`：

\- `commitment = Poseidon3([value, secret, nullifier])`

\- `nullifierHash = Poseidon1([nullifier])`

\- 将 `commitment` 插入 IMT（二叉 Merkle 树），更新根并持久化叶子数组与 `note`。

\- 作用：

\- `commitment` 进入公共集合（树）作为匿名资格

\- `note` 由用户私密保存，后续提现时作为见证使用

示例（简化）：

\`\`\`ts

const note = { value, secret: rand(), nullifier: rand() };

const commitment = poseidon3(\[note.value, note.secret, note.nullifier\]);

tree.insert(commitment); // 更新根

storage.setLeaves(tree.leaves);

storage.setNote(note);

\`\`\`

\## 为什么要用 Merkle Tree

\- 仅知道 `commitment` 原像，无法证明“它属于系统的有效存款集合”，会允许伪造资金。

\- Merkle 树提供“集合承诺”与“隐私友好的成员证明”（只公开根与路径，O(log N) 验证）。

电路中的成员证明（重算根并比对公开根）：

\`\`\`nr

let _merkle_root = binary\_merkle\_root(hash\_2, commitment, len, indices, siblings);

assert(merkle\_root == _merkle_root, "merkle roots don't match");

\`\`\`

\## 提款（Withdraw）与电路

\- 私有见证`value, secret, nullifier, new_secret, new_nullifier`

\- 公共输入（参数 pub）`withdrawAmount`, `merkle_root`

\- 公共输出（return pub）`[nullifier_hash, new_state_commitment_or_0]`

电路关键逻辑（简化）：

\`\`\`nr

assert(withdrawAmount <= value, "withdraw amount exceeds balance");

let commitment = H3(value, secret, nullifier);

assert(merkle\_root == recomputed\_root(commitment, path));

let nullifier\_hash = H1(nullifier);

let new\_balance = value - withdrawAmount;

return new\_balance == 0

? \[nullifier\_hash, 0\]

: \[nullifier\_hash, H3(new\_balance, new\_secret, new\_nullifier)\];

\`\`\`

\- 含义：

\- 证明你“知道能开出该承诺的原像”且该承诺在树中

\- 金额合法

\- 输出 `nullifier_hash`（登记防双花）

\- 若部分提现，输出“新状态承诺”供继续匿名持有余额

\## 公共输入 vs 公共输出

\- **公共输入（pub 参数）**：外部先给，电路用来校验（如 `merkle_root`, `withdrawAmount`）。

\- **公共输出（pub 返回值）**：电路计算后公开给验证者使用（如 `nullifier_hash`, `new_commitment_or_0`），确保与私密见证一致，防止外部伪造这些结果。

\## 证明与验证流程

\- 生成证明：

\- 本地计算 `commitment` 在树中的 `merkle_proofindices/siblings`）

\- Noir 执行得到 `witness`，Barretenberg 生成 `proof`

\- 验证证明：

\- 调用 `verifyProof(proof)ProofData` 内已包含公共输入）

\- 业务侧还应比对期望的 `merkle_root/withdrawAmount` 与 `proof.publicInputs`，再登记 `nullifierHash`、插入新承诺（若有）

示例（简化）：

\`\`\`ts

const idx = tree.indexOf(note.commitment);

const p = tree.createProof(idx);

const { witness } = await noir.execute({

value: note.value, secret: note.secret, nullifier: note.nullifier,

new\_secret, new\_nullifier,

withdrawAmount, merkle\_root: tree.root.toString(),

merkle\_proof\_length: p.siblings.length,

merkle\_proof\_indices: p.pathIndices,

merkle\_proof\_siblings: [p.siblings.map](http://p.siblings.map)(v => v.toString()),

});

const proof = await backend.generateProof(witness);

const ok = await backend.verifyProof(proof);

\`\`\`

\## 存储结构（键值对）

\- 介质`unstorage` + FS 驱动（目录 `./db`）

\- 键：

\- `tree:leaves`：叶子数组（字符串化）

\- `note{ value, secret, nullifier }`（读取时再计算 `commitment/nullifierHash`）

\- `nulliferHashMap:<hash>`：布尔值（已登记即防双花）

\- 运行时用叶子数组重建 IMT，并在每次更新后持久化。

\## Web 与 CLI

\- 电路源码共享`src/main.nr`）；电路产物分别位于根 `target/` 与 `web-app/circuit/`。

\- 证明生成逻辑两端各自实现但流程一致（Noir 执行 → BB.js 生成/验证证明）。

\## 风险与实践建议

\- 依赖版本统一`@aztec/bb.js@noir-lang/noir_js`）以避免不兼容。

\- 构建时同步电路产物，避免前端/CLI 产物不一致。

\- 验证时务必比对期望的公共输入`merkle_root/withdrawAmount` 等）。

\- 妥善保管 `note`（尤其 `secret/nullifier`），它等价于“匿名资金的私钥”。
<!-- DAILY_CHECKIN_2025-08-29_END -->


# 2025-08-28
<!-- DAILY_CHECKIN_2025-08-28_START -->
```markdown
# PLONK 学习笔记

## 概述
PLONK（Permutation over Lagrange-bases for Oecumenical Noninteractive arguments of Knowledge）是一种通用、高效的零知识证明（ZKP）协议，广泛用于隐私保护和可扩展的区块链应用。它通过多项式承诺和排列论证实现高效的证明生成和验证。

## 核心特点
1. **通用性**：支持任意电路，无需为每个应用定制电路（如 zk-SNARKs 的 trusted setup）。
2. **无信任设置**：使用通用参考字符串（Universal SRS），一次生成可重复使用。
3. **高效性**：证明大小较小，验证时间短，适合区块链场景。
4. **模块化**：结合多项式承诺（如 KZG）和排列检查，易于优化。

## 工作原理
1. **电路表示**：将计算问题表示为算术电路，转换为多项式约束。
2. **多项式承诺**：
   - 使用 KZG 承诺方案，将电路约束编码为多项式。
   - 证明者生成多项式承诺，验证者检查承诺有效性。
3. **排列论证**：通过排列多项式验证输入输出的正确映射，减少约束检查开销。
4. **证明生成**：
   - 证明者生成证明，包含多项式评估和承诺。
   - 验证者通过少量查询验证证明，效率高。
5. **通用 SRS**：一次生成，适用于多种电路，消除 per-circuit 的信任设置。

## 与 Panagram 的关系
- Panagram 使用 UltraHonk（基于 PLONK 的改进版）作为后端（`@aztec/bb.js`），生成和验证零知识证明。
- PLONK 的高效性支持 Panagram 的链下证明生成和链上验证，降低 gas 成本。

## 关键优势
- **灵活性**：支持复杂电路，适合如 Panagram 的隐私保护游戏。
- **可扩展性**：小证明大小和快速验证，适用于区块链。
- **安全性**：依赖多项式承诺和 Fiat-Shamir 转换，抗量子攻击（需选择合适承诺方案）。

## 学习资源
- **文档**：PLONK 原始论文（https://eprint.iacr.org/2019/953）。
- **工具**：`@aztec/bb.js`（Barretenberg 的 JS 实现，支持 PLONK 和 UltraHonk）。
- **实践**：参考类似 `github.com/cyfrin/zk-panagram` 的代码，理解 PLONK 在 Web3 应用中的实现。

## 总结
PLONK 是一种高效、通用、无信任设置的零知识证明协议，广泛应用于区块链隐私场景。其在 Panagram 中的使用展示了它在生成高效证明和保护用户隐私方面的强大能力。
```
<!-- DAILY_CHECKIN_2025-08-28_END -->


# 2025-08-27
<!-- DAILY_CHECKIN_2025-08-27_START -->
```markdown
# 学习零知识证明（ZKP）后的应用与实践指南

## 1. ZKP 核心概念
零知识证明（ZKP）允许在不泄露信息的情况下证明某计算或知识的正确性。SNARK（简洁非交互式知识论证）和 STARK（可扩展透明知识论证）是两种主流 ZKP 系统，掌握它们可应用于区块链、隐私保护、身份认证等领域。

### 1.1 SNARK 与分工
- **特点**：简洁（证明大小小，约几百字节），需可信设置（Trusted Setup）。
- **适用场景**：区块链（如 zk-Rollups）、隐私智能合约。
- **关键技术**：有限域（Finite Fields）、多项式承诺（Polynomial Commitments）、椭圆曲线（ECC）、算术电路（Arithmetic Circuits）。

### 1.2 STARK
- **特点**：无需可信设置，抗量子计算，证明大小较大（几 KB 到 MB）。
- **适用场景**：高安全性、大规模数据验证。
- **关键技术**- 同 SNARK，但使用 FRI（Fast Reed-Solomon Interactive Oracle Proofs）。

### 1.3 学习重点
- **数学基础**：理解有限域（Finite Fields，如 FP7，模 7 运算）、多项式、ECC。
- **电路设计**：将计算转化为 ZK 电路（如 Merkle Root 验证）。
- **优化技术**：如 Lookup Tables、Co-processors、Recursion。
- **工具**：Circom、Halo2（SNARK）、Cairo（STARK）、Zokrates。

## 2. 具体应用场景
掌握 SNARK/STARK 后，你可以在以下领域实践：

### 2.1 区块链与 Layer 2 解决方案
- **zk-Rollups**：
  - **任务**：
    - 开发 zkEVM/zkVM 电路（验证 EVM opcodes 或 RISC-V 指令，如 ADD、CALL）。
    - 优化证明生成时间（并行化、Co-processors）。
    - 实现状态更新（Merkle Patricia Trie、RLP 编码）。
  - **示例**：为 Scroll 优化 CALL 电路，降低 gas 成本；为 StarkNet 开发隐私 Cairo 程序。
  - **工具**：Halo2、Plonky2、Circom。

- **隐私 DeFi/智能合约**：
  - **任务**：
    - 设计隐藏交易细节的合约（如金额、地址）。
    - 开发隐私投票系统。
  - **示例**：为 Uniswap 设计隐私池。

### 2.2 身份与认证
- **去中心化身份（DID）**：
  - **任务**：
    - 开发 ZK 身份证明电路（如证明“年满 18 岁”）。
    - 优化移动端证明生成。
  - **示例**：为 KYC 系统设计 SNARK 电路，隐藏身份证号。
- **匿名认证**：
  - **任务**：实现 ZK 签名（Signatures of Knowledge）。
  - **示例**：为 SSO 系统设计匿名登录。

### 2.3 数据隐私与安全
- **隐私计算**：
  - **任务**：
    - 证明 ML 模型推理结果（如 STARK 验证医疗数据）。
    - 结合 MPC（多方计算）与 ZKP。
  - **示例**：为医院设计隐私统计系统。
- **云服务隐私**：
  - **任务**：验证云端计算（如 AWS SQL 查询）。
  - **示例**：为 Lambda 设计 SNARK 隐私证明。

### 2.4 密码学研究与工具开发
- **改进 Proving Systems**：
  - **任务**：
    - 优化多项式承诺（KZG vs FRI）。
    - 开发新 Lookup Tables 或 Recursion。
    - 贡献 Halo2/Plonky2（如改进 error messages）。
  - **示例**：为 GKR 系统优化性能（参考 PSE 合作）。
- **开发 ZK 工具**：
  - **任务**：
    - 开发 DSL（如 Circom 的 Merkle Root 电路）。
    - 优化编译器（Rust → ZK 电路）。
  - **示例**：为 zkVM 开发 RISC-V 编译器。

### 2.5 其他创新应用
- **游戏与 NFT**：设计公平游戏或隐私 NFT 电路。
- **供应链**：用 STARK 验证供应链数据（如产品来源）。
- **示例**：为 Axie Infinity 设计 ZK 抽卡系统。

## 3. 职业方向
- **ZK 工程师**：在 Scroll、StarkWare、Polygon 开发 zk-Rollups/zkVM。
- **密码学研究员**：研究 GKR、Plonky2 等（参考 transcript 的 GKR 探索）。
- **隐私工程师**：为 DeFi/DID 设计隐私方案。
- **开源贡献者**：为 Halo2、Circom 贡献代码。
- **创业者**：开发 ZK 应用（如隐私支付）或工具。

## 4. 学习路径
结合 transcript（从 zkEVM 到 zkVM）的建议：
1. **数学基础**：
   - 学习有限域（FP7 示例：3 ≡ 10 mod 7）、ECC。
   - 资源：OpenZeppelin YouTube（10-15min 密码学教学）。
2. **SNARK/STARK**：
   - SNARK：Halo2、Circom、Groth16。
   - STARK：Cairo、FRI、StarkNet 文档。
3. **实践**：
   - 用 Circom 写 Merkle Root 电路。
   - 用 Risc0/SP1 编译 Rust 到 ZK 证明。
   - 贡献 PSE/Scroll 的 GKR 项目。
4. **社区**：
   - 参加 East Taipei、ZK Hack。
   - 关注 X 上的 @zksecurity、@StarkWareLtd。

## 5. zkEVM vs zkVM 的启发
- **zkEVM 痛点**（transcript 46:41-49:02）：
  - 审计复杂（如 700 行 CALL 电路）。
  - 升级难（hard forks 需重写）。
  - 性能慢（老系统无法及时生成 proof）。
- **zkVM 优势**（transcript 54:56-56:05）：
  - 通用：编译任意代码到 RISC-V，无需改电路。
  - 简单 opcode 易审计/验证。
  - 生态成熟（如 RISC-V 工具）。
- **趋势**：GKR 等新系统（58:31）提供更快证明。

## 6. 示例项目
1. **简单**：用 Circom 证明“某数的平方”并部署到 zkSync。
2. **中级**：用 Cairo 开发 StarkNet 隐私投票。
3. **高级**：为 Risc0 优化椭圆曲线 co-processor。
4. **创业**：开发 ZK 隐私浏览器插件。

## 7. 常见问题
- **数学深度**：基础代数（有限域、多项式）够入门，深入需数论。
- **时间投入**：3-6 个月基础，1-2 年精通。
- **前景**：Layer 2、DeFi、DID 需求旺盛。
- **SNARK vs STARK**：SNARK 适合小证明，STARK 适合高安全。

## 8. 下一步
- **选择方向**：区块链（zk-Rollups）、隐私（DID）、研究（GKR）。
- **实践**：用 Circom 写小电路，跑证明。
- **提问**：指定感兴趣领域（如 DeFi），可提供代码示例。
```
<!-- DAILY_CHECKIN_2025-08-27_END -->


# 2025-08-26
<!-- DAILY_CHECKIN_2025-08-26_START -->
````markdown
# Panagram 项目学习笔记

## 项目概述
Panagram 是一个基于零知识证明（Zero-Knowledge Proof, ZKP）的 Web3 应用，采用三层架构设计，确保功能分离和高效运行。以下是其核心组成部分：

1. **前端（React/TypeScript）**：用户交互界面，负责玩家输入、证明生成及与区块链的通信。
2. **零知识电路（Noir）**：定义了以零知识方式证明玩家知道秘密单词的逻辑，是隐私保护的核心。
3. **智能合约（Solidity）**：部署在区块链上，管理游戏状态、验证链上证明并处理 NFT 奖励的铸造。

## 用户体验：Panagram 的游戏流程
玩家通过 React 和 TypeScript 构建的网页界面与 Panagram 互动，主要 UI 元素包括：

- **单词拼图**：展示当前秘密单词的乱序字母，挑战玩家解谜。
- **输入框**：玩家输入猜测的单词。
- **提交按钮**：触发零知识证明生成和提交流程。
- **NFT 展示区**：显示玩家获得的 NFT，分为：
  - **“获胜次数”**：首个正确猜测秘密单词的玩家获得。
  - **“正确但未获胜”**：在其他玩家获胜后正确猜测的玩家获得。

## 核心机制：零知识证明生成
当玩家提交猜测时，前端会执行以下步骤生成零知识证明：

1. **输入处理**：
   - 玩家输入的猜测通过前端代码（主要在 `src/components/Input.tsx` 中）捕获。
   - 猜测被哈希（例如使用 keccak256），并对一个字段素数取模，以确保与零知识电路兼容。此哈希值作为**私有输入**，不公开。

2. **使用 NoirJS 生成见证（Witness）**：
   - 前端通过 `@noir-lang/noir_js` 执行预编译的 Noir 电路（通常为 `panagram.json`）。
   - 电路接受私有输入（哈希后的猜测）和公共输入（包括玩家以太坊地址和当前轮次正确答案的哈希）。
   - 执行电路生成一个**见证**，即满足电路约束的所有变量赋值集合。

3. **使用 BB.js 生成证明**：
   - 生成的见证和电路字节码传递给 `@aztec/bb.js` 的 `UltraHonkBackend`，生成零知识证明。
   - 证明是一个紧凑的密码学结构，证明玩家知道秘密单词但不泄露具体信息。

4. **链下验证**：
   - 在提交到区块链之前，使用 BB.js 进行链下验证，确保证明有效，提供即时反馈并节省交易费用。
   - 相关逻辑主要在 `src/utils/generateProof.ts` 中实现。

## 隐私核心：Noir 零知识电路
Panagram 的隐私保护逻辑位于使用 Noir 语言编写的零知识电路中（通常在 `circuits/src/main.nr`）。其特点包括：

- **私有输入**：玩家的猜测哈希值。
- **公共输入**：
  - 当前轮次正确秘密单词的哈希值。
  - 玩家的以太坊地址，防止证明重放攻击并确保奖励分配给正确玩家。
- **断言逻辑**：电路检查私有输入（猜测哈希）是否与公共输入（正确答案哈希）匹配。
- 电路编译为 ACIR 和 ABI（存储在 `circuits/target/panagram.json`），供前端使用。

## 链上信任：Solidity 智能合约
Panagram 的游戏状态和奖励系统由部署在以太坊兼容区块链上的 Solidity 智能合约管理，位于 `contracts` 文件夹。主要包括：

1. **验证合约（Verifier.sol）**：
   - 由 Barretenberg CLI 根据 Noir 电路自动生成，负责链上验证零知识证明。
   - 包含从电路结构派生的数学逻辑。

2. **主游戏合约（Panagram.sol）**：
   - **状态管理**：跟踪轮次编号、当前秘密单词哈希及获胜玩家记录。
   - **makeGuess 函数**：玩家提交证明的入口，接受前端生成的证明和公共输入。
   - **链上验证**：调用验证合约的 `verify` 函数检查证明有效性。
   - **奖励分发**：
     - 如果证明有效，合约根据玩家表现铸造 ERC-1155 NFT：
       - 首个获胜者获得 Token ID 0（“获胜次数”）。
       - 非首个正确猜测者获得 Token ID 1（“正确但未获胜”）。
     - 更新玩家的 NFT 余额。
   - **轮次管理**：在轮次获胜后更新秘密单词哈希并重置状态。

## 代码解析：证明生成与提交
以下是证明生成和提交的关键代码片段：

### 证明生成（`src/utils/generateProof.ts`）
```typescript
import { UltraHonkBackend } from "@aztec/bb.js";
import circuit from "../../circuits/target/panagram.json";
import { Noir } from "@noir-lang/noir_js";
import { ANSWER_HASH } from "../constants";

export async function generateProof(guess: string, address: string) {
    const noir = new Noir(circuit); // 初始化 Noir 电路
    const honk = new UltraHonkBackend(circuit.bytecode); // 初始化后端
    const inputs = {
        guess,           // 私有输入：猜测哈希
        address,         // 公共输入：玩家地址
        expected_hash: ANSWER_HASH // 公共输入：正确答案哈希
    };
    const witness = await noir.execute(inputs); // 生成见证
    const { proof, publicInputs } = await honk.generateProof(witness, { keccak: true }); // 生成证明
    const isValid = await honk.verifyProof({ proof, publicInputs }); // 链下验证
    return { proof, publicInputs }; // 返回证明和公共输入
}
```

### 提交猜测（`src/components/Input.tsx` 中的 `handleSubmit`）
- 获取玩家输入并哈希（例如 `keccak256(toUtf8Bytes(guessInput))`）。
- 调用 `generateProof` 生成证明。
- 使用 Viem 或 Ethers.js 提交证明到智能合约：
```typescript
await writeContract({
    address: PANAGRAM_CONTRACT_ADDRESS,
    abi: panagramAbi,
    functionName: "makeGuess",
    args: [`0x${uint8ArrayToHex(proof)}`]
});
```

## 游戏场景示例
假设秘密单词为“outnumber”：
1. 玩家输入“outnumber”并点击提交。
2. 前端生成证明，日志显示：
   - "Generating witness... ⌛️"
   - "Generated witness... ✔️"
   - "Generating proof... ⌛️"
   - "Generated proof... ✔️"
   - "Verifying proof off-chain... ⌛️"
   - "Proof is valid (off-chain): true ✔️"
3. 玩家通过钱包（如 MetaMask）签名并提交交易。
4. 链上验证通过后：
   - 如果是首个获胜者，铸造 Token ID 0 的 NFT。
   - UI 更新 NFT 余额，显示新轮次字母。

## 关键经验总结
1. **技术协同**：Panagram 结合前端（React/TypeScript）、零知识电路（Noir）和智能合约（Solidity），展示了 Web3 应用的复杂性。
2. **隐私保护**：零知识证明允许玩家证明知道秘密单词而不泄露信息。
3. **用户体验**：链下证明生成和验证提供快速反馈，降低链上交易成本。
4. **链上信任**：智能合约的链上验证确保去中心化和可验证性。
5. **灵活代币化**：ERC-1155 标准支持多种 NFT 类型，适合多奖励场景。

## 推荐工具与资源
- **Noir 语言与 NoirJS**：`@noir-lang/noir_js` 用于在 JavaScript 环境中与 Noir 电路交互。
- **Barretenberg 与 BB.js**：`@aztec/bb.js` 用于生成和验证零知识证明。
- **参考代码**：可参考类似 `github.com/cyfrin/zk-panagram` 的代码库。

通过学习 Panagram，开发者可以掌握构建隐私保护型去中心化应用的技能。
````
<!-- DAILY_CHECKIN_2025-08-26_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
````markdown
# zkVM学习笔记

## 1. 什么是zkVM？
- **定义**：零知识虚拟机（Zero-Knowledge Virtual Machine），是一种抽象层，允许开发者用高级语言（如Rust、C++）编写程序，自动编译为低级代码并生成零知识证明（ZKP），无需深入了解底层ZKP参数。
- **核心思想**：复用现有CPU架构和编译流程，简化ZKP开发。开发者只需关注高级逻辑，zkVM处理约束和证明生成。
- **优势**：
  - 不需要手动定义约束或学习特定领域语言（如Circom）。
  - 易于审计：逻辑在高级语言中，代码清晰。
  - 标准开发流程：用现有工具链（如Rust的LLVM）。
  - 开发效率高：如斐波那契序列，传统ZKP需1-2小时写约束，zkVM只需几行Rust代码。

## 2. ZKP基础回顾
- **零知识证明（ZKP）**：证明者（Prover）向验证者（Verifier）证明某语句为真，且不透露额外信息。
- **场景示例**：证明者知道消息M（哈希函数预像），哈希(M)=零哈希，但不透露M。
- **ZKP三大属性**：
  - **完整性（Completeness）**：真语句，诚实双方，验证通过。
  - **可靠性（Soundness）**：假语句无法生成有效证明。
  - **零知识（Zero-Knowledge）**：验证者只知道公共参数，不获知秘密。
- **SNARKs vs. STARKs**：
  - **SNARKs**（Succinct Non-interactive ARguments of Knowledge）：
    - 需要可信设置（Trusted Setup）。
    - 证明大小恒定，验证快（例：Groth16验证用200k-300k gas）。
    - 基于ECC（椭圆曲线）密码学。
    - 例子：Groth16、Plonk。
  - **STARKs**（Scalable Transparent ARguments of Knowledge）：
    - 无需可信设置，抗量子攻击。
    - 证明大小随计算规模增加，验证稍慢。
    - 基于哈希函数。
    - 例子：StarkNet、Herodotus。
  - **zkVM偏好**：多用STARKs生成证明（易约束），有时转为SNARKs用于链上验证（如Ethereum）。

## 3. zkVM的核心组件
- **虚拟机（VM）**：软件程序，继承硬件资源，执行通用计算，生成执行追踪（traces）。
- **指令集架构（ISA）**：定义低级代码的指令规则，如RISC-V 32位（RISC Zero、SP1常用）。
- **编译器**：将高级语言（如Rust）编译为遵循ISA的低级代码。zkVM常修改现有Rust工具链，添加支持证明的系统调用（syscalls/ecalls）。
- **证明器（Prover）**：将执行追踪转为多项式约束，生成证明（STARK或SNARK）。
- **验证器（Verifier）**：通常部署在链上（如Ethereum），验证证明的正确性。

## 4. zkVM的编译与证明流程
1. **输入**：用高级语言（如Rust）编写程序逻辑。
2. **编译**：编译器将代码转为低级机器码（遵循ISA，如RISC-V）。
3. **执行**：VM运行机器码，生成执行追踪。
4. **算术化（Arithmetization）**：将追踪转为多项式约束（将计算问题转为代数问题）。
5. **证明生成**：
   - 使用交互式预言机证明（IOP）：证明者与验证者交互，验证多项式评估点。
   - 多项式承诺方案（PCS）：证明者承诺多项式，绑定且隐藏（例：KZG、Pedersen）。
   - 生成STARK证明，必要时转为SNARK证明。
6. **验证**：验证者用公共参数验证证明。

**示例流程（斐波那契序列）**：
```rust
// Rust代码：斐波那契逻辑
fn fib(n: u32) -> u32 {
    if n <= 1 { return n; }
    fib(n - 1) + fib(n - 2)
}
```
- 编译为RISC-V机器码 → VM执行生成追踪 → 约束为多项式 → 生成STARK证明 → 可选转为SNARK → 验证。

## 5. zkVM vs. zkEVM
- **zkVM**：通用零知识虚拟机，适用于任何计算逻辑。
- **zkEVM**：专为Ethereum优化，运行智能合约，兼容EVM。用于批量交易（rollup），オフ链执行，生成证明，上链验证，提升可扩展性。
- **区别**：zkEVM专注Ethereum扩展，zkVM更通用。

## 6. RISC Zero的具体实现
- **主机（Host）与客人（Guest）**：
  - **Host**：控制程序，初始化环境，运行证明。
  - **Guest**：开发者写的逻辑代码，编译为ELF二进制（基于RISC-V）。
- **执行流程**：
  1. Guest代码编译为ELF。
  2. 执行器（Executor，VM实例）运行ELF，生成追踪。
  3. 会话（Session）：追踪转为表格，应用IOP/PCS，生成收据（Receipt）。
  4. 收据包含：
     - **Journal**：程序的公共输出。
     - **Seal**：STARK证明（可转为SNARK）。
- **输入输出**：Guest读输入流，执行，写输出（私有或公共，通过`env::commit`）。

**示例（RISC Zero Guest代码）**：
```rust
// Guest代码：简单加法
use risc0_zkvm::guest::env;

fn main() {
    // 读输入
    let a: u32 = env::read();
    let b: u32 = env::read();
    // 计算
    let sum = a + b;
    // 写公共输出
    env::commit(&sum);
}
```
- 输出Journal包含`sum`，Seal包含证明，链上验证。

## 7. 入门建议
- **学习Rust**：zkVM多用Rust（如RISC Zero、SP1）。
- **尝试工具**：安装RISC Zero SDK，写简单程序（如加法、斐波那契）。
- **理解概念**：ISA、算术化、IOP/PCS。
- **实践**：用RISC Zero或SP1生成证明，部署到Ethereum测试验证。
- **资源**：查看RISC Zero文档、zkForge Bootcamp视频。

## 8. 常见问题
- **为什么用zkVM？** 简化ZKP开发，复用现有工具，无需手动写电路。
- **SNARKs还是STARKs？** zkVM多用STARKs（易生成），转SNARKs（链上高效）。
- **如何审计？** 高级语言代码易读，降低审计难度。

## 总结
zkVM是ZKP开发的革命性工具，通过抽象底层密码学，让开发者用熟悉的高级语言（如Rust）编写逻辑，自动生成证明。核心是编译、执行、算术化、证明生成流程。RISC Zero等实现让开发更高效，适合区块链扩展（如zkEVM）和其他通用场景。下一步：动手实践，写一个简单的Rust程序，生成并验证证明！
````
<!-- DAILY_CHECKIN_2025-08-23_END -->

# 2025-08-22

# BlockSec Web3安全课程第二课学习笔记

## 课程概览
- **时间**: 2025年8月22日
- **主题**: 智能合约安全实战（第二课）
- **讲师**: 周老师
- **主持人**: 七客（灯店社区）
- **内容**: 本课深入探讨智能合约的创建、调用、代理机制及EVM内存模型，结合四个真实攻击案例（Fomo3D、Parity Wallet、Wormhole、Tornado Cash DAO），分析安全隐患及防范措施。

---

## 一、智能合约基础与部署
### 1. 智能合约账户
- **定义**: 智能合约是包含可执行代码的区块链账户，与外部账户（EOA）不同。
- **功能**:
  - 可持有原生代币（如ETH）及ERC20代币。
  - 可与其他账户（EOA或智能合约）交互。
- **交易类型**:
  - **外部交易**: EOA与智能合约交互。
  - **内部交易**: 智能合约与智能合约交互。

### 2. 部署智能合约
- **部署方式**: 通过发送交易实现，关键字段：
  - **from**: 部署者地址（EOA或智能合约）。
  - **to**: 为空（标志部署新合约）。
  - **value**: 可选，传递原生代币。
  - **data**: 包含**deploy code**（运行时代码 + 初始化代码，如构造函数）。
- **构造函数**:
  - 一次性执行，用于初始化状态。
  - 不存储于链上，节省存储空间。

### 3. 地址生成
- **CREATE**:
  - 地址公式: `address = hash(sender, nonce)`。
  - 特点: 确定性（可预计算），但nonce递增，无法重用地址。
- **CREATE2**:
  - 地址公式: `address = hash(0xFF, sender, salt, bytecode)`。
  - 特点: 与nonce无关，可通过salt重用地址。
- **安全隐患**:
  - CREATE2结合自销毁（selfdestruct）可重部署不同逻辑到同一地址，易引发欺骗性攻击。

### 4. 验证（Verify）
- **目的**: 将字节码（bytecode）与源代码对应，公开透明。
- **工具**: Foundry的`forge create`简化部署，Etherscan验证代码。
- **隐患**: 攻击者可伪造验证，显示安全源代码，实际部署恶意字节码。

---

## 二、EVM与内存模型
### 1. EVM简介
- **定义**: 以太坊虚拟机（EVM）是基于栈的解释型虚拟机，执行智能合约代码。
- **指令结构**: 由操作码（opcode，如ADD、JUMP）+ 操作数组成，操作数存储在栈上。
- **示例**:
  ```solidity
  // ADD指令
  PUSH 98  // 压入98
  PUSH 12  // 压入12
  ADD      // 弹出98和12，压入和（110）
  ```

### 2. 内存模型
- **Storage（持久化存储）**:
  - 结构: 键值对（KV），键空间2^256，值固定32字节。
  - 操作: `sstore`写入，`sload`读取。
  - 示例: uint256变量存储在slot 0、1、2；可打包小变量到同一slot。
- **Memory（临时存储）**:
  - 用途: 存储栈、参数、局部变量，执行后清空。
  - 操作: `mstore`写入，`mload`读取。
  - 约定: 偏移量0x40存储“下一个可用Memory地址”，0x00-0x3F为保留区（Scratch Space）。
  - 示例:
    ```solidity
    mload(0x40)     // 读取当前可用Memory地址
    mstore(0x40, add(mload(0x40), 128))  // 更新指针
    ```

### 3. 安全分析
- **审计需求**: 无源代码时，需分析字节码，理解EVM指令（如opcode、stack、slot）。
- **工具**: EVM Code Playground，调试字节码执行。

---

## 三、调用机制
### 1. 函数调用
- **交易字段**:
  - **from**: 调用者地址。
  - **to**: 目标合约地址。
  - **value**: 传递的原生代币（msg.value）。
  - **data**: 函数选择器（4字节）+ 参数。
- **示例**:
  ```solidity
  // 调用deployCreator(uint256)
  data: 0x<function_selector><encoded_salt>
  ```
- **工具**: Foundry/Web3.py/Web3.js自动编码data。

### 2. Call vs Delegatecall
- **Call**:
  - 执行目标合约代码，修改目标上下文（Storage、Memory）。
  - 上下文隔离，A调用B，修改B的Storage。
- **Delegatecall**:
  - 执行目标代码，修改调用者上下文。
  - 示例: A Delegatecall B，执行B代码，修改A的Storage。
  - 用途: 代理（Proxy）机制，借用逻辑代码。
- **代码示例**:
  ```solidity
  // Call
  B.functionCall();
  // Delegatecall
  (bool success, ) = address(B).delegatecall(abi.encodeWithSignature("functionCall()"));
  ```

### 3. Proxy机制
- **结构**:
  - Proxy合约（固定地址）通过Delegatecall调用实现合约（Implementation）。
  - 用户交互Proxy，逻辑在Implementation执行，Storage存储在Proxy。
- **优势**:
  - 可升级: 更新Implementation（B→C），Proxy地址不变。
- **隐患**:
  - 升级可能引入恶意逻辑，隐藏在不变的Proxy地址下。
  - 挑战immutable原则，需监控升级事件。

### 4. Low-Level Call vs Contract Call
- **Contract Call**:
  - 高层调用，如`Contract.function()`。
  - 自动传播异常（revert）。
- **Low-Level Call**:
  - 底层调用，如`address.call()`。
  - 返回`(success, data)`，不自动revert。
  - 示例:
    ```solidity
    function safeTransferFrom(address token, ...) {
        (bool success, ) = token.call(abi.encodeWithSignature("transferFrom(...)"));
        // 未检查success，零地址调用success=true
    }
    ```
  - **隐患**: 未检查success，可能假成功（如Q Bridge漏洞，损失数百万美元）。

---

## 四、攻击案例
### 1. Fomo3D（2018）
- **机制**: 博彩游戏，投注ETH获随机奖励（airdrop）。
- **漏洞**: 随机数基于blockhash/timestamp，可通过CREATE地址预计算，稳赚投注。
- **代码**:
  ```solidity
  function lJob() internal returns (uint256) {
      // 伪随机数生成，易被预计算
      return uint256(keccak256(abi.encode(blockhash(block.number-1), timestamp)));
  }
  ```
- **启示**: 使用Chainlink VRF确保随机数安全。

### 2. Parity Wallet（多签钱包）
- **漏洞**: Delegatecall初始化库合约，initWallet函数未限制，攻击者修改owner并自毁。
- **代码**:
  ```solidity
  function initWallet(address[] owners) public {
      // Delegatecall修改调用者Storage
  }
  ```
- **启示**: 限制初始化函数（如onlyNotInitialized），损失数亿美元。

### 3. Wormhole（跨链桥）
- **漏洞**: CREATE2部署合约可自毁，重部署不同逻辑到同一地址。
- **防范**: 安全研究人员提前发现，需监控自毁/重部署。
- **启示**: 桥需验证地址一致性。

### 4. Tornado Cash DAO
- **漏洞**: 提案合约用CREATE2创建，可自毁并重部署恶意代码，绕过投票审查。
- **过程**:
  ```solidity
  // A通过CREATE2创建B，B通过CREATE创建P
  // 自毁B和P，重部署新B和新P（地址相同，代码恶意）
  function deployCreator(uint256 salt) public {
      address B = create2(salt, bytecode);
      B.deployTarget(); // 创建P
  }
  ```
- **启示**: DAO需警惕CREATE2提案，监控地址重用。

---

## 五、Q&A与关键问题
1. **Bytecode vs Deploy Code**:
   - Bytecode: 通用字节码。
   - Deploy Code: 包含Runtime Code + 构造函数。
2. **检测Bytecode变化**:
   - 监控CREATE/CREATE2交易，自行编写工具。
3. **CREATE2创建C为何不可**:
   - 地址与bytecode绑定，改代码改地址，无法重用。
4. **Low-Level Call不revert**:
   - 返回success，需手动`require(success)`。
   - 零地址调用success=true，需白名单检查。
5. **Scratch Space**:
   - Memory 0x00-0x3F为EVM保留区，存储临时变量，用户从0x80开始。

---

## 六、安全启示
- **地址重用**: CREATE2+自销毁需监控，防止恶意逻辑替换。
- **Delegatecall/Proxy**: 限制初始化函数，审计升级逻辑。
- **Low-Level Call**: 优先contract call，low-level call必须检查success。
- **审计**: 熟悉EVM指令（opcode、slot），使用Foundry/EVM Playground。
- **随机数**: 避免可预测算法，采用VRF。
- **DAO治理**: 审查CREATE2提案，防范时间锁绕过。

---

## 七、课后资源
- **EVM Code Playground**: 调试字节码。
- **Foundry**: 部署/验证工具。
- **PPT链接**: 课后查看Falcon示例，分析攻击细节。
- **社区**: 微信群交流问题。

# 2025-08-21

# 许可资本市场的智能合约协议漏洞学习笔记

## 学习目标
通过学习《许可资本市场的智能合约协议漏洞》（Vulnerabilities In Permissioned Capital Market Smart Contract Protocols），了解传统金融（TradFi）许可资本市场的智能合约协议（PCM）与去中心化金融（DeFi）协议的区别，掌握其独特的漏洞类型及缓解措施。

## 目录
1. TradFi PCM 与 DeFi 的对比
2. 许可参与
3. 资本要求
4. 监管合规性
5. TradFi PCM 漏洞分析
6. 跨链 PCM 漏洞
7. TradFi PCM 气体优化
8. 总结

## 1. TradFi PCM 与 DeFi 的对比
- **核心差异**：TradFi PCM 与 DeFi 在以下三个方面存在显著差异：
  - **许可参与**：DeFi 通常是无许可的，任何人都可以创建钱包并自由交互；而 PCM 限制为经过 KYC/AML 验证的合格投资者。
  - **资本要求**：DeFi 通常无高资本门槛，PCM 则要求高资本量，交易最低金额通常在数千至数百万美元。
  - **监管合规性**：DeFi 类似“野蛮西部”，缺乏监管；PCM 在高度监管环境中运行，需满足链上和链下的合规要求。

## 2. 许可参与
- **DeFi 特点**：
  - 无需许可，任何人都可参与，资产具有完全主权（无法被冻结或没收）。
  - 开放性导致高风险，2022 年 DeFi 总锁仓价值（TVL）达 1370 亿美元，但被黑客窃取 37 亿美元（占 2.7%）。
- **PCM 特点**：
  - 仅限预批准的参与者，需通过 KYC/AML 和合格投资者验证。
  - 管理员可冻结或没收资产，增强了应对黑客攻击的能力。
  - 减少了无许可攻击的风险，正确配置的 PCM 协议可阻止未经授权的状态更改。
- **伪 DeFi**：
  - 许多标榜“去中心化”的协议实际上是中心化的（如使用集中式排序器或非分布式验证者集）。
  - PCM 与伪 DeFi 的区别在于 PCM 更坦诚地披露管理员权限，而伪 DeFi 在紧急情况下也会行使类似权限。

## 3. 资本要求
- DeFi：通常无资本要求或要求较低，普通人可参与。
- PCM：高资本门槛，限制了大部分人的参与，交易金额通常较大。

## 4. 监管合规性
- **DeFi**：无明确法律实体或监管框架，创新与欺诈并存。
- **PCM**：
  - 在监管环境中运行，需满足监管和业务合规要求。
  - 通过链上和链下基础设施跟踪用户和产品数据，确保合规。
  - 用户与已知、受监管的实体交互，享有法律追索权，可向监管机构申诉。

## 5. TradFi PCM 漏洞分析
通过 Cyfrin 的私密审计，发现以下 PCM 漏洞类别：

### 5.1 跟踪数据损坏破坏合规规则
- **机制**：
  - PCM 协议包含跟踪钩子（记录用户和产品数据）和合规钩子（在操作前后检查数据）。
  - 交易可能因合规检查失败而被回滚或受到限制。
- **漏洞类型**：
  - 跟踪数据参数编码/解码不匹配。
  - 缺少跟踪钩子，导致数据点未更新。
  - 堆叠跟踪钩子，导致同一数据点被多次更新。
  - 缺少合规钩子，导致部分交易未强制执行合规规则。
  - 合规钩子逻辑错误，复杂性较高。
  - 传递错误参数导致数据点损坏。
  - 边缘情况：无法禁用或移除钩子，或钩子因依赖的跟踪钩子被禁用而失效。
- **影响**：
  - **拒绝服务**：核心功能因数据溢出/下溢而回滚，协议几乎不可用，但可通过修复数据恢复。
  - **隐性错误**：合规规则看似正常运行，但因输入数据错误导致违规，管理员可能未察觉。

### 5.2 管理员操作导致状态不一致
- **场景**：复杂的管理员功能用于强制执行合规规则，但常因不常用且需处理多种状态而引入漏洞。
- **示例**：
  - 强制赎回资产导致变量值损坏，破坏关键不变量，引发后续结算失败。
  - 强制取消订单导致跟踪数据点损坏，影响合规检查。
  - 撤销用户凭证阻止用户获取新凭证。
  - 更新凭证架构导致旧凭证无法撤销。
- **影响**：协议状态不一致，可能导致功能失效或合规违规。

### 5.3 权限配置错误和权限提升
- **问题**：
  - 缺少访问控制，公开可调用的状态更改函数未受限。
  - 继承的标准合约（如 ERC-20/721/4626）中的公开函数未受限。
  - 权限提升：用户可通过漏洞获得未经授权的权限。
  - 基金经理可操作未授权的基金。
  - 仅限 KYC 地址持有的代币可转移至非 KYC 地址。
  - 用户可将交易输出定向到非 KYC 地址。
  - 移除跟踪钩子时未撤销相关权限。
  - 缺少快速响应的管理员功能以应对私钥泄露。
- **影响**：可能导致严重的安全漏洞，如合约地址更改或资产转移。

### 5.4 前置交易（Front-running）
- PCM 的受限性质降低了前置交易攻击的影响。
- **发现**：用户可通过前置交易规避管理员操作（如强制赎回），将代币转移到其他地址。

### 5.5 输入验证、舍入、滑点和精度损失
- 与 DeFi 类似，PCM 涉及复杂的财务计算，需注意以下问题：
  - 缺少或不足的输入验证，允许代表他人进行交易。
  - 除法优先于乘法导致精度损失或资金归零。
  - 缺少滑点参数，动态汇率导致输出代币少于预期。
  - 精度缩放不匹配，导致金库用户或协议的重大财务损失。
  - 流动性检查因精度不匹配而错误阻止赎回。

## 6. 跨链 PCM 漏洞
- **场景**：PCM 协议支持跨链操作，桥接用户凭证和代币化 RWA。
- **漏洞**：
  - 目标链上缺少来源验证，攻击者可伪造恶意消息触发代币铸造。
  - 目标链上交易可被多次执行。
  - 跨链用户凭证同步中断。
  - 目标链交付失败导致源链代币锁定。
  - 区块重组处理错误，导致用户未放弃源链代币却在目标链收到代币。
  - 外部链上相同地址与源链多个投资者关联。
  - 地址格式验证错误导致桥接到其他链失败。

## 7. TradFi PCM 气体优化
- PCM 协议因合规要求和防御性代码而“重”，需优化气体消耗：
  - 缓存相同存储读取，仅读取一次。
  - 缓存常用变量，传递给子函数。
  - 缓存存储写入，延迟到交易结束。
  - 避免迭代整个列表，防止气体耗尽。
  - 使用命名返回变量优化内存变量。
  - 优先使用 calldata 而非 memory。
  - 快速失败，最小化无关代码执行。
  - 非可升级合约的存储变量在构造函数中设为不可变。
  - 不初始化默认值。
  - 启用优化器。
  - 仅复制所需结构体字段。
  - 高效打包存储槽。

## 8. 总结
- **核心洞察**：
  - PCM 协议因监管和合规要求引入了独特的漏洞，如跟踪数据损坏和权限配置错误。
  - 尽管 PCM 审计报告通常不公开，但其漏洞类型为区块链安全研究提供了宝贵经验。
- **启示**：
  - 理解 PCM 和 DeFi 的共同点和差异有助于提升智能合约安全性。
  - 开发者和安全研究人员需关注合规钩子、权限控制和跨链操作的潜在风险。
- **建议**：
  - 开发 TradFi PCM 或 DeFi 协议时，应与专业审计机构（如 Cyfrin）合作，增强安全性。

# 2025-08-20

# 捐赠攻击学习笔记

## 目录
- 捐赠攻击的危害：为什么需要关注？
- ERC-4626 股份膨胀：经典的捐赠攻击
- 易受攻击的保险库示例
- 结论

## 捐赠攻击的危害：为什么需要关注？
在去中心化金融（DeFi）和智能合约领域，看似无害的行为可能被恶意利用。**捐赠攻击（Donation Attacks）**通过利用智能合约对代币余额的错误假设来实施攻击，通常源于对外部因素的错误依赖。

### 核心问题
攻击者通过直接向智能合约发送代币（而不是通过协议的接口）来操纵合约的状态。这种“捐赠”行为可能严重干扰合约的逻辑，导致资产分配不公，损害合法用户的利益。如果协议仅依赖代币余额（`balanceOf`）进行记账，而不考虑此类直接转账，攻击者就能通过操纵状态造成破坏。

### Solodit 检查清单项：SOL-AM-DA-1
> **问题**：协议是否依赖 `balance` 或 `balanceOf` 而不是内部记账？  
> **描述**：攻击者通过“捐赠”代币来操纵记账。  
> **修复方法**：实现内部记账，而不是直接依赖 `balanceOf`。

此检查项旨在通过确保协议不依赖外部函数（如 `balanceOf` 或 `balance`）进行记账，防止捐赠攻击。内部记账使用专用的状态变量来跟踪和管理余额，而不是依赖外部数据。

### 为什么危险？
任何人可以直接向合约发送代币，而不受合约逻辑的限制。如果合约使用 `token.balanceOf(address(this))` 来计算股份或取款金额，攻击者通过“捐赠”代币即可破坏系统，改变预期结果。

## ERC-4626 股份膨胀：经典的捐赠攻击
### 什么是 ERC-4626？
ERC-4626 是一种用于**代币化保险库（Tokenized Vaults）**的标准接口。保险库是一个智能合约，用户可存入特定底层资产（如 USDC 或 WETH），并获得代表其在保险库中资产份额的“股份”。这些股份通常会随着保险库的收益（例如通过借贷或挖矿策略）而增值。

### 漏洞：捐赠与 ERC-4626 的碰撞
许多早期的 ERC-4626 实现根据保险库持有的底层资产总量（通过 `asset.balanceOf(address(this))` 确定）来计算存款时应铸造的股份数量。其简化公式如下：

```
shares_to_mint = deposit_amount * total_shares / total_assets_in_vault
```

这里的 `total_assets_in_vault` 通常由 `asset.balanceOf(address(this))` 计算，而这正是捐赠攻击的切入点。

### 股份膨胀攻击（Share Inflation Attack）
攻击通常针对新部署的 ERC-4626 保险库，尤其是在没有任何合法用户存款之前。攻击者通过操纵股份价格计算来窃取用户资金。以下是一个攻击示例：

1. **初始状态**：保险库为空，无股份，无 WETH。
2. **攻击者首次存款**：攻击者通过存款函数存入 1 WEI（WETH）。由于保险库为空，设计上 1 WEI 存款铸造 1 股份。此时，股份价格为 1 WEI/股份。
3. **操纵行为**：攻击者直接向保险库发送 1 WETH（1e18 WEI）。现在保险库持有约 1 WETH（1e18+1 WEI），但流通股份仍为 1 WEI。此时股份价格接近 1e18 WEI/股份（即“股份膨胀”）。
4. **受害者损失**：普通用户尝试存入 0.5 WETH。保险库根据公式计算股份：`(0.5e18 * 1) / (1e18 + 1)`，结果四舍五入为 0 股份。用户存入 0.5 WETH 却未获得任何股份！
5. **攻击者获利**：攻击者提取其 1 WEI 股份，拿走保险库中的全部资产（1.5 WETH）。

### 为什么是捐赠攻击？
攻击者通过直接向合约发送资产（不增加股份总量）来操纵股份价格，造成数学陷阱，使合法用户的存款无法获得股份。

### 缓解措施
核心问题在于处理首次存款和防止 `totalSupply` 为零或极小时的操纵。以下是一些解决方案：
- **最低初始存款**：要求首次存款达到一定规模，但这会影响用户体验，且无法完全解决问题。
- **内部余额跟踪**：完全忽略 `asset.balanceOf`，通过内部变量精确跟踪存款和取款。这是理想方案，但会增加复杂性和 Gas 成本。
- **虚拟股份/偏移**：通过假定保险库一开始就持有一定数量的资产和股份，锚定股份价格计算，防止初始状态被操纵。OpenZeppelin 的 ERC-4626 实现现已采用此方法。

## 易受攻击的保险库示例
以下是一个简化的易受攻击的代币保险库代码：

<xaiArtifact artifact_id="e8d44561-66f4-4b89-b721-9130ca3119f6" artifact_version_id="431a5c98-e8b5-4e02-bfe6-b082a26b8462" title="Vulnerable_TokenVault.sol" contentType="text/x-solidity">

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

contract TokenVault {
    IERC20 public token;
    mapping(address => uint256) public shares;
    uint256 public totalSupply;

    constructor(IERC20 _token) {
        token = _token;
    }

    function deposit(uint256 _amount) external {
        uint256 tokenBalance = token.balanceOf(address(this));
        uint256 shareAmount = 0;

        if (totalSupply == 0) {
            shareAmount = _amount;
        } else {
            shareAmount = _amount * totalSupply / tokenBalance;
        }

        shares[msg.sender] = shares[msg.sender] + shareAmount;
        totalSupply = totalSupply + shareAmount;
        token.transferFrom(msg.sender, address(this), _amount);
    }

    function withdraw(uint256 _amount) external {
        uint256 shareAmount = _amount;
        require(shares[msg.sender] >= shareAmount, "Insufficient shares");

        uint256 tokenBalance = token.balanceOf(address(this));
        uint256 amountToWithdraw = shareAmount * tokenBalance / totalSupply;

        shares[msg.sender] = shares[msg.sender] - shareAmount;
        totalSupply = totalSupply - shareAmount;
        token.transfer(msg.sender, amountToWithdraw);
    }
}

# 2025-08-19

# ERC-4626 通胀攻击学习笔记

## 引言
ERC-4626 是一个 ERC-20 兼容的代币化金库（Tokenized Vaults）标准，用于管理资产存款和赎回。该标准中存在一种称为“通胀攻击”（Inflation Attack）的漏洞，攻击者可以通过操纵金库的初始存款来窃取后续存款者的资产。本笔记基于学习内容，总结攻击原理、OpenZeppelin 实现的缓解措施，以及防护建议。

## 通胀攻击概述

### 基本概念
- **金库（Vault）**：ERC-4626 金库允许用户存入资产（如 USDC）以换取金库代币（Vault Tokens），并可通过销毁代币赎回资产。
- **通胀攻击**：攻击者通过在金库初始部署时存入极少量资产（如 1 wei USDC），并通过直接转账增加金库余额，操控存款公式中的精度损失，导致后续存款者获得的代币数量减少甚至为零，进而使攻击者获利。

### 攻击原理
1. **存款公式**：
   - 标准 ERC-4626 存款公式为：
     ```
     shares = (assets * totalSupply) / totalAssets
     ```
     其中：
     - `assets`：用户存入的资产数量
     - `totalSupply`：金库代币的总供应量
     - `totalAssets`：金库中的总资产余额
   - 如果 `totalSupply` 为 0（即金库刚部署，首次存款），`shares = assets`，即存入多少资产就获得等量的金库代币（1:1 比例）。

2. **攻击步骤**：
   - **步骤 1：攻击者抢先存款**  
     攻击者通过观察内存池（MEM pool），以较高的 gas 费用抢先在目标存款者之前存入极小金额（如 1 wei USDC）。这使得 `totalSupply = 1`（假设金库代币和资产代币小数位相同，例如 USDC 和金库代币均为 6 位小数）。
   - **步骤 2：直接转账资产**  
     攻击者通过直接转账（如 `transfer`）向金库发送大量资产（如 10 USDC），增加 `totalAssets`（例如变为 10 + 1 wei），但不触发存款函数，因此 `totalSupply` 保持不变。
   - **步骤 3：后续存款者受害**  
     假设后续用户存入 10 USDC，代币计算为：
     ```
     shares = (10 * 10^6 * 1) / (10 * 10^6 + 1) ≈ 0（向下取整）
     ```
     由于精度损失，用户获得 0 个金库代币，但 10 USDC 已存入金库。
   - **步骤 4：攻击者获利**  
     攻击者持有唯一的金库代币（1 wei），可赎回金库中几乎所有资产（包括后续存款者的 10 USDC），从而获利。

3. **关键问题**：
   - 初始 `totalSupply` 极小（如 1 wei）且 `totalAssets` 被人为放大，导致存款公式中的除法运算产生严重的精度损失。
   - 后续存款者因获得 0 代币而无法赎回资产，资金被攻击者“吸收”。

## OpenZeppelin 的缓解措施
OpenZeppelin 的 ERC-4626 实现通过以下两种机制降低通胀攻击的风险：

### 1. 虚拟资产与虚拟代币（Virtual Assets and Shares）
- **机制**：在存款公式中加入虚拟资产和虚拟代币的偏移量：
  ```
  shares = (assets * (totalSupply + 1)) / (totalAssets + 1)
  ```
  - 在 `totalSupply` 和 `totalAssets` 上各加 1，模拟金库中始终存在 1 wei 的虚拟代币和虚拟资产。
  - 即使攻击者存入 1 wei 并额外转账 10 USDC，公式变为：
    ```
    shares = (10 * 10^6 * (1 + 1)) / (10 * 10^6 + 1 + 1) ≈ 1
    ```
    用户将获得约 1 个代币（而非 0），避免完全损失。
- **效果**：
  - 攻击者仍可使后续存款者获得较少的代币，但无法完全剥夺其代币。
  - 攻击者赎回时只能获得部分资产（例如 50%），而非全部，导致攻击无利可图。

### 2. 小数位偏移（Decimal Offset）
- **机制**：金库代币的小数位（decimals）可以设置为比底层资产代币更高。例如，若 USDC 为 6 位小数，金库代币可设置为 7 位小数，公式调整为：
  ```
  shares = (assets * (totalSupply + 10^decimalOffset)) / (totalAssets + 1)
  ```
  - 默认 `decimalOffset = 0`，但开发者可通过构造函数设置更高值（如 1）。
  - 若 `decimalOffset = 1`，攻击者需转入更多资产（例如 200 USDC 而非 20 USDC）才能造成显著的精度损失。
- **效果**：
  - 增加攻击成本，使攻击者在造成用户损失时自己也需承担更大的资金投入，降低攻击动机。
  - 当 `decimalOffset` 较大时，攻击成本可能远超潜在收益，攻击变得几乎不可行。

## 实际案例分析
假设金库初始状态为空，攻击者与用户操作如下：
1. **攻击者**：
   - 存款 1 wei USDC，获得 1 wei 金库代币（`totalSupply = 1`）。
   - 直接转账 20 USDC 至金库（`totalAssets = 20 * 10^6 + 1`）。
2. **用户**：
   - 存入 10 USDC，使用 OpenZeppelin 公式：
     ```
     shares = (10 * 10^6 * (1 + 1)) / (20 * 10^6 + 1 + 1) ≈ 0.5
     ```
     用户获得约 0.5 个金库代币。
3. **赎回**：
   - 金库总资产为 30 USDC（1 wei + 20 USDC + 10 USDC）。
   - 攻击者持有 1 wei 代币，赎回约 50% 资产（15 USDC）。
   - 用户持有 0.5 代币，赎回约 25% 资产（7.5 USDC）。
   - 剩余资产归虚拟代币（1 wei），无人可赎回。

**结论**：攻击者需投入 20 USDC 才能使用户损失部分价值，但自身仅回收 15 USDC，攻击无利可图。

## 防护建议
1. **使用 OpenZeppelin 的 ERC-4626 实现**：
   - 默认包含虚拟资产和代币机制，显著降低攻击可行性。
   - 开发者无需额外修改即可获得基础防护。

2. **设置小数位偏移**：
   - 通过构造函数设置 `decimalOffset > 0`（例如 1 或更高），增加攻击者所需投入的资金成本。
   - 例如，若 `decimalOffset = 1`，攻击者需投入数百 USDC 才能造成显著精度损失，攻击成本远超收益。

3. **监控与限制初始存款**：
   - 在金库部署初期，设置最低存款金额，防止攻击者以极小金额（如 1 wei）抢占首次存款。
   - 可通过前端运行检测异常存款行为。

4. **审计与测试**：
   - 对金库合约进行全面审计，模拟通胀攻击场景，确保实现无漏洞。
   - 在测试环境中验证不同存款金额下的精度损失情况。

## 总结
通胀攻击利用了 ERC-4626 存款公式中的精度损失漏洞，通过抢先存款和直接转账放大金库余额，使后续存款者获得极少或零代币。OpenZeppelin 的实现通过虚拟资产/代币和小数位偏移机制，使攻击变得无利可图。开发者应优先使用 OpenZeppelin 的标准实现，并考虑增加 `decimalOffset` 或设置最低存款金额，以进一步提高金库安全性。

# 2025-08-15

# Resupply 协议攻击事件总结

## 1. 背景
根据 BlockSec 博客（2025年7月6日发布，分析2025年6月26日 Resupply 稳定币协议攻击事件），Resupply 是一个在 Curve 生态系统中运行的去中心化稳定币协议，发行名为 reUSD 的稳定币，采用抵押债务头寸（CDPs）机制。用户可以通过存入 crvUSD 或 frxUSD 等其他稳定币（在外部借贷市场中赚取利息）来借入 reUSD，实现资产的再融资。

### 1.1 Resupply 协议概况
- **核心功能**：用户在链上部署的 Resupply Market 中进行借贷操作。Market 由 DAO 管理，每个 Market 指定一个 ERC-4626 Vault 作为抵押品，底层资产为 Vault 对应的资产（例如 crvUSD）。
- **示例**：攻击涉及的 Market `0x6e90` 和 Vault `0x0114`：
  - **Market `0x6e90`**：
    - 底层资产：crvUSD
    - 抵押品：cvcrvUSD（Vault `0x0114` 发行的 ERC-4626 代币）
    - 借入资产：reUSD
  - **Vault `0x0114`**：
    - 资产：crvUSD（存储在 Curve LlamaLend Controller 中）
    - 抵押品：wstUSR
    - 借入资产：crvUSD
    - 股份：cvcrvUSD
- **操作流程**：用户存入 cvcrvUSD（或 crvUSD，实际会通过 Vault 转换为 cvcrvUSD）到 Market 以借入 reUSD。

### 1.2 借贷资格判定机制
- Resupply Market 通过 `isSolvent` 修饰符对用户头寸进行资产健康检查，调用 `_isSolvent` 函数，检查贷款价值比（LTV，Loan-to-Value ratio），要求借入资产与抵押资产的比率不超过系统设定的最大值（`ltv <= maxLTV`）。
- LTV 的计算依赖于 `_exchangeRate`，即借入资产（reUSD）相对于抵押资产（cvcrvUSD）的价格（汇率）。

## 2. 攻击分析
### 2.1 根本原因
攻击的核心原因是 Resupply Market 的价格预言机（Price Oracle）实现存在漏洞。在新创建的低流动性 Market 中，攻击者可以通过“捐赠攻击”（Donation Attack）操纵汇率，导致 `_exchangeRate = 0`，从而绕过健康检查，借入大量 reUSD 以获利。

#### 汇率计算
- 汇率公式：
  ```
  _exchangeRate = 1e36 / getPrices()
  ```
  如果 `getPrices()` 返回的价格大于 `1e36`，由于整数除法的向下取整，`_exchangeRate` 将变为 0。

#### 价格操纵
- 价格计算公式：
  ```
  price = (total_assets * shares * 1e18) / ((totalSupply + DEAD_SHARES) * precision)
  ```
  其中：
  - `precision = 1`
  - `DEAD_SHARES = 1000`
  - `shares = 1e18`
  最终公式为：
  ```
  price = (total_assets * 1e18) / (totalSupply + 1000)
  ```
- **攻击关键**：通过增加 `total_assets`（底层资产 crvUSD）并保持 `totalSupply`（股份 cvcrvUSD）极小，放大 `price` 值。
  - `total_assets` 取决于 Market 中的 crvUSD 总量。
  - `totalSupply` 取决于 Market 的整体流动性（cvcrvUSD 股份）。
  - 这是一个典型的捐赠攻击场景：攻击者向协议捐赠大量资产以操纵价格。

### 2.2 攻击交易分析
攻击交易的核心步骤如下：
1. **闪电贷**：攻击者通过闪电贷借入 4000 USDC，并兑换为 3999 crvUSD。
2. **捐赠 crvUSD**：向 Controller `0x8970` 捐赠 2000 crvUSD。捐赠前 Controller 持有 0 crvUSD，捐赠后记录为 2000（18位小数）。
3. **存入 Vault**：向 Vault `0x0114` 存入约 2 crvUSD，获得 1 单位 cvcrvUSD。此时记录的 crvUSD 总量为 2002（18位小数）。
4. **后续操作**：（文档中此处内容被截断，但推测攻击者利用操纵后的价格借入大量 reUSD，最终获利约 1000 万美元）。

## 3. 经验教训
- **预言机安全**：低流动性市场容易受到价格操纵攻击，必须改进预言机设计，避免依赖单一或易受操纵的数据源。
- **捐赠攻击防范**：协议应设置机制（如限制捐赠或检查异常资产流入）以防止攻击者通过捐赠操纵资产池。
- **实时监控**：BlockSec 强调其 Phalcon 平台能够通过实时监控和毫秒级响应，检测并阻止类似攻击。文章提到 Phalcon 保护了超 500 亿美元资产，成功阻止了 20 多次真实攻击，挽回超 2000 万美元损失。

## 4. 生态关系与争议
- **Curve 生态复杂性**：Resupply 属于 Curve 生态，与其他项目（如 crvUSD、frxUSD）有深层联系。攻击引发了社区对项目方及其利益相关者的激烈争议。
- **社区影响**：攻击后，Resupply 官方公告缺乏技术细节，引发社区不满。BlockSec 指出，项目方应通过透明的监控数据和顶级安全解决方案（如 Phalcon）重建社区信任。

## 5. 进一步反思
- **时间线**：BlockSec 在攻击发生后率先发布预警（通过 X 平台的帖子），提供了初步分析，显示其快速响应能力。
- **Phalcon 的作用**：如果 Resupply 部署了 Phalcon 的实时监控，可能提前检测到异常交易，阻止攻击。
- **安全必要性**：文章强调 DeFi 安全是生存的关键，建议项目方立即部署顶级安全保护，并与 BlockSec 等行业领导者合作进行全面安全评估。

## 6. 总结
Resupply 攻击事件暴露了 DeFi 协议在低流动性市场和预言机设计中的常见漏洞。攻击者利用捐赠攻击操纵价格，绕过健康检查，造成约 1000 万美元的损失。本次事件提醒开发者：
- 加强预言机设计，采用多源或抗操纵的机制。
- 实施实时监控和异常检测。
- 提高透明度，及时披露技术细节以维护社区信任。

🔗 **参考链接**：
- BlockSec Phalcon 安全应用：https://blocksec.com/phalcon/security
- 预约演示：https://blocksec.com/book-demo

# 2025-08-14

# Web3 审计课程总结：重要漏洞发现

以下是对Web3审计课程中讨论的三个关键漏洞（Incorrect Fee Structure、DoS Dividends、Swap Margin）的总结，基于讲者的思路，梳理问题本质、攻击路径、影响及解决方案。全程用中文，确保清晰易懂。

## 1. Incorrect Fee Structure（不正确的费用结构）

### 问题描述

在Key Finance协议中，奖励复合（reward compounding）机制存在漏洞，允许攻击者通过三明治攻击（sandwich attack）窃取质押奖励。核心问题是：

- **无费用或锁定期**：质押和取消质押无需成本或时间限制。
- **奖励分配基于瞬时快照**：奖励通过`updateAllRewards`函数更新，分配按调用时的质押比例计算，不考虑质押时间。

### 攻击路径

攻击者（如Bob）监控mempool，在奖励更新前一刻质押大额资金（例如10000 GMX Key），获取奖励后立即取消质押并卖出。例如：

- Alice质押100（持续一周），Bob质押10000（仅1秒）。
- 总质押10100，产生1%利润（101单位奖励）。
- Alice得：101 \* (100/10100) ≈ 1单位；Bob得：101 \* (10000/10100) ≈ 100单位。

Bob贡献极少（仅1秒），却拿走99%奖励，严重稀释长期质押者（如Alice）的收益。

### 影响

- **奖励稀释**：长期质押者的收益被短期投机者抢夺，破坏激励机制。
- **无风险套利**：攻击者无需承担资产价格风险，成本极低。
- **协议吸引力下降**：用户因收益低而失去质押动力，可能导致流动性不足。

### 解决方案

- **锁定期**：要求质押持续一定时间（如7天）。
- **收取费用**：对质押/取消质押加费用，增加攻击成本。
- **时间加权奖励**：奖励按质押时间\*金额分配，类似“points per share”模型。
- **平滑奖励更新**：奖励随时间累积（如GMX的P&L计算），避免阶梯式更新。

## 2. DoS Dividends（分红机制的DoS攻击）

### 问题描述

协议的`distributeDividends`函数使用无界for循环遍历`users`数组，为每个用户分配分红。攻击者可通过创建大量地址（每个mint 1 wei）无限膨胀`users`数组，导致分红函数gas超限，交易失败。

### 攻击路径

- **添加用户**：`mint`函数在余额为0时将地址push到`users`数组。
- **膨胀数组**：攻击者创建数千地址，分别mint 1 wei，使数组巨大。
- **触发DoS**：调用`distributeDividends`时，循环遍历所有用户，耗尽block gas limit。

### 影响

- **协议瘫痪**：分红无法分发，影响正常用户。
- **用户信任下降**：协议功能受阻，可能导致用户流失。

### 解决方案

- **Points per Share模型**：不逐个计算用户分红，用全局`dividendPerToken`记录每token分红。
  - 计算公式：`dividendPerToken = totalDividends / totalSupply`。
  - 用户领取：`balance * (currentDividendPerToken - userLastDividendPerToken)`。
- **优势**：只需更新单一存储变量，适用于所有持有者，避免循环。

## 3. Swap Margin（交换保证金操纵AMM）

### 问题描述

在IVX期权协议中，`swapMargin`函数允许用户在portfolio中将保证金（如USDC）换成另一支持token（如DAI），但未验证交换后position是否可清算（liquidatable）。攻击者可通过Uniswap交换，指定极低amountOut并三明治自己，抽取portfolio几乎所有价值。

### 攻击路径

- 用户卖期权给AMM，收取premium。
- 调用`swapMargin`，在Uniswap交换保证金，指定amountOut=0，前跑/后跑自己，抽取价值。
- 重复操作，提取99%保证金，留下破产position，协议清算时承担损失。

### 影响

- **资金流失**：攻击者拿走保证金+premium，协议损失资金。
- **不公平机制**：攻击者在亏损时撤回保证金，盈利时正常关闭，形同“无风险”操作。
- **协议风险**：清算破产position导致流动性池受损。

### 解决方案

- **后置验证**：交换后检查position是否可清算，若是则revert。
- **限制amountOut**：确保交换输出足够维持position健康。
- **类比perpetuals**：类似GMX，需确保用户不能主动使position破产。

## 总结

这三个漏洞反映了DeFi协议设计中的常见问题：

- **Incorrect Fee Structure**：缺少费用或锁定期，导致奖励分配不公。
- **DoS Dividends**：无界循环使协议易受gas攻击。
- **Swap Margin**：缺少后置验证，允许用户操纵保证金提取。

审计时需关注：

- 奖励机制是否考虑时间权重。
- 循环是否可被膨胀。
- 用户操作是否可能导致协议损失。

通过引入锁定期、费用、时间加权模型、单一存储变量或后置验证，可有效防范这些漏洞。

# 2025-08-13

# Web3 升级模式与 Eternal Storage 学习笔记

## 日期
2025年8月13日

## 学习内容

### 1. 升级模式（Upgrade Pattern）

#### 定义
升级模式是一种智能合约设计模式，用于解决以太坊区块链上智能合约部署后不可修改的问题。通过将**逻辑（业务代码）**和**数据（状态存储）**分离，实现在不更改合约地址的情况下更新合约逻辑。

#### 核心思想
- **代理合约（Proxy Contract）**：
  - 存储状态数据，用户的交互入口。
  - 通过`delegatecall`将调用转发到逻辑合约，保持地址不变。
- **逻辑合约（Implementation Contract）**：
  - 包含业务逻辑，可部署新版本进行升级。
- **工作原理**：
  - 用户与代理合约交互，代理合约调用逻辑合约的代码，但使用自己的存储空间。
  - 升级时，只需更新代理指向的新逻辑合约地址。

#### 优点
- 可升级性：支持修复漏洞或添加功能。
- 用户体验：用户无需更新合约地址。
- 灵活性：逻辑可自由更新，数据保持不变。

#### 缺点与风险
- 复杂性：代理模式增加开发和审计难度。
- 安全风险：升级机制可能被恶意利用（例如治理攻击）。
- 存储冲突：新旧逻辑合约存储布局不一致可能导致数据损坏。

#### 常见实现
- **OpenZeppelin 的 UUPS（Universal Upgradeable Proxy Standard）**：轻量、高效的代理模式。
- **Transparent Proxy Pattern**：避免函数选择器冲突，适合复杂合约。
- **Diamond Standard (EIP-2535)**：支持多个逻辑合约（facets）通过单一代理管理。

#### 审计要点
- **初始化安全**：确保`initialize`函数只能调用一次，防止重入攻击。
- **存储布局**：检查新旧逻辑合约的存储一致性。
- **权限管理**：验证升级权限（治理或多签）的安全性。
- **事件记录**：确保升级事件被记录，便于追踪。

---

### 2. Eternal Storage（永恒存储）

#### 定义
Eternal Storage 是一种与升级模式结合的设计模式，通过独立的存储合约管理状态数据，进一步分离数据和逻辑，提高可升级性和灵活性。

#### 核心思想
- **存储合约**：专门存储状态变量（mapping、数组等），提供 getter 和 setter 方法。
- **逻辑合约**：不存储状态，仅通过调用存储合约读写数据。
- **升级方式**：逻辑合约可替换，存储合约保持不变，数据持久保存。

#### 工作原理
1. **存储合约设计**：
   - 使用通用数据结构（如`mapping(bytes32 => uint256)`）存储数据。
   - 提供 getter 和 setter 方法，例如：
     ```solidity
     contract EternalStorage {
         mapping(bytes32 => uint256) private uintStorage;
         
         function getUint(bytes32 key) public view returns (uint256) {
             return uintStorage[key];
         }
         
         function setUint(bytes32 key, uint256 value) public {
             uintStorage[key] = value;
         }
     }
     ```
2. **逻辑合约交互**：
   - 逻辑合约通过调用存储合约的 getter/setter 方法操作数据。
   - 升级时部署新逻辑合约，存储合约地址和数据不变。

#### 优点
- 数据持久性：数据与逻辑分离，升级不影响状态。
- 灵活性：通用存储结构支持多种逻辑合约。
- 可扩展性：支持新功能添加。

#### 缺点与风险
- 复杂性：维护存储和逻辑两套合约，增加开发和审计成本。
- Gas 成本：外部调用存储合约增加 gas 消耗。
- 键冲突：逻辑合约使用相同键可能导致数据覆盖。
- 权限管理：需严格控制存储合约的 setter 方法调用权限。

#### Eternal Storage vs. 标准代理模式
- **标准代理模式**：数据和逻辑共享代理合约的存储，逻辑通过`delegatecall`访问。
- **Eternal Storage**：数据完全独立于存储合约，逻辑通过`call`访问，逻辑合约无状态。

#### 使用场景
- 复杂系统：需要频繁升级或模块化设计。
- 多逻辑合约：一个存储合约支持多个逻辑合约。
- 历史案例：The DAO 等早期项目使用类似设计。

#### 审计要点
- **键冲突**：确保逻辑合约使用唯一键访问存储。
- **权限控制**：验证存储合约 setter 方法的访问权限。
- **数据一致性**：检查 getter/setter 方法的正确性。
- **Gas 优化**：评估外部调用的 gas 成本。
- **升级兼容性**：确保新逻辑合约与存储合约接口兼容。

---

## 总结
- **升级模式**通过代理模式实现合约逻辑的可升级，保持用户交互地址不变，适合需要长期维护的 DApp。
- **Eternal Storage**进一步分离数据和逻辑，通过独立存储合约提高灵活性，但需注意复杂性和键冲突风险。
- 审计时需关注存储布局、权限管理和数据一致性，确保安全性和可靠性。

# 2025-08-12

# Web3 审计课程学习笔记（2025年8月12日）

## 1. 课程背景与目标
本次课程聚焦智能合约安全审计，旨在帮助学习者：
- 掌握以太坊密码学基础（secp256k1 椭圆曲线、ECDSA）。
- 识别常见漏洞（如签名可延展性）。
- 培养审计敏感性，关注边缘案例（如负数舍入）和复杂逻辑（如升级模式）。
- 为后续学习（如升级模式）奠定基础。

---

## 2. 边缘案例：负数舍入问题
- **内容**：在智能合约审计中，需关注数值处理的边缘案例，尤其是负数舍入。
- **关键点**：
  - 负数“向下舍入”（round down）时，绝对值变小（更接近零），而非数值变小。
  - 审计时需检查合约逻辑是否正确处理负数，以防意外行为。
- **应用**：提高对数值运算细节的敏感性，避免逻辑漏洞。

---

## 3. 以太坊密码学基础：secp256k1 椭圆曲线
### 3.1 椭圆曲线的数学结构
- **定义**：secp256k1 是以太坊使用的椭圆曲线，定义为 \( y^2 = x^3 + 7 \mod p \)，其中：
  - \( p \)：一个大素数，定义模运算范围。
  - \( y^2 \)：导致曲线关于 x 轴对称，每个 \( x \) 坐标对应正负两个 \( y \) 值。
- **群结构**：
  - secp256k1 是一个点集（群），点满足上述方程，坐标为模 \( p \) 的大整数。
  - 群的阶 \( n \)（点数）是一个大素数，支持点的数学映射。
- **模运算**：
  - 模 \( p \) 使曲线点有限，超过 \( p/2 \) 的 \( y^2 \) 值“绕回”曲线另一侧。
  - 这种结构保障了密码学运算的安全性。

### 3.2 椭圆曲线运算
- **加法**：
  - 取两点 \( A \) 和 \( B \)，画直线相交于第三点 \( C \)，再关于 x 轴反射得到 \( A + B \)。
  - 保证结果仍在曲线上，满足群的封闭性。
- **乘法**：
  - 重复加法，例如 \( k \cdot A = A + A + \dots + A \)（\( k \) 次）。
  - 以太坊公钥生成：公钥 \( R = e \cdot A \)，其中 \( e \) 是私钥，\( A \) 是生成点。
- **安全性**：
  - 离散对数问题：已知 \( R \) 和 \( A \)，反推 \( e \) 几乎不可能。
  - 私钥 \( e \) 保密，公钥 \( R \) 可公开。

### 3.3 应用
- 以太坊使用 secp256k1 生成公私钥对，签名消息，验证身份。
- 审计中需理解这些运算，以识别签名相关漏洞。

---

## 4. ECDSA 签名机制
### 4.1 签名生成
- **流程**：
  1. 用户拥有私钥 \( e \) 和公钥 \( E = e \cdot A \)。
  2. 生成临时随机私钥 \( k \)，计算临时公钥 \( K = k \cdot A \)。
  3. 签名 \( (v, r, s) \)：
     - \( r \)：\( K \) 的 \( x \) 坐标。
     - \( s \)：通过公式 \( s = k^{-1} (hash(m) + e \cdot r) \mod n \) 计算，\( n \) 是曲线阶。
     - \( v \)：恢复标识符，指示 \( K \) 的 \( y \) 坐标（正或负）。
- **作用**：
  - 签名验证通过 `ecrecover` 恢复公钥 \( E \)，确认消息由 \( e \) 签名。
  - 验证高效，但反推私钥 \( e \) 或 \( k \) 不可行。

### 4.2 签名可延展性（Signature Malleability）
- **原理**：
  - 椭圆曲线的对称性：每个 \( x \) 坐标（\( r \)）对应两个点 \( (x, y) \) 和 \( (x, -y) \)。
  - 原始签名 \( (r, s, v) \) 可被篡改为 \( (r, n-s, v') \)，仍恢复出相同公钥。
  - 攻击者可利用此构造新签名，重复执行合约操作（如双重支付）。
- **代码演示**：
  - 使用 Node.js 环境，生成签名 \( (r, s) \)，篡改 \( s \) 为 \( n-s \)。
  - 验证新签名 \( (r, n-s) \)，结果仍有效。
- **漏洞场景**：
  - 合约未检查签名唯一性，攻击者可重放篡改签名，导致逻辑错误。

### 4.3 防御措施
- **限制 \( s \) 值**：
  - 要求 \( s \leq n/2 \)，拒绝 \( n-s \)，如 OpenZeppelin ECDSA 库实现。
- **防止重放**：
  - 使用 nonce 或签名哈希记录已用签名。
- **审计要点**：
  - 检查 `ecrecover` 使用，确保规范化签名和防重放机制。

---

## 5. 后续主题：升级模式
- **内容预告**：
  - 介绍智能合约升级模式（如代理合约、透明代理）。
  - 分析五种升级模式的优缺点及潜在漏洞（如存储冲突）。
- **建议**：
  - 完成 Side Quest 2，熟悉升级模式后再学习漏洞分析。
  - 审计时关注复杂逻辑交互，借鉴密码学部分的严谨方法。

---

## 6. “Mob Audit”可能的含义
- **背景**：课程未明确定义“mob audit”，以下是基于上下文的推测。
- **可能解释**：
  1. **社区审计（Community Audit）**：
     - 通过 bug bounty 或社区协作审查合约代码，寻找漏洞。
     - 类似平台：Immunefi、Hacken。
     - 与课程中多人协作审查复杂逻辑（如签名、升级模式）一致。
  2. **移动端审计（Mobile Audit）**：
     - 针对移动端应用（如 MetaMask 移动版）与合约的交互安全。
     - 关注签名验证、用户输入等移动端特有风险。
  3. **模块化审计（Modular Audit）**：
     - 课程特有术语，可能指针对特定模块（如签名、升级逻辑）的集中审计。
     - 类似“mob programming”，强调协作或模块化审查。
- **审计应用**：
  - 无论具体含义，需结合自动化工具（Slither、MythX）和手动审查。
  - 关注高风险模块（如 `ecrecover`、代理合约），确保逻辑安全。

---

## 7. 审计中的关键 takeaways
- **密码学基础**：
  - 理解 secp256k1 和 ECDSA 是审计签名相关逻辑的前提。
  - 掌握椭圆曲线运算（加法、乘法）和离散对数问题的安全性。
- **漏洞识别**：
  - 签名可延展性是常见漏洞，需检查 `ecrecover` 和签名验证逻辑。
  - 关注边缘案例（如负数舍入）和复杂模块（如升级模式）。
- **防御策略**：
  - 使用规范化签名（\( s \leq n/2 \)）、nonce 防止重放。
  - 结合社区协作和工具，提升审计覆盖面。
- **后续学习**：
  - 深入研究升级模式漏洞，关注代理合约、存储布局等。
  - 培养对复杂逻辑的敏感性，结合课程练习（如 Side Quest 2）实践。

---

## 8. 进一步探索
- **建议**：
  - 复习 ECDSA 签名公式，理解 \( s \) 和 \( n-s \) 的数学等价性。
  - 实践 Node.js 代码，模拟签名可延展性攻击。
  - 学习 OpenZeppelin ECDSA 库，掌握规范化签名实现。
  - 探索升级模式（如 UUPS、透明代理）的审计要点。
- **资源**：
  - OpenZeppelin 文档：ECDSA 库和代理合约实现。
  - Bug Bounty 平台：Immunefi、Hacken。
  - 工具：Slither、MythX、Echidna。

# 2025-08-11

# Web3 审计学习笔记 - 2025年8月11日

以下是基于转录内容的Web3审计学习笔记，涵盖Solidity舍入问题和以太坊密码学（secp256k1椭圆曲线及签名可延展性）的核心要点，整理自讲者的逻辑，适用于智能合约安全审计。

---

## 一、Solidity中的舍入问题（Rounding Issues）

### 1.1 核心问题：整数除法截断
- **原因**：Solidity没有浮点数，所有除法操作会自动向下取整（truncation），丢弃小数部分。
  - 示例：`9 / 10` 在Python中得`0.9`，在Solidity中得`0`。
  - 后果：精度丢失，可能导致金融合约中的严重漏洞。
- **代币小数位（Decimals）**：
  - ERC20代币通过`decimals`函数模拟小数：
    - WETH：18位小数，`1 WETH = 1 * 10^18 Wei`。
    - USDC：6位小数，`1 USDC = 1 * 10^6`。
  - 若代币定义为0小数（如假想USDF），则`1 / 2 = 0`，无法表示小数（如0.5 USDF）。
- **常见性**：舍入问题是智能合约审计中前三到前五的常见漏洞原因，可能导致中、高、严重级别的问题。

### 1.2 案例分析

#### 案例1：M9 - 更新平均价格总是向下舍入
- **场景**：DeFi协议中，计算仓位平均价格以确定盈亏（P&L）。
  - 长仓P&L：`仓位大小 * (当前市场价格 - 平均进入价格)`。
  - 短仓P&L：`仓位大小 * (平均进入价格 - 当前市场价格)`。
  - `increasePosition`函数更新平均价格：
    ```
    新平均价格 = (现有仓位大小 * 现有平均价格 + 新增订单大小 * 执行价格) / (现有仓位大小 + 新增订单大小)
    ```
- **问题**：
  - 除法操作导致截断。例如：
    - 现有仓位：`1 * 10^18`，平均价格：`100 * 10^8`（$100）。
    - 新增订单：`1 * 10^10`，执行价格：`101 * 10^8`（$101）。
    - 计算结果应为`100.000099... * 10^8`，但截断后仍为`100 * 10^8`。
  - 影响：用户可用高价（$101）买入代币，但平均价格仍记录为$100，可能被恶意利用。
- **修复**：
  - 长仓：使用**向上舍入除法**（如OpenZeppelin的`ceilDiv`），确保平均价格偏高，防止获利。
  - 短仓：继续使用向下舍入，降低P&L。
- **审计要点**：检查除法操作，验证是否正确处理截断，尤其在金融计算中。

#### 案例2：T-MAP2 - 错误Tick舍入
- **场景**：集中流动性协议（如Uniswap V3），价格曲线分tick（价格点），需将非标准tick（如7）舍入到有效tick（如0或10，tickSpacing=10）。
  - 0-for-1（零兑一）：tick向左舍入（向上，如7→10）。
  - 1-for-0（一兑零）：tick向右舍入（向下，如7→0）。
- **问题**：
  - 原`roundAhead`函数逻辑错误：
    ```solidity
    function roundAhead(int tick, int tickSpacing, bool zeroForOne) {
        int rounded = tick / tickSpacing * tickSpacing;
        if (zeroForOne && rounded < 0) {
            rounded += tickSpacing;
        }
        return rounded;
    }
    ```
  - 示例：
    - `tick = 7`，`zeroForOne = true`，期望舍入到10，实际得0。
    - `tick = -7`，`zeroForOne = false`，期望舍入到-10，实际得0。
  - 问题根源：未正确处理正负tick和交易方向的组合。
- **修复**：
  - 修改逻辑：
    ```solidity
    function roundAhead(int tick, int tickSpacing, bool zeroForOne) {
        int rounded = tick / tickSpacing * tickSpacing;
        if (zeroForOne) {
            if (rounded > 0 || (rounded == 0 && tick > 0)) {
                rounded += tickSpacing;
            }
        } else {
            if (rounded < 0 || (rounded == 0 && tick < 0)) {
                rounded -= tickSpacing;
            }
        }
        return rounded;
    }
    ```
  - 修复效果：正确处理所有正负tick和交易方向。
- **审计要点**：
  - 测试边缘情况（如正负tick、零值）。
  - 验证自定义舍入逻辑的正确性，使用工具（如Chisel）测试。

### 1.3 审计建议
- **关注除法符号（/）**：
  - 检查是否发生截断，验证是否符合预期。
  - 测试小值、负数、零值等边缘情况，确认是否影响系统逻辑或可被利用。
- **负数舍入**：
  - 负数截断使绝对值变小（向0靠拢），如`-7 / 10 = 0`。
  - 确保逻辑正确处理正负数舍入方向。
- **复杂逻辑**：自定义舍入逻辑需全面测试，避免逻辑错误。
- **使用成熟库**：如OpenZeppelin的Math库，提供安全的舍入函数。

---

## 二、以太坊密码学：secp256k1椭圆曲线与签名可延展性

### 2.1 secp256k1椭圆曲线
- **作用**：以太坊用于生成公钥、私钥和签名验证的密码学基础。
- **方程**：`y^2 ≡ x^3 + 7 (mod p)`。
  - `y^2`导致曲线关于x轴对称。
  - `mod p`（p为大质数）使曲线在有限域`Zp`中，值“环绕”p/2。
- **群结构**：
  - secp256k1是一个点群，包含满足方程的(x, y)坐标。
  - 群的阶n（点总数）是大质数。
  - 生成点G用于生成公钥：`公钥 = k * G`（k为私钥）。
- **群操作**：
  - **点加法**：两点A、B的连线与曲线交于第三点C，反射C得A+B。
  - **点乘**：重复点加法，如k * A是将A加k次。
  - **安全性**：给定公钥R和G，逆向计算私钥k（离散对数问题）几乎不可能。

### 2.2 公钥与私钥生成
- **私钥**：随机选择的小于n的整数，保密。
- **公钥**：`公钥 = k * G`，是曲线上的点，可公开。
- **签名机制**：
  - 私钥签名消息，生成(v, r, s)。
  - 公钥验证签名，确认消息来源。
  - 验证高效，反推私钥极难。

### 2.3 签名可延展性（Signature Malleability）
- **定义**：ECDSA签名(v, r, s)存在对称性，`(r, s)`和`(r, -s mod n)`都能恢复到相同公钥，导致多个有效签名。
- **漏洞场景**：
  - 合约使用`ecrecover`验证签名：
    ```solidity
    function execute(address owner, bytes32 messageHash, uint8 v, bytes32 r, bytes32 s) {
        require(ecrecover(messageHash, v, r, s) == owner, "Invalid signature");
        // 执行操作
    }
    ```
  - 攻击者观察合法签名(v, r, s)，构造等价签名(v', r', s')，重复执行操作（如双重花费）。
- **原因**：
  - ECDSA签名的s值对称：`(r, s)`和`(r, -s mod n)`等价。
  - 合约未限制签名唯一性或s值范围。
- **修复**：
  - 限制`s < n/2`，防止-s mod n：
    ```solidity
    require(s < n / 2, "Invalid s value");
    ```
  - 记录已使用签名：
    ```solidity
    mapping(bytes32 => bool) usedSignatures;
    require(!usedSignatures[messageHash], "Signature already used");
    usedSignatures[messageHash] = true;
    ```
  - 使用EIP-712，增加签名上下文（如链ID、合约地址）。
- **审计要点**：
  - 检查`ecrecover`使用，验证是否限制s值或记录签名。
  - 测试签名重用和边缘情况。
  - 推荐使用OpenZeppelin的`ECDSA.sol`库。

---

## 三、总结与审计建议
- **舍入问题**：
  - 根源：Solidity整数除法截断。
  - 案例教训：M9（平均价格截断导致获利）、T-MAP2（错误tick舍入）。
  - 建议：检查除法操作，测试边缘情况，使用成熟数学库。
- **签名可延展性**：
  - 根源：ECDSA签名对称性。
  - 案例：未限制签名唯一性导致双重花费。
  - 建议：限制s值，记录签名，使用EIP-712和安全库。
- **通用建议**：
  - 关注高风险操作（除法、`ecrecover`）。
  - 测试极端输入（小值、负数、零值）。
  - 优先使用经过验证的库（如OpenZeppelin）。

---

**后续学习**：
- 深入ECDSA签名算法的数学细节。
- 分析更多签名可延展性漏洞案例。
- 学习其他密码学相关漏洞（如重放攻击）。

# 2025-08-10

### 智能合约安全漏洞学习笔记

---

这份笔记基于提供的文件内容，整理了智能合约中常见的几种安全漏洞及其原理。

#### 1. Ether Note 合约审计漏洞

* **漏洞类型**: 会计不匹配（Accounting Mismatch）
* **问题描述**: 合约赎回功能设计不当，导致费用收取和存储机制不一致。当用户使用 ERC-20 代币赎回时，费用从 ERC-20 余额中扣除，但合约所有者只能以本地 ETH 形式提取费用。
* **最终影响**: 随着时间推移，合约中的 ERC-20 代币费用被“锁定”，无法提取。同时，ETH 余额会减少。这可能导致当用户使用 ETH 赎回时，合约内没有足够的 ETH 支付，从而导致赎回失败，形成拒绝服务（DoS）攻击。
* **根本原因**: 赎回机制有两种（ETH 和 ERC-20），但费用存储和提取机制只有一种，且未对代币类型进行区分。

#### 2. 奖励复合中的“三明治攻击”

* **漏洞类型**: 激励机制滥用（Exploiting Incentive Mechanisms）
* **问题描述**: 某些质押或保险库系统奖励分批次发放。
* **攻击过程**: 攻击者通过监控内存池（mempool），在奖励发放前质押大量代币，获取高额奖励。奖励发放后，他们立即解除质押并卖出代币，实现无风险套利。
* **影响**: 长期质押者应得的奖励被稀释，攻击者窃取了他们的收益，可能导致系统不可持续。
* **解决方案**: 采用平滑的奖励分配模型，如“每股分红”或“每代币分红”，避免依赖于分批更新。

#### 3. DoS（拒绝服务）攻击

* **漏洞类型**: 拒绝服务（Denial of Service）
* **问题描述**: 合约在分发股息或奖励时使用一个遍历所有用户的无界 `for` 循环。
* **攻击过程**: 攻击者通过创建大量地址（余额为零），将这些地址添加到合约的用户列表中。
* **影响**: 当合约尝试分发奖励时，由于巨大的用户列表，`for` 循环会消耗过多的 Gas，导致交易失败。这使合约的奖励分发功能无法使用，形成了拒绝服务攻击。
* **解决方案**: 避免使用无界 `for` 循环。应使用“每股分红”或类似模型，让用户可以随时根据自己的持股比例自行提取奖励。

#### 4. IVX 合约中的不当授权

* **漏洞类型**: 授权逻辑错误（Authorization Logic Flaw）
* **问题描述**: 永续合约协议中的 `swap margin` 功能缺乏对代币的充分验证。
* **攻击过程**: 攻击者利用该功能将受支持的抵押品“交换”为不受系统支持的代币，然后将这些抵押品从合约中取出。
* **影响**: 攻击者在未实现亏损的情况下提取了所有抵押品，导致其头寸缺乏担保。协议的有限合伙人（LPs）因此面临损失，而攻击者则实现了“无风险获利”。
* **根本原因**: 这是一个经典的“会计问题”，当合约同时处理多种代币类型时，未能正确地进行区分和处理。

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
