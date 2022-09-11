# GAS REPORT

## [GAS] The following require statements could be split into multiple require statements to save gas


Example: [NounsBuilderTest.sol#L107](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol#L107)

## [GAS] Caching msg.sender is unnecessary


### Proof of concept:
- [Gov.t.sol#L446](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L446)
- [Auction.sol#L104](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L104)

--