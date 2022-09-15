## Unbounded array can lead to more gas usage and possibly DOS
In line https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L164, 
no of external call is equal to size of `_targets[]` array. As it is unbounded, so this can lead to DOS.

## Variables can be marked as constants 
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L80-L81
`settings.timeBuffer` and `settings.minBidIncrement` can be marked as constant to save gas, as their value doesn't change

## Storage variables should be cached in local memory instead before emitting to save gas
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L153 : `auction.endTime` should be cached in memory before using in emit() function.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L172 : `proposal` should be cached in memory before emitting
