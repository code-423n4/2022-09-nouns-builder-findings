# 2022-09-NOUNS-BUILDER
## Gas Optimizations Report

### Functions that are access-restricted from most users may be marked as `payable`
Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **26** instances of this issue:_

```solidity
File: src/auction/Auction.sol

244:  function unpause() external onlyOwner {

263:  function pause() external onlyOwner {

307:  function setDuration(uint256 _duration) external onlyOwner {

315:  function setReservePrice(uint256 _reservePrice) external onlyOwner {

323:  function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {

331:  function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {

374:  function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol

```solidity
File: src/governance/governor/Governor.sol

564:  function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {

572:  function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {

580:  function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {

588:  function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {

596:  function updateVetoer(address _newVetoer) external onlyOwner {

605:  function burnVetoer() external onlyOwner {

618:  function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol

```solidity
File: src/governance/treasury/Treasury.sol

116:  function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {

180:  function cancel(bytes32 _proposalId) external onlyOwner {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol

```solidity
File: src/lib/utils/Ownable.sol

63:   function transferOwnership(address _newOwner) public onlyOwner {

71:   function safeTransferOwnership(address _newOwner) public onlyOwner {

87:   function cancelOwnershipTransfer() public onlyOwner {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol

```solidity
File: src/manager/Manager.sol

187:  function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

196:  function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

209:  function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol

```solidity
File: src/token/metadata/MetadataRenderer.sol

347:  function updateContractImage(string memory _newContractImage) external onlyOwner {

355:  function updateRendererBase(string memory _newRendererBase) external onlyOwner {

363:  function updateDescription(string memory _newDescription) external onlyOwner {

376:  function _authorizeUpgrade(address _impl) internal view override onlyOwner {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **7** instances of this issue:_

```solidity
File: src/auction/storage/AuctionStorageV1.sol

12:   Settings internal settings;

15:   Token public token;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/storage/AuctionStorageV1.sol

```solidity
File: src/governance/governor/storage/GovernorStorageV1.sol

11:   Settings internal settings;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/storage/GovernorStorageV1.sol

```solidity
File: src/governance/treasury/storage/TreasuryStorageV1.sol

11:   Settings internal settings;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/storage/TreasuryStorageV1.sol

```solidity
File: src/lib/proxy/UUPS.sol

43:   function _authorizeUpgrade(address _newImpl) internal virtual;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol

```solidity
File: src/token/metadata/storage/MetadataRendererStorageV1.sol

11:   Settings internal settings;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/storage/MetadataRendererStorageV1.sol

```solidity
File: src/token/storage/TokenStorageV1.sol

11:   Settings internal settings;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/storage/TokenStorageV1.sol

### Using bools for storage variables incurs overhead
Use `uint256(1)` and `uint256(2)` for `true`/`false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

_There are **2** instances of this issue:_

```solidity
File: src/lib/utils/Initializable.sol

20:   bool internal _initializing;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol

```solidity
File: src/lib/utils/Pausable.sol

15:   bool internal _paused;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Pausable.sol

### Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
It is expected that the value should be converted into a constant value at compile time. But actually the expression is re-calculated each time the constant is referenced.

_There are **3** instances of this issue:_

```solidity
File: src/governance/governor/Governor.sol

27:   bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol

```solidity
File: src/lib/token/ERC721Votes.sol

21:   bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol

```solidity
File: src/lib/utils/EIP712.sol

19:   bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/EIP712.sol

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.
This change would save up to 6 gas per instance/loop.

_There are **2** instances of this issue:_

```solidity
File: src/token/Token.sol

91:   uint256 founderId = settings.numFounders++;

154:  tokenId = settings.totalSupply++;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol

### `internal` and `private` functions that are called only once should be inlined.
The execution of a non-inlined function would cost up to 40 more gas because of two extra `jump`s as well as some other instructions.

_There are **3** instances of this issue:_

```solidity
File: src/token/metadata/MetadataRenderer.sol

250:  function _generateSeed(uint256 _tokenId) private view returns (uint256) {

255:  function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol

```solidity
File: src/token/Token.sol

177:  function _isForFounder(uint256 _tokenId) private returns (bool) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol

### Using `!= 0` on `uints` costs less gas than `> 0`.
This change saves 3 gas per instance/loop

_There are **1** instances of this issue:_

```solidity
File: src/lib/token/ERC721Votes.sol

203:  if (_from != _to && _amount > 0) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied
Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **5** instances of this issue:_

```solidity
File: src/governance/treasury/Treasury.sol

162:  for (uint256 i = 0; i < numTargets; ++i) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol

```solidity
File: src/token/metadata/MetadataRenderer.sol

119:  for (uint256 i = 0; i < numNewProperties; ++i) {

133:  for (uint256 i = 0; i < numNewItems; ++i) {

189:  for (uint256 i = 0; i < numProperties; ++i) {

229:  for (uint256 i = 0; i < numProperties; ++i) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol

### Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **1** instances of this issue:_

```solidity
File: src/governance/governor/Governor.sol

27:   bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops
When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **8** instances of this issue:_

```solidity
File: src/governance/treasury/Treasury.sol

162:  for (uint256 i = 0; i < numTargets; ++i) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol

```solidity
File: src/token/metadata/MetadataRenderer.sol

119:  for (uint256 i = 0; i < numNewProperties; ++i) {

133:  for (uint256 i = 0; i < numNewItems; ++i) {

189:  for (uint256 i = 0; i < numProperties; ++i) {

229:  for (uint256 i = 0; i < numProperties; ++i) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol

```solidity
File: src/token/Token.sol

80:   for (uint256 i; i < numFounders; ++i) {

108:  for (uint256 j; j < founderPct; ++j) {

261:  for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead
'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **25** instances of this issue:_

```solidity
File: src/auction/types/AuctionTypesV1.sol

18:   uint8 minBidIncrement;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol

```solidity
File: src/governance/governor/Governor.sol

213:  uint8 _v,
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol

```solidity
File: src/governance/governor/IGovernor.sol

181:  uint8 v,
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol

```solidity
File: src/governance/governor/types/GovernorTypesV1.sol

21:   uint16 proposalThresholdBps;

22:   uint16 quorumThresholdBps;

44:   uint32 timeCreated;

45:   uint32 againstVotes;

46:   uint32 forVotes;

47:   uint32 abstainVotes;

48:   uint32 voteStart;

49:   uint32 voteEnd;

50:   uint32 proposalThreshold;

51:   uint32 quorumVotes;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol

```solidity
File: src/governance/treasury/types/TreasuryTypesV1.sol

12:   uint128 gracePeriod;

13:   uint128 delay;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/types/TreasuryTypesV1.sol

```solidity
File: src/lib/interfaces/IERC721Votes.sol

36:   uint64 timestamp;

72:   uint8 v,
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IERC721Votes.sol

```solidity
File: src/lib/token/ERC721Votes.sol

148:  uint8 _v,
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol

```solidity
File: src/lib/utils/Initializable.sol

17:   uint8 internal _initialized;

55:   modifier reinitializer(uint8 _version) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol

```solidity
File: src/token/metadata/types/MetadataRendererTypesV1.sol

20:   uint16 referenceSlot;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/types/MetadataRendererTypesV1.sol

```solidity
File: src/token/types/TokenTypesV1.sol

20:   uint8 numFounders;

21:   uint8 totalOwnership;

30:   uint8 ownershipPct;

31:   uint32 vestExpiry;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol

### Use `calldata` instead of `memory` for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **24** instances of this issue:_

```solidity
File: src/governance/governor/Governor.sol

99:   address[] memory _targets,

100:  uint256[] memory _values,

101:  bytes[] memory _calldatas,

117:  address[] memory _targets,

118:  uint256[] memory _values,

119:  bytes[] memory _calldatas,

120:  string memory _description

195:  string memory _reason

252:  string memory _reason
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol

```solidity
File: src/lib/proxy/ERC1967Upgrade.sol

35:   bytes memory _data,

56:   bytes memory _data,
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol

```solidity
File: src/lib/proxy/UUPS.sol

55:   function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol

```solidity
File: src/lib/token/ERC721.sol

      /// @audit Store `_name` in calldata.
47:   function __ERC721_init(string memory _name, string memory _symbol) internal onlyInitializing {

      /// @audit Store `_symbol` in calldata.
47:   function __ERC721_init(string memory _name, string memory _symbol) internal onlyInitializing {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol

```solidity
File: src/lib/utils/Address.sol

      /// @audit Store `_data` in calldata.
37:   function functionDelegateCall(address _target, bytes memory _data) internal returns (bytes memory) {

      /// @audit Store `_returndata` in calldata.
46:   function verifyCallResult(bool _success, bytes memory _returndata) internal pure returns (bytes memory) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Address.sol

```solidity
File: src/lib/utils/EIP712.sol

      /// @audit Store `_name` in calldata.
48:   function __EIP712_init(string memory _name, string memory _version) internal onlyInitializing {

      /// @audit Store `_version` in calldata.
48:   function __EIP712_init(string memory _name, string memory _version) internal onlyInitializing {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/EIP712.sol

```solidity
File: src/token/metadata/MetadataRenderer.sol

      /// @audit Store `_item` in calldata.
255:  function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {

      /// @audit Store `_propertyName` in calldata.
255:  function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {

      /// @audit Store `_jsonBlob` in calldata.
308:  function _encodeAsJson(bytes memory _jsonBlob) private pure returns (string memory) {

347:  function updateContractImage(string memory _newContractImage) external onlyOwner {

355:  function updateRendererBase(string memory _newRendererBase) external onlyOwner {

363:  function updateDescription(string memory _newDescription) external onlyOwner {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`
In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **5** instances of this issue:_

```solidity
File: src/auction/Auction.sol

98:   if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol

```solidity
File: src/governance/treasury/Treasury.sol

89:   return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol

```solidity
File: src/lib/token/ERC721Votes.sol

61:   if (_timestamp >= block.timestamp) revert INVALID_TIMESTAMP();

78:   if (accountCheckpoints[lastCheckpoint].timestamp <= _timestamp) return accountCheckpoints[lastCheckpoint].votes;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol

```solidity
File: src/lib/utils/Initializable.sol

56:   if (_initializing || _initialized >= _version) revert ALREADY_INITIALIZED();
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol

### Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

_There are **20** instances of this issue:_

```solidity
File: src/lib/interfaces/IEIP712.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IEIP712.sol

```solidity
File: src/lib/interfaces/IERC1967Upgrade.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IERC1967Upgrade.sol

```solidity
File: src/lib/interfaces/IERC721.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IERC721.sol

```solidity
File: src/lib/interfaces/IERC721Votes.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IERC721Votes.sol

```solidity
File: src/lib/interfaces/IInitializable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IInitializable.sol

```solidity
File: src/lib/interfaces/IOwnable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IOwnable.sol

```solidity
File: src/lib/interfaces/IPausable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IPausable.sol

```solidity
File: src/lib/proxy/ERC1967Proxy.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Proxy.sol

```solidity
File: src/lib/proxy/ERC1967Upgrade.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol

```solidity
File: src/lib/proxy/UUPS.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol

```solidity
File: src/lib/token/ERC721.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol

```solidity
File: src/lib/token/ERC721Votes.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol

```solidity
File: src/lib/utils/Address.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Address.sol

```solidity
File: src/lib/utils/EIP712.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/EIP712.sol

```solidity
File: src/lib/utils/Initializable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol

```solidity
File: src/lib/utils/Ownable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol

```solidity
File: src/lib/utils/Pausable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Pausable.sol

```solidity
File: src/lib/utils/ReentrancyGuard.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/ReentrancyGuard.sol

```solidity
File: src/lib/utils/SafeCast.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/SafeCast.sol

```solidity
File: src/lib/utils/TokenReceiver.sol

2:    pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/TokenReceiver.sol
