---
timezone: UTC+8
---

# 杨尽能

**GitHub ID:** adureychloe

**Telegram:** @JOJOyang

## Self-introduction

毕业一年的职场新人，目前在昆明一个银行技术岗，对国企氛围不喜欢，想要转型。对web3基本概念有了解和投资经验，刚开始学相关技术，想要研究方向是智能合约、ai和链上数据分析相关，目标是找到远程工作实现地理自由和时间自由。

## Notes

<!-- Content_START -->

# 2025-08-28
<!-- DAILY_CHECKIN_2025-08-28_START -->
## **Storage和Memory**

**_Storage_** 变量是指永久存储在区块链中的变量。 **_Memory_** 变量则是临时的，当外部函数对某合约调用完成时，内存型变量即被移除。 你可以把它想象成存储在你电脑的硬盘或是RAM中数据的关系。

大多数时候你都用不到这些关键字，默认情况下 Solidity 会自动处理它们。 状态变量（在函数之外声明的变量）默认为“存储”形式，并永久写入区块链；而在函数内部声明的变量是“内存”型的，它们函数调用结束后消失。

然而也有一些情况下，你需要手动声明存储类型，主要用于处理函数内的 **\_ 结构体 \_** 和 **\_ 数组 \_** 时：

```
contract SandwichFactory {
  struct Sandwich {
    string name;
    string status;
  }
​
  Sandwich[] sandwiches;
​
  function eatSandwich(uint _index) public {
    // Sandwich mySandwich = sandwiches[_index];
​
    // ^ 看上去很直接，不过 Solidity 将会给出警告
    // 告诉你应该明确在这里定义 `storage` 或者 `memory`。
​
    // 所以你应该明确定义 `storage`:
    Sandwich storage mySandwich = sandwiches[_index];
    // ...这样 `mySandwich` 是指向 `sandwiches[_index]`的指针
    // 在存储里，另外...
    mySandwich.status = "Eaten!";
    // ...这将永久把 `sandwiches[_index]` 变为区块链上的存储
​
    // 如果你只想要一个副本，可以使用`memory`:
    Sandwich memory anotherSandwich = sandwiches[_index + 1];
    // ...这样 `anotherSandwich` 就仅仅是一个内存里的副本了
    // 另外
    anotherSandwich.status = "Eaten!";
    // ...将仅仅修改临时变量，对 `sandwiches[_index + 1]` 没有任何影响
    // 不过你可以这样做:
    sandwiches[_index + 1] = anotherSandwich;
    // ...如果你想把副本的改动保存回区块链存储
  }
}
```

是时候给我们的僵尸增加“猎食”和“繁殖”功能了！

当一个僵尸猎食其他生物体时，它自身的DNA将与猎物生物的DNA结合在一起，形成一个新的僵尸DNA。

1.  创建一个名为 `feedAndMultiply` 的函数。 使用两个参数：`_zombieId`（ `uint`类型 ）和`_targetDna` （也是 `uint` 类型）。 设置属性为 `public` 的。
    
2.  我们不希望别人用我们的僵尸去捕猎。 首先，我们确保对自己僵尸的所有权。 通过添加一个`require` 语句来确保 `msg.sender` 只能是这个僵尸的主人（类似于我们在 `createRandomZombie` 函数中做过的那样）。
    

1.  为了获取这个僵尸的DNA，我们的函数需要声明一个名为 `myZombie` 数据类型为`Zombie`的本地变量（这是一个 `storage` 型的指针）。 将其值设定为在 `zombies` 数组中索引为`_zombieId`所指向的值。
    

```
pragma solidity ^0.4.19;
​
import "./zombiefactory.sol";
​
contract ZombieFeeding is ZombieFactory {
​
  // Start here
  function feedAndMultiply(uint _zombieId, uint _targetDna) public {
      require(msg.sender == zombieToOwner[_zombieId]);
      Zombie storage myZombie = zombies[_zombieId];
  }
​
}
​
```

1.  首先我们确保 `_targetDna` 不长于16位。要做到这一点，我们可以设置 `_targetDna` 为 `_targetDna ％ dnaModulus` ，并且只取其最后16位数字。
    
2.  接下来为我们的函数声明一个名叫 `newDna` 的 `uint`类型的变量，并将其值设置为 `myZombie`的 DNA 和 `_targetDna` 的平均值（如上例所示）。
    

> 注意：您可以用 `myZombie.name` 或 `myZombie.dna` 访问 `myZombie` 的属性。

1.  一旦我们计算出新的DNA，再调用 `_createZombie` 就可以生成新的僵尸了。如果你忘了调用这个函数所需要的参数，可以查看 `zombiefactory.sol` 选项卡。请注意，需要先给它命名，所以现在我们把新的僵尸的名字设为`NoName` - 我们回头可以编写一个函数来更改僵尸的名字。
    

```
pragma solidity ^0.4.19;
​
import "./zombiefactory.sol";
​
contract ZombieFeeding is ZombieFactory {
​
  function feedAndMultiply(uint _zombieId, uint _targetDna) public {
    require(msg.sender == zombieToOwner[_zombieId]);
    Zombie storage myZombie = zombies[_zombieId];
    // start here
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    _createZombie("NoName",newDna);
  }
​
}
​
```

## **internal 和 external**

除 `public` 和 `private` 属性之外，Solidity 还使用了另外两个描述函数可见性的修饰词：`internal`（内部） 和 `external`（外部）。

`internal` 和 `private` 类似，不过， 如果某个合约继承自其父合约，这个合约即可以访问父合约中定义的“内部”函数。

`external` 与`public` 类似，只不过这些函数只能在合约之外调用 - 它们不能被合约内的其他函数调用。稍后我们将讨论什么时候使用 `external` 和 `public`。

声明函数 `internal` 或 `external` 类型的语法，与声明 `private` 和 `public`类 型相同：

```
contract Sandwich {
  uint private sandwichesEaten = 0;
​
  function eat() internal {
    sandwichesEaten++;
  }
}
​
contract BLT is Sandwich {
  uint private baconSandwichesEaten = 0;
​
  function eatWithBacon() public returns (string) {
    baconSandwichesEaten++;
    // 因为eat() 是internal 的，所以我们能在这里调用
    eat();
  }
}
```

1.  将 `_createZombie()` 函数的属性从 `private` 改为 `internal` ， 使得其他的合约也能访问到它。
    

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    event NewZombie(uint zombieId, string name, uint dna);
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;
​
    // 在这里修改函数的功能
    function _createZombie(string _name, uint _dna) internal {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }
​
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
​
    function createRandomZombie(string _name) public {
        require(ownerZombieCount[msg.sender] == 0);
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
​
}
​
```

## **interface（接口）**

先举一个简单的栗子。 假设在区块链上有这么一个合约：

```
contract LuckyNumber {
  mapping(address => uint) numbers;
​
  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }
​
  function getNum(address _myAddress) public view returns (uint) {
    return numbers[_myAddress];
  }
}
```

这是个很简单的合约，您可以用它存储自己的幸运号码，并将其与您的以太坊地址关联。 这样其他人就可以通过您的地址查找您的幸运号码了。

现在假设我们有一个外部合约，使用 `getNum` 函数可读取其中的数据。

首先，我们定义 `LuckyNumber` 合约的 **_interface_** ：

```
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
```

请注意，这个过程虽然看起来像在定义一个合约，但其实内里不同：

首先，我们只声明了要与之交互的函数 —— 在本例中为 `getNum` —— 在其中我们没有使用到任何其他的函数或状态变量。

其次，我们并没有使用大括号（`{` 和 `}`）定义函数体，我们单单用分号（`;`）结束了函数声明。这使它看起来像一个合约框架。

编译器就是靠这些特征认出它是一个接口的。

在我们的 app 代码中使用这个接口，合约就知道其他合约的函数是怎样的，应该如何调用，以及可期待什么类型的返回值。

CryptoKitties 的源代码中存在一个名为 `getKitty`的函数，它返回所有的加密猫的数据，包括它的“基因”（我们的僵尸游戏要用它生成新的僵尸）。

该函数如下所示：

```
function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
) {
    Kitty storage kit = kitties[_id];
​
    // if this variable is 0 then it's not gestating
    isGestating = (kit.siringWithId != 0);
    isReady = (kit.cooldownEndBlock <= block.number);
    cooldownIndex = uint256(kit.cooldownIndex);
    nextActionAt = uint256(kit.cooldownEndBlock);
    siringWithId = uint256(kit.siringWithId);
    birthTime = uint256(kit.birthTime);
    matronId = uint256(kit.matronId);
    sireId = uint256(kit.sireId);
    generation = uint256(kit.generation);
    genes = kit.genes;
}
```

这个函数看起来跟我们习惯的函数不太一样。 它竟然返回了...一堆不同的值！ 如果用过 JavaScript 之类的编程语言，一定会感到奇怪 —— 在 Solidity中，可以让一个函数返回多个值。

现在我们知道这个函数长什么样的了，就可以用它来创建一个接口：

1.定义一个名为 `KittyInterface` 的接口。 请注意，因为我们使用了 `contract` 关键字， 这过程看起来就像创建一个新的合约一样。

2.在interface里定义了 `getKitty` 函数（不过是复制/粘贴上面的函数，但在 `returns` 语句之后用分号，而不是大括号内的所有内容。

```
pragma solidity ^0.4.19;
​
import "./zombiefactory.sol";
​
// Create KittyInterface here
contract KittyInterface{
    function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
    );
}
​
contract ZombieFeeding is ZombieFactory {
​
  function feedAndMultiply(uint _zombieId, uint _targetDna) public {
    require(msg.sender == zombieToOwner[_zombieId]);
    Zombie storage myZombie = zombies[_zombieId];
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    _createZombie("NoName", newDna);
  }
​
}
​
```
<!-- DAILY_CHECKIN_2025-08-28_END -->


# 2025-08-27
<!-- DAILY_CHECKIN_2025-08-27_START -->
继续学习cryptozombie

## **地址（Address）**

指定“地址”作为僵尸主人的 ID。当用户通过与我们的应用程序交互来创建新的僵尸时，新僵尸的所有权被设置到调用者的以太坊地址下。

## **Mapping（映射）**

映射是这样定义的：

```
//对于金融应用程序，将用户的余额保存在一个 uint类型的变量中：
mapping (address => uint) public accountBalance;
//或者可以用来通过userId 存储/查找的用户名
mapping (uint => string) userIdToName;
```

映射本质上是存储和查找数据所用的键-值对。在第一个例子中，键是一个 `address`，值是一个 `uint`，在第二个例子中，键是一个`uint`，值是一个 `string`。

为了存储僵尸的所有权，我们会使用到两个映射：一个记录僵尸拥有者的地址，另一个记录某地址所拥有僵尸的数量。

1.创建一个叫做 `zombieToOwner` 的映射。其键是一个`uint`（我们将根据它的 id 存储和查找僵尸），值为 `address`。映射属性为`public`。

2.创建一个名为 `ownerZombieCount` 的映射，其中键是 `address`，值是 `uint`。

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    event NewZombie(uint zombieId, string name, uint dna);
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    // 在这里定义映射
    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;
​
    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        NewZombie(id, _name, _dna);
    }
​
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
​
    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
​
}
​
```

