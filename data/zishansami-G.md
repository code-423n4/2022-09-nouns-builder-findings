## Gas Optimizations
 *** 


### G-01: `++i/i++` should be placed in unchecked blocks to save gas as it is impossible for them to overflow in for and while loops
Unchecked keyword is available in solidity version `0.8.0` or higher and can be applied to iterator variables to save gas. 
Saves more than `30 gas` per loop.

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133
```solidity
src/token/metadata/MetadataRenderer.sol

133:            for (uint256 i = 0; i < numNewItems; ++i) {

```

instance #2
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229
```solidity
src/token/metadata/MetadataRenderer.sol

229:            for (uint256 i = 0; i < numProperties; ++i) {

```

 *** 


### G-02: `x += y` costs more gas than `x = x + y` for state variables

Total instances of this issue: 6

instance #1
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L88
```solidity
src/token/Token.sol

88:                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();

```

instance #2
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118
```solidity
src/token/Token.sol

118:                    (baseTokenId += schedule) % 100;

```

instance #3
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140
```solidity
src/token/metadata/MetadataRenderer.sol

140:                    _propertyId += numStoredProperties;

```

instance #4
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280
```solidity
src/governance/governor/Governor.sol

280:                proposal.againstVotes += uint32(weight);

```

instance #5
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285
```solidity
src/governance/governor/Governor.sol

285:                proposal.forVotes += uint32(weight);

```

instance #6
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290
```solidity
src/governance/governor/Governor.sol

290:                proposal.abstainVotes += uint32(weight);

```

 *** 


### G-03: Adding `payable` to functions which are only meant to be called by specific actors like `onlyOwner` will save gas cost
Marking functions payable removes additional checks for whether a payment was provided, hence reducing gas cost

Total instances of this issue: 24

instance #1
Permalink: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L91-L95
```solidity
src/token/metadata/MetadataRenderer.sol

91:    function addProperties(
92:        string[] calldata _names,
93:        ItemParam[] calldata _items,
94:        IPFSGroup calldata _ipfsGroup
95:    ) external onlyOwner {

```

instance #2
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L347
```solidity
src/token/metadata/MetadataRenderer.sol

347:    function updateContractImage(string memory _newContractImage) external onlyOwner {

```

instance #3
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L355
```solidity
src/token/metadata/MetadataRenderer.sol

355:    function updateRendererBase(string memory _newRendererBase) external onlyOwner {

```

instance #4
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L363
```solidity
src/token/metadata/MetadataRenderer.sol

363:    function updateDescription(string memory _newDescription) external onlyOwner {

```

instance #5
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L376
```solidity
src/token/metadata/MetadataRenderer.sol

376:    function _authorizeUpgrade(address _impl) internal view override onlyOwner {

```

instance #6
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L244
```solidity
src/auction/Auction.sol

244:    function unpause() external onlyOwner {

```

instance #7
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L263
```solidity
src/auction/Auction.sol

263:    function pause() external onlyOwner {

```

instance #8
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307
```solidity
src/auction/Auction.sol

307:    function setDuration(uint256 _duration) external onlyOwner {

```

instance #9
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315
```solidity
src/auction/Auction.sol

315:    function setReservePrice(uint256 _reservePrice) external onlyOwner {

```

instance #10
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323
```solidity
src/auction/Auction.sol

323:    function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {

```

instance #11
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331
```solidity
src/auction/Auction.sol

331:    function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {

```

instance #12
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L374
```solidity
src/auction/Auction.sol

374:    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {

```

instance #13
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L187
```solidity
src/manager/Manager.sol

187:    function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

```

instance #14
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L196
```solidity
src/manager/Manager.sol

196:    function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

```

instance #15
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209
```solidity
src/manager/Manager.sol

209:    function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}

```

instance #16
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L564
```solidity
src/governance/governor/Governor.sol

564:    function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {

```

instance #17
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L572
```solidity
src/governance/governor/Governor.sol

572:    function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {

```

instance #18
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L580
```solidity
src/governance/governor/Governor.sol

580:    function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {

```

instance #19
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L588
```solidity
src/governance/governor/Governor.sol

588:    function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {

```

instance #20
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L596
```solidity
src/governance/governor/Governor.sol

596:    function updateVetoer(address _newVetoer) external onlyOwner {

```

instance #21
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L605
```solidity
src/governance/governor/Governor.sol

605:    function burnVetoer() external onlyOwner {

```

instance #22
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L618
```solidity
src/governance/governor/Governor.sol

618:    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {

```

instance #23
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L116
```solidity
src/governance/treasury/Treasury.sol

116:    function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {

```

instance #24
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L180
```solidity
src/governance/treasury/Treasury.sol

180:    function cancel(bytes32 _proposalId) external onlyOwner {

```

 *** 


### G-04: No need to initialize non-constant/non-immutable variables to zero
Since the default value is already zero, overwriting is not required.
Saves `8 gas` per instance.

Total instances of this issue: 5

instance #1
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119
```solidity
src/token/metadata/MetadataRenderer.sol

119:            for (uint256 i = 0; i < numNewProperties; ++i) {

```

instance #2
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133
```solidity
src/token/metadata/MetadataRenderer.sol

133:            for (uint256 i = 0; i < numNewItems; ++i) {

```

