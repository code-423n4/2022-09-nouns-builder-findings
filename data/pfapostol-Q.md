# Summary

## Low Risk Issues

|       | Issue                                                            | Instances |
| :---: | :--------------------------------------------------------------- | :-------: |
| **1** | Lack of zero checks for immutable variables                      |     9     |
| **2** | Use two-step procedure to avoid unintended burning of veto power |     1     |
| **3** | Check result of `WETH` transfer                                  |     1     |

## Non-critical Issues

|       | Issue                                                                                       | Instances |
| :---: | :------------------------------------------------------------------------------------------ | :-------: |
| **1** | Incomplite NatSpec                                                                          |     1     |
| **2** | Typos                                                                                       |     5     |
| **3** | The code repeats the functionality of the library function, instead of calling it directly. |     2     |
| **4** | `public` functions not called by the contract should be declared `external` instead         |     3     |

## Total: 22 instances over 7 issues

---

Low-Risk Issues:

1. **Lack of zero checks for immutable variables (9 instances)**

   - [src/auction/Auction.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol)

   ```diff
   diff --git a/src/auction/Auction.sol b/src/auction/Auction.sol
   index 794da99..bfd685c 100644
   --- a/src/auction/Auction.sol
   +++ b/src/auction/Auction.sol
   @@ -37,6 +37,7 @@ contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionS
      37,  37:     /// @param _manager The contract upgrade manager address
      38,  38:     /// @param _weth The address of WETH
      39,  39:     constructor(address _manager, address _weth) payable initializer {
   +       40:+        if (_manager == address(0) || _weth == address(0)) revert ZERO_ADDRESS();
      40,  41:         manager = IManager(_manager);
      41,  42:         WETH = _weth;
      42,  43:     }
   diff --git a/src/auction/IAuction.sol b/src/auction/IAuction.sol
   index 2a86610..19bc1ea 100644
   --- a/src/auction/IAuction.sol
   +++ b/src/auction/IAuction.sol
   @@ -80,6 +80,8 @@ interface IAuction is IUUPS, IOwnable, IPausable {
      80,  80:     /// @dev Reverts if the caller was not the contract manager
      81,  81:     error ONLY_MANAGER();
      82,  82:
   +       83:+    error ZERO_ADDRESS();
   +       84:+
      83,  85:     ///                                                          ///
      84,  86:     ///                          FUNCTIONS                       ///
      85,  87:     ///                                                          ///
   ```

   ```diff
   diff --git a/src/auction/Auction.sol b/src/auction/Auction.sol
   index bfd685c..78a86c5 100644
   --- a/src/auction/Auction.sol
   +++ b/src/auction/Auction.sol
   @@ -61,6 +61,7 @@ contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionS
   61,  61:     ) external initializer {
   62,  62:         // Ensure the caller is the contract manager
   63,  63:         if (msg.sender != address(manager)) revert ONLY_MANAGER();
   +       64:+        if (_token == address(0) || _founder == address(0) || _treasury == address(0)) revert ZERO_ADDRESS();
   64,  65:
   65,  66:         // Initialize the reentrancy guard
   66,  67:         __ReentrancyGuard_init();
   ```

   - [src/governance/governor/Governor.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol)

   ```diff
   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index 0d963c5..64ebf5f 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -39,6 +39,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
      39,  39:
      40,  40:     /// @param _manager The contract upgrade manager address
      41,  41:     constructor(address _manager) payable initializer {
   +       42:+        if (_manager == address(0)) revert ZERO_ADDRESS();
      42,  43:         manager = IManager(_manager);
      43,  44:     }
      44,  45:
   diff --git a/src/governance/governor/IGovernor.sol b/src/governance/governor/IGovernor.sol
   index 8e4f75d..aec9963 100644
   --- a/src/governance/governor/IGovernor.sol
   +++ b/src/governance/governor/IGovernor.sol
   @@ -104,6 +104,8 @@ interface IGovernor is IUUPS, IOwnable, IEIP712, GovernorTypesV1 {
     104, 104:     /// @dev Reverts if the caller was not the contract manager
     105, 105:     error ONLY_MANAGER();
     106, 106:
   +      107:+    error ZERO_ADDRESS();
   +      108:+
     107, 109:     ///                                                          ///
     108, 110:     ///                          FUNCTIONS                       ///
     109, 111:     ///                                                          ///
   ```

   - [src/governance/treasury/Treasury.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol)

   ```diff
   diff --git a/src/governance/treasury/ITreasury.sol b/src/governance/treasury/ITreasury.sol
   index b12f672..ccc813b 100644
   --- a/src/governance/treasury/ITreasury.sol
   +++ b/src/governance/treasury/ITreasury.sol
   @@ -54,6 +54,8 @@ interface ITreasury is IUUPS, IOwnable {
      54,  54:     /// @dev Reverts if the caller was not the contract manager
      55,  55:     error ONLY_MANAGER();
      56,  56:
   +       57:+    error ZERO_ADDRESS();
   +       58:+
      57,  59:     ///                                                          ///
      58,  60:     ///                          FUNCTIONS                       ///
      59,  61:     ///                                                          ///
   diff --git a/src/governance/treasury/Treasury.sol b/src/governance/treasury/Treasury.sol
   index b78bc8c..906dc24 100644
   --- a/src/governance/treasury/Treasury.sol
   +++ b/src/governance/treasury/Treasury.sol
   @@ -30,6 +30,7 @@ contract Treasury is ITreasury, UUPS, Ownable, TreasuryStorageV1 {
      30,  30:
      31,  31:     /// @param _manager The contract upgrade manager address
      32,  32:     constructor(address _manager) payable initializer {
   +       33:+        if (_manager == address(0)) revert ZERO_ADDRESS();
      33,  34:         manager = IManager(_manager);
      34,  35:     }
      35,  36:
   ```

   - [src/token/Token.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol)

   ```diff
   diff --git a/src/token/IToken.sol b/src/token/IToken.sol
   index 4e83558..24dae40 100644
   --- a/src/token/IToken.sol
   +++ b/src/token/IToken.sol
   @@ -39,6 +39,8 @@ interface IToken is IUUPS, IERC721Votes, TokenTypesV1 {
      39,  39:     /// @dev Reverts if the caller was not the contract manager
      40,  40:     error ONLY_MANAGER();
      41,  41:
   +       42:+    error ZERO_ADDRESS();
   +       43:+
      42,  44:     ///                                                          ///
      43,  45:     ///                           FUNCTIONS                      ///
      44,  46:     ///                                                          ///
   diff --git a/src/token/Token.sol b/src/token/Token.sol
   index afad142..f8a2735 100644
   --- a/src/token/Token.sol
   +++ b/src/token/Token.sol
   @@ -28,6 +28,7 @@ contract Token is IToken, UUPS, ReentrancyGuard, ERC721Votes, TokenStorageV1 {
      28,  28:
      29,  29:     /// @param _manager The contract upgrade manager address
      30,  30:     constructor(address _manager) payable initializer {
   +       31:+        if (_manager == address(0)) revert ZERO_ADDRESS();
      31,  32:         manager = IManager(_manager);
      32,  33:     }
      33,  34:
   ```

