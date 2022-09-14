## QA

### Non-critical
### Events not indexed
Off-chain scripts cannot efficiently filter these events because they are not indexed.

Example how events should be indexed:
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IOwnable.sol#L15-L25

Locations where it is necessary to add index to events:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L22-L50

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L18-L57

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L16-L28

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L13

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L14

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L14-L18

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L21-L31

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol#L21

### Need to create a new modifier onlyAuction for Token.sol
Duplicate 1: [`Token.sol#L145-L148`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L145-L148)
Duplicate 2: [`Token.sol#L209`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L209)

Token.sol has a validation duplication and there should be created a new modifier in order to reduce code.

**Recommendation**: 
```diff=
+    modifier onlyAuction {
+      if (msg.sender != minter) revert ONLY_AUCTION();
+      _;
+    }
-    function mint() external nonReentrant returns (uint256 tokenId) {
+    function mint() external nonReentrant onlyAuction returns (uint256 tokenId) {
-        // Cache the auction address
-        address minter = settings.auction;

-        // Ensure the caller is the auction
-        if (msg.sender != minter) revert ONLY_AUCTION();

        // Cannot realistically overflow
        unchecked {
            do {
                // Get the next token to mint
                tokenId = settings.totalSupply++;

                // Lookup whether the token is for a founder, and mint accordingly if so
            } while (_isForFounder(tokenId));
        }

        // Mint the next available token to the auction house for bidding
-        _mint(minter, tokenId);
+        _mint(msg.sender, tokenId);
    }
-    function burn(uint256 _tokenId) external {
+    function burn(uint256 _tokenId) external onlyAuction {
-        // Ensure the caller is the auction house
-        if (msg.sender != settings.auction) revert ONLY_AUCTION();

        // Burn the token
        _burn(_tokenId);
    }
```