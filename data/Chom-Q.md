## proposalThresholdBps and quorumThresholdBps are missing the maximum limit check

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L578-L592

Since maximum proposalThreshold and quorum is (totalSupply * 10000) / 10000 = totalSupply, maximum would be 10000 (Not make sense to have more than totalSupply)

Should add the maximum limit check as follow

    /// @notice Updates the minimum proposal threshold
    /// @param _newProposalThresholdBps The new proposal threshold basis points
    function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {
        require(_newProposalThresholdBps <= 10000, "Over Limit");
        emit ProposalThresholdBpsUpdated(settings.proposalThresholdBps, _newProposalThresholdBps);

        settings.proposalThresholdBps = SafeCast.toUint16(_newProposalThresholdBps);
    }

    /// @notice Updates the minimum quorum threshold
    /// @param _newQuorumVotesBps The new quorum votes basis points
    function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {
        require(_newQuorumVotesBps <= 10000, "Over Limit");
        emit QuorumVotesBpsUpdated(settings.quorumThresholdBps, _newQuorumVotesBps);

        settings.quorumThresholdBps = SafeCast.toUint16(_newQuorumVotesBps);
    }