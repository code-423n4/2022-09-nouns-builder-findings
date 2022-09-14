## (1) Contracts are not using their OZ Upgradeable counterparts

Severity: Low

The non-upgradeable standard version of OpenZeppelin’s library, such as UUPS, Ownable, ReentrancyGuard and Pausable are inherited / used by both the manager and the implementation contracts.

It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

## Recommended Mitigation Steps

Where applicable, use the contracts from @openzeppelin/contracts-upgradeable instead of @openzeppelin/contracts.


## (2) Unable to change founder wallet address

Severity: Low

In Token.sol once founders have been added it is no longer possible to change the founders addresses.
Should there be a case where a founder wishes to change his wallet address, it wouldn't be possible as there is no option to update his wallet address.

## Recommended Mitigation Steps

Add an option for the specific founder to change his wallet address.


## (3) The comment '// Ensure at least one founder is provided' in Manager.sol is wrong

Severity: Low

The comment mentions that it ensures there's at least one founder is provided. However, the following requirement only checksfor the first existence of the first founder's wallet address and not the rest of the founders provided in _foundersParams.

	if ((founder = _founderParams[0].wallet) == address(0)) revert FOUNDER_REQUIRED();
	
## Proof of Concept

	// Ensure at least one founder is provided
	if ((founder = _founderParams[0].wallet) == address(0)) revert FOUNDER_REQUIRED();
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L116-L117

## (4) Missing WhenNotPaused modifier 

Severity: Low

Function createBid can still be called when the auction is paused. Should there be any issues with the createBid function and since it deals with IWETH transfers. It is also recommended to pause this external function.

