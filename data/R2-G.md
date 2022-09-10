## 1. No need to cache startTime
### Details
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L211
``block.timestamp`` costs only 2 gas, it so cheep
So you do not need to create new variable

## 2. Redundant function call
### Details
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L128
You already saved function result in ``currentProposalThreshold``, use it

## 3. Copy mapping to memory
### Details
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L70
You have many calls to this object, so it would be better to save it to memory


## 4. Useless ``EIP712._computeDomainSeparator()``
### Details
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L68
There is no overrides for this function, so you can just hardcode this value and remove function


