1 - Internal functions only called once can be inlined to save gas
==

Not inlining costs more gas because of extra ```JUMP``` instructions and additional stack operations needed for function calls.

Instances of this issue:

```
src/token/Token.sol#L130                         function _getNextTokenId(uint256 _tokenId) internal view returns (uint256) {
src/token/metadata/MetadataRenderer.sol#L250     function _generateSeed(uint256 _tokenId) private view returns (uint256) {
```

2 - Unspecific Compiler Version Pragma
==

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Instances of this issue:

```
  src/lib/proxy/ERC1967Proxy.sol#L2         pragma solidity ^0.8.4;
  src/lib/proxy/ERC1967Upgrade.sol#L2       pragma solidity ^0.8.4;
  src/lib/proxy/UUPS.sol#L2                 pragma solidity ^0.8.4;
  src/lib/token/ERC721.sol#L2               pragma solidity ^0.8.4;
  src/lib/token/ERC721Votes.sol#L2          pragma solidity ^0.8.4;
```
