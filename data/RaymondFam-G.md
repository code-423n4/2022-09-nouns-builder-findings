## No Need to Initialize Variables with Default Values
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you will be incurring more gas. Hence, in the for loop of the following instances, refrain from doing i = 0. Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229

## calldata and memory
When running a function we could pass the function parameters as calldata or memory for variables such as strings, structs, arrays etc. If we are not modifying the passed parameter we should pass it as calldata because calldata is more gas efficient than memory. Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L99-L101
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L117-L120
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L195
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L252

## Change string to bytes32 If Possible
The string type is basically equal to bytes so that we can use bytes32 as alternative of string. It’s better to covert string to bytes32 from efficiency perspective, as long as the string length is less than 32. Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L195
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L252

## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number. Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

## Avoid Using += or i++ to Save Gas
Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L91

On line 280 pertaining to `Governor.sol`, for instance, it could have been refactored as follows to save some gas:

```
proposal.againstVotes = proposal.againstVotes + uint32(weight);
```

And, line 91 associated with `Token.sol` could have been rewritten as:

```
uint256 founderId = ++settings.numFounders;
```

## Cache Storage Values in Memory

Anytime you are reading from storage more than once, it is cheaper to cache variables in memory. An SLOAD cost 100 GAS while MLOAD and MSTORE only cost 3 GAS. For instance, the following instance of struct could be cached as local variable prior to updating all respective values:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L77-L81
