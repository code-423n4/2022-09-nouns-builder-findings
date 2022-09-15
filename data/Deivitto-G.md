# GAS
## Constants expressions are expressions, not constants.
### Summary
Constant expressions are left as expressions, not constants.
### Details
Reference to this kind of issue: https://github.com/ethereum/solidity/issues/9232
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L27
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L21
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/EIP712.sol#L19
### Mitigation
Use immutable for this expressions



## Public function visibility can be made external
### Summary
Functions should have the strictest visibility possible. Public functions may lead to more gas usage by forcing the copy of their parameters to memory from calldata.
### Details
If a function is never called from the contract it should be marked as external. This will save gas.
### Github Permalinks
- governance
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L237-L244
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L247-L255
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L258-L266

- lib
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721.sol#L54
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721.sol#L57
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721.sol#L91-L97
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L45-L54
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L59-L118
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L52-L54
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L57-L59
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L63-L67
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L71-L75
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L87-L91
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L78-L84
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/EIP712.sol#L63-L65



### Mitigation
Consider changing visibility from public to external.


## usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
### Summary
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Details
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 
Use a larger size than downcast where needed

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Initializable.sol#L17
    `uint8 internal _initialized;`

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Initializable.sol#L17
    `uint8 internal _initialized;`


### Mitigation
Consider using some data type that uses 32 bytes, for example uint256


## Using bools for storage incurs overhead { 
### Summary
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### Details
Here is one example of OpenZeppelin about this optimization
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Pausable.sol#L15
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Initializable.sol#L20
### Mitigation
Consider using uint256 with values 0 and 1 rather than bool



## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/storage/GovernorStorageV1.sol#L13-L19
- - - 
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/token/ERC721.sol#L26
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/token/ERC721.sol#L34
- - -
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/token/ERC721.sol#L30
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/token/ERC721.sol#L38
- - -
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L29-L37
### Mitigation
Consider mixing different mappings into a struct when able in order to save gas.



## Using private rather than public for constants saves gas
### Summary
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L27
`bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");`

### Mitigation
Consider replacing public for private in constants for gas saving.

## Index initialized in for loop
### Summary
In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L119
            for (uint256 i = 0; i < numNewProperties; ++i) {

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L133
            for (uint256 i = 0; i < numNewItems; ++i) {

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L189
            for (uint256 i = 0; i < numProperties; ++i) {

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L229
            for (uint256 i = 0; i < numProperties; ++i) {

### Mitigation
Don't initialize variables to default value







## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
newFounder
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L113-L115

### Mitigation
Cache variables used more than one into a local variable.


## Shift right instead of dividing by 2
### Summary
Shifting is cheaper than dividing by 2

### Details
A division by 2 can be calculated by shifting one to the right.
While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity’s division operation also includes a division-by-0 prevention which is bypassed using shifting.

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L95
               ` middle = high - (high - low) / 2;`

### Mitigation
Consider replacing / 2 with >> 1 here


## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/token/ERC721.sol#L47-L50
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L130
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L71
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/token/ERC721.sol#L211
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Address.sol#L37
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Address.sol#L46
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Pausable.sol#L39
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Pausable.sol#L56
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/SafeCast.sol#L45

### Mitigation
Consider changing internal function only called once to inline code for gas savings


## abi.encode() is less gas efficient than abi.encodePacked()
### Summary
In general, abi.encodePacked is more gas-efficient.

### Details
Changing the abi.encode function to abi.encodePacked can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient.
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/EIP712.sol#L69
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L104
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L230
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L107
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L162
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L68-L71
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L251
### Mitigation
Consider changing abi.encode to abi.encodePacked



## Functions guaranteed to revert when called by normal users can be marked payable
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L244

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L263

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L307

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L315

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L323

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L331

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L374

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L564

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L572

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L580

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L588

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L596

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L605

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L618

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L63

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L71

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Ownable.sol#L87

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L187

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L196

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L209

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L95

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L347

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L355

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L363

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L376
### Mitigation
It's suggested to add payable to functions guaranteed to revert when called by normal users to improve gas costs


## >= cheaper than >
### Summary
Strict inequalities ( > ) are more expensive than non-strict ones ( >= ). This is due to some supplementary checks (ISZERO, 3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/proxy/ERC1967Upgrade.sol#L61
        if (_data.length > 0 || _forceCall) {

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L203
            if (_from != _to && _amount > 0) {

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Address.sol#L50
            if (_returndata.length > 0) {
### Mitigation
Consider using >= 1 instead of > 0 to avoid some opcodes


## <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables
### Summary
x+=y costs more gas than x=x+y for state variables

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L280
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L285
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L290

### Mitigation
Don't use += for state variables as it cost more gas.

## Use calldata instead of memory for function parameters
### Summary
It is generally cheaper to load variables directly from calldata, rather than copying them to memory. 
### Details
Only use memory if the variable needs to be modified
### Github Permalinks
- Contracts
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L116-L121
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L192-L198
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/proxy/UUPS.sol#L54-L58
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L347-L367

- Interfaces
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/IGovernor.sol#L134-L151
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/IGovernor.sol#L162-L166
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/IGovernor.sol#L195-L200
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/interfaces/IUUPS.sol#L35
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L74-L84

### Mitigation
Use calldata rather than memory in external functions where the parameter is not modified but only read

