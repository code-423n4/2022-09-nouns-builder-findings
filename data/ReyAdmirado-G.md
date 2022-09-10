| | issue |
| ----------- | ----------- |
| 1 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#1-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 2 | [can make the variable outside the loop to save gas](#2-can-make-the-variable-outside-the-loop-to-save-gas) |
| 3 | [`++x` costs less gas than `x++`](#3-x-costs-less-gas-than-x) |
| 4 | [it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied](#4-it-costs-more-gas-to-initialize-non-constantnon-immutable-variables-to-zero-than-to-let-the-default-of-zero-be-applied) |
| 5 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#5-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 6 | [using `bools` for storage incurs overhead](#6-using-bools-for-storage-incurs-overhead) |
| 7 | [internal functions only called once can be inlined to save gas](#7-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 8 | [private functions only called once can be inlined to save gas](#8-private-functions-only-called-once-can-be-inlined-to-save-gas) |
| 9 | [abi.encode() is less efficient than abi.encodepacked()](#9-abiencode-is-less-efficient-than-abiencodepacked) |
| 10 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#10-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 11 | [using private rather than public for constants, saves gas](#11-using-private-rather-than-public-for-constants-saves-gas) |
| 12 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#12-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 13 | [bytes constants are more efficient than string constants](#13-bytes-constants-are-more-efficient-than-string-constants) |


## 1. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

- [Token.sol#L88](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L88)
- [Token.sol#L118](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118)

- [MetadataRenderer.sol#L140](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140)

- [Governor.sol#L280](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280)
- [Governor.sol#L285](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285)
- [Governor.sol#L290](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290)


## 2. can make the variable outside the loop to save gas

- [Token.sol#L82](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L82)
- [Token.sol#L91](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L91)
- [Token.sol#L94](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L94)
- [Token.sol#L102](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L102)
- [Token.sol#L105](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L105)
- [Token.sol#L108](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L108)

- [MetadataRenderer.sol#L124](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L124)
- [MetadataRenderer.sol#L135](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L135)
- [MetadataRenderer.sol#L144](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L144)
- [MetadataRenderer.sol#L151](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L151)
- [MetadataRenderer.sol#L154](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L154)
- [MetadataRenderer.sol#L191](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L191)
- [MetadataRenderer.sol#L231](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L231)
- [MetadataRenderer.sol#L234](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L234)
- [MetadataRenderer.sol#L237](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L237)
- [MetadataRenderer.sol#L240](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L240)

- [Treasury.sol#L164](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L164)


## 3. `++x` costs less gas than `x++`

- [Token.sol#L91](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L91)
- [Token.sol#L154](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L154)

- [Governor.sol#L230](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230)


## 4. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [MetadataRenderer.sol#L119](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119)
- [MetadataRenderer.sol#L133](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133)
- [MetadataRenderer.sol#L189](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189)
- [MetadataRenderer.sol#L229](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229)

- [Treasury.sol#L162](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162)


## 5. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

- [MetadataRenderer.sol#L347](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L347)
- [MetadataRenderer.sol#L355](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L355)
- [MetadataRenderer.sol#L363](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L363)

- [Governor.sol#L116](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L116)
- [Governor.sol#L192](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L192)


## 6. using `bools` for storage incurs overhead

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

- [MetadataRenderer.sol#L231](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L231)

- [Treasury.sol#L164](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L164)

- [Auction.sol#L136](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L136)
- [Auction.sol#L349](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L349)

- [ERC721.sol#L115](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L115)

- [ERC1967Upgrade.sol#L36](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L36)
- [ERC1967Upgrade.sol#L57](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L57)


## 7. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

- [Token.sol#L71](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L71)
- [Token.sol#L130](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L130)


## 8. private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

- [Token.sol#L177](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L177)

- [MetadataRenderer.sol#L250](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L250)
- [MetadataRenderer.sol#L255](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L255)


## 9. abi.encode() is less efficient than abi.encodepacked()

- [MetadataRenderer.sol#L251](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L251)

- [Treasury.sol#L107](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L107)

- [Governor.sol#L104](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L104)
- [Governor.sol#L230](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230)

- [Manager.sol#L68](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L68)
- [Manager.sol#L69](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L69)
- [Manager.sol#L70](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L70)
- [Manager.sol#L71](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L71)


## 10. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [Governor.sol#L213](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L213)


## 11. using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- [Governor.sol#L27](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27)

- [ERC1967Upgrade.sol#L24](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L24)

- [ERC721Votes.sol#L21](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21)


## 12. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [Governor.sol#L27](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27)

- [ERC721Votes.sol#L21](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21)


## 13. bytes constants are more efficient than string constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

