## QA

### Missing checks for address(0x0) when assigning values to address state variables

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L41
```solidity
File: /src/auction/Auction.sol
41:        WETH = _weth;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L76
```solidity
File: /src/governance/governor/Governor.sol

76:        settings.vetoer = _vetoer;
```


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L62-L66

```solidity
File: /src/manager/Manager.sol
62:        tokenImpl = _tokenImpl;
63:        metadataImpl = _metadataImpl;
64:        auctionImpl = _auctionImpl;
65:        treasuryImpl = _treasuryImpl;
66:        governorImpl = _governorImpl;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L46
```solidity
File: /src/lib/utils/Ownable.sol
46:        _owner = _initialOwner;
```

### Non assembly method exists
```assembly{ id := chainid() }``` => ```uint256 id = block.chainid```, ```assembly { size := extcodesize() }``` => ```uint256 size = address().code.length```
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L30-L34
```solidity
File: /src/lib/utils/Address.sol
30:    function isContract(address _account) internal view returns (bool rv) {
31:        assembly {
32:            rv := gt(extcodesize(_account), 0)
33:        }
34:    }
```

We can modify the above code as follows
```solidity
     function isContract(address _account) internal view returns (bool) {

          return account.code.length > 0;
```

[see openzeppelin implementation on the same](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/6a8d977d2248cf1c115497fccfd7a2da3f86a58f/contracts/utils/Address.sol#L36-L42)


### State variable shadowing:
Shadowing state variables in derived contracts may be dangerous for critical variables such as contract owner (for e.g. where modifiers in base contracts check on base variables but shadowed variables are set mistakenly) and contracts incorrectly use base/shadowed variables. Do not shadow state variables

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L80-L86
```solidity
File: /src/manager/Manager.sol
80:    function initialize(address _owner) external initializer {
81:        // Ensure an owner is specified
82:        if (_owner == address(0)) revert ADDRESS_ZERO();
83:
84:        // Set the contract owner
85:        __Ownable_init(_owner);
86:    }
```

A similar variable is declared in an inherited contract, see below
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L18
```solidity
File: /src/lib/utils/Ownable.sol
18:     address internal _owner;
```

### constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name. 

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L468
```solidity
File: /src/governance/governor/Governor.sol
//@audit: 10_000
468:            return (settings.token.totalSupply() * settings.proposalThresholdBps) / 10_000;

//@audit: 10_000
475:            return (settings.token.totalSupply() * settings.quorumThresholdBps) / 10_000;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L88
```solidity
File: /src/token/Token.sol
//@audit: 100
88:                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();

//@audit: 100
102:                uint256 schedule = 100 / founderPct;

//@audit: 100
118:                (baseTokenId += schedule) % 100;

//@audit: 100
179:        uint256 baseTokenId = _tokenId % 100;

//@audit: 100
271:        return tokenRecipient[_tokenId % 100];
```
### abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions .Unless there is a compelling reason, abi.encode should be preferred". If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

If all arguments are strings and or bytes, bytes.concat() should be used instead
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L68-L71
```solidity
File: /src/manager/Manager.sol
68:        metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_metadataImpl, "")));
71:        auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_auctionImpl, "")));
72:        treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_treasuryImpl, "")));
73:        governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(_governorImpl, "")));
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L167-L170
```solidity
File: /src/manager/Manager.sol
167:        metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
168:        auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
169:        treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
170:        governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L161-L163
```solidity
File: /src/lib/token/ERC721Votes.sol
161:            digest = keccak256(
162:                abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
163: );
```


### ERC-721 transfers do not use safeTransferFrom
The cases where ERC-721 tokens are transferred are always using transferFrom and not safeTransferFrom. This means users need to be aware their tokens may get locked up in an unprepared contract recipient.

This may be intentional to reduce the potential for reentrancy, but it may strike users unprepared.

If it is by design, then it means users must be educated about this fact and the frontend would need to verify target addresses prior to submitting any transactions and hope that other frontends/integrations do not exists.

Should a ERC-721 compatible token be transferred to an unprepared contract, it would end up being locked up there. 

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L192
```solidity
File: /src/auction/Auction.sol
192:            token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
```


###  \_safeMint() should be used rather than \_mint() wherever possible

\_mint() is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of \_safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren't lost if they're minted to contracts that cannot transfer them back out.

safeMint should be used in place of \_mint in the following
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L161

```solidity
File: /src/token/Token.sol
161:         _mint(minter, tokenId);

