# QA Report for Nouns Builder contest

## Overview
During the audit, 3 low and 5 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | [Storage variables can be packed tightly](#l-1-storage-variables-can-be-packed-tightly) | Low | 2
L-2 | [Large number of items may cause out-of-gas error](#l-2-large-number-of-items-may-cause-out-of-gas-error) | Low | 4
L-3 | [Empty function bodies](#l-3-empty-function-bodies) | Low | 3
NC-1 | [Public functions can be external](#nc-1-public-functions-can-be-external) | Non-Critical | 18
NC-2 | [Order of Functions](#nc-2-order-of-functions) | Non-Critical | 13
NC-3 | [Scientific notation may be used](#nc-3-scientific-notation-may-be-used) | Non-Critical | 1
NC-4 | [Missing NatSpec](#nc-4-missing-natspec) | Non-Critical | 10
NC-5 | [Inconsistent use of named return variables](#nc-5-inconsistent-use-of-named-return-variables) | Non-Critical | 

## Low Risk Findings (3)
### L-1. Storage variables can be packed tightly
##### Description
According to [docs](https://docs.soliditylang.org/en/v0.8.16/internals/layout_in_storage.html#layout-of-state-variables-in-storage), multiple, contiguous items that need less than 32 bytes are packed into a single storage slot if possible. It might be beneficial to use reduced-size types if you are dealing with storage values because the compiler will pack multiple elements into one storage slot, and thus, combine multiple reads or writes into a single operation. 

##### Instances
- 1. [Link to code](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/types/GovernorTypesV1.sol#L21-L25)
```
        uint16 proposalThresholdBps;
        uint16 quorumThresholdBps;
        Treasury treasury;
        uint48 votingDelay;
        uint48 votingPeriod;
```
- 2. [Link to code](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/types/TokenTypesV1.sol#L18-L21)
```
        uint96 totalSupply;
        IBaseMetadata metadataRenderer;
        uint8 numFounders;
        uint8 totalOwnership;
```

##### Recommendation
Consider changing order of variables to:
- 1.
```
        Treasury treasury;
        //PACKED
        uint16 proposalThresholdBps;
        uint16 quorumThresholdBps;
        uint48 votingDelay;
        uint48 votingPeriod;
```
- 2.
```
        IBaseMetadata metadataRenderer;
        //PACKED
        uint96 totalSupply;
        uint8 numFounders;
        uint8 totalOwnership;
```

#
### L-2. Large number of items may cause out-of-gas error
##### Description
[Loops](https://docs.soliditylang.org/en/develop/security-considerations.html#gas-limit-and-loops) that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully: Due to the block gas limit, transactions can only consume a certain amount of gas. Either explicitly or just due to normal operation, the number of iterations in a loop can grow beyond the block gas limit, which can cause the complete contract to be stalled at a certain point. 

##### Instances
- [```for (uint256 i = 0; i < numTargets; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162) 
- [```for (uint256 i = 0; i < numNewProperties; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L119) 
- [```for (uint256 i = 0; i < numNewItems; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L133) 
- [```for (uint256 i; i < numFounders; ++i) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L80) 

##### Recommendation
Restrict the maximum number of items in loop.

#
### L-3. Empty function bodies
##### Instances
- [```function tokenURI(uint256 _tokenId) public view virtual returns (string memory) {}```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L54)
- [```function contractURI() public view virtual returns (string memory) {}```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L57)
- [```function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L209)

## Non-Critical Risk Findings (5)
### NC-1. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external.

##### Instances
- [```function onERC721Received(```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L237)
- [```function onERC1155Received(```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L247)
- [```function onERC1155BatchReceived(```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L258)
- [```function tokenURI(uint256 _tokenId) public view virtual returns (string memory) {}```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L54)
- [```function contractURI() public view virtual returns (string memory) {}```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L57)
- [```function balanceOf(address _owner) public view returns (uint256) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L83)
- [```function ownerOf(uint256 _tokenId) public view returns (address) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L91)
- [```function getVotes(address _account) public view returns (uint256) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L45)
- [```function getPastVotes(address _account, uint256 _timestamp) public view returns (uint256) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L59)
- [```function DOMAIN_SEPARATOR() public view returns (bytes32) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L63)
- [```function owner() public view returns (address) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L52)
- [```function pendingOwner() public view returns (address) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L57)
- [```function transferOwnership(address _newOwner) public onlyOwner {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L63)
- [```function safeTransferOwnership(address _newOwner) public onlyOwner {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L71)
- [```function acceptOwnership() public onlyPendingOwner {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L78)
- [```function cancelOwnershipTransfer() public onlyOwner {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L87)
- [```function tokenURI(uint256 _tokenId) public view override(IToken, ERC721) returns (string memory) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L221)
- [```function contractURI() public view override(IToken, ERC721) returns (string memory) {```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L226)

##### Recommendation
Make public functions external, where possible.

#
### NC-2. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private

##### Instances
1) [receive function in wrong place](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L269)
2) [internal functions before public](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L48) and [(2)](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L45)
3) [public and internal functions before external](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L413-L477) and [(2)](
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L54-L57), [(3)](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L43), [(4)](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L47)
4) [private and external functions between internal](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L76) and [(2)](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L247), [(3)](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Pausable.sol#L44)
5) [public functions between external](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L83-L91) and [(2)](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L129)
6) [modifier after functions](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/ReentrancyGuard.sol#L39)

##### Recommendation
Reorder functions where possible.

#
### NC-3. Scientific notation may be used
##### Description
For readability, it is better to use scientific notation.

##### Instances
[```success := call(50000, _to, _amount, 0, 0, 0, 0)```](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L354)

##### Recommendation
Replace ```50000``` with ```50e4```.

#
### NC-4. Missing NatSpec
##### Description
NatSpec is missing for 10 functions in 2 contracts.

##### Instances
- [7 functions in SafeCast.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol)
- [3 functions in TokenReceiver.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol)

##### Recommendation
Add NatSpec for all functions.

#
### NC-5. Inconsistent use of named return variables
##### Description
Some functions return named variables, others return explicit values.

##### Instances
For [example](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L126-L131):
```
    /// @notice The minimum percentage an incoming bid must raise the highest bid
    function minBidIncrement() external view returns (uint256);


    /// @notice Updates the time duration of each auction
    /// @param duration The new time duration
    function setDuration(uint256 duration) external;
```