## **Msg.sender**

在 Solidity 中，有一些全局变量可以被所有函数调用。 其中一个就是 `msg.sender`，它指的是当前调用者（或智能合约）的 `address`。

> 注意：在 Solidity 中，功能执行始终需要从外部调用者开始。 一个合约只会在区块链上什么也不做，除非有人调用其中的函数。所以 `msg.sender`总是存在的。

以下是使用 `msg.sender` 来更新 `mapping` 的例子：

```
mapping (address => uint) favoriteNumber;
​
function setMyNumber(uint _myNumber) public {
  // 更新我们的 `favoriteNumber` 映射来将 `_myNumber`存储在 `msg.sender`名下
  favoriteNumber[msg.sender] = _myNumber;
  // 存储数据至映射的方法和将数据存储在数组相似
}
​
function whatIsMyNumber() public view returns (uint) {
  // 拿到存储在调用者地址名下的值
  // 若调用者还没调用 setMyNumber， 则值为 `0`
  return favoriteNumber[msg.sender];
}
```

在这个例子中，任何人都可以调用 `setMyNumber` 在我们的合约中存下一个 `uint` 并且与他们的地址相绑定。 然后，他们调用 `whatIsMyNumber` 就会返回他们存储的 `uint`。

使用 `msg.sender` 很安全，因为它具有以太坊区块链的安全保障 —— 除非窃取与以太坊地址相关联的私钥，否则是没有办法修改其他人的数据的。

我们来修改第1课的 `_createZombie` 方法，将僵尸分配给函数调用者吧。

1.  首先，在得到新的僵尸 `id` 后，更新 `zombieToOwner` 映射，在 `id` 下面存入 `msg.sender`。
    
2.  然后，我们为这个 `msg.sender` 名下的 `ownerZombieCount` 加 1。
    

跟在 JavaScript 中一样， 在 Solidity 中你也可以用 `++` 使 `uint` 递增。

```
uint number = 0;
number++;
// `number` 现在是 `1`了
```

修改两行代码即可。

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    event NewZombie(uint zombieId, string name, uint dna);
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;
​
    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        // 从这里开始
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }
​
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
​
    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
​
}
​
```

## **Require**

`require`使得函数在执行过程中，当不满足某些条件时抛出错误，并停止执行：

```
function sayHiToVitalik(string _name) public returns (string) {
  // 比较 _name 是否等于 "Vitalik". 如果不成立，抛出异常并终止程序
  // (敲黑板: Solidity 并不支持原生的字符串比较, 我们只能通过比较
  // 两字符串的 keccak256 哈希值来进行判断)
  require(keccak256(_name) == keccak256("Vitalik"));
  // 如果返回 true, 运行如下语句
  return "Hi!";
}
```

如果你这样调用函数 `sayHiToVitalik（“Vitalik”）` ,它会返回“Hi！”。而如果调用的时候使用了其他参数，它则会抛出错误并停止执行。

因此，在调用一个函数之前，用 `require` 验证前置条件是非常有必要的。

在我们的僵尸游戏中，我们不希望用户通过反复调用 `createRandomZombie` 来給他们的军队创建无限多个僵尸 —— 这将使得游戏非常无聊。

我们使用了 `require` 来确保这个函数只有在每个用户第一次调用它的时候执行，用以创建初始僵尸。

1.  在 `createRandomZombie` 的前面放置 `require` 语句。 使得函数先检查 `ownerZombieCount [msg.sender]` 的值为 `0` ，不然就抛出一个错误。
    

> 注意：在 Solidity 中，关键词放置的顺序并不重要

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    event NewZombie(uint zombieId, string name, uint dna);
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;
​
    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }
​
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
​
    function createRandomZombie(string _name) public {
        // start here
        require(ownerZombieCount[msg.sender]==0);
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
​
}
​
```

## **继承（Inheritance）**

```
contract Doge {
  function catchphrase() public returns (string) {
    return "So Wow CryptoDoge";
  }
}
​
contract BabyDoge is Doge {
  function anotherCatchphrase() public returns (string) {
    return "Such Moon BabyDoge";
  }
}
```

由于 `BabyDoge` 是从 `Doge` 那里 **_inherits_** （继承)过来的。 这意味着当你编译和部署了 `BabyDoge`，它将可以访问 `catchphrase()` 和 `anotherCatchphrase()`和其他我们在 `Doge` 中定义的其他公共函数。

这可以用于逻辑继承（比如表达子类的时候，`Cat` 是一种 `Animal`）。 但也可以简单地将类似的逻辑组合到不同的合约中以组织代码。

在接下来的章节中，我们将要为僵尸实现各种功能，让它可以“猎食”和“繁殖”。 通过将这些运算放到父类 `ZombieFactory` 中，使得所有 `ZombieFactory` 的继承者合约都可以使用这些方法。

1.  在 `ZombieFactory` 下创建一个叫 `ZombieFeeding` 的合约，它是继承自 \`ZombieFactory 合约的。
    

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    event NewZombie(uint zombieId, string name, uint dna);
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;
​
    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }
​
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
​
    function createRandomZombie(string _name) public {
        require(ownerZombieCount[msg.sender] == 0);
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
​
}
​
// Start here
contract ZombieFeeding is ZombieFactory {
    
}
​
```

## **引入（import）**

在 Solidity 中，当你有多个文件并且想把一个文件导入另一个文件时，可以使用 `import` 语句：

```
import "./someothercontract.sol";
​
contract newContract is SomeOtherContract {
​
}
```

这样当我们在合约（contract）目录下有一个名为 `someothercontract.sol` 的文件（ `./` 就是同一目录的意思），它就会被编译器导入。

## **Storage和Memory**

**_Storage_** 变量是指永久存储在区块链中的变量。 **_Memory_** 变量则是临时的，当外部函数对某合约调用完成时，内存型变量即被移除。 你可以把它想象成存储在你电脑的硬盘或是RAM中数据的关系。

大多数时候你都用不到这些关键字，默认情况下 Solidity 会自动处理它们。 状态变量（在函数之外声明的变量）默认为“存储”形式，并永久写入区块链；而在函数内部声明的变量是“内存”型的，它们函数调用结束后消失。

然而也有一些情况下，你需要手动声明存储类型，主要用于处理函数内的 **_结构体_** 和 **_数组_** 时：

```
contract SandwichFactory {
  struct Sandwich {
    string name;
    string status;
  }
​
  Sandwich[] sandwiches;
​
  function eatSandwich(uint _index) public {
    // Sandwich mySandwich = sandwiches[_index];
​
    // ^ 看上去很直接，不过 Solidity 将会给出警告
    // 告诉你应该明确在这里定义 `storage` 或者 `memory`。
​
    // 所以你应该明确定义 `storage`:
    Sandwich storage mySandwich = sandwiches[_index];
    // ...这样 `mySandwich` 是指向 `sandwiches[_index]`的指针
    // 在存储里，另外...
    mySandwich.status = "Eaten!";
    // ...这将永久把 `sandwiches[_index]` 变为区块链上的存储
​
    // 如果你只想要一个副本，可以使用`memory`:
    Sandwich memory anotherSandwich = sandwiches[_index + 1];
    // ...这样 `anotherSandwich` 就仅仅是一个内存里的副本了
    // 另外
    anotherSandwich.status = "Eaten!";
    // ...将仅仅修改临时变量，对 `sandwiches[_index + 1]` 没有任何影响
    // 不过你可以这样做:
    sandwiches[_index + 1] = anotherSandwich;
    // ...如果你想把副本的改动保存回区块链存储
  }
}
```

是时候给我们的僵尸增加“猎食”和“繁殖”功能了！

当一个僵尸猎食其他生物体时，它自身的DNA将与猎物生物的DNA结合在一起，形成一个新的僵尸DNA。

1.  创建一个名为 `feedAndMultiply` 的函数。 使用两个参数：`_zombieId`（ `uint`类型 ）和`_targetDna` （也是 `uint` 类型）。 设置属性为 `public` 的。
    
2.  我们不希望别人用我们的僵尸去捕猎。 首先，我们确保对自己僵尸的所有权。 通过添加一个`require` 语句来确保 `msg.sender` 只能是这个僵尸的主人（类似于我们在 `createRandomZombie` 函数中做过的那样）。
    

1.  为了获取这个僵尸的DNA，我们的函数需要声明一个名为 `myZombie` 数据类型为`Zombie`的本地变量（这是一个 `storage` 型的指针）。 将其值设定为在 `zombies` 数组中索引为`_zombieId`所指向的值。
    

