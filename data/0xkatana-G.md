# [G-01] Redundant zero initialization

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L162
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L119
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L133
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L189
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L229

## Recommended Mitigation Steps

Remove the redundant zero initialization
`uint256 i;` instead of `uint256 i = 0;`

# [G-02] Use != 0 instead of > 0

Using `> 0` uses slightly more gas than using `!= 0`. Use `!= 0` when comparing uint variables to zero, which cannot hold values below zero

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L203
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Address.sol#L50
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L61

## Recommended Mitigation Steps

Replace `> 0` with `!= 0` to save gas

# [G-03] Use prefix not postfix in loops

Using a prefix increment (++i) instead of a postfix increment (i++) saves gas for each loop cycle and so can have a big gas impact when the loop executes on a large number of elements.

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L91
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L154
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L207
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L222

## Recommended Mitigation Steps

Use prefix not postfix to increment in a loop

# [G-04] For loop incrementing can be unsafe

For loops that use i++ do not need to use safemath for this operation because the loop would run out of gas long before this point. Making this addition operation unsafe using unchecked saves gas.

Sample code to make the for loop increment unsafe
```
for (uint i = 0; i < length; i = unchecked_inc(i)) {
    // do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
    unchecked {
        return i + 1;
    }
}
```

Idea borrowed from
https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L162
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L119
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L133
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L189
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L229
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L80
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L108
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L132
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L261

## Recommended Mitigation Steps

Make the increment in for loops unsafe to save gas

# [G-05] Use iszero assembly for zero checks

Comparing a value to zero can be done using the `iszero` EVM opcode. This can save gas

Source from t11s https://twitter.com/transmissions11/status/1474465495243898885

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L135
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L278
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L418
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L445
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L100
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L222
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L85
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L175
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L248
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L67

## Recommended Mitigation Steps

Use the assembly `iszero` evm opcode to compare values to zero

# [G-6] Add payable to functions that won't receive ETH

Identifying a function as payable saves gas. Functions that have a modifier like onlyOwner cannot be called by normal users and will not mistakenly receive ETH. These functions can be payable to save gas.

There are many functions that have the onlyOwner modifier in the contracts. Some examples are
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L180
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L564
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L572
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L580
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L588
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L596
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L605
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L618
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L95
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L347
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L355
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L363
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L376
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L244
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L263
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L307
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L315
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L323
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L331
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L374
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol#L63
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol#L71
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol#L87
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L187
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L196
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L209

## Recommended Mitigation Steps

Add payable to these functions for gas savings

# [G-7] Add payable to constructors that won't receive ETH

Identifying a constructor as payable saves gas. Constructors should only be called by the admin or deployer and should not mistakenly receive ETH. Constructors can be payable to save gas.

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L32
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L41
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L32
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L30
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L39
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Proxy.sol#L21
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L55

## Recommended Mitigation Steps

Add payable to these functions for gas savings

# [G-8] Use internal function in place of modifier

An internal function can save gas vs. a modifier. A modifier inlines the code of the original function but an internal function does not.

Source https://blog.polymath.network/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size-c44580b218e6#dde7

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol#L27
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol#L33
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol#L55
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol#L28
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Ownable.sol#L34
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/ReentrancyGuard.sol#L39
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Pausable.sol#L22
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Pausable.sol#L28
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol#L25
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol#L32

## Recommended Mitigation Steps

Use internal functions in place of modifiers to save gas.

# [G-9] Use uint not bool

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L52
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L53
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/types/GovernorTypesV1.sol#L54
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/types/MetadataRendererTypesV1.sol#L11
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L136
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L349
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/types/AuctionTypesV1.sol#L35
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol#L20
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Pausable.sol#L15
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L36
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L57

## Recommended Mitigation Steps

Replace bool variables with uints

# [G-10] Bitshift for divide by 2

When multiply or dividing by a power of two, it is cheaper to bitshift than to use standard math operations.

There is a divide by 2 operation on these lines
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L95

## Recommended Mitigation Steps

Bitshift right by one bit instead of dividing by 2 to save gas

# [G-11] Non-public variables save gas

Many constant variables are public, but changing the visibility of these variables to private or internal can save gas.

Locations where this was found include
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L27
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L25
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L28
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L31
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L34
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager/Manager.sol#L37

## Recommended Mitigation Steps

Declare some public variables as private or internal to save gas.

Example: change this line
```
bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```

to this line
```
bytes32 internal constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```

# [G-12] Write contracts in vyper

The contracts are all written entirely in solidity. Writing contracts with vyper instead of solidity can save gas.

Source https://twitter.com/eiber_david/status/1515737811881807876
doggo demonstrates https://twitter.com/fubuloubu/status/1528179581974417414?t=-hcq_26JFDaHdAQZ-wYxCA&s=19

## Recommended Mitigation Steps

Write some or all of the contracts in vyper to save gas