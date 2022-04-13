> The creator of this contract was careful enough to protect the sensitive areas of its storage.
>
>Unlock this contract to beat the level.

Upon my first look at the contract, the goal is clear: we need to call the `unlock()` method with whatever is held in `data[2]`, casted as a `bytes16` variable.

I immediately attempted to try the same approach that worked for lesson 8, Vault. Using my injected web3 provider, I grabbed `data[2]` which I thought would sit in the 5th slot of the storage variable of this contract. Next, I tried calling `unlock()` with this value and it didn't do the trick!

## Storage Optimization
At the time of writing, the intricacies of this challenge were beyond my understanding. What I do know is that there are certain gas efficiencies that occur when you declare state variables of the same types next to one another. Turns out, this helps them get packed into storage slots together!

See this [detailed guide](https://medium.com/coinmonks/ethernaut-lvl-12-privacy-walkthrough-how-ethereum-optimizes-storage-to-save-space-and-be-less-c9b01ec6adb6) for more context on this challenge. Basically, storage for this contract gets slotted as follows:
```javascript
/*
* each storage slot is 32 bytes
*/

// boolean values take up 1 byte
bool public locked = true;

// uint256 takes up 32 bytes
uint256 public ID = block.timestamp;

// uint8 vars take up 1 byte
uint8 private flattening = 10;
uint8 private denomination = 255;

// uint16 takes up 2 bytes
uint16 private awkwardness = uint16(now);

// each bytes32 takes up one slot
// so an array of length 3 takes up are 3 slots
bytes32[3] private data;
```

So
* slot 0: `[locked(1 byte) < 31 bytes open >]`
* slot 1: `[ID(32 bytes)]`
* slot 2: `[flattening(1 byte), denomination(1 byte), awkwardness(2 bytes) < 28 bytes open >]`
* slot 3: `data[0]`
* slot 4: `data[1]`
* slot 5: `data[2]`

## Solution

1. Instantiate your contract instance in the console
```javascript
const myInstance = '<YOUR CONTRACT INSTANCE>'
```
2. Grab the contents of storage slot at index 5
```javascript
const unlockKey = await web3.eth.getStorageAt(
    myInstance,
    5 // data[2]
)
```
3. Cast to bytes16
```javascript
// bytes16 = 16 bytes = 32 hex chars, +2 for 0x prefix
const key16 = unlockKey.slice(0, 34)
```
4. Unlock the contract
```javascript
await contract.unlock(key16)
```