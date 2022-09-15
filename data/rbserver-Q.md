## [L-01] TOKEN CAN BE LOCKED IF SENDING TO A CONTRACT
Currently, as shown below, `token.transferFrom` is called to transfer the token to a receiver address. If the receiver is a contract that does not support the ERC721 protocol, the token can be locked and cannot be retrieved. To prevent this from happening, the ERC721 `safeTransferFrom` function could be used instead of the `transferFrom` function.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L192
```solidity
token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
```

## [L-02] TOKEN CAN BE LOCKED WHEN MINTING IT TO A CONTRACT
Currently, as shown below, `super._mint` is called to mint the token to a receiver address. If the receiver is a contract that does not support the ERC721 protocol, the token can be locked and cannot be retrieved. To mitigate this risk, the ERC721 `_safeMint` function could be used instead of the `_mint` function. It looks like that the `_safeMint` function is currently not found in the relevant contracts in the codebase; please consider adding one in the `Token` contract for using it.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L167-L173
```solidity
    function _mint(address _to, uint256 _tokenId) internal override {
        // Mint the token
        super._mint(_to, _tokenId);

        // Generate the token attributes
        if (!settings.metadataRenderer.onMinted(_tokenId)) revert NO_METADATA_GENERATED();
    }
```

## [L-03] CANCEL OR VETO FUNCTION CAN BE CALLED TO CANCEL OR VETO CANCELED, VETOED, AND EXPIRED PROPOSALS
The following `cancel` or `veto` function can be called to cancel a proposal that is already canceled, vetoed, or expired, which can be meaningless. Emitting the `ProposalCanceled` and `ProposalVetoed` events for these proposals that are already canceled, vetoed, and expired can spam the frontend with meaningless information. When calling these functions, please consider requiring the proposal to be not canceled, vetoed, or expired.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L353-L377
```solidity
    function cancel(bytes32 _proposalId) external {
        // Ensure the proposal hasn't been executed
        if (state(_proposalId) == ProposalState.Executed) revert PROPOSAL_ALREADY_EXECUTED();

        // Get a copy of the proposal
        Proposal memory proposal = proposals[_proposalId];

        // Cannot realistically underflow and `getVotes` would revert
        unchecked {
            // Ensure the caller is the proposer or the proposer's voting weight has dropped below the proposal threshold
            if (msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)
                revert INVALID_CANCEL();
        }

        // Update the proposal as canceled
        proposals[_proposalId].canceled = true;

        // If the proposal was queued:
        if (settings.treasury.isQueued(_proposalId)) {
            // Cancel the proposal
            settings.treasury.cancel(_proposalId);
        }

        emit ProposalCanceled(_proposalId);
    }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L385-L405
```solidity
    function veto(bytes32 _proposalId) external {
        // Ensure the caller is the vetoer
        if (msg.sender != settings.vetoer) revert ONLY_VETOER();

        // Ensure the proposal has not been executed
        if (state(_proposalId) == ProposalState.Executed) revert PROPOSAL_ALREADY_EXECUTED();

        // Get the pointer to the proposal
        Proposal storage proposal = proposals[_proposalId];

        // Update the proposal as vetoed
        proposal.vetoed = true;

        // If the proposal was queued:
        if (settings.treasury.isQueued(_proposalId)) {
            // Cancel the proposal
            settings.treasury.cancel(_proposalId);
        }

        emit ProposalVetoed(_proposalId);
    }