2. **Use two-step procedure to avoid unintended burning of veto power (1 instances)**

   - [src/governance/governor/Governor.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol)

   ```diff
   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index 0d963c5..1d099bf 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -596,6 +596,14 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
     596, 596:     function updateVetoer(address _newVetoer) external onlyOwner {
     597, 597:         if (_newVetoer == address(0)) revert ADDRESS_ZERO();
     598, 598:
   +      599:+        emit PendingVetoerUpdated(pendingVetoer, _newVetoer);
   +      600:+
   +      601:+        pendingVetoer = _newVetoer;
   +      602:+    }
   +      603:+
   +      604:+    function acceptVetoer() external {
   +      605:+        if (msg.sender != pendingVetoer) revert ONLY_PENDING_VETOER();
   +      606:+
     599, 607:         emit VetoerUpdated(settings.vetoer, _newVetoer);
     600, 608:
     601, 609:         settings.vetoer = _newVetoer;
   diff --git a/src/governance/governor/IGovernor.sol b/src/governance/governor/IGovernor.sol
   index 8e4f75d..f1ddf7e 100644
   --- a/src/governance/governor/IGovernor.sol
   +++ b/src/governance/governor/IGovernor.sol
   @@ -55,6 +55,7 @@ interface IGovernor is IUUPS, IOwnable, IEIP712, GovernorTypesV1 {
      55,  55:
      56,  56:     //// @notice Emitted when the governor's vetoer is updated
      57,  57:     event VetoerUpdated(address prevVetoer, address newVetoer);
   +       58:+    event PendingVetoerUpdated(address prevVetoer, address newVetoer);
      58,  59:
      59,  60:     ///                                                          ///
      60,  61:     ///                            ERRORS                        ///
   @@ -104,6 +105,7 @@ interface IGovernor is IUUPS, IOwnable, IEIP712, GovernorTypesV1 {
     104, 105:     /// @dev Reverts if the caller was not the contract manager
     105, 106:     error ONLY_MANAGER();
     106, 107:
   +      108:+    error ONLY_PENDING_VETOER();
     107, 109:     ///                                                          ///
     108, 110:     ///                          FUNCTIONS                       ///
     109, 111:     ///                                                          ///
   diff --git a/src/governance/governor/storage/GovernorStorageV1.sol b/src/governance/governor/storage/GovernorStorageV1.sol
   index beff22e..5973d5e 100644
   --- a/src/governance/governor/storage/GovernorStorageV1.sol
   +++ b/src/governance/governor/storage/GovernorStorageV1.sol
   @@ -17,4 +17,6 @@ contract GovernorStorageV1 is GovernorTypesV1 {
      17,  17:     /// @notice If a user has voted on a proposal
      18,  18:     /// @dev Proposal Id => User => Has Voted
      19,  19:     mapping(bytes32 => mapping(address => bool)) internal hasVoted;
   +       20:+
   +       21:+    address internal pendingVetoer;
      20,  22: }
   ```

