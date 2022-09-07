1. Storing "setting.timebuffer" into uint cache in the function createBid() can save a decent amount of gas.

In the following function, setting.timebuffer should be cached as it is read several times.

Create a cache: uint256 cache = setting.timebuffer and replace them.
src/auction/Auction.sol
(Before) 141: extend = (_auction.endTime - block.timestamp) < settings.timeBuffer;
(After) 141: extend = (_auction.endTime - block.timestamp) < cache;

(Before) 149: auction.endTime = uint40(block.timestamp + settings.timeBuffer);
(After) 149: auction.endTime = uint40(block.timestamp + cache);

2. Storing "auction.settled" into bool cache in the function _settleAuction() can save a decent amount of gas.

In the following function, auction.settled should be cached as it is read several times

Create a cache: bool settled = auction.settled and replace them.
src/auction/Auction.sol
(Before) 172: if (auction.settled) revert AUCTION_SETTLED();
(After) 172: if (settled) revert AUCTION_SETTLED();

(Before) 181: auction.settled = true;
(After) 181: settled = true;

3. Two functions in the Auction contract use a memory location to retrieve auction

Using storage pointer instead of memory location can save a decent amount of gas.

a) The first function is createBid(), make sure to declare the memory location to a storage one

src/auction/Auction.sol
(Before) 92: Auction memory _auction = auction;
(After) 92:  Auction storage _auction = auction;

And replacing the storage cache with these three saves extra gas.
(Before) 130: auction.highestBid = msg.value;
(After) 130: _auction.highestBid = msg.value;

(Before) 133: auction.highestBidder = msg.sender;
(After) 133: _auction.highestBidder = msg.sender;

(Before) 149: auction.endTime = uint40(block.timestamp + settings.timeBuffer);
(After) 149: _auction.endTime = uint40(block.timestamp + settings.timeBuffer);

b) The second function is _settleAuction(), make sure to declared the memory location to a storage one

src/auction/Auction.sol
(Before) 169: Auction memory _auction = auction;
(After) 169: Auction storage _auction = auction;

And replacing the storage cache with these two saves extra gas.
(Before) 172: if (auction.settled) revert AUCTION_SETTLED();
(After) 172: if (_auction.settled) revert AUCTION_SETTLED();

(Before) 181: auction.settled = true;
(After) 181: _auction.settled = true;

// Note that the b) method can't be used at the same time with the "2. Storing "auction.settled" into bool cache ", this here saves a lot more gas than the bool cache. 

4. Boolean comprasions

There is no need to compare constant to (true or false)

src/auction/Auction.sol
(Before) 181: auction.settled = true;
(After) 181: !auction.settled;

5. l am going to do few changes in a function, and l don't want to describe them separately. So l will form them in one report here:

In the function cancle() - https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L353

a) l am going to replace the memory location with storage:
(Before) 358: Proposal memory proposal = proposals[_proposalId];
(After) 358: Proposal storage proposal = proposals[_proposalId];

b) Then l am going to make a new storage cache for the struct "settings" and replace it.
(Add)+ Settings storage _settings = settings
(Before) 371: if (settings.treasury.isQueued(_proposalId)) {
(After) 371: if (_settings.treasury.isQueued(_proposalId)) {
(Before) 373: settings.treasury.cancel(_proposalId);
(After) 373: _settings.treasury.cancel(_proposalId);

c) After that l will use the storage proposal on: 
(Before) 368: proposals[_proposalId].canceled = true;
(After) 368: proposal.canceled = true;

d) And finally there is no need for boolean comprasions, so l am going to delete the "true".
(Before) 368: proposal.canceled = true;
(After) 368: !proposal.canceled;