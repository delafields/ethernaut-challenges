> Make it past the gatekeeper and register as an entrant to pass this level.

This contract requires passing through three modifier `Gate`s.

Modifier `GateOne` seems easy enough, if you recall the `Telephone` lesson, we'll just have to call `enter()` from another contract.

`GateTwo` seems sorta easy, send enough gas so that `gasleft() % 8191 == 0`. We'll have to be aware of how much gas we've consumed up to this point. I failed to crack this one so I ended up consulting a [guide](https://medium.com/coinmonks/ethernaut-lvl-13-gatekeeper-1-walkthrough-how-to-calculate-smart-contract-gas-consumption-and-eb4b042d3009) (which I needed for `GateThree` anyway).
* The trick here is having your Remix's Solidity version === the Ethernaut contract's Solidity version.

As a non bytecode lord `GateThree` is tough to parse. I've recently been playing with [evm codes](https://github.com/fvictorio/evm-puzzles) and `xor`ing some bytes but this one was beyond me. I leaned on the guide I linked above which lays out the properties that `_gateKey` must have:
* (first `require`) - `0x11111111 == 0x1111`, which is only possible if the value is masked by `0x0000FFFF`
* (second `require`) - `0x1111111100001111 != 0x00001111`, which is only possible if you keep the preceding values, with the mask `0xFFFFFFFF0000FFFF`

So, we can cover both of those byte masks with the latter, `0xFFFFFFFF0000FFFF`

## Solution
1. Create a `GateOpener` contract and instantiate the Ethernaut contract
```javascript
contract GateOpener {
    GatekeeperOne gate = GatekeeperOne(<YOUR CONTRACT INSTANCE>);
}
```
2. Create a byte masked key to solve gate three
```javascript
    bytes8 key = bytes8(tx.origin) & 0xFFFFFFFF0000FFFF;
```

3. Pass enough gas to the contract to pass gate 2
* The most simple way to get the amount of gas needed is by stepping through the Remix debugger until we get to the opcode that highlights `msg.gas% 8191`
* Count the remaining gas and work backwards to calculate the correct original gas amount
```javascript
function enterGate() public {
    gate.call.gas(56348)(bytes4(keccak256('enter(bytes8)')), key);
}
```
Call this function and enter the gate!