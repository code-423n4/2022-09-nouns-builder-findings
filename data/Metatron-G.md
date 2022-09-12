### [G-01] Upgrade pragma to 0.8.16 to save gas

From the 46 files in scope of the audit: 
26 are declared 0.8.15 
19 are declared 0.8.4 
and 1 is declared 0.8.0 

I recommend upgrading all to use 0.8.16 since it's more gas-efficient even in compared to 0.8.15:
Source:
```
According to the release note of 0.8.16: https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/
".. there are several minor bug fixes and improvements like more gas-efficient overflow checks for addition and subtraction."
```

If you doubt the new 0.8.16 for some reason, I recommend at least upgrading the other 20 to 0.8.15, for gas-efficiency:
```
According to the release note of 0.8.15: https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/
The benchmark shows saving of 2.5-10% Bytecode size,
Saving 2-8% Deployment gas,
And saving up to 6.2% Runtime gas.
```

List of files and their declared compiler version:
```
src/auction/Auction.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/auction/IAuction.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/auction/storage/AuctionStorageV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/auction/types/AuctionTypesV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/governance/governor/Governor.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/governance/governor/IGovernor.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/governance/governor/storage/GovernorStorageV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/governance/governor/types/GovernorTypesV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/governance/treasury/ITreasury.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/governance/treasury/Treasury.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/governance/treasury/storage/TreasuryStorageV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/governance/treasury/types/TreasuryTypesV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/lib/interfaces/IEIP712.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/interfaces/IERC721.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/interfaces/IERC721Votes.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/interfaces/IERC1967Upgrade.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/interfaces/IInitializable.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/interfaces/IOwnable.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/interfaces/IPausable.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/interfaces/IUUPS.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.15;
  3  

src/lib/interfaces/IWETH.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.15;
  3  

src/lib/proxy/ERC1967Proxy.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/proxy/ERC1967Upgrade.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/proxy/UUPS.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/token/ERC721.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/token/ERC721Votes.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/utils/Address.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/utils/EIP712.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/utils/Initializable.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/utils/Ownable.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/utils/Pausable.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/utils/ReentrancyGuard.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/utils/SafeCast.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.4;
  3  

src/lib/utils/TokenReceiver.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.0;
  3  

src/manager/IManager.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/manager/Manager.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/manager/storage/ManagerStorageV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/IToken.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/Token.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/metadata/MetadataRenderer.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/metadata/interfaces/IBaseMetadata.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/metadata/storage/MetadataRendererStorageV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/metadata/types/MetadataRendererTypesV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/storage/TokenStorageV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  

src/token/types/TokenTypesV1.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity 0.8.15;
  3  
```

---------------------------------------------------------------------------

### [G-02] Using default values is cheaper than assignment

If a variable is not set/initialized, it is assumed to have the default value 0 for uint, and false for boolean.
Explicitly initializing it with its default value is an anti-pattern and wastes gas.
For example: ```uint8 i = 0;``` should be replaced with ```uint8 i;```

I've found 5 locations in 2 files:

```
src/governance/treasury/Treasury.sol:
  161              // For each target:
  162:             for (uint256 i = 0; i < numTargets; ++i) {
  163                  // Execute the transaction

src/token/metadata/MetadataRenderer.sol:
  118              // For each new property:
  119:             for (uint256 i = 0; i < numNewProperties; ++i) {
  120                  // Append storage space

  132              // For each new item:
  133:             for (uint256 i = 0; i < numNewItems; ++i) {
  134                  // Cache the id of the associated property

  188              // For each property:
  189:             for (uint256 i = 0; i < numProperties; ++i) {
  190                  // Get the number of items to choose from

  228              // For each of the token's properties:
  229:             for (uint256 i = 0; i < numProperties; ++i) {
  230                  // Check if this is the last property
```

---------------------------------------------------------------------------

### [G-03] != 0 is cheaper than > 0

!= 0 costs less gas compared to > 0 for unsigned integers even when optimizer enabled
All of the following 3 findings are uint - so >0 and != have exactly the same effect.
** saves 6 gas ** each

I've found 3 locations in 3 files:

```
src/lib/proxy/ERC1967Upgrade.sol:
  60  
  61:         if (_data.length > 0 || _forceCall) {
  62              Address.functionDelegateCall(_newImpl, _data);

src/lib/token/ERC721Votes.sol:
  202              // If voting weight is being transferred:
  203:             if (_from != _to && _amount > 0) {
  204                  // If this isn't a token mint:

src/lib/utils/Address.sol:
  49          } else {
  50:             if (_returndata.length > 0) {
  51                  assembly {

```

