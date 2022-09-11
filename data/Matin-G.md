For array inputs as function arguments, it’s better to use calldata instead of memory. Otherwise, the abi.decode() would operate a for loop for every member of the array. 

function hashProposal:
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L98-L101

function propose:
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L116-L119

function mintBatch:
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/test/utils/mocks/MockERC1155.sol#L17-L20

