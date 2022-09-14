# Low Report

|No.|Issue|Instances|
|:-|:-|:-:|
|1|`abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`|6|
|2|Unused/empty `receive()`/`fallback()` function|1|
|3|Consider addings checks for signature malleability|2|

Total: **9** instances over **3** issues

## `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
As seen from the [Solidity Docs](https://docs.soliditylang.org/en/v0.8.15/abi-spec.html#non-standard-packed-mode):
> If you use `keccak256(abi.encodePacked(a, b))` and both `a` and `b` are dynamic types, it is easy to craft collisions in the hash value by moving parts of `a` into `b` and vice-versa. More specifically, `abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c")`. 
>
> If you use `abi.encodePacked` for signatures, authentication or data integrity, make sure to always use the same types and check that at most one of them is dynamic. Unless there is a compelling reason, `abi.encode` should be preferred.

Compared to `abi.encodePacked()`, `abi.encode()` pads all items to 32 bytes, which helps to prevent hash collisions. Additionally, if there is only one argument to `abi.encodePacked()`, it can be cast to `bytes()` or `bytes32()` instead.

_There are **6** instances of this issue:_  
```solidity
src/manager/Manager.sol:
 167:        metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
 168:        auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
 169:        treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
 170:        governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));

src/governance/governor/Governor.sol:
 226:        digest = keccak256(
 227:            abi.encodePacked(
 228:                "\x19\x01",
 229:                DOMAIN_SEPARATOR(),
 230:                keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
 231:            )
 232:        );

src/lib/token/ERC721Votes.sol:
 161:        digest = keccak256(
 162:            abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
 163:        );
```

## Unused/empty `receive()`/`fallback()` function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert.

_There is **1** instance of this issue:_  
```solidity
src/governance/treasury/Treasury.sol:
 269:        receive() external payable {}
```

## Consider addings checks for signature malleability
Use OpenZeppelin's `ECDSA` contract, which checks for [signature malleability](https://swcregistry.io/docs/SWC-117), rather than calling `ecrecover()` directly.

_There are **2** instances of this issue:_  
```solidity
src/governance/governor/Governor.sol:
 236:        address recoveredAddress = ecrecover(digest, _v, _r, _s);

src/lib/token/ERC721Votes.sol:
 167:        address recoveredAddress = ecrecover(digest, _v, _r, _s);
```

# Non-Critical Report

|No.|Issue|Instances|
|:-|:-|:-:|
|1|Unused `override` function arguments|1|
|2|`constants` should be defined rather than using magic numbers|18|
|3|`event` is missing `indexed` fields|10|

Total: **29** instances over **3** issues

## Unused `override` function arguments
For functions declared as `override`, unused arguments should have the variable name removed or commented out to avoid compiler warnings.

_There is **1** instance of this issue:_  
```solidity
src/manager/Manager.sol:
 209:        function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
```

## `constants` should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) code can benefit from using readable constants instead of hex/numeric literals.

_There are **18** instances of this issue:_  
`96`:
```solidity
src/manager/Manager.sol:
 123:        bytes32 salt = bytes32(uint256(uint160(token)) << 96); 
```
`96`:
```solidity
src/manager/Manager.sol:
 165:        bytes32 salt = bytes32(uint256(uint160(_token)) << 96);
```
`0xff`:
```solidity
src/manager/Manager.sol:
 167:        metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
```
`0xff`:
```solidity
src/manager/Manager.sol:
 168:        auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
```
`0xff`:
```solidity
src/manager/Manager.sol:
 169:        treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
```
`0xff`:
```solidity
src/manager/Manager.sol:
 170:        governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
```
`10_000`:
```solidity
src/governance/governor/Governor.sol:
 468:        return (settings.token.totalSupply() * settings.proposalThresholdBps) / 10_000;
```
`10_000`:
```solidity
src/governance/governor/Governor.sol:
 475:        return (settings.token.totalSupply() * settings.quorumThresholdBps) / 10_000;
```
`2 weeks`:
```solidity
src/governance/treasury/Treasury.sol:
  57:        settings.gracePeriod = 2 weeks;
```
`16`:
```solidity
src/token/metadata/MetadataRenderer.sol:
 179:        uint16[16] storage tokenAttributes = attributes[_tokenId];
```
`16`:
```solidity
src/token/metadata/MetadataRenderer.sol:
 197:        seed >>= 16;
```
`20`:
```solidity
src/token/metadata/MetadataRenderer.sol:
 210:        Strings.toHexString(uint256(uint160(address(this))), 20),
```
`16`:
```solidity
src/token/metadata/MetadataRenderer.sol:
 216:        uint16[16] memory tokenAttributes = attributes[_tokenId];
```
`0x01ffc9a7`:
```solidity
src/lib/token/ERC721.sol:
  63:        _interfaceId == 0x01ffc9a7 || // ERC165 Interface ID
```
`0x80ac58cd`:
```solidity
src/lib/token/ERC721.sol:
  64:        _interfaceId == 0x80ac58cd || // ERC721 Interface ID
```
`0x5b5e139f`:
```solidity
src/lib/token/ERC721.sol:
  65:        _interfaceId == 0x5b5e139f; // ERC721Metadata Interface ID
```
`96`:
```solidity
src/lib/utils/Address.sol:
  26:        return bytes32(uint256(uint160(_account)) << 96);
```
`5 minutes`:
```solidity
src/auction/Auction.sol:
  80:        settings.timeBuffer = 5 minutes;
```

## `event` is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields.
  
_There are **10** instances of this issue:_  
```solidity
src/manager/IManager.sol:
  21:        event DAODeployed(address token, address metadata, address auction, address treasury, address governor);

src/governance/governor/IGovernor.sol:
  18:        event ProposalCreated(
  19:            bytes32 proposalId,
  20:            address[] targets,
  21:            uint256[] values,
  22:            bytes[] calldatas,
  23:            string description,
  24:            bytes32 descriptionHash,
  25:            Proposal proposal
  26:        );

  42:        event VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason);

src/governance/treasury/ITreasury.sol:
  22:        event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);

src/token/IToken.sol:
  21:        event MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder);

src/lib/interfaces/IERC721Votes.sol:
  19:        event DelegateVotesChanged(address indexed delegate, uint256 prevTotalVotes, uint256 newTotalVotes);

src/lib/interfaces/IERC721.sol:
  28:        event ApprovalForAll(address indexed owner, address indexed operator, bool approved);

src/auction/IAuction.sol:
  22:        event AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime);
  28:        event AuctionSettled(uint256 tokenId, address winner, uint256 amount);
  34:        event AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime);
```
