## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2022-09-nouns-builder/src/lib/interfaces/IEIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IInitializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IOwnable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IPausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IUUPS.sol::2 => pragma solidity ^0.8.15;
2022-09-nouns-builder/src/lib/interfaces/IWETH.sol::2 => pragma solidity ^0.8.15;
2022-09-nouns-builder/src/lib/proxy/ERC1967Proxy.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/proxy/ERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/proxy/UUPS.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/token/ERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Address.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/EIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Initializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Ownable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Pausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/ReentrancyGuard.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/SafeCast.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/TokenReceiver.sol::2 => pragma solidity ^0.8.0;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-09-nouns-builder/src/auction/Auction.sol::98 => if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();
2022-09-nouns-builder/src/auction/Auction.sol::141 => extend = (_auction.endTime - block.timestamp) < settings.timeBuffer;
2022-09-nouns-builder/src/auction/Auction.sol::149 => auction.endTime = uint40(block.timestamp + settings.timeBuffer);
2022-09-nouns-builder/src/auction/Auction.sol::178 => if (block.timestamp < _auction.endTime) revert AUCTION_ACTIVE();
2022-09-nouns-builder/src/auction/Auction.sol::211 => uint256 startTime = block.timestamp;
2022-09-nouns-builder/src/governance/governor/Governor.sol::128 => if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
2022-09-nouns-builder/src/governance/governor/Governor.sol::160 => snapshot = block.timestamp + settings.votingDelay;
2022-09-nouns-builder/src/governance/governor/Governor.sol::170 => proposal.timeCreated = uint32(block.timestamp);
2022-09-nouns-builder/src/governance/governor/Governor.sol::218 => if (block.timestamp > _deadline) revert EXPIRED_SIGNATURE();
2022-09-nouns-builder/src/governance/governor/Governor.sol::363 => if (msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)
2022-09-nouns-builder/src/governance/governor/Governor.sol::433 => } else if (block.timestamp < proposal.voteStart) {
2022-09-nouns-builder/src/governance/governor/Governor.sol::437 => } else if (block.timestamp < proposal.voteEnd) {
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::76 => return block.timestamp > (timestamps[_proposalId] + settings.gracePeriod);
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::89 => return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::123 => eta = block.timestamp + settings.delay;
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::61 => if (_timestamp >= block.timestamp) revert INVALID_TIMESTAMP();
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::153 => if (block.timestamp > _deadline) revert EXPIRED_SIGNATURE();
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::253 => checkpoint.timestamp = uint64(block.timestamp);
2022-09-nouns-builder/src/token/Token.sol::186 => } else if (block.timestamp < tokenRecipient[baseTokenId].vestExpiry) {
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::251 => return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
```

## [L-03] Unused receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
2022-09-nouns-builder/src/governance/treasury/Treasury.sol::269 => receive() external payable {}
```

## [L-04] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
2022-09-nouns-builder/src/manager/Manager.sol::68 => metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));
2022-09-nouns-builder/src/manager/Manager.sol::69 => auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));
2022-09-nouns-builder/src/manager/Manager.sol::70 => treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));
2022-09-nouns-builder/src/manager/Manager.sol::71 => governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));
2022-09-nouns-builder/src/manager/Manager.sol::167 => metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
2022-09-nouns-builder/src/manager/Manager.sol::168 => auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
2022-09-nouns-builder/src/manager/Manager.sol::169 => treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
2022-09-nouns-builder/src/manager/Manager.sol::170 => governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
```

## [L-05] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-09-nouns-builder/src/auction/Auction.sol::192 => token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
2022-09-nouns-builder/src/auction/Auction.sol::363 => IWETH(WETH).transfer(_to, _amount);
```

## [L-06] Missing Zero address checks

Zero-address checks are a best practice for input validation of critical address parameters. While the codebase applies this to most cases, there are many places where this is missing in constructors and setters.
Impact: Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

```
2022-09-nouns-builder/src/auction/Auction.sol::41 => WETH = _weth;
2022-09-nouns-builder/src/auction/Auction.sol::79 => settings.treasury = _treasury;
2022-09-nouns-builder/src/governance/governor/Governor.sol::76 => settings.vetoer = _vetoer;
2022-09-nouns-builder/src/manager/Manager.sol::62 => tokenImpl = _tokenImpl;
2022-09-nouns-builder/src/manager/Manager.sol::63 => metadataImpl = _metadataImpl;
2022-09-nouns-builder/src/manager/Manager.sol::64 => auctionImpl = _auctionImpl;
2022-09-nouns-builder/src/manager/Manager.sol::65 => treasuryImpl = _treasuryImpl;
2022-09-nouns-builder/src/manager/Manager.sol::66 => governorImpl = _governorImpl;
2022-09-nouns-builder/src/token/Token.sol::66 => settings.auction = _auction;
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::65 => settings.token = _token;
2022-09-nouns-builder/src/token/metadata/MetadataRenderer.sol::66 => settings.treasury = _treasury;
```

## [L-07] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2022-09-nouns-builder/src/auction/Auction.sol::268 => function settleAuction() external nonReentrant whenPaused {
```

## [N-1] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2022-09-nouns-builder/src/lib/interfaces/IEIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IInitializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IOwnable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/interfaces/IPausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/proxy/ERC1967Proxy.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/proxy/ERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/proxy/UUPS.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/token/ERC721.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Address.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/EIP712.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Initializable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Ownable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/Pausable.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/ReentrancyGuard.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/SafeCast.sol::2 => pragma solidity ^0.8.4;
2022-09-nouns-builder/src/lib/utils/TokenReceiver.sol::2 => pragma solidity ^0.8.0;
```