```
pragma solidity ^0.4.19;
​
import "./zombiefactory.sol";
​
contract ZombieFeeding is ZombieFactory {
​
  // Start here
  function feedAndMultiply(uint _zombieId, uint _targetDna) public {
      require(msg.sender == zombieToOwner[_zombieId]);
      Zombie storage myZombie = zombies[_zombieId];
  }
​
}
​
```
<!-- DAILY_CHECKIN_2025-08-27_END -->


# 2025-08-25
<!-- DAILY_CHECKIN_2025-08-25_START -->
## **返回值和修饰符**

```
string greeting = "What's up dog";
​
function sayHello() public returns (string) {
  return greeting;
}
```

Solidity 里，函数的定义里可包含返回值的数据类型(如本例中 `string`)。

上面的函数实际上没有改变 Solidity 里的状态，即，它没有改变任何值或者写任何东西。

这种情况下我们可以把函数定义为 **view**, 意味着它只能读取数据不能更改数据:

```
function sayHello() public view returns (string) {
```

Solidity 还支持 **pure** 函数, 表明这个函数甚至都不访问应用里的数据，例如：

```
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

这个函数甚至都不读取应用里的状态 — 它的返回值完全取决于它的输入参数，在这种情况下我们把函数定义为 **pure**.

定义一个函数，根据字符串随机生成DNA：

```
pragma solidity ^0.4.19;
 
contract ZombieFactory {
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    }
​
    // 这里开始
    function _generateRandomDna(string _str) private view returns (uint) {
​
    }
​
}
​
```

## **Keccak256 和类型转换**

Ethereum内部有一个散列函数 `keccak256` ，用了SHA3版本。把一个字符串转换为一个256位的16进制数字。

```
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5
keccak256("aaaab");
//b1f078126895a1424524de5321b339ab00408010b7cf0e6ed451514981e58aa9
keccak256("aaaac");
```

> 注：这种产生随机数的方法不安全

```
uint8 a = 5;
uint b = 6;
// 将会抛出错误，因为 a * b 返回 uint, 而不是 uint8:
uint8 c = a * b;
// 我们需要将 b 转换为 uint8:
uint8 c = a * uint8(b);
```

上面, `a * b` 返回类型是 `uint`, 但是当我们尝试用 `uint8` 类型接收时, 就会造成潜在的错误。如果把它的数据类型转换为 `uint8`, 就可以了，编译器也不会出错。

给 `_generateRandomDna` 函数添加代码! 它应该完成如下功能:

1.  第一行代码取 `_str` 的 `keccak256` 散列值生成一个伪随机十六进制数，类型转换为 `uint`, 最后保存在类型为 `uint` 名为 `rand` 的变量中。
    
2.  我们只想让我们的DNA的长度为16位 (还记得 `dnaModulus`?)。所以第二行代码应该 `return` 上面计算的数值对 `dnaModulus` 求余数(`%`)。
    

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    }
​
    function _generateRandomDna(string _str) private view returns (uint) {
        // 这里开始
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
​
}
​
```

写一个公共函数，它有一个参数，用来接收僵尸的名字，之后用它生成僵尸的DNA。

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    }
​
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
​
    // 从这里开始
    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
​
}
​
```

## **事件**

**事件** 是合约和区块链通讯的一种机制。你的前端应用“监听”某些事件，并做出反应。

例子:

```
// 这里建立事件
event IntegersAdded(uint x, uint y, uint result);
​
function add(uint _x, uint _y) public {
  uint result = _x + _y;
  //触发事件，通知app
  IntegersAdded(_x, _y, result);
  return result;
}
```

你的 app 前端可以监听这个事件。JavaScript 实现如下:

```
YourContract.IntegersAdded(function(error, result) {
  // 干些事
})
```

我们想每当一个僵尸创造出来时，我们的前端都能监听到这个事件，并将它显示出来。

1。 定义一个 `事件` 叫做 `NewZombie`。 它有3个参数: `zombieId` (`uint`)， `name` (`string`)， 和 `dna` (`uint`)。

2。 修改 `_createZombie` 函数使得当新僵尸造出来并加入 `zombies`数组后，生成事件`NewZombie`。

3。 需要定义僵尸`id`。 `array.push()` **返回数组的长度**，类型是`uint` - 因为数组的第一个元素的索引是 0， `array.push() - 1` 将是我们加入的僵尸的索引。 `zombies.push() - 1` 就是 `id`，数据类型是 `uint`。在下一行中你可以把它用到 `NewZombie` 事件中。

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    // 这里建立事件
    event NewZombie(uint zombieId, string name, uint dna);
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    function _createZombie(string _name, uint _dna) private {
        
        // 这里触发事件
        uint id = zombies.push(Zombie(_name,_dna)) - 1;
        NewZombie(id, _name, _dna);
    }
​
    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
​
    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
​
}
​
```

## **Web3.js**

Solidity合约部分完成，现在要写JS前端代码来调用合约。

以太坊有一个JS库，叫做Web3.js。

```
// 下面是调用合约的方式:
var abi = /* abi是由编译器生成的 */
var ZombieFactoryContract = web3.eth.contract(abi)
var contractAddress = /* 发布之后在以太坊上生成的合约地址 */
var ZombieFactory = ZombieFactoryContract.at(contractAddress)
// `ZombieFactory` 能访问公共的函数以及事件
​
// 某个监听文本输入的监听器:
$("#ourButton").click(function(e) {
  var name = $("#nameInput").val()
  //调用合约的 `createRandomZombie` 函数:
  ZombieFactory.createRandomZombie(name)
})
​
// 监听 `NewZombie` 事件, 并且更新UI
var event = ZombieFactory.NewZombie(function(error, result) {
  if (error) return
  generateZombie(result.zombieId, result.name, result.dna)
})
​
// 获取 Zombie 的 dna, 更新图像
function generateZombie(id, name, dna) {
  let dnaStr = String(dna)
  // 如果dna少于16位,在它前面用0补上
  while (dnaStr.length < 16)
    dnaStr = "0" + dnaStr
​
  let zombieDetails = {
    // 前两位数构成头部.我们可能有7种头部, 所以 % 7
    // 得到的数在0-6,再加上1,数的范围变成1-7
    // 通过这样计算：
    headChoice: dnaStr.substring(0, 2) % 7 + 1，
    // 我们得到的图片名称从head1.png 到 head7.png
​
    // 接下来的两位数构成眼睛, 眼睛变化就对11取模:
    eyeChoice: dnaStr.substring(2, 4) % 11 + 1,
    // 再接下来的两位数构成衣服，衣服变化就对6取模:
    shirtChoice: dnaStr.substring(4, 6) % 6 + 1,
    //最后6位控制颜色. 用css选择器: hue-rotate来更新
    // 360度:
    skinColorChoice: parseInt(dnaStr.substring(6, 8) / 100 * 360),
    eyeColorChoice: parseInt(dnaStr.substring(8, 10) / 100 * 360),
    clothesColorChoice: parseInt(dnaStr.substring(10, 12) / 100 * 360),
    zombieName: name,
    zombieDescription: "A Level 1 CryptoZombie",
  }
  return zombieDetails
}
```

我们的 JavaScript 所做的就是获取由`zombieDetails` 产生的数据, 并且利用浏览器里的 JavaScript 神奇功能 (我们用 Vue.js)，置换出图像以及使用CSS过滤器。

![image-20250825225858727](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250825225859139.png)
<!-- DAILY_CHECKIN_2025-08-25_END -->


# 2025-08-24
<!-- DAILY_CHECKIN_2025-08-24_START -->
开始跟着 cryptozombie 学习基础。

## Solidity合约

一份合约就是以太应用的基本模块，一份空合约如下：

```
contract HelloWorld {
​
}
```

## **版本指令**

所有的solidity源码都必须冠以“version pragma” - 标明solidity编译器的版本，以避免将来新的编译器可能破坏代码。

例如：`pragma solidity ^0.4.19;`代表当前solidity的最新版本是0.4.19.

## **状态变量**

状态变量被永久保存在合约中，也就是说被写入以太坊区块链中。

例子：

```
contract Example {
  // 这个无符号整数将会永久的被保存在区块链中
  uint myUnsignedInteger = 100;
}
```

在上面的例子中，定义 `myUnsignedInteger` 为 `uint` 类型，并赋值100。

`uint` 无符号数据类型， 指**其值不能是负数**，对于有符号的整数存在名为 `int` 的数据类型。

> 注: Solidity中， `uint` 实际上是 `uint256`代名词， 一个256位的无符号整数。

## **数学运算**

-   加法: `x + y`
    
-   减法: `x - y`,
    
-   乘法: `x * y`
    
-   除法: `x / y`
    
-   取模 / 求余: `x % y` _(例如,_ `13 % 5` _余_ `3`_, 因为13除以5，余3)_
    
-   乘方：x \*\* y
    

为了保证我们的僵尸的DNA只含有16个字符，我们先造一个`uint`数据，让它等于10^16。这样一来以后我们可以用模运算符 `%` 把一个整数变成16位。

### **数学原理**

-   `10^16`\= 10000000000000000（1后面跟着16个0）
    
-   任何数字 `% 10^16`的结果范围是：0 到 9999999999999999
    
-   这正好是**16位十进制数字**的最大范围
    

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    uint dnaDigits = 16;
    //这里开始
    uint dnaModulus = 10 ** dnaDigits;
​
}
```

## **结构体**

结构体允许你生成一个更复杂的数据类型，有多个属性：

```
struct Person {
  uint age;
  string name;
}
```

> `string` 用于保存任意长度的UTF-8编码数据。

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    // 这里开始
    struct Zombie {
        string name;
        uint dna;
    }
}
```

## **数组**

静态数组和动态数组：

