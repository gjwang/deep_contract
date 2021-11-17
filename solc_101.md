Solidty 学习笔记

中文社区
https://learnblockchain.cn/

1, 参数打包
https://docs.soliditylang.org/en/v0.8.10/solidity-by-example.html

Packing arguments
Now that we have identified what information to include in the signed message, we are ready to put the message together, hash it, and sign it. For simplicity, we concatenate the data. The ethereumjs-abi library provides a function called soliditySHA3 that mimics the behaviour of Solidity’s keccak256 function applied to arguments encoded using abi.encodePacked. Here is a JavaScript function that creates the proper signature for the ReceiverPays example:

                // recipient is the address that should be paid.
// amount, in wei, specifies how much ether should be sent.
// nonce can be any unique number to prevent replay attacks
// contractAddress is used to prevent cross-contract replay attacks
function signPayment(recipient, amount, nonce, contractAddress, callback) {
    var hash = "0x" + abi.soliditySHA3(
        ["address", "uint256", "uint256", "address"],
        [recipient, amount, nonce, contractAddress]
    ).toString("hex");

    web3.eth.personal.sign(hash, web3.eth.defaultAccount, callback);
}
              

2, solidity 是一门静态语言, 所有新声明变量都有默认值. 
Types ‒ Solidity 0.8.10 documentation

The concept of “undefined” or “null” values does not exist in Solidity, but newly declared variables always have a default value dependent on its type. To handle any unexpected values, you should use the revert function to revert the whole transaction, or return a tuple with a second bool value denoting success.

3, 
- pure functions can be converted to view and non-payable functions
- view functions can be converted to non-payable functions
- payable functions can be converted to non-payable functions

The rule about payable and non-payable might be a little confusing, but in essence, if a function is payable, this means that it also accepts a payment of zero Ether, so it also is non-payable. On the other hand, a non-payable function will reject Ether sent to it, so non-payable functions cannot be converted to payable functions.

4, Enums
Enums cannot have more than 256 members.

5, Data location
Every reference type has an additional annotation, the “data location”, about where it is stored. There are three data locations: memory, storage and calldata. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

Data location and assignment behaviour
Data locations are not only relevant for persistency of data, but also for the semantics of assignments:
- Assignments between storage and memory (or from calldata) always create an independent copy.
- Assignments from memory to memory only create references. This means that changes to one memory variable are also visible in all other memory variables that refer to the same data.
- Assignments from storage to a local storage variable also only assign a reference.
- All other assignments to storage always copy. Examples for this case are assignments to state variables or to members of local variables of storage struct type, even if the local variable itself is just a reference.

6, Private
Everything that is inside a contract is visible to all observers external to the blockchain. Making something private only prevents other contracts from reading or modifying the information, but it will still be visible to the whole world outside of the blockchain.

7, Modifiers
- pure for functions: Disallows modification or access of state.
- view for functions: Disallows modification of state.
- payable for functions: Allows them to receive Ether together with a call.
- constant for state variables: Disallows assignment (except initialisation), does not occupy storage slot.
- immutable for state variables: Allows exactly one assignment at construction time and is constant afterwards. Is stored in code.
- anonymous for events: Does not store event signature as topic.
- indexed for event parameters: Stores the parameter as topic.
- virtual for functions and modifiers: Allows the function’s or modifier’s behaviour to be changed in derived contracts.
- override: States that this function, modifier or public state variable changes the behaviour of a function or modifier in a base contract.

可以升级合约
在编写新版本的合约时，无论是由于新功能还是 bug 修复，都有一个额外的限制需要遵守：你不能改变合约状态变量的声明顺序，也不能改变它们的类型。你可以通过了解 Proxies 来阅读更多关于这个限制背后的原因。

https://cloud.tencent.com/developer/article/1605183