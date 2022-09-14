# Gas Report

|No.|Issue|Instances|
|:-|:-|:-:|
|1|For-loops: Index initialized with default value|5|
|2|Arithmetics: `++i` costs less gas compared to `i++` or `i += 1`|2|
|3|Arithmetics: Use Shift Right/Left instead of Division/Multiplication if possible|1|
|4|Using `bool` for storage incurs overhead|5|
|5|Use `calldata` instead of `memory` for read-only arguments in external functions|9|
|6|Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead|39|
|7|`abi.encode()` is less efficient than `abi.encodePacked()`|4|
|8|`internal` functions only called once can be inlined to save gas|2|
|9|Multiple accesses of a mapping/array should use a local variable cache|3|

Total: **70** instances over **9** issues

## For-loops: Index initialized with default value
Uninitialized `uint` variables are assigned with a default value of `0`. 

Thus, in for-loops, explicitly initializing an index with `0` costs unnecesary gas. For example, the following code:
```js
for (uint256 i = 0; i < length; ++i) {
```
can be written without explicitly setting the index to `0`:  
```js
for (uint256 i; i < length; ++i) {
```

_There are **5** instances of this issue:_  
```solidity
src/governance/treasury/Treasury.sol:
 162:        for (uint256 i = 0; i < numTargets; ++i) {

src/token/metadata/MetadataRenderer.sol:
 119:        for (uint256 i = 0; i < numNewProperties; ++i) {
 133:        for (uint256 i = 0; i < numNewItems; ++i) {
 189:        for (uint256 i = 0; i < numProperties; ++i) {
 229:        for (uint256 i = 0; i < numProperties; ++i) {
```

## Arithmetics: `++i` costs less gas compared to `i++` or `i += 1`
`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers, as pre-increment is cheaper (about **5 gas** per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:
```js
uint i = 1;  
i++; // == 1 but i == 2  
```
But `++i` returns the actual incremented value:
```js
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`, thus it costs more gas.

The same logic applies for `--i` and `i--`.

_There are **2** instances of this issue:_  
```solidity
src/token/Token.sol:
  91:        uint256 founderId = settings.numFounders++;
 154:        tokenId = settings.totalSupply++;
```

## Arithmetics: Use Shift Right/Left instead of Division/Multiplication if possible
A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.

While the `MUL` and `DIV` opcodes use 5 gas, the `SHL` and `SHR` opcodes only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

For example, the following code:
```js
uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
```
can be changed to:
```js
uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;
```

_There is **1** instance of this issue:_  
```solidity
src/lib/token/ERC721Votes.sol:
  95:        middle = high - (high - low) / 2;
```

## Using `bool` for storage incurs overhead
Declaring storage variables as `bool` is more expensive compared to `uint256`, as explained [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27):

> Booleans are more expensive than `uint256` or any type that takes up a full word because each write operation emits an extra `SLOAD` to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Use `uint256(1)` and `uint256(2)` rather than true/false to avoid a Gwarmaccess ([**100 gas**](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)) for the extra `SLOAD`, and to avoid Gsset (**20000 gas**) when changing from 'false' to 'true', after having been 'true' in the past.

_There are **5** instances of this issue:_  
```solidity
src/manager/storage/ManagerStorageV1.sol:
  10:        mapping(address => mapping(address => bool)) internal isUpgrade;

src/governance/governor/storage/GovernorStorageV1.sol:
  19:        mapping(bytes32 => mapping(address => bool)) internal hasVoted;

src/lib/token/ERC721.sol:
  38:        mapping(address => mapping(address => bool)) internal operatorApprovals;

src/lib/utils/Pausable.sol:
  15:        bool internal _paused;

src/lib/utils/Initializable.sol:
  20:        bool internal _initializing;
```

## Use `calldata` instead of `memory` for read-only arguments in external functions
When an external function with a `memory` array is called, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). 

Using `calldata` directly instead of `memory` helps to save gas as values are read directly from `calldata` using `calldataload`, thus removing the need for such a loop in the contract code during runtime execution. 

Also, structs have the same overhead as an array of length one.

_There are **9** instances of this issue:_  
```solidity
src/governance/governor/Governor.sol:
 117:        address[] memory _targets,
 118:        uint256[] memory _values,
 119:        bytes[] memory _calldatas,
 120:        string memory _description
 195:        string memory _reason

