- [https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol/#L80](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol/#L80) _owner variable is shadowed by the storage variable. New developer might confuse it with the function parameter.

- remove `nounsDAO` ivar. it is unused [https://github.com/code-423n4/2022-09-nouns-builder/blob/main/test/utils/NounsBuilderTest.sol/#L33](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/test/utils/NounsBuilderTest.sol/#L33)

- add onlyTreasury modifier. This check is used in 3 functions, and could be refactored into a single modifier.
   
    `if (msg.sender != address(this)) revert ONLY_TREASURY();`
    
    [https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol/#L224](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol/#L224)