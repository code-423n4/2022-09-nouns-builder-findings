# Gas Optimizations Report for Nouns Builder contest

## Overview
During the audit, 4 gas issues were found.

№ | Title | Instance Count
--- | --- | --- 
G-1 | [Initializing variables with default value](#g-1-initializing-variables-with-default-value) | 5
G-2 | [> 0 is more expensive than =! 0](#g-2--0-is--more-expensive-than--0) | 3
G-3 | [x += y is more expensive than x = x + y](#g-3-x--y-is--more-expensive-than-x--x--y) | 6
G-4 | [Public is more expensive than private for constants](#g-4-public-is-more-expensive-than-private-for-constants) | 1

## Gas Optimizations Findings (4)
### G-1. Initializing variables with default value
##### Description
It costs gas to initialize integer variables with 0 or bool variables with false but it is not necessary.

##### Instances
- [```for (uint256 i = 0; i < numTargets; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162)
- [```for (uint256 i = 0; i < numNewProperties; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L119)
- [```for (uint256 i = 0; i < numNewItems; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L133)
- [```for (uint256 i = 0; i < numProperties; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L189)
- [```for (uint256 i = 0; i < numProperties; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L229)

##### Recommendation
Remove initialization for default values.  
For example:
```for (uint256 i; i < reqLength; ) {```

#
### G-2. ```> 0``` is  more expensive than ```=! 0```
##### Instances
- [```if (_data.length > 0 || _forceCall) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L61)
- [```if (_from != _to && _amount > 0) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L203)
- [```if (_returndata.length > 0) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L50)

##### Recommendation
Use ```=! 0``` instead of ```> 0```, where possible.

#
### G-3. ```x += y``` is  more expensive than ```x = x + y```
##### Instances
- [```proposal.againstVotes += uint32(weight);```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L280)
- [```proposal.forVotes += uint32(weight);```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L285)
- [``` proposal.abstainVotes += uint32(weight);```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L290)
- [```_propertyId += numStoredProperties;```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L140)
- [```totalOwnership += uint8(founderPct))```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L88)
- [```(baseTokenId += schedule) % 100;```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L118)

##### Recommendation
Use ```x = x + y``` instead of ```x += y```.
Use ```x = x - y``` instead of ```x -= y```.

#
### G-4. ```Public``` is more expensive than ```private``` for constants
##### Instances
[```bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L27)