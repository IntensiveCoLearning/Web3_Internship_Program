---
timezone: UTC+8
---

# Link

**GitHub ID:** Link-990

**Telegram:** @15270969921

## Self-introduction

学习web3

## Notes

<!-- Content_START -->
# 2025-08-10

uint8 a = 5;
uint b = 6;
// 将会抛出错误，因为 a * b 返回 uint, 而不是 uint8:
uint8 c = a * b;
// 我们需要将 b 转换为 uint8:
uint8 c = a * uint8(b)；
总结：a * b 返回类型是 uint, 但是当我们尝试用 uint8 类型接收时, 就会造成潜在的错误。如果把它的数据类型转换为 uint8, 就可以了，编译器也不会出错。

# 2025-08-09

在 Solidity 中，学习了数学运算
加法: x + y减法: x - y,乘法: x * y 除法: x / y
了解了Solidity 定义的函数的属性默认为公共。 这就意味着任何一方 (或其它合约) 都可以调用你合约里的函数。
明白了，不是什么时候都需要这样，而且这样的合约易于受到攻击。 所以将自己的函数定义为私有是一个好的编程习惯，只有当你需要外部世界调用它时才将它设置为公共。
了解Keccak256 和 类型转换

# 2025-08-08

继续学习solidity基础语法

# 2025-08-07

学习了solidity的基础语法

# 2025-08-06

学习了区块链入门基础


# 2025.07.29


<!-- Content_END -->
