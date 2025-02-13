## Storage
storage 存储的是声明定义的数据。

slot 是存储在 storage 中的，一个 slot 可以存储 256 位（32 字节）的大小。
storage 没有上限，但是 gas 会很高。
[Solidity 中 Storage](https://blog.csdn.net/rfrder/article/details/115706983)

## Memory
memory 存储的是内存中的数据。也就是过程中的数据。

看一个例子：
在这个例子中间的信息，过程性的信息、局部变量就是存储在 Memory
```solidity
pragma solidity ^0.5.17;

contract SimpleContract {
    uint256 public storedData; // Storage

    function set(uint256 x) public {
        uint256 tempData = x + 10; // Memory
        storedData = tempData; // Storage
    }
}

```


<font color="#ff0000">在函数中定义的局部变量是存储在哪里的呢？</font>
函数中也是可以定义变量的，但是这个变量是存储在 memory 中的，而不是 storage。

## Stack
stack 是伴随着 memory 而产生和结束的，但是两者并非是一方包括一方。而是 memory 属于临时数据的存储，而 stack 而伴生在其左右，用来跟进函数的执行。

也就是，memory 之于 stack，就像 storage 之于 memory。 

stack 这种后进先出的结构就是会从 memory 中拿数据，进而跟进函数的执行过程。

```solidity
pragma solidity ^0.5.17;

contract StackExample {
    function functionA(uint256 x) public pure returns (uint256) {
        uint256 y = x + 5;
        uint256 result = functionB(y);
        return result;
    }

    function functionB(uint256 a) internal pure returns (uint256) {
        uint256 b = a * 2;
        return b;
    }
}

```
在这个例子中，当 `functionA` 被调用时，参数 `x` 和局部变量 `y` 都会被置于 Stack 中。然后，当 `functionA` 调用 `functionB` 时，`functionB` 的参数 `a` 以及返回地址（指向 `functionA` 继续执行的位置）也会被压入 Stack。

当 `functionB` 执行完成，它的返回结果 `b` 以及返回地址会从 Stack 中弹出，Stack 的状态就回到了我们首次调用 `functionA` 时的状态，这就使得 `functionA` 可以接着执行。

现在，我们回到 `functionA`，执行完余下的部分（从 `functionB` 返回并将结果赋值给 `result`）。一旦 `functionA` 完成，它的参数和局部变量也会从 Stack 上移除。
  
这是 Stack 在函数调用中发挥作用的例子。Stack 提供了一种灵活、高效的方式来跟踪函数的状态，包括局部变量、参数以及函数调用的嵌套情况。


## calldata

Calldata 相比于 storage 和 Memory 这种固定或者临时存储数据的位置而言，calldata 更像是一种临时工具，只用来在不同的参数当中传递参数。


## 四者之间的比较


Storage（存储）
	1	定义：Storage 是 Solidity 中用于永久存储数据的地方。它对应于以太坊区块链上的状态变量。
	2	特点：
	•	存储在区块链上，每次合约调用都会持久化保存。
	•	访问速度较慢，成本较高。
	•	主要用于存储合约的状态和重要数据。
	
Memory（内存）
	1	定义：Memory 是 Solidity 中用于临时存储数据的地方。它在函数调用期间存在，并在函数执行完毕后销毁。
	2	特点：
	•	存储在内存中，访问速度快，成本较低。
	•	主要用于处理函数参数和局部变量。
	•	数据在函数执行完毕后会被清除。
	
Calldata（调用数据）
	1	定义：Calldata 是 Solidity 中用于传递函数参数的特殊区域。它是一个不可变的字节数组，包含了函数调用的输入数据。
	2	特点：
	•	只读，不可修改。
	•	主要用于外部函数的输入参数。
	•	访问速度快，成本较低。
	•	适用于处理大量数据，因为它不会占用额外的内存空间。
Stack（栈）
	1	定义：Stack 是 Solidity 虚拟机（EVM）中用于执行操作的内部数据结构。它用于临时存储运算过程中的中间结果。
	2	特点：
	•	存储在 EVM 的栈中，访问速度快。
	•	主要用于执行算术运算、逻辑运算和函数调用等操作。
	•	栈的大小有限，通常为 1024 个元素。
	
示例代码
总结
	•	Storage：用于永久存储合约状态。
	•	Memory：用于临时存储函数参数和局部变量。
	•	Calldata：用于传递外部函数的输入参数，只读且高效。
	•	Stack：EVM 内部用于执行操作的数据结构。



这里通过一个例子展示这些存储类型的具体应用
```solidity
pragma solidity ^0.8.0;

contract StorageExample {
    uint256 public storedData; // 存储在storage中

    function setStorageData(uint256 _data) public {
        storedData = _data; // 修改storage中的数据
    }
}

contract MemoryExample {
    function useMemory(uint256[] memory _array) public pure returns (uint256) {
        uint256 sum = 0;
        for (uint256 i = 0; i < _array.length; i++) {
            sum += _array[i]; // 使用memory中的数组
        }
        return sum;
    }
}

contract CalldataExample {
    function processCalldata(uint256[] calldata _array) external pure returns (uint256) {
        uint256 sum = 0;
        for (uint256 i = 0; i < _array.length; i++) {
            sum += _array[i]; // 使用calldata中的数组
        }
        return sum;
    }
}
```


总结一下：
- **默认参数类型**：未声明类型的参数默认是 `memory`。
- **Storage 变量**：需要显式声明。
- **External 函数参数**：推荐使用`calldata`，尤其是对于复杂类型，但标量类型可以使用`memory`或`calldata`。

补充：
复杂类型（Complex Types）
复杂类型通常指的是包含多个值或者具有内部结构的数据类型。在Solidity（一种智能合约编程语言）中，复杂类型包括但不限于：

结构体（Structs）
数组（Arrays）
映射（Mappings）
枚举（Enums）
这些类型通常占用更多的内存空间，并且在函数调用时可能需要更多的处理来传递它们的状态。

标量类型（Scalar Types）
标量类型指的是单一的值，它们是最基本的数据类型。在Solidity中，标量类型包括：

整数（如 int, uint）
布尔值（bool）
地址（address）
固定大小字节数组（如 bytes1, bytes32）