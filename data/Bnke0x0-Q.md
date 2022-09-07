



### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::40 => manager = IManager(_manager);
2022-09-nouns-builder-main/src/auction/Auction.sol::41 => WETH = _weth;
2022-09-nouns-builder-main/src/auction/Auction.sol::74 => token = Token(_token);
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::48 => name = _name;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::49 => symbol = _symbol;
2022-09-nouns-builder-main/src/manager/Manager.sol::62 => tokenImpl = _tokenImpl;
2022-09-nouns-builder-main/src/manager/Manager.sol::63 => metadataImpl = _metadataImpl;
2022-09-nouns-builder-main/src/manager/Manager.sol::64 => auctionImpl = _auctionImpl;
2022-09-nouns-builder-main/src/manager/Manager.sol::65 => treasuryImpl = _treasuryImpl;
2022-09-nouns-builder-main/src/manager/Manager.sol::66 => governorImpl = _governorImpl;
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::157 => newItem.name = _items[i].name;
```

### [L02]`initialize` functions can be front-run

#### Impact
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)
 link for a description of this storage variable. While some contracts 
may not currently be sub-classed, adding the variable now protects 
against forgetting to add it in the future.
#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::39 => constructor(address _manager, address _weth) payable initializer {
2022-09-nouns-builder-main/src/auction/Auction.sol::54 => function initialize(
2022-09-nouns-builder-main/src/auction/Auction.sol::60 => ) external initializer {
2022-09-nouns-builder-main/src/auction/IAuction.sol::93 => function initialize(
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::41 => constructor(address _manager) payable initializer {
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::57 => function initialize(
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::65 => ) external initializer {
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::119 => function initialize(
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::64 => function initialize(address governor, uint256 delay) external;
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::32 => constructor(address _manager) payable initializer {
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::43 => function initialize(address _governor, uint256 _delay) external initializer {
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::33 => modifier initializer() {
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::55 => modifier reinitializer(uint8 _version) {
2022-09-nouns-builder-main/src/manager/Manager.sol::61 => ) payable initializer {
2022-09-nouns-builder-main/src/manager/Manager.sol::80 => function initialize(address _owner) external initializer {
2022-09-nouns-builder-main/src/token/IToken.sol::51 => function initialize(
2022-09-nouns-builder-main/src/token/Token.sol::30 => constructor(address _manager) payable initializer {
2022-09-nouns-builder-main/src/token/Token.sol::43 => function initialize(
2022-09-nouns-builder-main/src/token/Token.sol::48 => ) external initializer {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::32 => constructor(address _manager) payable initializer {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::45 => function initialize(
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::50 => ) external initializer {
2022-09-nouns-builder-main/src/token/metadata/interfaces/IBaseMetadata.sol::27 => function initialize(
```



### [L03] Unused `receive()` function will lock Ether in contract

#### Impact
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert
#### Findings:
```
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::269 => receive() external payable {}
```





### [L04] `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`

#### Impact
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
#### Findings:
```
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::162 => abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
2022-09-nouns-builder-main/src/manager/Manager.sol::68 => metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));
2022-09-nouns-builder-main/src/manager/Manager.sol::69 => auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));
2022-09-nouns-builder-main/src/manager/Manager.sol::70 => treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));
2022-09-nouns-builder-main/src/manager/Manager.sol::71 => governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));
2022-09-nouns-builder-main/src/manager/Manager.sol::167 => metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
2022-09-nouns-builder-main/src/manager/Manager.sol::168 => auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
2022-09-nouns-builder-main/src/manager/Manager.sol::169 => treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
2022-09-nouns-builder-main/src/manager/Manager.sol::170 => governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
```






### [L05]`_safeMint()` should be used rather than `_mint()` wherever possible

