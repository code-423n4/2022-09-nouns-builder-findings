- [Gas](#gas)
    - [**1. Avoid compound assignment operator in state variables**](#1-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 3 = 39**](#total-gas-saved-13--3--39)
    - [**2. Shift right instead of dividing by 2**](#2-shift-right-instead-of-dividing-by-2)
        - [Total gas saved: **172 * 1 = 172**](#total-gas-saved-172--1--172)
    - [**3. Remove empty blocks**](#3-remove-empty-blocks)
    - [**4. Use calldata instead of memory**](#4-use-calldata-instead-of-memory)
    - [**5. Change bool to uint256 can save gas**](#5-change-bool-to-uint256-can-save-gas)
    - [**6. constants expressions are expressions, not constants**](#6-constants-expressions-are-expressions-not-constants)
    - [**7. Optimize ERC721Votes.delegateBySig logic**](#7-optimize-erc721votesdelegatebysig-logic)
    - [**8. Optimize MetadataRenderer.initialize logic**](#8-optimize-metadatarendererinitialize-logic)
    - [**9. There's no need to set default values for variables**](#9-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 5 = 40**](#total-gas-saved-8--5--40)
    - [**10. Optimize MetadataRenderer.onMinted logic**](#10-optimize-metadatarendereronminted-logic)
    - [**11. Avoid unused returns**](#11-avoid-unused-returns)
    - [**12. Optimize MetadataRenderer.getAttributes**](#12-optimize-metadatarenderergetattributes)
    - [**13. Change arguments type in MetadataRenderer**](#13-change-arguments-type-in-metadatarenderer)
    - [**14. Optimize Token.initialize**](#14-optimize-tokeninitialize)
    - [**15. Remove unused math**](#15-remove-unused-math)
    - [**16. Use array instead of mapping**](#16-use-array-instead-of-mapping)
    - [**17. Change arguments type in Auction**](#17-change-arguments-type-in-auction)
    - [**18. Change arguments type in Governor**](#18-change-arguments-type-in-governor)

# Gas

## **1. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
uint private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [Governor.sol:280](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L280)
- [Governor.sol:285](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L285)
- [Governor.sol:290](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L290)

### Total gas saved: **13 * 3 = 39**

## **2. Shift right instead of dividing by 2**

Shifting one to the right will calculate a division by two.

he `SHR` opcode only requires 3 gas, compared to the `DIV` opcode's consumption of 5. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testDiv(uint a) public returns (uint) { return a / 2; }
}

contract TesterB {
function testShift(uint a) public returns (uint) { return a >> 1; }
}
```

Gas saving executing: **172 per entry**

```
TesterA.testDiv:    21965
TesterB.testShift:  21793   
```

**Affected source code:**

- [ERC721Votes.sol:95](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L95)

### Total gas saved: **172 * 1 = 172**

## **3. Remove empty blocks**

An empty block or an empty method generally indicates a lack of logic that it’s unnecessary and should be eliminated, call a method that literally does nothing only consumes gas unnecessarily, if it is a `virtual` method which is expects it to be filled by the class that implements it, it is better to use `abstract`  contracts with just the definition.

**Sample code:**

```javascript
pragma solidity >=0.4.0 <0.7.0;

abstract contract Feline {
    function utterance() public virtual returns (bytes32);
}
```

**Reference:**
- https://docs.soliditylang.org/en/v0.6.0/contracts.html?highlight=abstract#abstract-contracts

**Affected source code:**

- [Treasury.sol:269](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L269)
- [ERC721.sol:54](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L54)
- [ERC721.sol:57](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L57)
- [ERC721.sol:235-239](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L235-L239)
- [ERC721.sol:245-249](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L245-L249)
- [Manager.sol:209](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L209)

## **4. Use `calldata` instead of `memory`**

Some methods are declared as `external` but the arguments are defined as `memory` instead of as `calldata`.

By marking the function as `external` it is possible to use `calldata` in the arguments shown below and save significant gas.

**Recommended change:**

```diff
-   function updateContractImage(string memory _newContractImage) external onlyOwner {
+   function updateContractImage(string calldata _newContractImage) external onlyOwner {
        emit ContractImageUpdated(settings.contractImage, _newContractImage);
        settings.contractImage = _newContractImage;
    }
```

**Affected source code:**

- [MetadataRenderer.sol:347-351](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L347-L351)
- [MetadataRenderer.sol:355-359](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L355-L359)
- [MetadataRenderer.sol:363-367](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L363-L367)

## **5. Change `bool` to `uint256` can save gas**

Because each write operation requires an additional `SLOAD` to read the slot's contents, replace the bits occupied by the boolean, and then write back, `booleans` are more expensive than `uint256` or any other type that uses a complete word. This cannot be turned off because it is the compiler's defense against pointer aliasing and contract upgrades.

Reference:

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27

Also, this is applicable to integer types different from `uint256` or `int256`.

**Affected source code for `booleans`:**

- [ManagerStorageV1.sol:10](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/storage/ManagerStorageV1.sol#L10)
- [MetadataRendererTypesV1.sol:11](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/types/MetadataRendererTypesV1.sol#L11)
- [GovernorStorageV1.sol:19](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/storage/GovernorStorageV1.sol#L19)
- [Pausable.sol:15](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Pausable.sol#L15)
- [Initializable.sol:20](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L20)

## **6. `constants` expressions are expressions, not `constants`**

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

**Reference:**

- https://github.com/ethereum/solidity/issues/9232

> Consequences: each usage of a "constant" costs ~100gas more on each access (it is still a little better than storing the result in storage, but not much..). since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library).

**Affected source code:**

- [EIP712.sol:19](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L19)
- [ERC721Votes.sol:21](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L21)
- [Governor.sol:27](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L27)

## **7. Optimize `ERC721Votes.delegateBySig` logic**

It's possible to optimize the method `ERC721Votes.delegateBySig` as follows:

**Recommended changes:**

```diff
    function delegateBySig(
        address _from,
        address _to,
        uint256 _deadline,
        uint8 _v,
        bytes32 _r,
        bytes32 _s
    ) external {
        // Ensure the signature has not expired
        if (block.timestamp > _deadline) revert EXPIRED_SIGNATURE();
+       if (_from == address(0)) revert INVALID_SIGNATURE();

        // Used to store the digest
        bytes32 digest;

        // Cannot realistically overflow
        unchecked {
            // Compute the hash of the domain seperator with the typed delegation data
            digest = keccak256(
-               abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
+               abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, ++nonces[_from], _deadline)))
            );
        }

        // Recover the message signer
        address recoveredAddress = ecrecover(digest, _v, _r, _s);

        // Ensure the recovered signer is the voter
-       if (recoveredAddress == address(0) || recoveredAddress != _from) revert INVALID_SIGNATURE();
+       if (recoveredAddress != _from) revert INVALID_SIGNATURE();

        // Update the delegate
        _delegate(_from, _to);
    }
```

**Affected source code:**

- [ERC721Votes.sol:170](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L170)

## **8. Optimize `MetadataRenderer.initialize` logic**

It's possible to optimize the method `MetadataRenderer.initialize` as follows:

**Recommended changes:**

```diff
    function initialize(
        bytes calldata _initStrings,
        address _token,
        address _founder,
        address _treasury
    ) external initializer {
        // Ensure the caller is the contract manager
        if (msg.sender != address(manager)) revert ONLY_MANAGER();

        // Decode the token initialization strings
+       (settings.name, , settings.description, settings.contractImage, settings.rendererBase) = abi.decode(
-       (string memory _name, , string memory _description, string memory _contractImage, string memory _rendererBase) = abi.decode(
            _initStrings,
            (string, string, string, string, string)
        );

        // Store the renderer settings
-       settings.name = _name;
-       settings.description = _description;
-       settings.contractImage = _contractImage;
-       settings.rendererBase = _rendererBase;
        settings.token = _token;
        settings.treasury = _treasury;

        // Grant initial ownership to a founder
        __Ownable_init(_founder);
    }
```

**Affected source code:**

- [MetadataRenderer.sol:45-70](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L45-L70)

## **9. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384   
```

**Affected source code:**

- [MetadataRenderer.sol:119](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L119)
- [MetadataRenderer.sol:133](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L133)
- [MetadataRenderer.sol:189](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L189)
- [MetadataRenderer.sol:229](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L229)
- [Treasury.sol:162](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162)

### Total gas saved: **8 * 5 = 40**

## **10. Optimize `MetadataRenderer.onMinted` logic**

It's possible to optimize the method `MetadataRenderer.onMinted` as follows:

**Recommended changes:**

```diff
    function onMinted(uint256 _tokenId) external returns (bool) {
        ...
        unchecked {
            // For each property:
-           for (uint256 i = 0; i < numProperties; ++i) {
+           for (uint256 i = 0; i < numProperties;) {
                // Get the number of items to choose from
                uint256 numItems = properties[i].items.length;

                // Use the token's seed to select an item
+               ++i;
+               tokenAttributes[i] = uint16(seed % numItems);
-               tokenAttributes[i + 1] = uint16(seed % numItems);

                // Adjust the randomness
                seed >>= 16;
            }
        }
        return true;
    }
```

**Affected source code:**

- [MetadataRenderer.sol:189-194](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L189-L194)

## **11. Avoid unused returns**

Having a method that always returns the same value is not correct in terms of consumption, if you want to modify a value, and the method will perform a `revert` in case of failure, it is not necessary to return a `true`, since it will never be `false`. It is less expensive not to return anything, and it also eliminates the need to double-check the returned value by the caller.

**Affected source code:**

- [MetadataRenderer.sol:201](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L201)

## **12. Optimize `MetadataRenderer.getAttributes`**

It's possible to optimize the method `MetadataRenderer.getAttributes` as follows:

**Recommended changes:**

```diff
+   string private immutable _queryStringPath = abi.encodePacked("?contractAddress=",Strings.toHexString(uint256(uint160(address(this))), 20),"&tokenId=");
+
    function getAttributes(uint256 _tokenId) public view returns (bytes memory aryAttributes, bytes memory queryString) {
        // Get the token's query string
        queryString = abi.encodePacked(
-           "?contractAddress=",
-           Strings.toHexString(uint256(uint160(address(this))), 20),
-           "&tokenId=",
+           _queryStringPath,
            Strings.toString(_tokenId)
        );
        ...
``` 

**Affected source code:**

- [MetadataRenderer.sol:206-213](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L206-L213)

## **13. Change arguments type in `MetadataRenderer`**

It's possible to optimize some methods in `MetadataRenderer` contract changing the argument type as follows:

**Recommended changes:**

```diff
-   function initialize(address _governor, uint256 _delay) external initializer {
+   function initialize(address _governor, uint128 _delay) external initializer {
        // Ensure the caller is the contract manager
        if (msg.sender != address(manager)) revert ONLY_MANAGER();

        // Ensure a governor address was provided
        if (_governor == address(0)) revert ADDRESS_ZERO();

        // Grant ownership to the governor
        __Ownable_init(_governor);

        // Store the time delay
-       settings.delay = SafeCast.toUint128(_delay);
+       settings.delay = _delay;

        // Set the default grace period
        settings.gracePeriod = 2 weeks;

        emit DelayUpdated(0, _delay);
    }
    ...
-   function updateDelay(uint256 _newDelay) external {
+   function updateDelay(uint128 _newDelay) external {
        // Ensure the caller is the treasury itself
        if (msg.sender != address(this)) revert ONLY_TREASURY();

        emit DelayUpdated(settings.delay, _newDelay);

        // Update the delay
-       settings.delay = SafeCast.toUint128(_newDelay);
+       settings.delay = _newDelay;
    }
    ...
-   function updateGracePeriod(uint256 _newGracePeriod) external {
+   function updateGracePeriod(uint128 _newGracePeriod) external {
        // Ensure the caller is the treasury itself
        if (msg.sender != address(this)) revert ONLY_TREASURY();

        emit GracePeriodUpdated(settings.gracePeriod, _newGracePeriod);

        // Update the grace period
-       settings.gracePeriod = SafeCast.toUint128(_newGracePeriod);
+       settings.gracePeriod = _newGracePeriod;
    }
```

**Affected source code:**

- [Treasury.sol:43](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L43)
- [Treasury.sol:210](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L210)
- [Treasury.sol:222](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L222)

## **14. Optimize `Token.initialize`**

It is not necessary to send more information than necessary to the `initialize` method. It's possible to optimize the method `Token.initialize` as follows:

**Recommended changes:**

```diff
    function initialize(
        IManager.FounderParams[] calldata _founders,
        bytes calldata _initStrings,
        address _metadataRenderer,
        address _auction
    ) external initializer {
        // Ensure the caller is the contract manager
        if (msg.sender != address(manager)) revert ONLY_MANAGER();

        // Initialize the reentrancy guard
        __ReentrancyGuard_init();

        // Store the founders and compute their allocations
        _addFounders(_founders);

        // Decode the token name and symbol
-       (string memory _name, string memory _symbol, , , ) = abi.decode(_initStrings, (string, string, string, string, string));
+       (string memory _name, string memory _symbol) = abi.decode(_initStrings, (string, string));

        // Initialize the ERC-721 token
        __ERC721_init(_name, _symbol);

        // Store the metadata renderer and auction house
        settings.metadataRenderer = IBaseMetadata(_metadataRenderer);
        settings.auction = _auction;
    }
```

**Affected source code:**

- [Token.sol:59](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L59)

## **15. Remove unused math**

There are mathematical operations that can be eliminated and not affect the logic of the contract.

**Recommended changes:**

```diff
    function _addFounders(IManager.FounderParams[] calldata _founders) internal {
        ...

        unchecked {
            // For each founder:
            for (uint256 i; i < numFounders; ++i) {
                ...

                // For each token to vest:
                for (uint256 j; j < founderPct; ++j) {
                    ...
                    // Update the base token id
-                   (baseTokenId += schedule) % 100;
+                   baseTokenId += schedule;
                }
            }
            ...
        }
    }
```

**Affected source code:**

- [Token.sol:118](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L118)

## **16. Use array instead of mapping**

The `Token` contract uses a map to store the founders, but the key is the index, it is better to use an array instead of a mapping because there is no need to store the length and it will also be cheaper to return the array without iterating through all the entries.

```javascript
    function getFounders() external view returns (Founder[] memory) {
        // Cache the number of founders
        uint256 numFounders = settings.numFounders;

        // Get a temporary array to hold all founders
        Founder[] memory founders = new Founder[](numFounders);

        // Cannot realistically overflow
        unchecked {
            // Add each founder to the array
            for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];
        }

        return founders;
    }
```

**Affected source code:**

- [Token.sol:251-265](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L251-L265)

## **17. Change arguments type in `Auction`**

It's possible to optimize some methods in `Auction` contract changing the argument type as follows:

**Recommended changes:**

```diff
-   function setDuration(uint256 _duration) external onlyOwner {
-       settings.duration = SafeCast.toUint40(_duration);
+   function setDuration(uint40 _duration) external onlyOwner {
+       settings.duration = _duration;

        emit DurationUpdated(_duration);
    }

    /// @notice Updates the time buffer of each auction
    /// @param _timeBuffer The new time buffer
-   function setTimeBuffer(uint256 _timeBuffer) external onlyOwner {
-       settings.timeBuffer = SafeCast.toUint40(_timeBuffer);
+   function setTimeBuffer(uint40 _timeBuffer) external onlyOwner {
+       settings.timeBuffer = _timeBuffer;

        emit TimeBufferUpdated(_timeBuffer);
    }

    /// @notice Updates the minimum bid increment of each subsequent bid
    /// @param _percentage The new percentage
-   function setMinimumBidIncrement(uint256 _percentage) external onlyOwner {
-       settings.minBidIncrement = SafeCast.toUint8(_percentage);
+   function setMinimumBidIncrement(uint40 _percentage) external onlyOwner {
+       settings.minBidIncrement = _percentage;

        emit MinBidIncrementPercentageUpdated(_percentage);
    }
```

**Affected source code:**

- [Auction.sol:77](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L77)
- [Auction.sol:307-335](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L307-L335)

## **18. Change arguments type in `Governor`**

It's possible to optimize some methods in `Governor` contract changing the argument type as follows:

**Recommended changes:**

```diff
    function initialize(
        address _treasury,
        address _token,
        address _vetoer,
-       uint256 _votingDelay,
-       uint256 _votingPeriod,
-       uint256 _proposalThresholdBps,
-       uint256 _quorumThresholdBps
+       uint48 _votingDelay,
+       uint48 _votingPeriod,
+       uint16 _proposalThresholdBps,
+       uint16 _quorumThresholdBps
    ) external initializer {
        // Ensure the caller is the contract manager
        if (msg.sender != address(manager)) revert ONLY_MANAGER();

        // Ensure non-zero addresses are provided
        if (_treasury == address(0)) revert ADDRESS_ZERO();
        if (_token == address(0)) revert ADDRESS_ZERO();

        // Store the governor settings
        settings.treasury = Treasury(payable(_treasury));
        settings.token = Token(_token);
        settings.vetoer = _vetoer;
-       settings.votingDelay = SafeCast.toUint48(_votingDelay);
-       settings.votingPeriod = SafeCast.toUint48(_votingPeriod);
-       settings.proposalThresholdBps = SafeCast.toUint16(_proposalThresholdBps);
-       settings.quorumThresholdBps = SafeCast.toUint16(_quorumThresholdBps);
+       settings.votingDelay = _votingDelay;
+       settings.votingPeriod = _votingPeriod;
+       settings.proposalThresholdBps = _proposalThresholdBps;
+       settings.quorumThresholdBps = _quorumThresholdBps;

        // Initialize EIP-712 support
        __EIP712_init(string.concat(settings.token.symbol(), " GOV"), "1");

        // Grant ownership to the treasury
        __Ownable_init(_treasury);
    }
    ...
-   function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {
+   function updateVotingDelay(uint48 _newVotingDelay) external onlyOwner {
        emit VotingDelayUpdated(settings.votingDelay, _newVotingDelay);

-       settings.votingDelay = SafeCast.toUint48(_newVotingDelay);
+       settings.votingDelay = _newVotingDelay;
    }

    /// @notice Updates the voting period
    /// @param _newVotingPeriod The new voting period
-   function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {
+   function updateVotingPeriod(uint48 _newVotingPeriod) external onlyOwner {
        emit VotingPeriodUpdated(settings.votingPeriod, _newVotingPeriod);

-       settings.votingPeriod = SafeCast.toUint48(_newVotingPeriod);
+       settings.votingPeriod = _newVotingPeriod;
    }

    /// @notice Updates the minimum proposal threshold
    /// @param _newProposalThresholdBps The new proposal threshold basis points
-   function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {
+   function updateProposalThresholdBps(uint16 _newProposalThresholdBps) external onlyOwner {
        emit ProposalThresholdBpsUpdated(settings.proposalThresholdBps, _newProposalThresholdBps);

-       settings.proposalThresholdBps = SafeCast.toUint16(_newProposalThresholdBps);
+       settings.proposalThresholdBps = _newProposalThresholdBps;
    }

    /// @notice Updates the minimum quorum threshold
    /// @param _newQuorumVotesBps The new quorum votes basis points
-   function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {
+   function updateQuorumThresholdBps(uint16 _newQuorumVotesBps) external onlyOwner {
        emit QuorumVotesBpsUpdated(settings.quorumThresholdBps, _newQuorumVotesBps);

-       settings.quorumThresholdBps = SafeCast.toUint16(_newQuorumVotesBps);
+       settings.quorumThresholdBps = _newQuorumVotesBps;
    }
```

**Affected source code:**

- [Governor.sol:61-64](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L61-L64)
- [Governor.sol:564-592](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L564-L592)
