-> EVENT IS MISSING INDEXED FIELDS
Each event should use three indexed fields if there are three or more fields.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#:~:text=event%20AuctionBid(uint256%20tokenId%2C%20address%20bidder%2C%20uint256%20amount%2C%20bool%20extended%2C%20uint256%20endTime)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#:~:text=event%20AuctionSettled(uint256%20tokenId%2C%20address%20winner%2C%20uint256%20amount)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#:~:text=event%20AuctionCreated(uint256%20tokenId%2C%20uint256%20startTime%2C%20uint256%20endTime)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#:~:text=event%20ApprovalForAll(address%20indexed%20owner%2C%20address%20indexed%20operator%2C%20bool%20approved)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#:~:text=event%20VoteCast(address%20voter%2C%20bytes32%20proposalId%2C%20uint256%20support%2C%20uint256%20weight%2C%20string%20reason)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#:~:text=event%20ApprovalForAll(address%20indexed%20owner%2C%20address%20indexed%20operator%2C%20bool%20approved)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#:~:text=event%20DelegateVotesChanged(address%20indexed%20delegate%2C%20uint256%20prevTotalVotes%2C%20uint256%20newTotalVotes)%3B

->USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.10 to have external calls skip contra

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol#:~:text=pragma%20solidity%20%5E0.8.0%3B