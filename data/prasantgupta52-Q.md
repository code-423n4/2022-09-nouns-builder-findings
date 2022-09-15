# 1. Avoid using Floating Pragma:
### Description:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

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
# 2. _safeMint() should be used rather than _mint() wherever possible
### Description:
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

### Links to github files
[Token.sol:L161](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L161)
[Token.sol:L167](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L167)
[Token.sol:L169](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L169)
[Token.sol:L188](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L188)
[ERC721.sol:L191](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L191)

### Instances
```
src/token/Token.sol:161:        _mint(minter, tokenId);
src/token/Token.sol:167:    function _mint(address _to, uint256 _tokenId) internal override {
src/token/Token.sol:169:        super._mint(_to, _tokenId);
src/token/Token.sol:188:            _mint(tokenRecipient[baseTokenId].wallet, _tokenId);
src/lib/token/ERC721.sol:191:    function _mint(address _to, uint256 _tokenId) internal virtual {
```
### Recommendations:
Use _safeMint() instead of _mint().

----
# 3. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom
### Description
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

### Links to github files
[Auction.sol:L192](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L192)
[Auction.sol:L363](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L363)

### Instances
```
src/auction/Auction.sol:192:            token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
src/auction/Auction.sol:363:            IWETH(WETH).transfer(_to, _amount);
```
### Recommended Mitigation Steps

Consider using safeTransfer/safeTransferFrom or require() consistently.

