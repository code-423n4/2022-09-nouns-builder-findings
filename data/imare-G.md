
### [G-01] **Use already cached auction variable** 

On line https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L172 instead of

```
        if (auction.settled) revert AUCTION_SETTLED();
```

use previously cached _auction value insted of storage variable read:

```
        if (_auction.settled) revert AUCTION_SETTLED();
```
### [G-02] **> 0 IS LESS EFFICIENT THAN != 0 FOR UNSIGNED INTEGERS**

On line: 
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L203

instead of
```
if (_from != _to && _amount > 0) {
```
use
```
if (_from != _to && _amount != 0) {
```

### [G-03] **FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE**

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 28 instances of this issue.