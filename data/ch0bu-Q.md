# Non-Critical

## 1. Floating Pragma version

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

 
Proof of Concept
https://swcregistry.io/docs/SWC-103

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IOwnable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IEIP712.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/ReentrancyGuard.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Pausable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IWETH.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IUUPS.sol#L2

## 2. Use a more recent version of Solidity

- Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)` 
- Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`
- Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IWETH.sol#L2
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IUUPS.sol#L2

## 3. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom

It is good to add a `require()` statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

Reference: This similar medium-severity finding from Consensys Diligence Audit of Fei Protocol: https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol
```
192		token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
363		IWETH(WETH).transfer(_to, _amount);
```

## 4. Consider adding checks for signature malleability

Use OpenZeppelin’s `ECDSA` contract rather than calling `ecrecover()` directly

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol
```
236		address recoveredAddress =  ecrecover(digest, _v, _r, _s);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol
```
167		address recoveredAddress =  ecrecover(digest, _v, _r, _s);
```

## 5. Event is missing `indexed` fields

Each `event` should use three `indexed` fields if there are three or more fields.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol
```
21		event MintScheduled(uint256  baseTokenId, uint256  founderId, Founder founder);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol
```
21		event DAODeployed(address  token, address  metadata, address  auction, address  treasury, address  governor);
26		event UpgradeRegistered(address  baseImpl, address  upgradeImpl);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol
```
19		event DelegateVotesChanged(address  indexed  delegate, uint256  prevTotalVotes, uint256  newTotalVotes);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol
```
22		event TransactionExecuted(bytes32 proposalId, address[] targets, uint256[] values, bytes[] payloads);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol
```
42		event VoteCast(address  voter, bytes32  proposalId, uint256  support, uint256  weight, string  reason);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol
```
22		event AuctionBid(uint256  tokenId, address  bidder, uint256  amount, bool  extended, uint256  endTime);
28		event AuctionSettled(uint256  tokenId, address  winner, uint256  amount);
34		event AuctionCreated(uint256  tokenId, uint256  startTime, uint256  endTime);
```
