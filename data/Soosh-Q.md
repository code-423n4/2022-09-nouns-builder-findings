## Incorrect Logic for whether Auction has started
The following check in `_settleAuction()` does not correctly check that the auction has started.
```solidity
// Ensure the auction had started
if (_auction.startTime == 0) revert AUCTION_NOT_STARTED();
```
In fact, `_auction.startTime` will never equal zero since it will be set to `block.timestamp` in `_createAuction`.

### Recommendations
The following code will revert if the auction has not started.
```solidity
if (_auction.startTime > block.timestamp) revert AUCTION_NOT_STARTED();
```
In fact, there may be no need for this check. Since the `auction.startTime` is always set to the `block.timestamp` when `_createAuction()` is called, each Auction starts as soon as  `_createAuction()` is called. In other words, the check will always pass.

### Affected
- https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L175