### `_SAFEMINT()` SHOULD BE USED RATHER THAN `_MINT()` WHEREVER POSSIBLE
`_mint()` is discouraged in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements IERC721Receiver. see [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) library.

## Instance:
```
        _mint(minter, tokenId);
    }

    function _mint(address _to, uint256 _tokenId) internal override {
       
        super._mint(_to, _tokenId);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L161-L170