# baseTokenId can be updated in efficient way
Updating base baseTokenId costs more gas. `a = a+b` costs less gas compared to `a += b`. Check audit tag below.

````solidity
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
                    (baseTokenId += schedule) % 100; //@audit gas: should be (baseTokenId = baseTokenId + schedule) % 100
                }
            }

            // Store the founders' details
            settings.totalOwnership = uint8(totalOwnership);
            settings.numFounders = uint8(numFounders);
        }
    }
````