## Issues found

# [G-01] Prefix increment costs less gas than postfix increment

There are 2 instances of this issue.

```
File: src/token/Token.sol
91: uint256 founderId = settings.numFounders++;
154: tokenId = settings.totalSupply++;
```

# [G-02] The increment in for loop post condition can be made unchecked to save gas

There are 8 instances of this issue.

```
File: src/governance/treasury/Treasury.sol
162: for (uint256 i = 0; i < numTargets; ++i) {
```

```
File: src/token/Token.sol
80: for (uint256 i; i < numFounders; ++i) {
108: for (uint256 j; j < founderPct; ++j) {
261: for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];
```

```
File: src/token/metadata/MetadataRenderer.sol
119: for (uint256 i = 0; i < numNewProperties; ++i) {
133: for (uint256 i = 0; i < numNewItems; ++i) {
189: for (uint256 i = 0; i < numProperties; ++i) {
229: for (uint256 i = 0; i < numProperties; ++i) {
```

# [G-03] Initializing a variable with the default value wastes gas

There are 5 instances of this issue.

```
File: src/governance/treasury/Treasury.sol
162: for (uint256 i = 0; i < numTargets; ++i) {
```

```
File: src/token/metadata/MetadataRenderer.sol
119: for (uint256 i = 0; i < numNewProperties; ++i) {
133: for (uint256 i = 0; i < numNewItems; ++i) {
189: for (uint256 i = 0; i < numProperties; ++i) {
229: for (uint256 i = 0; i < numProperties; ++i) {
```

# [G-04] Use != 0 instead of > 0 to save gas.

Replace `> 0` with `!= 0` for unsigned integers.

There is 1 instance of this issue

```
File: src/lib/token/ERC721Votes.sol
203: if (_from != _to && _amount > 0) {
```

# [G-05] Use right/left shift instead of division/multiplication to save gas

There is 1 instance of this issue.

```
File: src/lib/token/ERC721Votes.sol
95: middle = high - (high - low) / 2;
```

# [G-06] Using private rather than public for constants, saves gas

The values can still be inspected on the source code if necessary.

There is 1 instance of this issue.

```
File: src/governance/governor/Governor.sol
27: bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```

# [G-07] Replace memory with calldata for read-only function arguments

There are 3 instances of this issue.

```
address[] memory _targets,
uint256[] memory _values,
bytes[] memory _calldatas,
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L99-L101

# [G-08] x += y costs more gas than x = x + y for state variables

There are 3 instances of this issue.

```
File: src/governance/governor/Governor.sol
280: proposal.againstVotes += uint32(weight);
285: proposal.forVotes += uint32(weight);
290: proposal.abstainVotes += uint32(weight);
```
