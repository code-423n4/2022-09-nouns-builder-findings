1. Missing Indexed 

Files :

1.) https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L22

Missing `address bidder`

2.) https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L28

Missing `address winner`

2. Defines a return type but never explicitly returns a value

Address.isContract(address): Defines a return type but never explicitly returns a value.

File : 

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L30-L34

3. Rename `error` and `revert` for `REETRANCY()` to `REENTRANT()`

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/ReentrancyGuard.sol#L27

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/ReentrancyGuard.sol#L40

In this case you can used `REENTRANT()` rather than `REENTRANCY()`. since it was good for coding style and grammarticaly.

4. Typo

File :

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L138

`ids` to `id`

5. inconsistent Pragma

This contract below was used ^0.8.0, since another was used ^0.8.4 you can put it same as each other for consistency  

File : 

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol



