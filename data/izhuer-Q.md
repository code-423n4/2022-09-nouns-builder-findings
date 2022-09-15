## int8 underflow in src/token/Token.sol:99  

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
                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();

                // Compute the founder's id
                uint256 founderId = settings.numFounders++;

                // Get the pointer to store the founder
                Founder storage newFounder = founder[founderId];

                // Store the founder's vesting details
                newFounder.wallet = _founders[i].wallet;
                newFounder.vestExpiry = uint32(_founders[i].vestExpiry);
                newFounder.ownershipPct = uint8(founderPct);

                // Compute the vesting schedule
                uint256 schedule = 100 / founderPct;

                // Used to store the base token id the founder will recieve
                uint256 baseTokenId;

                // For each token to vest:
                for (uint256 j; j < founderPct; ++j) {
                    // Get the available token id
                    baseTokenId = _getNextTokenId(baseTokenId);

                    // Store the founder as the recipient
                    tokenRecipient[baseTokenId] = newFounder;

                    emit MintScheduled(baseTokenId, founderId, newFounder);

                    // Update the base token id
                    (baseTokenId += schedule) % 100;
                }
            }

            // Store the founders' details
            settings.totalOwnership = uint8(totalOwnership);
            settings.numFounders = uint8(numFounders);
        }
```

When a malicious founder supplies a `founderPct` bigger than 2^8, he can bypass the check against `if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();` 

## unknown statement in src/token/Token.sol:118 

This statement `(baseTokenId += schedule) % 100;` seems not helpful.

##  array indexing overflow in src/token/metadata/MetadataRenderer.sol:194 

` tokenAttributes[i + 1] = uint16(seed % numItems);` 

When `numProperties` is larger than `16`, any token mint will fail.

## ECDSA Malleability in src/governance/governor/Governor.sol:208 

It is suggested to use OpenZeppelin ECDSA library.