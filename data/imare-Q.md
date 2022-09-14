## [L-01] **If auction is paused you can still bid**

Impact
This behaviour can draw away users because it resembles a possible rugpull

Recommended Mitigation Steps
Two posibilities:
* Use of whenNotPaused modifier on Auction#createBid call
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L90

* Stop the process of biding in the application UX/frontend if the auction is paused. And save a little on transaction gas

## [L-02] **Immutable addresses lack zero-address check**

Constructors should check the address written in an immutable address variable is not the zero address

Proof of Concept

Instances include:

Auction.sol

 manager = IManager(_manager);

later the address of manager is again checked as:
```
if (msg.sender != address(manager)) revert ONLY_MANAGER();
```

But manager can be mistakenly assigned address(0)

Mitigation

Add a zero address check for manager.


## [L-03] **USE SAFETRANSFERFROM INSTEAD OF TRANSFERFROM**

OpenZeppelin’s documentation discourages the use of transferFrom(), use safeTransferFrom() whenever possible. Even for ERC721 tokens.

https://docs.openzeppelin.com/contracts/2.x/api/token/erc721


Recommended Mitigation Steps
Mybe insted of direct sending the NTF when the auction is settled implement a user withdrawal pattern. 
Instead of sending use a map to identify
winning addresses. If withdrawal is succesfull delete the queried entrie.
```
mapping(uint256 => address) internal tokenWinners;
````

## [L-04] **SIGNATURE MALLEABILITY OF EVM’S ECRECOVER**

EVM’s ecrecover is susceptible to signature malleability and is recommend using OpenZeppelin’s ECDSA library.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L236

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L167