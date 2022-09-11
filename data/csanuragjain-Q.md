## User can overwrite other user bids

Contract:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331

## Impact
If admin sets minimumBidIncrement to 0 percent then one User can overwrite other user bids without need to provide extra funds

## Proof of Concept

1. Assume that settings.minBidIncrement is currently set to 0 by Admin
2. User A creates bid on the auction with amount X
3. Since settings.minBidIncrement is 0, so User B only needs to provide amount X which will replace User A bid with User B bid

## Recommended Mitigation Steps
Add a check to make sure settings.minBidIncrement>0

```
require(settings.minBidIncrement>0, "Incorrect increment");
```

## Auction can be extended to an indefinite amount of time

Contract:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323

## Impact
Auction can be extended to an indefinite amount of time

## Proof of Concept

1. Assume that settings.timeBuffer is currently set to a 1000 days by Admin
2. User A creates bid on the auction with amount X
3. Since settings.timeBuffer is 1000 days, so new Auction can only be created post 1000 days

## Recommended Mitigation Steps
There should be a max cap on timeBuffer