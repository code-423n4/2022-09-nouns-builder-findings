### MULTIPLE `ADDRESS` MAPPINGS CAN BE COMBINED INTO A SINGLE `MAPPING` OF AN `ADDRESS` TO A `STRUCT`, WHERE APPROPRIATE

_There is 1 instance of this issue:

```solidity
// lib/token/ERC721Votes.sol
29:    mapping(address => address) internal delegation;
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L29

&nbsp;
&nbsp;


### UNUSED/EMPTY `RECEIVE()`/`FALLBACK()` FUNCTION

_There is 1 instance of this issue:
```solidity
// governance/treasury/Treasury.sol
269:    receive() external payable {}
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269

&nbsp;
&nbsp;

### Return values of  `transfer` and `transferFrom` are unchecked
Boolean return value for `transfer` and `transferFrom` is not checked.
*There are 2 instances this issue*

```solidity
// auction/Auction.sol
192:            token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
363:            IWETH(WETH).transfer(_to, _amount);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L192
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L363

