## Gas
### Should use consistently prefix increment for loops

Prefix increments are cheaper than postfix increments. Code ussually uses prefix however some places were missed.

uint256 founderId = settings.numFounders++;
to
uint256 founderId = ++settings.numFounders;
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L91

tokenId = settings.totalSupply++;
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L154

### should use cached currentProposalThreshold in Governor.sol

Context 1: [`Governor.sol#L123-L129`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L123-L129)

Developer forgot to use cached value and code is invoking the same function for the second time.


**Recommendation**: 
```diff=
    uint256 currentProposalThreshold = proposalThreshold();

    // Cannot realistically underflow and `getVotes` would revert
    unchecked {
        // Ensure the caller's voting weight is greater than or equal to the threshold
-        if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
+        if (getVotes(msg.sender, block.timestamp - 1) < currentProposalThreshold) revert BELOW_PROPOSAL_THRESHOLD();
    }
```