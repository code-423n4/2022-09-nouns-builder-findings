# GAS REPORT

## [GAS] Do not cache msg.sender since loading msg.sender is more efficient than a local variable


### Proof of concept:
- [Gov.t.sol#L41](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L41)
- [Gov.t.sol#L25](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/test/Gov.t.sol#L25)

## [GAS] Use abiEncodePacked()


### Proof of concept:
- [Governor.sol#L103](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L103)
- [Manager.sol#L70](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L70)

## [GAS] Mark as payable If has onlyOwner modifier
In order to save gas you can put a payable modifier for functions that are called by protocol owners.

### Proof of concept:
- [Manager.sol#L196](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L196)
- [Auction.sol#L307](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L307)

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [Governor.sol#L417](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L417)
- [Governor.sol#L444](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L444)

--