instance #3
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189
```solidity
src/token/metadata/MetadataRenderer.sol

189:            for (uint256 i = 0; i < numProperties; ++i) {

```

instance #4
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229
```solidity
src/token/metadata/MetadataRenderer.sol

229:            for (uint256 i = 0; i < numProperties; ++i) {

```

instance #5
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162
```solidity
src/governance/treasury/Treasury.sol

162:            for (uint256 i = 0; i < numTargets; ++i) {

```

 *** 


### G-05: Using uints/ints smaller than 256 bits increases overhead
Gas usage becomes higher with uint/int smaller than 256 bits because EVM operates on 32 bytes and uses additional operations to reduce the size from 32 bytes to the target size.

Total instances of this issue: 28

instance #1
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L18
```solidity
src/token/types/TokenTypesV1.sol

18:        uint96 totalSupply;

```

instance #2
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L20
```solidity
src/token/types/TokenTypesV1.sol

20:        uint8 numFounders;

```

instance #3
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L21
```solidity
src/token/types/TokenTypesV1.sol

21:        uint8 totalOwnership;

```

instance #4
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L30
```solidity
src/token/types/TokenTypesV1.sol

30:        uint8 ownershipPct;

```

instance #5
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L31
```solidity
src/token/types/TokenTypesV1.sol

31:        uint32 vestExpiry;

```

instance #6
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/types/MetadataRendererTypesV1.sol#L20
```solidity
src/token/metadata/types/MetadataRendererTypesV1.sol

20:        uint16 referenceSlot;

```

instance #7
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L16
```solidity
src/auction/types/AuctionTypesV1.sol

16:        uint40 duration;

```

instance #8
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L17
```solidity
src/auction/types/AuctionTypesV1.sol

17:        uint40 timeBuffer;

```

instance #9
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L18
```solidity
src/auction/types/AuctionTypesV1.sol

18:        uint8 minBidIncrement;

```

instance #10
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L33
```solidity
src/auction/types/AuctionTypesV1.sol

33:        uint40 startTime;

```

instance #11
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L34
```solidity
src/auction/types/AuctionTypesV1.sol

34:        uint40 endTime;

```

instance #12
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L21
```solidity
src/governance/governor/types/GovernorTypesV1.sol

21:        uint16 proposalThresholdBps;

```

instance #13
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L22
```solidity
src/governance/governor/types/GovernorTypesV1.sol

22:        uint16 quorumThresholdBps;

```

instance #14
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L24
```solidity
src/governance/governor/types/GovernorTypesV1.sol

24:        uint48 votingDelay;

```

instance #15
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L25
```solidity
src/governance/governor/types/GovernorTypesV1.sol

25:        uint48 votingPeriod;

```

instance #16
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L44
```solidity
src/governance/governor/types/GovernorTypesV1.sol

44:        uint32 timeCreated;

```

instance #17
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L45
```solidity
src/governance/governor/types/GovernorTypesV1.sol

45:        uint32 againstVotes;

```

instance #18
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L46
```solidity
src/governance/governor/types/GovernorTypesV1.sol

46:        uint32 forVotes;

```

instance #19
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L47
```solidity
src/governance/governor/types/GovernorTypesV1.sol

47:        uint32 abstainVotes;

```

instance #20
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L48
```solidity
src/governance/governor/types/GovernorTypesV1.sol

48:        uint32 voteStart;

```

instance #21
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L49
```solidity
src/governance/governor/types/GovernorTypesV1.sol

49:        uint32 voteEnd;

```

instance #22
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L50
```solidity
src/governance/governor/types/GovernorTypesV1.sol

50:        uint32 proposalThreshold;

```

instance #23
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L51
```solidity
src/governance/governor/types/GovernorTypesV1.sol

51:        uint32 quorumVotes;

```

instance #24
Permalink: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L208-L216
```solidity
src/governance/governor/Governor.sol

208:    function castVoteBySig(
209:        address _voter,
210:        bytes32 _proposalId,
211:        uint256 _support,
212:        uint256 _deadline,
213:        uint8 _v,
214:        bytes32 _r,
215:        bytes32 _s
216:    ) external returns (uint256) {

```

instance #25
Permalink: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L176-L184
```solidity
src/governance/governor/IGovernor.sol

176:    function castVoteBySig(
177:        address voter,
178:        bytes32 proposalId,
179:        uint256 support,
180:        uint256 deadline,
181:        uint8 v,
182:        bytes32 r,
183:        bytes32 s
184:    ) external returns (uint256);

```

instance #26
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/types/TreasuryTypesV1.sol#L12
```solidity
src/governance/treasury/types/TreasuryTypesV1.sol

12:        uint128 gracePeriod;

```

instance #27
Link: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/types/TreasuryTypesV1.sol#L13
```solidity
src/governance/treasury/types/TreasuryTypesV1.sol

13:        uint128 delay;

```

instance #28
Permalink: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L144-L151
```solidity
src/lib/token/ERC721Votes.sol

144:    function delegateBySig(
145:        address _from,
146:        address _to,
147:        uint256 _deadline,
148:        uint8 _v,
149:        bytes32 _r,
150:        bytes32 _s
151:    ) external {

```

