
## 1.  Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom

It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

### Instances:
[Auction.sol:L363](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L363)
[Auction.sol:L192](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L192)
```
auction/Auction.sol:363:            IWETH(WETH).transfer(_to, _amount);
auction/Auction.sol:192:            token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
``` 
### Reference:

This [similar medium-severity finding](https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call) from Consensys Diligence Audit of Fei Protocol.

### Recommended Mitigation Steps:

Consider using safeTransfer/safeTransferFrom or require() consistently.


-----

## 2. _safeMint() should be used rather than _mint() wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

### Instances
[Token.sol:L161](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L161)
[Token.sol:L169](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L169)
[Token.sol:L188](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L188)
```
token/Token.sol:161:        _mint(minter, tokenId);
token/Token.sol:169:        super._mint(_to, _tokenId);
token/Token.sol:188:            _mint(tokenRecipient[baseTokenId].wallet, _tokenId);
``` 
### Recommendations:

Use _safeMint() instead of _mint().


-----


## 3. Unused receive() function will lock Ether in contract

If the intention is for the Ether to be used, the function should call another function, otherwise, it should revert.

### Instances:
[Treasury.sol:L269](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L269)
```
governance/treasury/Treasury.sol:269:    receive() external payable {}
``` 
### References

[https://code4rena.com/reports/2022-05-sturdy#l-06-unused-receive-function-will-lock-ether-in-contract](https://code4rena.com/reports/2022-05-sturdy#l-06-unused-receive-function-will-lock-ether-in-contract)


-----

## 4. Avoid using Floating Pragma:

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Instances:
[ERC1967Upgrade.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L2)
[ERC1967Proxy.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Proxy.sol#L2)
[UUPS.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/UUPS.sol#L2)
[ERC721Votes.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L2)
[ERC721.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L2)
```
lib/proxy/ERC1967Upgrade.sol:2:pragma solidity ^0.8.4;
lib/proxy/ERC1967Proxy.sol:2:pragma solidity ^0.8.4;
lib/proxy/UUPS.sol:2:pragma solidity ^0.8.4;
lib/token/ERC721Votes.sol:2:pragma solidity ^0.8.4;
lib/token/ERC721.sol:2:pragma solidity ^0.8.4;
``` 
Recommend using fixed solidity version

### References:

[https://code4rena.com/reports/2022-04-phuture#g-20-use-a-more-recent-version-of-solidity](https://code4rena.com/reports/2022-04-phuture#g-20-use-a-more-recent-version-of-solidity)


-----


## 5.Add constructor initializers

As per OpenZeppelin’s (OZ) recommendation, “The guidelines are now to make it impossible for anyone to run initialize on an implementation contract, by adding an empty constructor with the initializer modifier. So the implementation contract gets initialized automatically upon deployment.”

Note that this behaviour is also incorporated the OZ Wizard since the UUPS vulnerability discovery: “Additionally, we modified the code generated by the Wizard 19 to include a constructor that automatically initializes the implementation when deployed.”

Furthermore, this thwarts any attempts to frontrun the initialization tx of these contracts:
### Instances:
```
src/auction/IAuction.sol:93:    function initialize(
src/auction/Auction.sol:54:    function initialize(
src/manager/Manager.sol:80:    function initialize(address _owner) external initializer {
src/governance/treasury/ITreasury.sol:64:    function initialize(address governor, uint256 delay) external;
src/governance/treasury/Treasury.sol:43:    function initialize(address _governor, uint256 _delay) external initializer {
src/governance/governor/Governor.sol:57:    function initialize(
src/governance/governor/IGovernor.sol:119:    function initialize(
src/token/metadata/interfaces/IBaseMetadata.sol:27:    function initialize(
src/token/metadata/MetadataRenderer.sol:45:    function initialize(
src/token/IToken.sol:51:    function initialize(
src/token/Token.sol:43:    function initialize(
```
### References:
https://github.com/code-423n4/2022-04-phuture-findings/issues/12 (See L01)