#### Impact
Issue Information: [L022](https://github.com/Bnke0x0/c4-common-issues/blob/main/2-Low-Risk.md#l022---_safeMint()-should-be-used-rather-than-_mint()-wherever-possible)

#### Findings:
```

2022-09-nouns-builder-main/src/token/Token.sol::161 => _mint(minter, tokenId);
2022-09-nouns-builder-main/src/token/Token.sol::169 => super._mint(_to, _tokenId);
2022-09-nouns-builder-main/src/token/Token.sol::188 => _mint(tokenRecipient[baseTokenId].wallet, _tokenId);
```









### [L06] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.
#### Findings:
```
2022-09-nouns-builder-main/src/lib/interfaces/IEIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IInitializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IOwnable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IPausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IUUPS.sol::2 => pragma solidity ^0.8.15;
2022-09-nouns-builder-main/src/lib/interfaces/IWETH.sol::2 => pragma solidity ^0.8.15;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Proxy.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Address.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::2 => pragma solidity ^0.8.0;
```


### [L07] Use Two-Step Transfer Pattern for Access Controls

#### Impact
Contracts implementing access control's, e.g. `owner`, should consider implementing a Two-Step Transfer pattern.

Otherwise it's possible that the role mistakenly transfers ownership to the wrong address, resulting in a loss of the role.
#### Findings:
```

2022-09-nouns-builder-main/src/lib/token/ERC721.sol::92 => address owner = owners[_tokenId];
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::103 => address owner = owners[_tokenId];
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::212 => address owner = owners[_tokenId];
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::18 => address internal _owner;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::21 => address internal _pendingOwner;
```




### Non-Critical Issues




### [N01] Adding a `return` statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::482 => return proposals[_proposalId];
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::86 => return balances[_owner];
2022-09-nouns-builder-main/src/lib/utils/Address.sol::48 => return _returndata;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::53 => return _owner;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::58 => return _pendingOwner;
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::45 => return _paused;
2022-09-nouns-builder-main/src/token/Token.sol::134 => return _tokenId;
```




### [N02] `require()`/`revert()` statements should have descriptive reason strings

#### Impact
Issue Information: [NC002](https://github.com/Bnke0x0/c4-common-issues/blob/main/2-Low-Risk.md#n002---require()/revert()-statements-should-have-descriptive-reason-strings)

#### Findings:
```
2022-09-nouns-builder-main/src/lib/utils/Address.sol::54 => revert(add(32, _returndata), returndata_size)
```




### [N03] constants should be defined rather than using magic numbers

#### Impact
Issue Information: [NC003](https://github.com/Bnke0x0/c4-common-issues/blob/main/2-Low-Risk.md#n003---constants-should-be-defined-rather-than-using-magic-numbers)

#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::119 => minBid = highestBid + ((highestBid * settings.minBidIncrement) / 100);
2022-09-nouns-builder-main/src/auction/Auction.sol::354 => success := call(50000, _to, _amount, 0, 0, 0, 0)
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::468 => return (settings.token.totalSupply() * settings.proposalThresholdBps) / 10_000;
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::475 => return (settings.token.totalSupply() * settings.quorumThresholdBps) / 10_000;
```








### [N04]  Use a more recent version of solidity


#### Findings:
```

2022-09-nouns-builder-main/src/lib/interfaces/IEIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IInitializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IOwnable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IPausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Proxy.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Address.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::2 => pragma solidity ^0.8.0;
```







### [N05] Variable names that consist of all capital letters should be reserved for `const`/`immutable `variables

#### Impact
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead

#### Findings:
```
2022-09-nouns-builder-main/src/auction/storage/AuctionStorageV1.sol::12 => Settings internal settings;
2022-09-nouns-builder-main/src/governance/governor/storage/GovernorStorageV1.sol::11 => Settings internal settings;
2022-09-nouns-builder-main/src/governance/treasury/storage/TreasuryStorageV1.sol::11 => Settings internal settings;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::21 => bytes32 private constant _ROLLBACK_SLOT = 0x4910fdfa16fed3260ed0e7147f7cc6da11a60208b5b9406d12a635614ffd9143;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::24 => bytes32 internal constant _IMPLEMENTATION_SLOT = 0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc;
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::26 => bytes32 internal HASHED_NAME;
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::29 => bytes32 internal HASHED_VERSION;
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::32 => bytes32 internal INITIAL_DOMAIN_SEPARATOR;
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::35 => uint256 internal INITIAL_CHAIN_ID;
2022-09-nouns-builder-main/src/token/metadata/storage/MetadataRendererStorageV1.sol::11 => Settings internal settings;
2022-09-nouns-builder-main/src/token/metadata/storage/MetadataRendererStorageV1.sol::14 => Property[] internal properties;
2022-09-nouns-builder-main/src/token/metadata/storage/MetadataRendererStorageV1.sol::17 => IPFSGroup[] internal ipfsData;
```





### [N06] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-09-nouns-builder-main/src/auction/IAuction.sol::22 => event AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime);
2022-09-nouns-builder-main/src/auction/IAuction.sol::28 => event AuctionSettled(uint256 tokenId, address winner, uint256 amount);
2022-09-nouns-builder-main/src/auction/IAuction.sol::34 => event AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::29 => event ProposalQueued(bytes32 proposalId, uint256 eta);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::33 => event ProposalExecuted(bytes32 proposalId);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::36 => event ProposalCanceled(bytes32 proposalId);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::39 => event ProposalVetoed(bytes32 proposalId);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::42 => event VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::45 => event VotingDelayUpdated(uint256 prevVotingDelay, uint256 newVotingDelay);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::48 => event VotingPeriodUpdated(uint256 prevVotingPeriod, uint256 newVotingPeriod);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::51 => event ProposalThresholdBpsUpdated(uint256 prevBps, uint256 newBps);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::54 => event QuorumVotesBpsUpdated(uint256 prevBps, uint256 newBps);
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::57 => event VetoerUpdated(address prevVetoer, address newVetoer);
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::16 => event TransactionScheduled(bytes32 proposalId, uint256 timestamp);
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::19 => event TransactionCanceled(bytes32 proposalId);
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::22 => event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::25 => event DelayUpdated(uint256 prevDelay, uint256 newDelay);
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::28 => event GracePeriodUpdated(uint256 prevGracePeriod, uint256 newGracePeriod);
2022-09-nouns-builder-main/src/lib/interfaces/IERC721.sol::28 => event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::19 => event DelegateVotesChanged(address indexed delegate, uint256 prevTotalVotes, uint256 newTotalVotes);
2022-09-nouns-builder-main/src/manager/IManager.sol::21 => event DAODeployed(address token, address metadata, address auction, address treasury, address governor);
2022-09-nouns-builder-main/src/manager/IManager.sol::26 => event UpgradeRegistered(address baseImpl, address upgradeImpl);
2022-09-nouns-builder-main/src/manager/IManager.sol::31 => event UpgradeRemoved(address baseImpl, address upgradeImpl);
2022-09-nouns-builder-main/src/token/IToken.sol::21 => event MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder);
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::16 => event PropertyAdded(uint256 id, string name);
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::19 => event ItemAdded(uint256 propertyId, uint256 index);
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::22 => event ContractImageUpdated(string prevImage, string newImage);
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::25 => event RendererBaseUpdated(string prevRendererBase, string newRendererBase);
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::28 => event DescriptionUpdated(string prevDescription, string newDescription);
```





### [N07] Use of sensitive/NC-inclusive terms


#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::136 => bool extend;
2022-09-nouns-builder-main/src/auction/Auction.sol::349 => bool success;
2022-09-nouns-builder-main/src/auction/types/AuctionTypesV1.sol::35 => bool settled;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::52 => bool executed;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::53 => bool canceled;
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::54 => bool vetoed;
2022-09-nouns-builder-main/src/token/metadata/types/MetadataRendererTypesV1.sol::11 => bool isNewProperty;
```






