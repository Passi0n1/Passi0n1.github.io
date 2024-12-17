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

        另外，如果foo的复制不是从myArray复制，而是从新的memory中进行复制，那么就会在slot中开辟空间存储数据。比如：
            Foo memory temp;
            temp.bar = 1;
            Foo storage foo = temp;
            如果是从已经有的storage中赋值，那么就是变量的指向。