## Table of Contents
- Change Function Visibility Public to External
- Internal Function Called Only Once can be Inlined
- Use Bit Shifting Instead of Multiplication/Division of 2
- Use Calldata instead of Memory for Read Only Function Parameters
- Constant Value of a Call to keccak256() should Use Immutable
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Unnecessary Default Value Initialization
- ++i Costs Less Gas than i++

&ensp;
### Change Function Visibility Public to External

#### Issue
If the function is not called internally, it is cheaper to set your function visibility to external instead of public.

#### PoC
Total of 6 instances found.

1. owner() of Ownable.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L52

2. pendingOwner() of Ownable.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L57

3. transferOwnership() of Ownable.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L63

4. safeTransferOwnership() of Ownable.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L71

5. acceptOwnership() of Ownable.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L78

6. cancelOwnershipTransfer() of Ownable.sol
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L87

#### Mitigation
Change the function visibility to external.

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined internal even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 2 instances found.

1. _addFounders() of Token.sol
This function is called only once at line 56
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L71
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L56

2. _getNextTokenId() of Token.sol
This function is called only once at line 110
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L130
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L110

#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
### Use Bit Shifting Instead of Multiplication/Division of 2

#### Issue
The MUL and DIV opcodes cost 5 gas but SHL and SHR only costs 3 gas.
Since MUL/DIV and SHL/SHR result the same, use cheaper bit shifting.

#### PoC
Total of 1 instances found.
```solidity
ERC721Votes.sol:95:                middle = high - (high - low) / 2;
```

#### Mitigation
Use bit shifting instead of multiplication/division.
Change to:
```solidity
middle = high - (high - low) >> 2;
```

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 9 instances found.
```
MetadataRenderer.sol:347:    function updateContractImage(string memory _newContractImage) external onlyOwner {
MetadataRenderer.sol:355:    function updateRendererBase(string memory _newRendererBase) external onlyOwner {
MetadataRenderer.sol:363:    function updateDescription(string memory _newDescription) external onlyOwner {
Governor.sol:117:        address[] memory _targets,
Governor.sol:118:        uint256[] memory _values,
Governor.sol:119:        bytes[] memory _calldatas,
Governor.sol:120:        string memory _description
Governor.sol:195:        string memory _reason
UUPS.sol:55:    function upgradeToAndCall(address _newImpl, bytes memory _data) external payable onlyProxy {
```

#### Mitigation
Change memory to calldata

&ensp;
### Constant Value of a Call to keccak256() should Use Immutable

#### Issue
When using constant it is expected that the value should be converted into a constant value at compile time.
However when using a call to keccak256(), the expression is re-calculated each time the constant is referenced.
Resulting in costing about 100 gas more on each access to this constant.
link for more details: https://github.com/ethereum/solidity/issues/9232

#### PoC
Total of 3 instances found.
```
Governor.sol:27:    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
ERC721Votes.sol:21:    bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");
EIP712.sol:19:    bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```

#### Mitigation
Change the variable from constant to immutable.

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size.

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 4 instances found.
```
Initializable.sol:17:    uint8 internal _initialized;
Initializable.sol:55:    modifier reinitializer(uint8 _version) {
Governor.sol:213:        uint8 _v,
ERC721Votes.sol:148:        uint8 _v,
```

#### Mitigation
I suggest using uint256 instead of anything smaller or downcast where needed.

&ensp;
### Unnecessary Default Value Initialization

#### Issue
When variable is not initialized, it will have its default values.
For example, 0 for uint, false for bool and address(0) for address.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#scoping-and-declarations

#### PoC
Total of 5 instances found.
```
MetadataRenderer.sol:119:            for (uint256 i = 0; i < numNewProperties; ++i) {
MetadataRenderer.sol:133:            for (uint256 i = 0; i < numNewItems; ++i) {
MetadataRenderer.sol:189:            for (uint256 i = 0; i < numProperties; ++i) {
MetadataRenderer.sol:229:            for (uint256 i = 0; i < numProperties; ++i) {
Treasury.sol:162:            for (uint256 i = 0; i < numTargets; ++i) {
```

#### Mitigation
I suggest removing default value initialization.
For example,
- for (uint256 i; i < numNewProperties; ++i) {

&ensp;
### ++i Costs Less Gas than i++

#### Issue
Prefix increments/decrements (++i or --i) costs cheaper gas than 
postfix increment/decrements (i++ or i--).

#### PoC
Total of 6 instances found.
```
Token.sol:91:                uint256 founderId = settings.numFounders++;
Token.sol:154:                tokenId = settings.totalSupply++;
Governor.sol:230:                    keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
ERC721Votes.sol:162:                abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
ERC721Votes.sol:207:                    uint256 nCheckpoints = numCheckpoints[_from]++;
ERC721Votes.sol:222:                    uint256 nCheckpoints = numCheckpoints[_to]++;
```

#### Mitigation
Change it to postfix increments/decrements.
It saves about 6 gas per instance.

&ensp;