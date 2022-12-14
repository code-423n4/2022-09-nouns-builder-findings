# Use less storage on Auction contract

Considering the following assumptions:
- [an uint40 is enough for storing the auctions timestamps](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L146)
- [auctions must last at least 1 second to receive bids](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L98), and in practice they have to last at least 12 seconds each, considering the block times post-merge
- [only the auction contract can mint tokens](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L148)
- [an auction can realistically mint at most 100 tokens](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L152-L157), considering 99 for founders with 1% ownership each

It's safe to assume that a tokenId won't realistically overflow an uint48 in the contract operational timespan.

With that assumption, we can usa a uint48 instead of uint256 on [Auction.tokenId](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/types/AuctionTypesV1.sol#L30) and move it down above uint40 startTime to save storage gas with the struct packing.

For consistency, it can be made an uint96 to match the [Token Setting.totalSupply](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/types/TokenTypesV1.sol#L18) while still fitting in a packed uint256

# use less storage on Governor.settings

The [Governor Settings struct](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/types/GovernorTypesV1.sol#L19-L27) can be [tightly packed](https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html#layout-of-state-variables-in-storage) and use one less storage slot (4 instead of 5) if its members are reordered to:
```
    struct Settings {
        Token token;
        Treasury treasury;
        address vetoer;
        uint16 proposalThresholdBps;
        uint16 quorumThresholdBps;
        uint48 votingDelay;
        uint48 votingPeriod;
    }
```

# redundant/unreachable EXECUTION_NOT_READY revert on Treasury.execute

On Treasury.execute [there's a check for the execution readiness of the proposal](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L151), which will never be truthy because the only possible execution of this function (onlyOwner modifier) is [via Governor.execute](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L340), which [already checks if the proposal is queued and reverts otherwise](https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L334)