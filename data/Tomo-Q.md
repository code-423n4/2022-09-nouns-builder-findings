# QA Report

## ✅ N-1: Consider addings checks for signature malleability

### 📝 Description

You should consider addings checks for signature malleability

### 💡 Recommendation

Use OpenZeppelin's `ECDSA` contract rather than calling `ecrecover()` directly

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L236` [address recoveredAddress = ecrecover(digest, \_v, \_r, \_s);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L236)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L167` [address recoveredAddress = ecrecover(digest, \_v, \_r, \_s);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L167)

## ✅ N-2: Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

### 📝 Description

Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

### 💡 Recommendation

You should use immutable instead of constant

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27` [bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21` [bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21)

`2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L19` [bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L19)

## ✅ N-3: No use of two-phase ownership transfers

### 📝 Description

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call `accept*()` to become the new owner. This prevents passing the ownership to an account that is unable to use it.

### 💡 Recommendation

Consider implementing a two step process where the admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of admin to fully succeed. This ensures the nominated EOA account is a valid and active account.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L5` [import { Ownable } from "../lib/utils/Ownable.sol";](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L5)

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L22` [contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionStorageV1 {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L22)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L5` [import { Ownable } from "../../lib/utils/Ownable.sol";](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L5)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L21` [contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L21)

`2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L19` [contract Treasury is ITreasury, UUPS, Ownable, TreasuryStorageV1 {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L19)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L19` [contract Manager is IManager, UUPS, Ownable, ManagerStorageV1 {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L19)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L19` [contract MetadataRenderer is IPropertyIPFSMetadataRenderer, UUPS, Ownable, MetadataRendererStorageV1 {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L19)

## ✅ N-4: Use a more recent version of solidity

### 📝 Description

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked (<bytes>, <bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked (<str>, <str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

### 💡 Recommendation

Use more recent version of solidity.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/treasury/types/TreasuryTypesV1.sol#L2` [pragma solidity 0.8.15;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/types/TreasuryTypesV1.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IEIP712.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IEIP712.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IOwnable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IOwnable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/Pausable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Pausable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/ReentrancyGuard.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/ReentrancyGuard.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol#L2` [pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol#L2)

## ✅ N-5: Use `string.concat()` or`bytes.concat()`

### 📝 Description

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)Solidity version 0.8.12 introduces `string.concat()`(vs `abi.encodePacked(<str>,<str>)`)

### 💡 Recommendation

Use concat instead of abi.encodePacked

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L227` [abi.encodePacked(](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L227)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162` [abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, \_from, \_to, nonces[\_from]++, \_deadline)))](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L68` [metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(\_metadataImpl, "")));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L68)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L69` [auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(\_auctionImpl, "")));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L69)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L70` [treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(\_treasuryImpl, "")));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L70)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L71` [governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(\_governorImpl, "")));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L71)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L167` [metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L167)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L168` [auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L168)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L169` [treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L169)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L170` [governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L170)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L208` [queryString = abi.encodePacked(](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L208)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L243` [aryAttributes = abi.encodePacked(aryAttributes, '"', property.name, '": "', item.name, '"', isLast ? "" : ",");](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L243)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L244` [queryString = abi.encodePacked(queryString, "&images=", \_getItemImage(item, property.name));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L244)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L259` [abi.encodePacked(ipfsData[\_item.referenceSlot].baseUri, \_propertyName, "/", \_item.name, ipfsData[\_item.referenceSlot].extension)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L259)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L272` [abi.encodePacked(](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L272)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L290` [abi.encodePacked(](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L290)

```2022-09-nouns-builder/blob/main/src/toke

```
