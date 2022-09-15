# Report
## Non-Critical Issues ##

### [N-01]: NatSpec is incomplete

+ @return tags are missing in all functions (where it is needed) in all contracts.

+ [ERC1967Upgrade._upgradeToAndCall](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L54) (@param tag for _forceCall is missing)

+ [ERC1967Upgrade._upgradeToAndCallUUPS](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L33) (@param tag for _forceCall is missing)

+ [Address.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol) (all functions have no @param tags)

+ [constructor](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L55) (NatSpec is missing)

+ [ERC721.safeTransferFrom](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L174) (@param tag for _data is missing)

+ [MetadataRenderer._generateSeed](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L250) (@param tag for _tokenId is missing)

+ [MetadataRenderer._getItemImage](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L255) (@param tags are missing)

+ [MetadataRenderer._encodeAsJson](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L308) (@param tag is missing)

+ @param tags are missing in all events in files: [IGovernor.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol), [ITreasury.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol),[IERC721Votes.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol), [IInitializable.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol), [IPropertyIPFSMetadataRenderer.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol)

+ NatSpec is incomplete: [IWETH.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IWETH.sol), [SafeCast.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol), [TokenReceiver.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol), [etadataRendererTypesV1.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/types/MetadataRendererTypesV1.sol)


### [N-02]: Public function can be external ###
**Context:** 

+ [Treasury.onERC721Received](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L237)

+ [Treasury.onERC1155Received](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L247)

+ [Treasury.onERC1155BatchReceived](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L258)

+ [ERC721.tokenURI](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L54)

+ [ERC721.contractURI](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L57)

+ [ERC721.balanceOf](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L83)

+ [ERC721.ownerOf](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L91)

+ [ERC721Votes.getVotes](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L45)

+ [ERC721Votes.getPastVotes](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L59)

+ [EIP712.DOMAIN_SEPARATOR](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L63)

+ [Ownable.owner](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L52)

+ [Ownable.pendingOwner](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L57)

+ [Ownable.transferOwnership](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L63)

+ [Ownable.safeTransferOwnership](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L71)

+ [Ownable.acceptOwnership](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L78)

+ [Ownable.cancelOwnershipTransfer](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L87)

+ [Token.tokenURI](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L221)

+ [Token.contractURI](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L226)
 

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.

