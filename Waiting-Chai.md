---
timezone: UTC+8
---

# Waiting-Chai

**GitHub ID:** Waiting-Chai

**Telegram:** @chai

## Self-introduction

想要当remote数字游民， web2后端转型， 也会一点前端； 主体技术栈 golang， java， ts等；

## Notes

<!-- Content_START -->
# 2025-08-11

## 关于erc-20学习；
---
### erc20标准中的token是什么东西 ？ 
- 简明讲：ERC-20 里的 “token” 就是由一个智能合约维护的“同质化余额单位”。
简明讲：**ERC-20 里的 “token” 就是由一个智能合约维护的“同质化余额单位”**。
这个合约就是“代币系统本身”，它用一张表记录每个地址有多少单位；你钱包只是去读/改这张表。

### 它到底是啥

* **一份账本**：在合约里通常是
  `mapping(address => uint256) balances;`
  你的“持币”= `balances[你的地址]` 的数值。
* **同质化**：每一单位都一模一样、可互换（不像 NFT）。
* **规则写死在合约里**：怎么转、谁能铸币/销毁、总量多少，都按合约代码来。

### 一个 ERC-20 代币通常包含

* **基本信息**：`name`、`symbol`、`decimals`（USDT 是 6，很多代币是 18）。
* **总供给**：`totalSupply()`
* **余额与转账**：`balanceOf()`、`transfer()`
* **授权与代扣**：`approve()`、`allowance()`、`transferFrom()`
  （给 DApp 一张“额度卡”，DApp 可代你扣款）
* **事件**：`Transfer`、`Approval`（区块浏览器靠它显示记录）
* （可选）`mint`/`burn`、EIP-2612 `permit`（免链上 approve 的签名授权）

### 跟“币”的关系

* **ETH 不是 ERC-20**：它是以太坊的原生币，付 Gas 用的；
  想把 ETH 按 ERC-20 用，要包成 **WETH**（1:1）。
* 其它链有对标标准：BSC 的 **BEP-20**、Solana 的 **SPL**……彼此不直接互通。

### 它能代表什么（举例）

* **钱**：稳定币 USDT/USDC（支付、清算）
* **治理权**：UNI、COMP（投票）
* **积分/游戏币**：项目内消费
* **凭证/收据**：DEX 的 LP 份额、质押凭证
* **包装资产**：WETH、wBTC（把别的资产“映射”成 ERC-20）

### 极小示意（核心思路）

```solidity
mapping(address => uint256) balances;
mapping(address => mapping(address => uint256)) allowances;

function transfer(address to, uint256 amt) external returns (bool) {
    balances[msg.sender] -= amt;
    balances[to] += amt;
    emit Transfer(msg.sender, to, amt);
    return true;
}

function approve(address spender, uint256 amt) external returns (bool) {
    allowances[msg.sender][spender] = amt;
    emit Approval(msg.sender, spender, amt);
    return true;
}

function transferFrom(address from, address to, uint256 amt) external returns (bool) {
    allowances[from][msg.sender] -= amt;
    balances[from] -= amt;
    balances[to] += amt;
    emit Transfer(from, to, amt);
    return true;
}
```

**一句话收尾**：
ERC-20 的 “token” 不是某个文件或硬币，而是**合约里的余额单位 + 一套统一的操作接口**。有了它，钱包/交易所/DeFi 都能用同一套办法和你的代币协作。

# 2025-08-10

##ChainOath合约代码；
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/utils/ReentrancyGuard.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

/**
 * ChainOath 誓约合约
 * 实现基于区块链的誓约系统，支持多角色参与和ERC20代币质押
 * 包含创建者、守约人、监督者角色的完整誓约生命周期管理
 */
