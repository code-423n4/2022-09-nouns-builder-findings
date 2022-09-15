1.Auction.sol unpause()
Judgment whether the first auction by tokenId = 0, but there tokenId can be equal to 0, it is recommended to add judgment startTime == 0

```
    function unpause() external onlyOwner {
        _unpause();

        // If this is the first auction:
---     if (auction.tokenId == 0) { 
+++     if (auction.tokenId == 0 && auction.startTime == 0) { 
            // Transfer ownership of the contract to the DAO
            transferOwnership(settings.treasury);

            // Start the first auction
            _createAuction();
        }
```

2. Token.sol _addFounders() 
Need to check ownershipPct <100,don't  check uint8(founderPct) after conversion to compare
```
    function _addFounders(IManager.FounderParams[] calldata _founders) internal {
        // Cache the number of founders
        uint256 numFounders = _founders.length;

        // Used to store the total percent ownership among the founders
        uint256 totalOwnership;

        unchecked {
            // For each founder:
            for (uint256 i; i < numFounders; ++i) {
                // Cache the percent ownership
                uint256 founderPct = _founders[i].ownershipPct;

                // Continue if no ownership is specified
                if (founderPct == 0) continue;

                // Update the total ownership and ensure it's valid
---             if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
+++             if ((totalOwnership += founderPct) > 100) revert INVALID_FOUNDER_OWNERSHIP();

```