# [G-01] Next available founder token ID can be computed in memory
## Targets:
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L110
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L132
## Description
When finding next available token ID for a founder, there's no need to read the `tokenRecipient` state variable. Since
founder token IDs are scattered evenly and shifted by 1, `baseTokenId` is simply the index of a founder. Here's how
the `Token` contract can be patched:
```diff

diff --git a/src/token/Token.sol b/src/token/Token.sol
index afad142..7c75505 100644
--- a/src/token/Token.sol
+++ b/src/token/Token.sol
@@ -102,13 +102,10 @@ contract Token is IToken, UUPS, ReentrancyGuard, ERC721Votes, TokenStorageV1 {
                 uint256 schedule = 100 / founderPct;

                 // Used to store the base token id the founder will recieve
-                uint256 baseTokenId;
+                uint256 baseTokenId = i;

                 // For each token to vest:
                 for (uint256 j; j < founderPct; ++j) {
-                    // Get the available token id
-                    baseTokenId = _getNextTokenId(baseTokenId);
-
                     // Store the founder as the recipient
                     tokenRecipient[baseTokenId] = newFounder;

@@ -125,16 +122,6 @@ contract Token is IToken, UUPS, ReentrancyGuard, ERC721Votes, TokenStorageV1 {
         }
     }

-    /// @dev Finds the next available base token id for a founder
-    /// @param _tokenId The ERC-721 token id
-    function _getNextTokenId(uint256 _tokenId) internal view returns (uint256) {
-        unchecked {
-            while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;
-
-            return _tokenId;
-        }
-    }
-
     ///                                                          ///
     ///                             MINT                         ///
     ///                                                          ///
```

# [G-02] Re-use `currentProposalThreshold`
## Targets:
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L128
## Description
Instead of calculating `proposalThreshold()` again, re-use the previously calculated value:
```diff
diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
index 0d963c5..b441972 100644
--- a/src/governance/governor/Governor.sol
+++ b/src/governance/governor/Governor.sol
@@ -125,7 +125,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
         // Cannot realistically underflow and `getVotes` would revert
         unchecked {
             // Ensure the caller's voting weight is greater than or equal to the threshold
-            if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
+            if (getVotes(msg.sender, block.timestamp - 1) < currentProposalThreshold) revert BELOW_PROPOSAL_THRESHOLD();
         }

         // Cache the number of targets
```