contract ChainOath is ReentrancyGuard, Ownable {
    
    /// 表示誓约当前状态的枚举
    enum OathStatus {
        Pending,    // 创建后尚未接受
        Accepted,   // 已被接受（所有角色成功在startTime之前完成了质押确认）
        Fulfilled,  // 誓言已履行（完成最后一轮监督者监督，并受约人满足守约条件）
        Broken,     // 誓言未履行（受约人誓约次数 > maxCommitterFailures）
        Aborted     // 因为种种原因被废止了
    }
    
    /// 誓约主体结构
    struct Oath {
        string title;                    // 誓约标题
        string description;              // 誓约描述
        address committer;               // 守约人，唯一
        address[] supervisors;           // 监督者列表，可以有多个
        uint256 totalReward;             // Creator 总质押奖励金额
        uint256 committerStake;          // 守约人需质押金额
        uint256 supervisorStake;         // 每位监督者需质押金额
        uint16 supervisorRewardRatio;    // 监督者奖励比例（如 10 表示 10%）
        uint32 checkInterval;            // check 间隔（单位：秒）
        uint32 checkWindow;              // check 后签名时间窗口（单位：秒）
        uint16 checkThresholdPercent;    // 判定守约成功的监督者签名比例
        uint16 maxSupervisorMisses;      // 监督者最大允许失职次数
        uint16 maxCommitterFailures;     // 守约人最大允许失约次数
        uint16 checkRoundsCount;         // 总检查轮次
        uint32 startTime;                // 誓约开始时间（时间戳，单位为s）
        uint32 endTime;                  // 誓约结束时间（时间戳，单位为s）
        uint32 createTime;               // 创建时间（时间戳，单位为s）
        address creator;                 // 创建者地址
        IERC20 token;                    // 使用的ERC20代币
        OathStatus status;               // 当前状态
    }
    
    /// 监督记录结构
    struct SupervisionRecord {
        mapping(address => bool) hasChecked;     // 监督者是否已检查
        mapping(address => bool) approvals;     // 监督者的批准状态
        uint16 totalChecked;                    // 总检查人数
        uint16 totalApproved;                   // 总批准人数
        bool isCompleted;                       // 本轮是否完成
        bool isSuccess;                         // 本轮是否成功
    }
    
    /// 质押信息结构
    struct StakeInfo {
        mapping(address => uint256) amounts;     // 质押金额
        mapping(address => IERC20) tokens;      // 质押代币类型
        mapping(address => bool) hasStaked;     // 是否已质押
    }
    
    /// 监督者状态结构
    struct SupervisorStatus {
        uint16 missCount;                       // 失职次数
        uint16 successfulChecks;                // 成功检查次数
        bool isDisqualified;                   // 是否被取消资格
    }
    
    // 状态变量
    uint256 public nextOathId;
    mapping(uint256 => Oath) public oaths;
    mapping(uint256 => mapping(uint16 => SupervisionRecord)) public supervisionRecords;
    mapping(uint256 => StakeInfo) internal creatorStakes;
    mapping(uint256 => StakeInfo) internal committerStakes;
    mapping(uint256 => StakeInfo) internal supervisorStakes;
    mapping(uint256 => mapping(address => SupervisorStatus)) public supervisorStatuses;
    mapping(uint256 => uint16) public currentRounds;
    mapping(uint256 => uint16) public committerFailures;
    
    // 事件定义
    event OathCreated(uint256 indexed oathId, address indexed creator, string title);
    event OathAccepted(uint256 indexed oathId);
    event OathAborted(uint256 indexed oathId);
    event OathFulfilled(uint256 indexed oathId);
    event OathBroken(uint256 indexed oathId);
    event StakeDeposited(uint256 indexed oathId, address indexed staker, uint256 amount, address token);
    event SupervisionSubmitted(uint256 indexed oathId, uint16 round, address indexed supervisor, bool approval);
    event RewardClaimed(uint256 indexed oathId, address indexed claimer, uint256 amount, address token);
    
    constructor() Ownable(msg.sender) {}
    
    /**
     * 创建新的誓约
     * _oath 誓约结构体数据
     * _token 使用的ERC20代币地址
     */
    function createOath(
        Oath memory _oath,
        address _token
    ) external nonReentrant {
        require(block.timestamp < _oath.startTime, "Start time must be in the future");
        require(_oath.startTime < _oath.endTime, "End time must be after start time");
        require(_oath.committer != address(0), "Invalid committer address");
        require(_oath.supervisors.length > 0, "At least one supervisor required");
        require(_oath.totalReward > 0, "Total reward must be greater than 0");
        require(_oath.supervisorRewardRatio <= 100, "Supervisor reward ratio cannot exceed 100%");
        require(_oath.checkThresholdPercent <= 100, "Check threshold cannot exceed 100%");
        require(_oath.checkInterval > 0, "Check interval must be greater than 0");
        require(_oath.checkWindow > 0, "Check window must be greater than 0");
        
        // 计算检查轮次
        uint32 duration = _oath.endTime - _oath.startTime;
        uint16 rounds = uint16((duration + _oath.checkInterval - 1) / _oath.checkInterval);
        
        uint256 oathId = nextOathId++;
        
        // 初始化誓约
        Oath storage oath = oaths[oathId];
        oath.title = _oath.title;
        oath.description = _oath.description;
        oath.committer = _oath.committer;
        oath.totalReward = _oath.totalReward;
        oath.committerStake = _oath.committerStake;
        oath.supervisorStake = _oath.supervisorStake;
        oath.supervisorRewardRatio = _oath.supervisorRewardRatio;
        oath.checkInterval = _oath.checkInterval;
        oath.checkWindow = _oath.checkWindow;
        oath.checkThresholdPercent = _oath.checkThresholdPercent;
        oath.maxSupervisorMisses = _oath.maxSupervisorMisses;
        oath.maxCommitterFailures = _oath.maxCommitterFailures;
        oath.checkRoundsCount = rounds;
        oath.startTime = _oath.startTime;
        oath.endTime = _oath.endTime;
        oath.createTime = uint32(block.timestamp);
        oath.creator = msg.sender;
        oath.token = IERC20(_token);
        oath.status = OathStatus.Pending;
        
        // 复制并检查监督者数组
        for (uint i = 0; i < _oath.supervisors.length; i++) {
            address supervisor = _oath.supervisors[i];
            require(supervisor != address(0), "Invalid supervisor address");
            // 检查重复地址
            for (uint j = i + 1; j < _oath.supervisors.length; j++) {
                require(_oath.supervisors[j] != supervisor, "Duplicate supervisor address");
            }
            oath.supervisors.push(supervisor);
        }
        
        // 创建者质押奖励金额
        require(oath.token.transferFrom(msg.sender, address(this), _oath.totalReward), "Creator stake transfer failed");
        creatorStakes[oathId].amounts[msg.sender] = _oath.totalReward;
        creatorStakes[oathId].tokens[msg.sender] = oath.token;
        creatorStakes[oathId].hasStaked[msg.sender] = true;
        
        emit OathCreated(oathId, msg.sender, _oath.title);
        emit StakeDeposited(oathId, msg.sender, _oath.totalReward, _token);
    }
    
    /**
     * 守约人质押
     * _oathId 誓约ID
     * _token 质押代币地址
     * _amount 质押金额
     */
    function committerStake(uint256 _oathId, address _token, uint256 _amount) external nonReentrant {
        Oath storage oath = oaths[_oathId];
        require(oath.creator != address(0), "Oath does not exist");
        require(msg.sender == oath.committer, "Only committer can stake");
        require(oath.status == OathStatus.Pending, "Oath is not in pending status");
        
        // 先检查状态，如果时间已过则设为Aborted
        if (block.timestamp >= oath.startTime) {
            oath.status = OathStatus.Aborted;
            emit OathAborted(_oathId);
            revert("Staking period has ended");
        }
        
        require(!committerStakes[_oathId].hasStaked[msg.sender], "Already staked");
        require(_amount >= oath.committerStake, "Insufficient stake amount");
        
        IERC20 token = IERC20(_token);
        require(token.transferFrom(msg.sender, address(this), _amount), "Stake transfer failed");
        
        committerStakes[_oathId].amounts[msg.sender] = _amount;
        committerStakes[_oathId].tokens[msg.sender] = token;
        committerStakes[_oathId].hasStaked[msg.sender] = true;
        
        emit StakeDeposited(_oathId, msg.sender, _amount, _token);
        
        _checkOathAcceptance(_oathId);
    }
    
    /**
     * 监督者质押
     * _oathId 誓约ID
     * _token 质押代币地址
     * _amount 质押金额
     */
    function supervisorStake(uint256 _oathId, address _token, uint256 _amount) external nonReentrant {
        Oath storage oath = oaths[_oathId];
        require(oath.creator != address(0), "Oath does not exist");
        require(_isSupervisor(_oathId, msg.sender), "Not a supervisor");
        require(oath.status == OathStatus.Pending, "Oath is not in pending status");
        require(block.timestamp < oath.startTime, "Staking period has ended");
        require(!supervisorStakes[_oathId].hasStaked[msg.sender], "Already staked");
        require(_amount >= oath.supervisorStake, "Insufficient stake amount");
        
        IERC20 token = IERC20(_token);
        require(token.transferFrom(msg.sender, address(this), _amount), "Stake transfer failed");
        
        supervisorStakes[_oathId].amounts[msg.sender] = _amount;
        supervisorStakes[_oathId].tokens[msg.sender] = token;
        supervisorStakes[_oathId].hasStaked[msg.sender] = true;
        
        emit StakeDeposited(_oathId, msg.sender, _amount, _token);
        
        _checkOathAcceptance(_oathId);
    }
    
    /**
     * 监督者提交检查结果
     * _oathId 誓约ID
     * _approval 是否批准（true为守约，false为失约）
     */
    function submitSupervision(uint256 _oathId, bool _approval) external nonReentrant {
        Oath storage oath = oaths[_oathId];
        require(oath.creator != address(0), "Oath does not exist");
        require(_isSupervisor(_oathId, msg.sender), "Not a supervisor");
        require(supervisorStakes[_oathId].hasStaked[msg.sender], "Supervisor not staked");
        require(oath.status == OathStatus.Accepted, "Oath is not active");
        require(!supervisorStatuses[_oathId][msg.sender].isDisqualified, "Supervisor is disqualified");
        
        uint16 currentRound = _getCurrentRound(_oathId);
        require(currentRound > 0 && currentRound <= oath.checkRoundsCount, "Invalid round");
        
        uint32 roundStartTime = oath.startTime + (currentRound - 1) * oath.checkInterval;
        require(block.timestamp >= roundStartTime, "Round not started yet");
        require(block.timestamp <= roundStartTime + oath.checkWindow, "Check window has passed");
        
        SupervisionRecord storage record = supervisionRecords[_oathId][currentRound];
        require(!record.hasChecked[msg.sender], "Already submitted for this round");
        
        record.hasChecked[msg.sender] = true;
        record.approvals[msg.sender] = _approval;
        record.totalChecked++;
        
        if (_approval) {
            record.totalApproved++;
            supervisorStatuses[_oathId][msg.sender].successfulChecks++;
        }
        
        emit SupervisionSubmitted(_oathId, currentRound, msg.sender, _approval);
        
        _processRoundCompletion(_oathId, currentRound);
    }
    
    /**
     * 处理超时的监督轮次
     * _oathId 誓约ID
     */
    function processTimeoutRound(uint256 _oathId) external {
        Oath storage oath = oaths[_oathId];
        require(oath.creator != address(0), "Oath does not exist");
        require(oath.status == OathStatus.Accepted, "Oath is not active");
        
        uint16 currentRound = _getCurrentRound(_oathId);
        require(currentRound > 0 && currentRound <= oath.checkRoundsCount, "Invalid round");
        
        uint32 roundStartTime = oath.startTime + (currentRound - 1) * oath.checkInterval;
        require(block.timestamp > roundStartTime + oath.checkWindow, "Check window not passed yet");
        
        SupervisionRecord storage record = supervisionRecords[_oathId][currentRound];
        require(!record.isCompleted, "Round already completed");
        
        // 处理未提交的监督者
        for (uint i = 0; i < oath.supervisors.length; i++) {
            address supervisor = oath.supervisors[i];
            if (supervisorStakes[_oathId].hasStaked[supervisor] && 
                !supervisorStatuses[_oathId][supervisor].isDisqualified &&
                !record.hasChecked[supervisor]) {
                
                supervisorStatuses[_oathId][supervisor].missCount++;
                
                if (supervisorStatuses[_oathId][supervisor].missCount > oath.maxSupervisorMisses) {
                    supervisorStatuses[_oathId][supervisor].isDisqualified = true;
                }
            }
        }
        
        _processRoundCompletion(_oathId, currentRound);
    }
    
    /**
     * 领取奖励
     * _oathId 誓约ID
     */
    function claimReward(uint256 _oathId) external nonReentrant {
        Oath storage oath = oaths[_oathId];
        require(oath.creator != address(0), "Oath does not exist");
        require(oath.status == OathStatus.Fulfilled || oath.status == OathStatus.Broken, "Oath not completed");
        
        if (msg.sender == oath.committer) {
            _claimCommitterReward(_oathId);
        } else if (_isSupervisor(_oathId, msg.sender)) {
            _claimSupervisorReward(_oathId);
        } else if (msg.sender == oath.creator) {
            _claimCreatorReward(_oathId);
        } else {
            revert("Not authorized to claim");
        }
    }
    
    /**
     * 检查并更新誓约状态
     * _oathId 誓约ID
     */
    function checkOathStatus(uint256 _oathId) external {
        Oath storage oath = oaths[_oathId];
        require(oath.creator != address(0), "Oath does not exist");
        
        if (oath.status == OathStatus.Pending && block.timestamp >= oath.startTime) {
            oath.status = OathStatus.Aborted;
            emit OathAborted(_oathId);
        }
    }
    
    /**
     * 退回质押金（仅在誓约被废止时）
     * _oathId 誓约ID
     */
    function refundStake(uint256 _oathId) external nonReentrant {
        Oath storage oath = oaths[_oathId];
        require(oath.creator != address(0), "Oath does not exist");
        require(oath.status == OathStatus.Aborted, "Oath is not aborted");
        
        if (msg.sender == oath.creator && creatorStakes[_oathId].hasStaked[msg.sender]) {
            uint256 amount = creatorStakes[_oathId].amounts[msg.sender];
            IERC20 token = creatorStakes[_oathId].tokens[msg.sender];
            creatorStakes[_oathId].hasStaked[msg.sender] = false;
            require(token.transfer(msg.sender, amount), "Refund transfer failed");
            emit RewardClaimed(_oathId, msg.sender, amount, address(token));
        } else if (msg.sender == oath.committer && committerStakes[_oathId].hasStaked[msg.sender]) {
            uint256 amount = committerStakes[_oathId].amounts[msg.sender];
            IERC20 token = committerStakes[_oathId].tokens[msg.sender];
            committerStakes[_oathId].hasStaked[msg.sender] = false;
            require(token.transfer(msg.sender, amount), "Refund transfer failed");
            emit RewardClaimed(_oathId, msg.sender, amount, address(token));
        } else if (_isSupervisor(_oathId, msg.sender) && supervisorStakes[_oathId].hasStaked[msg.sender]) {
            uint256 amount = supervisorStakes[_oathId].amounts[msg.sender];
            IERC20 token = supervisorStakes[_oathId].tokens[msg.sender];
            supervisorStakes[_oathId].hasStaked[msg.sender] = false;
            require(token.transfer(msg.sender, amount), "Refund transfer failed");
            emit RewardClaimed(_oathId, msg.sender, amount, address(token));
        } else {
            revert("No stake to refund");
        }
    }
    
    // 内部函数
    
    /**
     * 检查是否为监督者
     */
    function _isSupervisor(uint256 _oathId, address _addr) internal view returns (bool) {
        Oath storage oath = oaths[_oathId];
        for (uint i = 0; i < oath.supervisors.length; i++) {
            if (oath.supervisors[i] == _addr) {
                return true;
            }
        }
        return false;
    }
    
    /**
     * 检查誓约是否可以被接受
     */
    function _checkOathAcceptance(uint256 _oathId) internal {
        Oath storage oath = oaths[_oathId];
        
        if (block.timestamp >= oath.startTime) {
            oath.status = OathStatus.Aborted;
            emit OathAborted(_oathId);
            return;
        }
        
        // 检查守约人是否已质押
        if (!committerStakes[_oathId].hasStaked[oath.committer]) {
            return;
        }
        
        // 检查所有监督者是否已质押
        for (uint i = 0; i < oath.supervisors.length; i++) {
            if (!supervisorStakes[_oathId].hasStaked[oath.supervisors[i]]) {
                return;
            }
        }
        
        // 所有人都已质押，誓约被接受
        oath.status = OathStatus.Accepted;
        emit OathAccepted(_oathId);
    }
    
    /**
     * 获取当前轮次
     */
    function _getCurrentRound(uint256 _oathId) internal view returns (uint16) {
        Oath storage oath = oaths[_oathId];
        if (block.timestamp < oath.startTime) {
            return 0;
        }
        if (block.timestamp >= oath.endTime) {
            return oath.checkRoundsCount;
        }
        
        uint32 elapsed = uint32(block.timestamp) - oath.startTime;
        return uint16(elapsed / oath.checkInterval) + 1;
    }
    
    /**
     * 处理轮次完成
     */
    function _processRoundCompletion(uint256 _oathId, uint16 _round) internal {
        Oath storage oath = oaths[_oathId];
        SupervisionRecord storage record = supervisionRecords[_oathId][_round];
        
        if (record.isCompleted) {
            return;
        }
        
        // 计算有效监督者数量
        uint16 validSupervisors = 0;
        for (uint i = 0; i < oath.supervisors.length; i++) {
            address supervisor = oath.supervisors[i];
            if (supervisorStakes[_oathId].hasStaked[supervisor] && 
                !supervisorStatuses[_oathId][supervisor].isDisqualified) {
                validSupervisors++;
            }
        }
        
        // 检查是否所有有效监督者都已提交或超时
        bool allSubmitted = (record.totalChecked >= validSupervisors);
        uint32 roundStartTime = oath.startTime + (_round - 1) * oath.checkInterval;
        bool timeoutPassed = (block.timestamp > roundStartTime + oath.checkWindow);
        
        if (!allSubmitted && !timeoutPassed) {
            return;
        }
        
        record.isCompleted = true;
        
        // 判断本轮是否成功
        if (validSupervisors > 0) {
            uint16 approvalPercent = (record.totalApproved * 100) / validSupervisors;
            record.isSuccess = (approvalPercent >= oath.checkThresholdPercent);
        } else {
            record.isSuccess = false;
        }
        
        if (!record.isSuccess) {
            committerFailures[_oathId]++;
            if (committerFailures[_oathId] > oath.maxCommitterFailures) {
                oath.status = OathStatus.Broken;
                emit OathBroken(_oathId);
                return;
            }
        }
        
        // 检查是否完成所有轮次
        if (_round >= oath.checkRoundsCount) {
            oath.status = OathStatus.Fulfilled;
            emit OathFulfilled(_oathId);
        }
    }
    
    /**
     * 守约人领取奖励
     */
    function _claimCommitterReward(uint256 _oathId) internal {
        Oath storage oath = oaths[_oathId];
        require(oath.status == OathStatus.Fulfilled, "Oath not fulfilled");
        require(committerStakes[_oathId].hasStaked[msg.sender], "No stake to claim");
        
        // 计算守约人奖励
        uint256 committerReward = oath.totalReward * (100 - oath.supervisorRewardRatio) / 100;
        
        // 退还质押金
        uint256 stakeAmount = committerStakes[_oathId].amounts[msg.sender];
        IERC20 stakeToken = committerStakes[_oathId].tokens[msg.sender];
        committerStakes[_oathId].hasStaked[msg.sender] = false;
        
        // 转账奖励和质押金
        require(oath.token.transfer(msg.sender, committerReward), "Reward transfer failed");
        require(stakeToken.transfer(msg.sender, stakeAmount), "Stake refund failed");
        
        emit RewardClaimed(_oathId, msg.sender, committerReward + stakeAmount, address(oath.token));
    }
    
    /**
     * 监督者领取奖励
     */
    mapping(uint256 => uint256) private claimedSupervisorRewards;

    function _claimSupervisorReward(uint256 _oathId) internal {
        Oath storage oath = oaths[_oathId];
        require(supervisorStakes[_oathId].hasStaked[msg.sender], "No stake to claim");
        
        SupervisorStatus storage status = supervisorStatuses[_oathId][msg.sender];
        
        // 计算监督者奖励
        uint256 totalSupervisorReward = oath.totalReward * oath.supervisorRewardRatio / 100;
        uint16 validSupervisors = 0;
        uint16 totalSuccessfulChecks = 0;

        for (uint i = 0; i < oath.supervisors.length; i++) {
            address supervisor = oath.supervisors[i];
            if (supervisorStakes[_oathId].hasStaked[supervisor] && !supervisorStatuses[_oathId][supervisor].isDisqualified) {
                validSupervisors++;
                totalSuccessfulChecks += supervisorStatuses[_oathId][supervisor].successfulChecks;
            }
        }
        
        uint256 supervisorReward = 0;
        if (totalSuccessfulChecks > 0) {
            supervisorReward = (totalSupervisorReward * status.successfulChecks) / totalSuccessfulChecks;
        }

        claimedSupervisorRewards[_oathId] += supervisorReward;
        
        // 处理质押金
        uint256 stakeAmount = supervisorStakes[_oathId].amounts[msg.sender];
        IERC20 stakeToken = supervisorStakes[_oathId].tokens[msg.sender];
        supervisorStakes[_oathId].hasStaked[msg.sender] = false;
        
        // 如果未被取消资格，退还质押金
        if (!status.isDisqualified) {
            require(stakeToken.transfer(msg.sender, stakeAmount), "Stake refund failed");
        }
        
        // 转账奖励
        if (supervisorReward > 0) {
            require(oath.token.transfer(msg.sender, supervisorReward), "Reward transfer failed");
        }
        
        emit RewardClaimed(_oathId, msg.sender, supervisorReward + (status.isDisqualified ? 0 : stakeAmount), address(oath.token));
    }
    
    /**
     * 创建者领取剩余资金
     */
    function _claimCreatorReward(uint256 _oathId) internal {
        Oath storage oath = oaths[_oathId];
        require(creatorStakes[_oathId].hasStaked[msg.sender], "No stake to claim");
        
        creatorStakes[_oathId].hasStaked[msg.sender] = false;
        
        // 计算剩余金额（包括没收的质押金和未分配的奖励）
        uint256 totalSupervisorReward = oath.totalReward * oath.supervisorRewardRatio / 100;
        uint256 remainingBalance = oath.token.balanceOf(address(this));
        uint256 undistributedSupervisorReward = totalSupervisorReward - claimedSupervisorRewards[_oathId];
        remainingBalance += undistributedSupervisorReward;
        
        if (remainingBalance > 0) {
            require(oath.token.transfer(msg.sender, remainingBalance), "Remaining transfer failed");
            emit RewardClaimed(_oathId, msg.sender, remainingBalance, address(oath.token));
        }
    }
    
    // 查询函数
    
    /**
     * 获取誓约信息
     */
    function getOath(uint256 _oathId) external view returns (Oath memory) {
        return oaths[_oathId];
    }
    
    /**
     * 获取监督记录
     */
    function getSupervisionRecord(uint256 _oathId, uint16 _round) external view returns (
        uint16 totalChecked,
        uint16 totalApproved,
        bool isCompleted,
        bool isSuccess
    ) {
        SupervisionRecord storage record = supervisionRecords[_oathId][_round];
        return (record.totalChecked, record.totalApproved, record.isCompleted, record.isSuccess);
    }
    
    /**
     * 获取监督者状态
     */
    function getSupervisorStatus(uint256 _oathId, address _supervisor) external view returns (
        uint16 missCount,
        uint16 successfulChecks,
        bool isDisqualified
    ) {
        SupervisorStatus storage status = supervisorStatuses[_oathId][_supervisor];
        return (status.missCount, status.successfulChecks, status.isDisqualified);
    }
    
    /**
     * 获取当前轮次
     */
    function getCurrentRound(uint256 _oathId) external view returns (uint16) {
        return _getCurrentRound(_oathId);
    }
    
    /**
     * 检查地址是否已质押
     */
    function hasStaked(uint256 _oathId, address _addr) external view returns (bool) {
        Oath storage oath = oaths[_oathId];
        if (_addr == oath.creator) {
            return creatorStakes[_oathId].hasStaked[_addr];
        } else if (_addr == oath.committer) {
            return committerStakes[_oathId].hasStaked[_addr];
        } else if (_isSupervisor(_oathId, _addr)) {
            return supervisorStakes[_oathId].hasStaked[_addr];
        }
        return false;
    }
}

