## 1. Use recent version of Solidity

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.
[ERC1967Upgrade.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L2)
```
src/lib/proxy/ERC1967Upgrade.sol:2:pragma solidity ^0.8.4;
```
[ERC1967Proxy.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Proxy.sol#L2)
```
src/lib/proxy/ERC1967Proxy.sol:2:pragma solidity ^0.8.4;
```
[UUPS.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol#L2)
```
src/lib/proxy/UUPS.sol:2:pragma solidity ^0.8.4;
```
[ERC721Votes.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L2)
```
src/lib/token/ERC721Votes.sol:2:pragma solidity ^0.8.4;
```
[ERC721.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L2)
```
src/lib/token/ERC721.sol:2:pragma solidity ^0.8.4;
```

-----
## 2. Use `calldata` instead of `memory`

Affected code (around 180 gas to save):

```
src/token/metadata/MetadataRenderer.sol:255:    function _getItemImage(Item memory _item, string memory _propertyName)
src/token/metadata/MetadataRenderer.sol:308:    function _encodeAsJson(bytes memory _jsonBlob)
src/lib/proxy/UUPS.sol:55:    function upgradeToAndCall(address _newImpl, bytes memory _data)
```

## 3. `Internal/Private` functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
src/lib/token/ERC721Votes.sol:179:    function _delegate(address _from, address _to) internal {
```

```
src/lib/token/ERC721Votes.sol:200:    ) internal {

`    function _moveDelegateVotes(
        address _from,
        address _to,
        uint256 _amount
    ) internal {
        unchecked {
`
```

-----
## 4. Using `bool` for storage incurs overhead
Booleans are more expensive than `uint256` or any type that takes up a full word because each write operation emits an extra `SLOAD` to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Consider doing like here: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 . They are using uint256(1) and uint256(2) for true/false to avoid the extra `SLOAD` (100 gas) and the extra `SSTORE` (20000 gas) when changing from `false` to `true`, after having been `true` in the past:

- State mappings using booleans:

```
src/lib/token/ERC721.sol:
38:    mapping(address => mapping(address => bool)) internal operatorApprovals;
src/manager/storage/ManagerStorageV1.sol:
10:    mapping(address => mapping(address => bool)) internal isUpgrade;
src/governance/governor/storage/GovernorStorageV1.sol:
19:    mapping(bytes32 => mapping(address => bool)) internal hasVoted;
```

----
## 5. `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)

Pre-increments and pre-decrements are cheaper.

For a uint256 i variable, the following is true with the Optimizer enabled at 10k:

### Increment:

 `i += 1` is the most expensive form
 `i++`  costs 6 gas less than  `i += 1`
 `++i` costs 5 gas less than `i++` (11 gas less than i += 1)

### Decrement:
`i -= 1` is the most expensive form
 `i--` costs 11 gas less than `i -= 1`
 `--i` costs 5 gas less than `i--` (16 gas less than i -= 1)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name post-increment:
```
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
However, pre-increments (or pre-decrements) return the new value:
```
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```

In the pre-increment case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2.

Affected code:
```
src/token/Token.sol:91:                uint256 founderId = settings.numFounders++;
src/token/Token.sol:154:                tokenId = settings.totalSupply++;
src/governance/governor/Governor.sol:
230:                 keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support,
nonces[_voter]++, _deadline))
```

## 6. Increments/decrements can be unchecked in for-loops

In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

`ethereum/solidity#10695`

Consider wrapping with an unchecked block here (around 25 gas saved per instance):
```
src/governance/treasury/Treasury.sol:162:            for (uint256 i = 0; i < numTargets; ++i)
src/token/metadata/MetadataRenderer.sol:119:            for (uint256 i = 0; i < numNewProperties; ++i) {
src/token/metadata/MetadataRenderer.sol:133:            for (uint256 i = 0; i < numNewItems; ++i) {
src/token/metadata/MetadataRenderer.sol:189:            for (uint256 i = 0; i < numProperties; ++i) {
src/token/metadata/MetadataRenderer.sol:229:            for (uint256 i = 0; i < numProperties; ++i) {
src/token/Token.sol:80:            for (uint256 i; i < numFounders; ++i) {
src/token/Token.sol:108:                for (uint256 j; j < founderPct; ++j) 
src/token/Token.sol:261:            for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];
```
The change would be:
```
- for (uint256 i; i < numIterations; i++) {
+ for (uint256 i; i < numIterations;) {
 // ...  
+   unchecked { ++i; }
}  
```

