1. There are two issues in the function createBid (src/auction/Auction.sol)

a) In the if statement "minBid" is set to be greater than the msg.value, which is wrong.
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L123
Example: first let's calculate how much is the minBid with the formula " minBid = highestBid + ((highestBid * settings.minBidIncrement) / 100)".
The settings.minBidIncrement is 10 , and let's say the highestBid is random number like 50, everything adds up to 5,5, which needs to be the minimum value bidded over the current highest bid 50. But If we bid with the minBid, it will revert because the msg.value is set to be greater than the minBid in the if statement. In the statement's comment above, it says that the if statement ensures the incoming bid meets the minimum and yet the msg.value needs to be greater than the minBid. Recommend the "<" to be replaced with "<=", as the way it is now the custom error's name "MINIMUM_BID_NOT_MET()" and the comment "// Ensure the incoming bid meets the minimum" is misleading.

b) same goes for the if statement above.
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L106
settings.reservePrice is the reserve price of each auction, the if statement's comment again ensure the bid meets the reserve price and yet again the msg.value needs to be greater than the reserver price. Again l recommend to replace "<" with "<=", because it leads to misleading the custom error's name "RESERVE_PRICE_NOT_MET()" and comment "// Ensure the bid meets the reserve price"
