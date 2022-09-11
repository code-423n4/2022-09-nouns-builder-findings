# QA REPORT

## [QA 00] The following require statements are missing an error message


### Proof of concept:
- [Gov.t.sol#L474](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L474)

## [QA 01] Not emitted event for state changes


### Proof of concept:
- [Auction.sol#L39](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L39)
- [NounsBuilderTest.sol#L145](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol#L145)
- [NounsBuilderTest.sol#L159](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol#L159)
- [Manager.sol#L61](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L61)
- [NounsBuilderTest.sol#L223](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol#L223)

## [QA 02] Remove the name from the following unused function parameters


### Proof of concept:
- [Manager.sol#L209](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L209)
