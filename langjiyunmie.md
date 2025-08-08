---
timezone: UTC+8
---

# 潘腾达

**GitHub ID:** langjiyunmie

**Telegram:** @so yun

## Self-introduction

web3新人，希望在这次共学中，能找到机会

## Notes

<!-- Content_START -->
# 2025-08-08

编写一个合约功能前我们要知道需要的参数

1. 币叫什么

2. 最小单位是多少

3. 币量初始值是多少

4. 之后呢，这些币应该是谁的，创始人的吧，那就要使用mapping捆绑其地址‘

   但是后面铸造也不一定全是给创始人的

5. 同时我们还需要实现授权功能，也就是创始人给的token赋权给一些人吧，那么当这些人使用这些授权的token的时候，授权资产减少，那么创始人的token也减少，那么需要写双重映射了

6. 准备工作做好了，正片开始，首先肯定是要初始化的，咱们使用构造函数自动初始化

7. 那么我们需要知道，什么时候token转了，授权了，铸造了，毁了，需要创建事件，emit触发事件，事件里面要使用indexed关键字

8. 接着就是完善函数功能了吧

### 开始写

1. 第一步声明我们要写的变量

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
constract token{
string public token_name="beaventao"//币全名
string public token_symbol="bea"//币简称
uint8 public dot=18;
uint256 public total_token=20000000*(10**uint256(dot))//这里是最小单位
address public owner;
}
```

2. 捆绑部分

   ```
   mapping(address => uint256) public balances;
   mapping(address => mapping(address => uint256)) public allowances;
   ```

3. 初始化

   ```
   constructor() {
           owner = msg.sender;
           balances[owner] = total_token;
       }
   ```

4. 创造事件

   ```
   event Transfer(address indexed from, address indexed to, uint256 value);
   event Approval(address indexed owner, address indexed spender, uint256 value);
   event Mint(address indexed to, uint256 value);
   event Burn(address indexed from, uint256 value);
   ```

5. 转账函数

   ```
    function transfer(address _to, uint256 _value) public returns (bool) {
           require(balances[msg.sender] >= _value, "Not enough tokens");
           balances[msg.sender] -= _value;
           balances[_to] += _value;
           emit Transfer(msg.sender, _to, _value);
           return true;
       }
   ```

6. 授权函数

   ```
   function approve(address _spender, uint256 _value) public returns (bool) {
           allowances[msg.sender][_spender] = _value;
           emit Approval(msg.sender, _spender, _value);
           return true;
       }
   ```

   这里解释两点

   1. 这里使用 “=“  而不是 ”+=”  因为是授权，必须是精确的数值
   2. [msg.sender] [_spender]  上面双重映射的另一个形式。该地址映射到另一个地址授权的代币数量，也就是映射到另一个地址，对该合约授权多少代币。

7. 授权转账函数

   ```
     function transferFrom(address _from, address _to, uint256 _value) public returns (bool) {
           require(balances[_from] >= _value, "Not enough tokens");
           require(allowances[_from][msg.sender] >= _value, "Allowance exceeded");
   
           balances[_from] -= _value;
           balances[_to] += _value;
           allowances[_from][msg.sender] -= _value;
           emit Transfer(_from, _to, _value);
           return true;
       }
   ```

8. 铸造和销毁函数

   ```
    function mint(address _to, uint256 _value) public returns (bool) {
           require(msg.sender == owner, "Only the owner can mint tokens");
           total_token += _value;
           balances[_to] += _value;
           emit Mint(_to, _value);
           emit Transfer(address(0), _to, _value);
           return true;
       }
   
       function burn(address _from, uint256 _value) public returns (bool) {
           require(msg.sender == owner, "Only the owner can burn tokens");
           require(balances[_from] >= _value, "Not enough tokens to burn");
           total_token -= _value;
           balances[_from] -= _value;
           emit Burn(_from, _value);
           emit Transfer(_from, address(0), _value);
           return true;
       }
   }
   ```

   针对address(0)解释

   1. `address(0)` 表示以太坊地址空间中的一个特定地址，这个地址通常被用来表示“空地址”或“无效地址”。它有助于明确地表示某个操作或状态没有实际关联的地址。
   2. 我理解为源泉地址，如同无尽的水源，我们只是从里面抽了一些水，销毁也就是把水送回去。这个过程只要没到其他人的手里，就不会被滥用，也就是无关系地址
   3. emit的参数数量要与事件对应

### 代码识别

下面是个错误代码，通过上面学习你把它改成正确的就算搞懂了

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
contract token{
 string public token_name="beaventao";
 string public token_symbol="bea";
 uint8  public dot=18;
 uint256 public total_token=20000000*(10**dot);
 address public owner;

mapping (address =>uint256) public balances;
mapping (address=> mapping (address=>uint256)) allowances;

constructor(){
 owner=msg.sender;
 balances[owner]=total_token;
}

 event transfer(address indexed from,address indexed to,uint256 value);
 event approval(address indexed from,address indexed to,uint256 value);
 event mint(address indexed from,address indexed to,uint256 value);
 event burn(address indexed from,address indexed to,uint256 value);


function transfer(address _to,uint256 _value) public returns(bool){
    require(balances[owner]>=_value,"not enough token");
    balances[owner]-=_value;
    balances[_to]+=_value;
    emit transfer(msg.sender, _to,_value);
    return true;
}

function approval(address _spendright, address _to,uint256 _value) public returns(bool){
    require(balances[msg.sender]>=_value,"error");
    allowances[msg.sender][_spendright]+=value;
    emit approval(msg.sender, _spendright,_value);
    return true;
}

function transferes(address _from,address _to,uint256 _value) returns (bool){
    require(allowances[msg.sender][_spendright]>=_value,"not enough money");
    balances[_to]+=_value;
    balances[msg.sender]-=_value;
    allowances[msg.sender][_spendright]-=_value;
    emit transfer(_from, _to, _value);
    return true;
}

function mint(address _to,uint256 token_num) public returns (bool){
    require(msg.sender,"you don't have right");
    total_token+=token_num;
    balances[_to]+=token_num;
    emit mint(_to, token_num);
    emit transfer( _to, token_num);
    return true;
}

function burn(address _to,uint256 token_num) public returns (bool){
    require(msg.sender,"you don't have right");
    total_token-=token_num;
    balances[_to]-=token_num;
    emit burn(_to, token_num);
    return true;
}
}
```



