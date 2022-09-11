# QA REPORT

## [LOW] For the following functions, add timelock


### Proof of concept:
- [Auction.sol#L161](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L161)
- [Auction.sol#L268](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L268)

## [NON CRITICAL] Use of magic numbers in the following locations


### Proof of concept:
- [Manager.t.sol#L54](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Manager.t.sol#L54)
- [Gov.t.sol#L744](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L744)

## [NON CRITICAL] The following functions may not be payable


### Proof of concept:
- [MetadataRenderer.sol#L32](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L32)
- [Treasury.sol#L269](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L269)
