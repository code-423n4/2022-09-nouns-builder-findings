
---

## Summary

- G-01 Use simple comparison in if statement (2 instances)
- G-02 Multiple address mappings can be combined (5 instances)
- G-03 State variables only set in the constructor should be declared immutable (1 instance)
- G-04 State variables can be packed into fewer storage slots (2 instances)
- G-05 Using calldata instead of memory for read-only arguments in external functions (23 instances)
- G-06 Expressions for constant values should use immutable rather than constant (4 instances)
- G-07 <x> += <y> costs more gas than <x> = <x> + <y> for state variables (4 instances)
- G-08 internal functions only called once can be inlined to save gas (5 instances)
- G-09 Use calldata instead of memory for function parameters (39 instances)
- G-10 ++i/i++ should be unchecked{++i}/unchecked{i++} (9 instances)
- G-11 Public functions to external (22 instances) 
- G-12 Functions guaranteed to revert when called by normal users can be marked payable (17 instances)
- G-13 Empty blocks should be removed or emit something (6 instances)
- G-14 Using bools for storage incurs overhead (13 instances)
- G-15 Use a more recent version of solidity (5 instances)
- G-16 Use != 0 instead of > 0 (2 instances)
- G-17 Redundant zero initialization (4 instances)
- G-18 Bitshift for multiple / divide by 2 (1 instance)
- G-19 Duplicated require()/revert() checks should be refactored to a modifier or function (9 instances)
- G-20 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead (28 instances)
- G-21 abi.encode() is less efficient than abi.encodePacked() (4 instances)
- G-22 Using private rather than public for constants, saves gas (1 instance)

Total: 206 instances in 22 issues.

---

## G-01 Use simple comparison in if statement

The comparison operators >= and <= use more gas than >, <, or ==. Replacing the >= and ≤ operators with a comparison operator that has an opcode in the EVM saves gas. 

Recommended Mitigation Steps: Replace the comparison operator and reverse the logic to save gas using the suggestions above.

2 instances in 2 files:

auction/Auction.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L98

lib/token/ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L61


## G-02 Multiple address mappings can be combined 

Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

5 instances in 3 files:
manager/storage/ManagerStorageV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/storage/ManagerStorageV1.sol#L10

lib/token/ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L38

token/ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L29-L37


## G-03 State variables only set in the constructor should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

1 instance in 1 file:

manager/Manager.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L56-L60 (5)


## G-04 State variables can be packed into fewer storage slots

Solidity contracts have contiguous 32 bytes (256 bits) slots used in storage. By arranging the variables, it is possible to minimize the number of slots used within a contract’s storage and therefore reduce deployment costs. address type variables are each of 20 bytes size (way less than 32 bytes). 

However, they here take up a whole 32 bytes slot (they are contiguous). As bool type variables are of size 1 byte, there’s a slot here that can get saved by moving them closer to an address

There 2 instances in 2 files:

auction/types/AuctionTypesV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L30-L35

governance/governor/types/GovernorTypesV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L43-L54


## G-05 Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. 
Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

23 instances in 5 files:

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L347
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L355
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L363

token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L76
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L80
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L84

governance/governor/Governor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L117-L120 (4)
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L195

governance/governor/IGovernor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L135-L137 (3)
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L147-L150 (4)
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L165
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L196-L198 (3)

lib/proxy/UUPS.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L55


## G-06 Expressions for constant values should use immutable rather than constant

Expressions for constant values such as a call to keccak256(), should use immutable rather than constant.

See this issue for a detail description of the issue https://github.com/ethereum/solidity/issues/9232

4 instances in 3 files:

governance/governor/Governor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27

lib/proxy/ERC1967Upgrade.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L21
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L24

lib/token/ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21



## G-07 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

4 instances in 2 files:

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140

governance/governor/Governor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290


## G-08 internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

5 instances in 3 files:

lib/proxy/ERC1967Upgrade.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L33-L37
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L54-L58

lib/token/ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L235-L239
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L245-L249

lib/token/ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L196-L200


## G-09 Use calldata instead of memory for function parameters

Having function arguments use calldata instead of memory can save gas. 

Recommended Mitigation Steps: Change function arguments from memory to calldata.

39 instances in 5 files:

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L255 (2)
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L308
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L347
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L355
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L363

token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L76
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L80
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L84

governance/governor/Governor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L99-L101 (3)
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L117-L120 (4)
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L195
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L252

governance/governor/IGovernor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L135-L137 (3)
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L147-L150 (4)
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L165
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L196-L198 (3)

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L241
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L252
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L261-L263 (3)

