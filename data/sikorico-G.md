# GAS REPORT

## [GAS 00] transferFrom(address(this), to, amount) can be changed to transfer(to, amount) to save gas


### Proof of concept:
- [Gov.t.sol#L814](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L814)
- [Gov.t.sol#L778](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L778)
- [Auction.sol#L191](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L191)

## [GAS 01] abiEncodePacked() instead abiEncode() in the following locations


### Proof of concept:
- [Gov.t.sol#L417](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L417)
- [Gov.t.sol#L328](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L328)
- [Governor.sol#L103](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L103)
- [Manager.sol#L67](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L67)
- [Gov.t.sol#L394](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L394)

--