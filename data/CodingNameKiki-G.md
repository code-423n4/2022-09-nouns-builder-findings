1. Storing "setting.timebuffer" into cache in the function createBid() can save a decent amount of gas.

In the following function, setting.timebuffer should be cached as it is read several times.

Create a cache: uint256 cache = setting.timebuffer and replace them.
src/auction/Auction.sol
(Before) 141: extend = (_auction.endTime - block.timestamp) < settings.timeBuffer;
(After) 141: extend = (_auction.endTime - block.timestamp) < cache;

(Before) 149: auction.endTime = uint40(block.timestamp + settings.timeBuffer);
(After) 149: auction.endTime = uint40(block.timestamp + cache);

2. 