lib/proxy/ERC1967Upgrade.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L35
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L56

lib/token/ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L47 (2)

lib/proxy/UUPS.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L55


## G-10 ++i/i++ should be unchecked{++i}/unchecked{i++} 

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. 
This saves 30-40 gas PER LOOP

9 instances in 3 files:

token/Token.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L80
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L108
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L132
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L261

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229

governance/treasury/Treasury.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162


## G-11 Public functions to external

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

Reference: https://docs.soliditylang.org/en/latest/contracts.html#function-overriding

22 instances in 6 files:

token/Token.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L221
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L226
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L294

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L206

governance/governor/Governor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L98-L103
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L413
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L461
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L466
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L473

governance/treasury/Treasury.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L82
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L88
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L101-L106
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L237-L242
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L247-L253
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L258-L264

lib/token/ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L54
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L57
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L83
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L91
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L125-L129

lib/token/ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L45
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L59


## G-12 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 

The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

17 instances in 4 files:

manager/Manager.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L187
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L196
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L91-L95
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L347
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L355
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L363
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L376

auction/Auction.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L244
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L263
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L374

governance/treasury/Treasury.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L116
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L180


## G-13 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. 

If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

6 instances in 3 files:

manager/Manager.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209

governance/treasury/Treasury.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269

lib/token/ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L54
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L57
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L239
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L249


## G-14 Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. 

This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. 

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 

Recommendation: Use uint256(1) and uint256(2) for true/false

13 instances in 9 files:

manager/storage/ManagerStorageV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/storage/ManagerStorageV1.sol#L10

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L231

token/metadata/types/MetadataRendererTypesV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/types/MetadataRendererTypesV1.sol#L11

auction/Auction.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L136
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L349

auction/types/AuctionTypesV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L35

governance/governor/storage/GovernorStorageV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/storage/GovernorStorageV1.sol#L19

governance/governor/types/GovernorTypesV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L52
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L53
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L54

lib/proxy/ERC1967Upgrade.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L36
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L57

lib/token/ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L115


## G-15 Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath.
Use a solidity version of at least 0.8.2 to get compiler automatic inlining.
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads.
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings.
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. 
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>). 
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions.

5 instances in 5 files:

lib/proxy/ERC1967Proxy.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2

lib/proxy/ERC1967Upgrade.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2

lib/proxy/UUPS.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2

lib/token/ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2

lib/token/ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2


## G-16 Use != 0 instead of > 0

Using > 0 uses slightly more gas than using != 0. Use != 0 when comparing uint variables to zero, which cannot hold values below zero. 

This change saves 6 gas per instance

2 instances in 2 files:

lib/proxy/ERC1967Upgrade.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L61

lib/token/ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L203


## G-17 Redundant zero initialization 

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas. 

There are several places where an int is initialized to zero, which looks like: 
  uint256 amount = 0; 

Recommended Mitigation Steps: 
Remove the redundant zero initialization uint256 amount; ( / or: If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

4 instances in 1 file:

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229


## G-18 Bitshift for multiple / divide by 2

When multiply or dividing by a power of two, it is cheaper to bitshift than to use standard math operations, ie <x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. 

The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

1 instance in 1 file:

lib/token/ERC721Votes.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L95


## G-19 Duplicated require()/revert() checks should be refactored to a modifier or function to save deployment costs

9 instances in 3 files:

token/Token.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L148
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L209

governance/governor/Governor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L70
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L71

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L138
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L139

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L355
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L390

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L212
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L224
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L280

lib/token/ERC721.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L84
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L132

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L94
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L130

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L167
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L185


## G-20 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 

Recommendation: Use a larger size then downcast where needed

28 instances in 8 files:

manager/Manager.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L123
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L167
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L168
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L169
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L170

token/Token.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L88
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L98
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L99
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L123
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L124

token/types/TokenTypesV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L18
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L20
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L21
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L30
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L31

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L185
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L194
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L210

token/metadata/storage/MetadataRendererStorageV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#L20

token/metadata/types/MetadataRendererTypesV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/types/MetadataRendererTypesV1.sol#L20

auction/Auction.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L149
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L223
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L224

auction/types/AuctionTypesV1.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L16
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L17
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L18
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L33
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L34


## G-21 abi.encode() is less efficient than abi.encodePacked()

4 instances in 3 files:

token/metadata/MetadataRenderer.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L251

governance/governor/Governor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L104
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230

governance/treasury/Treasury.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L107


## G-22 Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

1 instance in 1 file:

governance/governor/Governor.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27

