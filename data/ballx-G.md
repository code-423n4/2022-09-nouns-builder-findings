
## Files analyzed
- src/auction/Auction.sol
- src/auction/IAuction.sol
- src/auction/storage/AuctionStorageV1.sol
- src/auction/types/AuctionTypesV1.sol
- src/governance/governor/Governor.sol
- src/governance/governor/IGovernor.sol
- src/governance/governor/storage/GovernorStorageV1.sol
- src/governance/governor/types/GovernorTypesV1.sol
- src/governance/treasury/ITreasury.sol
- src/governance/treasury/Treasury.sol
- src/governance/treasury/storage/TreasuryStorageV1.sol
- src/governance/treasury/types/TreasuryTypesV1.sol
- src/lib/interfaces/IEIP712.sol
- src/lib/interfaces/IERC1967Upgrade.sol
- src/lib/interfaces/IERC721.sol
- src/lib/interfaces/IERC721Votes.sol
- src/lib/interfaces/IInitializable.sol
- src/lib/interfaces/IOwnable.sol
- src/lib/interfaces/IPausable.sol
- src/lib/interfaces/IUUPS.sol
- src/lib/interfaces/IWETH.sol
- src/lib/proxy/ERC1967Proxy.sol
- src/lib/proxy/ERC1967Upgrade.sol
- src/lib/proxy/UUPS.sol
- src/lib/token/ERC721.sol
- src/lib/token/ERC721Votes.sol
- src/lib/utils/Address.sol
- src/lib/utils/EIP712.sol
- src/lib/utils/Initializable.sol
- src/lib/utils/Ownable.sol
- src/lib/utils/Pausable.sol
- src/lib/utils/ReentrancyGuard.sol
- src/lib/utils/SafeCast.sol
- src/lib/utils/TokenReceiver.sol
- src/manager/IManager.sol
- src/manager/Manager.sol
- src/manager/storage/ManagerStorageV1.sol
- src/token/IToken.sol
- src/token/Token.sol
- src/token/metadata/MetadataRenderer.sol
- src/token/metadata/interfaces/IBaseMetadata.sol
- src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol
- src/token/metadata/storage/MetadataRendererStorageV1.sol
- src/token/metadata/types/MetadataRendererTypesV1.sol
- src/token/storage/TokenStorageV1.sol
- src/token/types/TokenTypesV1.sol

## Issues found

### Don't Initialize Variables with Default Value

