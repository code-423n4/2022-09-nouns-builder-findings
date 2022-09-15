# Nouns Builder Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unsafe ERC20 Operation(s) (2 instances)
2. Unspecific Compiler Version Pragma (22 instances)

Total 24 instances of 2 issues.

## 1. Unsafe ERC20 Operation(s) (2 instances)

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

```solidity
src/auction/Auction.sol::192 => token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);

src/auction/Auction.sol::363 => IWETH(WETH).transfer(_to, _amount);
```

## 2. Unspecific Compiler Version Pragma (22 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
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