##连接钱包代码，ethers.js;
  //   点击链接钱包按钮出发
  const connectWallet = async () => {
    // 检测 MetaMask 注入
    if (!window.ethereum) {
      setWarningMessage(withoutMateMask);
      throw new Error("请先安装 MetaMask");
    }
    // v6 里叫 BrowserProvider
    const provider = new ethers.BrowserProvider(window.ethereum);
    // 请求账户授权
    await provider.send("eth_requestAccounts", []);
    const signer = await provider.getSigner();
    const addr = await signer.getAddress();
    console.log("当前地址：", addr);
    sessionStorage.setItem("currentUserAddr", addr);
  };

# 2025-08-09

安全的金库式存取款骨架（节选）

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.21;

import {IERC20} from "openzeppelin-contracts/token/ERC20/IERC20.sol";
import {SafeERC20} from "openzeppelin-contracts/token/ERC20/utils/SafeERC20.sol";
import {ReentrancyGuard} from "openzeppelin-contracts/security/ReentrancyGuard.sol";
import {Ownable} from "openzeppelin-contracts/access/Ownable.sol";

contract SafeVault is ReentrancyGuard, Ownable {
    using SafeERC20 for IERC20;

    IERC20 public immutable asset;
    mapping(address => uint256) public balances;

    error ZeroAmount();
    error InsufficientBalance();

    event Deposit(address indexed user, uint256 amount);
    event Withdraw(address indexed user, uint256 amount);

    constructor(IERC20 _asset, address _owner) {
        asset = _asset;
        _transferOwnership(_owner);
    }

    function deposit(uint256 amount) external nonReentrant {
        if (amount == 0) revert ZeroAmount();
        // Checks
        uint256 beforeBal = asset.balanceOf(address(this));
        // Interactions (pull)
        asset.safeTransferFrom(msg.sender, address(this), amount);
        // Effects
        uint256 received = asset.balanceOf(address(this)) - beforeBal; // 兼容手续费代币
        balances[msg.sender] += received;
        emit Deposit(msg.sender, received);
    }

    function withdraw(uint256 amount) external nonReentrant {
        if (amount == 0) revert ZeroAmount();
        uint256 bal = balances[msg.sender];
        if (bal < amount) revert InsufficientBalance();
        // Effects
        balances[msg.sender] = bal - amount;
        // Interactions (push)
        asset.safeTransfer(msg.sender, amount);
        emit Withdraw(msg.sender, amount);
    }
}

