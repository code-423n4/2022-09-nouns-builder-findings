## generate seed optimization
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L176

this line can be changed to `uint256 seed = uint256(blockhash(block.number - 1));`
This would cut 150 gas on average and reduce codebase a little (you could delete _generateSeed function after this change). There is no advantage to generate it the way _generateSeed function does it, blockhash provides sufficent amount of entropy, as it hashes whole block and it's metadata