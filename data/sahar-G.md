
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/types/AuctionTypesV1.sol#L16

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/types/AuctionTypesV1.sol#L17
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/types/AuctionTypesV1.sol#L33

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/types/AuctionTypesV1.sol#L34

In all the above lines, uint40 can be replaced with a smaller variable, cause 2 ^40 seconds is a significant time duration. 

---------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L22

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L34

In all the above lines, uint256 endTime and 256 startTime can be replaced with a smaller type of variables, 2^256 is a huge number that never be reached in these cases.


----------------------------------------------------------------------------------------
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L38

uint256 duration can be replaced with much smaller variable. 
----------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L46

 uint256 minBidIncrementPercentage  can be replaced with an uint8 variable because of its task.
----------------------------------------------------------------------------------------


https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/types/GovernorTypesV1.sol#L24

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/types/GovernorTypesV1.sol#L25

The values that are used in the real world for the variable votingDelay  and votingPeriod  are much smaller than the amount of memory considered for these two variables. Therefore, a smaller variable type can be used

----------------------------------------------------------------------------------------

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L73

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L76  

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L85 

The values that are used in the real world for the variable numFounders  and totalOwnership  and founderPct  are much smaller than the amount of memory considered for these two variables. Therefore, a smaller variable type can be used 