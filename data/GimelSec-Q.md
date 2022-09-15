# Summary

We list 1 low-critical finding:
* (Low) It’s better to define uint8 founderPct

# (Low) It’s better to define uint8 founderPct

## Impact

`founderPct` is defined as uint256, but it’s used for both uint8 and uint256.

## Proof of Concept

founderPct is uint256 but is used by uin8:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L82
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L88

```
                uint256 founderPct = _founders[i].ownershipPct;
                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
```

But L102 uint256 again:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L102

```
                uint256 schedule = 100 / founderPct;
```

## Recommended Mitigation Steps

Define uint8 rather than uint256 in L82.
