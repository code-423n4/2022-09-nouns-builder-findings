## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```

## [G-02] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::95 => middle = high - (high - low) / 2;
```

## [G-03] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2022-09-nouns-builder/src/lib/utils/Address.sol::37 => function functionDelegateCall(address _target, bytes memory _data) internal returns (bytes memory) {
2022-09-nouns-builder/src/lib/utils/Address.sol::46 => function verifyCallResult(bool _success, bytes memory _returndata) internal pure returns (bytes memory) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::255 => function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::308 => function _encodeAsJson(bytes memory _jsonBlob) private pure returns (string memory) {
```

## [G-04] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-09-nouns-builder/src/manager/Manager.sol::25 => address public immutable tokenImpl;
2022-09-nouns-builder/src/manager/Manager.sol::28 => address public immutable metadataImpl;
2022-09-nouns-builder/src/manager/Manager.sol::31 => address public immutable auctionImpl;
2022-09-nouns-builder/src/manager/Manager.sol::34 => address public immutable treasuryImpl;
2022-09-nouns-builder/src/manager/Manager.sol::37 => address public immutable governorImpl;
```

## [G-05] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-09-nouns-builder/src/auction/Auction.sol::244 => function unpause() external onlyOwner {
2022-09-nouns-builder/src/auction/Auction.sol::263 => function pause() external onlyOwner {
2022-09-nouns-builder/src/auction/Auction.sol::307 => function setDuration(uint256 _duration) external onlyOwner {
2022-09-nouns-builder/src/auction/Auction.sol::315 => function setReservePrice(uint256 _reservePrice) external onlyOwner {
2022-09-nouns-builder/src/auction/Auction.sol::323 => function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {
2022-09-nouns-builder/src/auction/Auction.sol::331 => function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {
2022-09-nouns-builder/src/governance/governor/Governor.sol::564 => function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {
2022-09-nouns-builder/src/governance/governor/Governor.sol::572 => function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {
2022-09-nouns-builder/src/governance/governor/Governor.sol::580 => function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {
2022-09-nouns-builder/src/governance/governor/Governor.sol::588 => function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {
2022-09-nouns-builder/src/governance/governor/Governor.sol::596 => function updateVetoer(address _newVetoer) external onlyOwner {
2022-09-nouns-builder/src/governance/governor/Governor.sol::605 => function burnVetoer() external onlyOwner {
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::116 => function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::180 => function cancel(bytes32 _proposalId) external onlyOwner {
2022-09-nouns-builder/src/lib/proxy/UUPS.sol::47 => function upgradeTo(address _newImpl) external onlyProxy {
2022-09-nouns-builder/src/lib/utils/Ownable.sol::63 => function transferOwnership(address _newOwner) public onlyOwner {
2022-09-nouns-builder/src/lib/utils/Ownable.sol::71 => function safeTransferOwnership(address _newOwner) public onlyOwner {
2022-09-nouns-builder/src/lib/utils/Ownable.sol::78 => function acceptOwnership() public onlyPendingOwner {
2022-09-nouns-builder/src/lib/utils/Ownable.sol::87 => function cancelOwnershipTransfer() public onlyOwner {
2022-09-nouns-builder/src/manager/Manager.sol::187 => function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
2022-09-nouns-builder/src/manager/Manager.sol::196 => function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::347 => function updateContractImage(string memory _newContractImage) external onlyOwner {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::355 => function updateRendererBase(string memory _newRendererBase) external onlyOwner {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::363 => function updateDescription(string memory _newDescription) external onlyOwner {
```

## [G-06] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::269 => receive() external payable {}
2022-09-nouns-builder/src/lib/token/ERC721.sol::54 => function tokenURI(uint256 _tokenId) public view virtual returns (string memory) {}
2022-09-nouns-builder/src/lib/token/ERC721.sol::57 => function contractURI() public view virtual returns (string memory) {}
2022-09-nouns-builder/src/manager/Manager.sol::209 => function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
```

## [G-07] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-09-nouns-builder/src/auction/types/AuctionTypesV1.sol::18 => uint8 minBidIncrement;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::21 => uint16 proposalThresholdBps;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::22 => uint16 quorumThresholdBps;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::44 => uint32 timeCreated;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::45 => uint32 againstVotes;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::46 => uint32 forVotes;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::47 => uint32 abstainVotes;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::48 => uint32 voteStart;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::49 => uint32 voteEnd;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::50 => uint32 proposalThreshold;
2022-09-nouns-builder/src/governance/governor/types/GovernorTypesV1.sol::51 => uint32 quorumVotes;
2022-09-nouns-builder/src/governance/treasury/types/TreasuryTypesV1.sol::12 => uint128 gracePeriod;
2022-09-nouns-builder/src/governance/treasury/types/TreasuryTypesV1.sol::13 => uint128 delay;
2022-09-nouns-builder/src/lib/interfaces/IERC721Votes.sol::36 => uint64 timestamp;
2022-09-nouns-builder/src/lib/utils/Initializable.sol::17 => uint8 internal _initialized;
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::179 => uint16[16] storage tokenAttributes = attributes[_tokenId];
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::216 => uint16[16] memory tokenAttributes = attributes[_tokenId];
2022-09-nouns-builder/src/token/metadata/types/MetadataRendererTypesV1.sol::20 => uint16 referenceSlot;
2022-09-nouns-builder/src/token/types/TokenTypesV1.sol::20 => uint8 numFounders;
2022-09-nouns-builder/src/token/types/TokenTypesV1.sol::21 => uint8 totalOwnership;
2022-09-nouns-builder/src/token/types/TokenTypesV1.sol::30 => uint8 ownershipPct;
2022-09-nouns-builder/src/token/types/TokenTypesV1.sol::31 => uint32 vestExpiry;
```