# 2025-08-08

学习打卡， 还是在做自己的dapp

# 2025-08-07

关于ChainOath
持续关注： https://github.com/Waiting-Chai/ChainOath

学习了 react 父子组件传值；

法律方面：

海外的合规性是否完备：AML&KYC

KYC（Know Your Customer）
· 目的：确认客户身份、评估风险等级、防止匿名或虚假账户。
· 关键动作：
– 身份核验：护照、驾照、人脸识别、活体检测等。
– 地址验证：近期水电账单、银行对账单等。
– 资金来源与用途：了解客户资金从哪来、用来干什么。
– 持续监控：账户行为发生异常时重新评估风险。

AML（Anti-Money Laundering）
· 目的：发现并阻止洗钱、恐怖融资等金融犯罪。
· 关键动作：
– 客户风险评级（CDD/EDD）。
– 交易监控：对大额、频繁、异常交易实时预警。
– 制裁名单筛查：与OFAC、EU、UN 等制裁名单实时比对。
– 可疑交易报告（SAR）：一旦触发阈值，必须向监管机构报告。
– 员工培训与内部审计：确保制度长期有效。

# 2025-08-06

学习了solidity，设计了一款dapp， 并进入了开发阶段， 目前前端页面开发完成， 合约还在开发中

链接在   https://github.com/Waiting-Chai/ChainOath

