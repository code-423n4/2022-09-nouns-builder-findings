
## make onlyowner functoins payable to save gas 
```
./src/auction/Auction.sol:244:    function unpause() external onlyOwner {
./src/auction/Auction.sol:263:    function pause() external onlyOwner {
./src/auction/Auction.sol:307:    function setDuration(uint256 _duration) external onlyOwner {
./src/auction/Auction.sol:315:    function setReservePrice(uint256 _reservePrice) external onlyOwner {
./src/auction/Auction.sol:323:    function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {
./src/auction/Auction.sol:331:    function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {
./src/auction/Auction.sol:374:    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
./src/governance/governor/Governor.sol:564:    function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {
./src/governance/governor/Governor.sol:572:    function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {
./src/governance/governor/Governor.sol:580:    function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {
./src/governance/governor/Governor.sol:588:    function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {
./src/governance/governor/Governor.sol:596:    function updateVetoer(address _newVetoer) external onlyOwner {
./src/governance/governor/Governor.sol:605:    function burnVetoer() external onlyOwner {
./src/governance/governor/Governor.sol:618:    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
./src/governance/treasury/Treasury.sol:116:    function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {
./src/governance/treasury/Treasury.sol:146:    ) external payable onlyOwner {
./src/governance/treasury/Treasury.sol:180:    function cancel(bytes32 _proposalId) external onlyOwner {
./src/lib/utils/Ownable.sol:28:    modifier onlyOwner() {
./src/lib/utils/Ownable.sol:63:    function transferOwnership(address _newOwner) public onlyOwner {
./src/lib/utils/Ownable.sol:71:    function safeTransferOwnership(address _newOwner) public onlyOwner {
./src/lib/utils/Ownable.sol:87:    function cancelOwnershipTransfer() public onlyOwner {
./src/manager/Manager.sol:187:    function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
./src/manager/Manager.sol:196:    function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
./src/manager/Manager.sol:209:    function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
./src/token/metadata/MetadataRenderer.sol:95:    ) external onlyOwner {
./src/token/metadata/MetadataRenderer.sol:347:    function updateContractImage(string memory _newContractImage) external onlyOwner {
./src/token/metadata/MetadataRenderer.sol:355:    function updateRendererBase(string memory _newRendererBase) external onlyOwner {
./src/token/metadata/MetadataRenderer.sol:363:    function updateDescription(string memory _newDescription) external onlyOwner {
./src/token/metadata/MetadataRenderer.sol:376:    function _authorizeUpgrade(address _impl) internal view override onlyOwner {

```
## Using bools for storage incurs overhead
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```
./src/auction/Auction.sol:136:        bool extend;
./src/auction/Auction.sol:349:        bool success;
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/storage/ManagerStorageV1.sol#L10
##  make array.length into a cahced variable to sav gas
```
./src/governance/governor/Governor.sol:132:        uint256 numTargets = _targets.length;
./src/governance/governor/Governor.sol:138:        if (numTargets != _values.length) revert PROPOSAL_LENGTH_MISMATCH();
./src/governance/governor/Governor.sol:139:        if (numTargets != _calldatas.length) revert PROPOSAL_LENGTH_MISMATCH();
./src/governance/treasury/Treasury.sol:157:        uint256 numTargets = _targets.length;
./src/lib/proxy/ERC1967Upgrade.sol:61:        if (_data.length > 0 || _forceCall) {
./src/lib/utils/Address.sol:50:            if (_returndata.length > 0) {
./src/token/metadata/MetadataRenderer.sol:78:        return properties.length;
./src/token/metadata/MetadataRenderer.sol:84:        return properties[_propertyId].items.length;
./src/token/metadata/MetadataRenderer.sol:97:        uint256 dataLength = ipfsData.length;
./src/token/metadata/MetadataRenderer.sol:109:        uint256 numStoredProperties = properties.length;
./src/token/metadata/MetadataRenderer.sol:112:        uint256 numNewProperties = _names.length;
./src/token/metadata/MetadataRenderer.sol:115:        uint256 numNewItems = _items.length;
./src/token/metadata/MetadataRenderer.sol:150:                // Cannot underflow as the items array length is ensured to be at least 1
./src/token/metadata/MetadataRenderer.sol:151:                uint256 newItemIndex = items.length - 1;
./src/token/metadata/MetadataRenderer.sol:182:        uint256 numProperties = properties.length;
./src/token/metadata/MetadataRenderer.sol:191:                uint256 numItems = properties[i].items.length;
./src/token/Token.sol:73:        uint256 numFounders = _founders.length;
```
## Unchecking arithmetics operations that can’t underflow/overflow
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: Checked or Unchecked Arithmetic. I suggest wrapping with an unchecked block:
Same thing with second unchecked because total can't overflow amount cant overflow
```
./src/governance/governor/Governor.sol:132:        uint256 numTargets = _targets.length;
./src/governance/governor/Governor.sol:138:        if (numTargets != _values.length) revert PROPOSAL_LENGTH_MISMATCH();
./src/governance/governor/Governor.sol:139:        if (numTargets != _calldatas.length) revert PROPOSAL_LENGTH_MISMATCH();
./src/governance/treasury/Treasury.sol:157:        uint256 numTargets = _targets.length;
./src/lib/proxy/ERC1967Upgrade.sol:61:        if (_data.length > 0 || _forceCall) {
./src/lib/utils/Address.sol:50:            if (_returndata.length > 0) {
./src/token/metadata/MetadataRenderer.sol:78:        return properties.length;
./src/token/metadata/MetadataRenderer.sol:84:        return properties[_propertyId].items.length;
./src/token/metadata/MetadataRenderer.sol:97:        uint256 dataLength = ipfsData.length;
./src/token/metadata/MetadataRenderer.sol:109:        uint256 numStoredProperties = properties.length;
./src/token/metadata/MetadataRenderer.sol:112:        uint256 numNewProperties = _names.length;
./src/token/metadata/MetadataRenderer.sol:115:        uint256 numNewItems = _items.length;
./src/token/metadata/MetadataRenderer.sol:150:                // Cannot underflow as the items array length is ensured to be at least 1
./src/token/metadata/MetadataRenderer.sol:151:                uint256 newItemIndex = items.length - 1;
./src/token/metadata/MetadataRenderer.sol:182:        uint256 numProperties = properties.length;
./src/token/metadata/MetadataRenderer.sol:191:                uint256 numItems = properties[i].items.length;
./src/token/Token.sol:73:        uint256 numFounders = _founders.length;
```
## make address(0) into long form to save gas ex:0
```
./src/auction/Auction.sol:104:        if (highestBidder == address(0)) {
./src/auction/Auction.sol:184:        if (_auction.highestBidder != address(0)) {
./src/auction/Auction.sol:228:            auction.highestBidder = address(0);
./src/governance/governor/IGovernor.sol:264:    /// @notice The address eligible to veto any proposal (address(0) if burned)
./src/governance/governor/Governor.sol:70:        if (_treasury == address(0)) revert ADDRESS_ZERO();
./src/governance/governor/Governor.sol:71:        if (_token == address(0)) revert ADDRESS_ZERO();
./src/governance/governor/Governor.sol:239:        if (recoveredAddress == address(0) || recoveredAddress != _voter) revert INVALID_SIGNATURE();
./src/governance/governor/Governor.sol:543:    /// @notice The address eligible to veto any proposal (address(0) if burned)
./src/governance/governor/Governor.sol:597:        if (_newVetoer == address(0)) revert ADDRESS_ZERO();
./src/governance/governor/Governor.sol:606:        emit VetoerUpdated(settings.vetoer, address(0));
./src/governance/treasury/Treasury.sol:48:        if (_governor == address(0)) revert ADDRESS_ZERO();
./src/lib/token/ERC721Votes.sol:128:        return current == address(0) ? _account : current;
./src/lib/token/ERC721Votes.sol:170:        if (recoveredAddress == address(0) || recoveredAddress != _from) revert INVALID_SIGNATURE();
./src/lib/token/ERC721Votes.sol:205:                if (_from != address(0)) {
./src/lib/token/ERC721Votes.sol:220:                if (_to != address(0)) {
./src/lib/token/ERC721.sol:84:        if (_owner == address(0)) revert ADDRESS_ZERO();
./src/lib/token/ERC721.sol:94:        if (owner == address(0)) revert INVALID_OWNER();
./src/lib/token/ERC721.sol:132:        if (_to == address(0)) revert ADDRESS_ZERO();
./src/lib/token/ERC721.sol:192:        if (_to == address(0)) revert ADDRESS_ZERO();
./src/lib/token/ERC721.sol:194:        if (owners[_tokenId] != address(0)) revert ALREADY_MINTED();
./src/lib/token/ERC721.sol:196:        _beforeTokenTransfer(address(0), _to, _tokenId);
./src/lib/token/ERC721.sol:204:        emit Transfer(address(0), _to, _tokenId);
./src/lib/token/ERC721.sol:206:        _afterTokenTransfer(address(0), _to, _tokenId);
./src/lib/token/ERC721.sol:214:        if (owner == address(0)) revert NOT_MINTED();
./src/lib/token/ERC721.sol:216:        _beforeTokenTransfer(owner, address(0), _tokenId);
./src/lib/token/ERC721.sol:226:        emit Transfer(owner, address(0), _tokenId);
./src/lib/token/ERC721.sol:228:        _afterTokenTransfer(owner, address(0), _tokenId);
./src/lib/interfaces/IERC721.sol:40:    /// @dev Reverts if a transfer is attempted to address(0)
./src/lib/interfaces/IInitializable.sol:19:    /// @dev Reverts if incorrectly initialized with address(0)
./src/lib/utils/Initializable.sol:36:        if ((!isTopLevelCall || _initialized != 0) && (Address.isContract(address(this)) || _initialized != 1)) revert ALREADY_INITIALIZED();
./src/lib/utils/Ownable.sol:48:        emit OwnerUpdated(address(0), _initialOwner);
./src/manager/Manager.sol:82:        if (_owner == address(0)) revert ADDRESS_ZERO();
./src/manager/Manager.sol:117:        if ((founder = _founderParams[0].wallet) == address(0)) revert FOUNDER_REQUIRED();
./src/manager/Manager.sol:167:        metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
./src/manager/Manager.sol:168:        auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
./src/manager/Manager.sol:169:        treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
./src/manager/Manager.sol:170:        governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
./src/token/metadata/MetadataRenderer.sol:210:            Strings.toHexString(uint256(uint160(address(this))), 20),
./src/token/Token.sol:132:            while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;
./src/token/Token.sol:182:        if (tokenRecipient[baseTokenId].wallet == address(0)) {
```
## USE ASSEMBLY TO CHECK FOR ADDRESS(0)
ex: iszero
```
./src/lib/utils/Ownable.sol:48:        emit OwnerUpdated(address(0), _initialOwner);
./src/manager/Manager.sol:82:        if (_owner == address(0)) revert ADDRESS_ZERO();
./src/manager/Manager.sol:117:        if ((founder = _founderParams[0].wallet) == address(0)) revert FOUNDER_REQUIRED();
./src/manager/Manager.sol:167:        metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
./src/manager/Manager.sol:168:        auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
./src/manager/Manager.sol:169:        treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
./src/manager/Manager.sol:170:        governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
./src/token/metadata/MetadataRenderer.sol:210:            Strings.toHexString(uint256(uint160(address(this))), 20),
./src/token/Token.sol:132:            while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;
./src/token/Token.sol:182:        if (tokenRecipient[baseTokenId].wallet == address(0)) {
```
## conversion not needed 
timestamp is already a uint32 and is a waste.
```
        proposal.timeCreated = uint32(block.timestamp);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L168
