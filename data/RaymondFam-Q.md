## Modded Value Unassigned
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118

The above line of code has a modded value unassigned to another variable, rendering the mod operation unusable.

## Default Visibility of State Variables
`internal` is the default visibility for state variables. Hence, there isn't a need to explicitly declare it. Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#L11
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#L14
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#L20

## Use safeTransferFrom instead of transferFrom
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L192

ERC721 has both safeTransferFrom and transferFrom, where safeTransferFrom throws an error if the receiving contract's onERC721Received method doesn't return a specific magic number. This will ensure a receiving contract is capable of receiving the token to prevent a permanent loss.