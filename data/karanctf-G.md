## \[G-1\] No need to explicitly initialize variables with default values


```solidity
governance/treasury/Treasury.sol:162 => for (uint256 i = 0; i < numTargets; ++i) {
token/metadata/MetadataRenderer.sol:119 => for (uint256 i = 0; i < numNewProperties; ++i) {
token/metadata/MetadataRenderer.sol:133 => for (uint256 i = 0; i < numNewItems; ++i) {
token/metadata/MetadataRenderer.sol:189 => for (uint256 i = 0; i < numProperties; ++i) {
token/metadata/MetadataRenderer.sol:229 => for (uint256 i = 0; i < numProperties; ++i) {
```



## \[G-2\] Use != 0 instead of > 0 for Unsigned Integer Comparison



```solidity
lib/proxy/ERC1967Upgrade.sol:61 => if (_data.length > 0 || _forceCall) {
lib/token/ERC721Votes.sol:203 => if (_from != _to && _amount > 0) {
lib/utils/Address.sol:50 => if (_returndata.length > 0) {
```



## \[G-3\] Use immutable for OpenZeppelin AccessControl's Roles Declarations



```
governance/governor/Governor.sol:27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
governance/governor/Governor.sol:104 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
governance/governor/Governor.sol:142 => bytes32 descriptionHash = keccak256(bytes(_description));
governance/governor/Governor.sol:226 => digest = keccak256(
governance/governor/Governor.sol:230 => keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
governance/treasury/Treasury.sol:107 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
lib/proxy/ERC1967Upgrade.sol:20 => /// @dev bytes32(uint256(keccak256('eip1967.proxy.rollback')) - 1)
lib/proxy/ERC1967Upgrade.sol:23 => /// @dev bytes32(uint256(keccak256('eip1967.proxy.implementation')) - 1)
lib/token/ERC721Votes.sol:21 => bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
lib/token/ERC721Votes.sol:161 => digest = keccak256(
lib/token/ERC721Votes.sol:162 => abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
lib/utils/EIP712.sol:19 => bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
lib/utils/EIP712.sol:49 => HASHED_NAME = keccak256(bytes(_name));
lib/utils/EIP712.sol:50 => HASHED_VERSION = keccak256(bytes(_version));
lib/utils/EIP712.sol:69 => return keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));
manager/Manager.sol:68 => metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));
manager/Manager.sol:69 => auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));
manager/Manager.sol:70 => treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));
manager/Manager.sol:71 => governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));
manager/Manager.sol:167 => metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
manager/Manager.sol:168 => auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
manager/Manager.sol:169 => treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
manager/Manager.sol:170 => governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
token/metadata/MetadataRenderer.sol:251 => return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
```



## \[G-4\] Long Revert Strings


```solidity
governance/governor/Governor.sol:27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
governance/treasury/Treasury.sol:6 => import { ERC721TokenReceiver, ERC1155TokenReceiver } from "../../lib/utils/TokenReceiver.sol";
lib/interfaces/IUUPS.sol:4 => import { IERC1822Proxiable } from "@openzeppelin/contracts/interfaces/draft-IERC1822.sol";
lib/interfaces/IWETH.sol:4 => import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
lib/proxy/ERC1967Proxy.sol:4 => import { Proxy } from "@openzeppelin/contracts/proxy/Proxy.sol";
lib/proxy/ERC1967Proxy.sol:6 => import { IERC1967Upgrade } from "../interfaces/IERC1967Upgrade.sol";
lib/proxy/ERC1967Upgrade.sol:4 => import { IERC1822Proxiable } from "@openzeppelin/contracts/interfaces/draft-IERC1822.sol";
lib/proxy/ERC1967Upgrade.sol:5 => import { StorageSlot } from "@openzeppelin/contracts/utils/StorageSlot.sol";
lib/proxy/ERC1967Upgrade.sol:7 => import { IERC1967Upgrade } from "../interfaces/IERC1967Upgrade.sol";
lib/token/ERC721Votes.sol:21 => bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
lib/utils/EIP712.sol:19 => bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
manager/Manager.sol:11 => import { IBaseMetadata } from "../token/metadata/interfaces/IBaseMetadata.sol";
manager/Manager.sol:13 => import { ITreasury } from "../governance/treasury/ITreasury.sol";
manager/Manager.sol:14 => import { IGovernor } from "../governance/governor/IGovernor.sol";
token/IToken.sol:5 => import { IERC721Votes } from "../lib/interfaces/IERC721Votes.sol";
token/Token.sol:10 => import { IBaseMetadata } from "./metadata/interfaces/IBaseMetadata.sol";
token/metadata/MetadataRenderer.sol:4 => import { Base64 } from "@openzeppelin/contracts/utils/Base64.sol";
token/metadata/MetadataRenderer.sol:5 => import { Strings } from "@openzeppelin/contracts/utils/Strings.sol";
token/metadata/MetadataRenderer.sol:6 => import { LibUintToString } from "sol2string/contracts/LibUintToString.sol";
token/metadata/MetadataRenderer.sol:12 => import { MetadataRendererStorageV1 } from "./storage/MetadataRendererStorageV1.sol";
token/metadata/MetadataRenderer.sol:13 => import { IPropertyIPFSMetadataRenderer } from "./interfaces/IPropertyIPFSMetadataRenderer.sol";
token/metadata/MetadataRenderer.sol:243 => aryAttributes = abi.encodePacked(aryAttributes, '"', property.name, '": "', item.name, '"', isLast ? "" : ",");
token/metadata/interfaces/IBaseMetadata.sol:4 => import { IUUPS } from "../../../lib/interfaces/IUUPS.sol";
token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol:4 => import { MetadataRendererTypesV1 } from "../types/MetadataRendererTypesV1.sol";
token/metadata/storage/MetadataRendererStorageV1.sol:4 => import { MetadataRendererTypesV1 } from "../types/MetadataRendererTypesV1.sol";
token/types/TokenTypesV1.sol:4 => import { IBaseMetadata } from "../metadata/interfaces/IBaseMetadata.sol";
```



## \[G-5\] Use bit shift instead of Division/Multiplication if possible


```solidity
lib/token/ERC721Votes.sol:95 => middle = high - (high - low) / 2;
```
