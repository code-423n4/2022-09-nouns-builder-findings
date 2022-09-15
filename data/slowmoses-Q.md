(1)
Events are missing indexed fields.

If there are three or more fields, an event should have three indexed fields. The following events are missing such indexed fields:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L16-L34
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol#L17-L21
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#L24-L28
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L18-L19
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L15-L21
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L41-L42
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L21-L22

(2)
Consider using the same version of solidity for all contracts.
Ideally the most recent version of solidity (0.8.17) should be used.

Examples:
0.8.15 in src/token/Token.sol
^0.8.15 in src/lib/interfaces/IWETH.sol
^0.8.4 un src/lib/token/ERC721Votes.sol

(3)
For the castVote, castVoteWithReason, castVoteBySig, and _castVote functions in Governor.sol an enum should be defined for the “_support” argument instead of using a uint256 and magic numbers.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L177-L297

(4)
Revert() statement should return some descriptive string or ideally a custom error.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L54

(5)
The function “hashProposal” in Governor.sol is only called internally. It probably should not be public.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L93-L105

