1. Missing zero address check
he following functions are missing zero address checks which may require redeployment of contracts
Manager.constructor()
Auction._authorizeUpgrade()


2. Missing zero value check
The following functions are missing zero value checks. Setting the state variable to 0 would affect other functions in the contract, possibly causing reverts.

Auction.setDuration()
Auction.setReservePrice()
Auction.setTimeBuffer()
Auction.setMinimumBidIncrement()
