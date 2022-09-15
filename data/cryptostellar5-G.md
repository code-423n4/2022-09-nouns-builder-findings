## G-01 FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

[Auction.sol#L244](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol)

```
244 function unpause() external onlyOwner {

263 function pause() external onlyOwner {

307 function setDuration(uint256 _duration) external onlyOwner {

315 function setReservePrice(uint256 _reservePrice) external onlyOwner {

323 function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {

331 function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {

374 function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
```

[Governor.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol)

```
564 function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {

572 function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {

580 function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {

588 function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {

596 function updateVetoer(address _newVetoer) external onlyOwner {

605 function burnVetoer() external onlyOwner {

618 function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
```

[Manager.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol)

```
187  function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

196 function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

209 function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
```

[MetadataRenderer.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol)

```
95  external onlyOwner {

347  function updateContractImage(string memory _newContractImage) external onlyOwner {

355 function updateRendererBase(string memory _newRendererBase) external onlyOwner {

363 function updateDescription(string memory _newDescription) external onlyOwner {

376 function _authorizeUpgrade(address _impl) internal view override onlyOwner {
```

[Treasury.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol)

```
116 function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {

180 function cancel(bytes32 _proposalId) external onlyOwner {
```

## G-02 EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

[Treasury.sol#L269](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269)

```
receive() external payable {}
```

## G-03  X += Y COSTS MORE GAS THAN X = X + Y

Although the `uncheck{}` is used to optimise gas for overflow/underflow conditions, yet X += Y costs more gas than X=X+Y, which is overlooked.

[Governor.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol)

```
280: proposal.againstVotes += uint32(weight);

285: proposal.forVotes += uint32(weight);

290: proposal.abstainVotes += uint32(weight);
```

[Token.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol)

```
88: if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();

118: (baseTokenId += schedule) % 100;
```

[MetadataRenderer.sol#L140](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140)

```
140: _propertyId += numStoredProperties;
```


## G-04 IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < numIterations; ++i) {` should be replaced with `for (uint256 i; i < numIterations; ++i) {`

Affected code: 

[Treasury.sol#L162](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162)

```
for (uint256 i = 0; i < numTargets; ++i) {
```

[MetadataRenderer.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol)

```
119: for (uint256 i = 0; i < numNewProperties; ++i) {

133: for (uint256 i = 0; i < numNewItems; ++i) {

189: for (uint256 i = 0; i < numProperties; ++i) {

229: for (uint256 i = 0; i < numProperties; ++i) {
```

Consider removing explicit initializations for default values.


## G-05 USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

[Governor.sol#L27](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27)

```
bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```