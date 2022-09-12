1.	Check address param for 0 address.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L40-L41
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L42
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L33
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L62-L66
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L31
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L65-L66
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L65-L69
2.	Check if `_proposalId` exists. Revert when no `_proposalId` instead of returning 0 values.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L482
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L488
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L516
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L508
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L516
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L69
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L76
3.	Check `_founders[i].wallet` is not 0 address as this address is very important for the dao.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L97
4.	Check if `_founderId` exists. Revert when no `_ founderId` instead of returning 0 values.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L247
