
# QA REPORT

## [LOW] Not verified input
At the following functions you should verify the parameters that are being assigned to a state variable.

### Proof of concept:
- [Manager.sol#L61](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L61)
- [Auction.sol#L39](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L39)

## [LOW] The project is compiled with different solidity versions


## [LOW] Missing pause functionality


Example: [MetadataRenderer.sol](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol)

## [LOW] Missing nonReentrancy modifier
The following functions allows attackers to try reentrancy since they are calling to external contracts / transferring eth. Consider adding a nonReentrancy modifier.

### Proof of concept:
- [Gov.t.sol#L170](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L170)
- [Gov.t.sol#L596](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L596)

## [LOW] Consider adding two steps verification process
Protocol ownership transfer should be dealt with great care. Adding two steps verification is necessary for that matter.

### Proof of concept:
- [Manager.t.sol](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Manager.t.sol)
- [NounsBuilderTest.sol](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol)

## [NON CRITICAL] Missing function spec comments


### Proof of concept:
- [Auction.sol#L282](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L282)
- [Treasury.sol#L68](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L68)

## [NON CRITICAL] Unused function parameters should have name removed
If for any reason the following unused parameters are necessary then remove their naming (since only the type matters for function signature)

Example: [Manager.sol#L209](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L209)

## [NON CRITICAL] Consider emitting an event at the following functions


### Proof of concept:
- [NounsBuilderTest.sol#L42](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol#L42)
- [Token.sol#L30](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L30)