# ⛓️ 四、智能合约设计（核心逻辑）

## 🧬 ChainOath 智能合约规则文档

## 1. 角色定义

| 角色 | 描述 | 是否需质押 | 是否可获奖 | 是否影响誓约状态 |
|------|------|------------|------------|------------------|
| **发起人 Creator** | 创建誓约，设定奖励池、配置规则、分配角色 | ✅（质押全部奖励） | ❌ | ✅（设定规则） |
| **守约人 Committer** | 接受任务，履行誓约，被监督签名评定 | ✅（履约押金，可配置） | ✅ | ✅（是否守约） |
| **监督者 Supervisor** | 定期进行 check 并签名，评定守约人行为 | ✅（质押金，可配置） | ✅（按 check 次数） | ✅（监督决定） |
| **查看者 Viewer** | 仅查看誓约详情与状态 | ❌ | ❌ | ❌ |

---

## 2. 创建誓约所需字段

```solidity
struct Oath {
  string title;                     // 誓约标题
  string description;               // 誓约描述
  address[] committers;            // 守约人列表
  address[] supervisors;           // 监督者列表
  address rewardToken;             // 奖励代币地址
  uint256 totalReward;             // Creator 总质押奖励金额
  uint256 committerStake;          // 每位守约人需质押金额
  uint256 supervisorStake;         // 每位监督者需质押金额
  uint256 supervisorRewardRatio;   // 监督者奖励比例（如 10 表示 10%）
  uint256 committerRewardRatio;    // 守约人奖励比例（如 90 表示 90%）
  uint256 checkInterval;           // check 间隔（单位：秒）
  uint256 checkWindow;             // check 后签名时间窗口（单位：秒）
  uint256 checkThresholdPercent;   // 判定守约成功的监督者签名比例
  uint256 maxSupervisorMisses;     // 监督者最大允许失职次数
  uint256 maxCommitterFailures;    // 守约人最大允许失约次数
  uint256 startTime;               // 誓约开始时间
  uint256 endTime;                 // 誓约结束时间
}
```

