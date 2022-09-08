## Low risk
### L01: RETURN VALUES OF TRANSFER()/TRANSFERFROM() NOT CHECKED
#### prof
TRANSFER()/TRANSFERFROM() NOT CHECKED
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment
#### problem
auction/Auction.sol, 192, b'            token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);'
auction/Auction.sol, 363, b'            IWETH(WETH).transfer(_to, _amount);'

### L02: UNUSED/EMPTY RECEIVE()/FALLBACK() FUNCTION
#### problem
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert
#### prof
governance/treasury/Treasury.sol, 269, b'    receive() external payable {}'

### L03:_SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
#### problem
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### prof
token/Token.sol, 161, b'        _mint(minter, tokenId);'
token/Token.sol, 188, b'            _mint(tokenRecipient[baseTokenId].wallet, _tokenId);'

### L04: INPUT ARRAY LENGTHS MAY DIFFER
#### problem
If the caller makes a copy-paste error, the lengths may be mismatchd and an operation believed to have been completed may not in fact have been completed
function withdrawMultipleERC721(address[] memory _tokens, uint256[] memory _tokenId, address _to) external override {
#### prof
governance/governor/Governor.sol, 175, b'    function propose(\n        address[] memory _targets,\n        uint256[] memory _values,\n        bytes[] memory _calldatas,\n        string memory _description\n    ) external returns (bytes32) '
governance/governor/Governor.sol, 345, b'    function execute(\n        address[] calldata _targets,\n        uint256[] calldata _values,\n        bytes[] calldata _calldatas,\n        bytes32 _descriptionHash\n    ) external payable returns (bytes32) '
governance/governor/IGovernor.sol, 139, b'    function hashProposal(\n        address[] memory targets,\n        uint256[] memory values,\n        bytes[] memory calldatas,\n        bytes32 descriptionHash\n    ) external pure returns (bytes32);'
governance/governor/IGovernor.sol, 151, b'    function propose(\n        address[] memory targets,\n        uint256[] memory values,\n        bytes[] memory calldatas,\n        string memory description\n    ) external returns (bytes32);'
governance/governor/IGovernor.sol, 200, b'    function execute(\n        address[] memory targets,\n        uint256[] memory values,\n        bytes[] memory calldatas,\n        bytes32 descriptionHash\n    ) external payable returns (bytes32);'
governance/treasury/ITreasury.sol, 92, b'    function hashProposal(\n        address[] calldata targets,\n        uint256[] calldata values,\n        bytes[] calldata calldatas,\n        bytes32 descriptionHash\n    ) external pure returns (bytes32);'
governance/treasury/ITreasury.sol, 112, b'    function execute(\n        address[] calldata targets,\n        uint256[] calldata values,\n        bytes[] calldata calldatas,\n        bytes32 descriptionHash\n    ) external payable;'
governance/treasury/Treasury.sol, 172, b'    function execute(\n        address[] calldata _targets,\n        uint256[] calldata _values,\n        bytes[] calldata _calldatas,\n        bytes32 _descriptionHash\n    ) external payable onlyOwner '
lib/utils/TokenReceiver.sol, 36, b'    function onERC1155BatchReceived(\n        address,\n        address,\n        uint256[] calldata,\n        uint256[] calldata,\n        bytes calldata\n    ) external virtual returns (bytes4) '

### L05: address variable assigning should check if equal zero
#### prof
auction/Auction.sol,https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L40
almost all addresss varibals did not check if they equal zero.


## none-critical issue foundings
### N01: EVENT IS MISSING INDEXED FIELDS
#### prof
auction/IAuction.sol, 22, b'    event AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime);'
auction/IAuction.sol, 28, b'    event AuctionSettled(uint256 tokenId, address winner, uint256 amount);'
auction/IAuction.sol, 34, b'    event AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime);'
auction/IAuction.sol, 38, b'    event DurationUpdated(uint256 duration);'
auction/IAuction.sol, 42, b'    event ReservePriceUpdated(uint256 reservePrice);'
auction/IAuction.sol, 46, b'    event MinBidIncrementPercentageUpdated(uint256 minBidIncrementPercentage);'
auction/IAuction.sol, 50, b'    event TimeBufferUpdated(uint256 timeBuffer);'
governance/governor/IGovernor.sol, 26, b'    event ProposalCreated(\n        bytes32 proposalId,\n        address[] targets,\n        uint256[] values,\n        bytes[] calldatas,\n        string description,\n        bytes32 descriptionHash,\n        Proposal proposal\n    );'
governance/governor/IGovernor.sol, 29, b'    event ProposalQueued(bytes32 proposalId, uint256 eta);'
governance/governor/IGovernor.sol, 33, b'    event ProposalExecuted(bytes32 proposalId);'
governance/governor/IGovernor.sol, 36, b'    event ProposalCanceled(bytes32 proposalId);'
governance/governor/IGovernor.sol, 39, b'    event ProposalVetoed(bytes32 proposalId);'
governance/governor/IGovernor.sol, 42, b'    event VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason);'
governance/governor/IGovernor.sol, 45, b'    event VotingDelayUpdated(uint256 prevVotingDelay, uint256 newVotingDelay);'
governance/governor/IGovernor.sol, 48, b'    event VotingPeriodUpdated(uint256 prevVotingPeriod, uint256 newVotingPeriod);'
governance/governor/IGovernor.sol, 51, b'    event ProposalThresholdBpsUpdated(uint256 prevBps, uint256 newBps);'
governance/governor/IGovernor.sol, 54, b'    event QuorumVotesBpsUpdated(uint256 prevBps, uint256 newBps);'
governance/governor/IGovernor.sol, 57, b'    event VetoerUpdated(address prevVetoer, address newVetoer);'
governance/treasury/ITreasury.sol, 16, b'    event TransactionScheduled(bytes32 proposalId, uint256 timestamp);'
governance/treasury/ITreasury.sol, 19, b'    event TransactionCanceled(bytes32 proposalId);'
governance/treasury/ITreasury.sol, 22, b'    event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);'
governance/treasury/ITreasury.sol, 25, b'    event DelayUpdated(uint256 prevDelay, uint256 newDelay);'
governance/treasury/ITreasury.sol, 28, b'    event GracePeriodUpdated(uint256 prevGracePeriod, uint256 newGracePeriod);'
lib/interfaces/IERC1967Upgrade.sol, 14, b'    event Upgraded(address impl);'
lib/interfaces/IInitializable.sol, 13, b'    event Initialized(uint256 version);'
lib/interfaces/IPausable.sol, 14, b'    event Paused(address user);'
lib/interfaces/IPausable.sol, 18, b'    event Unpaused(address user);'
manager/IManager.sol, 21, b'    event DAODeployed(address token, address metadata, address auction, address treasury, address governor);'
manager/IManager.sol, 26, b'    event UpgradeRegistered(address baseImpl, address upgradeImpl);'
manager/IManager.sol, 31, b'    event UpgradeRemoved(address baseImpl, address upgradeImpl);'
token/IToken.sol, 21, b'    event MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder);'

### N02: inconsistent solidity version usage.
auction/Auction.sol, 2, b'pragma solidity 0.8.15;'
auction/IAuction.sol, 2, b'pragma solidity 0.8.15;'
auction/storage/AuctionStorageV1.sol, 2, b'pragma solidity 0.8.15;'
auction/types/AuctionTypesV1.sol, 2, b'pragma solidity 0.8.15;'
governance/governor/Governor.sol, 2, b'pragma solidity 0.8.15;'
governance/governor/IGovernor.sol, 2, b'pragma solidity 0.8.15;'
governance/governor/storage/GovernorStorageV1.sol, 2, b'pragma solidity 0.8.15;'
governance/governor/types/GovernorTypesV1.sol, 2, b'pragma solidity 0.8.15;'
governance/treasury/ITreasury.sol, 2, b'pragma solidity 0.8.15;'
governance/treasury/Treasury.sol, 2, b'pragma solidity 0.8.15;'
governance/treasury/storage/TreasuryStorageV1.sol, 2, b'pragma solidity 0.8.15;'
governance/treasury/types/TreasuryTypesV1.sol, 2, b'pragma solidity 0.8.15;'
lib/interfaces/IEIP712.sol, 2, b'pragma solidity ^0.8.4;'
lib/interfaces/IERC1967Upgrade.sol, 2, b'pragma solidity ^0.8.4;'
lib/interfaces/IERC721.sol, 2, b'pragma solidity ^0.8.4;'
lib/interfaces/IERC721Votes.sol, 2, b'pragma solidity ^0.8.4;'
lib/interfaces/IInitializable.sol, 2, b'pragma solidity ^0.8.4;'
lib/interfaces/IOwnable.sol, 2, b'pragma solidity ^0.8.4;'
lib/interfaces/IPausable.sol, 2, b'pragma solidity ^0.8.4;'
lib/interfaces/IUUPS.sol, 2, b'pragma solidity ^0.8.15;'
lib/interfaces/IWETH.sol, 2, b'pragma solidity ^0.8.15;'
lib/proxy/ERC1967Proxy.sol, 2, b'pragma solidity ^0.8.4;'
lib/proxy/ERC1967Upgrade.sol, 2, b'pragma solidity ^0.8.4;'
lib/proxy/UUPS.sol, 2, b'pragma solidity ^0.8.4;'
lib/token/ERC721.sol, 2, b'pragma solidity ^0.8.4;'
lib/token/ERC721Votes.sol, 2, b'pragma solidity ^0.8.4;'
lib/utils/Address.sol, 2, b'pragma solidity ^0.8.4;'
lib/utils/EIP712.sol, 2, b'pragma solidity ^0.8.4;'
lib/utils/Initializable.sol, 2, b'pragma solidity ^0.8.4;'
lib/utils/Ownable.sol, 2, b'pragma solidity ^0.8.4;'
lib/utils/Pausable.sol, 2, b'pragma solidity ^0.8.4;'
lib/utils/ReentrancyGuard.sol, 2, b'pragma solidity ^0.8.4;'
lib/utils/SafeCast.sol, 2, b'pragma solidity ^0.8.4;'
lib/utils/TokenReceiver.sol, 2, b'pragma solidity ^0.8.0;'
manager/IManager.sol, 2, b'pragma solidity 0.8.15;'
manager/Manager.sol, 2, b'pragma solidity 0.8.15;'
manager/storage/ManagerStorageV1.sol, 2, b'pragma solidity 0.8.15;'
token/IToken.sol, 2, b'pragma solidity 0.8.15;'
token/Token.sol, 2, b'pragma solidity 0.8.15;'
token/metadata/interfaces/IBaseMetadata.sol, 2, b'pragma solidity 0.8.15;'
token/metadata/storage/MetadataRendererStorageV1.sol, 2, b'pragma solidity 0.8.15;'
token/metadata/types/MetadataRendererTypesV1.sol, 2, b'pragma solidity 0.8.15;'
token/storage/TokenStorageV1.sol, 2, b'pragma solidity 0.8.15;'
token/types/TokenTypesV1.sol, 2, b'pragma solidity 0.8.15;'