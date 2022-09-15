# Nouns Builder Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (40 instances)
2. Use `unchecked{}` to suppress overflow/underflow check (8 instances)
3. Using `bool`s for storage incurs overhead (13 instances)
4. Use `!= 0` instead of `> 0` when comparing uint (3 instances)
5. Empty blocks should be removed or emit something (6 instances)
6. Don't initialize variables with default value (5 instances)
7. Use `abi.encodePacked()` instead of `abi.encode()` (10 instances)
8. Use shift right/left instead of division/multiplication if possible (1 instances)
9. Using `bool`s for storage incurs overhead (13 instances)
10. Use `private` instead of `public` for constants (1 instances)
11. Use `++x`/`--x` is more efficient than `x++`/`x--` (6 instances)
12. Use `x = x + y` instead of `x += y` (6 instances)

Total 112 instances of 12 issues.

## 1. Use `calldata` instead of `memory` (40 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
src/governance/governor/Governor.sol::481 => function getProposal(bytes32 _proposalId) external view returns (Proposal memory) {

src/governance/governor/IGovernor.sol::227 => function getProposal(bytes32 proposalId) external view returns (Proposal memory);

src/lib/interfaces/IUUPS.sol::35 => function upgradeToAndCall(address newImpl, bytes memory data) external payable;

src/lib/proxy/UUPS.sol::55 => function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {

src/lib/token/ERC721.sol::47 => function __ERC721_init(string memory _name, string memory _symbol) internal onlyInitializing {

src/lib/token/ERC721.sol::54 => function tokenURI(uint256 _tokenId) public view virtual returns (string memory) {}

src/lib/token/ERC721.sol::57 => function contractURI() public view virtual returns (string memory) {}

src/lib/utils/Address.sol::37 => function functionDelegateCall(address _target, bytes memory _data) internal returns (bytes memory) {

src/lib/utils/Address.sol::46 => function verifyCallResult(bool _success, bytes memory _returndata) internal pure returns (bytes memory) {

src/lib/utils/EIP712.sol::48 => function __EIP712_init(string memory _name, string memory _version) internal onlyInitializing {

src/token/IToken.sol::67 => function tokenURI(uint256 tokenId) external view returns (string memory);

src/token/IToken.sol::70 => function contractURI() external view returns (string memory);

src/token/IToken.sol::80 => function getFounder(uint256 founderId) external view returns (Founder memory);

src/token/IToken.sol::83 => function getFounders() external view returns (Founder[] memory);

src/token/IToken.sol::88 => function getScheduledRecipient(uint256 tokenId) external view returns (Founder memory);

src/token/Token.sol::221 => function tokenURI(uint256 _tokenId) public view override(IToken, ERC721) returns (string memory) {

src/token/Token.sol::226 => function contractURI() public view override(IToken, ERC721) returns (string memory) {

src/token/Token.sol::246 => function getFounder(uint256 _founderId) external view returns (Founder memory) {

src/token/Token.sol::251 => function getFounders() external view returns (Founder[] memory) {

src/token/Token.sol::270 => function getScheduledRecipient(uint256 _tokenId) external view returns (Founder memory) {

src/token/metadata/MetadataRenderer.sol::206 => function getAttributes(uint256 _tokenId) public view returns (bytes memory aryAttributes, bytes memory queryString) {

src/token/metadata/MetadataRenderer.sol::255 => function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {

src/token/metadata/MetadataRenderer.sol::269 => function contractURI() external view returns (string memory) {

src/token/metadata/MetadataRenderer.sol::286 => function tokenURI(uint256 _tokenId) external view returns (string memory) {

src/token/metadata/MetadataRenderer.sol::308 => function _encodeAsJson(bytes memory _jsonBlob) private pure returns (string memory) {

src/token/metadata/MetadataRenderer.sol::327 => function contractImage() external view returns (string memory) {

src/token/metadata/MetadataRenderer.sol::332 => function rendererBase() external view returns (string memory) {

src/token/metadata/MetadataRenderer.sol::337 => function description() external view returns (string memory) {

src/token/metadata/MetadataRenderer.sol::347 => function updateContractImage(string memory _newContractImage) external onlyOwner {

src/token/metadata/MetadataRenderer.sol::355 => function updateRendererBase(string memory _newRendererBase) external onlyOwner {

src/token/metadata/MetadataRenderer.sol::363 => function updateDescription(string memory _newDescription) external onlyOwner {

src/token/metadata/interfaces/IBaseMetadata.sol::40 => function tokenURI(uint256 tokenId) external view returns (string memory);

src/token/metadata/interfaces/IBaseMetadata.sol::43 => function contractURI() external view returns (string memory);

src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::63 => function getAttributes(uint256 tokenId) external view returns (bytes memory aryAttributes, bytes memory queryString);

src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::66 => function contractImage() external view returns (string memory);

src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::69 => function rendererBase() external view returns (string memory);

src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::72 => function description() external view returns (string memory);

src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::76 => function updateContractImage(string memory newContractImage) external;

src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::80 => function updateRendererBase(string memory newRendererBase) external;

src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::84 => function updateDescription(string memory newDescription) external;
```

## 2. Use `unchecked{}` to suppress overflow/underflow check (8 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
src/governance/treasury/Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {

src/token/Token.sol::80 => for (uint256 i; i < numFounders; ++i) {

src/token/Token.sol::108 => for (uint256 j; j < founderPct; ++j) {

src/token/Token.sol::261 => for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];

src/token/metadata/MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {

src/token/metadata/MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {

src/token/metadata/MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {

src/token/metadata/MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```

## 3. Using `bool`s for storage incurs overhead (13 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
src/auction/Auction.sol::136 => bool extend;

src/auction/Auction.sol::349 => bool success;

src/auction/types/AuctionTypesV1.sol::35 => bool settled;

src/governance/governor/types/GovernorTypesV1.sol::52 => bool executed;

src/governance/governor/types/GovernorTypesV1.sol::53 => bool canceled;

src/governance/governor/types/GovernorTypesV1.sol::54 => bool vetoed;

src/lib/proxy/ERC1967Upgrade.sol::36 => bool _forceCall

src/lib/proxy/ERC1967Upgrade.sol::57 => bool _forceCall

src/lib/utils/Initializable.sol::20 => bool internal _initializing;

src/lib/utils/Initializable.sol::34 => bool isTopLevelCall = !_initializing;

src/lib/utils/Pausable.sol::15 => bool internal _paused;

src/token/metadata/MetadataRenderer.sol::231 => bool isLast = i == lastProperty;

src/token/metadata/types/MetadataRendererTypesV1.sol::11 => bool isNewProperty;
```

## 4. Use `!= 0` instead of `> 0` when comparing uint (3 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
src/lib/proxy/ERC1967Upgrade.sol::61 => if (_data.length > 0 || _forceCall) {

src/lib/token/ERC721Votes.sol::203 => if (_from != _to && _amount > 0) {

src/lib/utils/Address.sol::50 => if (_returndata.length > 0) {
```

## 5. Empty blocks should be removed or emit something (6 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
src/governance/treasury/Treasury.sol::269 => receive() external payable {}

src/lib/token/ERC721.sol::54 => function tokenURI(uint256 _tokenId) public view virtual returns (string memory) {}

src/lib/token/ERC721.sol::57 => function contractURI() public view virtual returns (string memory) {}

src/lib/token/ERC721.sol::239 => ) internal virtual {}

src/lib/token/ERC721.sol::249 => ) internal virtual {}

src/manager/Manager.sol::209 => function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
```

## 6. Don't initialize variables with default value (5 instances)

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```solidity
src/governance/treasury/Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {

src/token/metadata/MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {

src/token/metadata/MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {

src/token/metadata/MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {

src/token/metadata/MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```

## 7. Use `abi.encodePacked()` instead of `abi.encode()` (10 instances)

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
src/governance/governor/Governor.sol::104 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));

src/governance/governor/Governor.sol::230 => keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))

src/governance/treasury/Treasury.sol::107 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));

src/lib/token/ERC721Votes.sol::162 => abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))

src/lib/utils/EIP712.sol::69 => return keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));

src/manager/Manager.sol::68 => metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));

src/manager/Manager.sol::69 => auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));

src/manager/Manager.sol::70 => treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));

src/manager/Manager.sol::71 => governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));

src/token/metadata/MetadataRenderer.sol::251 => return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
```

## 8. Use shift right/left instead of division/multiplication if possible (1 instances)

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left. While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```solidity
src/lib/token/ERC721Votes.sol::95 => middle = high - (high - low) / 2;
```

## 9. Using `bool`s for storage incurs overhead (13 instances)

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from `false` to `true`, after having been `true` in the past.

```solidity
src/auction/Auction.sol::136 => bool extend;

src/auction/Auction.sol::349 => bool success;

src/auction/types/AuctionTypesV1.sol::35 => bool settled;

src/governance/governor/types/GovernorTypesV1.sol::52 => bool executed;

src/governance/governor/types/GovernorTypesV1.sol::53 => bool canceled;

src/governance/governor/types/GovernorTypesV1.sol::54 => bool vetoed;

src/lib/proxy/ERC1967Upgrade.sol::36 => bool _forceCall

src/lib/proxy/ERC1967Upgrade.sol::57 => bool _forceCall

src/lib/utils/Initializable.sol::20 => bool internal _initializing;

src/lib/utils/Initializable.sol::34 => bool isTopLevelCall = !_initializing;

src/lib/utils/Pausable.sol::15 => bool internal _paused;

src/token/metadata/MetadataRenderer.sol::231 => bool isLast = i == lastProperty;

src/token/metadata/types/MetadataRendererTypesV1.sol::11 => bool isNewProperty;
```

## 10. Use `private` instead of `public` for constants (1 instances)

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
src/governance/governor/Governor.sol::27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```

## 11. Use `++x`/`--x` is more efficient than `x++`/`x--` (6 instances)

Using `++x`/`--x` saves around 6 gas.

```solidity
src/governance/governor/Governor.sol::230 => keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))

src/lib/token/ERC721Votes.sol::162 => abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))

src/lib/token/ERC721Votes.sol::207 => uint256 nCheckpoints = numCheckpoints[_from]++;

src/lib/token/ERC721Votes.sol::222 => uint256 nCheckpoints = numCheckpoints[_to]++;

src/token/Token.sol::91 => uint256 founderId = settings.numFounders++;

src/token/Token.sol::154 => tokenId = settings.totalSupply++;
```

## 12. Use `x = x + y` instead of `x += y` (6 instances)

The expression `x = x + y` is less expensive than `x += y`.

```solidity
src/governance/governor/Governor.sol::280 => proposal.againstVotes += uint32(weight);

src/governance/governor/Governor.sol::285 => proposal.forVotes += uint32(weight);

src/governance/governor/Governor.sol::290 => proposal.abstainVotes += uint32(weight);

src/token/Token.sol::88 => if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();

src/token/Token.sol::118 => (baseTokenId += schedule) % 100;

src/token/metadata/MetadataRenderer.sol::140 => _propertyId += numStoredProperties;
```
