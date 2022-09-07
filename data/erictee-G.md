### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
src/token/metadata/MetadataRenderer.sol:L251        return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));

src/manager/Manager.sol:L68        metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));

src/manager/Manager.sol:L69        auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));

src/manager/Manager.sol:L70        treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));

src/manager/Manager.sol:L71        governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));

src/lib/token/ERC721Votes.sol:L162                abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))

src/lib/utils/EIP712.sol:L69        return keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));

src/governance/treasury/Treasury.sol:L107        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));

src/governance/governor/Governor.sol:L104        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));

src/governance/governor/Governor.sol:L230                    keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
```

### [G-02] Using bools for storage incurs overhead.


#### Impact
 
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use ```uint256(1)``` and ```uint256(2)``` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


#### Findings:
```
src/manager/storage/ManagerStorageV1.sol:L10    mapping(address => mapping(address => bool)) internal isUpgrade;

src/lib/token/ERC721.sol:L38    mapping(address => mapping(address => bool)) internal operatorApprovals;

src/governance/governor/storage/GovernorStorageV1.sol:L19    mapping(bytes32 => mapping(address => bool)) internal hasVoted;

```

### [G-03] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
src/governance/treasury/Treasury.sol:L269    receive() external payable {}
```

### [G-04] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
src/token/metadata/MetadataRenderer.sol:L347    function updateContractImage(string memory _newContractImage) external onlyOwner {

src/token/metadata/MetadataRenderer.sol:L355    function updateRendererBase(string memory _newRendererBase) external onlyOwner {

src/token/metadata/MetadataRenderer.sol:L363    function updateDescription(string memory _newDescription) external onlyOwner {

src/token/metadata/MetadataRenderer.sol:L376    function _authorizeUpgrade(address _impl) internal view override onlyOwner {

src/manager/Manager.sol:L187    function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

src/manager/Manager.sol:L196    function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

src/manager/Manager.sol:L209    function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}

src/lib/token/ERC721.sol:L47    function __ERC721_init(string memory _name, string memory _symbol) internal onlyInitializing {

src/lib/utils/EIP712.sol:L48    function __EIP712_init(string memory _name, string memory _version) internal onlyInitializing {

src/lib/utils/Ownable.sol:L45    function __Ownable_init(address _initialOwner) internal onlyInitializing {

src/lib/utils/Ownable.sol:L63    function transferOwnership(address _newOwner) public onlyOwner {

src/lib/utils/Ownable.sol:L71    function safeTransferOwnership(address _newOwner) public onlyOwner {

src/lib/utils/Ownable.sol:L78    function acceptOwnership() public onlyPendingOwner {

src/lib/utils/Ownable.sol:L87    function cancelOwnershipTransfer() public onlyOwner {

src/lib/utils/Pausable.sol:L39    function __Pausable_init(bool _initPause) internal onlyInitializing {

src/lib/utils/ReentrancyGuard.sol:L34    function __ReentrancyGuard_init() internal onlyInitializing {

src/lib/proxy/UUPS.sol:L47    function upgradeTo(address _newImpl) external onlyProxy {

src/governance/treasury/Treasury.sol:L116    function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {

src/governance/treasury/Treasury.sol:L180    function cancel(bytes32 _proposalId) external onlyOwner {

src/governance/governor/Governor.sol:L564    function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {

src/governance/governor/Governor.sol:L572    function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {

src/governance/governor/Governor.sol:L580    function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {

src/governance/governor/Governor.sol:L588    function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {

src/governance/governor/Governor.sol:L596    function updateVetoer(address _newVetoer) external onlyOwner {

src/governance/governor/Governor.sol:L605    function burnVetoer() external onlyOwner {

src/governance/governor/Governor.sol:L618    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {

src/auction/Auction.sol:L244    function unpause() external onlyOwner {

src/auction/Auction.sol:L263    function pause() external onlyOwner {

src/auction/Auction.sol:L307    function setDuration(uint256 _duration) external onlyOwner {

src/auction/Auction.sol:L315    function setReservePrice(uint256 _reservePrice) external onlyOwner {

src/auction/Auction.sol:L323    function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {

src/auction/Auction.sol:L331    function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {

src/auction/Auction.sol:L374    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {

```
### [G-05] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
src/token/Token.sol:L88                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();

src/token/Token.sol:L118                    (baseTokenId += schedule) % 100;

src/token/metadata/MetadataRenderer.sol:L140                    _propertyId += numStoredProperties;

src/governance/governor/Governor.sol:L280                proposal.againstVotes += uint32(weight);

src/governance/governor/Governor.sol:L285                proposal.forVotes += uint32(weight);

src/governance/governor/Governor.sol:L290                proposal.abstainVotes += uint32(weight);

```
### [G-06] ++i costs less gas than i++, especially when it's used in for loops.


#### Impact
Saves 6 gas per instance.


#### Findings:
```
src/token/Token.sol:L91                uint256 founderId = settings.numFounders++;

src/token/Token.sol:L154                tokenId = settings.totalSupply++;

```
### [G-07] Using private rather than public for constants to saves gas.


#### Impact
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.


#### Findings:
```
src/governance/governor/Governor.sol:L27    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");

```

### [G-08] Explicit initialization with zero not required


#### Impact
Explicit initialization with zero is not required for variable declaration because uints are 0 by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
src/token/metadata/MetadataRenderer.sol:L119            for (uint256 i = 0; i < numNewProperties; ++i) {

src/token/metadata/MetadataRenderer.sol:L133            for (uint256 i = 0; i < numNewItems; ++i) {

src/token/metadata/MetadataRenderer.sol:L189            for (uint256 i = 0; i < numProperties; ++i) {

src/token/metadata/MetadataRenderer.sol:L229            for (uint256 i = 0; i < numProperties; ++i) {

src/governance/treasury/Treasury.sol:L162            for (uint256 i = 0; i < numTargets; ++i) {

```

### [G-09] Public functions not called by the contract should be declared external instead


#### Impact
public functions that are never called by the contract should be declared external to save gas.


#### Findings:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L45
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L59

