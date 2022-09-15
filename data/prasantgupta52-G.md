# 1. Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value
### Links to github files
[ERC1967Proxy.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Proxy.sol#L2)
[UUPS.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L2)
[ERC1967Upgrade.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L2)
[ERC721Votes.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L2)
[ERC721.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L2)
### Instances
```
src/lib/proxy/ERC1967Proxy.sol:2:pragma solidity ^0.8.4;
src/lib/proxy/UUPS.sol:2:pragma solidity ^0.8.4;
src/lib/proxy/ERC1967Upgrade.sol:2:pragma solidity ^0.8.4;
src/lib/token/ERC721Votes.sol:2:pragma solidity ^0.8.4;
src/lib/token/ERC721.sol:2:pragma solidity ^0.8.4;
```

----
# 2. Inline a modifier that’s only used once
### Description
As notDelegated() is only used once in this contract (in function proxiableUUID()), it should get inlined to save gas:
### Links to github files
[UUPS.sol:L32](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L32)
### Instances
```
src/lib/proxy/UUPS.sol:32:    modifier notDelegated() {
```
### Links to github files
[UUPS.sol:L61](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L61)
### Instances where modifiers are used only once
```
src/lib/proxy/UUPS.sol:61:    function proxiableUUID() external view notDelegated returns (bytes32) {
```
----
# 3. Multiple address mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~**42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - **30 gas**) and that calculation's associated stack operations.

*There are 2 instances of this issue:*
### Links to github files
[ERC721Votes.sol:L29](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L29)
[ERC721Votes.sol:L33](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L33)
[ERC721Votes.sol:L37](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L37)

### Instances 1
```
src/lib/token/ERC721Votes.sol:29:    mapping(address => address) internal delegation;
src/lib/token/ERC721Votes.sol:33:    mapping(address => uint256) internal numCheckpoints;
src/lib/token/ERC721Votes.sol:37:    mapping(address => mapping(uint256 => Checkpoint)) internal checkpoints;
```
### Links to github files
[ERC721.sol:L26](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L26)
[ERC721.sol:L30](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L30)
[ERC721.sol:L34](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L34)
[ERC721.sol:L38](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L38)
### Instances 2
```
src/lib/token/ERC721.sol:26:    mapping(uint256 => address) internal owners;
src/lib/token/ERC721.sol:30:    mapping(address => uint256) internal balances;
src/lib/token/ERC721.sol:34:    mapping(uint256 => address) internal tokenApprovals;
src/lib/token/ERC721.sol:38:    mapping(address => mapping(address => bool)) internal operatorApprovals;
```
----
# 4. Use `abi.encodePacked()` not `abi.encode()`
Changing `abi.encode` to `abi.encodePacked` can save gas. `abi.encode` pads extra null bytes at the end of the call data which is normally unnecessary. In general, `abi.encodePacked` is more gas-efficient.
### Links to github files
[MetadataRenderer.sol:L251](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L251)
[Manager.sol:L68](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L68)
[Manager.sol:L69](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L69)
[Manager.sol:L70](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L70)
[Manager.sol:L71](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L71)
[ERC721Votes.sol:L162](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L162)
[Governor.sol:L104](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L104)
[Governor.sol:L230](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L230)
[Treasury.sol:L107](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L107)
### Instances
```
src/token/metadata/MetadataRenderer.sol:251:        return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
src/manager/Manager.sol:68:        metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));
src/manager/Manager.sol:69:        auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));
src/manager/Manager.sol:70:        treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));
src/manager/Manager.sol:71:        governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));
src/lib/token/ERC721Votes.sol:162:                abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
src/governance/governor/Governor.sol:104:        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
src/governance/governor/Governor.sol:230:                    keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
src/governance/treasury/Treasury.sol:107:        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
```

### Recommended Mitigation Steps
You should change **abi.encode** to **abi.encodePacked**

----
# 5. Using private rather than public for constants / immutable, saves gas
### Description
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves **3406-3606** gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table
### Links to github files
[Governor.sol:L27](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L27)
[Manager.sol:L25](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L25)
[Manager.sol:L28](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L28)
[Manager.sol:L31](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L31)
[Manager.sol:L34](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L34)
[Manager.sol:L37](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L37)

### Instances
```
src/governance/governor/Governor.sol:27:    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
src/manager/Manager.sol:25:    address public immutable tokenImpl;
src/manager/Manager.sol:28:    address public immutable metadataImpl;
src/manager/Manager.sol:31:    address public immutable auctionImpl;
src/manager/Manager.sol:34:    address public immutable treasuryImpl;
src/manager/Manager.sol:37:    address public immutable governorImpl;
```
----