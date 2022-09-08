## gas optimization
### G01: COMPARISONS WITH ZERO FOR UNSIGNED INTEGERS
#### problem
0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes https://twitter.com/gzeon/status/1485428085885640706
#### prof
lib/proxy/ERC1967Upgrade.sol, 61, b'        if (_data.length > 0 || _forceCall) {'
lib/token/ERC721Votes.sol, 203, b'            if (_from != _to && _amount > 0) {'
lib/utils/Address.sol, 50, b'            if (_returndata.length > 0) {'

### G02: PREFIX INCREMENT SAVE MORE GAS
#### problem
prefix increment ++i is more cheaper than postfix i++
#### prof
lib/token/ERC721Votes.sol, 207, b'                    uint256 nCheckpoints = numCheckpoints[_from]++;'
lib/token/ERC721Votes.sol, 222, b'                    uint256 nCheckpoints = numCheckpoints[_to]++;'
token/Token.sol, 91, b'                uint256 founderId = settings.numFounders++;'

### G03: USING BOOLS FOR STORAGE INCURS OVERHEAD
#### problem
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
#### prof
governance/governor/storage/GovernorStorageV1.sol, 19, b'    mapping(bytes32 => mapping(address => bool)) internal hasVoted;'
lib/token/ERC721.sol, 38, b'    mapping(address => mapping(address => bool)) internal operatorApprovals;'
manager/storage/ManagerStorageV1.sol, 10, b'    mapping(address => mapping(address => bool)) internal isUpgrade;'


### G04: resign the default value to the variables.
#### problem
 resign the default value to the variables will cost more gas.
#### prof
governance/treasury/Treasury.sol, 162, b'            for (uint256 i = 0; i < numTargets; ++i) {'

### G05: ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
#### problem
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
#### prof
governance/treasury/Treasury.sol, 162, b'            for (uint256 i = 0; i < numTargets; ++i) {'
lib/token/ERC721.sol, 139, b'            --balances[_from];'
lib/token/ERC721.sol, 141, b'            ++balances[_to];'
lib/token/ERC721.sol, 199, b'            ++balances[_to];'
lib/token/ERC721.sol, 219, b'            --balances[owner];'
lib/token/ERC721Votes.sol, 207, b'                    uint256 nCheckpoints = numCheckpoints[_from]++;'
lib/token/ERC721Votes.sol, 222, b'                    uint256 nCheckpoints = numCheckpoints[_to]++;'
token/Token.sol, 91, b'                uint256 founderId = settings.numFounders++;'
token/Token.sol, 108, b'                for (uint256 j; j < founderPct; ++j) {'
token/Token.sol, 80, b'            for (uint256 i; i < numFounders; ++i) {'
token/Token.sol, 132, b'            while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;'
token/Token.sol, 261, b'            for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];'


### G06：ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
#### prof
governance/governor/Governor.sol, 104, b'        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));'
governance/treasury/Treasury.sol, 107, b'        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));'
lib/utils/EIP712.sol, 69, b'        return keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));'

