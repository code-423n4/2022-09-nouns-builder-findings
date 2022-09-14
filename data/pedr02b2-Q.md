### No limits to maximum auction duration 

 /// @param _duration The duration of each auction

There are no checks to see if the auction time has been set by some unreasonable character to unreasonable end time, or even by human error, maybe a check could be put in place to limit the max time of an auction to mitigate the posibility of this happening by accident or other wise.

mitigate by creating 1/3/7 day auction times as a standard

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L52

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L58

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L77

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L281

### Fix pragma version before deployment to mainet

src= https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/

ALL files under "lib/interfaces/" source file (total 9 contracts), ALL files under "lib/proxy" (total 3 contracts), ALL files under "lib/token" (total 2 contracts), ALL files under "lib/utils/" (total 8 contracts)

 



