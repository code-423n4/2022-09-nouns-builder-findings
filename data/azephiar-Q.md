Low risk & QA findings:
1. Potential out of gas caused by `_addFounders` and `_createAuction()`
2. `__EIP712_init()` is not initialized
3. Typo / Gas saving tip at [Auction.sol#L172](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L172)
4. Token approvals are deleted for tokens self-transfers
5. Reserved tokens IDS are calculated wrong

## Potential out of gas caused by `_addFounders` and `_createAuction()`

The function `_addFounders` that's called on token initialization also reserves tokens for founders by filling `tokenReserved[id]` where `id` is a value between `0` and `99`, which represents the ids of tokens reserved for founders.

If `tokenReserved[id]` is non empty for every possible `id` (0-99) then it's not possible to call `_createAuction()` because it would try to mint tokens for founders indefinitely, eventually running out of gas.

### Impact
As far as I can tell this can only happen in the case described above, which implies setting the founders to have a total of 100% ownership. I don't see a reason why users could consciuosly decide to create a DAO with founders having 100% ownership but it's allowed by the protocol and could cause users to deploy a set of contracts which can potentially be expensive for nothing.

### Proof of concept
The following test describe a possible scenario in which this bug occurs. Just copy it into `Token.t.sol` and run with:
`forge test -m test_CantCallUnpause`

```javascript
function test_CantCallUnpause() public {
    createUsers(2, 1 ether);

    address[] memory wallets = new address[](2);
    uint256[] memory percents = new uint256[](2);
    uint256[] memory vestExpirys = new uint256[](2);

    uint256 end = 4 weeks;

    wallets[0] = otherUsers[0];
    percents[0] = 50;

    wallets[1] = otherUsers[1];
    percents[1] = 50;

    unchecked {
        for (uint256 i; i < 2; ++i) {
            vestExpirys[i] = end;
        }
    }

    deployWithCustomFounders(wallets, percents, vestExpirys);

    vm.prank(wallets[0]);

    //Can't
    vm.expectRevert();
    auction.unpause();
}
```

### Mitigation
Having the function `_addFounders()` to keep at least one slot empty in `tokenReserved[id]` would prevent it from trying to mint indefinitely. It's also worth taking in consideration that in the case there's 1 slot free `_createAuction` would still mint 100 (99 for founders plus 1 for the bidder) tokens, which might be a lot of gas.

## `__EIP712_init()` is not initialized
Missing initialization: [Token.sol#L43](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L43)


### Impact
`DOMAIN_SEPARATOR()`, which is used in `token.delegateBySig()` will always be computed as `keccak256(abi.encode(bytes32(0), bytes32(0), bytes32(0), uint(0), address(token)))`.

I guess `address(token)`, which is the address of the token proxy and it's different for every DAO saves the day here.

I think this has no serious implication but I'm not going to explore any further because even if there's no scenario in which this can be exoploited it should be fixed.


## Typo / Gas saving tip
At [Auction.sol#L172](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L172
) we have:

```javascript
if (auction.settled) revert AUCTION_SETTLED();
```

I think it was meant to be (`_auction` instead of `auction`):
```javascript
if (_auction.settled) revert AUCTION_SETTLED();
```

Accessed from memory and consistent with the rest of the code. 

## Token approvals are deleted for tokens self-transfers
At [ERC721.sol#L146](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L146), inside `token.transferFrom()` we have:

```javascript
delete tokenApprovals[_tokenId];
```

In the case of a transfer from an address to itself the approvals gets deleted because there's no check on `_from != _to`, which is not expected behaviour. This is such an edge case that it might be worth living is as it is to save gas for users.

## Reserved tokens IDS are calculated wrong
Reserved token ids are calculated in a weird way in `token._addFounders()` at this line of code at [Token.sol#L113](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L113):
```javascript
(baseTokenId += schedule) % 100
```

What this is actually doing is:
```javascript
baseTokenId =  baseTokenId + (schedule % 100);
```

### Impact
Due to a combination of this and `schedule` being calculated as:
```javascript
uint256 schedule = 100 / founderPct;
```
which rounds down to `1` for `founderPct >= 51` it causes the remaining founders to get less tokens. 

Intuitevely what should happen is that `baseTokenId` might result in values higher than `99`. This can happen if one `founderPct` is `2` because that sets `schedule` to `50`. 

Unfortunately I discovered this quite late in the contest and didn't have time to indagate further. What seems weird to me is that the code seems to work anyway for most the of the cases.

Changing: 
```javascript
(baseTokenId += schedule) % 100
```
to
```javascript
baseTokenId = (baseTokenId + schedule) % 100
```
seems to fix the issue even for cases in which 1 founder has more than 51 `ownershipPcts`.