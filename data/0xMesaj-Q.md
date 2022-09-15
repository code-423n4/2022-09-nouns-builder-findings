USE OF BLOCK.TIMESTAMP


Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L138
https://github.com/code-423n4/2022-09-canto/blob/main/src/Swap/BaseV1-core.sol#L159