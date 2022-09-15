### deadline comparison

There is `castVoteBySig` function in `Governor` contract. It accepts `_deadline` as a parameter and checks it is valid comparing to `block.timestamp`. Specifically, the function performs such checks.

```
// Ensure the deadline has not passed
if (block.timestamp > _deadline) revert EXPIRED_SIGNATURE();
```

There is another logic with non-strict comparison in different parts of the code:

Treasury.sol
```
/// @notice If a proposal is ready to execute (does not consider expiration)
/// @param _proposalId The proposal id
function isReady(bytes32 _proposalId) public view returns (bool) {
    return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
}
```

Auction.sol
```
// Ensure the auction is still active
if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();
```

It is reasonable to have consistent logic in all places -- we propose to use non-strict comparison in `castVoteBySig` function.