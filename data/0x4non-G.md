# Gas report


## Use cached `_auction` varible instead of state variable.

```diff
diff --git a/src/auction/Auction.sol b/src/auction/Auction.sol
index 794da99..3754f3b 100644
--- a/src/auction/Auction.sol
+++ b/src/auction/Auction.sol
@@ -169,7 +169,7 @@ contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionS
         Auction memory _auction = auction;
 
         // Ensure the auction wasn't already settled
-        if (auction.settled) revert AUCTION_SETTLED();
+        if (_auction.settled) revert AUCTION_SETTLED();
 
         // Ensure the auction had started
         if (_auction.startTime == 0) revert AUCTION_NOT_STARTED();
```

---

## Use Reentrancy guard based on solmate implementation, plus use `uint32` instead of `uint256`, overall savings;
`Overall gas change: -475255 (-1.036%)`
Changes;
```diff
diff --git a/src/lib/utils/ReentrancyGuard.sol b/src/lib/utils/ReentrancyGuard.sol
index 8c6dd15..7afec50 100644
--- a/src/lib/utils/ReentrancyGuard.sol
+++ b/src/lib/utils/ReentrancyGuard.sol
@@ -10,14 +10,8 @@ abstract contract ReentrancyGuard is Initializable {
     ///                            STORAGE                       ///
     ///                                                          ///
 
-    /// @dev Indicates a function has not been entered
-    uint256 internal constant _NOT_ENTERED = 1;
-
-    /// @dev Indicates a function has been entered
-    uint256 internal constant _ENTERED = 2;
-
     /// @notice The reentrancy status of a function
-    uint256 internal _status;
+    uint32 private _status;
 
     ///                                                          ///
     ///                            ERRORS                        ///
@@ -32,17 +26,19 @@ abstract contract ReentrancyGuard is Initializable {
 
     /// @dev Initializes the reentrancy guard
     function __ReentrancyGuard_init() internal onlyInitializing {
-        _status = _NOT_ENTERED;
+        // 1 = _NOT_ENTERED
+        _status = 1;
     }
 
     /// @dev Ensures a function cannot be reentered
     modifier nonReentrant() {
-        if (_status == _ENTERED) revert REENTRANCY();
-
-        _status = _ENTERED;
+        if (_status == 2) revert REENTRANCY();
+        // 2 = _ENTERED
+        _status = 2;
 
         _;
 
-        _status = _NOT_ENTERED;
+        // 1 = _NOT_ENTERED
+        _status = 1;
     }
 }
```

---

## Inline modifier (used only once)

You are using the modifier [`onlyPendingOwner`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L34) only once, insteaf of create a modifier just add a check on `acceptOwnership`to save gas
```diff
diff --git a/src/lib/utils/Ownable.sol b/src/lib/utils/Ownable.sol
index 04b5854..8de8863 100644
--- a/src/lib/utils/Ownable.sol
+++ b/src/lib/utils/Ownable.sol
@@ -30,12 +30,6 @@ abstract contract Ownable is IOwnable, Initializable {
         _;
     }
 
-    /// @dev Ensures the caller is the pending owner
-    modifier onlyPendingOwner() {
-        if (msg.sender != _pendingOwner) revert ONLY_PENDING_OWNER();
-        _;
-    }
-
     ///                                                          ///
     ///                           FUNCTIONS                      ///
     ///                                                          ///
@@ -75,7 +69,8 @@ abstract contract Ownable is IOwnable, Initializable {
     }
 
     /// @notice Accepts an ownership transfer
-    function acceptOwnership() public onlyPendingOwner {
+    function acceptOwnership() public {
+        if (msg.sender != _pendingOwner) revert ONLY_PENDING_OWNER();
         emit OwnerUpdated(_owner, msg.sender);
 
         _owner = _pendingOwner;
```

---

## These funcions can be declared as external to save gas;
[cancelOwnershipTransfer](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Ownable.sol#L87)