### [N08] States/flags should use Enums rather than separate constants


#### Findings:
```
2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::14 => uint256 internal constant _NOT_ENTERED = 1;
2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::17 => uint256 internal constant _ENTERED = 2;
```









### [N09] Unused file


#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/auction/IAuction.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/auction/storage/AuctionStorageV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/auction/types/AuctionTypesV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/governance/governor/IGovernor.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/governance/governor/storage/GovernorStorageV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/governance/governor/types/GovernorTypesV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/governance/treasury/ITreasury.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/governance/treasury/storage/TreasuryStorageV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/governance/treasury/types/TreasuryTypesV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IEIP712.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IERC1967Upgrade.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IERC721.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IInitializable.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IOwnable.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IPausable.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IUUPS.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/interfaces/IWETH.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Proxy.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/utils/Address.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/manager/IManager.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/manager/Manager.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/manager/storage/ManagerStorageV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/IToken.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/Token.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/metadata/interfaces/IBaseMetadata.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/metadata/storage/MetadataRendererStorageV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/metadata/types/MetadataRendererTypesV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/storage/TokenStorageV1.sol::1 => // SPDX-License-Identifier: MIT
2022-09-nouns-builder-main/src/token/types/TokenTypesV1.sol::1 => // SPDX-License-Identifier: MIT
```





### [N10] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from external to public.
#### Findings:
```

