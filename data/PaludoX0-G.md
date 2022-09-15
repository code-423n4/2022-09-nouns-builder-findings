https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L248
It's better to check at function beginning (or at calling parent functions with a modifier) if voter has weight>0 in order to save gas: 
require(getVotes(_voter, proposal.timeCreated)>0,"NOT_ENOUGH_VOTES");

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L88
Finding that INVALID_FOUNDER_OWNERSHIP() at the very last founder is a waste of gas, a previous check at line 74 through another for cycle would save gas 