### 标准的erc20

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract BaseERC20 {
    string public name = "BaseERC20";
    string public symbol = "BERC20";
    uint8 public decimals = 18;
    uint256 public totalSupply = 100000000 * (10 ** uint256(decimals));
    address public owner;

    mapping(address => uint256) balances;
    mapping(address => mapping(address => uint256)) allowances;

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);

    constructor() {
        owner = msg.sender;
        balances[msg.sender] = totalSupply;
    }

    function balanceOf(address _owner) public view returns (uint256 balance) {
        return balances[_owner];
    }

    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(_to != address(0), "Invalid address");
        require(balances[msg.sender] >= _value, "ERC20: transfer amount exceeds balance");
        
        balances[msg.sender] -= _value;
        balances[_to] += _value;
        
        emit Transfer(msg.sender, _to, _value);
        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
        require(_from != address(0) && _to != address(0), "Invalid address");
        require(balances[_from] >= _value, "ERC20: transfer amount exceeds balance");
        require(allowances[_from][msg.sender] >= _value, "ERC20: transfer amount exceeds allowance");

        balances[_from] -= _value;
        balances[_to] += _value;
        allowances[_from][msg.sender] -= _value;

        emit Transfer(_from, _to, _value);
        return true;
    }

    function approve(address _spender, uint256 _value) public returns (bool success) {
        require(_spender != address(0), "Invalid address");

        allowances[msg.sender][_spender] = _value;
        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    function allowance(address _owner, address _spender) public view returns (uint256 remaining) {
        return allowances[_owner][_spender];
    }
}

