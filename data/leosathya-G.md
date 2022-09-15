## [G-01] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

There is 4 instance of this issue:

> **File : Token.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L88
>
> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#280
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#285
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#290


#### **Mitigation**

for example :: 

> if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();

#### To

> if ((totalOwnership = totalOwnership + uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();




## [G-02] >= COSTS LESS GAS THAN >

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

There is 5 instance of this issue:

> **File : Auction.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L106
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L122
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L141
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L346
>
> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#128



## [G-03] VARIABLE DECLARED AND THEN INITIALIZED

Here "founder" variable first initialized to its default value, then after again changed to other value and checked in if condition

> **File : Manager.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L114-L117  

#### **Mitigation**

> address founder;
> if ((founder = _founderParams[0].wallet) == address(0)) revert FOUNDER_REQUIRED();

#### To

> address founder = _founderParams[0].wallet;
> if ((founder) == address(0)) revert FOUNDER_REQUIRED();


## [G-04] USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENT IN EXTERNAL FUNCTIONS CAN SAVE GAS

> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L195
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L117-L120



## [G-05] USING BOOLS FOR STORAGE INCURS OVERHEAD

 Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the 
 slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and
 pointer aliasing, and it cannot be disabled.
	
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Asset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L264

Use uint256 in **hasVoted** mapping instead bool 


## [G-06] REPEATING SAME CODE IN DIFFERENT CONTRACTS

hashProposal() code repeated in 2 contract files

> **File : Treasury.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L98-L105
>
> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L101-L108

#### **Mitigation**

shift these code of hashProposal() to a separate library, and this library in above contract files


## [G-07] EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

_authorizeUpgrade() function is just blank block which doing nothing,
At least it should emit a events when Implemented address changes   

> **File : Manager.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209-L210


## [G-8] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 


> **File : Governer.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#564
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#572
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#580
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#588
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#596
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#605
>
> **File : Treasury.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L98-L116
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L98-L180
>
> **File : Manager.sol**
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L187
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L196
> https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209
>

