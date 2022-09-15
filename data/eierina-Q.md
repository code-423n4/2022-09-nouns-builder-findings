## Impact

Verified use case with @kulk from the team and "a user can place a bid without attaching any ETH. If that is the only bid then they can win the auction". The current implementation, when reservePrice is zero, allows the next zero bid always win over the previous zero bid.


## Proof of Concept
In createBid, the [minBid](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L119) for the next bid is calculated as follows:

minBid = highestBid + ((highestBid * settings.minBidIncrement) / 100);

When a bidder started with a 0 ETH bid, then the next bidder can win the bid with another 0 ETH bid due to the (highestBid * settings.minBidIncrement) resulting in minBid being still zero.


## Tools Used
n/a

## Recommended Mitigation Steps

A possible solution would be to
change from: [if (msg.value < minBid) revert MINIMUM_BID_NOT_MET();](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L123)
to: if (msg.value < minBid || minBid == highestBid) revert MINIMUM_BID_NOT_MET();
