## [NAZ-G1] Use `delete` Keyword To Reset State Data
**Context**: [`Auction.sol#L227-L229`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L227-L229)

**Description**:
It's cheaper to use the `delete` keyword when resetting state data to their default values than setting them.

**Recommendation**: 
Consider using `delete` keyword to reset state data to default values.


## [NAZ-G2] Build the `DOMAIN_TYPEHASH` In The `initialize()` To Save Gas and Clarify/Clean Code
**Context**: [`Governor.sol#L27`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27), [`ERC721Votes.sol#L21`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21)

**Description**:
Building the `DOMAIN_TYPEHASH` in the `initialize()` can save gas and clean the code up.

**Recommendation**: 
1. Remove the `bytes32 public constant DOMAIN_TYPEHASH`
2. Add `bytes32 private immutable `DOMAIN_SEPARATOR` as a contract VAR
3. Assign the `DOMAIN_TYPEHASH` in the `initialize()`

## [NAZ-G3] Setting The Constructor To Payable
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src)

**Description**:
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves 21 gas on deployment with no security risks.

**Recommendation**: 
Set the constructor to payable.


## [NAZ-G4] Function Ordering via Method ID
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src)

**Description**:
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. One could use [`This tool`](https://emn178.github.io/solidity-optimize-name/) to help find alternative function names with lower Method IDs while keeping the original name intact.

**Recommendation**: 
Find a lower method ID name for the most called functions for example ```mostCalled()``` vs. ```mostCalled_41q()``` is cheaper by 44 gas.