2022-09-nouns-builder-main/src/governance/governor/Governor.sol::413 => function state(bytes32 _proposalId) public view returns (ProposalState) {
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::461 => function getVotes(address _account, uint256 _timestamp) public view returns (uint256) {
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::466 => function proposalThreshold() public view returns (uint256) {
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::473 => function quorum() public view returns (uint256) {
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::82 => function isQueued(bytes32 _proposalId) public view returns (bool) {
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::88 => function isReady(bytes32 _proposalId) public view returns (bool) {
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::54 => function tokenURI(uint256 _tokenId) public view virtual returns (string memory) {}
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::57 => function contractURI() public view virtual returns (string memory) {}
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::83 => function balanceOf(address _owner) public view returns (uint256) {
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::91 => function ownerOf(uint256 _tokenId) public view returns (address) {
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::45 => function getVotes(address _account) public view returns (uint256) {
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::59 => function getPastVotes(address _account, uint256 _timestamp) public view returns (uint256) {
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::63 => function DOMAIN_SEPARATOR() public view returns (bytes32) {
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::52 => function owner() public view returns (address) {
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::57 => function pendingOwner() public view returns (address) {
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::63 => function transferOwnership(address _newOwner) public onlyOwner {
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::71 => function safeTransferOwnership(address _newOwner) public onlyOwner {
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::78 => function acceptOwnership() public onlyPendingOwner {
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::87 => function cancelOwnershipTransfer() public onlyOwner {
2022-09-nouns-builder-main/src/token/Token.sol::221 => function tokenURI(uint256 _tokenId) public view override(IToken, ERC721) returns (string memory) {
2022-09-nouns-builder-main/src/token/Token.sol::226 => function contractURI() public view override(IToken, ERC721) returns (string memory) {
2022-09-nouns-builder-main/src/token/Token.sol::294 => function owner() public view returns (address) {
2022-09-nouns-builder-main/src/token/metadata/MetadataRenderer.sol::206 => function getAttributes(uint256 _tokenId) public view returns (bytes memory aryAttributes, bytes memory queryString) {
```







### [N11] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2022-09-nouns-builder-main/src/auction/Auction.sol::80 => settings.timeBuffer = 5 minutes;
2022-09-nouns-builder-main/src/governance/treasury/Treasury.sol::57 => settings.gracePeriod = 2 weeks;
```





### [N12] Constant redefined elsewhere

#### Impact
Consider defining in only one contract so that values cannot become out of sync when only one location is updated
#### Findings:
```
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
2022-09-nouns-builder-main/src/governance/governor/Governor.sol::142 => bytes32 descriptionHash = keccak256(bytes(_description));
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::21 => bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::19 => bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```




### [N13] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2022-09-nouns-builder-main/src/lib/interfaces/IEIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IInitializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IOwnable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IPausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/interfaces/IUUPS.sol::2 => pragma solidity ^0.8.15;
2022-09-nouns-builder-main/src/lib/interfaces/IWETH.sol::2 => pragma solidity ^0.8.15;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Proxy.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/ERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/proxy/UUPS.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/token/ERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/token/ERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Address.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/EIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Initializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Ownable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/Pausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/ReentrancyGuard.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/SafeCast.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder-main/src/lib/utils/TokenReceiver.sol::2 => pragma solidity ^0.8.0;
```