-----
## 7. It costs more gas to initialize variables with their default value than letting the default value be applied

f a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for `address...`). Explicitly initializing it with its default value is an anti-pattern and wastes gas (around 3 gas per instance).

Affected code:

```
src/governance/treasury/Treasury.sol:162:            for (uint256 i = 0; i < numTargets; ++i)
src/token/metadata/MetadataRenderer.sol:119:            for (uint256 i = 0; i < numNewProperties; ++i) {
src/token/metadata/MetadataRenderer.sol:133:            for (uint256 i = 0; i < numNewItems; ++i) {
src/token/metadata/MetadataRenderer.sol:189:            for (uint256 i = 0; i < numProperties; ++i) {
src/token/metadata/MetadataRenderer.sol:229:            for (uint256 i = 0; i < numProperties; ++i) {
```
Consider removing explicit initializations for default values.

## 8.Using private rather than `public` for constants, saves gas

f needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

```
src/governance/governor/Governor.sol:
27:    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```

----
## 9. `x += y` costs more gas than `x = x + y` for state variables
There are 6 such instances

```
src/governance/governor/Governor.sol:280:                proposal.againstVotes += uint32(weight);
src/governance/governor/Governor.sol:285:                proposal.forVotes += uint32(weight);
src/governance/governor/Governor.sol:290:                proposal.abstainVotes += uint32(weight);
src/token/metadata/MetadataRenderer.sol:140:                    _propertyId += numStoredProperties;
src/token/Token.sol:88:                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
src/token/Token.sol:118:                    (baseTokenId += schedule) % 100;
```

-----
## 10. `> =` costs less gas than `>`

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves `3` gas


```
src/lib/token/ERC721Votes.sol:61:        if (_timestamp >= block.timestamp) revert INVALID_TIMESTAMP();
src/governance/treasury/Treasury.sol:89:        return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
src/auction/Auction.sol:98:        if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();

```