```

# 2025-08-07

## 什么是erc721？

ERC721标准定义了智能合约接口，使得每一个代币都具有独特的标识符。这意味着每一个ERC721代币都是独一无二的，可以具有不同的价值和属性，与其他同一类型的代币不可互换。这种功能使得ERC721代币非常适合用于代表艺术品、收藏品、游戏物品等领域，因为它们可以精确地记录和证明每个物品的唯一性和所有权。

## 编写思路

### 接口

将要使用的接口有  IERC165   IERC721   IERC721Metadata   IERC721Receiver

这些接口的作用

#### IERC165

检查合约是否符合标准，也就是检查该有的函数有没有，检查函数名，代码如下

```
function supportsInterface(bytes4 interfaceId) external pure override returns(bool){
    return
        interfaceId == type(IERC721).interfaceId ||
        interfaceId == type(IERC165).interfaceId ||
        interfaceId == type(IERC721Metadata).interfaceId;
}
```

1. type：用于获取接口 的唯一标识符（`interfaceId`）。这个标识符是通过将接口中所有函数的签名进行哈希运算得到的

#### IERC721

`ERC721`标准的接口合约，规定了`ERC721`要实现的基本函数

```solidity
interface IERC721 is IERC165 {
    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
    event Approval(address indexed owner, address indexed approved, uint256 indexed tokenId);
    event ApprovalForAll(address indexed owner, address indexed operator, bool approved);

    function balanceOf(address owner) external view returns (uint256 balance);

    function ownerOf(uint256 tokenId) external view returns (address owner);

    function safeTransferFrom(address from,address to,uint256 tokenid,bytes calldata data) external;
    
    function safeTransferFrom(address from,address to,uint256 tokenid) external;

    function transferFrom(address from,address ot,uint256 tokenid) external;

    function approve(address to, uint256 tokenId) external;

    function setApprovalForAll(address operator, bool _approved) external;

    function getApproved(uint256 tokenId) external view returns (address operator);

    function isApprovedForAll(address owner, address operator) external view returns (bool);
}
```

1. calldata：一种数据位置，用于指定函数参数存储在外部调用的数据区。

   特点：1.不可修改

   ​		   2.相较于 `memory` 和 `storage`，`calldata` 的存取成本较低，因为数据直接从外部调用的数据区读取，而不需要额外的拷贝操作。

      		3. `calldata` 通常用于外部函数的参数，因为这些参数是直接从外部调用的数据传入的。

#### IERC721Metadata

实现了3个查询`metadata`元数据的常用函数

```
interface IERC721Metadata is IERC721 {
    function name() external view returns (string memory);

    function symbol() external view returns (string memory);

    function tokenURI(uint256 tokenId) external view returns (string memory);
}
```

#### IERC721Receiver

检查合约是否有能力转入转出 nft   防止nfr转入黑洞无法出来。

```
interface IERC721Receiver {
    function onERC721Received(address operator,address from,uint tokenid,bytes calldata data) 	external returns (bytes4);
}
```

它被利用，在ERC721主合约中的代码

```
// _checkOnERC721Received：函数，用于在 to 为合约的时候调用IERC721Receiver-onERC721Received, 以防 tokenId 被不小心转入黑洞。
    function _checkOnERC721Received(address from, address to, uint256 tokenId, bytes memory data) private {
        if (to.code.length > 0) {
            try IERC721Receiver(to).onERC721Received(msg.sender, from, tokenId, data) returns (bytes4 retval) {
                if (retval != IERC721Receiver.onERC721Received.selector) {
                    revert ERC721InvalidReceiver(to);
                }
            } catch (bytes memory reason) {
                if (reason.length == 0) {
                    revert ERC721InvalidReceiver(to);
                } else {
                    /// @solidity memory-safe-assembly
                    assembly {
                        revert(add(32, reason), mload(reason))
                    }
                }
            }
        }
    }

