### Severity: Low

## Owner transfer function not checking zero address

## Description:
The transfer ownership function should be checked zero address can't be the owner cause when the zero address is the owner, the owner never accesses to onlyOwner functions and the protocol get big damage.

## Impact: 
the owner can transferOwnership to a zero address and when the owner does this, the owner cant get owner again because the transferOwnership function has onlyOwner a modifier and the owner is a zero address. That means nobody forever cant access to onlyOwner functions.

## PoC:
Bob is the owner and calls transferOwnership function and inputs zero address, contract forever lost an owner role.

## instead of issue:
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L63

## Recommendation:
Check that the address is not zero.