3. **Check result of `WETH` transfer (1 instances)**

   - [src/auction/Auction.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol)

   ```diff
   diff --git a/src/auction/Auction.sol b/src/auction/Auction.sol
   index 794da99..8d8cae4 100644
   --- a/src/auction/Auction.sol
   +++ b/src/auction/Auction.sol
   @@ -360,7 +360,10 @@ contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionS
     360, 360:             IWETH(WETH).deposit{ value: _amount }();
     361, 361:
     362, 362:             // Transfer WETH instead
   - 363     :-            IWETH(WETH).transfer(_to, _amount);
   +      363:+            if (!IWETH(WETH).transfer(_to, _amount)) {
   +      364:+                revert WETH_TRANSFER_ERROR();
   +      365:+            }
   +      366:+
     364, 367:         }
     365, 368:     }
     366, 369:
   diff --git a/src/auction/IAuction.sol b/src/auction/IAuction.sol
   index 2a86610..3854b0a 100644
   --- a/src/auction/IAuction.sol
   +++ b/src/auction/IAuction.sol
   @@ -80,6 +80,8 @@ interface IAuction is IUUPS, IOwnable, IPausable {
     80,  80:     /// @dev Reverts if the caller was not the contract manager
     81,  81:     error ONLY_MANAGER();
     82,  82:
   +       83:+    error WETH_TRANSFER_ERROR();
   +       84:+
     83,  85:     ///                                                          ///
     84,  86:     ///                          FUNCTIONS                       ///
     85,  87:     ///                                                          ///
   ```

---

Non-critical Issues:

1. **Incomplite NatSpec (1 instance)**

   - [src/governance/governor/Governor.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol)

   ```solidity
   252                 string memory _reason // @audit not covered in NatSpec
   ```

