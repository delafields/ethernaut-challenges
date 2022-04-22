> To solve this level, you only need to provide the Ethernaut with a Solver, a contract that responds to whatIsTheMeaningOfLife() with the right number.
>
> Easy right? Well... there's a catch.
>
> The solver's code needs to be really tiny. Really reaaaaaallly tiny. Like freakin' really really itty-bitty tiny: 10 opcodes at most.

If you don't get the `MeaningOfLife()` bit/can't read the comment, go read The Hitchhiker's Guide to the Galaxy.

The goal here is simple, write some EVM bytecode that sends **42** to `whatIsTheMeaningOfLife()`. If you've never interacted with EVM opcodes, I highly recommend going through [evm puzzles](https://github.com/fvictorio/evm-puzzles) or playing with [evm codes](https://www.evm.codes/).

It's also probably worth reading this challenge's author's post on [deconstructing a Solidity contract](https://blog.zeppelin.solutions/deconstructing-a-solidity-contract-part-i-introduction-832efd2d7737) to understand what's happening under the hood.

To solve this one we're going to have to get super shadowy and write some [Yul(https://docs.soliditylang.org/en/v0.8.9/yul.html)] 😎.

## Writing bytecode

<!-- https://medium.com/coinmonks/ethernaut-lvl-19-magicnumber-walkthrough-how-to-deploy-contracts-using-raw-assembly-opcodes-c50edb0f71a2 -->
<!-- https://cmichel.io/ethernaut-solutions/ -->

<!-- pragma solidity ^0.7.3;

interface IMagicNum {
    function setSolver(address _solver) external;
}

contract MagicNumAttacker {
    IMagicNum public challenge;

    constructor(address challengeAddress) {
        challenge = IMagicNum(challengeAddress);
    }

    function attack() public {
        bytes memory bytecode = hex"600a600c600039600a6000f3602a60005260206000f3";
        bytes32 salt = 0;
        address solver;

        assembly {
            solver := create2(0, add(bytecode, 0x20), mload(bytecode), salt)
        }

        challenge.setSolver(solver);
    }
} -->