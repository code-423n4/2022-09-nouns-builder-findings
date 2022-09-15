L-1 MISSING ZERO-ADDRESS CHECK IN THE SETTER FUNCTIONS AND INITILIAZERS
Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331

L-2 _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements IERC721Receiver

L-3 SETTERS SHOULD CHECK THE INPUT VALUE
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L316

N-1 NATSPEC IS INCOMPLETE
Missing @returns token, metadata, auction, treasury, governor
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L104

N-2 PUBLIC FUNCTIONS CAN BE EXTERNAL
It is good practice to mark functions as `external` instead of `public` if they are not called by the contract where they are defined.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L98
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L461