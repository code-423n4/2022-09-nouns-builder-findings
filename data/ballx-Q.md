### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
src/auction/Auction.sol::192 => token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
src/auction/Auction.sol::363 => IWETH(WETH).transfer(_to, _amount);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
src/lib/interfaces/IEIP712.sol::2 => pragma solidity ^0.8.4;
src/lib/interfaces/IERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
src/lib/interfaces/IERC721.sol::2 => pragma solidity ^0.8.4;
src/lib/interfaces/IERC721Votes.sol::2 => pragma solidity ^0.8.4;
src/lib/interfaces/IInitializable.sol::2 => pragma solidity ^0.8.4;
src/lib/interfaces/IOwnable.sol::2 => pragma solidity ^0.8.4;
src/lib/interfaces/IPausable.sol::2 => pragma solidity ^0.8.4;
src/lib/interfaces/IUUPS.sol::2 => pragma solidity ^0.8.15;
src/lib/interfaces/IWETH.sol::2 => pragma solidity ^0.8.15;
src/lib/proxy/ERC1967Proxy.sol::2 => pragma solidity ^0.8.4;
src/lib/proxy/ERC1967Upgrade.sol::2 => pragma solidity ^0.8.4;
src/lib/proxy/UUPS.sol::2 => pragma solidity ^0.8.4;
src/lib/token/ERC721.sol::2 => pragma solidity ^0.8.4;
src/lib/token/ERC721Votes.sol::2 => pragma solidity ^0.8.4;
src/lib/utils/Address.sol::2 => pragma solidity ^0.8.4;
src/lib/utils/EIP712.sol::2 => pragma solidity ^0.8.4;
src/lib/utils/Initializable.sol::2 => pragma solidity ^0.8.4;
src/lib/utils/Ownable.sol::2 => pragma solidity ^0.8.4;
src/lib/utils/Pausable.sol::2 => pragma solidity ^0.8.4;
src/lib/utils/ReentrancyGuard.sol::2 => pragma solidity ^0.8.4;
src/lib/utils/SafeCast.sol::2 => pragma solidity ^0.8.4;
src/lib/utils/TokenReceiver.sol::2 => pragma solidity ^0.8.0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

