Low Risk Findings:

1. Use of block.timestamp

block.timestamp can be manipulated by miners, and should preferrably not be used
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L363