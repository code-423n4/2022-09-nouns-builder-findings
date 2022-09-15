# QA Report

## Use `.safeTransferFrom` instead of `.transfer`

Make sure that the transfer of WETH is safe in the following instance:

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L363

## Use `.safeTransferFrom` instead of `.transferFrom`:

Make sure that the transfer of tokens is safe in the following instance:

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L192