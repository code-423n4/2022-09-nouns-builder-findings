 1 ) ABI.ENCODEPACKED() SHOULD NOT BE USED WITH DYNAMIC TYPES WHEN PASSING THE RESULT TO A HASH FUNCTION SUCH AS KECCAK256() ,
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L226
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L68

2)USE OF BLOCK.TIMESTAMP  , Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

Proof of Concept
Navigate to the following contract.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol



Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.