---
timezone: UTC+8
---

# Jintol

**GitHub ID:** JintolChan

**Telegram:** @JintolOfficial

## Self-introduction

Freelance

## Notes

<!-- Content_START -->

# 2025-08-28
<!-- DAILY_CHECKIN_2025-08-28_START -->
今天去了bitcoinasia 2025
<!-- DAILY_CHECKIN_2025-08-28_END -->


# 2025-08-27
<!-- DAILY_CHECKIN_2025-08-27_START -->
打卡，今天去了sui workshop
<!-- DAILY_CHECKIN_2025-08-27_END -->


# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
水卡，开始补作业
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-23
<!-- DAILY_CHECKIN_2025-08-23_START -->
水卡，ethshenzhen面基了几位助教老师和实习计划的小伙伴
<!-- DAILY_CHECKIN_2025-08-23_END -->


# 2025-08-22
<!-- DAILY_CHECKIN_2025-08-22_START -->
水卡，今天参加了ethshenzhen志愿者
<!-- DAILY_CHECKIN_2025-08-22_END -->

# 2025-08-20

听了两场知识分享会

# 2025-08-19

学习uniswap V2,V3,V4

# 2025-08-17

做了一个基于以太坊的 DApp，用户通过质押 ETH 来解锁 AI 对话权限。
https://github.com/JintolChan/ChainChat

# 2025-08-16

了解更多web3项目，Uniswap、Aave、Lido、ENS、Farcaster

# 2025-08-15

阅读0G白皮书，写了一篇精读稿。  
vibe coding Dapp的前端。

# 2025-08-14

水卡，提交了X Space申请，听了两场知识分享会。

# 2025-08-13

## Docker基本操作
查看版本
```shell
docker --version
```
搜索镜像
```shell
docker search 镜像名
```
拉取镜像
```shell
docker pull 镜像名
```
查看镜像
```shell
docker images
```
删除镜像
```shell
docker rmi 镜像ID
```
启动容器
```shell
docker run [参数] 镜像名
```
停止容器
```shell
docker stop 容器ID
```
查看容器
```shell
docker ps -a
```
删除容器
```shell
docker rm 容器ID
```
进入容器
```shell
docker exec -it 容器ID bash
```
构建容器
```shell
docker build -t 镜像名 .
```
## Kurtosis CLI 基本操作
创建 Enclave
```shell
kurtosis enclave add my-enclave
```
列出 Enclave
```shell
kurtosis enclave ls
```
删除 Enclave
```shell
kurtosis enclave rm my-enclave
```
运行 Startlark包
```shell
kurtosis run ./package
```
列出服务
```shell
kurtosis service ls my-enclave
```
查看日志
```shell
kurtosis service logs my-enclave service-name
``` 
进入容器
```shell
kurtosis service shell my-enclave service-name
```

## 以太坊测试网络搭建
### 环境准备
- Docker: https://docs.docker.com/get-started/get-docker/
- Kurtosis CLI: https://docs.kurtosis.com/install/

MacOS：

- Docker Desktop verison: 4.41.1
- Kurtosis CLI version: 1.10.3

Ubuntu:

- Docker Engine version:  27.2.0
- Kurtosis CLI: 1.10.3  

Windows可以使用WSL

镜像源：https://hub.docker.com/u/ethpandaops

kurtosis Ethereum package: https://github.com/ethpandaops/ethereum-package?tab=readme-ov-file

### 使用 kurtosis 搭建测试网络
网络的配置：network_params.yaml
```YAML
participants:
  - el_type: geth
    cl_type: lighthouse
    count: 1
network_params:
  preset: minimal
additional_services:
  - tx_fuzz
```

运行一个隔离的环境，可以随时创建和销毁:
```shell
kurtosis run --enclave eth-dev github.com/ethpandaops/ethereum-package --args-file network_params.yaml
```

