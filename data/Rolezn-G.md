## (1) Abi.encode() Is Less Efficient Than Abi.encodepacked()

Severity: Gas Optimizations

## Proof Of Concept


	return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L104

	keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L230

	return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L107

	abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L162

	return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L251





## (2) Using Calldata Instead Of Memory For Read-only Arguments In External Functions Saves Gas

Severity: Gas Optimizations

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one


## Proof Of Concept


	function hashProposal(
        address[] memory _targets,
        uint256[] memory _values,
        bytes[] memory _calldatas,
        bytes32 _descriptionHash
    ) public pure returns (bytes32) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L98-L103

	function propose(
        address[] memory _targets,
        uint256[] memory _values,
        bytes[] memory _calldatas,
        string memory _description
    ) external returns (bytes32) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L116-L121

	function onERC1155BatchReceived(
        address,
        address,
        uint256[] memory,
        uint256[] memory,
        bytes memory
    ) public pure returns (bytes4) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L258-L264

	function getFounders() external view returns (Founder[] memory) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L252






## (3) Empty Blocks Should Be Removed Or Emit Something

Severity: Gas Optimizations

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

## Proof Of Concept

	receive() external payable {}
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L269



## (4) It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

Severity: Gas Optimizations

## Proof Of Concept


	for (uint256 i = 0; i < numTargets; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L162

	for (uint256 i = 0; i < numNewProperties; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L119

	for (uint256 i = 0; i < numProperties; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L189

	for (uint256 i = 0; i < numProperties; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L229






## (5) Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Severity: Gas Optimizations

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

## Proof Of Concept


	mapping(address => uint256) internal balances;
    mapping(address => mapping(address => bool)) internal operatorApprovals;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L30

	mapping(address => address) internal delegation;
    mapping(address => uint256) internal numCheckpoints;
    mapping(address => mapping(uint256 => Checkpoint)) internal checkpoints;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L29






## (6) Multiplication/division By Two Should Use Bit Shifting

Severity: Gas Optimizations

<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

## Proof Of Concept


	middle = high - (high - low) / 2;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L95





## (7) <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables

Severity: Gas Optimizations

## Proof Of Concept


	proposal.againstVotes += uint32(weight);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L280

	proposal.forVotes += uint32(weight);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L285

	proposal.abstainVotes += uint32(weight);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L290

	if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L88

	(baseTokenId += schedule) % 100;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L118

	_propertyId += numStoredProperties;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L140





## (8) Using Private Rather Than Public For Constants, Saves Gas

Severity: Gas Optimizations

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

## Proof Of Concept



    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L27



## Recommended Mitigation Steps

Set variable to private.



## (9) Public Functions To External

Severity: Gas Optimizations

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

## Proof Of Concept


	function hashProposal(
        address[] memory _targets,
        uint256[] memory _values,
        bytes[] memory _calldatas,
        bytes32 _descriptionHash
    ) public pure returns (bytes32) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L98

	function state(bytes32 _proposalId) public view returns (ProposalState) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L413

	function getVotes(address _account, uint256 _timestamp) public view returns (uint256) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L461

	function proposalThreshold() public view returns (uint256) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L466

	function quorum() public view returns (uint256) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L473

	function isQueued(bytes32 _proposalId) public view returns (bool) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L82

	function isReady(bytes32 _proposalId) public view returns (bool) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L88

	function hashProposal(
        address[] calldata _targets,
        uint256[] calldata _values,
        bytes[] calldata _calldatas,
        bytes32 _descriptionHash
    ) public pure returns (bytes32) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L101

	function onERC721Received(
        address,
        address,
        uint256,
        bytes memory
    ) public pure returns (bytes4) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L237

	function onERC1155Received(
        address,
        address,
        uint256,
        uint256,
        bytes memory
    ) public pure returns (bytes4) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L247

	function onERC1155BatchReceived(
        address,
        address,
        uint256[] memory,
        uint256[] memory,
        bytes memory
    ) public pure returns (bytes4) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L258

	function tokenURI(uint256 _tokenId) public view override(IToken, ERC721) returns (string memory) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L222

	function contractURI() public view override(IToken, ERC721) returns (string memory) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L227

	function owner() public view returns (address) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L295

	function getAttributes(uint256 _tokenId) public view returns (bytes memory aryAttributes, bytes memory queryString) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L206






## (10) Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It

Severity: Gas Optimizations

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

## Proof Of Concept


	Proposal storage proposal = proposals[proposalId];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L148

	Founder storage newFounder = founder[founderId];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L94

	if (tokenRecipient[baseTokenId].wallet == address(0)) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L183

	} else if (block.timestamp < tokenRecipient[baseTokenId].vestExpiry) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L187

	_mint(tokenRecipient[baseTokenId].wallet, _tokenId);
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L189

	delete tokenRecipient[baseTokenId];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L196

	properties[propertyId].name = _names[i];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L127

	Item storage newItem = items[newItemIndex];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L154

	Item memory item = property.items[attribute];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L240






## (11) Use of non-uint256 state variable is less efficient than uint256

Severity: Gas Optimizations

Lower than uint256 size storage variables are less gas efficient.
i.e. Using uint40 does not give any efficiency, actually, it is the opposite as EVM operates on default of 256-bit values so uint40 is more expensive in this case as it needs a conversion. It only gives improvements in cases where you can pack variables together, e.g. structs.

## Proof Of Concept


	uint40 duration;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol#L16

	uint40 timeBuffer;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol#L17

	uint8 minBidIncrement;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol#L18

	uint40 startTime;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol#L33

	uint40 endTime;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol#L34

	uint16 proposalThresholdBps;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L21

	uint16 quorumThresholdBps;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L22

	uint48 votingDelay;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L24

	uint48 votingPeriod;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L25

	uint32 timeCreated;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L44

	uint32 againstVotes;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L45

	uint32 forVotes;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L46

	uint32 abstainVotes;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L47

	uint32 voteStart;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L48

	uint32 voteEnd;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L49

	uint32 proposalThreshold;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L50

	uint32 quorumVotes;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L51

	uint128 gracePeriod;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/types/TreasuryTypesV1.sol#L12

	uint128 delay;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/types/TreasuryTypesV1.sol#L13

	uint16[16] storage tokenAttributes = attributes[_tokenId];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L179

	uint16[16] memory tokenAttributes = attributes[_tokenId];
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L216

	uint16 referenceSlot;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/types/MetadataRendererTypesV1.sol#L20

	uint96 totalSupply;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol#L18

	uint8 numFounders;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol#L20

	uint8 totalOwnership;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol#L21

	uint8 ownershipPct;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol#L30

	uint32 vestExpiry;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol#L31






## (12) ++i/i++ Should Be Unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

Severity: Gas Optimizations

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

## Proof Of Concept


	for (uint256 i = 0; i < numTargets; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L162

	for (uint256 i; i < numFounders; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L80

	for (uint256 j; j < founderPct; ++j) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L108

	for (uint256 i = 0; i < numNewProperties; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L119

	for (uint256 i = 0; i < numNewItems; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L133

	for (uint256 i = 0; i < numProperties; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L189

	for (uint256 i = 0; i < numProperties; ++i) {
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L229





## (13) Use assembly to check for address(0)

Severity: Gas Optimizations

Save 6 gas per instance if using assembly to check for address(0)

e.g.
assembly {
	if iszero(_addr) {
		mstore(0x00, "AddressZero")
		revert(0x00, 0x20)
	}
}

## Proof Of Concept


    if (_newVetoer == address(0)) revert ADDRESS_ZERO();
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L597

    if (_owner == address(0)) revert ADDRESS_ZERO();
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L82






## (14) Using Bools For Storage Incurs Overhead

Severity: Gas Optimizations

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

## Proof Of Concept


    mapping(bytes32 => mapping(address => bool)) internal hasVoted;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/storage/GovernorStorageV1.sol#L19

    mapping(address => mapping(address => bool)) internal isUpgrade;
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/storage/ManagerStorageV1.sol#L10





