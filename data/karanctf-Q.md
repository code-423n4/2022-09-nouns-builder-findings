

## \[N-1\] Event is missing `indexed` fields

Each `event` should use three `indexed` fields if there are three or more fields

```solidity
lib/interfaces/IERC721.sol:16:    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
lib/interfaces/IERC721.sol:22:    event Approval(address indexed owner, address indexed approved, uint256 indexed tokenId);
lib/interfaces/IERC721.sol:28:    event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
lib/interfaces/IERC721Votes.sol:16:    event DelegateChanged(address indexed delegator, address indexed from, address indexed to);
lib/interfaces/IERC721Votes.sol:19:    event DelegateVotesChanged(address indexed delegate, uint256 prevTotalVotes, uint256 newTotalVotes);
```

## \[L-1\] Unsafe ERC20 Operation(s)


```solidity
auction/Auction.sol:192 => token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
auction/Auction.sol:363 => IWETH(WETH).transfer(_to, _amount);
```
