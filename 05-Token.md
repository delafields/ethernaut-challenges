## Explanation

If you know what `uint` stands for then you know how to solve this one.

The goal of this challenge is to *"somehow manage to get your hands on any additional tokens". Logically, we then go and look at the `Token` contract's transfer function which has an \*interesting\* `require`:
```javascript
require(balances[msg.sender] - _value >= 0);
```

What's interesting about this? Well, its hackable.

You see, `balances[msg.sender]` and `_value` are both unsigned integers, `uint`s. This means if you subtract `value` from `balances[msg.sender]`, even if `balances[msg.sender]` < `value`, the result will be be positive. Why? **underflow**

### Integer overflow/underflow
---
```javascript
/** OVERFLOW **/
uint8 max = 255;
max++;
// result: max = 0

/** UNDERFLOW **/
uint min = 0;
uint--;
// result: min = 255
```
In layman's terms, `uint`s have minimum/maximum values that they can hold and if you go over or under those thresholds, they will "roll over".

## Solution
---
Let's underflow our mf `balance`. Running `await contract.balanceOf(player)` in our browser console tells us we have 20 tokens, so if we send 21 tokens somewhere, we'll underflow our `balance` into a value of `255`😎. This specific action happens after the `require` call: `balances[msg.sender] -= _value;`.

To solve, run
```javascript
// <SOME OTHER ADDRESS> should be some wallet address
// that isn't the one your're connected with
await contract.transfer(<SOME OTHER ADDRESS>, 21);
```

^ We use a different address for the param `_to` because otherwise we'd overflow our `balance` AFTER underflowing it when the `transfer` method runs `balances[_to] += _value;`