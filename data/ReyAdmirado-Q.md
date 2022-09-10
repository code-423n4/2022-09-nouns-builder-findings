## 1. inconsistent use of named return variables

there is an inconsistent use of named return variables in the contract some functions return named variables, others return explicit values. consider adopting a consistent approach.
this would improve both the explicitness and readability of the code, and it may also help reduce regressions during future code refactors.
these `returns` are different from other parts of the code:

- [Token.sol#L143](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L143)

- [MetadataRenderer.sol#L206](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L206)

- [Treasury.sol#L116](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L116)

- [Governor.sol#L305](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L305)

- [Auction.sol#L206](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L206)

- [Manager.sol#L104](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L104)
- [Manager.sol#L158](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L158)


## 2. typo in comments

recieve --> receive

- [Token.sol#L104](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L104)

psuedo --> pseudo

- [MetadataRenderer.sol#L249](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L249)

responsiblity --> responsibility

- [Manager.sol#L113](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L113)

addresss --> address

- [IGovernor.sol#L290](https://github.com/code-2903n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L42)

seperator --> separator

- [ERC721Votes.sol#L160](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L160)


## 3. event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

- [IAuction.sol#L22](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L22)
- [IAuction.sol#L28](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L28)
- [IAuction.sol#L34](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L34)

- [IGovernor.sol#L42](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L42)

- [ITreasury.sol#L22](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L22)

- [IManager.sol#L21](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L21)

- [IToken.sol#L21](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol#L21)


## 4. _safemint() should be used rather than _mint() wherever possible

- [Token.sol#L169](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L169)


## 5. constants should be defined rather than using magic numbers 

Even assembly can benefit from using readable constants instead of hex/numeric literals

every `100` in `_addFounders`:
- [Token.sol#L71](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L71)

and:

- [Token.sol#L179](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L179)
- [Token.sol#L271](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L271)

- [MetadataRenderer.sol#L197](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L197)
- [MetadataRenderer.sol#L210](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L210)

- [Governor.sol#L468](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L468)
- [Governor.sol#L475](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L475)

- [Auction.sol#L354](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L354)

- [Manager.sol#L123](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L123)
- [Manager.sol#L165](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L165)


## 6. remove inconsistencies with the main code

change these interfaces to the same version used in the main code

- [IGovernor.sol#L135](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L135)
- [IGovernor.sol#L146](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L146)
- [IGovernor.sol#L195](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L195)