src/token/metadata/MetadataRenderer.sol:
 347:        function updateContractImage(string memory _newContractImage) external onlyOwner {
 355:        function updateRendererBase(string memory _newRendererBase) external onlyOwner {
 363:        function updateDescription(string memory _newDescription) external onlyOwner {

src/lib/proxy/UUPS.sol:
  55:        function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {
```

## Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
As seen from [here](https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html):
> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

However, this does not apply to storage values as using reduced-size types might be beneficial to pack multiple elements into a single storage slot. Thus, where appropriate, use `uint256`/`int256` and downcast when needed.

_There are **39** instances of this issue:_  
```solidity
src/governance/governor/Governor.sol:
 213:        uint8 _v,

src/governance/governor/IGovernor.sol:
 181:        uint8 v,

src/governance/governor/types/GovernorTypesV1.sol:
  21:        uint16 proposalThresholdBps;
  22:        uint16 quorumThresholdBps;
  24:        uint48 votingDelay;
  25:        uint48 votingPeriod;
  44:        uint32 timeCreated;
  45:        uint32 againstVotes;
  46:        uint32 forVotes;
  47:        uint32 abstainVotes;
  48:        uint32 voteStart;
  49:        uint32 voteEnd;
  50:        uint32 proposalThreshold;
  51:        uint32 quorumVotes;

src/governance/treasury/types/TreasuryTypesV1.sol:
  12:        uint128 gracePeriod;
  13:        uint128 delay;

src/token/types/TokenTypesV1.sol:
  18:        uint96 totalSupply;
  20:        uint8 numFounders;
  21:        uint8 totalOwnership;
  30:        uint8 ownershipPct;
  31:        uint32 vestExpiry;

src/token/metadata/types/MetadataRendererTypesV1.sol:
  20:        uint16 referenceSlot;

src/lib/token/ERC721Votes.sol:
 148:        uint8 _v,

src/lib/utils/SafeCast.sol:
   9:        function toUint128(uint256 x) internal pure returns (uint128) {
  15:        function toUint64(uint256 x) internal pure returns (uint64) {
  21:        function toUint48(uint256 x) internal pure returns (uint48) {
  27:        function toUint40(uint256 x) internal pure returns (uint40) {
  33:        function toUint32(uint256 x) internal pure returns (uint32) {
  39:        function toUint16(uint256 x) internal pure returns (uint16) {
  45:        function toUint8(uint256 x) internal pure returns (uint8) {

src/lib/utils/Initializable.sol:
  55:        modifier reinitializer(uint8 _version) {

src/lib/interfaces/IERC721Votes.sol:
  36:        uint64 timestamp;
  37:        uint192 votes;
  72:        uint8 v,

src/auction/types/AuctionTypesV1.sol:
  16:        uint40 duration;
  17:        uint40 timeBuffer;
  18:        uint8 minBidIncrement;
  33:        uint40 startTime;
  34:        uint40 endTime;
```

## `abi.encode()` is less efficient than `abi.encodePacked()`
`abi.encode()` pads its parameters to 32 bytes, regardless of their type. 

In comparison, `abi.encodePacked()` encodes its parameters using the minimal space required by their types. For example, encoding a `uint8` it will use only 1 byte. Thus, `abi.encodePacked()` should be used instead of `abi.encode()` when possible as it saves space, thus reducing gas costs.

_There are **4** instances of this issue:_  
```solidity
src/manager/Manager.sol:
  68:        metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));
  69:        auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));
  70:        treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));
  71:        governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));
```

## `internal` functions only called once can be inlined to save gas
As compared to `internal` functions, a non-inlined function costs **20 to 40 gas** extra because of two extra `JUMP` instructions and additional stack operations needed for function calls. Thus, consider inlining `internal` functions that are only called once in the function that calls them.

_There are **2** instances of this issue:_  
```solidity
src/token/Token.sol:
  71:        function _addFounders(IManager.FounderParams[] calldata _founders) internal {
 130:        function _getNextTokenId(uint256 _tokenId) internal view returns (uint256) {
```

## Multiple accesses of a mapping/array should use a local variable cache
If a mapping/array is read with the same key/index multiple times in a function, it should be cached in a stack variable.

 Caching a mapping's value in a local `storage` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory.

_There are **3** instances of this issue:_  
`timestamps[_proposalId]` in `isReady()`:
```solidity
src/governance/treasury/Treasury.sol:
  89:        return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
```
`tokenRecipient[baseTokenId]` in `_isForFounder()`:
```solidity
src/token/Token.sol:
 182:        if (tokenRecipient[baseTokenId].wallet == address(0)) {
 186:        } else if (block.timestamp < tokenRecipient[baseTokenId].vestExpiry) {
 188:        _mint(tokenRecipient[baseTokenId].wallet, _tokenId);
```
`ipfsData[_item.referenceSlot]` in `_getItemImage()`:
```solidity
src/token/metadata/MetadataRenderer.sol:
 259:        abi.encodePacked(ipfsData[_item.referenceSlot].baseUri, _propertyName, "/", _item.name, ipfsData[_item.referenceSlot].extension)
```
