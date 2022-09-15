## 1. Pre-increment costs less gas as compared to Post-increment :

++i costs less gas as compared to i++ for unsigned integer, as per-increment is cheaper(its about 5 gas per iteration cheaper)

i++ increments i and returns initial value of i. Which means

```
uint i =  1;
i++; // ==1 but i ==2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // ==2 and i ==2 , no need for temporary variable here
```

In the first case, the compiler has create a temporary variable (when used) for returning 1 instead of 2.

### Instances:
[ERC721Votes.sol:L207](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L207)
[ERC721Votes.sol:L222](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L222)
[Token.sol:L91](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L91)
[Token.sol:L154](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L154)
```
lib/token/ERC721Votes.sol:207:                    uint256 nCheckpoints = numCheckpoints[_from]++;
lib/token/ERC721Votes.sol:222:                    uint256 nCheckpoints = numCheckpoints[_to]++;
token/Token.sol:91:                uint256 founderId = settings.numFounders++;
token/Token.sol:154:                tokenId = settings.totalSupply++;
``` 

### Recommendations:
Change post-increment to pre-increment.

-----


## 2.Use Shift Right/Left instead of Division/Multiplication 2X

A division by 2 can be calculated by shifting one to the right.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity’s division operation also includes a division-by-0 prevention which is bypassed using shifting.

I suggest replacing  `/ 2` with  `>> 1` here:

### Instances
[ERC721Votes.sol:L95](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L95)
```
lib/token/ERC721Votes.sol:95:                middle = high - (high - low) / 2;
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-32-shift-right-instead-of-dividing-by-2](https://code4rena.com/reports/2022-04-badger-citadel/#g-32-shift-right-instead-of-dividing-by-2)


-----

## 3. Strict inequalities (>) are more expensive than non-strict ones (>=)

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas. I suggest using >= instead of > to avoid some opcodes here:

### Instances:
[Treasury.sol:L76](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L76)
```
governance/treasury/Treasury.sol:76:            return block.timestamp > (timestamps[_proposalId] + settings.gracePeriod);
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than](https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than)


-----

## 4. x += y costs more gas than x = x + y for state variables

### Instances:
[Governor.sol:L280](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L280)
[Governor.sol:L285](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L285)
[Governor.sol:L290](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L290)
[MetadataRenderer.sol:L140](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L140)
[Token.sol:L88](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L88)
[Token.sol:L118](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L118)
```
governance/governor/Governor.sol:280:                proposal.againstVotes += uint32(weight);
governance/governor/Governor.sol:285:                proposal.forVotes += uint32(weight);
governance/governor/Governor.sol:290:                proposal.abstainVotes += uint32(weight);
token/metadata/MetadataRenderer.sol:140:                    _propertyId += numStoredProperties;
token/Token.sol:88:                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
token/Token.sol:118:                    (baseTokenId += schedule) % 100;
``` 
### References:

[https://github.com/code-423n4/2022-05-backd-findings/issues/108](https://github.com/code-423n4/2022-05-backd-findings/issues/108)


-----


## 5. Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

This results in the keccak operation being performed whenever the variable is used, increasing gas costs relative to just storing the output hash. Changing to immutable will only perform hashing on contract deployment which will save gas.

### Instances:
[ERC721Votes.sol:L21](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L21)
[Governor.sol:L27](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L27)
```
lib/token/ERC721Votes.sol:21:    bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
governance/governor/Governor.sol:27:    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
``` 
### References:

[https://github.com/code-423n4/2021-10-slingshot-findings/issues/3](https://github.com/code-423n4/2021-10-slingshot-findings/issues/3)


-----

## 6. abi.encode() is less efficient than abi.encodepacked()

Use abi.encodepacked() instead of abi.encode()

### Instances:
[Treasury.sol:L107](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L107)
[Governor.sol:L104](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L104)
[Governor.sol:L230](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L230)
[MetadataRenderer.sol:L251](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L251)
[Treasury.sol:L107](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L107)
[Governor.sol:L104](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L104)
[Governor.sol:L230](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L230)
[MetadataRenderer.sol:L251](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L251)
```
governance/treasury/Treasury.sol:107:        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
governance/governor/Governor.sol:104:        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
governance/governor/Governor.sol:230:                    keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
token/metadata/MetadataRenderer.sol:251:        return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
governance/treasury/Treasury.sol:107:        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
governance/governor/Governor.sol:104:        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
governance/governor/Governor.sol:230:                    keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
token/metadata/MetadataRenderer.sol:251:        return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
``` 
### Reference:

[https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked](https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked)


-----


## 7. Use calldata instead of memory in external functions

Use calldata instead of memory in external functions to save gas.

### Instances:
[UUPS.sol:L55](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol#L55)
[IPropertyIPFSMetadataRenderer.sol:L76](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L76)
[IPropertyIPFSMetadataRenderer.sol:L80](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L80)
[IPropertyIPFSMetadataRenderer.sol:L84](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L84)
[MetadataRenderer.sol:L347](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L347)
[MetadataRenderer.sol:L355](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L355)
[MetadataRenderer.sol:L363](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L363)
```
lib/proxy/UUPS.sol:55:    function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {
token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol:76:    function updateContractImage(string memory newContractImage) external;
token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol:80:    function updateRendererBase(string memory newRendererBase) external;
token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol:84:    function updateDescription(string memory newDescription) external;
token/metadata/MetadataRenderer.sol:347:    function updateContractImage(string memory _newContractImage) external onlyOwner {
token/metadata/MetadataRenderer.sol:355:    function updateRendererBase(string memory _newRendererBase) external onlyOwner {
token/metadata/MetadataRenderer.sol:363:    function updateDescription(string memory _newDescription) external onlyOwner {
``` 
### Reference:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-12-stakedcitadeldepositall-use-calldata-instead-of-memory](https://code4rena.com/reports/2022-04-badger-citadel/#g-12-stakedcitadeldepositall-use-calldata-instead-of-memory)


-----

## 8. Using bools for storage incurs overhead

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

[Refer Here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Instances:
[Auction.sol:L136](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L136)
[Auction.sol:L349](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L349)
[AuctionTypesV1.sol:L35](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol#L35)
[ManagerStorageV1.sol:L10](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/storage/ManagerStorageV1.sol#L10)
[ERC721.sol:L38](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L38)
[GovernorStorageV1.sol:L19](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/storage/GovernorStorageV1.sol#L19)
[GovernorTypesV1.sol:L52](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L52)
[GovernorTypesV1.sol:L53](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L53)
[GovernorTypesV1.sol:L54](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L54)
[MetadataRendererTypesV1.sol:L11](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/types/MetadataRendererTypesV1.sol#L11)
[MetadataRenderer.sol:L231](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L231)
```
auction/Auction.sol:136:        bool extend;
auction/Auction.sol:349:        bool success;
auction/types/AuctionTypesV1.sol:35:        bool settled;
manager/storage/ManagerStorageV1.sol:10:    mapping(address => mapping(address => bool)) internal isUpgrade;
lib/token/ERC721.sol:38:    mapping(address => mapping(address => bool)) internal operatorApprovals;
governance/governor/storage/GovernorStorageV1.sol:19:    mapping(bytes32 => mapping(address => bool)) internal hasVoted;
governance/governor/types/GovernorTypesV1.sol:52:        bool executed;
governance/governor/types/GovernorTypesV1.sol:53:        bool canceled;
governance/governor/types/GovernorTypesV1.sol:54:        bool vetoed;
token/metadata/types/MetadataRendererTypesV1.sol:11:        bool isNewProperty;
token/metadata/MetadataRenderer.sol:231:                bool isLast = i == lastProperty;
``` 
### References:

[https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead](https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead)


-----

##  9. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed

### Instances:
[AuctionTypesV1.sol:L18](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol#L18)
[ERC721Votes.sol:L148](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L148)
[Governor.sol:L213](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L213)
[IGovernor.sol:L181](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L181)
[GovernorTypesV1.sol:L21](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L21)
[GovernorTypesV1.sol:L22](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L22)
[MetadataRendererTypesV1.sol:L20](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/types/MetadataRendererTypesV1.sol#L20)
[TokenTypesV1.sol:L20](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol#L20)
[TokenTypesV1.sol:L21](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol#L21)
[TokenTypesV1.sol:L30](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/types/TokenTypesV1.sol#L30)
```
auction/types/AuctionTypesV1.sol:18:        uint8 minBidIncrement;
lib/token/ERC721Votes.sol:148:        uint8 _v,
governance/governor/Governor.sol:213:        uint8 _v,
governance/governor/IGovernor.sol:181:        uint8 v,
governance/governor/types/GovernorTypesV1.sol:21:        uint16 proposalThresholdBps;
governance/governor/types/GovernorTypesV1.sol:22:        uint16 quorumThresholdBps;
token/metadata/types/MetadataRendererTypesV1.sol:20:        uint16 referenceSlot;
token/types/TokenTypesV1.sol:20:        uint8 numFounders;
token/types/TokenTypesV1.sol:21:        uint8 totalOwnership;
token/types/TokenTypesV1.sol:30:        uint8 ownershipPct;
``` 
### References:

[https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead](https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)


-----

## 10. Use a more recent version of solidity

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

### Instances:
[ERC1967Upgrade.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L2)
[ERC1967Proxy.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Proxy.sol#L2)
[UUPS.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol#L2)
[ERC721Votes.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L2)
[ERC721.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L2)
```
lib/proxy/ERC1967Upgrade.sol:2:pragma solidity ^0.8.4;
lib/proxy/ERC1967Proxy.sol:2:pragma solidity ^0.8.4;
lib/proxy/UUPS.sol:2:pragma solidity ^0.8.4;
lib/token/ERC721Votes.sol:2:pragma solidity ^0.8.4;
lib/token/ERC721.sol:2:pragma solidity ^0.8.4;
``` 
### References:

[https://code4rena.com/reports/2022-06-notional-coop/#10-use-a-more-recent-version-of-solidity](https://code4rena.com/reports/2022-06-notional-coop/#10-use-a-more-recent-version-of-solidity)