## 3. 誓约流程说明

**阶段 1：创建**

Creator 创建誓约，配置上述所有参数并质押 totalReward。

守约人和监督者分别调用质押函数，缴纳履约/监督押金。

**阶段 2：监督与履约**

每隔 checkInterval 触发一个监督周期：

监督者需在 checkWindow 内签名表达“守约”或“失约”判断。

若签名未提交 → 判定为失职，本次奖励转入守约人奖励池。

若监督者失职次数超 maxSupervisorMisses → 其质押金被没收，归守约人。

若有效签名中 守约 占比 ≥ checkThresholdPercent → 判定该周期守约成功。

若守约失败次数超出 maxCommitterFailures → 判定整体誓约失败，守约人失去奖励，其质押金被没收，返还给 Creator。

**阶段 3：结算**

守约人成功完成任务：

- 获得奖励池中 committerRewardRatio 对应金额；
- 获得监督者失职转入的奖励；
- 取回自己质押金额。

守约人失约（失败次数超限）：

- 奖励池返还给 Creator；
- 守约人质押金没收。

监督者：

- 每完成一个有效 check 签名，可领取 (supervisorRewardRatio / 总check次数) 的奖励；
- 若失职次数超限 → 所有质押金被没收。

---

## 4. 奖励计算与惩罚机制