----
# 4.Multiple initialization due to initialize function not having initializer modifier.
### Description
The attacker can initialize the contract, take malicious actions, and allow it to be re-initialized by the project without any error being noticed.
### Links to github files
[IAuction.sol:L93](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L93)
[IToken.sol:L51](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/IToken.sol#L51)
[IBaseMetadata.sol:L27](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/interfaces/IBaseMetadata.sol#L27)
[IGovernor.sol:L119](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/IGovernor.sol#L119)
[ITreasury.sol:L64](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/ITreasury.sol#L64)

### Instances
```
src/auction/IAuction.sol:93:    function initialize(
src/token/IToken.sol:51:    function initialize(
src/token/metadata/interfaces/IBaseMetadata.sol:27:    function initialize(
src/governance/governor/IGovernor.sol:119:    function initialize(
src/governance/treasury/ITreasury.sol:64:    function initialize(address governor, uint256 delay) external;
```

----
# 5. Use of Block.timestamp
Impact - Non-Critical
### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Links to github files
[Auction.sol:L98](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L98)
[Auction.sol:L141](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L141)
[Auction.sol:L149](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L149)
[Auction.sol:L178](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L178)
[Auction.sol:L211](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L211)
[Token.sol:L186](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L186)
[MetadataRenderer.sol:L251](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L251)
[ERC721Votes.sol:L61](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L61)
[ERC721Votes.sol:L153](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L153)
[ERC721Votes.sol:L253](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L253)
[Governor.sol:L128](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L128)
[Governor.sol:L160](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L160)
[Governor.sol:L170](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L170)
[Governor.sol:L218](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L218)
[Governor.sol:L363](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L363)
[Governor.sol:L433](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L433)
[Governor.sol:L437](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L437)
[Treasury.sol:L76](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L76)
[Treasury.sol:L89](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L89)
[Treasury.sol:L123](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L123)

### Instances
```
src/auction/Auction.sol:98:        if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();
src/auction/Auction.sol:141:            extend = (_auction.endTime - block.timestamp) < settings.timeBuffer;
src/auction/Auction.sol:149:                auction.endTime = uint40(block.timestamp + settings.timeBuffer);
src/auction/Auction.sol:178:        if (block.timestamp < _auction.endTime) revert AUCTION_ACTIVE();
src/auction/Auction.sol:211:            uint256 startTime = block.timestamp;
src/token/Token.sol:186:        } else if (block.timestamp < tokenRecipient[baseTokenId].vestExpiry) {
src/token/metadata/MetadataRenderer.sol:251:        return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
src/lib/token/ERC721Votes.sol:61:        if (_timestamp >= block.timestamp) revert INVALID_TIMESTAMP();
src/lib/token/ERC721Votes.sol:153:        if (block.timestamp > _deadline) revert EXPIRED_SIGNATURE();
src/lib/token/ERC721Votes.sol:253:        checkpoint.timestamp = uint64(block.timestamp);
src/governance/governor/Governor.sol:128:            if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
src/governance/governor/Governor.sol:160:            snapshot = block.timestamp + settings.votingDelay;
src/governance/governor/Governor.sol:170:        proposal.timeCreated = uint32(block.timestamp);
src/governance/governor/Governor.sol:218:        if (block.timestamp > _deadline) revert EXPIRED_SIGNATURE();
src/governance/governor/Governor.sol:363:            if (msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)
src/governance/governor/Governor.sol:433:        } else if (block.timestamp < proposal.voteStart) {
src/governance/governor/Governor.sol:437:        } else if (block.timestamp < proposal.voteEnd) {
src/governance/treasury/Treasury.sol:76:            return block.timestamp > (timestamps[_proposalId] + settings.gracePeriod);
src/governance/treasury/Treasury.sol:89:        return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
src/governance/treasury/Treasury.sol:123:            eta = block.timestamp + settings.delay;
```

### Recommended Mitigation Steps

Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

----
# 6. Event is missing `indexed` fields
Impact - Non-Critical
Each `event` should use three `indexed` fields if there are three or more fields
### Links to github files
[IAuction.sol:L22](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L22)
[IAuction.sol:L28](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L28)
[IAuction.sol:L34](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L34)
[IToken.sol:L21](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/IToken.sol#L21)
[IManager.sol:L21](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/IManager.sol#L21)
[IGovernor.sol:L42](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/IGovernor.sol#L42)
[ITreasury.sol:L22](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/ITreasury.sol#L22)

### Instances
```
src/auction/IAuction.sol:22:    event AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime);
src/auction/IAuction.sol:28:    event AuctionSettled(uint256 tokenId, address winner, uint256 amount);
src/auction/IAuction.sol:34:    event AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime);
src/token/IToken.sol:21:    event MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder);
src/manager/IManager.sol:21:    event DAODeployed(address token, address metadata, address auction, address treasury, address governor);
src/governance/governor/IGovernor.sol:42:    event VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason);
src/governance/treasury/ITreasury.sol:22:    event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);
```
----
# 7. Variable names that consist of all capital letters should be reserved for const/immutable variables
### Description
If the variable needs to be different based on which class it comes from, a `view`/`pure` function should be used instead
### Links to github files
[Auction.sol:L31](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L31)
[Token.sol:L23](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L23)
[MetadataRenderer.sol:L25](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L25)
[Manager.sol:L25](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L25)
[Manager.sol:L28](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L28)
[Manager.sol:L31](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L31)
[Manager.sol:L34](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L34)
[Manager.sol:L37](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L37)
[Manager.sol:L40](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L40)
[Manager.sol:L43](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L43)
[Manager.sol:L46](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L46)
[Manager.sol:L49](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L49)
[Governor.sol:L34](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L34)
[Treasury.sol:L25](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L25)

### Instances
```
src/auction/Auction.sol:31:    IManager private immutable manager;
src/token/Token.sol:23:    IManager private immutable manager;
src/token/metadata/MetadataRenderer.sol:25:    IManager private immutable manager;
src/manager/Manager.sol:25:    address public immutable tokenImpl;
src/manager/Manager.sol:28:    address public immutable metadataImpl;
src/manager/Manager.sol:31:    address public immutable auctionImpl;
src/manager/Manager.sol:34:    address public immutable treasuryImpl;
src/manager/Manager.sol:37:    address public immutable governorImpl;
src/manager/Manager.sol:40:    bytes32 private immutable metadataHash;
src/manager/Manager.sol:43:    bytes32 private immutable auctionHash;
src/manager/Manager.sol:46:    bytes32 private immutable treasuryHash;
src/manager/Manager.sol:49:    bytes32 private immutable governorHash;
src/governance/governor/Governor.sol:34:    IManager private immutable manager;
src/governance/treasury/Treasury.sol:25:    IManager private immutable manager;
```
----
# 8. Incosistent return type within contracts
### Description
The functions uses an named return, while the rest of the contract and functions uses unamed returns. Please keep it consistent within contracts and accross the project if possible to improve readibility.
### Links to github files
[Token.sol:L143](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L143)
[MetadataRenderer.sol:L206](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L206)
[Manager.sol:L104](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L104)
[Manager.sol:L158](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L158)
[Governor.sol:L305](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L305)
[ITreasury.sol:L96](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/ITreasury.sol#L96)


### Instances
```
src/token/Token.sol:143:    function mint() external nonReentrant returns (uint256 tokenId) {
src/token/metadata/MetadataRenderer.sol:206:    function getAttributes(uint256 _tokenId) public view returns (bytes memory aryAttributes, bytes memory queryString) {
src/manager/Manager.sol:104:        returns (
src/manager/Manager.sol:158:        returns (
src/governance/governor/Governor.sol:305:    function queue(bytes32 _proposalId) external returns (uint256 eta) {
src/governance/treasury/ITreasury.sol:96:    function queue(bytes32 proposalId) external returns (uint256 eta);
```
### Recommended Mitigation Steps
use unamed return for every function
