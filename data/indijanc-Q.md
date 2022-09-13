## Overall impression
The project looks well written and structured in terms of code and design. It's leveraging OpenZeppelin and of course NounsDAO, but the rest of the project also looks well thought through and with security in mind. I really liked the fact that the team deployed and offered to try the project out on a testnet.

## Received functions should be external
[Treasury.sol L242](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L242)
[Treasury.sol L253](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L253)
[Treasury.sol L264](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L264)

All of the received functions should be made external. I don't see a security issue here but this would conform with the EIP721 and EIP1155 receiver definitions and also save some gas. You actually have the correct functions already implemented in `TokenReceiver.sol`, so maybe consider just deriving `Treasury` from `ERC721TokenReceiver` and `ERC1155TokenReceiver`.

## Weird code comment
[ERC721.sol L209](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L209)

Seems like a typo in the comment, as I assume it should just say "Burns a token".

## `_status` variable could be made private
[ReentrancyGuard.sol L20](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/ReentrancyGuard.sol#L20)

The `_status` variable of `ReentrancyGuard` could be made private to prevent any modifications from deriving contracts. No issues currently but as contracts are upgradeable it might be good to follow the principle of least privilege.

## Comment sections in NatSpec
[IManager.sol L12](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L12)

The triple slash comment style produces NatSpec which is well used across the project. But the section titles also use triple slashes and end up in NatSpec, along with all those whitespaces :). I don't think this was intended and would recommend using double slash for the section comments across the whole project.

## `_owner` variable shadowing state variable
[Manager.sol L82](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L82)

Input variable `_owner` is shadowing inherited state variable `_owner` from `Ownable`, consider renaming to `_initialOwner`, `_initOwner`, or similar.

## Missing variable comment
[IToken.sol L18](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol#L18)

Missing `baseTokenId` variable comment :P

## Redundant modulo operation
[Token.sol L118](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118)

The token vest for loop seems to have a redundant modulo operation at the end that isn't needed and has no effect. Or am I missing something here?

## blockhash used for randomness
[MetadataRenderer.sol L251](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L251)

`blockhash()` should not be used for randomness. This is just informational as I believe you're aware of that and it's ok in the given use case. I'm assuming all attributes of a given token are equal in value / rarity.