## [G-08] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {
2022-09-nouns-builder/src/lib/token/ERC721.sol::141 => ++balances[_to];
2022-09-nouns-builder/src/lib/token/ERC721.sol::199 => ++balances[_to];
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::207 => uint256 nCheckpoints = numCheckpoints[_from]++;
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::222 => uint256 nCheckpoints = numCheckpoints[_to]++;
2022-09-nouns-builder/src/token/Token.sol::80 => for (uint256 i; i < numFounders; ++i) {
2022-09-nouns-builder/src/token/Token.sol::91 => uint256 founderId = settings.numFounders++;
2022-09-nouns-builder/src/token/Token.sol::108 => for (uint256 j; j < founderPct; ++j) {
2022-09-nouns-builder/src/token/Token.sol::132 => while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;
2022-09-nouns-builder/src/token/Token.sol::154 => tokenId = settings.totalSupply++;
2022-09-nouns-builder/src/token/Token.sol::261 => for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```

## [G-09] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-09-nouns-builder/src/governance/governor/Governor.sol::280 => proposal.againstVotes += uint32(weight);
2022-09-nouns-builder/src/governance/governor/Governor.sol::285 => proposal.forVotes += uint32(weight);
2022-09-nouns-builder/src/governance/governor/Governor.sol::290 => proposal.abstainVotes += uint32(weight);
2022-09-nouns-builder/src/token/Token.sol::88 => if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
2022-09-nouns-builder/src/token/Token.sol::118 => (baseTokenId += schedule) % 100;
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::140 => _propertyId += numStoredProperties;
```

## [G-10] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2022-09-nouns-builder/src/governance/governor/Governor.sol::104 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
2022-09-nouns-builder/src/governance/governor/Governor.sol::230 => keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::107 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
2022-09-nouns-builder/src/lib/utils/EIP712.sol::69 => return keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::251 => return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
```

## [G-11] Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2022-09-nouns-builder/src/lib/interfaces/IEIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IInitializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IOwnable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IPausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/proxy/ERC1967Proxy.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/proxy/ERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/proxy/UUPS.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/token/ERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Address.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/EIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Initializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Ownable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Pausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/ReentrancyGuard.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/SafeCast.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/TokenReceiver.sol::2 => pragma solidity ^0.8.0;
```

## [G-12] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-09-nouns-builder/src/lib/token/ERC721.sol::30 => mapping(address => uint256) internal balances;
2022-09-nouns-builder/src/lib/token/ERC721.sol::38 => mapping(address => mapping(address => bool)) internal operatorApprovals;
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::29 => mapping(address => address) internal delegation;
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::33 => mapping(address => uint256) internal numCheckpoints;
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::37 => mapping(address => mapping(uint256 => Checkpoint)) internal checkpoints;
2022-09-nouns-builder/src/manager/storage/ManagerStorageV1.sol::10 => mapping(address => mapping(address => bool)) internal isUpgrade;
```

## [G-13] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-09-nouns-builder/src/auction/Auction.sol::184 => if (_auction.highestBidder != address(0)) {
2022-09-nouns-builder/src/lib/token/ERC721.sol::194 => if (owners[_tokenId] != address(0)) revert ALREADY_MINTED();
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::205 => if (_from != address(0)) {
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::220 => if (_to != address(0)) {
2022-09-nouns-builder/src/token/Token.sol::132 => while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;
```

## [G-14] Use selfbalance()

Use selfbalance() instead of address(this).balance when getting your contract's balance of ETH to save gas.

```
2022-09-nouns-builder/src/auction/Auction.sol::346 => if (address(this).balance < _amount) revert INSOLVENT();
```

## [G-15] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::216 => uint16[16] memory tokenAttributes = attributes[_tokenId];
```

## [G-16] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2022-09-nouns-builder/src/token/Token.sol::71 => function _addFounders(IManager.FounderParams[] calldata _founders) internal {
2022-09-nouns-builder/src/token/Token.sol::130 => function _getNextTokenId(uint256 _tokenId) internal view returns (uint256) {
```