* I think it would be better if changes to essential auction parameters (```setDuration```, ```setReservePrice```, ```setTimeBuffer```, ```setMinimumBidIncrement```, etc) did not impact the current auction. They should take effect only when the next auction round starts. The current ongoing auction should finish with the old settings so that users won't be front-runned with unexpected new settings that they were not bidding on. Or at least these settings should have reasonable upper/lower boundaries to prevent the possibility of griefing.
This is submitted as a QA issue because it was explicitly mentioned: "Wardens should assume that governance variables are set sensibly".

* There are many constructors that are payable for no reason, especially when there is no way to retrieve the native asset later unless the contract is upgraded.

* ```Token``` in ```AuctionStorageV1``` should probably be of an interface type, that is ```IToken```, not implementation:
```solidity
  Token public token;
```

* ```minBid``` can have a minimum value of 1%, consider increasing bps to allow broader spreads:
```solidity
  minBid = highestBid + ((highestBid * settings.minBidIncrement) / 100);
```

* When executing the proposal, it does not check that ```msg.value ==``` sum of all ```_values```, the caller will lose excess eth, it will remain in the treasury:
```solidity
  function execute(
        ...
    ) external payable returns (bytes32) {
        ...
        settings.treasury.execute{ value: msg.value }(_targets, _values, _calldatas, _descriptionHash);
```

* I am not sure if this is a feature or a bug but ```createBid``` does not have ```whenNotPaused``` modifier, meaning the bids for the current auction can still come even when the contract is paused:
```solidity
  function createBid(uint256 _tokenId) external payable nonReentrant
```
On one hand, it is bad in case you discover a flaw and want to stop bidding, on the other hand, it is good that you let finish the current auction and the owners cannot interrupt it by pausing and unpausing when it is beneficial to them. So because I was not sure what was the original intention, I am submitting this as a QA issue.