# Gas Optimizations
## [G-01] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_There is 1 instance of this issue:_

```solidity
File: auction/Auction.sol     #1

363:        IWETH(WETH).transfer(_to, _amount);     // @audit-gas - State variables (WETH) should be cached
```


## [G-02] Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the ABI decoding begins with copying each index of the `calldata` to `memory` in a for loop, with **each iteration of costing at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

It is still more gas-efficient to use `calldata` when the `external` function uses modifiers because the modifiers may prevent the `internal` functions from being called if the array is passed to an `internal` function that then passes the array to another `internal` function where the array is modified and `memory` is used in the `external` call.

Consider replacing `memory` with `calldata` here:

_There are 3 instance(s) of this issue:_

[IGovernor#hashProposal](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/IGovernor.sol#L135-L137)

```solidity
File: governance/governor/IGovernor.sol     #1

135:        address[] memory targets,
136:        uint256[] memory values,
137:        bytes[] memory calldatas,
```



[IGovernor#propose](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/IGovernor.sol#L147-L149)

```solidity
File: governance/governor/IGovernor.sol     #2

147:        address[] memory targets,
148:        uint256[] memory values,
149:        bytes[] memory calldatas,
```

[IGovernor#execute](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/IGovernor.sol#L196-L198)

```solidity
File: governance/governor/IGovernor.sol     #3

196:        address[] memory targets,
197:        uint256[] memory values,
198:        bytes[] memory calldatas,
```

## [G-03] Using a solidity version of at least 0.8.10 to have `external` calls skip contract existence checks if the `external` call has a return value

_There are 11 instance(s) of this issue:_

[IEIP712](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/interfaces/IEIP712.sol#L2)

```solidity
File: lib/interfaces/IEIP712.sol     #1

2:        pragma solidity ^0.8.4;
```
[IERC721](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/interfaces/IERC721.sol#L2)

```solidity
File: lib/interfaces/IERC721.sol     #2

2:        pragma solidity ^0.8.4;
```
[IERC721Votes](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/interfaces/IERC721Votes.sol#L2)

```solidity
File: lib/interfaces/IERC721Votes.sol     #3

2:        pragma solidity ^0.8.4;
```
[IOwnable](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/interfaces/IOwnable.sol#L2)

```solidity
File: lib/interfaces/IOwnable.sol     #4

2:        pragma solidity ^0.8.4;
```
[IPausable](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/interfaces/IPausable.sol#L2)

```solidity
File: lib/interfaces/IPausable.sol     #5

2:        pragma solidity ^0.8.4;
```
[UUPS](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L2)

```solidity
File: lib/proxy/UUPS.sol     #6

2:        pragma solidity ^0.8.4;
```
[ERC721](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L2)

```solidity
File: lib/token/ERC721.sol     #7

2:        pragma solidity ^0.8.4;
```
[ERC721Votes](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L2)

```solidity
File: lib/token/ERC721Votes.sol     #8

2:        pragma solidity ^0.8.4;
```
[EIP712](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L2)

```solidity
File: lib/utils/EIP712.sol     #9

2:        pragma solidity ^0.8.4;
```
[Pausable](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Pausable.sol#L2)

```solidity
File: lib/utils/Pausable.sol     #10

2:        pragma solidity ^0.8.4;
```
[TokenReceiver](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/TokenReceiver.sol#L2)

```solidity
File: lib/utils/TokenReceiver.sol     #11

2:        pragma solidity ^0.8.4;
```

## [G-04] Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. The `DIV` opcode costs **5 gas**, whereas `SHR` only costs **3 gas**.

_There is 1 instance of this issue:_

[ERC721Votes#getPastVotes](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L95)

```solidity
File: lib/token/ERC721Votes.sol     #1

95:        middle = high - (high - low) / 2;
```

## [G-05] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a regular user attemps to pay the function. Marking the function as payable will reduce the gas cost for legitimate callers because the compiler won't add checks for whether a payment was made.

The extra opcodes avoided are:
`CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost.

_There are 24 instance(s) of this issue:_

[Auction](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L244)

```solidity
File: auction/Auction.sol

244:        function unpause() external onlyOwner {

263:        function pause() external onlyOwner {

307:        function setDuration(uint256 _duration) external onlyOwner {

315:        function setReservePrice(uint256 _reservePrice) external onlyOwner {

323:        function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {

331:        function setMinimumBidIncrement(uint256 _percentage) external onlyOwner { 
```
[Governor](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L564)

```solidity
File: governance/governor/Governor.sol

564:        function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner { 

572:        function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {

580:        function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {

588:        function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {

596:        function updateVetoer(address _newVetoer) external onlyOwner {

605:        function burnVetoer() external onlyOwner {
```

[Treasury](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L116)

```solidity
File: governance/treasury/Treasury.sol

116:        function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta) { 

180:        function cancel(bytes32 _proposalId) external onlyOwner {
```
[Ownable](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L63)

```solidity
File: lib/utils/Ownable.sol

63:        function transferOwnership(address _newOwner) public onlyOwner {

71:        function safeTransferOwnership(address _newOwner) public onlyOwner {

78:        function acceptOwnership() public onlyPendingOwner {

87:        function cancelOwnershipTransfer() public onlyOwner {
```

[Manager](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L187)

```solidity
File: manager/Manager.sol

187:       function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {

196:        function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
```

[MetadataRenderer](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L91-L95)

```solidity
File: token/metadata/MetadataRenderer.sol

91:         function addProperties(
92:                string[] calldata _names,
93:                ItemParam[] calldata _items,
94:                IPFSGroup calldata _ipfsGroup
95:            ) external onlyOwner {

347:        function updateContractImage(string memory _newContractImage) external onlyOwner {

355:        function updateRendererBase(string memory _newRendererBase) external onlyOwner {

363:        function updateDescription(string memory _newDescription) external onlyOwner { 
```