```
// 固定长度为2的静态数组:
uint[2] fixedArray;
// 固定长度为5的string类型的静态数组:
string[5] stringArray;
// 动态数组，长度不固定，可以动态添加元素:
uint[] dynamicArray;
// 结构体数组
Person[] people; // 这是动态数组，我们可以不断添加元素
```

### **公共数组**

可以定义 `public` 数组, Solidity 会自动创建 **getter** 方法. 语法如下:

```
Person[] public people;
```

其它的合约可以从这个数组读取数据（但不能写入数据），所以这在合约中是一个有用的保存公共数据的模式。

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    // 这里开始
    Zombie[] public zombies;
​
}
​
```

## **定义函数**

```
function eatHamburgers(string _name, uint _amount) {
​
}
```

这是一个名为 `eatHamburgers` 的函数，它接受两个参数：一个 `string`类型的 和 一个 `uint`类型的。现在函数内部还是空的。

> 注：: 习惯上函数里的变量都是以(`_`)开头 (但不是硬性规定) 以区别全局变量。

我们的函数定义如下:

```
eatHamburgers("vitalik", 100);
```

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    // 这里开始
    function createZombie(string _name, uint _dna) {
        
    }
​
}
​
```

## **使用结构体和数组**

创建新的结构体，然后加入到名为 `people` 的数组中。

```
// 创建一个新的Person:
Person satoshi = Person(172, "Satoshi");
​
// 将新创建的satoshi添加进people数组:
people.push(satoshi);
```

也可以两步并一步，用一行代码更简洁:

```
people.push(Person(16, "Vitalik"));
```

> 注：`array.push()` 在数组的 **尾部** 加入新元素 ，所以元素在数组中的顺序就是我们添加的顺序， 如:

```
uint[] numbers;
numbers.push(5);
numbers.push(10);
numbers.push(15);
// The `numbers` array is now equal to [5, 10, 15]
```

## **私有/公共函数**

Solidity定义函数默认属性为公共，这意味着任何一方（或其他合约）都可以调用合约里的函数。

私有函数：

```
uint[] numbers;
​
function _addToArray(uint _number) private {
  numbers.push(_number);
}
```

这意味着只有我们合约中的其它函数才能够调用这个函数，给 `numbers` 数组添加新成员。

可以看到，在函数名字后面使用关键字 `private` 即可。和函数的参数类似，私有函数的名字用(`_`)起始。

```
pragma solidity ^0.4.19;
​
contract ZombieFactory {
​
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
​
    struct Zombie {
        string name;
        uint dna;
    }
​
    Zombie[] public zombies;
​
    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    }
​
}
​
```
<!-- DAILY_CHECKIN_2025-08-24_END -->


# 2025-08-22
<!-- DAILY_CHECKIN_2025-08-22_START -->
智能合约实战：OpenZeppelin的Ethernaut挑战

## 第二关 Fallback

### 通关要求：

1. 得到对合约的所有权
2. 余额变为0

### 合约源代码：

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



### 解题方法

观察函数，将余额变为0的方法是通过 `withdraw` 函数，只有当`msg.sender`等于变量`owner`的值时才能调用（见`onlyOwner`函数修改器）。这个函数将把合约中的所有资金转移到 "所有者"地址。

```solidity
function withdraw() public onlyOwner {
        payable(owner).transfer(address(this).balance);
    }
```

因此，如果能找到一种方法，将所有者的值改为我们的地址，就能从合约抽出所有余额。

有两个地方是修改owner的，一个是`contribute`函数

```solidity
    function contribute() public payable {
        require(msg.value < 0.001 ether);
        contributions[msg.sender] += msg.value;
        if (contributions[msg.sender] > contributions[owner]) {
            owner = msg.sender;
        }
    }
```

该函数允许发送不大于0.001 ether的wei，作为发送者的贡献，如果贡献大于当前所有者贡献。就发送者就可以成为新的所有者。但是构造函数里设置了贡献是1000ETH，所以不会比这个大，不能从这里改。

一个是`withdraw`函数

```solidity
function withdraw() public onlyOwner {
        payable(owner).transfer(address(this).balance);
    }

```

这是一个 "特殊" 的函数，当有人向合约发送一些以太坊而没有在交易的 "数据"字段中指定任何东西时，receive 就会被 "自动"调用。

