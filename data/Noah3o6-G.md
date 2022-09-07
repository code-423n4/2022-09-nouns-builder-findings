-> X = X + Y IS CHEAPER THAN X += Y (same for X = X - Y IS CHEAPER THAN X -= Y)

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#:~:text=_propertyId%20%2B%3D%20numStoredProperties%3B


->STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas)

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#:~:text=address%20private%20immutable%20__self%20%3D%20address(this)%3B

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#:~:text=bytes32%20private%20immutable%20metadataHash%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#:~:text=bytes32%20private%20immutable%20auctionHash%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#:~:text=bytes32%20private%20immutable%20treasuryHash%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#:~:text=bytes32%20private%20immutable%20governorHash%3B


-> USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#:~:text=uint8%20minBidIncrement%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#:~:text=uint256%20_deadline%2C-,uint8%20_v%2C,-bytes32%20_r%2C
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#:~:text=uint256%20deadline%2C-,uint8%20v%2C,-bytes32%20r%2C
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#:~:text=uint256%20deadline%2C-,uint8%20v%2C,-bytes32%20r%2C
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#:~:text=uint256%20_deadline%2C-,uint8%20_v%2C,-bytes32%20_r%2C
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#:~:text=uint8%20internal%20_initialized%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#:~:text=%3E%20type(-,uint16,-).max)%20revert
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#:~:text=return-,uint16,-(x)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#:~:text=%3E%20type(-,uint8,-).max)%20revert
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#:~:text=return-,uint8,-(x)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#:~:text=(uint256%20%3D%3E-,uint16%5B16%5D,-)%20internal%20attributes
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#:~:text=0%5D%20%3D-,uint16,-(numProperties)%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#:~:text=token%27s%20generated%20attributes-,uint16%5B16%5D,-memory%20tokenAttributes%20%3D
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#:~:text=IBaseMetadata%20metadataRenderer%3B-,uint8%20numFounders%3B,-uint8%20totalOwnership%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#:~:text=uint8%20totalOwnership%3B
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#:~:text=address%20wallet%3B-,uint8%20ownershipPct%3B,-uint32%20vestExpiry%3B


