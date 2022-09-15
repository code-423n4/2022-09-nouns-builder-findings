### USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

_There is 1 instance of this issue:_

```solidity
// governance/governor/Governor.sol
27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");

```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27
&nbsp;
&nbsp;


###  IT COSTS MORE GAS TO INITIALIZE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

_There are 5 instances of this issue:_

```solidity
// governance/treasury/Treasury.sol
162 => for (uint256 i = 0; i < numTargets; ++i) {

// token/metadata/MetadataRenderer.sol
119 => for (uint256 i = 0; i < numNewProperties; ++i) {
133 => for (uint256 i = 0; i < numNewItems; ++i) {
189 => for (uint256 i = 0; i < numProperties; ++i) {
229 => for (uint256 i = 0; i < numProperties; ++i) {
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229
