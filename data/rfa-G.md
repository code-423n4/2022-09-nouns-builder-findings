GAS

## [G-1] SLOAD `auction`

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L172

`auction` was cached in memory at L169. We can just call `_auction.settled` to save gas by avoiding SLOAD


## [G-2] Using calldata on read only parameter

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L99-L101
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L117-L120
