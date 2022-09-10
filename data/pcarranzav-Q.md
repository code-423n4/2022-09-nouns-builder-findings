# Summary

I've separately reported what I believe are a high severity issue and a medium severity issue, both related to the token allocation for founders. That's the section of the code that I've (so far) been able to spend more time on, and I believe that particular section could use a refactor to make errors like these less likely.

The rest of the code looks okay to me so far. I'm including in this QA report one low-severity bug, and a few non-critical suggestions. The first of these (Immutability of implementations) is an architectural suggestion that I think could make the code more future-proof. The rest are simply style suggestions.

Other than that, even though I could find no vulnerabilities in these, I would recommend extra care and testing in:

- the proposal ID generation and hashing (https://github.com/code-423n4/2022-09-nouns-builder/blob/main/Governor.sol#L331 and https://github.com/code-423n4/2022-09-nouns-builder/blob/main/Governor.sol#L98) - I couldn't find a way to produce a collision, but an attacker somehow producing a collision in the ABI-encoded parameters would be able to void a proposal or call the wrong contract/function
- the upgrade path for Governor and Treasury implementations; the fact that these contracts are both owned by the Treasury make the whole thing a bit complex, which makes it look risky as well. Having some tests for Treasury upgrading Governor, and Treasury upgrading itself, would be beneficial.

# Low severity

## Blockhash of current block is zero and therefore not random

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L251

In `_generateSeed()` I assume blockhash is used to introduce randomness, but `blockhash(block.number)` will always be zero. I recommend using `blockhash(block.number - 1)`.

# Non-critical

## Immutability of implementations

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L25-L37

The pattern of using immutable variables in Manager is clever, but it doesn’t allow updating the default implementation for the contracts. This means any vulnerability found in the contracts after deployment would always be present in a newly-created DAO, until the founder upgrades to the newer implementation.

I understand it’d be a big change to make the base implementations mutable (especially because you then have to somehow figure out what creation hash to use to get the addresses in getAddresses).

But there is an easier alternative: providing a way to atomically deploy and upgrade a DAO to the latest version of each implementation. This could be done by keeping the latest version of each type of contract in Manager storage; the `deploy()` function can then check if there is a newer version for each contract and upgrade to it before returning.

## Named returns

e.g. in https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L105-L109

I recommend not using named returns (https://github.com/ethereum/solidity/issues/1401) and instead specifying the meaning of the return values in the NatSpec documentation. 

## Auction contract and Auction struct having the same name is confusing

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L22
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L92

Recommendation: change the name of one of the two, e.g. rename the contract to AuctionHouse

## Assignment in conditional

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/managed/Managed.sol#L117

Having this assignment in conditional is confusion-prone, it would be safer to assign when declaring `address founder`.

## Long blocks with unchecked math

e.g. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L201-L234

While I can see the benefit of saving gas with unchecked math
where it's guaranteed there'll be no overflows, the use of long
blocks of code with `unchecked` makes it more likely that
future code changes will introduce operations that can overflow.

I'd recommend applying `unchecked{}` to the specific statements where the lack of overflows is guaranteed.

