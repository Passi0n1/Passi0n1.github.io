**事件**
enevt 是个被定义的事件，主要的功能的对外展示和记录作用，并不是真正的代码的执行。


一个完整的案例：[https://blog.csdn.net/watson2017/article/details/123423834](https://blog.csdn.net/watson2017/article/details/123423834)

```solidity
pragma solidity >=0.6.0 <0.9.0;

contract Demo {
  uint256 demoIndex = 0;
  event DemoIndexInc(uint256 indexed index);
  #这里定义一下时候触发事件时对外展示的信息。
  #顺便补充一下这里的indexed和tx中的log是息息相关的。后续会详细说明的

  function getDemoIndex() public view returns (uint256) {
    return demoIndex;
  }

  function incDemoIndex() public {
    demoIndex = demoIndex+1;
    emit DemoIndexInc(demoIndex);
  }
}
```

而emit就是一个类似于断点一样的东西，或者说是触发点。每当运行到emit的时候就会按照event当中定义的一样把数据整理记录好。
从这角度来看，emit就像是一个类似于接口一样的东西，在emit有了定义之后，tx中的log就按照这个event中的数据来展现，而监听的时候也是从这里获取数据的。



## topic、indexed、与log
学习的原文：[https://www.wtf.academy/solidity-start/Event/](https://www.wtf.academy/solidity-start/Event/)

注意，要跟有event的交互才会有log。。。。。。。

topic中最多三个indexed。
<img width="1175" alt="image" src="https://github.com/user-attachments/assets/394bc6b1-061b-4158-a481-d323b6824bed">
另外emit也可以这样用
```solidity
contract Yeye {
  event Log(string msg);

  // 定义3个function: hip(), pop(), man()，Log值为Yeye。
  function hip() public virtual{
    emit Log("Yeye");
  }

  function pop() public virtual{
    emit Log("Yeye");
  }

  function yeye() public virtual {
    emit Log("Yeye");
  }
}
```

## enevt  emit  监听
用来处理的监听逻辑如下：
```html
<script src="./dist/web3.min.js"></script>
<script type="text/javascript">
  const addEventWatchTx = async () => {
    var web3 = new Web3(ethereum)
    var metaTxContract = new web3.eth.Contract(MetaTxABI, contractAddr)
    metaTxContract.events.DemoIndexInc({
      filter:{},
      fromBlock: 'latest'
    }, function(error, event){})
      .on('data', function(event){
        console.log(event); // same results as the optional callback above
      })
      .on('changed', function(event){
        console.log('emove event from local database');
      })        
      .on('error', console.error);
  }
```


其他的监听案例：[https://blog.csdn.net/qq_37928038/article/details/130200781](https://blog.csdn.net/qq_37928038/article/details/130200781)


## 为什么solidity中要存在 event 和 emit？
event 和 emit 是区块链链接外部世界的渠道。
<img width="698" alt="image" src="https://github.com/user-attachments/assets/80e44148-c996-488f-aa34-a9525bd4eab1">




## 在 solidity 中 event 当中的indexed 如何方便检索的
event 机制更像是一种主动的推送，自我分类机制。在合约执行的时候由于一些原因，触发了这个机制然后主动的在众多交易当中脱颖而出，这个时候就方便前端进行监控发布相关信息，或者方便开发者通过脚本遍历众多庞大的交易筛选出来自己想要的事件。

而 indexed 的存在，让相关的参数更为明显，可以作为筛选的条件被使用。


比如下面的这个例子：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract DepositContract {
    event Deposit(address indexed user, uint256 amount);

    function deposit(uint256 amount) public payable {
        // 存款逻辑...
        emit Deposit(msg.sender, amount);
    }
}
```
这里定义了一个存款的事件


然后前端就可以通过 `Deposit` 进行监控，把相应的信息利用起来。
```js
const { ethers } = require("ethers");

// 连接到以太坊节点
const provider = new ethers.providers.JsonRpcProvider("YOUR_RPC_URL");

// 合约地址和ABI
const contractAddress = "YOUR_CONTRACT_ADDRESS";
const contractABI = [/* ... */]; // 你的合约ABI

// 创建合约实例
const contract = new ethers.Contract(contractAddress, contractABI, provider);

// 监听Deposit事件
contract.on("Deposit", (user, amount) => {
    console.log(`User ${user} deposited ${amount} wei.`);
});


```

测试链怎么去进行监听之类的呢？