### 奖励分配公式

```sql
监督者总奖励 = totalReward × (supervisorRewardRatio / 100)
守约人总奖励 = totalReward × (committerRewardRatio / 100)
每次 check 奖励 = 监督者总奖励 / 总 check 次数
```

### 惩罚规则

| 行为 | 惩罚结果 |
|------|----------|
| 监督者未 check | 当次奖励归守约人，记录失职一次 |
| 监督者累计失职超限 | 没收全部质押金，奖励归守约人 |
| 守约人未完成任务次数超限 | 没收质押金，失去奖励，奖励归 Creator |

---

## 5. 状态管理与签名结构（示例）

```solidity
enum OathStatus { Pending, Active, Completed, Breached, Cancelled }
enum CheckResult { Pending, Success, Failed }

struct Check {
  uint256 slotId;
  address supervisor;
  CheckResult result;
  bytes32 reasonHash;
  bool rewarded;
}
```

---

## 6. 安全建议

- 所有转账使用 pullPayment 模式，防止重入；
- 签名采用 EIP-712 标准，确保链下交互安全；
- 支持链下存储 description 和 证明材料 到 IPFS，引用哈希；
- 未来可引入 仲裁者角色 处理争议情况（如监督者失联等）。

---

## 7. 示例配置参考

| 字段 | 示例值 |
|------|--------|
| totalReward | 100 ETH |
| supervisorRatio | 10% |
| committerRatio | 90% |
| committerStake | 2 ETH |
| supervisorStake | 1 ETH |
| checkInterval | 每 5 天 |
| checkWindow | 2 天 |
| maxSupervisorMisses | 2 次 |
| maxCommitterFailures | 1 次 |