#### Impact
Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
src/governance/treasury/Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {
src/token/metadata/MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {
src/token/metadata/MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {
src/token/metadata/MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {
src/token/metadata/MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
src/governance/governor/Governor.sol::132 => uint256 numTargets = _targets.length;
src/governance/governor/Governor.sol::138 => if (numTargets != _values.length) revert PROPOSAL_LENGTH_MISMATCH();
src/governance/governor/Governor.sol::139 => if (numTargets != _calldatas.length) revert PROPOSAL_LENGTH_MISMATCH();
src/governance/treasury/Treasury.sol::157 => uint256 numTargets = _targets.length;
src/lib/proxy/ERC1967Upgrade.sol::61 => if (_data.length > 0 || _forceCall) {
src/lib/utils/Address.sol::50 => if (_returndata.length > 0) {
src/token/Token.sol::73 => uint256 numFounders = _founders.length;
src/token/metadata/MetadataRenderer.sol::78 => return properties.length;
src/token/metadata/MetadataRenderer.sol::84 => return properties[_propertyId].items.length;
src/token/metadata/MetadataRenderer.sol::97 => uint256 dataLength = ipfsData.length;
src/token/metadata/MetadataRenderer.sol::109 => uint256 numStoredProperties = properties.length;
src/token/metadata/MetadataRenderer.sol::112 => uint256 numNewProperties = _names.length;
src/token/metadata/MetadataRenderer.sol::115 => uint256 numNewItems = _items.length;
src/token/metadata/MetadataRenderer.sol::150 => // Cannot underflow as the items array length is ensured to be at least 1
src/token/metadata/MetadataRenderer.sol::151 => uint256 newItemIndex = items.length - 1;
src/token/metadata/MetadataRenderer.sol::182 => uint256 numProperties = properties.length;
src/token/metadata/MetadataRenderer.sol::191 => uint256 numItems = properties[i].items.length;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
src/lib/proxy/ERC1967Upgrade.sol::61 => if (_data.length > 0 || _forceCall) {
src/lib/token/ERC721Votes.sol::203 => if (_from != _to && _amount > 0) {
src/lib/utils/Address.sol::50 => if (_returndata.length > 0) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
src/governance/governor/Governor.sol::27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
src/governance/governor/Governor.sol::104 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
src/governance/governor/Governor.sol::142 => bytes32 descriptionHash = keccak256(bytes(_description));
src/governance/governor/Governor.sol::226 => digest = keccak256(
src/governance/governor/Governor.sol::230 => keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
src/governance/treasury/Treasury.sol::107 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
src/lib/proxy/ERC1967Upgrade.sol::20 => /// @dev bytes32(uint256(keccak256('eip1967.proxy.rollback')) - 1)
src/lib/proxy/ERC1967Upgrade.sol::23 => /// @dev bytes32(uint256(keccak256('eip1967.proxy.implementation')) - 1)
src/lib/token/ERC721Votes.sol::21 => bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
src/lib/token/ERC721Votes.sol::161 => digest = keccak256(
src/lib/token/ERC721Votes.sol::162 => abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
src/lib/utils/EIP712.sol::19 => bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
src/lib/utils/EIP712.sol::49 => HASHED_NAME = keccak256(bytes(_name));
src/lib/utils/EIP712.sol::50 => HASHED_VERSION = keccak256(bytes(_version));
src/lib/utils/EIP712.sol::69 => return keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));
src/manager/Manager.sol::68 => metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));
src/manager/Manager.sol::69 => auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));
src/manager/Manager.sol::70 => treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));
src/manager/Manager.sol::71 => governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));
src/manager/Manager.sol::167 => metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
src/manager/Manager.sol::168 => auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
src/manager/Manager.sol::169 => treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
src/manager/Manager.sol::170 => governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
src/token/metadata/MetadataRenderer.sol::251 => return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
src/governance/governor/Governor.sol::27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
src/governance/treasury/Treasury.sol::6 => import { ERC721TokenReceiver, ERC1155TokenReceiver } from "../../lib/utils/TokenReceiver.sol";
src/lib/interfaces/IUUPS.sol::4 => import { IERC1822Proxiable } from "@openzeppelin/contracts/interfaces/draft-IERC1822.sol";
src/lib/interfaces/IWETH.sol::4 => import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
src/lib/proxy/ERC1967Proxy.sol::4 => import { Proxy } from "@openzeppelin/contracts/proxy/Proxy.sol";
src/lib/proxy/ERC1967Proxy.sol::6 => import { IERC1967Upgrade } from "../interfaces/IERC1967Upgrade.sol";
src/lib/proxy/ERC1967Upgrade.sol::4 => import { IERC1822Proxiable } from "@openzeppelin/contracts/interfaces/draft-IERC1822.sol";
src/lib/proxy/ERC1967Upgrade.sol::5 => import { StorageSlot } from "@openzeppelin/contracts/utils/StorageSlot.sol";
src/lib/proxy/ERC1967Upgrade.sol::7 => import { IERC1967Upgrade } from "../interfaces/IERC1967Upgrade.sol";
src/lib/token/ERC721Votes.sol::21 => bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
src/lib/utils/EIP712.sol::19 => bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
src/manager/Manager.sol::11 => import { IBaseMetadata } from "../token/metadata/interfaces/IBaseMetadata.sol";
src/manager/Manager.sol::13 => import { ITreasury } from "../governance/treasury/ITreasury.sol";
src/manager/Manager.sol::14 => import { IGovernor } from "../governance/governor/IGovernor.sol";
src/token/IToken.sol::5 => import { IERC721Votes } from "../lib/interfaces/IERC721Votes.sol";
src/token/Token.sol::10 => import { IBaseMetadata } from "./metadata/interfaces/IBaseMetadata.sol";
src/token/metadata/MetadataRenderer.sol::4 => import { Base64 } from "@openzeppelin/contracts/utils/Base64.sol";
src/token/metadata/MetadataRenderer.sol::5 => import { Strings } from "@openzeppelin/contracts/utils/Strings.sol";
src/token/metadata/MetadataRenderer.sol::6 => import { LibUintToString } from "sol2string/contracts/LibUintToString.sol";
src/token/metadata/MetadataRenderer.sol::12 => import { MetadataRendererStorageV1 } from "./storage/MetadataRendererStorageV1.sol";
src/token/metadata/MetadataRenderer.sol::13 => import { IPropertyIPFSMetadataRenderer } from "./interfaces/IPropertyIPFSMetadataRenderer.sol";
src/token/metadata/MetadataRenderer.sol::243 => aryAttributes = abi.encodePacked(aryAttributes, '"', property.name, '": "', item.name, '"', isLast ? "" : ",");
src/token/metadata/interfaces/IBaseMetadata.sol::4 => import { IUUPS } from "../../../lib/interfaces/IUUPS.sol";
src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::4 => import { MetadataRendererTypesV1 } from "../types/MetadataRendererTypesV1.sol";
src/token/metadata/storage/MetadataRendererStorageV1.sol::4 => import { MetadataRendererTypesV1 } from "../types/MetadataRendererTypesV1.sol";
src/token/types/TokenTypesV1.sol::4 => import { IBaseMetadata } from "../metadata/interfaces/IBaseMetadata.sol";
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
src/lib/token/ERC721Votes.sol::95 => middle = high - (high - low) / 2;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)
