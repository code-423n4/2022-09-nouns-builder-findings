Table Of Content
================

* [GAS REPORT](#gas-report)
        * [Don't cache msg.sender](#dont-cache-msgsender)
        * [If the function is onlyOwner you may make it payable to reduce gas usage.](#if-the-function-is-onlyowner-you-may-make-it-payable-to-reduce-gas-usage)

# GAS REPORT

## Don't cache msg.sender
reading msg.sender is 2 gas units which is less than a read of a local var + the unnecessary store operation.

### Code Instances:
- [Gov.t.sol#L432](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L432)
- [Governor.sol#L481](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L481)
- [Token.t.sol#L221](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Token.t.sol#L221)
- [Auction.sol#L94](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L94)

## If the function is onlyOwner you may make it payable to reduce gas usage.


### Code Instances:
- [Treasury.sol#L116](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L116)
- [Manager.sol#L187](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L187)
- [Auction.sol#L307](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L307)
- [Auction.sol#L374](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L374)
