## Missing 0 address checked
Line number : https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L30-L32
`manager` is immutable, therefore it should be 0 address checked.

Line number : https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L55-L72
`tokenImpl`,  `metadataImpl`, `auctionImpl`, `treasuryImpl` and `governorImpl` are immutable, therefore it should be 0 address checked.

## Initialize function can be called by everyone
Line number: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L80
This could lead to a race condition when the contract is deployed. At that moment a hacker could call the `initialize` function and make the deployed contract useless. Then it would have to be redeployed, costing a lot of gas.

## Possibility of DOS in _authorizeUpgrade()
Line number: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L305
If the address of `newImpl` is assigned by mistake to a malicious or a 0 address than the upgraded contract will lead to DOS.

## Unlocked Pragma used in multiple contracts
Some contracts  use an `unlocked pragma`. Locking the pragma helps ensure that contracts do not accidentally get deployed using a different compiler version with which they have been tested the most.

https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib
All contracts present inside the folder have `unlocked pragma`, avoid using it.