### G07:FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
#### problem
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
#### prof
auction/Auction.sol, 82, b'    function initialize(\n        address _token,\n        address _founder,\n        address _treasury,\n        uint256 _duration,\n        uint256 _reservePrice\n    ) external initializer '
auction/Auction.sol, 260, b'    function unpause() external onlyOwner '
auction/Auction.sol, 260, b'    function unpause() external onlyOwner '
auction/Auction.sol, 265, b'    function pause() external onlyOwner '
auction/Auction.sol, 265, b'    function pause() external onlyOwner '
auction/Auction.sol, 311, b'    function setDuration(uint256 _duration) external onlyOwner '
auction/Auction.sol, 311, b'    function setDuration(uint256 _duration) external onlyOwner '
auction/Auction.sol, 319, b'    function setReservePrice(uint256 _reservePrice) external onlyOwner '
auction/Auction.sol, 319, b'    function setReservePrice(uint256 _reservePrice) external onlyOwner '
auction/Auction.sol, 327, b'    function setTimeBuffer(uint256 _timeBuffer) external onlyOwner '
auction/Auction.sol, 327, b'    function setTimeBuffer(uint256 _timeBuffer) external onlyOwner '
auction/Auction.sol, 335, b'    function setMinimumBidIncrement(uint256 _percentage) external onlyOwner '
auction/Auction.sol, 335, b'    function setMinimumBidIncrement(uint256 _percentage) external onlyOwner '
auction/Auction.sol, 377, b'    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner '
auction/Auction.sol, 377, b'    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner '
governance/governor/Governor.sol, 87, b'    function initialize(\n        address _treasury,\n        address _token,\n        address _vetoer,\n        uint256 _votingDelay,\n        uint256 _votingPeriod,\n        uint256 _proposalThresholdBps,\n        uint256 _quorumThresholdBps\n    ) external initializer '
governance/governor/Governor.sol, 568, b'    function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner '
governance/governor/Governor.sol, 568, b'    function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner '
governance/governor/Governor.sol, 576, b'    function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner '
governance/governor/Governor.sol, 576, b'    function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner '
governance/governor/Governor.sol, 584, b'    function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner '
governance/governor/Governor.sol, 584, b'    function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner '
governance/governor/Governor.sol, 592, b'    function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner '
governance/governor/Governor.sol, 592, b'    function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner '
governance/governor/Governor.sol, 602, b'    function updateVetoer(address _newVetoer) external onlyOwner '
governance/governor/Governor.sol, 602, b'    function updateVetoer(address _newVetoer) external onlyOwner '
governance/governor/Governor.sol, 609, b'    function burnVetoer() external onlyOwner '
governance/governor/Governor.sol, 609, b'    function burnVetoer() external onlyOwner '
governance/governor/Governor.sol, 621, b'    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner '
governance/governor/Governor.sol, 621, b'    function _authorizeUpgrade(address _newImpl) internal view override onlyOwner '
governance/treasury/Treasury.sol, 60, b'    function initialize(address _governor, uint256 _delay) external initializer '
governance/treasury/Treasury.sol, 130, b'    function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) '
governance/treasury/Treasury.sol, 130, b'    function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) '
governance/treasury/Treasury.sol, 172, b'    function execute(\n        address[] calldata _targets,\n        uint256[] calldata _values,\n        bytes[] calldata _calldatas,\n        bytes32 _descriptionHash\n    ) external payable onlyOwner '
governance/treasury/Treasury.sol, 188, b'    function cancel(bytes32 _proposalId) external onlyOwner '
governance/treasury/Treasury.sol, 188, b'    function cancel(bytes32 _proposalId) external onlyOwner '
lib/token/ERC721.sol, 79, b'    function isApprovedForAll(address _owner, address _operator) external view returns (bool) '
lib/token/ERC721.sol, 79, b'    function isApprovedForAll(address _owner, address _operator) external view returns (bool) '
lib/token/ERC721.sol, 87, b'    function balanceOf(address _owner) public view returns (uint256) '
lib/token/ERC721.sol, 87, b'    function balanceOf(address _owner) public view returns (uint256) '
lib/token/ERC721.sol, 97, b'    function ownerOf(uint256 _tokenId) public view returns (address) '
lib/token/ERC721.sol, 97, b'    function ownerOf(uint256 _tokenId) public view returns (address) '
lib/token/ERC721.sol, 110, b'    function approve(address _to, uint256 _tokenId) external '
lib/token/ERC721.sol, 151, b'    function transferFrom(\n        address _from,\n        address _to,\n        uint256 _tokenId\n    ) public '
lib/token/ERC721.sol, 207, b'    function _mint(address _to, uint256 _tokenId) internal virtual '
lib/token/ERC721.sol, 229, b'    function _burn(uint256 _tokenId) internal virtual '
lib/utils/Ownable.sol, 49, b'    function __Ownable_init(address _initialOwner) internal onlyInitializing '
lib/utils/Ownable.sol, 49, b'    function __Ownable_init(address _initialOwner) internal onlyInitializing '
lib/utils/Ownable.sol, 54, b'    function owner() public view returns (address) '
lib/utils/Ownable.sol, 54, b'    function owner() public view returns (address) '
lib/utils/Ownable.sol, 59, b'    function pendingOwner() public view returns (address) '
lib/utils/Ownable.sol, 59, b'    function pendingOwner() public view returns (address) '
lib/utils/Ownable.sol, 67, b'    function transferOwnership(address _newOwner) public onlyOwner '
lib/utils/Ownable.sol, 67, b'    function transferOwnership(address _newOwner) public onlyOwner '
lib/utils/Ownable.sol, 75, b'    function safeTransferOwnership(address _newOwner) public onlyOwner '
lib/utils/Ownable.sol, 75, b'    function safeTransferOwnership(address _newOwner) public onlyOwner '
lib/utils/Ownable.sol, 84, b'    function acceptOwnership() public onlyPendingOwner '
lib/utils/Ownable.sol, 84, b'    function acceptOwnership() public onlyPendingOwner '
lib/utils/Ownable.sol, 91, b'    function cancelOwnershipTransfer() public onlyOwner '
lib/utils/Ownable.sol, 91, b'    function cancelOwnershipTransfer() public onlyOwner '
manager/Manager.sol, 86, b'    function initialize(address _owner) external initializer '
manager/Manager.sol, 86, b'    function initialize(address _owner) external initializer '
manager/Manager.sol, 191, b'    function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner '
manager/Manager.sol, 191, b'    function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner '
manager/Manager.sol, 200, b'    function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner '
manager/Manager.sol, 200, b'    function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner '
manager/Manager.sol, 209, b'    function _authorizeUpgrade(address _newImpl) internal override onlyOwner '
manager/Manager.sol, 209, b'    function _authorizeUpgrade(address _newImpl) internal override onlyOwner '
token/Token.sol, 126, b'    function _addFounders(IManager.FounderParams[] calldata _founders) internal '
token/Token.sol, 242, b'    function totalFounderOwnership() external view returns (uint256) '
token/Token.sol, 242, b'    function totalFounderOwnership() external view returns (uint256) '
token/Token.sol, 296, b'    function owner() public view returns (address) '
token/Token.sol, 296, b'    function owner() public view returns (address) '
token/Token.sol, 311, b'    function _authorizeUpgrade(address _newImpl) internal view override '

