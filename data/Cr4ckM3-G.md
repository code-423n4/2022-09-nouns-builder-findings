# Gas Opt. Use memory cached variable.

https://github.com/code-423n4/2022-09-nouns-builder/blob/HEAD/src/auction/Auction.sol#L167-L181

```
    function _settleAuction() private {
        // Get a copy of the current auction
        Auction memory _auction = auction;

        // Ensure the auction wasn't already settled
        if (auction.settled) revert AUCTION_SETTLED();
        //  ^^^^^^  We can use `_auction` instead to save gas.

        // Ensure the auction had started
        if
```