## [N-2] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-09-nouns-builder/src/auction/IAuction.sol::22 => event AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime);
2022-09-nouns-builder/src/auction/IAuction.sol::28 => event AuctionSettled(uint256 tokenId, address winner, uint256 amount);
2022-09-nouns-builder/src/auction/IAuction.sol::34 => event AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime);
2022-09-nouns-builder/src/auction/IAuction.sol::38 => event DurationUpdated(uint256 duration);
2022-09-nouns-builder/src/auction/IAuction.sol::42 => event ReservePriceUpdated(uint256 reservePrice);
2022-09-nouns-builder/src/auction/IAuction.sol::46 => event MinBidIncrementPercentageUpdated(uint256 minBidIncrementPercentage);
2022-09-nouns-builder/src/auction/IAuction.sol::50 => event TimeBufferUpdated(uint256 timeBuffer);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::29 => event ProposalQueued(bytes32 proposalId, uint256 eta);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::33 => event ProposalExecuted(bytes32 proposalId);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::36 => event ProposalCanceled(bytes32 proposalId);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::39 => event ProposalVetoed(bytes32 proposalId);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::42 => event VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::45 => event VotingDelayUpdated(uint256 prevVotingDelay, uint256 newVotingDelay);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::48 => event VotingPeriodUpdated(uint256 prevVotingPeriod, uint256 newVotingPeriod);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::51 => event ProposalThresholdBpsUpdated(uint256 prevBps, uint256 newBps);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::54 => event QuorumVotesBpsUpdated(uint256 prevBps, uint256 newBps);
2022-09-nouns-builder/src/governance/governor/IGovernor.sol::57 => event VetoerUpdated(address prevVetoer, address newVetoer);
2022-09-nouns-builder/src/governance/treasury/ITreasury.sol::16 => event TransactionScheduled(bytes32 proposalId, uint256 timestamp);
2022-09-nouns-builder/src/governance/treasury/ITreasury.sol::19 => event TransactionCanceled(bytes32 proposalId);
2022-09-nouns-builder/src/governance/treasury/ITreasury.sol::22 => event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);
2022-09-nouns-builder/src/governance/treasury/ITreasury.sol::25 => event DelayUpdated(uint256 prevDelay, uint256 newDelay);
2022-09-nouns-builder/src/governance/treasury/ITreasury.sol::28 => event GracePeriodUpdated(uint256 prevGracePeriod, uint256 newGracePeriod);
2022-09-nouns-builder/src/lib/interfaces/IERC1967Upgrade.sol::14 => event Upgraded(address impl);
2022-09-nouns-builder/src/lib/interfaces/IInitializable.sol::13 => event Initialized(uint256 version);
2022-09-nouns-builder/src/lib/interfaces/IPausable.sol::14 => event Paused(address user);
2022-09-nouns-builder/src/lib/interfaces/IPausable.sol::18 => event Unpaused(address user);
2022-09-nouns-builder/src/manager/IManager.sol::21 => event DAODeployed(address token, address metadata, address auction, address treasury, address governor);
2022-09-nouns-builder/src/manager/IManager.sol::26 => event UpgradeRegistered(address baseImpl, address upgradeImpl);
2022-09-nouns-builder/src/manager/IManager.sol::31 => event UpgradeRemoved(address baseImpl, address upgradeImpl);
2022-09-nouns-builder/src/token/IToken.sol::21 => event MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder);
2022-09-nouns-builder/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::16 => event PropertyAdded(uint256 id, string name);
2022-09-nouns-builder/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::19 => event ItemAdded(uint256 propertyId, uint256 index);
2022-09-nouns-builder/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::22 => event ContractImageUpdated(string prevImage, string newImage);
2022-09-nouns-builder/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::25 => event RendererBaseUpdated(string prevRendererBase, string newRendererBase);
2022-09-nouns-builder/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol::28 => event DescriptionUpdated(string prevDescription, string newDescription);
```

## [N-3] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2022-09-nouns-builder/src/auction/Auction.sol::268 => function settleAuction() external nonReentrant whenPaused {
2022-09-nouns-builder/src/auction/IAuction.sol::106 => function settleCurrentAndCreateNewAuction() external;
2022-09-nouns-builder/src/auction/IAuction.sol::109 => function settleAuction() external;
2022-09-nouns-builder/src/auction/IAuction.sol::131 => function setDuration(uint256 duration) external;
2022-09-nouns-builder/src/auction/IAuction.sol::135 => function setReservePrice(uint256 reservePrice) external;
2022-09-nouns-builder/src/auction/IAuction.sol::139 => function setTimeBuffer(uint256 timeBuffer) external;
2022-09-nouns-builder/src/auction/IAuction.sol::143 => function setMinimumBidIncrement(uint256 percentage) external;
2022-09-nouns-builder/src/lib/interfaces/IERC721.sol::78 => function setApprovalForAll(address operator, bool approved) external;
```

## [N-4] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
2022-09-nouns-builder/src/governance/governor/Governor.sol::27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::21 => bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
2022-09-nouns-builder/src/lib/utils/EIP712.sol::19 => bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```

## [N-5] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-09-nouns-builder/src/lib/token/ERC721Votes.sol::10 => /// @notice Modified from OpenZeppelin Contracts v4.7.3 (token/ERC721/extensions/draft-ERC721Votes.sol) & Nouns DAO ERC721Checkpointable.sol commit 2cbe6c7 - licensed under the BSD-3-Clause license.
```