```

### 主合约

#### 变量声明

```
string public override name;
string public oberride symbol;
mapping(uint => address) private _owners; //通过id查询owner地址
mapping(address => uint) private _balances; //通过地址查询持仓数量
mapping(uint => address) private _tokenapprovals;//通过id查询授权地址
mapping(address => mapping(address => bool)) private _operatorapprovals; //owner地址到授权地址的映射
```

#### 针对IERC721的函数

1. balanceof：查询地址持有数量

   ```
   function balanceOf(address owner) external view override returns (uint) {
           require(owner != address(0), "owner = zero address");
           return _balances[owner];
       }
   ```

2. ownerof: 用id查询持有该nft的地址

   ```
   function ownerof(uint tokenid) public view override returns(address owner){
   	owner = _owners[tokenid];
   	require(owner!=address(0),"not exist");
   }
   ```

3. isApprovedForAll：检查owner地址的nft是否全部授权给operator地址

   ```
   function isApprovedForAll(address owner,address operator) external public view override returns(bool){
   	return _operatorapprovals[owner][operator];
   }
   ```

   1. 映射的默认值就是布尔值，如果映射不到就会返回  false

4. setApprovalForAll: 把owner地址的nft全部授权给operator地址

   ```
   function setApprovalForAll(address operator,bool approved) external override{
   	_operatorapprovals[msg.sender][operator] = approved;
   	emit ApprovalForAll(msg.sender, operator, approved);
   }
   ```

5. getApproved: 查询授权地址是否存在该nft

   ```
   function getApproved(uint tokenid,address operator) external view override returns(bool){
   	require(_owners[tokenid]!=address(0),"not exist");
   	return _tokenapprovals[tokenid];
   }
   ```

   这里直接先检查了owner地址有没有，没有的话那授权地址肯定也没有

6. _approve: 授权给新的地址，单个nft

   ```
   function _approve(address owner,address to,uint tokenid) private{
   	 _tokenApprovals[tokenId] = to;
   	  emit Approval(owner, to, tokenId);
   }
   ```

7. approve: 声明授权了新的地址

   ```
   function approve(address to,uint tokenid) external override{
   	address owner = _owners[tokenid];
   	require(msg.sender == owner || _operatorApprovals[owner][msg.sender],"无权限")
   	_approve(owner, to, tokenId);
   }
   ```

8. _isApprovedOrOwner：查询使用地址是否有某个nft的使用权限

   ```
   function _isApprovedOrOwner(address owner,address spender,uint tokenid) private view returns (bool) {
           return (spender == owner ||
               _tokenApprovals[tokenId] == spender ||
               _operatorApprovals[owner][spender]);
       }
   ```

9. _transfer：清楚当前授权，转ntf

   ```
   function _transfer(
           address owner,
           address from,
           address to,
           uint tokenId
       ) private {
           require(from == owner, "not owner");
           require(to != address(0), "transfer to the zero address");
   
           _approve(owner, address(0), tokenId);
   
           _balances[from] -= 1;
           _balances[to] += 1;
           _owners[tokenId] = to;
   
           emit Transfer(from, to, tokenId);
       }
   ```

   1. _approve(owner, address(0), tokenId);  将当前授权的地址清除，当一个代币从一个所有者转移到另一个所有者时，原来的授权地址就不再有效了，因为授权是基于旧的所有权的。为确保安全和逻辑上的一致性，需要清除旧的授权

10. _safeTransfer：检查接收nft的合约是否有能力处理nft

    ```
     address owner,
            address from,
            address to,
            uint tokenId,
            bytes memory _data
        ) private {
            _transfer(owner, from, to, tokenId);
            require(_checkOnERC721Received(from, to, tokenId, _data), "not ERC721Receiver");
        }
    ```

11. safeTransferFrom：安全转账

    ```
     function safeTransferFrom(
            address from,
            address to,
            uint tokenId,
            bytes memory _data
        ) public override {
            address owner = ownerOf(tokenId);
            require(
                _isApprovedOrOwner(owner, msg.sender, tokenId),
                "not owner nor approved"
            );
            _safeTransfer(owner, from, to, tokenId, _data);
        }
    ```

12. mint：铸造函数

    ```
     function _mint(address to, uint tokenId) internal virtual {
            require(to != address(0), "mint to zero address");
            require(_owners[tokenId] == address(0), "token already minted");
    
            _balances[to] += 1;
            _owners[tokenId] = to;
    
            emit Transfer(address(0), to, tokenId);
        }
    ```

    1. 所有的映射（`mapping`）在初始化时，其所有的键值对默认都是其值类型的默认值。对于地址类型的值，默认值就是 `address(0)`。

13. tokenurl：返回特定 `tokenId` 对应的 NFT 元数据的 URI

    ```
    function tokenURI(uint256 tokenId) public view virtual override returns (string memory) {
            require(_owners[tokenId] != address(0), "Token Not Exist");
    
            string memory baseURI = _baseURI();
            return bytes(baseURI).length > 0 ? string(abi.encodePacked(baseURI, tokenId.toString())) : "";
        }
    
    
    function _baseURI() internal view virtual returns (string memory) {
            return "";
        }
    }
    ```

    **`bytes(baseURI).length > 0`**

    这一部分检查 `baseURI` 是否非空。`baseURI` 是一个字符串，但是 Solidity 不允许直接检查字符串是否为空。因此，我们将 `baseURI` 转换为字节数组，然后检查其长度是否大于 0。如果长度大于 0，表示 `baseURI` 存在。

    **三元运算符 `? :`**

    这是一个条件运算符，根据条件的真伪选择返回的值。如果 `bytes(baseURI).length > 0` 为真，则执行 `string(abi.encodePacked(baseURI, tokenId.toString()))`；否则返回空字符串 `""`。

    **`tokenId.toString()`**

    这一部分将 `tokenId` 转换为字符串。`toString` 是一个常见的库函数，将 `uint256` 类型的 `tokenId` 转换为字符串表示。

    **`abi.encodePacked(baseURI, tokenId.toString())`**

    `abi.encodePacked` 是一个变长参数函数，将多个参数打包成一个字节数组。这里，它将 `baseURI` 和 `tokenId` 的字符串表示连接起来。

    **`string(abi.encodePacked(...))`**

    将 `abi.encodePacked` 打包的字节数组转换回字符串。这一步将字节数组的拼接结果转换为字符串。

     

    假设 `baseURI` 是 `"https://example.com/metadata/"`，`tokenId` 是 `123`，那么：

    1. `baseURI` 非空，所以 `bytes(baseURI).length > 0` 为真。
    2. `tokenId.toString()` 将 `123` 转换为 `"123"`。
    3. `abi.encodePacked(baseURI, tokenId.toString())` 生成一个字节数组，内容是 `"https://example.com/metadata/123"`。
    4. `string(abi.encodePacked(...))` 将这个字节数组转换为字符串，结果是 `"https://example.com/metadata/123"`。

## 其他须知

1. 如果你访问一个映射中不存在的键，它会返回该映射值类型的默认值。对于布尔类型，默认值是 `false`。因此，如果你查询 `_operatorApprovals[owner][operator]`，如果 `owner` 从未将 `operator` 授权给他们，那么该查询将返回 `false`，即默认值。

2. 检查全部授权给operator了

3. mapping(address => bool) private _isMember;

   function addMember(address member) external {
       _isMember[member] = true; // 将地址标记为成员
   }

   function isMember(address user) external view returns (bool) {
       return _isMember[user]; // 查询地址是否是成员
   }  

   它等于是一个过程，，，就是owner先映射operator，，，之后看operator是否有这个值，，没有返回false，，，

4. function setApprovalForAll(address operator, bool approved) external override {
       _operatorApprovals[msg.sender][operator] = approved;
       emit ApprovalForAll(msg.sender, operator, approved);
   }

   其实这个代码跟上面的是一样的，只是更加明显一个步骤

   上面那个代码是批量授权，，如果没有映射到

5. ```
   function _isapproved(address owner,address spender,uint tokenid) private view returns (bool){
       return (spender ==owner || _tokenApprovals[tokenid]==spender || _operatorApprovals[owner][spender]);
   }
   
   ```

   这种检验的，一般都是private  隐藏的检测好了

6. 在ERC-721标准中，`transferFrom`函数用于将NFT从一个地址转移到另一个地址。然而，这个基本的转移操作被认为是“非安全的”，因为它不检查接收方地址是否能够接收NFT。如果NFT被发送到一个无法处理它的合约地址，那么这个NFT可能会被永久锁定，因为没有办法从那个合约中取回它。

   为了解决这个问题，ERC-721标准引入了一个名为`safeTransferFrom`的函数。这个函数在执行转移操作之前，会检查目标地址是否是一个合约。如果是，合约必须实现`IERC721Receiver`接口，并且必须能够正确响应`onERC721Received`函数调用。这是通过调用目标合约的`onERC721Received`函数并验证返回值来实现的。如果目标地址是一个普通的外部账户（EOA），则不需要这个检查。

   这是`safeTransferFrom`的基本流程：

   1. 检查调用者是否有权转移NFT。
   2. 检查目标地址是否不是零地址（以防止烧毁代币）。
   3. 如果目标地址是合约，则调用它的`onERC721Received`函数。
   4. 如果目标合约正确地实现了`onERC721Received`，它应该返回一个特定的魔术值。
   5. 如果接收到正确的魔术值，或者目标地址是EOA，则执行转移。
   6. 更新所有相关的状态变量（如所有者和余额映射）。
   7. 触发一个`Transfer`事件。

   这个过程确保了NFT不会意外地被发送到无法处理它的合约，从而增加了转移操作的安全性。因此，当你在智能合约中编程时，建议使用`safeTransferFrom`而不是`transferFrom`，除非你完全确定接收者可以处理NFT，或者你正在执行一个需要更低级别控制的特殊操作。

   transferFrom，非安全转账，不建议使用。调用_transfer函数 解释一下为什么是非安全的

7. 在ERC-721标准中，特别是在`transferFrom`函数中，`from` 参数代表了NFT当前所有者的地址。这个参数用来指示哪个地址的NFT将被转移。当你调用`transferFrom`时，你需要指定三个参数：

   1. `from`: NFT当前所有者的地址。
   2. `to`: 将接收NFT的地址。
   3. `tokenId`: 要转移的NFT的唯一标识符。

   在这个上下文中，“from = owner”意味着“从当前所有者的地址转移NFT”。这是一种安全措施，确保只有NFT的实际所有者（或被授权的代理）才能发起转移操作。

   这样做的原因包括：

   - **所有权验证**：确保只有NFT的真实所有者才能决定如何转移它。
   - **权限控制**：防止未经授权的账户转移不属于它们的NFT。
   - **合规性**：符合ERC-721标准，该标准规定了如何安全地转移NFT。

   在调用`transferFrom`之前，通常会有一些检查，以确保调用者有权进行转移。这可能是因为调用者是NFT的当前所有者，或者是当前所有者通过`approve`或`setApprovalForAll`明确授权的代理。

   如果你看到代码中的`from`参数被设置为NFT的所有者，这是正常和预期的行为，因为这是NFT转移操作的一个基本要求。

   那为什么要from = owner

8. _approve(owner, address(0), tokenId);

   在您的 ERC-721 合约实现中，`_approve(owner, address(0), tokenId);` 这一行代码的作用是清除对 `tokenId` 的现有授权。这在转移 NFT 的过程中是一个重要的步骤，确保 NFT 在转移后不再保留任何之前的授权。这是为了防止授权地址在 NFT 被转移到新所有者后仍然能够转移该 NFT。

   具体来说，让我们来详细解释一下它的作用：

   ### 授权清除操作

   #### 场景

   假设 NFT 目前属于 `Alice`，并且她已经授权 `Bob` 可以转移这个 NFT（`tokenId`）。授权关系已经在 `_tokenApprovals` 映射中记录，即 `_tokenApprovals[tokenId] = Bob`。

   #### 转移过程

   1. 当 `Bob` 或 `Alice` 想要将这个 NFT 转移给 `Charlie` 时，他们会调用 `transferFrom` 或 `safeTransferFrom` 函数。
   2. 在这两个函数内部，会调用 `_transfer` 函数来执行实际的转移操作

9. ### 为什么检验是不是智能合约

   检验目标地址是否是智能合约，是为了确保在将 ERC721 代币转移到智能合约地址时，目标智能合约能正确处理接收到的代币。如果目标地址是智能合约但没有实现必要的接口方法，代币可能会被锁定在该合约中，无法再被转移或使用。这个检查的目的在于提高安全性，防止代币意外丢失。

10. 我想知道传递的data是什么，举个例子

   ### 携带附加信息（如操作指令）

   假设我们有一个支持特殊操作的智能合约，当接收到 ERC721 代币时需要一些指令数据。

   ```