2. **Typos (5 instances)**

   ```diff
   diff --git a/src/governance/governor/IGovernor.sol b/src/governance/governor/IGovernor.sol
   index 8e4f75d..30c6983 100644
   --- a/src/governance/governor/IGovernor.sol
   +++ b/src/governance/governor/IGovernor.sol
   @@ -287,7 +287,7 @@ interface IGovernor is IUUPS, IOwnable, IEIP712, GovernorTypesV1 {
     287, 287:     function updateQuorumThresholdBps(uint256 newQuorumVotesBps) external;
     288, 288:
     289, 289:     /// @notice Updates the vetoer
   - 290     :-    /// @param newVetoer The new vetoer addresss
   +      290:+    /// @param newVetoer The new vetoer address
     291, 291:     function updateVetoer(address newVetoer) external;
     292, 292:
     293, 293:     /// @notice Burns the vetoer
   diff --git a/src/lib/token/ERC721Votes.sol b/src/lib/token/ERC721Votes.sol
   index 3e64720..718aaff 100644
   --- a/src/lib/token/ERC721Votes.sol
   +++ b/src/lib/token/ERC721Votes.sol
   @@ -157,7 +157,7 @@ abstract contract ERC721Votes is IERC721Votes, EIP712, ERC721 {
     157, 157:
     158, 158:         // Cannot realistically overflow
     159, 159:         unchecked {
   - 160     :-            // Compute the hash of the domain seperator with the typed delegation data
   +      160:+            // Compute the hash of the domain separator with the typed delegation data
     161, 161:             digest = keccak256(
     162, 162:                 abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
     163, 163:             );
   diff --git a/src/manager/Manager.sol b/src/manager/Manager.sol
   index d1025ec..ccca9ec 100644
   --- a/src/manager/Manager.sol
   +++ b/src/manager/Manager.sol
   @@ -110,7 +110,7 @@ contract Manager is IManager, UUPS, Ownable, ManagerStorageV1 {
     110, 110:         )
     111, 111:     {
     112, 112:         // Used to store the address of the first (or only) founder
   - 113     :-        // This founder is responsible for adding token artwork and launching the first auction -- they're also free to transfer this responsiblity
   +      113:+        // This founder is responsible for adding token artwork and launching the first auction -- they're also free to transfer this responsibility
     114, 114:         address founder;
     115, 115:
     116, 116:         // Ensure at least one founder is provided
   diff --git a/src/token/Token.sol b/src/token/Token.sol
   index afad142..a724efa 100644
   --- a/src/token/Token.sol
   +++ b/src/token/Token.sol
   @@ -101,7 +101,7 @@ contract Token is IToken, UUPS, ReentrancyGuard, ERC721Votes, TokenStorageV1 {
     101, 101:                 // Compute the vesting schedule
     102, 102:                 uint256 schedule = 100 / founderPct;
     103, 103:
   - 104     :-                // Used to store the base token id the founder will recieve
   +      104:+                // Used to store the base token id the founder will receive
     105, 105:                 uint256 baseTokenId;
     106, 106:
     107, 107:                 // For each token to vest:
   diff --git a/src/token/metadata/MetadataRenderer.sol b/src/token/metadata/MetadataRenderer.sol
   index 7a140ec..3f14c2a 100644
   --- a/src/token/metadata/MetadataRenderer.sol
   +++ b/src/token/metadata/MetadataRenderer.sol
   @@ -246,7 +246,7 @@ contract MetadataRenderer is IPropertyIPFSMetadataRenderer, UUPS, Ownable, Metad
     246, 246:         }
     247, 247:     }
     248, 248:
   - 249     :-    /// @dev Generates a psuedo-random seed for a token id
   +      249:+    /// @dev Generates a pseudo-random seed for a token id
     250, 250:     function _generateSeed(uint256 _tokenId) private view returns (uint256) {
     251, 251:         return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
     252, 252:     }
   ```

