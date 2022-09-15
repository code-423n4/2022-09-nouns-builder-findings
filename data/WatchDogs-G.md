## [Gas - 01] ` >= ` cost more than ` > `

 A single > operator costs less gas as compared to the >= operator.

*Instances of this issue :*

* Auction.sol [L98](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L98)
* Treasury.sol [L89](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L89)
* ERC721Votes.sol [L61](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L61)


## [G - 02] Add payable to functions that won't receive ETH

OnlyOwner functions cannot be called by normal users and will not receive ETH by mistake. To save gas, these functions can be made payable.  

*Instances of this issue :*

* Auction.sol [L244](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L244) [L263](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L263) [L307](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307) [L315](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315) [L323](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323) [L331](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331)
* Governor.sol [L564](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L564) [L572](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L572) [L580](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L580) [L588](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L588) [L596](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L596) [L605](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L605)
* Treasury.sol [L180](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L180)


## [Gas - 03] Using `bool` s for storage incurs overhead 

It costs more to declare storage variables as bools than as uint256.

*Instances of this issue :*

* GovernorStorageV1.sol [L19](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/storage/GovernorStorageV1.sol#L19 )
* ERC721.sol [L38](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L38)
* ManagerStorageV1.sol [L10](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/storage/ManagerStorageV1.sol#L10)
* Auction.sol [L136](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L136) [L349](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L349)
* ERC1967Upgrade.sol [L57](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L57)


## [Gas - 04] For array parameters in functions, it is recommended to use `calldata` over `memory`

When an external function with a `memory` array is called, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`).

*Change the following from `memory` to `calldata` :*

* Governor.sol [L98](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L98) [L116](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L116)
* IGovernor.sol [L134](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L134) [L146](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L146) [L195](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L195) 
* Treasury.sol [L258](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L258)
* ERC1967Upgrade.sol [L35](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L35) [L56](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L56)

## [Gas - 05] No need to explicitly initialize variables with default values

Declaration of variables with default values cost gas. such as

`uint256 a = 0;`  change to → `uint256 a;`


*Instances of this issue :* 

* Treasury.sol [L162](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162)
* MetadataRenderer.sol [L119](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119) [L133](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133) [L189](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189) [L229](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229)


## [Gas - 06] Use Shift Right/Left instead of Division/Multiplication if possible

Change the following from:

`uint256 b = a / 2;`  →  `uint256 b = a >> 1;`

* ERC721Votes.sol [L95](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L95)


## [Gas - 07] Use `++x` instead of `x++`

Use Prefix increment rather than Postfix increment, it saves gas.

*Instances of this issue :*

* Token.sol [L154](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L154) [L91](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L91)
* Governor.sol [L230](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230)


## [Gas - 08] `<x> += <y>` costs more gas than `<x> = <x> + <y>`

Using the addition operator instead of plus-equals saves gas.

*Instances of this issue :*

* Governor.sol [L280](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280) [L285](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285) [L290](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290)
* MetadataRenderer.sol [L140](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140)


## [Gas - 09] Splitting `if()` statements that use `||/&&` saves gas

Whenever the conditional statement uses the `||/&&` operator to revert the same error, it‘s better to split the if statement so that the first condition is executed directly rather than having both conditions executed.

*Instances of this issue :*

* Governor.sol [L239](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L239) [L363](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L363)
* ERC721Votes.sol [L170](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L170)



## [Gas - 10] Internal functions called once can be inlined to save gas

When internal functions are called once in the contract, it is better to inline them rather than declaring because function declarations are expensive.

*Instances of this issue :*

* ERC1967Upgrade.sol [L68](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L68)




## [Gas - 11] Use IsZero EVM opcode for Zero address / Zero value checks

Since there is a direct opcode for this operation, implementing this saves gas.

*Instances of this issue :*

* Auction.sol [L104](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L104) [L175](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L175) [L184](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L184) [L189](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L189) [L248](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L248) 
* Governor.sol [L70](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L70) [L71](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L71) [L135](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L135)
* Treasury.sol [L48](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L48)



## [Gas - 12] Empty block  should be removed or emit something  

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures are added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}`  =>  `if(!x){if(y){...}else{...}}`)

*Instances of this issue :*

* Manager.sol [L209](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209)
* Treasury.sol [L269](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269)