查看已经创建的测试网络：
```shell
kurtosis enclave inspect 
```
查看当前在运行中的容器：
```shell
docker ps --no-trunc
```
查看容器具体的日志：
```shell
docker logs -f 
```
验证者客户端的启动参数：
```shell
# 启动 Lighthouse 验证者客户端
lighthouse vc \
  # 基础配置
  --debug-level=info \                      # 日志级别（info 为默认）
  --testnet-dir=/network-configs \          # 测试网配置文件目录

  # 验证者密钥配置
  --validators-dir=/validator-keys/keys \   # 验证者密钥存储目录
  --secrets-dir=/validator-keys/secrets \   # 密钥解密密码存储目录
  --init-slashing-protection \              # 初始化 slashed 保护数据库（防止双重签名）

  # 与共识层通信
  --beacon-nodes=http://ip:4000 \  # 连接的 beacon 节点 HTTP 地址

  --suggested-fee-recipient=地址 \  # 区块奖励接收地址

  # 监控指标配置
  --metrics \                               # 启用 metrics 数据收集
  --metrics-address=0.0.0.0 \               # metrics 监听地址（允许外部访问）
  --metrics-port=8080 \                     # metrics 端口
  --metrics-allow-origin=* \                # 允许所有来源的 metrics 请求（测试环境用）
```

共识层客户端的启动参数：
```shell
# 启动 Lighthouse 共识层节点
lighthouse beacon_node \
  # 基础配置
  --debug-level=info \                      # 日志级别（info 为默认）
  --datadir=/data/lighthouse/beacon-data \  # 数据存储目录
  --testnet-dir=/network-configs \          # 测试网配置文件目录

  # 网络监听配置
  --listen-address=0.0.0.0 \                # 监听所有网络接口
  --port=9000 \                             # UDP 发现端口
  --quic-port=9001 \                        # QUIC 协议端口
  --target-peers=0 \                        # 目标连接节点数（0 表示不主动连接）
  --disable-packet-filter \                 # 禁用数据包过滤（测试环境用）
  --enable-private-discovery \              # 启用私有网络发现

  # ENR (以太坊节点记录) 配置
  --disable-enr-auto-update \               # 禁用 ENR 自动更新
  --enr-address=172.16.0.12 \               # ENR 中声明的节点 IP
  --enr-tcp-port=9000 \                     # ENR 中声明的 TCP 端口
  --enr-udp-port=9000 \                     # ENR 中声明的 UDP 端口
  --enr-quic-port=9001 \                    # ENR 中声明的 QUIC 端口

  # HTTP API 配置
  --http \                                  # 启用 HTTP API
  --http-address=0.0.0.0 \                  # API 监听地址
  --http-port=4000 \                        # API 端口

  # 与执行层通信配置
  --execution-endpoints=http://172.16.0.11:8551 \  # 执行层认证 RPC 地址
  --jwt-secrets=/jwt/jwtsecret \             # JWT 密钥文件（与执行层共享）
  --suggested-fee-recipient=0x8943545177806ED17B9F23F0a21ee5948eCaa776 \  # 建议的费用接收地址

  # 监控指标配置
  --metrics \                               # 启用 metrics 收集
  --metrics-address=0.0.0.0 \               # metrics 监听地址
  --metrics-port=5054 \                     # metrics 端口
  --metrics-allow-origin=* \                # 允许所有来源的 metrics 请求
```

执行层客户端的启动参数：
```shell
# 初始化Geth节点（基于创世文件配置区块链）
geth init \
  --datadir=/data/geth/execution-data \  # 指定数据存储目录
  /network-configs/genesis.json         # 创世区块配置文件

# 启动Geth节点（核心运行参数）
geth \
  --networkid=3151908 \                  # 网络ID（与创世文件匹配）
  --verbosity=3 \                        # 日志详细程度（3=info级别）
  --datadir=/data/geth/execution-data \  # 数据存储目录（与初始化一致）

  # HTTP RPC配置（供外部调用）
  --http \                               # 启用HTTP RPC
  --http.addr=0.0.0.0 \                  # 监听所有网络接口
  --http.port=8545 \                     # HTTP端口
  --http.vhosts=* \                      # 允许所有虚拟主机
  --http.corsdomain=* \                  # 允许所有跨域请求
  --http.api=admin,engine,net,eth,web3,debug,txpool \  # 启用的API列表

  # WebSocket配置（实时数据推送）
  --ws \                                 # 启用WebSocket
  --ws.addr=0.0.0.0 \                    # 监听所有网络接口
  --ws.port=8546 \                       # WebSocket端口
  --ws.api=admin,engine,net,eth,web3,debug,txpool \    # 启用的API列表
  --ws.origins=* \                       # 允许所有来源的连接

  # 安全与认证配置
  --allow-insecure-unlock \              # 允许不安全的账户解锁（测试环境用）
  --nat=extip:172.16.0.11 \              # 外部IP（用于P2P发现）
  --authrpc.port=8551 \                  # 认证RPC端口（与共识层通信）
  --authrpc.addr=0.0.0.0 \               # 认证RPC监听地址
  --authrpc.vhosts=* \                   # 认证RPC允许的虚拟主机
  --authrpc.jwtsecret=/jwt/jwtsecret \   # JWT密钥文件（与共识层共享）

  # 同步与交易配置
  --syncmode=full \                      # 全节点同步模式
  --rpc.allow-unprotected-txs \          # 允许未受保护的交易（测试环境用）

  # 监控与 metrics
  --metrics \                            # 启用metrics收集
  --metrics.addr=0.0.0.0 \               # metrics监听地址
  --metrics.port=9001 \                  # metrics端口

  # P2P网络配置
  --discovery.port=30303 \               # 节点发现端口
  --port=30303 \                         # P2P通信端口
  --miner.gasprice=1 \                   # 矿工最低Gas价格（测试环境用）
  --bootnodes=                           # 启动节点列表（留空表示无初始节点）
```

