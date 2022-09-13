## Modded Value Unassigned
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118

The above line of code has a modded value unassigned to another variable, rendering the mod operation unusable.

## Un-indexed Parameters in Events 
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute indexed which will cause the respective arguments to be treated as log topics instead of data. The following links are some of the instances entailed:
 
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L42
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L22
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L22
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L21
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol#L21

## Use safeTransferFrom instead of transferFrom
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L192

ERC721 has both safeTransferFrom and transferFrom, where safeTransferFrom throws an error if the receiving contract's onERC721Received method doesn't return a specific magic number. This will ensure a receiving contract is capable of receiving the token to prevent a permanent loss.

## Zero Value and Zero Address Checks
Implement zero value and zero address checks for all `intialize()` functions where possible to avoid accidental error(s) that could result in non-functional calls associated with it. For instance, the following lines of codes could be refactored as:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L77-L81

```     
        /// Introduce a more verbose custom error if need be
        if (_duration == 0 || _reservePrice == 0 || _treasury == address(0)) revert ADDRESS_ZERO(); 
        settings.duration = SafeCast.toUint40(_duration);
        settings.reservePrice = _reservePrice;
        settings.treasury = _treasury;
        settings.timeBuffer = 5 minutes;
        settings.minBidIncrement = 10;
```
## Modifier for Repeated Use of the Identical if-revert
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L212
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L224
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L280

Consider implementing a modifier in the contract to replace the above identical code lines:

```
modifier onlyTreasury() {
        if (msg.sender != address(this)) revert ONLY_TREASURY();
        _;
    }
```
  