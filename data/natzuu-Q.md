## **L001 - Unsafe ERC20 Operation(s)**

### **Description**

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's `SafeERC20` library or at least to wrap each operation in a `require` statement.

To circumvent ERC20's `approve` functions race-condition vulnerability use OpenZeppelin's `SafeERC20` library's `safe{Increase|Decrease}Allowance` functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

```solidity
token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
```

[https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L192](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L192)

```solidity
IWETH(WETH).transfer(_to, _amount);
```

[https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L363](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L363)