执行层 RPC：

获取链ID：
```shell
curl http://127.0.0.1:52538 \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"eth_chainId","params":[],"id":1,"jsonrpc":"2.0"}'
```
获取当前最新的区块高度：
```shell
curl http://127.0.0.1:52538 \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"eth_blockNumber","params":[],"id":1,"jsonrpc":"2.0"}'
```

共识层 RPC：

查看当前节点是否还在同步操作：
```shell
curl --location 'http://127.0.0.1:52544/eth/v1/node/syncing' \
--header 'Content-Type: application/json'
```

获取当前节点的版本：
```shell
curl --location 'http://127.0.0.1:32856/eth/v1/node/version' \
--header 'Content-Type: application/json'
```
### 节点可观测性
通过以下配置安装 grafana 和 prometheus：
```YAML
participants:
  - el_type: geth
    cl_type: lighthouse
    count: 1
network_params:
  preset: minimal
additional_services:
  - prometheus
  - grafana
  - tx_fuzz
```
添加 granafa dashboard：

grafana Id：https://grafana.com/grafana/dashboards/18463-go-ethereum-by-instance/

# 2025-08-12

## Uniswap
Uniswap 是以太坊上最大的去中心化交易所（DEX），通过自动化做市商（AMM）模型实现无需订单薄的代币交易  
V1 (2018) AMM 机制：支持 ETH 和 ERC-20 对  
V2 (2020) 任意 ERC20/ERC20 交易对，闪电贷，价格预言机  
V3 (2021) 集中流动性 (LP定制价格区间)  
V4 (2025) 可定制化流动性池 (Hooks 机制)  

## SushiSwap
SushiSwap的“吸血鬼攻击” (2020年9月)  
SushiSwap 分叉 Uniswap V2，推出 SUSHI 代币激励，吸引Uniswap LP迁移，把 Uniswap 的流动性“抽走”  
短期内SushiSwap TVL 超过 Uniswap  
但创始人抛售 SUSHI 引发信任危机  

## MakerDAO 
以太坊上首个去中心化稳定币协议，主打超额抵押借贷生成稳定币DAI  
单抵押 DAI (Sai，2017) 仅支持 ETH 作为抵押资产  
多抵押 DAI (MCD，2019) 支持多种抵押品 (WBTC、USDC等)  
Maker Endgame （2023-2024，重大改革）子 DAO、算法稳定币、RWA 扩展

# 2025-08-11

## 智能合约基础特性
### 什么是智能合约
智能合约是以太坊应用层的基础构建模块。它们是存储在区块链上的计算机程序，遵循“如果这样，那么那样”的逻辑，并保证按照其代码定义的规则执行，一旦创建，这些规则就无法更改。

Nick Szabo提出了“智能合约”这一概念。1994年，他撰写了关于该概念的介绍性文章，1996年又撰写了关于智能合约潜在应用的探索性文章。

