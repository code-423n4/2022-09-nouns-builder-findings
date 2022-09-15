## Missing Parameter Validation in Token.sol initializer 

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L56

the `founder` parameter is not being checked if it contains length > 0 (this will make `totalOwnership` to be always 0