solidity复制代码bytes memory data = abi.encodeWithSignature("specialFunction(uint256)", 123);
// 用户 A 将 tokenId 为 1 的代币安全转移到合约 C，同时传递一些指令数据
erc721Contract.safeTransferFrom(userA, contractC, 1, data);
   ```

   在这个例子中，传递的 `data` 包含了编码后的调用信息，合约 C 可以在 `onERC721Received` 方法中解析并执行这些指令。

   ### 携带消息或元数据

   有时，发送者可能希望传递一些描述性数据，如消息或元数据。

   ```solidity
solidity复制代码string memory message = "This NFT is a gift";
bytes memory data = abi.encode(message);
// 用户 A 将 tokenId 为 1 的代币安全转移到用户 B，同时传递一条消息
erc721Contract.safeTransferFrom(userA, userB, 1, data);
   ```

11. `abi.encodeWithSignature("specialFunction(uint256)", 123)` 是 Solidity 中的一个函数调用，用于将函数调用编码为 ABI 编码格式的字节数组，以便在以太坊网络上进行传输和解析。

    具体来说，`abi.encodeWithSignature` 函数接受函数签名和参数，并返回一个字节数组，其中包含了函数调用的 ABI 编码。

    在这个例子中，函数签名是 `specialFunction(uint256)`，它表示一个名为 `specialFunction` 的函数，接受一个 `uint256` 类型的参数。参数是 `123`，表示要传递给 `specialFunction` 函数的参数值。

    因此，`abi.encodeWithSignature("specialFunction(uint256)", 123)` 会将函数调用 `specialFunction(123)` 编码为 ABI 格式的字节数组，以便在以太坊网络上传输和解析。

11. 是的，您理解得完全正确。

    当您在Solidity中将一个地址转换为一个接口类型并调用该接口的函数时，您实际上是在调用与该地址关联的合约中实现了该接口的函数。您自己的合约不包含这些函数的实现，它只是定义了如何与实现了这些函数的外部合约交互。

    这里有一个简单的例子来说明这一点：

    假设我们有一个ERC20代币的接口：

    ```solidity
    interface IERC20 {
        function transfer(address recipient, uint256 amount) external returns (bool);
        // ... 其他函数定义 ...
    }
    ```

    然后，您有另一个合约，它想要与遵守ERC20标准的代币合约互动：

    ```solidity
    contract MyContract {
        function transferTokens(address tokenContract, address recipient, uint256 amount) public {
            IERC20 token = IERC20(tokenContract);
            require(token.transfer(recipient, amount), "Transfer failed");
        }
    }
    ```

    在上面的`MyContract`合约中，`transferTokens`函数接受一个代币合约的地址、一个接收者的地址和一个数量。它首先将代币合约的地址转换为`IERC20`接口。然后，它调用`transfer`函数。在这个调用中，我们期望`tokenContract`地址指向的合约已经实现了`IERC20`接口，也就是说，它有一个`transfer`函数，该函数正是我们在接口中定义的那样。

    因此，当`token.transfer(recipient, amount)`被调用时，实际上调用的是`tokenContract`地址上的合约中的`transfer`函数实现。如果该地址上的合约没有正确实现`transfer`函数，调用将会失败。

11. `size := extcodesize(account)` - 在汇编块内，这行代码实际上是执行了 `extcodesize` 操作码。操作码 `extcodesize` 接受一个地址（在这个例子中是变量 `account`）并返回该地址上的合约代码大小。在Solidity中，合约地址的代码大小是零，意味着该地址是一个普通的外部拥有账户（Externally Owned Account, EOA），而不是一个合约。如果代码大小大于零，则意味着地址是一个合约。赋值操作使用 `:=` 来将 `extcodesize(account)` 的结果赋给变量 `size`。

11. `return size > 0;` - 最后，这行代码检查 `size` 是否大于零。如果是，这意味着 `account` 是一个合约地址；如果不是，那么 `account` 是一个普通的钱包地址。

这个检查通常用于确定一个地址是否是合约地址，这在智能合约编程中是一个常见的需求，因为合约地址和外部拥有账户（人类用户的钱包地址）在某些情况下会有不同的处理方式

# 2025-08-04

成功安装了常用的工具，foundry和hardhat，收集了一些常用的命令

#Hardhat

## 用hardhat创建项目

```
yarn init   初始化项目安装依赖

