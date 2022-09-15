### [L01] Transfer Can Fail Silently

**Description:**
The transfer in handleOutgoingTransfer can fail silently and cause a loss of funds to user. It is however an unlikely scenario as the reciever has to be a contract account that fails to recieve the call() transfer and has not implemented a way to deal with ERC20 tokens. 

**Recommendation:**
It can't be fixed by changing to safeTransfer as this will open the potential for a user to DoS anyone else who tries to call createBid, so I recommend changing to a pull pattern where the users balance is stored in a variable and they must call a second function to withdraw the funds where safeTransfer can be used.

**LOC:**
[Auction.sol#L350-L364](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L350-L364) 

### [N01] Incomplete Natspec
[Governor.sol#L247](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L247) - Missing @param reason