# Solidity

Solidity是一种智能合约高级语言，运行在EVM虚拟机上



## 常见的字段：

`view`：不能修改变量，只能读取，不消耗gas

`pure`：输入数据，直接读取，不消耗gas，不能访问状态变量

 

`payable`：必须要使用payable关键字代表我们可以通过这个函数给合约地址进行转账充值

```javascript
pragma solidity ^0.4.0;

contract payableTest{
    
    function pay() payable{
        
    }
    
    function getBalance()  view returns(uint){
        return this.balance;
    }
    
    //0x64eBc7D246699db943917e8be1eB12C28B2aE322
    function getThis() view returns(address){
        return this; 
        //this就代表当前的合约地址  
        //address: 0x64eBc7D246699db943917e8be1eB12C28B2aE322
    }
    
    function getrandBalance(address a) view returns(uint){
        return a.balance;
    }
    
    function transfer() payable{
        address account = 0x17F6AD8Ef982297579C203069C1DbfFE4348c372;
        account.transfer(msg.value);
    }
    
    function transfer2() payable{
        this.transfer(msg.value);
    }
    
    //回滚函数 
    function() payable{
        
    }
   
       
}
```



## 全局变量

```
block.blockhash(uint blockNumber) returns (bytes32)：指定区块的区块哈希——仅可用于最新的 256 个区块且不包括当前区块；而 blocks 从 0.4.22 版本开始已经不推荐使用，由 blockhash(uint blockNumber) 代替

block.coinbase (address): 挖出当前区块的矿工地址

block.difficulty (uint): 当前区块难度

block.gaslimit (uint): 当前区块 gas 限额

block.number (uint): 当前区块号

block.timestamp (uint): 自 unix epoch 起始当前区块以秒计的时间戳

gasleft() returns (uint256)：剩余的 gas

msg.data (bytes): 完整的 calldata

msg.gas (uint): 剩余 gas - 自 0.4.21 版本开始已经不推荐使用，由 gesleft() 代替

msg.sender (address): 消息发送者（当前调用）

msg.sig (bytes4): calldata 的前 4 字节（也就是函数标识符）

msg.value (uint): 随消息发送的 wei 的数量

now (uint): 目前区块时间戳（block.timestamp）

tx.gasprice (uint): 交易的 gas 价格

tx.origin (address): 交易发起者（完全的调用链）

```



