# QA (LOW & NON-CRITICAL)

## [L-01] Missing SafeCast
Failing to safecast from a greater value type to a lesser one might cause unintended math troubles and there are missing safecast operations with the instances below;

```solidity
141:  extend = (_auction.endTime - block.timestamp) < settings.timeBuffer; //Missing safe casting to uint40
```
[Permalink](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L141)

## [L-02] No indexed events in Auction Contract
There are no indexed events in Auction.sol. This makes the project remain trackable only on one network.

## [L-03] safeTransferFrom to be used instead of transferFrom
It's the user's obligation to prepare his/her smart contract to be able to receive NFT's if he/she participates in an auction via their custom contract. However, it would be safer to use `safeTransferFrom` for the project as well.
```solidity
192:             token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
```
[Permalink](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L192)

## [L-04] Critical address changes

Changing critical addresses in contracts should be a two-step process where the first transaction (from the old/current address) registers the new address (i.e. grants ownership) and the second transaction (from the new address) replaces the old address with the new one (i.e. claims ownership). This gives an opportunity to recover from incorrect addresses mistakenly used in the first step. If not, contract functionality might become inaccessible. [Reference](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/1488), [Reference](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2369)
Critical address changes are not done in two steps for the following instances:
```solidity
250:             transferOwnership(settings.treasury);
```
[Permalink-1](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L250)

```solidity
102:             transferOwnership(settings.treasury);
```
[Permalink-2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L102)

It's safer to use `safeTransferOwnership`.

## [L-05] Checking the success of transfer
The ERC20.transfer() and ERC20.transferFrom() functions return a boolean value indicating success. This parameter needs to be checked for success.
It would be a best practice to wrap them in require statements.
Return values of the transfers are not checked in the below instances;
```solidity
363: IWETH(WETH).transfer(_to, _amount);
```
[Permalink](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L363)


## [L-06] No Storage Gap for Base Contracts
For upgradeable contracts, there must be a storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Otherwise, it may be very difficult to write new implementation code. Without a storage gap, the variable in the child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts.
[Reference](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#storage-gaps)
Recommend adding an appropriate storage gap at the end of upgradeable contracts such as the below. Please reference OpenZeppelin upgradeable contract templates.
```solidity
uint256[50] private __gap;
```

## [L-07] Contract declared Interfaces
AuctionTypesV1.sol, TreasuryTypesV1.sol are declared as contracts. However, they don't have states and they should be declared as interfaces.

## [L-08] Check contract existence
Manager.sol contract has `registerUpgrade` function which offers a new implementation upgrade. However, there is no check whether the offered address exists. 
```solidity
    function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
        isUpgrade[_baseImpl][_upgradeImpl] = true;
        emit UpgradeRegistered(_baseImpl, _upgradeImpl);
    }
```
[Permalink](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L187-L191)
