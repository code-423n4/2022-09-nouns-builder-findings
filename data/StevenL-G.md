First Gas optimization - "<=" is cheaper than "<"

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol
88: -if ((totalOwnership += uint8(founderPct)) > 100);
88: +if ((totalOwnership += uint8(founderPct)) >= 101);

Second Gas optimization - using bools for storage incurs overhead

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol
136: bool extend;
349: bool success;

Third Gas optimization - default values of state variable is not needed and wastes gas

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol
162: for (uint256 i = 0; i < numTargets; ++i) {

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol
119: for (uint256 i = 0; i < numNewProperties; ++i) {
133: for (uint256 i = 0; i < numNewItems; ++i) {
189: for (uint256 i = 0; i < numProperties; ++i) {
229: for (uint256 i = 0; i < numProperties; ++i) {