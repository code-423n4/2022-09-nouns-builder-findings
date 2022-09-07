# 2022-09-NOUNS-BUILDER
## Low Risk and Non-Critical Issues

### Events not emmited on important state changes
Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

_There are **4** instances of this issue:_

```solidity
File: src/lib/token/ERC721.sol

47:   function __ERC721_init(string memory _name, string memory _symbol) internal onlyInitializing {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol

```solidity
File: src/lib/utils/EIP712.sol

48:   function __EIP712_init(string memory _name, string memory _version) internal onlyInitializing {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/EIP712.sol

```solidity
File: src/lib/utils/Pausable.sol

39:   function __Pausable_init(bool _initPause) internal onlyInitializing {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Pausable.sol

```solidity
File: src/lib/utils/ReentrancyGuard.sol

34:   function __ReentrancyGuard_init() internal onlyInitializing {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/ReentrancyGuard.sol

### `public` functions not called by the contract should be declared `external` instead

### Not checking for return value after function call
Correct error handling after external calls is impornant and may prevent potential vulnerabilities in code. Could not find anything specific that may open a direct attack vector, but there are multiple places in code where this may be applied.

_There are **11** instances of this issue:_

_There are **14** instances of this issue:_

```solidity
File: src/governance/treasury/Treasury.sol

237:  function onERC721Received(

247:  function onERC1155Received(

258:  function onERC1155BatchReceived(
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol

```solidity
File: src/lib/token/ERC721.sol

83:   function balanceOf(address _owner) public view returns (uint256) {

91:   function ownerOf(uint256 _tokenId) public view returns (address) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol

```solidity
File: src/lib/token/ERC721Votes.sol

45:   function getVotes(address _account) public view returns (uint256) {

59:   function getPastVotes(address _account, uint256 _timestamp) public view returns (uint256) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol

```solidity
File: src/lib/utils/EIP712.sol

63:   function DOMAIN_SEPARATOR() public view returns (bytes32) {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/EIP712.sol

```solidity
File: src/lib/utils/Ownable.sol

52:   function owner() public view returns (address) {

57:   function pendingOwner() public view returns (address) {

63:   function transferOwnership(address _newOwner) public onlyOwner {

71:   function safeTransferOwnership(address _newOwner) public onlyOwner {

78:   function acceptOwnership() public onlyPendingOwner {

87:   function cancelOwnershipTransfer() public onlyOwner {
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol

### Empty `receive()`/`fallback()` functions


_There are **1** instances of this issue:_

```solidity
File: src/governance/treasury/Treasury.sol

269:  receive() external payable {}
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol

### Non-library/interface files should use fixed compiler versions, not floating ones


_There are **13** instances of this issue:_

```solidity
File: src/lib/interfaces/IInitializable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IInitializable.sol

```solidity
File: src/lib/interfaces/IPausable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IPausable.sol

```solidity
File: src/lib/proxy/ERC1967Proxy.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Proxy.sol

```solidity
File: src/lib/proxy/ERC1967Upgrade.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol

```solidity
File: src/lib/proxy/UUPS.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol

```solidity
File: src/lib/token/ERC721.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol

```solidity
File: src/lib/token/ERC721Votes.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol

```solidity
File: src/lib/utils/EIP712.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/EIP712.sol

```solidity
File: src/lib/utils/Initializable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol

```solidity
File: src/lib/utils/Ownable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol

```solidity
File: src/lib/utils/Pausable.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Pausable.sol

```solidity
File: src/lib/utils/ReentrancyGuard.sol

2:    pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/ReentrancyGuard.sol

```solidity
File: src/lib/utils/TokenReceiver.sol

2:    pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/TokenReceiver.sol

### Lack of input validation
Check if input parameters of type `address` equal address(0) and uints do not equal 0 - especially if they are strictly associated with important contract's state changes

_There are **27** instances of this issue:_

### Event is missing `indexed` fields

_There are **9** instances of this issue:_

```solidity
File: src/auction/IAuction.sol

event      AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime)

event      AuctionSettled(uint256 tokenId, address winner, uint256 amount)

event      AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime)
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/IAuction.sol

```solidity
File: src/governance/governor/IGovernor.sol

event      VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason)
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol

```solidity
File: src/governance/treasury/ITreasury.sol

event      TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads)
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/ITreasury.sol

```solidity
File: src/lib/interfaces/IERC721.sol

event      ApprovalForAll(address indexed owner, address indexed operator, bool approved)
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IERC721.sol

```solidity
File: src/lib/interfaces/IERC721Votes.sol

event      DelegateVotesChanged(address indexed delegate, uint256 prevTotalVotes, uint256 newTotalVotes)
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/interfaces/IERC721Votes.sol

```solidity
File: src/manager/IManager.sol

event      DAODeployed(address token, address metadata, address auction, address treasury, address governor)
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/IManager.sol

```solidity
File: src/token/IToken.sol

event      MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder)
```
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/IToken.sol