yarn add --dev hardhat   安装 hardhat 工具，将 hardhat 包作为开发依赖项添加到你的项目中。

yarn hardhat
```
## 测试命令

```js
yarn hardhat compile //先进行编译，方便再写测试脚本的时候，hardhat可以直接找到目标文件

yarn hardhat  //加载下载的所有插件，查看使用

yarn hardhat run scripts/deploy.js    运行测试脚本

yarn hardhat run scripts/deploy.js --network hardhat  这里指定的是 hardhat本地网络，可以切换
								//如果没有添加任何信息，默认  defaultNetwork: "hardhat"
yarn add --dev @nomiclabs/hardhat-etherscan   //安装代码验证插件

```
#Foundry

## foundry安装

安装foundry框架推荐使用 Ubuntu系统，用虚拟机下载其iso文件即可，Windows系统会出很多问题，如果坚持要用Windows系统，可以下载wls2WSL2 (Windows Subsystem for Linux 2）

安装foundry的时候，如果存在访问不到情况，可能是要访问外网，操作可以参照这篇文章     https://blog.xzr.moe/archives/124/

之后使用该命令下载foundry

```
curl -L https://foundry.paradigm.xyz | bash
```

再通过这个命令来运行

```
foundryup
```

如果存在环境问题，在Ubuntu系统里面可以非常方便，个人之前在Windows系统安装非常麻烦，会出现各种bug，还有文件权限之类的问题，而且无法使用上面这个方便命令，好像是需要git才行。

第一步：编译

创建项目工程，如果是创建初始项目的话

```
forge init <work name>
```

init命令会创建项目目录，同时安装好 forge-std 库

或者手动安装库

```
forge install forge/forge-std
```

这里会遇到一个问题，就是在安装forge-std 库这一步，会让你设置好github的用户身份认证，用一下两个命令，信息填写就是你GitHub用户信息


## 使用命令

编译合约

```
forge compile
```

显示虚拟用户

```
anvil
```



## 识别测试脚本

```solidity
import {Script, console} from "forge-std/Script.sol"

CounterScript is Script  //合约继承 Script 
```

需要导入这个文件。因为与hardhat不同，脚本语言使用的是solidity。

# 2025.07.31


<!-- Content_END -->