引用官方[Solidity文档](https://learnblockchain.cn/docs/solidity/contracts.html#receive)中对receive函数的介绍：

> *一个合约现在只能有一个* `receive` *函数，声明的语法是：* `receive() external payable {...} `（没有 `function` *关键词）。它在没有数据（**calldata**）的合约调用时执行，例如通过* `send()` *或* `transfer()`*调用。该函数不能有参数，不能返回任何东西，并且必须有* `external` *可见性和* `payable `状态可变性。

下面是`receive`函数的代码：

```solidity
receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
}
```

在`receive`函数中，只有当与交易一起发送的`wei`数额`>0`并且我们在`contributions[msg.sender]`中的贡献`>0`时，`owner`就会更新`msg.sender`。

步骤：

1. 用`0.0001 ether`（通过`require`检查）向合约捐款，调用`contribute`函数，这样`contributions[msg.sender]`将大于0 ；
2. 直接向合约发送`wei`，触发`receive`函数，成为新的`owner`
3. 调用`withdraw`，将合约中储存的`ETH`全部掏空!

### 执行结果

在控制台中输入命令：

```js
contract.contribute({value: toWei("0.00001")})
```

![telegram-cloud-photo-size-5-6145661132275304355-x](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250822233239646.jpg)

![telegram-cloud-photo-size-5-6145661132275304352-x](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250822233331567.jpg)

用toWei函数将ether转换成wei

![telegram-cloud-photo-size-5-6145661132275304354-x](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250822233256430.jpg)

```js
contract.sendTransaction({value: toWei("0.00001")})
```



此时owner已经转为msg.sender了，余额归零：

```js
contract.withdraw()
```

![telegram-cloud-photo-size-5-6145661132275304353-x](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250822233354607.jpg)

![telegram-cloud-photo-size-5-6145661132275304351-x](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250822233417699.jpg)

提交成功：

![telegram-cloud-photo-size-5-6145661132275304350-x](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250822233424689.jpg)
<!-- DAILY_CHECKIN_2025-08-22_END -->

# 2025-08-20

DAO：社区驱动的去中心化协作新模式

## 什么是DAO？

DAO（去中心化自治组织）正在重塑我们对于组织和协作的认知。简单来说，DAO=社区+智能合约+去中心化资产/协作。这种新型组织形态通过区块链技术实现了完全透明、无需信任的协作方式。

与传统组织不同，DAO没有中心化的管理层，所有决策由社区成员共同做出。智能合约作为"自动化章程"确保规则被严格执行，而组织资产则完全由代码和社区控制，没有任何单一方能够擅自挪用。

## 如何集体决策？

DAO的核心魅力在于其集体决策机制。典型的决策流程包括：

1. **提案机制**：任何符合条件的成员都可以提出改进建议或资金使用方案。提案需要包含详细说明和预期影响。
2. **投票规则**：大多数DAO采用代币加权投票，持币越多投票权越大；也有些项目尝试一人一票制。投票通常有最低参与门槛要求。
3. **委托治理**：为提升效率，成员可将投票权委托给更了解特定领域的代表。这种"流动民主"结合了直接民主和代议制优点。

## 如何分配角色？

DAO中的角色管理极具创新性：

1. **准入机制**：有的DAO完全开放，有的则需要持有一定代币或通过现有成员投票才能加入。NFT常被用作会员资格证明。
2. **角色划分**：典型角色包括核心贡献者（全职）、兼职贡献者、顾问和普通成员。不同角色对应不同权限和责任，全部记录在链上。

## 如何分配贡献？

DAO通过以下方式确保贡献得到合理回报：

1. **贡献评估**：采用同行评审、量化指标或社区投票等方式评估工作价值。所有贡献记录在透明数据库中。
2. **奖励形式**：报酬可以是原生代币、稳定币、NFT或未来收益分成。部分DAO尝试将贡献转化为"声望"系统，影响长期收益分配。

## 如何管理资金？

DAO的资金管理完全透明：

1. **资金管理**：国库资金由多重签名钱包或专用智能合约保管，任何支出都需要提案和投票通过。所有交易记录在区块链上可查。
2. **国库用途**：资金通常用于开发拨款、社区奖励、营销活动、流动性提供和战略收购等。部分DAO还会将资金投入DeFi获取收益。

DAO代表了组织演化的下一步，它通过代码将信任制度化，让全球陌生人能够无需中介地协作共创价值。虽然仍在早期阶段，但DAO已经在风险投资、开源开发和创意产业等领域展现出巨大潜力。未来，我们可能会看到更多传统企业借鉴DAO的治理模式，实现更加开放和民主的运营方式。

# 2025-08-19

智能合约实战：OpenZeppelin的Ethernaut挑战

## 第一关：Hello Ethernaut

### 配置钱包

连接钱包，切换到Sepolia测试网。

### 打开浏览器控制台

F12打开浏览器开发者模式，在控制台下输入命令：

```js
player
```

得到玩家地址

![9cb99466b4ef33e557b564b9956bd07e](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019697.PNG)

通过以下命令得到账户余额：

```js
getBalance(player)
```

![dbce830c35d05a6a586ec252042f343a](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019534.PNG)

### ethernaut 合约

```js
ethernaut
```

得到游戏的主要合约，展开来可以和ABI互动：

![39f551a5d75cb149eded5d922674d773](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019643.PNG)

`ethernaut` 是一个 `TruffleContract` 对象， 它包装了部署在区块链上的 `Ethernaut.sol` 合约.

除此之外，合约的 ABI 还提供了所有的 `Ethernaut.sol` 公开方法, 比如 `owner`. 比如输入以下命令:

```js
ethernaut.owner()
```

![3e3fc37045bea203a28cee62bcb1d15a](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019655.PNG)

可以看到合约的拥有者是谁

### 获得关卡实例

请求生成一个level instance，点击页面下方的按钮，钱包会发送请求，部署一个新的合约。

![ea0bcb4f29a5a1927e4e9dc62de988e1](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019181.PNG)

![1ff65b79379e306da6aeb943c940a392](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019187.PNG)

输入 contract 变量来观察这个合约的ABI：

![d1ed8f404e3a01c0edcca44fa2b9f5b5](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224016426.PNG)

### 合约互动

查看info方法，开始得到通关信息：

![60e78d1f167d12dc20dab3b4f980c560](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019197.PNG)

根据提示，调用info1方法后调用info2并传入参数“hello”：

![19ab34df259610b4ca0836aaa5dc1bec](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224016415.PNG)

然后又要调用属性infoNum：

![d7dd13ac0120da749cec163271baf6ba](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019585.PNG)

得到数字42，调用info42方法，又要调用另一个方法：

![1d1710cbacb20549ce214a8c7037cfed](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224016423.PNG)

一直套娃，最后输入认证方法和密码：

![1d712c00a7dd2d3b9678641ad0daee32](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019175.PNG)

![b676f469aed1f6ade11da76e43592b61](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224031694.PNG)

认证成功，通关！！！

![8db4142706a813fbf878f169fb9a3b98](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250819224019191.PNG)

### 整体代码

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Instance {
    string public password;
    uint8 public infoNum = 42;
    string public theMethodName = "The method name is method7123949.";
    bool private cleared = false;

    // constructor
    constructor(string memory _password) {
        password = _password;
    }

    function info() public pure returns (string memory) {
        return "You will find what you need in info1().";
    }

    function info1() public pure returns (string memory) {
        return 'Try info2(), but with "hello" as a parameter.';
    }

    function info2(string memory param) public pure returns (string memory) {
        if (keccak256(abi.encodePacked(param)) == keccak256(abi.encodePacked("hello"))) {
            return "The property infoNum holds the number of the next info method to call.";
        }
        return "Wrong parameter.";
    }

    function info42() public pure returns (string memory) {
        return "theMethodName is the name of the next method.";
    }

    function method7123949() public pure returns (string memory) {
        return "If you know the password, submit it to authenticate().";
    }

    function authenticate(string memory passkey) public {
        if (keccak256(abi.encodePacked(passkey)) == keccak256(abi.encodePacked(password))) {
            cleared = true;
        }
    }

    function getCleared() public view returns (bool) {
        return cleared;
    }
}
```

# 2025-08-18

今天了解了一下SocialFi协议Farcaster。

## 什么是Farcaster？
Farcaster是一个建立在以太坊上的SocialFi（去中心化社交网络）协议，由前Coinbase工程师Dan Romero和Varun Srinivasan于2020年创立。与Twitter或Facebook等传统社交平台不同，Farcaster将用户身份和数据所有权完全交还给用户，同时保持了类似传统社交媒体的流畅体验。

## Farcaster的核心特点
1. 去中心化身份​​：用户通过以太坊地址或ENS域名注册唯一身份，完全掌控自己的社交图谱

2. 混合架构​​：结合了链上身份验证与链下数据存储，在去中心化和用户体验间取得平衡

3. 抗审查​​：没有中心化实体可以单方面删除用户或内容

4. 可组合性​​：开放协议允许开发者构建各种客户端和应用

5. 数据可移植性​​：用户可以自由迁移数据到其他兼容客户端

## Farcaster的技术架构
Farcaster采用独特的"链上+链下"混合架构：

• 链上部分​​：处理用户身份注册和关键操作，使用以太坊作为基础层

• 链下部分​​：通过Farcaster Hub网络存储和同步社交数据，确保高性能和低成本

这种设计使得Farcaster既能享受区块链的安全保障，又能提供接近Web2社交平台的用户体验。

## 为什么选择Farcaster？
1. 真正的数据所有权​​：你的关注列表、发帖历史都归你所有，而非平台资产

2. 开放的生态系统​​：开发者可以基于协议构建各种创新应用，用户可以选择最适合自己的客户端

3. 抗平台风险​​：不再担心平台突然改变政策或无故封号

4. 经济激励对齐​​：通过代币经济将平台价值回馈给内容创作者和活跃用户

5. 可验证的身份​​：基于区块链的身份系统减少了虚假账号和机器人问题

## Farcaster生态系统
目前Farcaster生态已经涌现出多个受欢迎的客户端和应用：

• Warpcast​​：官方推出的旗舰客户端，体验类似Twitter

• Yup​​：社交内容策展和奖励平台

• Discove​​：基于兴趣的内容发现引擎

• Fardrop​​：NFT分发和空投工具

# 2025-08-16

今晚参加了运营方向的同学们组织的SPACE，了解了一些工作的情况。

## 社保与合同

### 国内远程

遵守国内法规，交社保

### 国外远程

遵守当地法律，可能发本地货币或U，社保可能是问题

### 发U能走劳动仲裁吗？

- 不受劳动法保护
- 尽量找大公司、好项目
- 尽量以法币结算
- 短期项目以兼职参与

## 术语

- 找准赛道

- 多在社区活跃，多参与项目应用

## 包装自己，脱颖而出

- 保持真诚，认真学习
- 与同事、导师交流
- 不断学习，保持求知欲
- 实事求是，能增色但是不要过度包装

## Web3与Web2职场区别

- 基础工资+代币激励
- 工作饱和度不一样
- 税务区别
- 扁平化，需要主动性
- 远程办公注意心态调节

## 怎么筛选靠谱公司和项目

- 看推特、官网、dc群
- 看rootdata
- 有无rug记录
- 考察项目负责人知识

## 职场技能

- 专业度
- 沟通技巧
- 前辈的代码习惯
- 深耕一个领域

# 2025-08-14

部署一个简单的链上留言板项目。

代码：

```solidity
//SPDX-License-Identifier:MIT
pragma solidity ^0.8.0;

contract MessageBoard{
    //保存所有人的留言记录
    //每个地址对应一个字符串数组，存储该用户的所有留言
    mapping(address => string[]) public messages;
    
    //留言事件，便于检索器和区块链浏览器追踪
    //​​indexed参数​​：address标记为indexed，可以高效过滤特定地址的留言事件
    event NewMessage(address indexed sender, string message);

    //构造函数，在部署时留言一条欢迎词
    constructor(){
        //在函数参数和局部变量中使用memory表示数据临时存储在内存中
        string memory initMsg = "Hello ETH Pandas";
        //将消息存入部署者(msg.sender)的留言数组中
        //msg是内置全局变量，msg.sender获取部署者地址
        messages[msg.sender].push(initMsg);
        //触发NewMessage事件记录这次初始化留言
        emit NewMessage(msg.sender, initMsg);
    
    }

    //发送一条留言
    function leaveMessage(string memory _msg) public{
        messages[msg.sender].push(_msg); //添加到发言记录
        emit NewMessage(msg.sender, _msg); //发出事件
    }

    // 查询某人第 n 条留言（从 0 开始）
    //​​view修饰符​​：表示只读取不修改链上状态，不消耗gas
    function getMessage(address user, uint256 index) public view returns (string memory) {
        return messages[user][index];
    }

    // 查询某人一共发了多少条
    function getMessageCount(address user) public view returns (uint256) {
        return messages[user].length;
    }
}
```

部署成功，命令终端查看部署日志和构造函数的执行信息：

![f45dcd7c978c53d457709f1ada0fb02c](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250814230613147.PNG)

调用留言函数：

1. 在合约实例中找到 `leaveMessage` 函数输入框；
2. 在输入框中填入留言内容（例如：`Hello World`）；
3. 点击 **leaveMessage** 按钮，发起交易调用；
4. 右侧命令终端将显示一条新的交易记录，点击该记录可查看交易详情与链上存储的留言信息。

![bc928a779b51d6176e7f3a38f7237f50](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250814230613161.PNG)

# 2025-08-13

# ERC-20标准

这是一个广泛使用的代币标准，定义了代币合约必须实现的基本功能，包括：

- 代币转账 (`transfer`)
- 查询余额 (`balanceOf`)
- 授权其他账户使用代币 (`approve`, `allowance`)
- 以及相关事件

# **OpenZeppelin**

OpenZeppelin是合约库，使用OpenZeppelin的实现可以确保合约的安全性，因为它经过了专业审计和广泛测试。

# 流程

**1. 准备工作**

**1.1 安装 MetaMask**

- 安装 [MetaMask](https://metamask.io/) 浏览器扩展。
- 创建或导入一个钱包。

**1.2 获取 Sepolia 测试网 ETH**

- 打开 MetaMask，切换到 Sepolia 测试网。
- 从 [Sepolia Faucet](https://sepoliafaucet.com/) 获取测试网 ETH。

**1.3 打开 Remix IDE**

- 打开在线solidity编辑器 **Remix IDE**

**2. 编写 ERC20 代币合约**

**2.1 创建新文件**

- 在 Remix IDE 的 File Explorers 中，点击 Create New File，命名为 MyToken.sol。

**2.2 编写合约代码**

```solidity
// SPDX-License-Identifier: MIT
// 指定合约的软件许可证 - 这里使用的是MIT许可证，一种宽松的开源许可证
// 这是Solidity编译器要求的，应该出现在每个源文件的开头

pragma solidity ^0.8.0;
// 指定编译器版本要求 - 这个合约需要使用0.8.0或更高版本的Solidity编译器编译
// ^符号表示可以使用0.8.0及以上版本，但不包括0.9.0及以上版本

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
// 导入OpenZeppelin合约库中的ERC20标准代币实现
// OpenZeppelin是一个经过审计的智能合约开发库，提供了安全的标准实现

contract MyToken is ERC20 {
    // 定义一个名为MyToken的新合约，它继承自ERC20合约
    // ERC20是以太坊上最常用的代币标准，定义了代币的基本功能和接口

    constructor(uint256 initialSupply) ERC20("MyToken", "MTK") {
        // 合约的构造函数，在合约部署时自动执行一次
        // 接收一个参数initialSupply，表示初始代币供应量
        
        // 调用父合约(ERC20)的构造函数，传入代币名称("MyToken")和符号("MTK")
        // 这会初始化代币的基本信息
        
        _mint(msg.sender, initialSupply);
        // 调用内部函数_mint来创建初始代币供应
        // msg.sender是部署合约的账户地址，initialSupply是要铸造的代币数量
        // 这意味着所有初始代币都会被发送到合约部署者的地址
    }
}
```

**2.3 编译合约**

- 在 Solidity Compiler 选项卡中，选择 0.8.0 或更高版本的编译器。
- 点击 Compile MyToken.sol。

![截屏2025-08-13 22.26.20](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250813222629219.png)

编译成功：

![截屏2025-08-13 22.34.49](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250813223840993.png)

**3. 部署合约到 Sepolia 测试网**

**3.1 配置 MetaMask**

- 确保 MetaMask 已连接到 Sepolia 测试网。
- 确保账户中有足够的 Sepolia ETH。

**3.2 部署合约**

- 在 Deploy & Run Transactions 选项卡中，选择 Injected Provider - MetaMask 作为环境。
- 在 Contract 下拉菜单中选择 MyToken。
- 在 Deploy 按钮旁边的输入框中，输入初始供应量（例如 1000000）。
- 点击 Deploy，MetaMask 会弹出确认交易窗口，点击 Confirm。

![截屏2025-08-13 22.41.30](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250813224139579.png)

![截屏2025-08-13 22.42.03](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250813224239329.png)

**3.3 获取合约地址**

- 部署成功后，在 Deployed Contracts 部分可以看到合约地址。

![截屏2025-08-13 22.43.22](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250813224325526.png)

**4. 与代币合约交互**

**4.1 添加代币到 MetaMask**

- 打开 MetaMask，点击 Add Token。
- 选择 Custom Token，输入合约地址。
- 点击 Next，然后 Add Tokens。

![截屏2025-08-13 22.45.42](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250813224549772.png)

**4.2 查看代币余额**

- 在 MetaMask 中，切换到 Sepolia 测试网。
- 你应该能看到 MyToken (MTK) 的余额。

**4.3 转账代币**

- 在 Remix IDE 的 Deployed Contracts 部分，展开 MyToken 合约。
- 在 transfer 函数中，输入接收地址和转账金额。
- 点击 transact，MetaMask 会弹出确认交易窗口，点击 Confirm。

# 2025-08-12

时间比较紧张，继续梳理一下笔记，还没开始学代码
![IMG_3336](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250812230639148.PNG)

# 2025-08-11

#### 1. DApp 与传统应用对比

##### 1.1. 技术栈对比

- **前端技术栈**：
  - **传统 Web App**：
    - 框架：React、Vue、Angular
    - 接口：HTTP/REST API
    - 身份验证：JWT/Session
    - 状态管理：Redux、Vuex
  - **DApp 前端**：
    - 框架：React、Vue、Angular
    - Web3 Provider：连接区块链
    - 钱包集成：MetaMask、WalletConnect
    - 状态管理：结合本地状态与区块链状态
- **后端架构对比**：
  - **传统后端**：
    - API 服务器：Node.js、Python、Java
    - 层级：业务逻辑层、数据访问层
    - 存储：中心化关系型数据库（如 MySQL、PostgreSQL）
    - 附加：认证授权、缓存（Redis）、消息队列
  - **DApp 后端**：
    - 智能合约：Solidity
    - 区块链网络：Ethereum、Polygon
    - 节点：分布式节点
    - 存储：分布式存储（如 IPFS、Arweave）
    - 事件日志：通过索引服务（如 TheGraph）

##### 1.2. 数据管理对比

- **数据存储方式**：

  - **传统应用**：
    - 用户/业务数据：中心化数据库
    - 文件存储：云存储（如 AWS S3）
    - 备份：定期备份策略
    - 数据访问：权限控制系统
  - **DApp**：
    - 状态数据：区块链存储
    - 大文件：IPFS/Arweave
    - 隐私数据：用户本地/加密
    - 数据完整性：密码学哈希
    - 数据访问：私钥签名控制

- **数据一致性保证**：

  | 方面           | 传统应用         | DApp                |
  | -------------- | ---------------- | ------------------- |
  | **一致性模型** | ACID 事务        | 最终一致性          |
  | **冲突解决**   | 数据库锁机制     | 共识算法            |
  | **数据同步**   | 主从复制         | P2P 网络同步        |
  | **性能**       | 高 TPS (数千/秒) | 低 TPS (10-1000/秒) |
  | **可靠性**     | 依赖备份策略     | 分布式冗余          |

##### 1.3. 用户体验对比

- **用户注册/登录流程**：
  - **传统应用**：输入邮箱密码 → 发送认证请求 → 验证用户信息 → 返回 JWT Token → 登录成功
  - **DApp**：连接钱包 → 请求连接 → 钱包弹窗确认 → 返回钱包地址 → 显示连接状态（无需密码，由私钥管理）
- **交易确认流程**：
  - **传统支付**：输入支付信息 → 第三方认证 → 银行处理 → 成功/失败（可重试）
  - **区块链交易**：构造交易 → 钱包签名 → 广播交易 → 等待确认 → 成功/失败（可重构）

---

#### 2. 智能合约基础特性

##### 2.1. 什么是智能合约？

- **定义**：Ethereum 应用层的基础构建块，是存储在区块链上的计算机程序，遵循“if this then that”逻辑，一旦创建，代码不可更改，保证按规则执行。
- **起源**：Nick Szabo 于 1994 年提出概念，1996 年探讨其潜力。Szabo 设想一个无需可信中介的数字市场，通过自动、加密安全的进程实现交易和业务功能。Ethereum 将此愿景付诸实践。
- **参考视频**：Finematics 解释智能合约（https://www.youtube.com/watch?v=pWGLtjG-F5c&t=3s）。

##### 2.2. 智能合约核心特性

- **不可篡改性 (Immutable)**：部署后代码无法修改。
- **透明性 (Transparent)**：所有人可查看和验证。
- **可编程性 (Programmable)**：灵活定制业务逻辑。
- **去中心化 (Decentralized)**：运行在分布式网络。
- **重要说明**：
  - **确定性**：相同输入和区块链状态下，总是产生相同结果。
  - **自动执行**：满足条件时，无需人工干预自动执行。

##### 2.3. 状态存储与执行环境

- **区块链状态机制**：区块链是一个全局状态机，每个区块包含交易，这些交易改变网络状态（账户余额、合约存储数据等）。
- **状态转换过程**：
  1. 区块创建：矿工收集待处理交易。
  2. 状态计算：按顺序执行交易，计算新状态。
  3. 状态根更新：汇总变化为新状态根哈希。
  4. 区块链接：通过哈希链接到前一个区块。
- **状态**：基于前一区块状态 + 当前区块交易累积结果。

##### 2.4. 以太坊智能合约开发语言 Solidity 特性

- **数据类型基础**：

  - 基本：`uint256`（无符号整数）、`bool`（布尔值）、`address`（以太坊地址）、`bytes32`（固定长度字节）。
  - 映射：`mapping(address => uint256)`。
  - 数组：`uint256[]`。

- **函数修饰符与可见性**：

  - **可见性**：`public`（内外可调用）、`external`（仅外部）、`internal`（内部及继承）、`private`（仅当前合约）。
  - **状态修饰符**：`view`（只读，不修改状态）、`pure`（纯函数，不读写状态）、`payable`（可接收 ETH）。

- **示例**：

  ```solidity
  contract VisibilityExample {
      uint256 private _value;
      function getValue() public view returns (uint256) { return _value; }  // 只读
      function add(uint256 a, uint256 b) public pure returns (uint256) { return a + b; }  // 纯函数
      function deposit() external payable { _value += msg.value; }  // 可接收 ETH
  }
  ```

##### 2.5. Gas 机制

- **Gas 定义**：以太坊的“燃料”，每个操作消耗 Gas，用户支付费用激励矿工/验证者。

- **EIP-1559（2021）**：总费用 = Gas Used × (Base Fee + Priority Fee)。

  - Gas Used：操作复杂度决定。
  - Base Fee：网络自动调整（销毁），使用率 > 50% 时上涨 12.5%，< 50% 时下降。
  - Priority Fee：用户设置，给验证者。

- **费用飙升原因**：有限区块空间（~30M Gas/区块）、Base Fee 调整、Priority Fee 竞价。

- **实际费用示例**：

  | 网络状态 | Base Fee | Priority Fee | 总费用   | 说明         |
  | -------- | -------- | ------------ | -------- | ------------ |
  | 空闲     | 8 Gwei   | 1 Gwei       | 9 Gwei   | 快速确认     |
  | 正常     | 15 Gwei  | 2 Gwei       | 17 Gwei  | 1-2 分钟确认 |
  | 繁忙     | 40 Gwei  | 5 Gwei       | 45 Gwei  | 可能等待     |
  | 拥堵     | 120 Gwei | 20 Gwei      | 140 Gwei | 长时间等待   |

- **存储类型与 Gas 消耗**：

  - Storage：昂贵（首次写入 20,000 Gas，修改 5,000 Gas）。
  - Memory：廉价（~3 Gas/字）。
  - Calldata：最廉价（读取 ~16/4 Gas）。

- **Gas 优化策略**：

  - 减少 Storage 操作（用临时变量计算，一次写入）。
  - 优化数据结构（uint256 优于 uint8，使用 packed struct）。
  - 批量操作（一次交易处理多个）。
  - 用事件替代存储（记录历史数据）。

- **实际 Gas 成本对比**：

  | 操作类型     | Gas 消耗 | 实际成本 (50 Gwei) | 说明        |
  | ------------ | -------- | ------------------ | ----------- |
  | 简单转账     | 21,000   | $2-5               | 基础操作    |
  | ERC20 转账   | 65,000   | $6-15              | 合约调用    |
  | Uniswap 交换 | 150,000  | $15-35             | 复杂 DeFi   |
  | NFT 铸造     | 80,000   | $8-20              | 存储 + 事件 |

- **开发者建议**：在测试网测试 Gas，使用 Gas Reporter 工具，考虑 Layer 2 方案降低费用。

##### 2.6. 事件与日志

- **目的**：记录重要操作到区块链日志（Gas 消耗比存储低），用于前端更新、历史查询和链下分析。

- **事件定义与使用**：

  ```solidity
  contract VaultExample {
      event Deposited(address indexed user, uint256 amount, uint256 timestamp);  // indexed 便于过滤
      // ... 其他事件和函数
      function deposit() external payable {
          // ... 逻辑
          emit Deposited(msg.sender, msg.value, block.timestamp);  // 触发事件
      }
  }
  ```

- **indexed 参数作用**：最多 3 个，作为过滤条件；非 indexed 参数 Gas 更低。

- **前端示例**（JavaScript）：

  ```javascript
  contract.on("Deposited", (user, amount, timestamp) => { console.log(...); });  // 监听
  const filter = contract.filters.Deposited("0x123...");  // 过滤
  const events = await contract.queryFilter(filter);  // 查询
  ```

##### 2.7. 安全考虑

- 常见漏洞：重入攻击、选择器碰撞、权限管理、签名重放、tx.origin 钓鱼、操纵区块时间等。
- **建议**：在测试网充分测试，使用安全工具审计合约。

---

### 关键 takeaway

- **DApp vs. 传统应用**：DApp 强调去中心化、不可篡改和透明，但 TPS 较低、延迟较高；传统应用性能高但依赖中介。
- **智能合约**：不可变、透明、可编程，去中心化执行；Solidity 是主要语言，Gas 优化至关重要。
- **开发提示**：测试 Gas 消耗，使用事件日志，探索 Layer 2 降低费用。

# 2025-08-10

今天跟着LXDAO的 my first nft 教程在sepolia测试网上Mint了自己的nft，下一步尝试用solidity编写智能合约部署nft和代币。

https://x.com/enzoyangjn/status/1954549995405013198

# 2025-08-08

## DApp架构解析

去中心化应用（Dapp）是与传统集中式应用不同的全新应用模式，通常运行在区块链或分布式网络上。与传统应用相比，Dapp 的核心特点在于去中心化，意味着应用的逻辑和数据不由单一实体控制，而是由多个参与者共同维护。

### 1. 核心组件架构

- 前端**（User Interface）**
- 智能合约**（Smart Contracts）**
- 数据检索器**（Indexer）**
- 区块链和去中心化存储**（Blockchain & Decentralized Storage）**



**技术栈对比表**：

|  组件  | 传统应用技术栈 |      Web3技术栈       |      核心差异      |
| :----: | :------------: | :-------------------: | :----------------: |
|  前端  | React/Angular  | React + Web3.js/Wagmi | 钱包集成、链上交互 |
|  后端  |    REST API    |       智能合约        | 无服务器、逻辑上链 |
| 数据库 | MySQL/MongoDB  |   区块链状态+索引器   | 不可篡改、去中心化 |
|  存储  |     AWS S3     |     IPFS/Filecoin     |  内容寻址、分布式  |

### 2. 开发流程详解

![Dapp 开发流程图](https://web3intern.xyz/assets/dapp_development-workflow_01-sGuU6DCd.jpg)

# 2025-08-07

## 一、岗位职责

### 技术岗位职责

#### 前端工程师核心职责

- **区块链应用开发**：设计和开发基于区块链技术的前端应用，支持去中心化平台交互
- **技术实现**：使用React/Vue框架实现钱包连接、交易签名、信息验证等Web3功能
- **智能合约集成**：优化智能合约的前端交互，确保链上数据与UI无缝连接
- **协作开发**：与后端团队协作通过GraphQL/RESTful API获取链上链下数据
- **性能优化**：持续优化前端性能，确保多设备/浏览器兼容性

#### 后端工程师核心职责

- **Dapp服务构建**：开发和维护去中心化应用的后端服务，处理链上数据交互
- **API开发**：构建高性能RESTful/GraphQL API支持Web3平台需求
- **智能合约协作**：确保智能合约与后端服务的无缝连接，优化数据读写效率
- **系统优化**：保障系统高可用性、高吞吐量和低延迟，满足高并发需求

#### 智能合约工程师核心职责

- **合约开发**：使用Solidity编写安全可靠的智能合约，涵盖NFT/DeFi/DAO等领域
- **合约部署**：在以太坊/Polygon/Arbitrum等网络部署合约并优化Gas费用
- **安全测试**：编写单元测试，进行代码审计与安全性测试，防范重入攻击等风险
- **技术研究**：跟踪区块链技术和智能合约最佳实践，推动技术迭代

### 非技术岗位职责

#### 产品与运营

- 协调Web3产品全生命周期管理，收集用户反馈推动产品迭代
- 执行用户增长战略，监控KPI指标并提出优化建议
- 跨部门协作确保产品上市策略一致性

#### 社区管理

- 构建并管理Telegram/Twitter/Discord等社交平台社区
- 组织AMA、线上活动等社区互动提升用户粘性
- 跟踪社区健康度指标，定期输出分析报告

#### 研究分析

- 收集分析Web3行业数据，编写可行性研究报告
- 跟踪区块链技术演进，撰写深度研究报告
- 进行竞争对手分析和加密经济模型设计

## 二、招聘需求与技术栈

### 技术岗位要求

#### 前端工程师

- **基础要求**：计算机相关本科，精通HTML/CSS/JavaScript，熟悉React/Vue
- **Web3技术**：熟悉Viem(主流)/Web3.js/Ethers.js，能与智能合约交互
- **区块链知识**：了解以太坊/Solana等网络，有Dapp开发经验优先
- **开发规范**：熟悉Git，注重代码可维护性，有良好问题解决能力

**技术栈**：

```
HTML5/CSS3/JavaScript(ES6+)
React/Vue/TypeScript/Next.js
Viem/Web3.js/Ethers.js
```

#### 后端工程师

- **基础要求**：计算机相关本科，精通Node.js/Go/Python
- **Web3技术**：熟悉Viem/Web3.js，能处理链上数据
- **系统架构**：有高并发分布式系统经验，熟悉RESTful/GraphQL
- **数据库**：熟悉MySQL/PostgreSQL或NoSQL优化

**技术栈**：

```
Node.js/Go/Python
Viem/Web3.js/Ethers.js
RESTful/GraphQL
MySQL/PostgreSQL
Docker/Kubernetes
```

#### 智能合约工程师

- **基础要求**：计算机相关本科，3年以上Solidity开发经验
- **安全知识**：熟悉常见攻击模式(重入攻击/溢出/权限管理等)
- **开发工具**：熟练使用Foundry/Hardhat/Remix等开发框架
- **标准协议**：熟悉ERC-20/721/1155等Token标准

**技术栈**：

```
Solidity
Remix/Foundry/Hardhat
Phalcon/Tenderly/Yul
```

### 非技术岗位要求

#### 产品与运营

- 熟悉产品上线全流程，擅长跨部门协调
- 具备数据分析能力，熟练使用SQL/Excel等工具

#### 社区管理

- 有Web3/DAO/NFT社区管理经验
- 优秀文案撰写与沟通能力
- 熟悉社区数据分析工具

#### 研究分析

- 熟练使用Excel/SPSS/Python等分析工具
- 深入了解区块链生态和加密经济学
- 精通Glassnode/Token Terminal等链上分析工具

## 三、法律框架与合规要求

### 中国监管政策

- **全面禁止**：ICO、虚拟货币交易所运营、虚拟货币支付功能
- **限制方向**：封堵金融属性，有限容忍技术创新
- **重点打击**：非法集资、传销、洗钱、赌博等违法行为

### 核心法律风险

#### 1. 代币发行与交易风险

- 任何形式的代币融资(ICO/IEO/IDO)均被禁止
- "空投"、"白名单"等变相融资可能构成非法吸收公众存款罪
- 技术人员参与代币设计/部署可能被视为共同犯罪

#### 2. 赌博/传销/洗钱风险

- 链游"充值-抽奖-提现"机制可能构成开设赌场罪
- "邀请返利"、"多级推广"可能触犯传销罪
- 虚拟货币场外交易可能涉及非法经营或洗钱罪

#### 3. 民商事争议

- 虚拟货币买卖合约可能被认定无效
- 委托代投关系中的资金风险需明确约定权责

### 全球监管趋势

#### 主要地区监管框架

- **美国**：SEC/CFTC/OCC多头监管，支持银行提供加密服务
- **欧盟MiCA**：2024年生效，全球首个全面加密监管框架
- **香港**：VASP制度要求交易所持牌，开放零售交易

#### FATF旅行规则

- 要求VASP在转账时收集发送方/接收方信息
- 适用于超过1000美元/欧元的转账
- 对DeFi/DEX提出合规挑战

### 入职法律风险防范

#### 雇佣关系风险

- 分布式办公可能导致劳动关系认定困难
- 缺乏社保公积金影响落户/购房/子女教育等权益
- 建议通过EOR(名义雇主)模式签署合规合同

#### 薪酬结构风险

- "人民币+Token"或"全USDT"模式可能违反《劳动法》
- 代币价值波动剧烈可能导致收入大幅缩水
- 出金过程可能卷入洗钱风险，需谨慎选择渠道

#### 项目合法性审查

- 审查项目白皮书、Token分发机制、收益模型
- 确认是否面向中国大陆用户，是否含返利结构
- 警惕跨境分区运营项目的刑事风险

## 四、典型攻击案例与安全防护

### 刑事犯罪案例

#### 1. 开设赌场罪(BigGame案例)

- **作案手法**：在EOS区块链开发赌博平台，推出"掷骰子"等游戏
- **涉案金额**：3.3亿余个EOS币赌资，322万余个EOS币盈利
- **判决结果**：主犯获刑5年9个月/4年6个月，从犯获缓刑

#### 2. 非法经营罪(TW711平台案例)

- **作案手法**：以USDT为媒介提供外币与人民币汇兑服务
- **涉案金额**：非法兑换人民币2.2亿余元
- **判决结果**：主犯获刑5年，共同犯罪人获刑3年3个月

#### 3. 非法吸收公众存款罪(AIP平台案例)

- **作案手法**：通过"矿机"释放AIP积分兑换USDT的模式吸收资金
- **涉案金额**：1.16亿元
- **判决结果**：帮助宣传推广者被追究刑责

#### 4. 组织、领导传销活动罪(PlusToken案例)

- **作案手法**：以区块链为噱头，通过层级返利发展会员
- **涉案金额**：特大传销案
- **判决要点**：技术开发人员被认定为共犯

#### 5. 洗钱罪(虚拟货币跨境兑换案例)

- **作案手法**：利用虚拟货币跨境转移犯罪所得
- **判决要点**：上游犯罪未判决不影响洗钱罪认定

### 网络安全风险与案例

#### 1. 钓鱼攻击

- **案例**：假冒面试通知诱导安装恶意软件，窃取钱包信息
- **防护**：核实发件人域名，不点击可疑链接

#### 2. 恶意软件

- **案例**：虚假"视频会议软件"植入木马，盗取钱包文件
- **防护**：只从官方渠道下载软件，安装前查杀病毒

#### 3. 社交工程

- **案例**：冒充学长提供"实习内推"，诱导钱包验证
- **防护**：多方核实身份，不轻信"紧急"要求

#### 4. 供应链攻击

- **案例**：恶意浏览器插件替换转账地址
- **防护**：限制插件数量，只安装知名开发者作品

#### 5. 剪贴板劫持

- **案例**：木马监控剪贴板，替换钱包地址
- **防护**：转账前核对地址前后几位，使用硬件钱包

### 安全防护清单

1. **身份验证**：启用2FA认证，优先选择硬件密钥
2. **密码管理**：使用密码管理器，避免密码复用
3. **钱包安全**：助记词离线保存，不截图不上传
4. **转账验证**：核对完整地址，小额测试交易
5. **软件安装**：只从官方渠道下载，检查数字签名
6. **社交防范**：不轻信群内"官方人员"，多方核实信息
7. **邮件警惕**：查看来信域名，不点击可疑附件
8. **系统防护**：定期更新系统，安装可靠安全软件

# 2025-08-06

今天参加了晚上的Web3故事会，了解到了很多故事和ETH、EOS的发展。体会到了一个项目的成功不只是看技术前景，还要看治理机制和社区发展。

然后在unphishable上学习了钓鱼攻防挑战，做完了beginner的挑战，了解了包括助记词泄漏、无限授权钓鱼、tg链接钓鱼、仿冒官网钓鱼等等常见的钓鱼场景，提高了对链上安全的认知和重视。

# 2025-08-05

今天参加了晚上的Web3钓鱼攻击安全分享会议，学习了Web3中针对钱包的钓鱼攻击相关的知识和案例。
攻击场景包括助记词泄漏、无限授权、空投诈骗、假冒代币空投、伪造签名等。
为了防范钓鱼攻击，几个关键防御点：
- 仔细核对网址和钱包地址
- 谨慎授权智能合约
- 助记词绝不联网

下一步会在https://unphishable.io/challenges上进行学习。

# 2025-08-04

### 区块链基础学习笔记

#### 1. 区块链是什么？

- **定义**：去中心化的分布式账本技术，用于安全、透明、不可篡改地记录交易数据。

- 

  区块结构

  ![区块结构示意图](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250804220958754.jpg)

  - 包含交易记录和前一区块的哈希值。
  - 区块容量有限，定期打包生成（如每10分钟一个区块）。

- **链式连接**：区块按顺序串联，形成不可篡改的历史记录。

#### 2. 区块链特性

- **不可篡改**：修改历史区块需修改后续所有区块，几乎不可能。
- **公开透明**：所有交易公开，但用户匿名（通过钱包地址）。
- **快速交易**：跨国交易无需中介，自动完成。

#### 3. 分布式网络

- **去中心化**：全球节点共同维护数据，无单一控制者。
- **抗篡改**：需控制51%以上节点才能篡改数据，成本极高。

#### 4. 比特币（BTC）

- **激励机制**：节点通过挖矿获得比特币奖励。
- **货币属性**：总量有限（2100万枚），可自由转账，成为加密货币。

------

### 区块链核心组成

1. 

   去中心化网络

   ![去中心化网络架构](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250804221053123.jpg)

   - 节点提供算力维护网络，存储完整区块链数据。

2. 

   代币激励

   ![区块链代币激励机制](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250804221103554.jpg)

   - 矿工通过挖矿获得代币奖励（如Gas Fee）。
   - 用户需支付代币使用网络服务（交易、智能合约等）。

#### 区块链运行流程

1. 用户发起交易。
2. 交易广播到全网节点。
3. 节点验证交易合法性。
4. 矿工打包交易成新区块。
5. 新区块链接到区块链。
6. 矿工获得代币奖励。

![区块链生态系统运行流程](https://adurey-picture.oss-cn-chengdu.aliyuncs.com/img/20250804221133926.jpg)

------

### 区块链类型

| **类型**   | **特点**                                     | **适用场景**       |
| ---------- | -------------------------------------------- | ------------------ |
| **公链**   | 完全开放，任何人可参与（如比特币、以太坊）。 | 加密货币、公共存证 |
| **联盟链** | 需授权加入，部分去中心化（如企业合作链）。   | 供应链、金融协作   |
| **私链**   | 中心化控制，权限严格（如企业内部链）。       | 内部管理、审计     |

------

### Web3 vs Web2 vs Web 3.0

| **维度**     | **Web2**           | **Web 3.0**       | **Web3**    |
| ------------ | ------------------ | ----------------- | ----------- |
| **控制权**   | 平台垄断（如谷歌） | 部分开放          | 用户自治    |
| **数据存储** | 中心化服务器       | 混合存储          | 区块链/IPFS |
| **支付系统** | 信用卡/支付宝      | 集成支付          | 加密货币    |
| **典型技术** | JavaScript         | RDF/OWL（语义网） | 智能合约    |

#### Web3核心创新

- **数据主权归用户**：资产（如NFT）真正属于用户，平台无法删除。
- **无需信任中介**：智能合约自动执行规则。
- **应用可组合**：DeFi协议可被其他应用直接调用（如“搭积木”）。

------

### 以太坊（Ethereum）

#### 1. 基本介绍

- **定位**：去中心化计算平台（“世界计算机”），支持智能合约。
- 核心组件
  - **钱包**（EOA）：用户控制的账户。
  - **DApp**：去中心化应用。
  - **智能合约**：自动执行的代码。
  - **区块链**：存储交易和合约数据。

#### 2. 以太坊 vs 比特币

| **维度**     | **比特币**           | **以太坊**             |
| ------------ | -------------------- | ---------------------- |
| **目标**     | 数字黄金（存储价值） | 可编程平台（智能合约） |
| **编程能力** | 有限脚本             | 图灵完备（Solidity）   |
| **共识机制** | PoW（工作量证明）    | PoS（权益证明）        |
| **交易速度** | 10分钟/区块          | 12秒/区块              |

#### 3. 以太坊升级（The Merge）

- **PoW → PoS**：降低能耗99.95%，验证者需质押32 ETH。
- 未来方向
  - **分片技术**：提升吞吐量（数据分片优先）。
  - **EIP-4844**：降低Layer2费用（Blob交易）。
  - **ZK-Rollup**：零知识证明提升效率（如zkSync）。

------

### DeFi（去中心化金融）

#### 1. Uniswap（DEX）

- **AMM机制**：通过公式 `x * y = k` 自动定价。
- **流动性池**：用户存入代币成为LP，赚取手续费（如0.3%）。

#### 2. Compound（借贷）

- **超额抵押**：借出资产需抵押更高价值的资产（如抵押ETH借DAI）。
- **清算风险**：抵押物价值下跌可能触发强制卖出。

#### 3. MakerDAO（稳定币）

- **DAI生成**：抵押ETH等资产生成1:1锚定美元的DAI。
- **品牌升级**：2024年更名为Sky，代币升级为USDS（稳定币）和SKY（治理代币）。

------

### NFT与DAO

#### 1. NFT（数字所有权）

- 案例
  - **CryptoPunks**：首个NFT项目（像素头像）。
  - **OpenSea**：最大NFT交易平台。
- **特点**：唯一性、可验证所有权、智能合约版税。

#### 2. DAO（去中心化自治组织）

- 案例
  - **Nouns DAO**：每日拍卖NFT，收益用于社区治理。
  - **ConstitutionDAO**：众筹4700万美元竞拍美国宪法副本（未成功）。
- **特点**：社区投票决策，资金透明分配。

------

### 2025年趋势

1. **Intent-Based交易**：用户声明目标（如“最优价格”），系统自动执行。
2. **账户抽象（AA）**：智能合约钱包（社交恢复、Gas代付）。
3. **模块化区块链**：分离执行、共识、数据可用性层（如Celestia）。
4. **AI+Web3**：去中心化AI训练、AI代理自动化交易（如Fetch.ai）。

------

### 总结

- **区块链**：不可篡改的分布式账本，核心是去中心化和激励机制。
- **以太坊**：智能合约平台，通过PoS和Layer2解决扩展性问题。
- **DeFi/NFT/DAO**：金融、所有权和治理的创新模式。
- **未来**：更高效（模块化）、更易用（AA）、更智能（AI）。


# 2025.07.29


<!-- Content_END -->
