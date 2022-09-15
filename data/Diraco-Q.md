1 USE OF BLOCK.TIMESTAMP


Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

Proof of Concept
Navigate to the following contract.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L138
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L159
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L176

Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.
2  _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L161