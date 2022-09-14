# Summary
This code is very well presented in my opinion, with generally good comments. 

___

# Non Critical
*(code style, clarity, syntax, versioning, off-chain monitoring (events, etc)*

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L79
"its" in this comment should be "it's" or "it is".

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L101-L120
The comments in this section could be improved to make some explanation of the logic of the `schedule` variable: it is being used to attempt to distribute the founders' NFT evenly throughout each group of 100, rather than have all the founders NFTs minted in a single big continuous group. The logic with calling `_getNextTokenId()` is related to this goal, but none of this is explained and so the code is not as clear as it could be.



# Low 
*(e.g. assets are not at risk: state handling, function incorrect as to spec, issues with comments)*

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L363
The test on line 363 of `Governor` will revert if `getVotes(proposal.proposer, block.timestamp - 1)` is greater than `proposal.proposalThreshold)`, but not if the two sides are equal. This means that a cancellation will be considered valid if the proposer's votes exactly meet the threshhold, which would be contrary to expectation. If the voting tokens had an unusually low supply, this issue could have a significant impact as the situation would occur reasonably frequently.
Recommendation: Change the test on line 363 from `>` to `>=`.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L353-L377
`Governor.cancel()` only prevents cancelling if the existing status is `Executed`. This means that an already cancelled or vetoed proposal can be cancelled repeatedly, each time emitting the event.
Recommendation: Return or revert if the propsal state is `ProposalState.Canceled` or `ProposalState.Vetoed`.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L385-L405
Similarly, `Governor.veto()` only prevents vetoing if the existing status is `Executed`. This means that an already cancelled or vetoed proposal can be vetoed repeatedly, each time emitting the event.
Recommendation: Return or revert if the propsal state is `ProposalState.Canceled` or `ProposalState.Vetoed`.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L351-L355
Bidders have the incentive to make calls to `Auction._handleOutgoingTransfer()` as expensive as possible when their bids are cancelled, as it is a gas price paid by opposing bidders. By bidding through a contract, they can implement a `receive()` function that ensures the 50,000 gas on line 354 is always spent and also that the call fails so that extra gas is then spent wrapping the repayment in `WETH`.
Recommendation: Return bids by a pull mechanism instead of pushing them to prior bidders.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L161
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L101-L133
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L157
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L187
Calls to `Auction.settleCurrentAndCreateNewAuction()` will sometimes be significantly more expensive than at other times.
This is because `Auction.settleCurrentAndCreateNewAuction()` calls `_createAuction()` which calls `token.mint()` which contains [a `do...while` loop which mints tokens to founders if the next token index is marked to do so.](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L152-L157) When we consider how those marks are distributed, we can see that, if founders have matching percentages, they will clump to some degree. The reason for this is [the logic in `Token._addFounders()`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L101-L133) which increments by one if an index is already reserved. So, if you have 5 founders with 10 percent ownership each, their indices will occur in blocks of 5 (0,1,2,3,4,10,11,12,13,14...etc).
This means that ending some auctions, which winners will wish to do, will sometimes involve paying for many tokens to be minted to founders, and sometimes will not.
Recommendation: Maintain an array of which indices in the range 0-99 are not for founders and step through that, setting flags for when founders have accumulated new tokens. Then have founders pay the gas to mint their own tokens.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L79
This comment caused me significant misunderstanding and should be changed. It states that ownership will be transferred. On consultation with the development team, I was informed that this transfer is expected to take place manually, but the comment very much reads as though the code will do it automatically. If this code is to be distributed to multiple organisations and users, it is not advisable to use such deterministic language, as their actions cannot be predicted.
Recommendation: Remove the part of the comment in brackets or change it to a recommendation.