萨博设想了一个数字市场，其中自动、加密安全的流程使交易和商业功能能够在无需可信中介的情况下实现。以太坊上的智能合约将这一愿景付诸实践。 
### 智能合约核心特性
不可篡改性：部署代码后无法修改  
透明性：所有人可查看验证  
可编程性：灵活定制业务逻辑  
去中心化：运行在分布式网络
### 状态存储与执行环境
区块链是一个全局状态机，每个区块包含一组交易，这些交易会改变整个网络的状态。状态包括所有账户余额、合约存储数据等信息。  
状态转换过程：  
区块创建：矿工收集待处理交易  
状态计算：按顺序执行每笔交易，计算新状态  
状态根更新：所有状态变化汇总为新的状态根哈希  
区块链接：新区块通过哈希链接到前一个区块  
### Gas 机制
总费用 = Gas Used * (Base Fee + Priority Fee)


Gas Used：实际消耗的计算量，由操作复杂度决定固定值  
Base Fee：网络基础费用，网络自动调整被销毁  
Priority Fee：优先级小费，用户设置给验证者

网络拥堵时费用飙升的原因：  
有限的区块空间：每个区块只能容纳约 30M Gas  
Base Fee 自动调整：高使用率推高基础费用  
Priority Fee 竞价：用户提高小费争夺优先级

## Hardhat部署合约流程
### 开发设备环境准备
本地开发环境
```
# 确保 Node.js 版本 >= 18
node --version

# 安装项目依赖
npm install

# 编译合约检查语法
npx hardhat compile
```
环境变量配置  
创建 .env 文件：
```
# RPC 节点配置
SEPOLIA_RPC_URL=https://sepolia.infura.io/v3/YOUR_PROJECT_ID
# 或使用 Alchemy: https://eth-sepolia.g.alchemy.com/v2/YOUR_API_KEY

# 部署者私钥
PRIVATE_KEY=0x... # ⚠️ 仅用于测试网，注意安全

# 合约验证
ETHERSCAN_API_KEY=YOUR_ETHERSCAN_API_KEY
```
### 部署配置与网络连接
Hardhat 网络配置

### 合约部署执行
部署命令执行
```
# 执行部署到 Sepolia
npx hardhat ignition deploy yourfile --network sepolia
```
### 部署验证清单
 合约编译成功  
 测试网 ETH 余额充足  
 所有合约部署成功  
 获得12个区块确认  
 Etherscan 验证通过  
 前端环境变量已更新  
 合约交互测试正常

# 2025-08-09

# Solidity开发入门 Ⅰ
## 合约
pragma是一个用于指定编译器版本的关键字。它的作用是确保代码能够在特定版本的编译器下正确编译和执行，以避免潜在的兼容性问题。
```solidity
pragma solidity ^0.8.20;
```
在最新版本的ERC20中，使用的编译器版本不低于0.8.20  

使用关键字 contract 定义合约，一个 Solidity 的 .sol 文件可以包含一个或多个 contract。  
```solidity
contract Name { }//要定义一个 合约，我们使用关键字 contract，后面跟上合约的名称。
```
## 变量
int 表示整数的变量类型  
uint 表示正整数的变量类型  
bool 布尔变量只有两个值：true 或 false，通常用于判断。

# 2025-08-08

# 以太坊开发环境搭建
## 基础环境搭建  
Node.js  
npm  
Git  
## 开发框架Hardhat
创建项目
```
npm install --global hardhat
mkdir eth-dev && cd eth-dev
npx hardhat
```
启动本地节点
```
npx hardhat node
```
部署智能合约
```
npx hardhat run scripts/deploy.js --network localhost
```

# 2025-08-07

# 判断Web3公司/项目是否合规

项目是否在明确、受监管的司法辖区内注册  
是否为了为了取得合规资质，经过专业机构的第三方审计或安全测试  
是否具备KYC、AML等反洗钱与用户身份识别制度  
是否对外公开项目负责人、团队背景、资金来源路径等基本信息  
是否具备高风险模块  
是否存在高风险的扩张方式

# 2025-08-06

Web3 故事会

2018年：Dapp爆发年

EOS失败的原因：
过度中心化，DPos的治理危机；资源模型设计失败，RAM炒作与用户体验恶化；技术承诺未兑现，性能与安全性问题；生态激励不足，资金耗尽与开发者流失；社区分裂与品牌失信。

# 2025-08-05

打卡


参加Web3 安全分享和答疑，学习web3安全，学习区块链黑暗森林自救手册

# 2025-08-04

打卡

参加以太坊中文周会，了解最新行业动态；
Gather Town自习，学习Uniswap V3

# 2025.07.31


<!-- Content_END -->