3. **`public` functions not called by the contract should be declared `external` instead (3 instances)**

   Contracts are allowed to override their parents' functions and change the visibility from `external` to `public`.

   - [src/governance/treasury/Treasury.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol)

   ```diff
   diff --git a/src/governance/treasury/Treasury.sol b/src/governance/treasury/Treasury.sol
   index b78bc8c..f3e6d67 100644
   --- a/src/governance/treasury/Treasury.sol
   +++ b/src/governance/treasury/Treasury.sol
   @@ -239,7 +239,7 @@ contract Treasury is ITreasury, UUPS, Ownable, TreasuryStorageV1 {
   239, 239:         address,
   240, 240:         uint256,
   241, 241:         bytes memory
   - 242     :-    ) public pure returns (bytes4) {
   +      242:+    ) external pure returns (bytes4) {
   243, 243:         return ERC721TokenReceiver.onERC721Received.selector;
   244, 244:     }
   245, 245:
   @@ -250,7 +250,7 @@ contract Treasury is ITreasury, UUPS, Ownable, TreasuryStorageV1 {
   250, 250:         uint256,
   251, 251:         uint256,
   252, 252:         bytes memory
   - 253     :-    ) public pure returns (bytes4) {
   +      253:+    ) external pure returns (bytes4) {
   254, 254:         return ERC1155TokenReceiver.onERC1155Received.selector;
   255, 255:     }
   256, 256:
   @@ -261,7 +261,7 @@ contract Treasury is ITreasury, UUPS, Ownable, TreasuryStorageV1 {
   261, 261:         uint256[] memory,
   262, 262:         uint256[] memory,
   263, 263:         bytes memory
   - 264     :-    ) public pure returns (bytes4) {
   +      264:+    ) external pure returns (bytes4) {
   265, 265:         return ERC1155TokenReceiver.onERC1155BatchReceived.selector;
   266, 266:     }
   267, 267:
   ```

4. **The code repeats the functionality of the library function, instead of calling it directly. (2 instances)**

   - [src/manager/Manager.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol)

   ```diff
   diff --git a/src/manager/Manager.sol b/src/manager/Manager.sol
   index d1025ec..55a6c9c 100644
   --- a/src/manager/Manager.sol
   +++ b/src/manager/Manager.sol
   @@ -4,6 +4,7 @@ pragma solidity 0.8.15;
       4,   4: import { UUPS } from "../lib/proxy/UUPS.sol";
       5,   5: import { Ownable } from "../lib/utils/Ownable.sol";
       6,   6: import { ERC1967Proxy } from "../lib/proxy/ERC1967Proxy.sol";
   +        7:+import { Address } from "../lib/utils/Address.sol";
       7,   8:
       8,   9: import { ManagerStorageV1 } from "./storage/ManagerStorageV1.sol";
       9,  10: import { IManager } from "./IManager.sol";
   @@ -17,6 +18,7 @@ import { IGovernor } from "../governance/governor/IGovernor.sol";
     17,  18: /// @author Rohan Kulkarni
     18,  19: /// @notice The DAO deployer and upgrade manager
     19,  20: contract Manager is IManager, UUPS, Ownable, ManagerStorageV1 {
   +       21:+    using Address for address;
     20,  22:     ///                                                          ///
     21,  23:     ///                          IMMUTABLES                      ///
     22,  24:     ///                                                          ///
   @@ -120,7 +122,7 @@ contract Manager is IManager, UUPS, Ownable, ManagerStorageV1 {
     120, 122:         token = address(new ERC1967Proxy(tokenImpl, ""));
     121, 123:
     122, 124:         // Use the token address to precompute the DAO's remaining addresses
   - 123     :-        bytes32 salt = bytes32(uint256(uint160(token)) << 96);
   +      125:+        bytes32 salt = token.toBytes32();
     124, 126:
     125, 127:         // Deploy the remaining DAO contracts
     126, 128:         metadata = address(new ERC1967Proxy{ salt: salt }(metadataImpl, ""));
   @@ -162,7 +164,7 @@ contract Manager is IManager, UUPS, Ownable, ManagerStorageV1 {
     162, 164:             address governor
     163, 165:         )
     164, 166:     {
   - 165     :-        bytes32 salt = bytes32(uint256(uint160(_token)) << 96);
   +      167:+        bytes32 salt = _token.toBytes32();
     166, 168:
     167, 169:         metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
     168, 170:         auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
   ```