----
## 11.Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas
```
src/token/metadata/MetadataRenderer.sol:52:        if (msg.sender != address(manager)) revert ONLY_MANAGER();
src/token/metadata/MetadataRenderer.sol:173:        if (msg.sender != settings.token) revert ONLY_TOKEN();
src/token/metadata/MetadataRenderer.sol:222:        if (numProperties == 0) revert TOKEN_NOT_MINTED(_tokenId);
src/token/metadata/MetadataRenderer.sol:377:        if (!manager.isRegisteredUpgrade(_getImplementation(), _impl)) revert INVALID_UPGRADE(_impl);
```
```
src/token/Token.sol:50:        if (msg.sender != address(manager)) revert ONLY_MANAGER();
src/token/Token.sol:88:                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
src/token/Token.sol:148:        if (msg.sender != minter) revert ONLY_AUCTION();
src/token/Token.sol:172:        if (!settings.metadataRenderer.onMinted(_tokenId)) revert NO_METADATA_GENERATED();
src/token/Token.sol:209:        if (msg.sender != settings.auction) revert ONLY_AUCTION();
src/token/Token.sol:307:        if (msg.sender != owner()) revert ONLY_OWNER();
```
```
src/auction/Auction.sol:62:        if (msg.sender != address(manager)) revert ONLY_MANAGER();
src/auction/Auction.sol:95:        if (_auction.tokenId != _tokenId) revert INVALID_TOKEN_ID();
src/auction/Auction.sol:98:        if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();
src/auction/Auction.sol:106:            if (msg.value < settings.reservePrice) revert RESERVE_PRICE_NOT_MET();
src/auction/Auction.sol:123:            if (msg.value < minBid) revert MINIMUM_BID_NOT_MET();
src/auction/Auction.sol:172:        if (auction.settled) revert AUCTION_SETTLED();
src/auction/Auction.sol:175:        if (_auction.startTime == 0) revert AUCTION_NOT_STARTED();
src/auction/Auction.sol:178:        if (block.timestamp < _auction.endTime) revert AUCTION_ACTIVE();
src/auction/Auction.sol:346:        if (address(this).balance < _amount) revert INSOLVENT();
src/auction/Auction.sol:376:        if (!manager.isRegisteredUpgrade(_getImplementation(), _newImpl)) revert INVALID_UPGRADE(_newImpl);
```
```
src/governance/governor/Governor.sol:255:        if (state(_proposalId) != ProposalState.Active) revert VOTING_NOT_STARTED();
src/governance/governor/Governor.sol:258:        if (hasVoted[_proposalId][_voter]) revert ALREADY_VOTED();
src/governance/governor/Governor.sol:261:        if (_support > 2) revert INVALID_VOTE();
src/governance/governor/Governor.sol:272:        // Cannot realistically underflow and `getVotes` would revert
src/governance/governor/Governor.sol:307:        if (state(_proposalId) != ProposalState.Succeeded) revert PROPOSAL_UNSUCCESSFUL();
src/governance/governor/Governor.sol:334:        if (state(proposalId) != ProposalState.Queued) revert PROPOSAL_NOT_QUEUED(proposalId);
src/governance/governor/Governor.sol:355:        if (state(_proposalId) == ProposalState.Executed) revert PROPOSAL_ALREADY_EXECUTED();
src/governance/governor/Governor.sol:360:        // Cannot realistically underflow and `getVotes` would revert
src/governance/governor/Governor.sol:364:                revert INVALID_CANCEL();
src/governance/governor/Governor.sol:387:        if (msg.sender != settings.vetoer) revert ONLY_VETOER();
src/governance/governor/Governor.sol:390:        if (state(_proposalId) == ProposalState.Executed) revert PROPOSAL_ALREADY_EXECUTED();
src/governance/governor/Governor.sol:418:        if (proposal.voteStart == 0) revert PROPOSAL_DOES_NOT_EXIST();
src/governance/governor/Governor.sol:597:        if (_newVetoer == address(0)) revert ADDRESS_ZERO();
src/governance/governor/Governor.sol:620:        if (!manager.isRegisteredUpgrade(_getImplementation(), _newImpl)) revert INVALID_UPGRADE(_newImpl);
src/governance/governor/Governor.sol:67:        if (msg.sender != address(manager)) revert ONLY_MANAGER();
src/governance/governor/Governor.sol:70:        if (_treasury == address(0)) revert ADDRESS_ZERO();
src/governance/governor/Governor.sol:71:        if (_token == address(0)) revert ADDRESS_ZERO();
src/governance/governor/Governor.sol:125:        // Cannot realistically underflow and `getVotes` would revert
src/governance/governor/Governor.sol:128:            if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
src/governance/governor/Governor.sol:135:        if (numTargets == 0) revert PROPOSAL_TARGET_MISSING();
src/governance/governor/Governor.sol:138:        if (numTargets != _values.length) revert PROPOSAL_LENGTH_MISMATCH();
src/governance/governor/Governor.sol:139:        if (numTargets != _calldatas.length) revert PROPOSAL_LENGTH_MISMATCH();
src/governance/governor/Governor.sol:151:        if (proposal.voteStart != 0) revert PROPOSAL_EXISTS(proposalId);
src/governance/governor/Governor.sol:218:        if (block.timestamp > _deadline) revert EXPIRED_SIGNATURE();
src/governance/governor/Governor.sol:239:        if (recoveredAddress == address(0) || recoveredAddress != _voter) revert INVALID_SIGNATURE();
```