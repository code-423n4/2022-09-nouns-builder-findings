## G-01 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as `onlyOwner` or `onlyProxy` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Proof of Concept

There are 22 instances of this issue

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol

```
File: src/auction/Auction.sol

244: function unpause() external onlyOwner {
263: function pause() external onlyOwner {
307: function setDuration(uint256 _duration) external onlyOwner {
315: function setReservePrice(uint256 _reservePrice) external onlyOwner {
323: function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {
331: function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {
374: function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol

```
File: src/governance/governor/Governor.sol

564: function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {
572: function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {
580:function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {
588: function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {
596: function updateVetoer(address _newVetoer) external onlyOwner {
605: function burnVetoer() external onlyOwner {
618: function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol

```
File: src/governance/treasury/Treasury.sol

116: function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {
180: function cancel(bytes32 _proposalId) external onlyOwner {

```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol

```
File: src/lib/proxy/UUPS.sol

47: function upgradeTo(address _newImpl) external onlyProxy {
55: function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol

```
File: src/token/metadata/MetadataRenderer.sol

347: function updateContractImage(string memory _newContractImage) external onlyOwner {
355: function updateRendererBase(string memory _newRendererBase) external onlyOwner {
363: function updateDescription(string memory _newDescription) external onlyOwner {
376: function _authorizeUpgrade(address _impl) internal view override onlyOwner {
```

-----------
## G-02 x += y costs more gas than x = x+y for state variables

### Proof of Concept

There are 6 instances of this issue

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280

```
File: src/governance/governor/Governor.sol    #1

proposal.againstVotes += uint32(weight);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285

```
File: src/governance/governor/Governor.sol    #2

proposal.forVotes += uint32(weight);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290

```
File: src/governance/governor/Governor.sol    #3

proposal.abstainVotes += uint32(weight);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L88

```
File: src/token/Token.sol    #4

if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118

```
File: src/token/Token.sol    #5

(baseTokenId += schedule) % 100;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140

```
File: src/token/metadata/MetadataRenderer.sol    #6

_propertyId += numStoredProperties;
```

------------

## G-03 Multiplication or division by 2 should use bit shifting

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

### Proof of Concept

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L95

```
File: src/lib/token/ERC721Votes.sol

middle = high - (high - low) / 2;
```

__________

## G-04 It costs more gas to initialize variables to zero than to let the default of zero be applied

### Proof of Concept

There are 5 instances of this issue

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162

```
File: src/governance/treasury/Treasury.sol    #1

            for (uint256 i = 0; i < numTargets; ++i) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119

```
File: src/token/metadata/MetadataRenderer.sol    #2

            for (uint256 i = 0; i < numNewProperties; ++i) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133

```
File: src/token/metadata/MetadataRenderer.sol    #3

            for (uint256 i = 0; i < numNewItems; ++i) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189

```
File: src/token/metadata/MetadataRenderer.sol    #4

            for (uint256 i = 0; i < numProperties; ++i) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229

```
File: src/token/metadata/MetadataRenderer.sol    #5

            for (uint256 i = 0; i < numProperties; ++i) {
```

_______________

## G-05 Usage of uints or ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Proof of Concept

There are 7 instances of this issue

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L18

```
File: src/auction/types/AuctionTypesV1.sol    #1

        uint8 minBidIncrement;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L213

```
File: src/governance/governor/Governor.sol    #2

        uint8 _v,
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L181

```
File: src/governance/governor/IGovernor.sol    #3

        uint8 v,
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L21-L22

```
File: src/governance/governor/types/GovernorTypesV1.sol    #4

        uint16 proposalThresholdBps;
        uint16 quorumThresholdBps;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L148

```
File: src/lib/token/ERC721Votes.sol    #5

        uint8 _v,
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L20-L21

```
File: src/token/types/TokenTypesV1.sol    #6

        uint8 numFounders;
        uint8 totalOwnership;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L30

```
File: src/token/types/TokenTypesV1.sol   #7

uint8 ownershipPct;
```
