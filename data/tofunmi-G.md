
- Saving the **_settings_** state variable into memory, instead of calling directly from the state can save up to 400+ gas from **sload()\***
  - affected lines in `createBid(uint256 _tokenId)`
  - [https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L90-L154](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L90-L154)
  - https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L106 :if (msg.value < settings.reservePrice) revert RESERVE_PRICE_NOT_MET();
  - https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L119 :minBid = highestBid + ((highestBid \* settings.minBidIncrement) / 100);
  - https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L141 :extend = (\_auction.endTime - block.timestamp) < settings.timeBuffer;
  - https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L149 :auction.endTime = uint40(block.timestamp + settings.timeBuffer);
- recommend mitigation steps
```
     │ File: src/auction/Auction.sol
───────┼─────────────────────────────────────────────────────────────────────────────
  90   │     function createBid(uint256 _tokenId) external payable nonReentrant {
  91   │         // Get a copy of the current auction
  92   │         Auction memory _auction = auction;
    +               settings memory _settings = settings;         
  93   │ 
  94   │         // Ensure the bid is for the current token
  95   │         if (_auction.tokenId != _tokenId) revert INVALID_TOKEN_ID();
  96   │ 
  97   │         // Ensure the auction is still active
  98   │         if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();
  99   │ 
 100   │         // Cache the address of the highest bidder
 101   │         address highestBidder = _auction.highestBidder;
 102   │ 
 103   │         // If this is the first bid:
 104   │         if (highestBidder == address(0)) {
 105   │             // Ensure the bid meets the reserve price
 106 +│             if (msg.value < _settings.reservePrice) revert RESERVE_PRICE_NOT_MET();
 107   │ 
 108   │             // Else this is a subsequent bid:
 109   │         } else {
 110   │             // Cache the highest bid
 111   │             uint256 highestBid = _auction.highestBid;
 112   │ 
 113   │             // Used to store the minimum bid required
 114   │             uint256 minBid;
 115   │ 
 116   │             // Cannot realistically overflow
 117   │             unchecked {
 118   │                 // Compute the minimum bid
 119   │                 minBid = highestBid + ((highestBid * _settings.minBidIncrement) / 100);
 120   │             }
 121   │ 
 122   │             // Ensure the incoming bid meets the minimum
 123   │             if (msg.value < minBid) revert MINIMUM_BID_NOT_MET();
 124   │ 
 125   │             // Refund the previous bidder
 126   │             _handleOutgoingTransfer(highestBidder, highestBid);
 127   │         }
 129   │         // Store the new highest bid
 130   │         auction.highestBid = msg.value;
 131   │ 
 132   │         // Store the new highest bidder
 133   │         auction.highestBidder = msg.sender;
 134   │ 
 135   │         // Used to store if the auction will be extended
 136   │         bool extend;
 137   │ 
 138   │         // Cannot underflow as `_auction.endTime` is ensured to be greater than the current time above
 139   │         unchecked {
 140   │             // Compute whether the time remaining is less than the buffer
 141  +│             extend = (_auction.endTime - block.timestamp) < _settings.timeBuffer;
 142   │         }
 143   │ 
 144   │         // If the time remaining is within the buffer:
 145   │         if (extend) {
 146   │             // Cannot realistically overflow
 147   │             unchecked {
 148   │                 // Extend the auction by the time buffer
 149  +│                 auction.endTime = uint40(block.timestamp + _settings.timeBuffer);
 150   │             }
 151   │         }
 152   │ 
 153   │         emit AuctionBid(_tokenId, msg.sender, msg.value, extend, auction.endTime);
 154   │     }
```