```

## [L-04] CONTRACT CONSTRUCTORS ARE PAYABLE
The following `constructor` are payable. For someone who is not familar with the protocol, it is possible to send ETH to these contracts during constructions. The sent ETH will be locked in these contracts. To prevent this, please consider removing `payable` from these `constructor`.

```solidity
auction\Auction.sol
  39: constructor(address _manager, address _weth) payable initializer {  

governance\governor\Governor.sol
  41: constructor(address _manager) payable initializer {  

governance\treasury\Treasury.sol
  32: constructor(address _manager) payable initializer {  

manager\Manager.sol
  61: ) payable initializer {

token\metadata\MetadataRenderer.sol
  32: constructor(address _manager) payable initializer {  
```

## [L-05] `_proposalThresholdBps` AND `_quorumThresholdBps` CAN BE FURTHER CHECKED
The sensible range of values for the following `settings.proposalThresholdBps` and `settings.quorumThresholdBps` would be `> 0` and `<= 10_000`. Please consider checking the values of `_proposalThresholdBps` and `_quorumThresholdBps` before using them to set `settings.proposalThresholdBps` and `settings.quorumThresholdBps` to prevent unintended behaviors.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L79-L80
```solidity
        settings.proposalThresholdBps = SafeCast.toUint16(_proposalThresholdBps);
        settings.quorumThresholdBps = SafeCast.toUint16(_quorumThresholdBps);
```

## [L-06] MISSING ZERO-ADDRESS CHECK FOR CRITICAL ADDRESSES
To prevent unintended behaviors, the critical address inputs should be checked against `address(0)`. Please consider checking the `address` variables in the following `constructor`.

```solidity
auction\Auction.sol
  39: constructor(address _manager, address _weth) payable initializer {  

governance\governor\Governor.sol
  41: constructor(address _manager) payable initializer {  

governance\treasury\Treasury.sol
  32: constructor(address _manager) payable initializer {  

lib\proxy\ERC1967Proxy.sol
  21: constructor(address _logic, bytes memory _data) payable {

manager\Manager.sol
  55: constructor(
        address _tokenImpl,
        address _metadataImpl,
        address _auctionImpl,
        address _treasuryImpl,
        address _governorImpl

token\Token.sol
  30: constructor(address _manager) payable initializer {

token\metadata\MetadataRenderer.sol
  32: constructor(address _manager) payable initializer {  
```

## [N-01] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of the magic numbers. Please consider replacing the magic numbers used in the following code with constants.
```solidity
auction\Auction.sol
  80: settings.timeBuffer = 5 minutes;
  81: settings.minBidIncrement = 10;
  119: minBid = highestBid + ((highestBid * settings.minBidIncrement) / 100);

governance\governor\Governor.sol
  468: return (settings.token.totalSupply() * settings.proposalThresholdBps) / 10_000;
  475: return (settings.token.totalSupply() * settings.quorumThresholdBps) / 10_000;

governance\treasury\Treasury.sol
  57: settings.gracePeriod = 2 weeks;
```

## [N-02] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. @param or @return comments are missing for the following functions. Please consider completing NatSpec comments for them.
```solidity
governance\governor\Governor.sol
  184: function castVote(bytes32 _proposalId, uint256 _support) external returns (uint256) {   
  192: function castVoteWithReason(   
  208: function castVoteBySig(   
  248: function _castVote(   
  305: function queue(bytes32 _proposalId) external returns (uint256 eta) {   
  324: function execute(   
  413: function state(bytes32 _proposalId) public view returns (ProposalState) {   
  461: function getVotes(address _account, uint256 _timestamp) public view returns (uint256) {    
  466: function proposalThreshold() public view returns (uint256) {    
  473: function quorum() public view returns (uint256) {    
  481: function getProposal(bytes32 _proposalId) external view returns (Proposal memory) {    
  487: function proposalSnapshot(bytes32 _proposalId) external view returns (uint256) {    
  493: function proposalDeadline(bytes32 _proposalId) external view returns (uint256) {    
  499: function proposalVotes(bytes32 _proposalId)    
  515: function proposalEta(bytes32 _proposalId) external view returns (uint256) {    
  524: function proposalThresholdBps() external view returns (uint256) {    
  529: function quorumThresholdBps() external view returns (uint256) {    
  534: function votingDelay() external view returns (uint256) {    
  539: function votingPeriod() external view returns (uint256) {    
  544: function vetoer() external view returns (address) {    
  549: function token() external view returns (address) {    
  554: function treasury() external view returns (address) {    

governance\treasury\Treasury.sol
  68: function timestamp(bytes32 _proposalId) external view returns (uint256) {   
  74: function isExpired(bytes32 _proposalId) external view returns (bool) {   
  82: function isQueued(bytes32 _proposalId) public view returns (bool) {   
  88: function isReady(bytes32 _proposalId) public view returns (bool) {   
  101: function hashProposal(   
  116: function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) {   
  195: function delay() external view returns (uint256) {   
  200: function gracePeriod() external view returns (uint256) {   
  237: function onERC721Received(   
  247: function onERC1155Received(   
  258: function onERC1155BatchReceived(   

token\Token.sol
  221: function tokenURI(uint256 _tokenId) public view override(IToken, ERC721) returns (string memory) {  
  226: function contractURI() public view override(IToken, ERC721) returns (string memory) {  
  235: function totalFounders() external view returns (uint256) {  
  240: function totalFounderOwnership() external view returns (uint256) {  
  246: function getFounder(uint256 _founderId) external view returns (Founder memory) {  
  251: function getFounders() external view returns (Founder[] memory) {  
  270: function getScheduledRecipient(uint256 _tokenId) external view returns (Founder memory) {  
  279: function totalSupply() external view returns (uint256) {  
  284: function auction() external view returns (address) {  
  289: function metadataRenderer() external view returns (address) {  
  294: function owner() public view returns (address) {  

token\metadata\MetadataRenderer.sol
  77: function propertiesCount() external view returns (uint256) {    
  83: function itemsCount(uint256 _propertyId) external view returns (uint256) {    
  171: function onMinted(uint256 _tokenId) external returns (bool) {    
  206: function getAttributes(uint256 _tokenId) public view returns (bytes memory aryAttributes, bytes memory queryString) {    
  250: function _generateSeed(uint256 _tokenId) private view returns (uint256) {    
  255: function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {    
  269: function contractURI() external view returns (string memory) {    
  286: function tokenURI(uint256 _tokenId) external view returns (string memory) {    
  308: function _encodeAsJson(bytes memory _jsonBlob) private pure returns (string memory) {    
  317: function token() external view returns (address) {    
  322: function treasury() external view returns (address) {    
  327: function contractImage() external view returns (string memory) {    
  332: function rendererBase() external view returns (string memory) {    
  337: function description() external view returns (string memory) {    
```