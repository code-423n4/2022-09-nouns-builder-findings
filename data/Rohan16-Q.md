## 1. UNUSED/EMPTY RECEIVE()/FALLBACK() FUNCTION
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth)))

There are 1 finding of this issue:
//Links to github files

[Treasury.sol:L269](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L269)

```
//actual codes used 
governance/treasury/Treasury.sol:269:    receive() external payable {}
```


------
## 2. Multiple initialization due to initialize function not having initializer modifier.

### Description

The attacker can initialize the contract, take malicious actions, and allow it to be re-initialized by the project without any error being noticed.

### Findings

[IToken.sol:L51](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/IToken.sol#L51)
[IBaseMetadata.sol:L27](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/srct/oken/metadata/interfaces/IBaseMetadata.sol#L27)
[IAuction.sol:L93](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/IAuction.sol#L93)
[ITreasury.sol:L64](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/ITreasury.sol#L64)
[IGovernor.sol:L119](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/IGovernor.sol#L119)

```
//actual codes used
token/IToken.sol:51:    function initialize(
token/metadata/interfaces/IBaseMetadata.sol:27:    function initialize(
auction/IAuction.sol:93:    function initialize(
governance/treasury/ITreasury.sol:64:    function initialize(address governor, uint256 delay) external;
src/governance/governor/IGovernor.sol:119:    function initialize(


```

------
## 3. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom
### Description
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

### Findings

[Auction.sol:L192](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L192)
[Auction.sol:L363](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L363)

```
auction/Auction.sol:192:            token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
auction/Auction.sol:363:            IWETH(WETH).transfer(_to, _amount);
```

### Recommended Mitigation Steps

Consider using safeTransfer/safeTransferFrom or require() consistently.

------

## 4.Avoid using Floating Pragma
In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
### Findings

[ERC1967Upgrade.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L2)
```
lib/proxy/ERC1967Upgrade.sol:2:pragma solidity ^0.8.4;
```
[ERC1967Proxy.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Proxy.sol#L2)
```
lib/proxy/ERC1967Proxy.sol:2:pragma solidity ^0.8.4;
```
[UUPS.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/srclib/proxy/UUPS.sol#L2)
```
lib/proxy/UUPS.sol:2:pragma solidity ^0.8.4;
```
[ERC721Votes.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L2)
```
lib/token/ERC721Votes.sol:2:pragma solidity ^0.8.4;
```
[ERC721.sol:L2](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L2)
```
lib/token/ERC721.sol:2:pragma solidity ^0.8.4;
```


-------
## 5. Immutable addresses lack zero-address check

Constructors should check the address written in an immutable address variable is not the zero address.

### Findings

```
//code used in contract

src/auction/Auction.sol:41:        WETH = _weth;

```

```
  constructor(address _manager, address _weth) payable initializer {
        manager = IManager(_manager);
        WETH = _weth;
   }

```

-----

## 6. USE _SAFEMINT() INSTEAD OF _MINT()

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`.

### Findings
[Token.sol:L161](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L161)
[Token.sol:L188](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L188)


```
src/token/Token.sol:161:        _mint(minter, tokenId);
src/token/Token.sol:188:            _mint(tokenRecipient[baseTokenId].wallet, _tokenId);
```

## 7. Add constructor initializer in implementation contracts
Description

As per OpenZeppelin’s (OZ) recommendation, “The guidelines are now to make it impossible for anyone to run initialize on an implementation contract, by adding an empty constructor with the initializer modifier. So the implementation contract gets initialized automatically upon deployment.”

Note that this behaviour is also incorporated the OZ Wizard since the UUPS vulnerability discovery: “Additionally, we modified the code generated by the Wizard 19 to include a constructor that automatically initializes the implementation when deployed.”

Furthermore, this thwarts any attempts to frontrun the initialization tx of the implementation contract.

Incorporating this change would require inheriting the Initializable contract instead of having an explicit initialized variable.

### Findings
```
src/lib/proxy/ERC1967Proxy.sol:21:    constructor(address _logic, bytes memory _data) payable {
src/manager/Manager.sol:55:    constructor(
src/governance/treasury/Treasury.sol:32:    constructor(address _manager) payable initializer {
src/governance/governor/Governor.sol:41:    constructor(address _manager) payable initializer {
src/auction/Auction.sol:39:    constructor(address _manager, address _weth) payable initializer {
src/token/metadata/MetadataRenderer.sol:32:    constructor(address _manager) payable initializer {
src/token/Token.sol:30:    constructor(address _manager) payable initializer {
```

