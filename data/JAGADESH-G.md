### [G-01] Cache storage values in memory to minimize SLOADs
The code can be optimized by minimising the number of SLOADs. SLOADs are expensive (100 gas) compared to MLOADs/MSTOREs (3 gas)

Storage value should get cached in memory

File: Auction.sol [Line 172](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L172) :  cached variable `_auction` should be used

```solidity
if (_auction.settled) revert AUCTION_SETTLED();
```

File: Auction.sol [Line 248&256](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L244-L260) :  should cache variable `auction`

```solidity
Auction memory _auction = auction
if (_auction.tokenId == 0) {...}
else if (_auction.settled) {...}
```


### [G-02] Emitting storage values instead of the memory one saves gas
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

**File:** Auction.sol [Line 153](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L153) : we should emit  `_auction.endTime`

```solidity
emit AuctionBid(_tokenId, msg.sender, msg.value, extend, _auction.endTime);
```

### [G-03] Using bools for storage incurs overhead
[source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

**Instances:**

[AuctionTypesV1.sol::35](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/types/AuctionTypesV1.sol#L35)       ⇒ `bool settled;`

[Auction.sol::181](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L181)                  ⇒ `auction.settled = true;` 

[GovernorStorageV1.sol::19](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/storage/GovernorStorageV1.sol#L19) ⇒ `mapping(bytes32 => mapping(address => bool)) internal hasVoted;`

[GovernorStorageV1.sol::52,53,54](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/types/GovernorTypesV1.sol#L52-L54) ⇒ `bool executed;` & `bool canceled;` & `bool vetoed;`

[Governor.sol::368](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L368) ⇒ `proposals[_proposalId].canceled = true;`

[Governor.sol::396](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L393) ⇒ `proposal.vetoed = true;`

[ManagerStorageV1.sol::10](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/storage/ManagerStorageV1.sol#L10) ⇒ `mapping(address => mapping(address => bool)) internal isUpgrade;`

### [G-04] Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it’s still more gass-efficient to use `calldata` when the `external`
 function uses modifiers, since the modifiers may prevent the internal 
functions from being called. Structs have the same overhead as an array 
of length one

**Instances:**

File: Governor.sol [Line 117, 118, 119](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L117-L119) :  `memory` array should be changed to `calldata` and variables need to be cached in function

```solidity
address[] calldata _targets,
uint256[] calldata _values,
bytes[] calldata _calldatas,
```

### [G-05] Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. 

Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack 
variables, will be much cheaper, only incuring the Gcoldsload for the 
fields actually read. The only time it makes sense to read the whole 
struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

**Instances:**

File: Governor.sol [Line 358](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L117-L119) :  `Proposal memory proposal = proposals[_proposalId];`

`memory`  should be changed to `storage`

```solidity
Proposal storage proposal = proposals[_proposalId];
```

only 3 fields from proposal struct used in code, so cache the fields necessary `proposal.proposer`

File: Governor.sol [Line 415](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L415) :  `Proposal memory proposal = proposals[_proposalId];`

File: Governor.sol [Line 508](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L508) :  `Proposal memory proposal = proposals[_proposalId];`

File: MetadataRenderer.sol [Line 240](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L240) : `Item memory item = property.items[attribute];`

### [G-06] **Don't Initialize Variables with Default Value**
Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecessary gas.

**Instances:** 

[Treasury.sol::162](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162) ⇒ `for (uint256 i = 0; i < numTargets; ++i) {`

[MetadataRenderer.sol::119](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L119) ⇒ `for (uint256 i = 0; i < numNewProperties; ++i) {`

[MetadataRenderer.sol::133](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L133) => `for (uint256 i = 0; i < numNewItems; ++i) {`

[MetadataRenderer.sol::189](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L189) => `for (uint256 i = 0; i < numProperties; ++i) {`

[MetadataRenderer.sol::229](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L229) ⇒ `for (uint256 i = 0; i < numProperties; ++i) {`

### [G-07] **Using unchecked blocks to save gas**
`++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

**Instances:**

[Treasury.sol::162-168](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162-L168) 

```solidity
for (uint256 i = 0; i < numTargets; ++i) {
				(bool success, ) = _targets[i].call{ value: _values[i] }(_calldatas[i]);
         if (!success) revert EXECUTION_FAILED(i);
}
```

change to

```solidity
for (uint256 i = 0; i < numTargets;) {
				(bool success, ) = _targets[i].call{ value: _values[i] }(_calldatas[i]);
         unchecked {
             ++i;
         }
         if (!success) revert EXECUTION_FAILED(i);
}
```

### [G-8] Gas optimizing through arithmetics
`<x> / 2` is the same as `<x> >> 1`. The `DIV` opcode costs **5 gas**, whereas `SHR` only costs **3 gas**

**Instances:**

[ERC721Votes.sol::95](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L95) ⇒ `middle = high - (high - low) / 2;`

**`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables**

**Instances:**

[MetadataRenderer.sol::L140](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L140) ⇒ `_propertyId += numStoredProperties;`

### [G-09] **Using > 0 costs more gas than != 0 when used on a uint in a require() statement**
When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

**Instances:**

[ERC721Votes.sol::203](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L203) ⇒ `if (_from != _to && _amount > 0) {`

[Address.sol::50](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L50) ⇒ `if (_returndata.length > 0) {`

### [G-10] Use assembly to check for address(0)
eg:

```solidity
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

**Instances:**

[Action.sol::104](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L104)   ⇒ `if (highestBidder == address(0))` 

[Auction.sol::184](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L184) ⇒ `if (_auction.highestBidder != address(0))`

[Auction.sol::228](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L228) ⇒ `auction.highestBidder = address(0);`

[Governor.sol::70&71](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L70-L71) ⇒ `if (_treasury == address(0)) revert ADDRESS_ZERO();` & `if (_token == address(0)) revert ADDRESS_ZERO();`

[Governor.sol::239](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L239) ⇒ `if (recoveredAddress == address(0) || recoveredAddress != _voter)`

[Governor.sol::597](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L597) ⇒ `if (_newVetoer == address(0)) revert ADDRESS_ZERO();`

[Treasury.sol::48](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L48) ⇒ `if (_governor == address(0)) revert ADDRESS_ZERO();`

[ERC721.sol::192](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L192) ⇒ `if (_to == address(0)) revert ADDRESS_ZERO();`

[ERC721.sol::194](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L194) ⇒ `if (owners[_tokenId] != address(0)) revert ALREADY_MINTED();`

[ERC721.sol::196](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L196) ⇒ `_beforeTokenTransfer(address(0), _to, _tokenId);`

[ERC721.sol::204](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L204) ⇒ `emit Transfer(address(0), _to, _tokenId);`

[ERC721.sol::206](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L206) ⇒ `_afterTokenTransfer(address(0), _to, _tokenId);`

[ERC721.sol::214](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L214) ⇒ `if (owner == address(0)) revert NOT_MINTED();`

[ERC721.sol::216](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L216) ⇒ `_beforeTokenTransfer(owner, address(0), _tokenId);`

[ERC721.sol::226](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L226) ⇒ `emit Transfer(owner, address(0), _tokenId);`

[ERC721.sol::228](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L228) ⇒ `_afterTokenTransfer(owner, address(0), _tokenId);`

[ERC721Votes.sol::205](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L205) ⇒ `if (_from != address(0))`

[ERC721Votes.sol::220](https://www.notion.so/Noun-Builder-8f929ff8af4742fba2fd318d55397a93) ⇒ `if (_to != address(0))`

[Manager.sol::82](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L82) ⇒ `if (_owner == address(0)) revert ADDRESS_ZERO();`

[Manager.sol::117](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L117) ⇒ `if ((founder = _founderParams[0].wallet) == address(0)) revert FOUNDER_REQUIRED();`

[Token.sol::132](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L132) ⇒ `while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;`

[Token.sol::182](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L182) ⇒ `if (tokenRecipient[baseTokenId].wallet == address(0)) {`