# **Floating pragma is risky**

### Target codebase

All contracts in lib folder

## **Vulnerability details**

The codebase uses floating pragma. All contracts should be compiled with same pragma version. Locking the pragma ensures that contracts do not accidentally get deployed using either an outdated buggy compiler version or a compiler version different from what the code has been tested with.

## **Recommended Mitigation Steps**

Use the same compiler version for all contracts by setting a specific version e.g. `0.8.17`
 for these contracts. Version 0.8.17 fixed important bugs.

---

# Missing `gap` storage variable

### Target codebase

All storage contracts

## **Vulnerability details**

Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

## **Recommended Mitigation Steps**

Add `__gap` variable at end of each storage contract:

```diff
+ uint256[50] __gap;
```

---

# Missing bounds validation in set functions

### Target codebase

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L310

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L318

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L326

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L335

## **Vulnerability details**

In each set function in Auction contract is missing upper/lower bound validation, in `setMinimumBidIncrement` function the owner could set it to maximum of 255% instead of 100%.

## **Recommended Mitigation Steps**

Add upper/lower bound check to prevent.