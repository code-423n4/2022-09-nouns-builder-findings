Repeated call to ```proposalThreshold()```:
```solidity
  // Get the current proposal threshold
  uint256 currentProposalThreshold = proposalThreshold();

  // Cannot realistically underflow and `getVotes` would revert
  unchecked {
    // Ensure the caller's voting weight is greater than or equal to the threshold
    if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
  }
```