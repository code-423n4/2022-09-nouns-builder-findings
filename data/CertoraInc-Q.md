# QA Report
* The `initialize` function of the `Treasury` contract doesn't emit the `GracePeriodUpdated` event
* ETH can be locked in the Auction contract - either by transferring value in the constructor (which is payable) or by transferring it without calling the fallback/receive functions (for example using the self destruct operation)