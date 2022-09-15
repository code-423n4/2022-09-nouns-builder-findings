# QA


## Variables `_owner` and `_pendingOwner` should be **private** instead of **internal**
Variables `_owner` and `_pendingOwner` should be **private** instead of **internal** to avoid other contracts messing around with this variables;
[Ownable.sol#L17-L21](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L17-L21)

---

## Avoid shadow variables

Shadow variable from [Ownable.sol#L18](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L18)

```diff
diff --git a/src/manager/Manager.sol b/src/manager/Manager.sol
index d1025ec..86a94e5 100644
--- a/src/manager/Manager.sol
+++ b/src/manager/Manager.sol
@@ -76,13 +76,13 @@ contract Manager is IManager, UUPS, Ownable, ManagerStorageV1 {
     ///                                                          ///
 
     /// @notice Initializes ownership of the manager contract
-    /// @param _owner The owner address to set (will be transferred to the Builder DAO once its deployed)
-    function initialize(address _owner) external initializer {
+    /// @param owner_ The owner address to set (will be transferred to the Builder DAO once its deployed)
+    function initialize(address owner_) external initializer {
         // Ensure an owner is specified
-        if (_owner == address(0)) revert ADDRESS_ZERO();
+        if (owner_ == address(0)) revert ADDRESS_ZERO();
 
         // Set the contract owner
-        __Ownable_init(_owner);
+        __Ownable_init(owner_);
     }
 
     ///                                                          ///
```


---

Unsafe downcasting to `_newTotalVotes` on `_writeCheckpoint` method
[ERC721Votes.sol#L252](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L252)

If `_newTotalVotes` are greater than `type(uint192).max` they will be downcast to `type(uint192).max`



---


The function `registerUpgrade(address _baseImpl, address _upgradeImpl)` should check that `_upgradeImpl` is a contract
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L187

---

## Private variables should start with underscore
On [Token.sol#L23](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L23) private variable `manager` should be `_manager`

---

## Interfaces filename should have `I` prefix
So [MetadataRendererTypesV1.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/types/MetadataRendererTypesV1.sol) should be `IMetadataRendererTypesV1.sol`


---

### Interfaces should have open pragma

[IManager.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/IManager.sol#L2)
[ITreasury.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/ITreasury.sol#L2)
[IToken.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/IToken.sol#L2)
[IGovernor.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/IGovernor.sol#L2)
[IBaseMetadata.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/interfaces/IBaseMetadata.sol#L2)
[IPropertyIPFSMetadataRenderer.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L2)
[MetadataRendererTypesV1.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/types/MetadataRendererTypesV1.sol#L2)
[IAuction.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/IAuction.sol#L2)

---

## Remove unused function
This functions are never used
```diff
diff --git a/src/lib/utils/Address.sol b/src/lib/utils/Address.sol
index 3447bc1..ec327cb 100644
--- a/src/lib/utils/Address.sol
+++ b/src/lib/utils/Address.sol
@@ -21,10 +21,6 @@ library Address {
     ///                           FUNCTIONS                      ///
     ///                                                          ///
 
-    /// @dev Utility to convert an address to bytes32
-    function toBytes32(address _account) internal pure returns (bytes32) {
-        return bytes32(uint256(uint160(_account)) << 96);
-    }
 
     /// @dev If an address is a contract
     function isContract(address _account) internal view returns (bool rv) {
```

Also Remove 
[Initializable.sol:_disableInitializers()](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L74)


---