## Proof Of Concept

	function createBid(uint256 _tokenId) external payable nonReentrant {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L90


## Recommended Mitigation Steps

Added the whenNotPaused modifier for createBid


## (5) Use _safeMint instead of _mint

Severity: Low

According to openzepplin's ERC721, the use of _mint is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

## Proof Of Concept

	_mint(minter, tokenId);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L162

	_mint(tokenRecipient[baseTokenId].wallet, _tokenId);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L189



## Recommended Mitigation Steps

Use _safeMint whenever possible instead of _mint



## (6) Missing Checks for Address(0x0) 

Severity: Low

## Proof Of Concept


	function _upgradeTo(address _newImpl) internal {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L68

	function _setImplementation(address _impl) private {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L76

	function delegate(address _to) external {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L133

	function _delegate(address _from, address _to) internal {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L179

	function _delegate(address _from, address _to) internal {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L179

	function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L187

	function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L187

	function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L196

	function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L196

	function _authorizeUpgrade(address _newImpl) internal override onlyOwner {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L209



## Recommended Mitigation Steps

Consider adding zero-address checks in the mentioned codebase.



## (7) Use Safetransfer Instead Of Transfer 

Severity: Low

It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

Reference: This similar medium-severity (https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call) finding from Consensys Diligence Audit of Fei Protocol.

## Proof Of Concept


	token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L192


## Recommended Mitigation Steps

Consider using safeTransfer/safeTransferFrom or require() consistently.



## (8) Transferownership Should Be Two Step

Severity: Low

The owner is the authorized user in the solidity contracts. Usually, an owner can be updated with transferOwnership function. However, the process is only completed with single transaction. If the address is updated incorrectly, an owner functionality will be lost forever.

## Proof Of Concept


	transferOwnership(settings.treasury);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L250

	transferOwnership(settings.treasury);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L102



## Recommended Mitigation Steps

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



## (9) Unused Receive() Function Will Lock Ether In Contract 

Severity: Low

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

## Proof Of Concept


	receive() external payable {}
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L269



## Recommended Mitigation Steps

The function should call another function, otherwise it should revert



## (10) Event Is Missing Indexed Fields

Severity: Non-Critical

Each event should use three indexed fields if there are three or more fields.
In addition, each event should have at least one indexed fields to allow easier filtering of logs.

## Proof Of Concept


	event AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/IAuction.sol#L22

	event AuctionSettled(uint256 tokenId, address winner, uint256 amount);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/IAuction.sol#L28

	event AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/IAuction.sol#L34

	event ProposalCreated(
        bytes32 proposalId,
        address[] targets,
        uint256[] values,
        bytes[] calldatas,
        string description,
        bytes32 descriptionHash,
        Proposal proposal
    );
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L18

	event ProposalQueued(bytes32 proposalId, uint256 eta);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L29

	event VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L42

	event VotingDelayUpdated(uint256 prevVotingDelay, uint256 newVotingDelay);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L45

	event VotingPeriodUpdated(uint256 prevVotingPeriod, uint256 newVotingPeriod);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L48

	event ProposalThresholdBpsUpdated(uint256 prevBps, uint256 newBps);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L51

	event QuorumVotesBpsUpdated(uint256 prevBps, uint256 newBps);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L54

	event VetoerUpdated(address prevVetoer, address newVetoer);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L57

	event TransactionScheduled(bytes32 proposalId, uint256 timestamp);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/ITreasury.sol#L16

	event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/ITreasury.sol#L22

	event DelayUpdated(uint256 prevDelay, uint256 newDelay);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/ITreasury.sol#L25

	event GracePeriodUpdated(uint256 prevGracePeriod, uint256 newGracePeriod);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/ITreasury.sol#L28

	event DAODeployed(address token, address metadata, address auction, address treasury, address governor);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/IManager.sol#L21

	event UpgradeRegistered(address baseImpl, address upgradeImpl);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/IManager.sol#L26

	event UpgradeRemoved(address baseImpl, address upgradeImpl);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/IManager.sol#L31

	event MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/IToken.sol#L21

	event PropertyAdded(uint256 id, string name);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L16

	event ItemAdded(uint256 propertyId, uint256 index);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L19

	event ContractImageUpdated(string prevImage, string newImage);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L22

	event RendererBaseUpdated(string prevRendererBase, string newRendererBase);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L25

	event DescriptionUpdated(string prevDescription, string newDescription);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L28


## (11) Use a more recent version of Solidity

Severity: Non-Critical

Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

## Proof Of Concept


	Found old version 0.8.4 of Solidity in ERC1967Proxy.sol
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Proxy.sol#L2

	Found old version 0.8.4 of Solidity in ERC1967Upgrade.sol
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L2

	Found old version 0.8.4 of Solidity in UUPS.sol
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol#L2

	Found old version 0.8.4 of Solidity in ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L2

	Found old version 0.8.4 of Solidity in ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L2



## Recommended Mitigation Steps

Consider updating to a more recent solidity version.



## (12) Adding A Return Statement When The Function Defines A Named Return Variable, Is Redundant

Severity: Non-Critical

## Proof Of Concept

	function token() external view returns (address) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L549

	function treasury() external view returns (address) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L554

	function metadataRenderer() external view returns (address) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L290

	function _generateSeed(uint256 _tokenId) private view returns (uint256) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L250





## (13) Use of Block.Timestamp

Severity: Non-Critical

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

## Proof Of Concept


    if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L98

    extend = (_auction.endTime - block.timestamp) < settings.timeBuffer;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L141

    auction.endTime = uint40(block.timestamp + settings.timeBuffer);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L149

    if (block.timestamp < _auction.endTime) revert AUCTION_ACTIVE();
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L178

    uint256 startTime = block.timestamp;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L211

    if (block.timestamp > _deadline) revert EXPIRED_SIGNATURE();
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L218

    } else if (block.timestamp < proposal.voteStart) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L433

    } else if (block.timestamp < proposal.voteEnd) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L437

    return block.timestamp > (timestamps[_proposalId] + settings.gracePeriod);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L76

    return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L89

    eta = block.timestamp + settings.delay;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L123

    } else if (block.timestamp < tokenRecipient[baseTokenId].vestExpiry) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L187


## Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



