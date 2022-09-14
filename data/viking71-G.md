# `baseTokenId` in `Token.sol` can be updated in efficient way
Updating base baseTokenId costs more gas. `a = a+b` costs less gas compared to `a += b`.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L118

Check audit tag below.

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

# `proposal.againstVotes`, `proposal.forVotes` and `proposal.abstainVotes` in `Governor.sol` can be updated in optimized way

 `a = a+b` costs less gas compared to `a += b`.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L280

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L285

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L290

Check @audit tag below in code.

```` solidity
 function _castVote(
        bytes32 _proposalId,
        address _voter,
        uint256 _support,
        string memory _reason
    ) internal returns (uint256) {
        // Ensure voting is active
        if (state(_proposalId) != ProposalState.Active) revert VOTING_NOT_STARTED();

        // Ensure the voter hasn't already voted
        if (hasVoted[_proposalId][_voter]) revert ALREADY_VOTED();

        // Ensure the vote is valid
        if (_support > 2) revert INVALID_VOTE();

        // Record the voter as having voted
        hasVoted[_proposalId][_voter] = true;

        // Get the pointer to the proposal
        Proposal storage proposal = proposals[_proposalId];

        // Used to store the voter's weight
        uint256 weight;

        // Cannot realistically underflow and `getVotes` would revert
        unchecked {
            // Get the voter's weight at the time the proposal was created
            weight = getVotes(_voter, proposal.timeCreated);

            // If the vote is against:
            if (_support == 0) {
                // Update the total number of votes against
                proposal.againstVotes += uint32(weight); //@audit gas: should be proposal.againstVotes= proposal.againstVotes+ uint32(weight);

                // Else if the vote is for:
            } else if (_support == 1) {
                // Update the total number of votes for
                proposal.forVotes += uint32(weight);//@audit gas: should be proposal.forVotes = proposal.forVotes+ uint32(weight);

                // Else if the vote is to abstain:
            } else if (_support == 2) {
                // Update the total number of votes abstaining
                proposal.abstainVotes += uint32(weight); //@audit gas:  should be proposal.abstainVotes = proposal.abstainVotes + uint32(weight)
            }
        }
````

