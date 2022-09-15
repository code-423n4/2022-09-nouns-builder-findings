https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L63
Because of function importance, it would be better to keep emit after ownership transfer, in case the transfership fails
There's no control that address is different from address(0), settings.treasury could be = 0 if not has been set correctly

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L141
It's better to set only ProposalId as function parameter, instead of repeating  _target/_values/_calldatas/_descriptionHash parameters which could led to errors