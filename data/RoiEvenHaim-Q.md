# Qa 
### Unsafe ERC20 Operation(s)

### Examples
```
2022-09-nouns-builder\src\auction\Auction.sol::192 => token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
2022-09-nouns-builder\src\auction\Auction.sol::363 => IWETH(WETH).transfer(_to, _amount);
```

