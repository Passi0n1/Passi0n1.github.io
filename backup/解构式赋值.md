解构式指的是将多维、多个的参数位同时批量（当然，也可以人为筛选掉一些参数不去进行复制）
```
uint256 _number;
bool _bool;
uint256[3] memory _array;
(_number, _bool, _array) = returnNamed();
```
在这个案例当中，先声明变量，然后将 `returnNamed` 运行的结果反馈赋值到这三个参数上面。

我们随意把这个案例完善一下，看一下完整的代码
```solidity
pragma solidity ^0.8.0;

contract DestructuringExample {
    function returnNamed() public pure returns (uint256, bool, uint256[3] memory) {
        return (42, true, [1, 2, 3]);
    }

    function testDestructuring() public pure returns (uint256, bool, uint256[3] memory) {
        uint256 _number;
        bool _bool;
        uint256[3] memory _array;
        (_number, _bool, _array) = returnNamed();
        return (_number, _bool, _array);
    }
}
```
这个时候则比较清晰了。那么是不是解构式赋值一定要这样的同时多个参数都赋值呢？ 不是的
看这个例子则是可以只读取部分的值进行复制
`(, _bool2, ) = returnNamed();`




解构式赋值规则只能用来处理元组么还可以应用到别的参数类型上么？

1. 数组

对于数组，解构式赋值可以用于提取数组中的元素。

```solidity
uint256[3] memory myArray = [1, 2, 3];
uint256 a, b, c;
(a, b, c) = myArray;
```

2. 结构体（struct）

对于结构体，解构式赋值可以用于提取结构体中的字段。

```solidity
struct Person {
    address addr;
    uint256 age;
    string name;
}

Person memory p = Person(address(0x123), 30, "Alice");
address addr;
uint256 age;
string memory name;
(addr, age, name) = p;
```

3. 映射（mapping）

对于映射，解构式赋值不能直接应用于整个映射，但可以应用于映射中的单个元素。

```solidity
mapping(uint256 => string) public myMapping;
myMapping[1] = "Alice";
myMapping[2] = "Bob";

string memory name1;
string memory name2;
(name1, name2) = (myMapping[1], my```ity[2]);
```

这种算是一种通过取巧的方式实现复杂的赋值，第一次查阅会觉得不理解是正常的。