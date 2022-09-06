Gas

## 1 Packed vs unpacked structure
# Description:
In ethereum, you pay gas for every storage slot you use. A slot is of 256 bits, and you can pack as many variables as you want in it. Packing is done by solidity compiler and optimizer automatically, you just need to declare the packable functions consecutively.
The below code is an example of poor code and will consume 3 storage slot

uint8 numberOne;
uint256 bigNumber;
uint8 numberTwo;
A much more efficient way to do this in solidity will be

uint8 numberOne;
uint8 numberTwo;
uint256 bigNumber;
This small change will save you a lot of gas as it will now only need 2 slots to store (It takes 20k gas to store 1 slot of data). These small things are what make solidity different from other languages. Stuff like the order of variables never mattered as much in other languages when it came to optimization.

# Code references where this can be corrected:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L14
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L29
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L16


