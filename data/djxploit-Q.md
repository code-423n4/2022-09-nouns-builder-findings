File: Auction.sol

## Missing 0 address check
Line number: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L39-L42
`manager` and `WETH` are immutable, so they should be checked that they are not 0 addresses.

## All `onlyOwner` functions should be timelocked
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331


