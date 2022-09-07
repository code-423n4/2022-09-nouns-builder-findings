

### [G001] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gsset (20000 gas)

#### Findings:
```

2022-09-nouns-builder-main/src/auction/storage/AuctionStorageV1.sol::15 => Token public token;
2022-09-nouns-builder-main/src/auction/storage/AuctionStorageV1.sol::18 => Auction public auction;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::19 => string public name;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::22 => string public symbol;
```





### [G002] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor, avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper

#### Findings:
```
2022-09-nouns-builder-main/src/auction/storage/AuctionStorageV1.sol::15 => Token public token;
2022-09-nouns-builder-main/src/auction/storage/AuctionStorageV1.sol::18 => Auction public auction;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::19 => string public name;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::22 => string public symbol;
```


### [G003] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops



#### Findings:
```
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {
2022-09-nouns-builder-main/src/token/Token.sol::80 => for (uint256 i; i < numFounders; ++i) {
2022-09-nouns-builder-main/src/token/Token.sol::108 => for (uint256 j; j < founderPct; ++j) {
2022-09-nouns-builder-main/src/token/Token.sol::261 => for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```

### [G004] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::185 => return _castVote(_proposalId, msg.sender, _support, "");
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::197 => return _castVote(_proposalId, msg.sender, _support, _reason);
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::241 => return _castVote(_proposalId, _voter, _support, "");
2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::62 => return _IMPLEMENTATION_SLOT;
2022-09-nouns-builder-main/src/lib/utils/Address.sol::48 => return _returndata;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::53 => return _owner;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::58 => return _pendingOwner;
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::45 => return _paused;
2022-09-nouns-builder-main/src/token/Token.sol::134 => return _tokenId;
```





### [G005] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement



#### Findings:
```
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::61 => if (_data.length > 0 || _forceCall) {
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::203 => if (_from != _to && _amount > 0) {
2022-09-nouns-builder-main/src/lib/utils/Address.sol::50 => if (_returndata.length > 0) {
```





### [G006] It costs more gas to initialize variables to zero than to let the default of zero be applied



#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::227 => auction.highestBid = 0;
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::83 => return timestamps[_proposalId] != 0;
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```





### [G007] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)



#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::230 => keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::162 => abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::207 => uint256 nCheckpoints = numCheckpoints[_from]++;
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::222 => uint256 nCheckpoints = numCheckpoints[_to]++;
2022-09-nouns-builder-main/src/token/Token.sol::91 => uint256 founderId = settings.numFounders++;
2022-09-nouns-builder-main/src/token/Token.sol::118 => (baseTokenId += schedule) % 100;
2022-09-nouns-builder-main/src/token/Token.sol::154 => tokenId = settings.totalSupply++;
```






### [G008] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

#### Impact
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detail description of the issue

#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::104 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::142 => bytes32 descriptionHash = keccak256(bytes(_description));
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::21 => bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::19 => bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```




### [G009] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::244 => function unpause() external onlyOwner {

2022-09-nouns-builder-main/src/auction/Auction.sol::263 => function pause() external onlyOwner {

2022-09-nouns-builder-main/src/auction/Auction.sol::307 => function setDuration(uint256 _duration) external onlyOwner {

2022-09-nouns-builder-main/src/auction/Auction.sol::315 => function setReservePrice(uint256 _reservePrice) external onlyOwner {

2022-09-nouns-builder-main/src/auction/Auction.sol::323 => function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {

2022-09-nouns-builder-main/src/auction/Auction.sol::331 => function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {

2022-09-nouns-builder-main/src/auction/Auction.sol::374 => function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {

2022-09-nouns-builder-main/src/governance/governor/Governor.sol::564 => function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {

2022-09-nouns-builder-main/src/governance/governor/Governor.sol::572 => function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {

2022-09-nouns-builder-main/src/governance/governor/Governor.sol::580 => function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {

2022-09-nouns-builder-main/src/governance/governor/Governor.sol::588 => function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {

2022-09-nouns-builder-main/src/governance/governor/Governor.sol::596 => function updateVetoer(address _newVetoer) external onlyOwner {

2022-09-nouns-builder-main/src/governance/governor/Governor.sol::605 => function burnVetoer() external onlyOwner {

2022-09-nouns-builder-main/src/governance/governor/Governor.sol::618 => function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {

2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::116 => function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {

2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::180 => function cancel(bytes32 _proposalId) external onlyOwner {

2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::47 => function upgradeTo(address _newImpl) external onlyProxy {

2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::55 => function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {

2022-09-nouns-builder-main/src/lib/token/ERC721.sol::47 => function __ERC721_init(string memory _name, string memory _symbol) internal onlyInitializing {

2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::48 => function __EIP712_init(string memory _name, string memory _version) internal onlyInitializing {

2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::45 => function __Ownable_init(address _initialOwner) internal onlyInitializing {

2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::63 => function transferOwnership(address _newOwner) public onlyOwner {

2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::71 => function safeTransferOwnership(address _newOwner) public onlyOwner {

2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::78 => function acceptOwnership() public onlyPendingOwner {

2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::87 => function cancelOwnershipTransfer() public onlyOwner {

2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::39 => function __Pausable_init(bool _initPause) internal onlyInitializing {

2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::34 => function __ReentrancyGuard_init() internal onlyInitializing {

2022-09-nouns-builder-main/src/manager/Manager.sol::187 => function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

2022-09-nouns-builder-main/src/manager/Manager.sol::196 => function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

2022-09-nouns-builder-main/src/manager/Manager.sol::209 => function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}

2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::347 => function updateContractImage(string memory _newContractImage) external onlyOwner 

2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::355 => function updateRendererBase(string memory _newRendererBase) external onlyOwner {

2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::363 => function updateDescription(string memory _newDescription) external onlyOwner {

2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::376 => function _authorizeUpgrade(address _impl) internal view override onlyOwner {
```





### [G010] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.10 to have external calls skip
 contract existence checks if the external call has a return value

#### Findings:
```
2022-09-nouns-builder-main/src/lib/interfaces/IEIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IInitializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IOwnable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IPausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IUUPS.sol::2 => pragma solidity ^0.8.15;
2022-09-nouns-builder-main/src/lib/interfaces/IWETH.sol::2 => pragma solidity ^0.8.15;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Proxy.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Address.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::2 => pragma solidity ^0.8.0;
```





### [G011] Bitshift for divide by 2

#### Impact
When multiply or dividing by a power of two, it is cheaper to bitshift than to use standard math operations.

#### Findings:
```
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::95 => middle = high - (high - low) / 2;
```





### [G011] Use simple comparison in trinary logic

#### Impact
The comparison operators >= and <= use more gas than >, 
<, or ==. Replacing the  >= and ≤ operators with a comparison 
operator that has an opcode in the EVM saves gas.
#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::98 => if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::89 => return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::61 => if (_timestamp >= block.timestamp) revert INVALID_TIMESTAMP();
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::78 => if (accountCheckpoints[lastCheckpoint].timestamp <= _timestamp) return accountCheckpoints[lastCheckpoint].votes;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::56 => if (_initializing || _initialized >= _version) revert ALREADY_INITIALIZED();

```





### [G012] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.

There are several cases of function arguments using memory instead of calldata
#### Findings:
```
2022-09-nouns-builder-main/src/lib/interfaces/IUUPS.sol::35 => function upgradeToAndCall(address newImpl, bytes memory data) external payable;
2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::55 => function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::47 => function __ERC721_init(string memory _name, string memory _symbol) internal onlyInitializing {
2022-09-nouns-builder-main/src/lib/utils/Address.sol::37 => function functionDelegateCall(address _target, bytes memory _data) internal returns (bytes memory) {
2022-09-nouns-builder-main/src/lib/utils/Address.sol::46 => function verifyCallResult(bool _success, bytes memory _returndata) internal pure returns (bytes memory) {
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::48 => function __EIP712_init(string memory _name, string memory _version) internal onlyInitializing {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::347 => function updateContractImage(string memory _newContractImage) external onlyOwner {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::355 => function updateRendererBase(string memory _newRendererBase) external onlyOwner {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::363 => function updateDescription(string memory _newDescription) external onlyOwner {
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::76 => function updateContractImage(string memory newContractImage) external;
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::80 => function updateRendererBase(string memory newRendererBase) external;
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::84 => function updateDescription(string memory newDescription) external;
```









### [G013] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done at some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value check here:

#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::192 => token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
2022-09-nouns-builder-main/src/auction/Auction.sol::363 => IWETH(WETH).transfer(_to, _amount);
```





### [G014] Unnecessary `initialize()` function

#### Impact
The initialize() function isn’t an initializer.

#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::54 => function initialize(
2022-09-nouns-builder-main/src/auction/IAuction.sol::93 => function initialize(
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::57 => function initialize(
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::119 => function initialize(
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::64 => function initialize(address governor, uint256 delay) external;
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::43 => function initialize(address _governor, uint256 _delay) external initializer {
2022-09-nouns-builder-main/src/manager/Manager.sol::80 => function initialize(address _owner) external initializer {
2022-09-nouns-builder-main/src/manager/Manager.sol::132 => IToken(token).initialize(_founderParams, _tokenParams.initStrings, metadata, auction);
2022-09-nouns-builder-main/src/manager/Manager.sol::133 => IBaseMetadata(metadata).initialize(_tokenParams.initStrings, token, founder, treasury);
2022-09-nouns-builder-main/src/manager/Manager.sol::134 => IAuction(auction).initialize(token, founder, treasury, _auctionParams.duration, _auctionParams.reservePrice);
2022-09-nouns-builder-main/src/manager/Manager.sol::135 => ITreasury(treasury).initialize(governor, _govParams.timelockDelay);
2022-09-nouns-builder-main/src/manager/Manager.sol::136 => IGovernor(governor).initialize(
2022-09-nouns-builder-main/src/token/IToken.sol::51 => function initialize(
2022-09-nouns-builder-main/src/token/Token.sol::43 => function initialize(
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::45 => function initialize(
2022-09-nouns-builder-main/src/token/metadata/interfaces/IBaseMetadata.sol::27 => function initialize(
```




### [G015] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot

#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/storage/GovernorStorageV1.sol::15 => mapping(bytes32 => Proposal) internal proposals;
2022-09-nouns-builder-main/src/governance/governor/storage/GovernorStorageV1.sol::19 => mapping(bytes32 => mapping(address => bool)) internal hasVoted;


2022-09-nouns-builder-main/src/lib/token/ERC721.sol::26 => mapping(uint256 => address) internal owners;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::30 => mapping(address => uint256) internal balances;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::34 => mapping(uint256 => address) internal tokenApprovals;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::38 => mapping(address => mapping(address => bool)) internal operatorApprovals;


2022-09-nouns-builder-main/src/token/storage/TokenStorageV1.sol::15 => mapping(uint256 => Founder) internal founder;
2022-09-nouns-builder-main/src/token/storage/TokenStorageV1.sol::19 => mapping(uint256 => Founder) internal tokenRecipient;
```




### [G016] Using `bools` for storage incurs overhead



#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::136 => bool extend;
2022-09-nouns-builder-main/src/auction/Auction.sol::349 => bool success;
2022-09-nouns-builder-main/src/auction/types/AuctionTypesV1.sol::35 => bool settled;
2022-09-nouns-builder-main/src/governance/governor/storage/GovernorStorageV1.sol::19 => mapping(bytes32 => mapping(address => bool)) internal hasVoted;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::52 => bool executed;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::53 => bool canceled;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::54 => bool vetoed;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::38 => mapping(address => mapping(address => bool)) internal operatorApprovals;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::20 => bool internal _initializing;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::34 => bool isTopLevelCall = !_initializing;
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::15 => bool internal _paused;
2022-09-nouns-builder-main/src/manager/storage/ManagerStorageV1.sol::10 => mapping(address => mapping(address => bool)) internal isUpgrade;
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::231 => bool isLast = i == lastProperty;
2022-09-nouns-builder-main/src/token/metadata/types/MetadataRendererTypesV1.sol::11 => bool isNewProperty;
```


### [G017] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable 
getter functions for deployment calldata, and not adding another entry 
to the method ID table
#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```



### [G018]  Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::269 => receive() external payable {}
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::54 => function tokenURI(uint256 _tokenId) public view virtual returns (string memory) {}
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::57 => function contractURI() public view virtual returns (string memory) {}
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::239 => ) internal virtual {}
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::249 => ) internal virtual {}
2022-09-nouns-builder-main/src/manager/Manager.sol::209 => function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
```




### [G019] Remove or replace unused state variables

#### Impact
Saves a storage slot. If the variable is assigned a non-zero value, 
saves Gsset (20000 gas). If it’s assigned a zero value, saves Gsreset 
(2900 gas). If the variable remains unassigned, there is no gas savings 
unless the variable is public, in which case the 
compiler-generated non-payable getter deployment cost is saved. If the 
state variable is overriding an interface’s public function, mark the 
variable as constant or immutable so that it does not use a storage slot
#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::100 => uint256[] memory _values,
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::118 => uint256[] memory _values,
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::326 => uint256[] calldata _values,
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::21 => uint256[] values,
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::136 => uint256[] memory values,
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::148 => uint256[] memory values,
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::197 => uint256[] memory values,
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::22 => event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::89 => uint256[] calldata values,
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::109 => uint256[] calldata values,
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::103 => uint256[] calldata _values,
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::143 => uint256[] calldata _values,
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::261 => uint256[] memory,
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::262 => uint256[] memory,
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::31 => uint256[] calldata,
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::32 => uint256[] calldata,
```


### [G020] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::104 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::227 => abi.encodePacked(
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::230 => keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::107 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::162 => abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::69 => return keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));
2022-09-nouns-builder-main/src/manager/Manager.sol::68 => metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));
2022-09-nouns-builder-main/src/manager/Manager.sol::69 => auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));
2022-09-nouns-builder-main/src/manager/Manager.sol::70 => treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));
2022-09-nouns-builder-main/src/manager/Manager.sol::71 => governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::251 => return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
```




### [G020] Multiplication/division by two should use bit shifting

#### Impact
<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas
#### Findings:
```
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::95 => middle = high - (high - low) / 2;
```




### [G021] Optimize names to save gas

#### Impact
optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9)
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)
#### Findings:
```
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::15 => abstract contract ERC1967Upgrade is IERC1967Upgrade {
2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::12 => abstract contract UUPS is IUUPS, ERC1967Upgrade {
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::13 => abstract contract ERC721 is IERC721, Initializable {
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::15 => abstract contract ERC721Votes is IERC721Votes, EIP712, ERC721 {
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::13 => abstract contract EIP712 is IEIP712, Initializable {
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::11 => abstract contract Initializable is IInitializable {
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::12 => abstract contract Ownable is IOwnable, Initializable {
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::9 => abstract contract Pausable is IPausable, Initializable {
2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::8 => abstract contract ReentrancyGuard is Initializable {
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::5 => abstract contract ERC721TokenReceiver {
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::17 => abstract contract ERC1155TokenReceiver {
```


### [G022] Use assembly to check for address(0)


#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::104 => if (highestBidder == address(0)) {
2022-09-nouns-builder-main/src/auction/Auction.sol::184 => if (_auction.highestBidder != address(0)) {
2022-09-nouns-builder-main/src/auction/Auction.sol::228 => auction.highestBidder = address(0);
```





### [G023] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed

#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::223 => auction.startTime = uint40(startTime);
2022-09-nouns-builder-main/src/auction/Auction.sol::224 => auction.endTime = uint40(endTime);
2022-09-nouns-builder-main/src/auction/types/AuctionTypesV1.sol::16 => uint40 duration;
2022-09-nouns-builder-main/src/auction/types/AuctionTypesV1.sol::17 => uint40 timeBuffer;
2022-09-nouns-builder-main/src/auction/types/AuctionTypesV1.sol::18 => uint8 minBidIncrement;
2022-09-nouns-builder-main/src/auction/types/AuctionTypesV1.sol::33 => uint40 startTime;
2022-09-nouns-builder-main/src/auction/types/AuctionTypesV1.sol::34 => uint40 endTime;
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::165 => proposal.voteStart = uint32(snapshot);
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::166 => proposal.voteEnd = uint32(deadline);
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::167 => proposal.proposalThreshold = uint32(currentProposalThreshold);
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::168 => proposal.quorumVotes = uint32(quorum());
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::170 => proposal.timeCreated = uint32(block.timestamp);
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::213 => uint8 _v,
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::280 => proposal.againstVotes += uint32(weight);
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::285 => proposal.forVotes += uint32(weight);
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::290 => proposal.abstainVotes += uint32(weight);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::181 => uint8 v,
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::21 => uint16 proposalThresholdBps;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::22 => uint16 quorumThresholdBps;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::24 => uint48 votingDelay;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::25 => uint48 votingPeriod;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::44 => uint32 timeCreated;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::45 => uint32 againstVotes;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::46 => uint32 forVotes;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::47 => uint32 abstainVotes;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::48 => uint32 voteStart;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::49 => uint32 voteEnd;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::50 => uint32 proposalThreshold;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::51 => uint32 quorumVotes;
2022-09-nouns-builder-main/src/governance/treasury/types/TreasuryTypesV1.sol::12 => uint128 gracePeriod;
2022-09-nouns-builder-main/src/governance/treasury/types/TreasuryTypesV1.sol::13 => uint128 delay;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::36 => uint64 timestamp;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::37 => uint192 votes;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::72 => uint8 v,
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::148 => uint8 _v,
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::252 => checkpoint.votes = uint192(_newTotalVotes);
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::253 => checkpoint.timestamp = uint64(block.timestamp);
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::17 => uint8 internal _initialized;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::55 => modifier reinitializer(uint8 _version) {
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::77 => if (_initialized < type(uint8).max) {
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::78 => _initialized = type(uint8).max;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::80 => emit Initialized(type(uint8).max);
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::10 => if (x > type(uint128).max) revert UNSAFE_CAST();
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::12 => return uint128(x);
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::16 => if (x > type(uint64).max) revert UNSAFE_CAST();
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::18 => return uint64(x);
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::22 => if (x > type(uint48).max) revert UNSAFE_CAST();
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::24 => return uint48(x);
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::28 => if (x > type(uint40).max) revert UNSAFE_CAST();
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::30 => return uint40(x);
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::34 => if (x > type(uint32).max) revert UNSAFE_CAST();
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::36 => return uint32(x);
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::40 => if (x > type(uint16).max) revert UNSAFE_CAST();
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::42 => return uint16(x);
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::46 => if (x > type(uint8).max) revert UNSAFE_CAST();
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::48 => return uint8(x);
2022-09-nouns-builder-main/src/token/Token.sol::88 => if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
2022-09-nouns-builder-main/src/token/Token.sol::98 => newFounder.vestExpiry = uint32(_founders[i].vestExpiry);
2022-09-nouns-builder-main/src/token/Token.sol::99 => newFounder.ownershipPct = uint8(founderPct);
2022-09-nouns-builder-main/src/token/Token.sol::123 => settings.totalOwnership = uint8(totalOwnership);
2022-09-nouns-builder-main/src/token/Token.sol::124 => settings.numFounders = uint8(numFounders);
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::158 => newItem.referenceSlot = uint16(dataLength);
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::179 => uint16[16] storage tokenAttributes = attributes[_tokenId];
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::185 => tokenAttributes[0] = uint16(numProperties);
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::194 => tokenAttributes[i + 1] = uint16(seed % numItems);
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::216 => uint16[16] memory tokenAttributes = attributes[_tokenId];
2022-09-nouns-builder-main/src/token/types/TokenTypesV1.sol::18 => uint96 totalSupply;
2022-09-nouns-builder-main/src/token/types/TokenTypesV1.sol::20 => uint8 numFounders;
2022-09-nouns-builder-main/src/token/types/TokenTypesV1.sol::21 => uint8 totalOwnership;
2022-09-nouns-builder-main/src/token/types/TokenTypesV1.sol::30 => uint8 ownershipPct;
2022-09-nouns-builder-main/src/token/types/TokenTypesV1.sol::31 => uint32 vestExpiry;
```