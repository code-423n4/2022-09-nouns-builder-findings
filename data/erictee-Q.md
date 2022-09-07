### [L-01] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
src/token/Token.sol:L161        _mint(minter, tokenId);

src/token/Token.sol:L167    function _mint(address _to, uint256 _tokenId) internal override {

src/token/Token.sol:L169        super._mint(_to, _tokenId);

src/token/Token.sol:L188            _mint(tokenRecipient[baseTokenId].wallet, _tokenId);

src/lib/token/ERC721.sol:L191    function _mint(address _to, uint256 _tokenId) internal virtual {

```

### [L-02] Missing zero address check


#### Impact
Zero-address checks as input validation closest to the function beginning is a best-practice.


#### Findings:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L97
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L39-L42
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L41-L43
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L76