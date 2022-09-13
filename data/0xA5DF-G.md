## Use cached variable instead of storage
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L172

The code at the link reads the storage var instead of the var cached at the line beforehand. This costs an additional 100 gas units.

```diff
        // Get a copy of the current auction
        Auction memory _auction = auction;

        // Ensure the auction wasn't already settled
-        if (auction.settled) revert AUCTION_SETTLED();
+        if (_auction.settled) revert AUCTION_SETTLED();
```

## Use off-chain calculation + on-chain verification instead of on-chain binary search
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L93

This one requires a bit of a change to the ABI and require the users to pre-calculate a parameter, but can save a significant amount of gas (a few thousands, depending on the length of `accountCheckpoints`).

Instead of using an on-chain binary search, you can calculate the right checkpoint index off-chain and send it as a parameter to the function (which will verify on-chain this is the right point), this can save a nice amount of gas since verifying will cost only 2 `sload`s (each `sload` can cost up to 2.1K gas units) instead of log2(accountCheckpoints.length).


## Caching `block.timestamp` costs more gas
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L211

The timestamp opcode costs only 2 gas units, while dup opcodes cost 3 (besides the initial `timestamp` opcode to cache the timestamp).
Therefore it's cheaper to just call `block.timestamp` each time than to cache it.

See https://www.evm.codes/ for info about opcodes cost.
