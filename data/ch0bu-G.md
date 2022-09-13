## 1. Use a more recent version of solidity

- Use a solidity version of at least 0.8.2 to get compiler automatic inlining
- Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
- Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Pausable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/ReentrancyGuard.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IEIP712.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IOwnable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2


## 2. No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < numIterations; ++i) {` should be replaced with for `(uint256 i; i < numIterations; ++i) {`


https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol
```
119		for (uint256 i =  0; i < numNewProperties; ++i) {
133		for (uint256 i =  0; i < numNewItems; ++i) {
189		for (uint256 i =  0; i < numProperties; ++i) {
229		for (uint256 i =  0; i < numProperties; ++i) {
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol
```
162		for (uint256 i =  0; i < numTargets; ++i) {
```

## 3. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

This shows up in most contracts. If it is not explicitly needed to have variables smaller than uint256 consider using uint256 instead.

Some examples:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L33-L34
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L16-L18
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L44-L51

## 4. `abi.encode()` is less efficient than `abi.encodepacked()`

Whenever possible consider using `abi.encodepacked()` to save gas.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol
```
104		return  keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol
```
107		return  keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol
```
69		return  keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));
```

## 5. Using bools for storage incurs overhead

// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Use uint256(1) and uint256(2) for true/false

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/storage/GovernorStorageV1.sol
```
19		mapping(bytes32  =>  mapping(address  =>  bool)) internal hasVoted;
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol
```
38		mapping(address  =>  mapping(address  =>  bool)) internal operatorApprovals;
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/storage/ManagerStorageV1.sol
```
mapping(address  =>  mapping(address  =>  bool)) internal isUpgrade;
```
## 6. Multiplication/division by two should use bit shifting

\<x> * 2 is equivalent to \<x> << 1 and \<x> / 2 is the same as \<x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol
```
95		middle = high - (high - low) /  2;
```

## 7. Not using the named return variables when a function returns, wastes deployment gas

In most of the contracts.

Some examples:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L59
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L69
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L86
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L78
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L190



