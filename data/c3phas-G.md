## FINDINGS

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

NB: *Some functions have been truncated where neccessary to just show affected parts of the code*

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L33-L51
### Initializable.sol.initializer(): \_initialized should be cached
```solidity
File: /src/lib/utils/Initializable.sol
33:    modifier initializer() {
34:        bool isTopLevelCall = !_initializing;

36:        if ((!isTopLevelCall || _initialized != 0) && (Address.isContract(address(this)) || _initialized != 1)) revert ALREADY_INITIALIZED();

51:    }
```

```git diff

diff --git a/src/lib/utils/Initializable.sol b/src/lib/utils/Initializable.sol
index c0e8c83..2e5693d 100644
--- a/src/lib/utils/Initializable.sol
+++ b/src/lib/utils/Initializable.sol
@@ -32,8 +32,8 @@ abstract contract Initializable is IInitializable {
     /// @dev Enables initializing upgradeable contracts
     modifier initializer() {
         bool isTopLevelCall = !_initializing;
-
-        if ((!isTopLevelCall || _initialized != 0) && (Address.isContract(address(this)) || _initialized != 1)) revert ALREADY_INITIALIZED();
+        uint8 temp = _initialized;
+        if ((!isTopLevelCall || temp != 0) && (Address.isContract(address(this)) || temp != 1)) revert ALREADY_INITIALIZED();

         _initialized = 1;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L172

## The result of a function call should be cached rather than re-calling the function

External calls are expensive. Consider caching the following:

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L116-L129
### Governor.sol.propose(): proposalThreshold() being called twice despite having a cached value(saves ~1333 gas)
```
Average Gas Before: 61309
Average Gas after: 59706
```

```solidity
File: /src/governance/governor/Governor.sol
116:    function propose(
117:        address[] memory _targets,
118:        uint256[] memory _values,
119:        bytes[] memory _calldatas,
120:       string memory _description
121:    ) external returns (bytes32) {
122:        // Get the current proposal threshold
123:        uint256 currentProposalThreshold = proposalThreshold();//@audit: First proposalThreshold() call

125:        // Cannot realistically underflow and `getVotes` would revert
126:        unchecked {
127:            // Ensure the caller's voting weight is greater than or equal to the threshold
128:            if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();//@audit: Second proposalThreshold() call
129:        }

```

```git diff

diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
index 0d963c5..ce1ad1a 100644
--- a/src/governance/governor/Governor.sol
+++ b/src/governance/governor/Governor.sol
@@ -125,7 +125,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
         // Cannot realistically underflow and `getVotes` would revert
         unchecked {
             // Ensure the caller's voting weight is greater than or equal to the threshold
-            if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
+            if (getVotes(msg.sender, block.timestamp - 1) < currentProposalThreshold) revert BELOW_PROPOSAL_THRESHOLD();
         }
```


## Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L250-L252
```solidity
File: /src/token/metadata/MetadataRenderer.sol
250:    function _generateSeed(uint256 _tokenId) private view returns (uint256) {
251:        return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
252:    }
```
The above is only called once on [Line 176](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L176)

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L255-L262
```solidity
File: /src/token/metadata/MetadataRenderer.sol
255:    function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {
```

The above is only called on [Line 244](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L244)

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L71
```solidity
File: /src/token/Token.sol
71:     function _addFounders(IManager.FounderParams[] calldata _founders) internal {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L130
```solidity
File: /src/token/Token.sol
130:    function _getNextTokenId(uint256 _tokenId) internal view returns (uint256) {
```
The above is only used on [Line 110](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L110)

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L177
```solidity
File: /src/token/Token.sol
177:    function _isForFounder(uint256 _tokenId) private returns (bool) {
```
The above is only used on [Line 157](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L157)

### Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it
Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L177-L199
### Token.sol.\_isForFounder(): tokenRecipient[baseTokenId] should be cached
```solidity
File: /src/token/Token.sol
177:    function _isForFounder(uint256 _tokenId) private returns (bool) {

181:        // If there is no scheduled recipient:
182:        if (tokenRecipient[baseTokenId].wallet == address(0)) {
183:            return false;

185:            // Else if the founder is still vesting:
186:        } else if (block.timestamp < tokenRecipient[baseTokenId].vestExpiry) {
187:            // Mint the token to the founder
188:            _mint(tokenRecipient[baseTokenId].wallet, _tokenId);

190:            return true;

199:    }
```

###  Using calldata instead of memory for read-only arguments in external functions saves gas(See audit tags in the code)
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it's still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is public but can be marked as external since it's not called by the contract, and cases where a constructor is involved

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L116-L121

```solidity
File: /src/governance/governor/Governor.sol
//@audit: use calldata for the variables _targets,_values,_calldatas, _description
116:    function propose(
117:        address[] memory _targets,
118:        uint256[] memory _values,
119:        bytes[] memory _calldatas,
120:        string memory _description
121:    ) external returns (bytes32) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L192-L198
```solidity
File: /src/governance/governor/Governor.sol

//@audit: use calldata for the variables _reason
192:    function castVoteWithReason(
193:        bytes32 _proposalId,
194:        uint256 _support,
195:        string memory _reason
196:    ) external returns (uint256) {
197:        return _castVote(_proposalId, msg.sender, _support, _reason);
198:    }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L324-L329
```solidity
File: /src/governance/governor/Governor.sol

//@audit: use calldata for the variables _targets,_values,_calldatas
324:    function execute(
325:        address[] calldata _targets,
326:        uint256[] calldata _values,
327:        bytes[] calldata _calldatas,
328:        bytes32 _descriptionHash
329:    ) external payable returns (bytes32) {

```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L101-L108
```solidity
File: /src/governance/treasury/Treasury.sol

//@audit: use calldata for the variables _targets,_values,_calldatas
101:    function hashProposal(
102:        address[] calldata _targets,
103:        uint256[] calldata _values,
104:        bytes[] calldata _calldatas,
105:        bytes32 _descriptionHash
106:    ) public pure returns (bytes32) {
107:        return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
108:    }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L141-L146
```solidity
File: /src/governance/treasury/Treasury.sol

//@audit: use calldata for the variables _targets,_values,_calldatas
141:    function execute(
142:        address[] calldata _targets,
143:        uint256[] calldata _values,
144:        bytes[] calldata _calldatas,
145:        bytes32 _descriptionHash
146:    ) external payable onlyOwner {
```


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L347-L351
```solidity
File: /src/token/metadata/MetadataRenderer.sol

//@audit: use calldata for the variable _newContractImage
347:    function updateContractImage(string memory _newContractImage) external onlyOwner {
348:        emit ContractImageUpdated(settings.contractImage, _newContractImage);
            ...
350:        settings.contractImage = _newContractImage;
351:    }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L355-L359
```solidity
File: /src/token/metadata/MetadataRenderer.sol

//@audit: use calldata for the variable _newRendererBase
355:    function updateRendererBase(string memory _newRendererBase) external onlyOwner {
356:        emit RendererBaseUpdated(settings.rendererBase, _newRendererBase);
            ...
358:        settings.rendererBase = _newRendererBase;
359:    }
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L363-L367
```solidity
File: /src/token/metadata/MetadataRenderer.sol

//@audit: use calldata for the variable _newDescription
363:    function updateDescription(string memory _newDescription) external onlyOwner {
364:        emit DescriptionUpdated(settings.description, _newDescription);
            ...
366:        settings.description = _newDescription;
367:    }
```


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L55-L58
```solidity
File: /src/lib/proxy/UUPS.sol

//@audit: use calldata for _data
55:    function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {
56:        _authorizeUpgrade(_newImpl);
57:        _upgradeToAndCallUUPS(_newImpl, _data, true);
58:    }

```


## Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/storage/TokenStorageV1.sol#L15-L19
```solidity
File: /src/token/storage/TokenStorageV1.sol
15:    mapping(uint256 => Founder) internal founder;
   
19:    mapping(uint256 => Founder) internal tokenRecipient;
```

### Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L92
```solidity
File: /src/auction/Auction.sol
92:        Auction memory _auction = auction;

```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L358
```solidity
File: /src/governance/governor/Governor.sol
358:        Proposal memory proposal = proposals[_proposalId];
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L415
```solidity
File: /src/governance/governor/Governor.sol
415:        Proposal memory proposal = proposals[_proposalId];

508:        Proposal memory proposal = proposals[_proposalId];
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L169
```solidity
File: /src/auction/Auction.sol
169:        Auction memory _auction = auction;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L216
```solidity
File: /src/token/metadata/MetadataRenderer.sol
216:        uint16[16] memory tokenAttributes = attributes[_tokenId];
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L234
```solidity
File: /src/token/metadata/MetadataRenderer.sol
234:                Property memory property = properties[i];
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L240
```solidity
File: /src/token/metadata/MetadataRenderer.sol
240:                Item memory item = property.items[attribute];
```



### Use Shift Right/Left instead of Division/Multiplication
While the DIV / MUL opcode uses 5 gas, the SHR / SHL opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version 0.8+
```
    Use >> 1 instead of / 2
```
[relevant source](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L95
```solidity
File: /src/lib/token/ERC721Votes.sol
95:                 middle = high - (high - low) / 2;
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

 When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L208-L216
```solidity
File: /src/governance/governor/Governor.sol

//@audit: uint8 _v
208:    function castVoteBySig(
209:        address _voter,
210:        bytes32 _proposalId,
211:        uint256 _support,
212:        uint256 _deadline,
213:        uint8 _v,
214:        bytes32 _r,
215:        bytes32 _s
216:    ) external returns (uint256) {
```


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L144-L151
```solidity
File: /src/lib/token/ERC721Votes.sol

//@audit: uint8 _v
144:    function delegateBySig(
145:        address _from,
146:        address _to,
147:        uint256 _deadline,
148:        uint8 _v,
149:        bytes32 _r,
150:        bytes32 _s
151:    ) external {
```

### Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.


**consequences:**
-    Each usage of a "constant" costs ~100gas more on each access (it is still a little better than storing the result in storage, but not much..)

-   Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library )
 
See: ethereum/solidity#9232

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L27
```solidity
File: /src/governance/governor/Governor.sol
27:    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");

```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L19
```solidity
File: /src/lib/utils/EIP712.sol
19:    bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L21
```solidity
File: /src/lib/token/ERC721Votes.sol
21:    bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
```

**Proof: can be tested on remix also, showing results for foundry** 
```solidity
pragma solidity ^0.8.6;
contract Constant{
    bytes32 constant noteDenom = keccak256(bytes("NOTE"));
    function doConstant() external view returns(bytes32){
        return noteDenom;
    }
}
contract Immutable{
    bytes32 immutable noteDenom = keccak256(bytes("NOTE"));
    function doImmutable() external view returns(bytes32){
        return noteDenom;
    }
}
```

**Results from :** `forge test --gas-report --optimize`

```solidity
Running 1 test for test/Constant.t.sol:ConstantTest
[PASS] testdoThing() (gas: 5416)
Test result: ok. 1 passed; 0 failed; finished in 420.08µs

Running 1 test for test/Immutable.t.sol:ImmutableTest
[PASS] testdoThing() (gas: 5356)
Test result: ok. 1 passed; 0 failed; finished in 389.91µs
```

### Using bools for storage incurs overhead
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/storage/GovernorStorageV1.sol#L19

```solidity
File: /src/governance/governor/storage/GovernorStorageV1.sol
19:    mapping(bytes32 => mapping(address => bool)) internal hasVoted;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/storage/ManagerStorageV1.sol#L10
```solidity
File: /src/manager/storage/ManagerStorageV1.sol
10:    mapping(address => mapping(address => bool)) internal isUpgrade;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L20
```solidity
File: /src/lib/utils/Initializable.sol
20:    bool internal _initializing;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Pausable.sol#L15
```solidity
File: /src/lib/utils/Pausable.sol
15:    bool internal _paused;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L38
```solidity
File: /src/lib/token/ERC721.sol
38:    mapping(address => mapping(address => bool)) internal operatorApprovals;
```

### Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L27
```solidity
File: /src/governance/governor/Governor.sol
27:    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```
