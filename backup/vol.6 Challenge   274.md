```
// @CalyptusCareers - Solidity Challenge
contract DoesWrite {
    struct Foo {
        uint256 bar;
    }

    Foo[] public myArray;

    constructor() {
        // Initialize array with dummy data
        myArray.push(Foo({bar: 1}));
        myArray.push(Foo({bar: 2}));
    }

    function moveToSlot0() external returns (uint256) {
        Foo storage foo = myArray[0];
        foo = myArray[1];
        return myArray[0].bar;
    }
}
```
**解析：**

      myArray.push(Foo({bar: 1}));的含义指的向myArray数组中加一个foo结构体的数据，内容是把bar赋值为1，下一行同理
        另外Foo storage foo = myArray[0];这里需要注意 storage 。如果没有storage字段，Foo  foo = myArray[0];，变量foo仅仅是一个内存变量，随着函数的调用而生死。

      而加上storage，则是成为指向实际slot的变量了。  这个时候再去操作foo，是无法进行修改的，只是操作storage的指向。

        所以如果调用函数moveToSlot0的话，返回的是myArray中的第一个结构体，即bar = 1 。
        
       
**问：为什么这里已经指向了myArray[0]，还是在后续的操作foo的时候，却不能修改myArray[0]的内容**

这需要考虑到赋值前后的变量类型、是否全局变量、赋值方式：
1、如果是针对于数据元素的显示赋值，那就是直接操作myArray[0]的数据了
2、如果是都是全局storage ，那就是数据复制，也可以修改myArray[0]的数据
3、如果是memory到storage，那也是数据复制，也可以修改myArray[0]的数据。

这些操作都是为了尽可能的节省资源和安全防护。
PS：记起来真的很繁琐啊。


