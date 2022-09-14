# Governor
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L128
There is no need to call `proposalThreshold()` on line 128 of `Governor.sol` as its value has already been saved in the variable `currentProposalThreshold` on line 123. `currentProposalThreshold` should be used on line 128 in place of the second call to `proposalThreshold()`.
