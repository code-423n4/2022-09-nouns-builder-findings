# Initialising an already 0 initialized variable can cost gas

The following lines of code in the respective files have `uint256 i = 0`.  Any variable in solidity is 0 initialised. So it does not make sense to reinitialise it.

File: src/token/metadata/MetadataRenderer.sol

LoC:
(https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119)
(https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133)
(https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189)
(https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229)

File: src/governance/treasury/Treasury.sol

LoC:
(https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162)