---

# 2025-08-05

# 区块链基础

- **定义**  
  去中心化分布式账本技术，通过按时间顺序将交易记录打包成区块并链接成链，保证数据安全、透明、且不可篡改。

- **核心特性**  
  - **不可篡改**：每个区块包含前一区块的哈希，篡改需重写所有后续区块。  
  - **公开透明 & 匿名**：链上交易记录对外可查，但地址与真实身份不直接关联。  
  - **去中心化**：节点分布式维护账本，无单点控制；抵御故障和审查。  
  - **快速交易**：无需中介，全球节点可直接参与，交易确认高效。

---

# 以太坊概览

- **定位**  
  “区块链 2.0”平台，既是加密货币（ETH），也是支持智能合约的全球共享计算机。

- **智能合约**  
  存储在链上的可执行代码，条件满足时自动触发，支撑去中心化应用（DApp）、DeFi、NFT、DAO 等生态。

- **共识机制演进**  
  1. **PoW 阶段**：矿工通过算力竞争打包，能耗高、TPS ≈ 30。  
  2. **The Merge（2022）**：切换至 PoS，能耗降低 >99%，并引入分片与 Layer 2 扩容方案。

- **生态分层**  
  - **Layer 1（主网）**：EVM 执行层 + PoS 共识层。  
  - **Layer 2**：Optimistic Rollup、ZK Rollup 等批量处理方案。  
  - **侧链**：如 Polygon PoS、xDAI，通过桥接与主网交互。

---

# 行业赛道全览

- ## DeFi（去中心化金融）  
  - **Uniswap（AMM DEX）**：基于 x×y=k 自动定价，流动性提供者赚取交易费。  
  - **Compound（借贷协议）**：存款获 cToken，借款需超额抵押，动态利率、自动清算。  
  - **MakerDAO → Sky（稳定币）**：超额抵押生成 DAI/USDS，通过稳定费率与清算保持与美元挂钩。

- ## NFT（非同质化代币）  
  - **唯一性 & 所有权**：为数字资产赋予唯一标识与可验证的所有权。  
  - **智能合约 & 版税**：自动执行转移并可设定原创版税分配。  
  - **案例**：CryptoPunks、OpenSea。

- ## DAO（去中心化自治组织）  
  通过代币和智能合约进行社区治理与决策，无需传统管理层。

- ## Web3 + 乡建示例  
  - **南塘 DAO**：安徽阜阳乡村发行 NT 代币，用于社区贡献量化、治理投票与本地服务兑换。

# 2025-08-04

1. 学习solidity语法完结done；
2. 寻找sepolia的测试水龙头：  https://cloud.google.com/application/web3/faucet/ethereum/sepolia


# 2025.07.29


<!-- Content_END -->