### G08: USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
#### problem
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table
#### prof
governance/governor/Governor.sol, 27, b'    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");'
manager/Manager.sol, 25, b'    address public immutable tokenImpl;'
manager/Manager.sol, 28, b'    address public immutable metadataImpl;'
manager/Manager.sol, 31, b'    address public immutable auctionImpl;'
manager/Manager.sol, 34, b'    address public immutable treasuryImpl;'
manager/Manager.sol, 37, b'    address public immutable governorImpl;'

### G09: USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
#### problem
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
#### prof
auction/Auction.sol, 77, b'        settings.duration = SafeCast.toUint40(_duration);'
auction/Auction.sol, 80, b'        settings.timeBuffer = 5 minutes;'
auction/Auction.sol, 81, b'        settings.minBidIncrement = 10;'
auction/Auction.sol, 98, b'        if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();'
auction/Auction.sol, 104, b'        if (highestBidder == address(0)) {'
auction/Auction.sol, 149, b'                auction.endTime = uint40(block.timestamp + settings.timeBuffer);'
auction/Auction.sol, 175, b'        if (_auction.startTime == 0) revert AUCTION_NOT_STARTED();'
auction/Auction.sol, 175, b'        if (_auction.startTime == 0) revert AUCTION_NOT_STARTED();'
auction/Auction.sol, 178, b'        if (block.timestamp < _auction.endTime) revert AUCTION_ACTIVE();'
auction/Auction.sol, 184, b'        if (_auction.highestBidder != address(0)) {'
auction/Auction.sol, 189, b'            if (highestBid != 0) _handleOutgoingTransfer(settings.treasury, highestBid);'
auction/Auction.sol, 248, b'        if (auction.tokenId == 0) {'
auction/Auction.sol, 283, b'        return settings.duration;'
auction/Auction.sol, 293, b'        return settings.timeBuffer;'
auction/Auction.sol, 298, b'        return settings.minBidIncrement;'
auction/Auction.sol, 308, b'        settings.duration = SafeCast.toUint40(_duration);'
auction/Auction.sol, 324, b'        settings.timeBuffer = SafeCast.toUint40(_timeBuffer);'
auction/Auction.sol, 332, b'        settings.minBidIncrement = SafeCast.toUint8(_percentage);'
governance/governor/Governor.sol, 70, b'        if (_treasury == address(0)) revert ADDRESS_ZERO();'
governance/governor/Governor.sol, 71, b'        if (_token == address(0)) revert ADDRESS_ZERO();'
governance/governor/Governor.sol, 77, b'        settings.votingDelay = SafeCast.toUint48(_votingDelay);'
governance/governor/Governor.sol, 78, b'        settings.votingPeriod = SafeCast.toUint48(_votingPeriod);'
governance/governor/Governor.sol, 79, b'        settings.proposalThresholdBps = SafeCast.toUint16(_proposalThresholdBps);'
governance/governor/Governor.sol, 80, b'        settings.quorumThresholdBps = SafeCast.toUint16(_quorumThresholdBps);'
governance/governor/Governor.sol, 83, b'        __EIP712_init(string.concat(settings.token.symbol(), " GOV"), "1");'
governance/governor/Governor.sol, 128, b'            if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();'
governance/governor/Governor.sol, 135, b'        if (numTargets == 0) revert PROPOSAL_TARGET_MISSING();'
governance/governor/Governor.sol, 142, b'        bytes32 descriptionHash = keccak256(bytes(_description));'
governance/governor/Governor.sol, 151, b'        if (proposal.voteStart != 0) revert PROPOSAL_EXISTS(proposalId);'
governance/governor/Governor.sol, 151, b'        if (proposal.voteStart != 0) revert PROPOSAL_EXISTS(proposalId);'
governance/governor/Governor.sol, 151, b'        if (proposal.voteStart != 0) revert PROPOSAL_EXISTS(proposalId);'
governance/governor/Governor.sol, 165, b'        proposal.voteStart = uint32(snapshot);'
governance/governor/Governor.sol, 166, b'        proposal.voteEnd = uint32(deadline);'
governance/governor/Governor.sol, 167, b'        proposal.proposalThreshold = uint32(currentProposalThreshold);'
governance/governor/Governor.sol, 168, b'        proposal.quorumVotes = uint32(quorum());'
governance/governor/Governor.sol, 170, b'        proposal.timeCreated = uint32(block.timestamp);'
governance/governor/Governor.sol, 236, b'        address recoveredAddress = ecrecover(digest, _v, _r, _s);'
governance/governor/Governor.sol, 236, b'        address recoveredAddress = ecrecover(digest, _v, _r, _s);'
governance/governor/Governor.sol, 239, b'        if (recoveredAddress == address(0) || recoveredAddress != _voter) revert INVALID_SIGNATURE();'
governance/governor/Governor.sol, 261, b'        if (_support > 2) revert INVALID_VOTE();'
governance/governor/Governor.sol, 278, b'            if (_support == 0) {'
governance/governor/Governor.sol, 283, b'            } else if (_support == 1) {'
governance/governor/Governor.sol, 288, b'            } else if (_support == 2) {'
governance/governor/Governor.sol, 290, b'                proposal.abstainVotes += uint32(weight);'
governance/governor/Governor.sol, 285, b'                proposal.forVotes += uint32(weight);'
governance/governor/Governor.sol, 280, b'                proposal.againstVotes += uint32(weight);'
governance/governor/Governor.sol, 363, b'            if (msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)'
governance/governor/Governor.sol, 363, b'            if (msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)'
governance/governor/Governor.sol, 418, b'        if (proposal.voteStart == 0) revert PROPOSAL_DOES_NOT_EXIST();'
governance/governor/Governor.sol, 418, b'        if (proposal.voteStart == 0) revert PROPOSAL_DOES_NOT_EXIST();'
governance/governor/Governor.sol, 433, b'        } else if (block.timestamp < proposal.voteStart) {'
governance/governor/Governor.sol, 437, b'        } else if (block.timestamp < proposal.voteEnd) {'
governance/governor/Governor.sol, 441, b'        } else if (proposal.forVotes < proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {'
governance/governor/Governor.sol, 441, b'        } else if (proposal.forVotes < proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {'
governance/governor/Governor.sol, 441, b'        } else if (proposal.forVotes < proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {'
governance/governor/Governor.sol, 441, b'        } else if (proposal.forVotes < proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {'
governance/governor/Governor.sol, 445, b'        } else if (settings.treasury.timestamp(_proposalId) == 0) {'
governance/governor/Governor.sol, 468, b'            return (settings.token.totalSupply() * settings.proposalThresholdBps) / 10_000;'
governance/governor/Governor.sol, 475, b'            return (settings.token.totalSupply() * settings.quorumThresholdBps) / 10_000;'
governance/governor/Governor.sol, 488, b'        return proposals[_proposalId].voteStart;'
governance/governor/Governor.sol, 494, b'        return proposals[_proposalId].voteEnd;'
governance/governor/Governor.sol, 510, b'        return (proposal.againstVotes, proposal.forVotes, proposal.abstainVotes);'
governance/governor/Governor.sol, 525, b'        return settings.proposalThresholdBps;'
governance/governor/Governor.sol, 530, b'        return settings.quorumThresholdBps;'
governance/governor/Governor.sol, 535, b'        return settings.votingDelay;'
governance/governor/Governor.sol, 540, b'        return settings.votingPeriod;'
governance/governor/Governor.sol, 567, b'        settings.votingDelay = SafeCast.toUint48(_newVotingDelay);'
governance/governor/Governor.sol, 575, b'        settings.votingPeriod = SafeCast.toUint48(_newVotingPeriod);'
governance/governor/Governor.sol, 583, b'        settings.proposalThresholdBps = SafeCast.toUint16(_newProposalThresholdBps);'
governance/governor/Governor.sol, 591, b'        settings.quorumThresholdBps = SafeCast.toUint16(_newQuorumVotesBps);'
governance/governor/Governor.sol, 597, b'        if (_newVetoer == address(0)) revert ADDRESS_ZERO();'
governance/treasury/Treasury.sol, 48, b'        if (_governor == address(0)) revert ADDRESS_ZERO();'
governance/treasury/Treasury.sol, 54, b'        settings.delay = SafeCast.toUint128(_delay);'
governance/treasury/Treasury.sol, 57, b'        settings.gracePeriod = 2 weeks;'
governance/treasury/Treasury.sol, 83, b'        return timestamps[_proposalId] != 0;'
governance/treasury/Treasury.sol, 89, b'        return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];'
governance/treasury/Treasury.sol, 162, b'            for (uint256 i = 0; i < numTargets; ++i) {'
governance/treasury/Treasury.sol, 196, b'        return settings.delay;'
governance/treasury/Treasury.sol, 201, b'        return settings.gracePeriod;'
governance/treasury/Treasury.sol, 217, b'        settings.delay = SafeCast.toUint128(_newDelay);'
governance/treasury/Treasury.sol, 229, b'        settings.gracePeriod = SafeCast.toUint128(_newGracePeriod);'
lib/proxy/ERC1967Upgrade.sol, 38, b'        if (StorageSlot.getBooleanSlot(_ROLLBACK_SLOT).value) {'
lib/proxy/ERC1967Upgrade.sol, 38, b'        if (StorageSlot.getBooleanSlot(_ROLLBACK_SLOT).value) {'
lib/proxy/ERC1967Upgrade.sol, 61, b'        if (_data.length > 0 || _forceCall) {'
lib/proxy/ERC1967Upgrade.sol, 84, b'        return StorageSlot.getAddressSlot(_IMPLEMENTATION_SLOT).value;'
lib/proxy/ERC1967Upgrade.sol, 84, b'        return StorageSlot.getAddressSlot(_IMPLEMENTATION_SLOT).value;'
lib/token/ERC721.sol, 63, b'            _interfaceId == 0x01ffc9a7 || // ERC165 Interface ID'
lib/token/ERC721.sol, 64, b'            _interfaceId == 0x80ac58cd || // ERC721 Interface ID'
lib/token/ERC721.sol, 65, b'            _interfaceId == 0x5b5e139f; // ERC721Metadata Interface ID'
lib/token/ERC721.sol, 84, b'        if (_owner == address(0)) revert ADDRESS_ZERO();'
lib/token/ERC721.sol, 94, b'        if (owner == address(0)) revert INVALID_OWNER();'
lib/token/ERC721.sol, 132, b'        if (_to == address(0)) revert ADDRESS_ZERO();'
lib/token/ERC721.sol, 192, b'        if (_to == address(0)) revert ADDRESS_ZERO();'
lib/token/ERC721.sol, 194, b'        if (owners[_tokenId] != address(0)) revert ALREADY_MINTED();'
lib/token/ERC721.sol, 196, b'        _beforeTokenTransfer(address(0), _to, _tokenId);'
lib/token/ERC721.sol, 206, b'        _afterTokenTransfer(address(0), _to, _tokenId);'
lib/token/ERC721.sol, 214, b'        if (owner == address(0)) revert NOT_MINTED();'
lib/token/ERC721.sol, 216, b'        _beforeTokenTransfer(owner, address(0), _tokenId);'
lib/token/ERC721.sol, 228, b'        _afterTokenTransfer(owner, address(0), _tokenId);'
lib/token/ERC721Votes.sol, 52, b'            return nCheckpoints != 0 ? checkpoints[_account][nCheckpoints - 1].votes : 0;'
lib/token/ERC721Votes.sol, 52, b'            return nCheckpoints != 0 ? checkpoints[_account][nCheckpoints - 1].votes : 0;'
lib/token/ERC721Votes.sol, 67, b'        if (nCheckpoints == 0) return 0;'
lib/token/ERC721Votes.sol, 67, b'        if (nCheckpoints == 0) return 0;'
lib/token/ERC721Votes.sol, 75, b'            uint256 lastCheckpoint = nCheckpoints - 1;'
lib/token/ERC721Votes.sol, 78, b'            if (accountCheckpoints[lastCheckpoint].timestamp <= _timestamp) return accountCheckpoints[lastCheckpoint].votes;'
lib/token/ERC721Votes.sol, 78, b'            if (accountCheckpoints[lastCheckpoint].timestamp <= _timestamp) return accountCheckpoints[lastCheckpoint].votes;'
lib/token/ERC721Votes.sol, 78, b'            if (accountCheckpoints[lastCheckpoint].timestamp <= _timestamp) return accountCheckpoints[lastCheckpoint].votes;'
lib/token/ERC721Votes.sol, 78, b'            if (accountCheckpoints[lastCheckpoint].timestamp <= _timestamp) return accountCheckpoints[lastCheckpoint].votes;'
lib/token/ERC721Votes.sol, 81, b'            if (accountCheckpoints[0].timestamp > _timestamp) return 0;'
lib/token/ERC721Votes.sol, 81, b'            if (accountCheckpoints[0].timestamp > _timestamp) return 0;'
lib/token/ERC721Votes.sol, 81, b'            if (accountCheckpoints[0].timestamp > _timestamp) return 0;'
lib/token/ERC721Votes.sol, 90, b'            Checkpoint memory cp;'
lib/token/ERC721Votes.sol, 98, b'                cp = accountCheckpoints[middle];'
lib/token/ERC721Votes.sol, 101, b'                if (cp.timestamp == _timestamp) {'
lib/token/ERC721Votes.sol, 101, b'                if (cp.timestamp == _timestamp) {'
lib/token/ERC721Votes.sol, 106, b'                } else if (cp.timestamp < _timestamp) {'
lib/token/ERC721Votes.sol, 106, b'                } else if (cp.timestamp < _timestamp) {'
lib/token/ERC721Votes.sol, 103, b'                    return cp.votes;'
lib/token/ERC721Votes.sol, 103, b'                    return cp.votes;'
lib/token/ERC721Votes.sol, 116, b'            return accountCheckpoints[low].votes;'
lib/token/ERC721Votes.sol, 116, b'            return accountCheckpoints[low].votes;'
lib/token/ERC721Votes.sol, 128, b'        return current == address(0) ? _account : current;'
lib/token/ERC721Votes.sol, 167, b'        address recoveredAddress = ecrecover(digest, _v, _r, _s);'
lib/token/ERC721Votes.sol, 167, b'        address recoveredAddress = ecrecover(digest, _v, _r, _s);'
lib/token/ERC721Votes.sol, 170, b'        if (recoveredAddress == address(0) || recoveredAddress != _from) revert INVALID_SIGNATURE();'
lib/token/ERC721Votes.sol, 203, b'            if (_from != _to && _amount > 0) {'
lib/token/ERC721Votes.sol, 205, b'                if (_from != address(0)) {'
lib/token/ERC721Votes.sol, 213, b'                    if (nCheckpoints != 0) prevTotalVotes = checkpoints[_from][nCheckpoints - 1].votes;'
lib/token/ERC721Votes.sol, 220, b'                if (_to != address(0)) {'
lib/token/ERC721Votes.sol, 228, b'                    if (nCheckpoints != 0) prevTotalVotes = checkpoints[_to][nCheckpoints - 1].votes;'
lib/token/ERC721Votes.sol, 249, b'        Checkpoint storage checkpoint = checkpoints[_account][_id];'
lib/token/ERC721Votes.sol, 249, b'        Checkpoint storage checkpoint = checkpoints[_account][_id];'
lib/token/ERC721Votes.sol, 252, b'        checkpoint.votes = uint192(_newTotalVotes);'
lib/token/ERC721Votes.sol, 253, b'        checkpoint.timestamp = uint64(block.timestamp);'
lib/token/ERC721Votes.sol, 268, b'        _moveDelegateVotes(_from, _to, 1);'
lib/utils/Address.sol, 26, b'        return bytes32(uint256(uint160(_account)) << 96);'
lib/utils/Address.sol, 26, b'        return bytes32(uint256(uint160(_account)) << 96);'
lib/utils/Address.sol, 26, b'        return bytes32(uint256(uint160(_account)) << 96);'
lib/utils/Address.sol, 50, b'            if (_returndata.length > 0) {'
lib/utils/Initializable.sol, 17, b'    uint8 internal _initialized;'
lib/utils/Initializable.sol, 77, b'        if (_initialized < type(uint8).max) {'
lib/utils/Initializable.sol, 77, b'        if (_initialized < type(uint8).max) {'
lib/utils/Initializable.sol, 77, b'        if (_initialized < type(uint8).max) {'
lib/utils/Initializable.sol, 77, b'        if (_initialized < type(uint8).max) {'
lib/utils/Initializable.sol, 78, b'            _initialized = type(uint8).max;'
lib/utils/SafeCast.sol, 10, b'        if (x > type(uint128).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 10, b'        if (x > type(uint128).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 10, b'        if (x > type(uint128).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 12, b'        return uint128(x);'
lib/utils/SafeCast.sol, 12, b'        return uint128(x);'
lib/utils/SafeCast.sol, 16, b'        if (x > type(uint64).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 16, b'        if (x > type(uint64).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 16, b'        if (x > type(uint64).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 18, b'        return uint64(x);'
lib/utils/SafeCast.sol, 18, b'        return uint64(x);'
lib/utils/SafeCast.sol, 22, b'        if (x > type(uint48).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 22, b'        if (x > type(uint48).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 22, b'        if (x > type(uint48).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 24, b'        return uint48(x);'
lib/utils/SafeCast.sol, 24, b'        return uint48(x);'
lib/utils/SafeCast.sol, 28, b'        if (x > type(uint40).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 28, b'        if (x > type(uint40).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 28, b'        if (x > type(uint40).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 30, b'        return uint40(x);'
lib/utils/SafeCast.sol, 30, b'        return uint40(x);'
lib/utils/SafeCast.sol, 34, b'        if (x > type(uint32).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 34, b'        if (x > type(uint32).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 34, b'        if (x > type(uint32).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 36, b'        return uint32(x);'
lib/utils/SafeCast.sol, 36, b'        return uint32(x);'
lib/utils/SafeCast.sol, 40, b'        if (x > type(uint16).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 40, b'        if (x > type(uint16).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 40, b'        if (x > type(uint16).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 42, b'        return uint16(x);'
lib/utils/SafeCast.sol, 42, b'        return uint16(x);'
lib/utils/SafeCast.sol, 46, b'        if (x > type(uint8).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 46, b'        if (x > type(uint8).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 46, b'        if (x > type(uint8).max) revert UNSAFE_CAST();'
lib/utils/SafeCast.sol, 48, b'        return uint8(x);'
lib/utils/SafeCast.sol, 48, b'        return uint8(x);'
manager/Manager.sol, 82, b'        if (_owner == address(0)) revert ADDRESS_ZERO();'
manager/Manager.sol, 117, b'        if ((founder = _founderParams[0].wallet) == address(0)) revert FOUNDER_REQUIRED();'
manager/Manager.sol, 123, b'        bytes32 salt = bytes32(uint256(uint160(token)) << 96);'
manager/Manager.sol, 123, b'        bytes32 salt = bytes32(uint256(uint160(token)) << 96);'
manager/Manager.sol, 123, b'        bytes32 salt = bytes32(uint256(uint160(token)) << 96);'
manager/Manager.sol, 165, b'        bytes32 salt = bytes32(uint256(uint160(_token)) << 96);'
manager/Manager.sol, 165, b'        bytes32 salt = bytes32(uint256(uint160(_token)) << 96);'
manager/Manager.sol, 165, b'        bytes32 salt = bytes32(uint256(uint160(_token)) << 96);'
token/Token.sol, 59, b'        (string memory _name, string memory _symbol, , , ) = abi.decode(_initStrings, (string, string, string, string, string));'
token/Token.sol, 85, b'                if (founderPct == 0) continue;'
token/Token.sol, 88, b'                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();'
token/Token.sol, 91, b'                uint256 founderId = settings.numFounders++;'
token/Token.sol, 98, b'                newFounder.vestExpiry = uint32(_founders[i].vestExpiry);'
token/Token.sol, 99, b'                newFounder.ownershipPct = uint8(founderPct);'
token/Token.sol, 102, b'                uint256 schedule = 100 / founderPct;'
token/Token.sol, 118, b'                    (baseTokenId += schedule) % 100;'
token/Token.sol, 123, b'            settings.totalOwnership = uint8(totalOwnership);'
token/Token.sol, 124, b'            settings.numFounders = uint8(numFounders);'
token/Token.sol, 132, b'            while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;'
token/Token.sol, 179, b'        uint256 baseTokenId = _tokenId % 100;'
token/Token.sol, 182, b'        if (tokenRecipient[baseTokenId].wallet == address(0)) {'
token/Token.sol, 186, b'        } else if (block.timestamp < tokenRecipient[baseTokenId].vestExpiry) {'
token/Token.sol, 236, b'        return settings.numFounders;'
token/Token.sol, 241, b'        return settings.totalOwnership;'
token/Token.sol, 253, b'        uint256 numFounders = settings.numFounders;'
token/Token.sol, 280, b'        return settings.totalSupply;'