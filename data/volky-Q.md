# _addFounders allow founders to own all tokens

The _addFounders method [allows setting the founders percent ownership to 100%](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L88), which then makes minting tokens impossible because it [hangs on an infinite loop](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L152-L157).

To fix that and reject the contract creation with 100% funders ownership, [this line](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L88) should be changed to
```solidity
if ((totalOwnership += uint8(founderPct)) >= 100) revert INVALID_FOUNDER_OWNERSHIP();
```
That is, use a `>=` comparison instead of `>`


# _isForFounder function name is misleading

The [name and return type of the function](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L177) makes it look like it is read-only and has no side effects on the contract, while it actually [mints tokens](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L188).

I suggest changing it to `_performScheduledMint` to be consistent with the terminology used on other parts of the contract

# Unused ITreasury error EXECUTION_EXPIRED

The [EXECUTION_EXPIRED error](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/ITreasury.sol#L49) was declared but never used

# use string.concat for concatenation on MetadataRenderer

use [string.concat](https://docs.soliditylang.org/en/v0.8.15/types.html#string-concat) instead of abi.encodePacked where simple string concatenation is needed for better readability:

- https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L208-L213
- https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L243-L244
- https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L258-L260
- https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L272-L280
- https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L290-L303
- https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L309
