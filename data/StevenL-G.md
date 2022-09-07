First Gas optimization - "<=" is cheaper than "<"

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol
88: -if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
88: +if ((totalOwnership += uint8(founderPct)) >= 101) revert INVALID_FOUNDER_OWNERSHIP(); 

