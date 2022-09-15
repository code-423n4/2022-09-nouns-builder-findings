# Summary

| Id | Title |
| -- | ----- |
| 1 | Should use already cached variable to save gas instead of calling function |
| 2 | No need condition in the last else block |

## 1. Should use already cached variable to save gas instead of calling function

In `propose()` function, the value of `currentProposalThreshold` is already get from `proposalThreshold()` and cached but when checking votes it calls `proposalThreshold()` which is unnessary and cost more gas

```solidity
uint256 currentProposalThreshold = proposalThreshold();

// Cannot realistically underflow and `getVotes` would revert
unchecked {
    // Ensure the caller's voting weight is greater than or equal to the threshold
    if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
}
```

Should use already cached one `currentProposalThreshold` to do the check

### Affected Codes
- https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L128


## 2. No need condition in the last else block

In `castVote()` function, since `_support > 2` will be reverted already, we do not need to check `_support == 2` in the last else block. Because the value of `_support` is always equaled to 2 after previous checks.

### Affected Codes
- https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L288


