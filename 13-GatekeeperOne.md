> Make it past the gatekeeper and register as an entrant to pass this level.

This contract requires passing through three modifier `Gate`s.

Modifier `GateOne` seems easy enough, if you recall the `Telephone` lesson, we'll just have to call `enter()` from another contract.

`GateTwo` seems pretty easy as well, send enough gas so that `gasleft() % 8191 == 0`.

`GateThree` seems a little tricker. Three requires with a bunch of `uint` casting. We could try brute forcing this, but that wouldn't be any fun.

My attention immediately focuses on the third require: 
```javascript
uint32(uint64(_gateKey)) == uint16(tx.origin)
```
This hints to us that this isn't some random `bytes8` that we need to send `enter()`. The key is passing whatever `uint16(txn.origin)` is.