 ## wrong blockhash usage
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L251

`blockhash(block.number)` would always return 0. It should be changed to `blockhash(block.number - 1)`. This is not affecting anything in it's current form, but can be dangerous if later someone would change the code