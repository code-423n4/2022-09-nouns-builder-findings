# Low effect on readability
## [G-01] Using `storage` instead of `memory`  can be cheaper.

A `storage` structure is pre allocated by the contract, by contrast, a `memory` one is newly created. Depending on the case both can be used to optimize the gas cost because simply, a `storage` is cheaper to create but more expensive to read from and to return and a `memory` on the other hand is more expensive to create but cheaper to read from and to return. 

Following this, we can deduce 2 cases that can be swapped to optimize runtime cost and deployment cost:

 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L358
 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L508

Consider changing `memory` to `storage` in these lines

With these changes, these evolutions in gas average report can be observed:

    Governor.sol: Deployment:  4045061 -> 3941326 (-103735)
    Governor.sol: cancel: 8414 -> 7964 (-450)
    Governor.sol: proposalVotes: 1149 ->  737 (-412)

## [G-02] Using `calldata` instead of `memory` for read only argument in external function
If a function parameter is read only, it is cheaper in gas to use `calldata` instead of `memory`.

1 instance:
 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L195
 
Consider changing `memory` to `calldata` in these lines/

*Save probably around 100 gas*
# Medium effect on readability
## [G-03] Some variable can be cached.

2 instances:
 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L151-L158

Consider caching `settings.totalSupply`to get something like this:

    uint96 _totalSup = settings.totalSupply;
    unchecked {
	    do  {
		    tokenId = _totalSup++;
	    }  while  (_isForFounder(tokenId));
	}
    settings.totalSupply = _totalSup;

With this changes, these evolutions in gas average report can be observed:

    Token.sol: Deployment: 3254451 -> 3263459 (+9008)
    Token.sol: mint: 182237 -> 182092 (-145)

------

 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L78-L124
 
Consider caching `settings.numFounders;` and modifing post-increment to pre-increment to get something like this:


    uint8 founderId = settings.numFounders;
    unchecked {
    	for  (uint256 i; i < numFounders;  ++i)  {
	    	IManager.FounderParams calldata founderI = _founders[i];
		    ...
	    	++founderId; //at the end of the loop
    	}
    	... // the original variable don't need to be updated.
    }
With this changes, these evolutions in gas average report can be observed:

    Token.sol: Deployment: 3254451 -> 3229019 (-25432)
    Token.sol: initialize: 539827 -> 537641 (-2186)

# High effect on readability

## [G-04] `And` condition can be swapped to `or` condition.
An `or` condition is cheaper than an `and` one even with optimizer.

2 instances:
 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L363
 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L89

Consider swapping `(a && b)` to `(!(!a || !b))` which are equivalent.
*"Everything is true" to "not (something is false) "*

With these changes, these evolutions in gas average report can be observed:

    Governor.sol: Deployment : 4045061 -> 4044661 (-400)
    Governor.sol: cancel: 7608 -> 7603 (-5)
    Treasury.sol: Deployment: 1883434 -> 1883634 (+200)
    Treasury.sol: isReady: not available (probably -5)

## [G-05] Inlining function

An internal/private function only called once can be inlined to save gas.

3 instances:
 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L71
 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L130
 - https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L177

Consider inlining these function.
With these changes, these evolutions in gas average report can be observed:

    Token.sol: Deployment:   3254451 -> 3241235 (-13216)
    Token.sol: initialize: 539827 -> 539322 (-505)
    Token.sol: mint: 182237 -> 182088 (-149)

## [G-06] Optimise function name
Every function have a keccak256 hash, this hash defines the order of the function in the contract. The best the ranking, the minimum the gas usage to access the function. Each time a function is called, the EVM need to pass through all the functions better ranked (going through a function cost 22 gas), and this operation cost gas. This can be optimized, the ranking is defined by the first four bytes of the kekkack256 hash of the function name. The name can be changed to improve the ranking, which greatly impacts the readability. That's why it's not practical to change all the names, but it's possible to change only the ones of the functions called a lot of times. This change can be done on the following functions according to their number of uses and their current ranking to find the best compromise.

1. `Auction.sol`: 659dd2b4 - `createBid(uint256)`
**Must outrank:** 0fb5a6b4 - `duration()`
**New name:** 012fe192  - `createBid_11(uint256)`
**Rank:**  11 -> 1 
*Save 220 gas each call*

2. `Auction.sol`: a4d0a17e - `settleAuction()`
**Must outrank:** 0fb5a6b4 - `duration()`
**New name:** 030a219b  - `settleAuction_60()`
**Rank:**  18 -> 2 
*Save 352 gas each call*

3. `Governor.sol`: 75a12d72 - `castVote(bytes32,uint256)`
**Must outrank:** 02a251a3 - `votingPeriod()`
**New name:** 017aff6d  - `castVote_127(bytes32,uint256)`
**Rank:**  23 -> 1
*Save 484 gas each call*

4. `Governor.sol`: 7d5e81e2 - `propose(address[],uint256[],bytes[],string)`
**Must outrank:** 02a251a3 - `votingPeriod()`
**New name:** 024aa7b8  - `propose_68(address[],uint256[],bytes[],string)`
**Rank:** 26 -> 2
*Save 528 gas each call*

5. `Treasury.sol`: 6a42b8f8 - `delay()`
**Must outrank:** 0dc051f8 - `isQueued(bytes32)`
**New name:** 049dfb8a - `delay_04()`
**Rank:**  13 -> 1 
*Save 264 gas each call*

6. `Manager.sol`: eb1cb38c - `deploy((address,uint256,uint256)[],(bytes),(uint256,uint256),(uint256,uint256,uint256,uint256,uint256))`
**Must outrank:** 23452b9c - `cancelOwnershipTransfer()`
**New name:** 0b4ad28f  - `deploy_0((address,uint256,uint256)[],(bytes),(uint256,uint256),(uint256,uint256,uint256,uint256,uint256))`
**Rank:** 17  -> 1
*Save 352 gas each call*

7. `Manager.sol`: 8da5cb5b - `owner()`
**Must outrank:** 23452b9c - `cancelOwnershipTransfer()`
**New name:** 0c73c4dc  - `owner_0()`
**Rank:**  12 -> 2
*Save 222 gas each call*

8. `Token.sol`: dc276e57 - `getScheduledRecipient(uint256)`
**Must outrank:** 01ffc9a7 - `supportsInterface(bytes4)`
**New name:** 004809d4  - `getScheduledRecipient_81(uint256)`
**Rank:** 34  -> 1 
*Save 726 gas each call*

Consider optimizing these function names.