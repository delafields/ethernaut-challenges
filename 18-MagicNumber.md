> To solve this level, you only need to provide the Ethernaut with a Solver, a contract that responds to whatIsTheMeaningOfLife() with the right number.
>
> Easy right? Well... there's a catch.
>
> The solver's code needs to be really tiny. Really reaaaaaallly tiny. Like freakin' really really itty-bitty tiny: 10 opcodes at most.

If you don't get the `MeaningOfLife()` bit/can't read the comment, go read The Hitchhiker's Guide to the Galaxy.

The goal here is simple, write some EVM bytecode that sends **42** to `whatIsTheMeaningOfLife()`. If you've never interacted with EVM opcodes, I highly recommend going through [evm puzzles](https://github.com/fvictorio/evm-puzzles) or playing with [evm codes](https://www.evm.codes/).

It's also probably worth reading this challenge's author's post on [deconstructing a Solidity contract](https://blog.zeppelin.solutions/deconstructing-a-solidity-contract-part-i-introduction-832efd2d7737) to understand what's happening under the hood.

## Writing bytecode

A quick note [covered in this writeup](https://medium.com/coinmonks/ethernaut-lvl-19-magicnumber-walkthrough-how-to-deploy-contracts-using-raw-assembly-opcodes-c50edb0f71a2) is that a deployment transaction consists of the actual contract bytecode and the initialization code. The short version is that the `return opcode requires the return value to be stored in memory instead of just popping it from the stack.

So our contract bytecode consists of storing **42** in memory and returning a pointer to it:
```assembly
## mstore(p, v) where p is position and v is hexadecimal value
# value is 0x42
v: push1 0x42
# memory slot is 0x80
p: push1 0x80

## then we return this 0x42 value
s: push1 0x20
p: push1: 0x80
return
```
This should return the opcode sequence of **604260805260206080f3**, which is 10 opcodes long.

We then need to handle the intialization opcodes which will replicate our runtine opcodes to memory and return them, using the `codecopy` opcodes.
```assembly
## codecopy takes (t, f, s) where
# t = destination position of the code in memory
# f = position of the runtime opcodes (this one is tricky)
# s: size of the code in bytes (10 for us, 0x0a)
s: push1 0x0a
# only known to us after finishing initialization opcodes
f: push1: 0x0c
t: push1 0x00
CODECOPY

## then return in-memory runtine opcodes
push1 0x0a
push1: 0x00
```

We then have a final bytecode sequence of **0x600a600c600039600a6000f3604260805260206080f3**

## Solution
To solve, pop open devtools. First, we'll deploy the contract from with the bytecode above.
```javascript
> let account = "<YOUR WALLET ADDRESS HERE>";

> let bytecode = "0x600a600c600039600a6000f3604260805260206080f3";

web3.eth.sendTransaction({ from: account, data: bytecode }, function(err,res){console.log(res)});
```

Check the returned transaction to see your created contract.

Then we'll call the contract's `setSolver()` method to secure the win:
```javascript
await contract.setSolver("<YOUR INSTANCE ADDRESS HERE>");
```