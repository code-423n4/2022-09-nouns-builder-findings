
Table Of Content
================

* [QA REPORT](#qa-report)
        * [Missing zero address check for initializers functions](#missing-zero-address-check-for-initializers-functions)
        * [Use timelock modifier for setter functions](#use-timelock-modifier-for-setter-functions)
        * [Unused success return value](#unused-success-return-value)
        * [Use safeTransfer() instead transfer()](#use-safetransfer-instead-transfer)
        * [Missing 0 address check at transfer](#missing-0-address-check-at-transfer)
        * [Different solidity versions are in use](#different-solidity-versions-are-in-use)
        * [Array access is out of bounds](#array-access-is-out-of-bounds)
        * [Missing two steps verification process](#missing-two-steps-verification-process)
        * [Events not emitted for important state changes](#events-not-emitted-for-important-state-changes)
        * [Missing an event after critical initialize() functions](#missing-an-event-after-critical-initialize-functions)
        * [Add event to the following functions](#add-event-to-the-following-functions)
        * [Consider removing the unused parameters names in the following functions](#consider-removing-the-unused-parameters-names-in-the-following-functions)
        * [Consider adding constant variables instead of hardcoded strings](#consider-adding-constant-variables-instead-of-hardcoded-strings)

# QA REPORT

## Missing zero address check for initializers functions
Missing checks for zero-addresses may lead to infunctional protocol. In this case the function is an initializer then the value can be passed only once and is important to be validated. If the variable addresses are updated incorrectly.

### Code Instances:
- [Token.sol#L48](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L48)
- [Governor.sol#L41](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L41)
- [Token.sol#L30](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L30)
- [Manager.sol#L80](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L80)

## Use timelock modifier for setter functions
It is good to have a timelock for functions that set key/critical variables.

### Code Instances:
- [Manager.t.sol#L15](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Manager.t.sol#L15)
- [Auction.t.sol#L10](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Auction.t.sol#L10)
- [Token.t.sol#L14](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Token.t.sol#L14)
- [Auction.sol#L315](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L315)

## Unused success return value
The following calls ignores the return value of the called function that might indicate the the call failed.

### Code Instances:
- [Manager.t.sol#L104](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Manager.t.sol#L104)
- [Token.sol#L156](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L156)

## Use safeTransfer() instead transfer()
Use openzeppelin safeTransfer() method instead of transfer() in the following locations.

### Code Instances:
- [Auction.sol#L191](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L191)
- [Auction.sol#L362](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L362)

## Missing 0 address check at transfer
Some contracts does not support 0 transfer, then the transaction will revert with no explanation. We recommend to add a require statement that the amount is not 0.

For instance, [Auction.sol#L362](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L362)

## Different solidity versions are in use
The project is compiled with different versions of solidity, which is not recommended because it can lead to undefined behaviors.

## Array access is out of bounds
There is no check for the access to be in the array bounds.

For instance, [Manager.sol#L111](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L111)

## Missing two steps verification process
The process of transferring ownership is dangerous since typing the wrong address can lead to severe implications. It is better to have to steps verification process with set and claim functions to decrease the chances of human error. Consider changing to two steps verification process of transferring privileges. Human mistakes can happen.

### Code Instances:
- [Gov.t.sol](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol)
- [Manager.t.sol](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Manager.t.sol)

## Events not emitted for important state changes
When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

### Code Instances:
- [NounsBuilderTest.sol#L145](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol#L145)
- [Auction.sol#L39](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L39)
- [Token.sol#L30](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L30)
- [NounsBuilderTest.sol#L196](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol#L196)

## Missing an event after critical initialize() functions
To record the initialize parameters for off-chain monitoring and transparency reasons, you might find it useful to emit an event after the initialize() functions

### Code Instances:
- [MetadataRenderer.sol#L50](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L50)
- [Token.sol#L48](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L48)
- [MetadataRenderer.sol#L32](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L32)
- [Auction.sol#L60](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L60)

## Add event to the following functions


### Code Instances:
- [Auction.t.sol#L10](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Auction.t.sol#L10)
- [Manager.t.sol#L15](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Manager.t.sol#L15)
- [MetadataRenderer.sol#L32](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L32)
- [Token.sol#L30](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L30)

## Consider removing the unused parameters names in the following functions


For instance, [Manager.sol#L209](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L209)

## Consider adding constant variables instead of hardcoded strings
A good practice is to use constant variables instead of hardcoded strings in the code.

### Code Instances:
- [NounsBuilderTest.sol#L53](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/utils/NounsBuilderTest.sol#L53)
- [Token.t.sol#L37](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Token.t.sol#L37)
- [Gov.t.sol#L256](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L256)
- [Manager.t.sol#L82](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Manager.t.sol#L82)
