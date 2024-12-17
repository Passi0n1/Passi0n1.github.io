## 1. 存储位置（Storage, Memory, Calldata）
	•	storage：链上的持久化存储，数据是永久保存的。
	•	memory：临时存储，用于函数调用期间，数据不会持久化。
	•	calldata：函数外部调用时的只读数据，通常用于 external 函数中的参数。

## 2. 赋值和引用规则
### 情况 1：Storage 赋值给 Storage
当你将一个 storage 变量赋值给另一个 storage 变量时，是引用赋值：
```
Foo storage foo1 = myArray[0]; // foo1 引用 myArray[0]
Foo storage foo2 = foo1;       // foo2 和 foo1 都引用 myArray[0]
foo2.bar = 42;                 // 修改 foo2 会影响 myArray[0] 的数据

```
总结：
	•	storage 到 storage 是引用赋值。
	•	两个变量会指向同一个存储位置，修改其中一个会影响另一个。

### 情况 2：Memory 赋值给 Memory
当将 memory 变量赋值给另一个 memory 变量时，是值的复制：
```
Foo memory foo1 = Foo({bar: 1});
Foo memory foo2 = foo1;    // 复制 foo1 的数据给 foo2
foo2.bar = 42;            // 修改 foo2 不影响 foo1
```
总结：
	•	memory 到 memory 是数据的复制。
	•	两个变量是独立的，修改一个不会影响另一个。

### 情况 3：Storage 赋值给 Memory
当将 storage 数据赋值给 memory 变量时，是数据的复制：
```
Foo storage foo1 = myArray[0];
Foo memory foo2 = foo1;   // 复制 foo1 的数据到 foo2
foo2.bar = 42;           // 修改 foo2 不影响 myArray[0]
```
总结：
	•	storage 到 memory 是值的复制。
	•	修改 memory 变量不会影响 storage 中的数据。

### 情况 4：Memory 赋值给 Storage
将 memory 数据赋值给 storage 时，是数据的复制：
```

Foo memory foo1 = Foo({bar: 1});
myArray[0] = foo1;   // 复制 foo1 的数据到 myArray[0]
foo1.bar = 42;       // 修改 foo1 不影响 myArray[0]
```
总结：
	•	memory 到 storage 是数据的复制。
	•	修改 memory 变量不会影响 storage 中的数据。

### 情况 5：Calldata 赋值给 Memory 或 Storage
	•	Calldata 到 Memory：数据复制。
	•	Calldata 到 Storage：数据复制。
例如：
```
function setFoo(Foo calldata fooInput) external {
    Foo memory fooTemp = fooInput;  // 数据复制
    myArray[0] = fooInput;          // 数据复制到 storage
}

```
## 3. 总结图表
<img width="721" alt="image" src="https://github.com/user-attachments/assets/67869a45-baa5-4861-b791-1cb7c49b6ced" />


## 总结
	•	storage 到 storage 是引用赋值。
	•	其他情况下（memory、calldata、storage 之间）都是 数据的复制。

当然，还要考虑局部变量和全局变量，以及显性赋值情况。


