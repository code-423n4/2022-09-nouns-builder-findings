
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L161

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L167

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L178

The auction smart contract is design in such a way that only “one auction” can be held at any time. Since there is no limit to the duration of the auction, a malicious actor can define a special auction with a very long duration, so because the “_settleAuction function” (due to not reaching the end time of this particular auction) is never executed. , so no new auctions can be established, and all next auctions are almost stopped. It is recommended to consider a fixed variable for each auction with the title of "the maximum duration of the auction" and stop the auction if this period is reached.
------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L344


In the” _handleOutgoingTransfer”  function, a solution is provided for the case where the ETH transmission is not successful, but there is no solution for the case where the WETH transmission is not successful for any reason. It is suggested to use a REVERT command and a suggested solution for the remaining funds caused by the incomplete execution of this function.
------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/storage/AuctionStorageV1.sol#L2

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/interfaces/IEIP712.sol#L2

The difference between the compiler version used in the libraries (0.8.4) and the compiler version used in writing the main code of the smart contracts of this project may cause disturbances and inconsistencies in the expected performance of some library functions.

------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L54

In order to prevent the concentration of too much power in one address, it is appropriate to check that the address of the _funder and the _treasury  are not the same

------------------------------------------------------------------------------------------------


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L92

The logic of this  Daap is such that only one auction can be held at any time. By adding an ID for auctions and redesigning the contracts, these possibilities can be added to the  Daap so that several auctions can be held at any moment.
------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L80

Setting a “constant”  timeBuffer and a “constanr” minBidIncrement  is not a suitable way to defiance an action cause any auction has its own features and PRPPERTIES
------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L161

Because any address can call this function, in a certain situation, a miner by manipulating the timestamp can manipulate the situation in it`s favor by ending an auction earlier.
------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L307

The presence of this function causes the owner to disrupt the auction process. For example, if owner  is against winning a certain address in an auction, by manipulating the duration of the auction, he interferes with this process, for example, by adding the duration of the auction, he creates another opportunity for other competitors. It is recommended to delete this function.

------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L331


By manipulating this variable through this function, the owner can disrupt the auction process. For example, he sets this number very high or sets it very low or even zero during the auction and causes manipulation of the possibility of entering or winning a particular address in the auction.

------------------------------------------------------------------------------------------------


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L132

The presence of duplicate and similar addresses in the presentation is not checked anywhere.
------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L324

Any user or another contract can execute a proposal using this function. While it seems that the execution of a proposal should be in the hands of certain users, especially the proposal proposer.
.


------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L580

Because the admin is allowed to manipulate this limit, if the reading of function 468 and manipulation happens at the same time in a very short time interval, it will cause the information entered by the function of line 468 to be incorrect. It is suggested to remove this possibility for the owner.
------------------------------------------------------------------------------------------------

In general, in most cases in this project, there is no solution for changing the address of the main roles. For example, the address of the funder or owner.


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L250


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L197

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L32


 Weak algorithm for determining random number,
  The method used to determine the random number is weak and predictable due to the use of predictable and manipulable elements, especially by miners.
Also, the method of using shift to generate the next random number is also predictable and has weak logic
------------------------------------------------------------------------------------------------




https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L62

Many changes have been made in the design of the token of this project compared to the standard and audited ERC721 token. This issue increases the possibility of errors and bugs. It is recommended to use the standard version, or at least a version with fewer changes.

------------------------------------------------------------------------------------------------


https://github.com/code-423n4/2022-09-nouns-builder/blob/main/docs/protocol-docs.md

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L30

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L41


Based on the plan graphically shown in the documents, it is assumed that there is one manager for the entire project, but no mechanism has been designed for whether the manager's address is the same in all contracts or not? In other words, because Each contract is deployed separately, so it can declare a different and new address for the manager's address. This weakness questions the integrity and continuity of the entire project
------------------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L97

No type of modifier is considered to control access and execution of this function. Therefore, this function can be deployed through any address. The drawback of this method is that it is possible to fraudulently give the address of one or more of the other contracts as an input argument to this function, and by doing so, provide a method that embeds a hidden solution for hacking or attack. And because there is no modification, the address of the manager or the owner can disclaim his responsibility in this case.  Usually users don't check details of deploying, or they have not enough knowledge to check this details.