188:            _mint(tokenRecipient[baseTokenId].wallet, _tokenId);
```

Be careful however to respect the CEI pattern or add a re-entrancy guard as _safeMint adds a callback-check (_checkOnERC721Received) and a malicious onERC721Received could be exploited if not careful.

### Missing a __gap[50] storage variable to allow for new storage variables in later versions

This is empty reserved space in storage that is put in place in Upgradeable contracts. It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments.
See [Docs](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L12
```solidity
File: /src/lib/proxy/UUPS.sol
12: abstract contract UUPS is IUUPS, ERC1967Upgrade {
```

Looking at the [OpenZeppelin UUPSUpgradeable.sol Implementation](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/utils/UUPSUpgradeable.sol#L107) we can see the `__gap` variable is present there at [Line 107](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/4667eeef314cd59cc9568db7bbd5be7124610e0d/contracts/proxy/utils/UUPSUpgradeable.sol#L107)

### Hardcoded Values - Recommend using Enums
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L278-L288
```solidity
File: /src/governance/governor/Governor.sol
278:            if (_support == 0) {
            
283:            } else if (_support == 1) {
              
288:            } else if (_support == 2) {
```


### Upper case variables should be reserved for constants

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L25-L35
```solidity
File: /src/lib/utils/EIP712.sol
25:    /// @notice The hash of the EIP-712 domain name
26:    bytes32 internal HASHED_NAME;

28:    /// @notice The hash of the EIP-712 domain version
29:    bytes32 internal HASHED_VERSION;

31:    /// @notice The domain separator computed upon initialization
32:    bytes32 internal INITIAL_DOMAIN_SEPARATOR;

34:    /// @notice The chain id upon initialization
35:    uint256 internal INITIAL_CHAIN_ID;
```

### Lack of event emission after critical initialize() functions

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L54-L82

```solidity
File: /src/auction/Auction.sol
54:    function initialize(
55:        address _token,
56:        address _founder,
57:        address _treasury,
58:        uint256 _duration,
59:        uint256 _reservePrice
60:    ) external initializer {
         ...
82:    }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L57-L65
```solidity
File: /src/governance/governor/Governor.sol
57:    function initialize(
58:        address _treasury,
59:        address _token,
60:        address _vetoer,
61:        uint256 _votingDelay,
62:        uint256 _votingPeriod,
63:        uint256 _proposalThresholdBps,
64:        uint256 _quorumThresholdBps
65:    ) external initializer {
```

### Missing indexed values on events
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L22
```solidity
File: /src/auction/IAuction.sol
22:    event AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime);

28:    event AuctionSettled(uint256 tokenId, address winner, uint256 amount);

34:    event AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime);

38:    event DurationUpdated(uint256 duration);

42:    event ReservePriceUpdated(uint256 reservePrice);

46:    event MinBidIncrementPercentageUpdated(uint256 minBidIncrementPercentage);

50:    event TimeBufferUpdated(uint256 timeBuffer);

```


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/IGovernor.sol#L18-L26

```solidity
File: /src/governance/governor/IGovernor.sol
18:    event ProposalCreated(
19:        bytes32 proposalId,
20:        address[] targets,
21:        uint256[] values,
22:        bytes[] calldatas,
23:        string description,
24:        bytes32 descriptionHash,
25:        Proposal proposal
26:    );


29:    event ProposalQueued(bytes32 proposalId, uint256 eta);

33:    event ProposalExecuted(bytes32 proposalId);

36:    event ProposalCanceled(bytes32 proposalId);

39:    event ProposalVetoed(bytes32 proposalId);

42:    event VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason);

45:    event VotingDelayUpdated(uint256 prevVotingDelay, uint256 newVotingDelay);

48:    event VotingPeriodUpdated(uint256 prevVotingPeriod, uint256 newVotingPeriod);

51:    event ProposalThresholdBpsUpdated(uint256 prevBps, uint256 newBps);

54:    event QuorumVotesBpsUpdated(uint256 prevBps, uint256 newBps);

57:    event VetoerUpdated(address prevVetoer, address newVetoer);

```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/ITreasury.sol#L16
```solidity
File: /src/governance/treasury/ITreasury.sol
16:    event TransactionScheduled(bytes32 proposalId, uint256 timestamp);

19:    event TransactionCanceled(bytes32 proposalId);

22:    event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);

25:    event DelayUpdated(uint256 prevDelay, uint256 newDelay);

28:    event GracePeriodUpdated(uint256 prevGracePeriod, uint256 newGracePeriod);

```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/IManager.sol#L21
```solidity
File: /src/manager/IManager.sol
21:    event DAODeployed(address token, address metadata, address auction, address treasury, address governor);

26:    event UpgradeRegistered(address baseImpl, address upgradeImpl);

31:    event UpgradeRemoved(address baseImpl, address upgradeImpl);

```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/IToken.sol#L21
```solidity
File: /src/token/IToken.sol
21:    event MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder);
```

## NatSpec is incomplete
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L93-L105
```solidity
File: /src/governance/governor/Governor.sol

//@audit: Missing @return
93:    /// @notice Hashes a proposal's details into a proposal id
94:    /// @param _targets The target addresses to call
95:    /// @param _values The ETH values of each call
96:    /// @param _calldatas The calldata of each call
97:    /// @param _descriptionHash The hash of the description
98:    function hashProposal(
99:        address[] memory _targets,
100:        uint256[] memory _values,
101:        bytes[] memory _calldatas,
102:        bytes32 _descriptionHash
103:    ) public pure returns (bytes32) {
104:        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
105:    }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L111-L121
```solidity
//@audit: missing @return 
111:    /// @notice Creates a proposal
112:    /// @param _targets The target addresses to call
113:    /// @param _values The ETH values of each call
114:    /// @param _calldatas The calldata of each call
115:    /// @param _description The proposal description
116:    function propose(
117:        address[] memory _targets,
118:        uint256[] memory _values,
119:        bytes[] memory _calldatas,
120:        string memory _description
121:    ) external returns (bytes32) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L181-L186
```solidity
File: /src/governance/governor/Governor.sol

//@audit: Missing @return
181:    /// @notice Casts a vote
182:    /// @param _proposalId The proposal id
183:    /// @param _support The support value (0 = Against, 1 = For, 2 = Abstain)
184:    function castVote(bytes32 _proposalId, uint256 _support) external returns (uint256) {
185:        return _castVote(_proposalId, msg.sender, _support, "");
186:    }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L303-L305
```solidity
File: /src/governance/governor/Governor.sol

//@audit: Missing @return eta
303:    /// @notice Queues a proposal
304:    /// @param _proposalId The proposal id
305:    function queue(bytes32 _proposalId) external returns (uint256 eta) {
```


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L204-L206
```solidity
File: /src/token/metadata/MetadataRenderer.sol

//@audit: Missing @return aryAttributes,@return queryString
204:    /// @notice The properties and query string for a generated token
205:    /// @param _tokenId The ERC-721 token id
206:    function getAttributes(uint256 _tokenId) public view returns (bytes memory aryAttributes, bytes memory queryString) {

```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L268-L269
```solidity
File: /src/token/metadata/MetadataRenderer.sol

//@audit: Missing @return 
268:    /// @notice The contract URI
269:    function contractURI() external view returns (string memory) {


//@audit: Missing @return
284:    /// @notice The token URI
285:    /// @param _tokenId The ERC-721 token id
286:    function tokenURI(uint256 _tokenId) external view returns (string memory) {


//@audit: Missing @return
316:    /// @notice The associated ERC-721 token
317:    function token() external view returns (address) {

//@audit: Missing @return
321:    /// @notice The DAO treasury
322:    function treasury() external view returns (address) {

//@audit: Missing @return
326:    /// @notice The contract image
327:    function contractImage() external view returns (string memory) {

//@audit: Missing @return
331:    /// @notice The renderer base
332:    function rendererBase() external view returns (string memory) {

//@audit: Missing @return
336:    /// @notice The collection description
337:    function description() external view returns (string memory) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L142-L143
```solidity
File: /src/token/Token.sol

//@audit: Missing @return tokenId
142:    /// @notice Mints tokens to the auction house for bidding and handles founder vesting
143:    function mint() external nonReentrant returns (uint256 tokenId) {


//@audit: Missing @return 
219:    /// @notice The URI for a token
220:    /// @param _tokenId The ERC-721 token id
221:    function tokenURI(uint256 _tokenId) public view override(IToken, ERC721) returns (string memory) {

//@audit: Missing @return
225:    /// @notice The URI for the contract
226:    function contractURI() public view override(IToken, ERC721) returns (string memory) {

//@audit: Missing @return
244:    /// @notice The vesting details of a founder
245:    /// @param _founderId The founder id
246:    function getFounder(uint256 _founderId) external view returns (Founder memory) {


//@audit: Missing @return
250:    /// @notice The vesting details of all founders
251:    function getFounders() external view returns (Founder[] memory) {

//@audit: Missing @return
269:    /// @param _tokenId The ERC-721 token id
270:    function getScheduledRecipient(uint256 _tokenId) external view returns (Founder memory) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L60-L61
```solidity
File: /src/lib/proxy/UUPS.sol

//@audit: Missing @return
60:  /// @notice The storage slot of the implementation address
61:    function proxiableUUID() external view notDelegated returns (bytes32) {

```

### For loops

There is some inconsistency in how loops are declared across the codebase
General structure used:
```solidity
for (uint256 i = 0; CONDITION; ++i) { ... }
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162
```solidity
File: /src/governance/treasury/Treasury.sol
162:            for (uint256 i = 0; i < numTargets; ++i) {
```

Other parts of code use (note : i is not initilized, assumes the default value)
```solidity
for (uint256 i; CONDITION; ++i) { ... }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L80
```solidity
File: /src/token/Token.sol
80:            for (uint256 i; i < numFounders; ++i) {
```
Try to stick to one.
