# Low

## DAO can be deployed with 100% allocation to founders

It's possible to deploy a DAO with an exact 100% allocation to the founders. If a DAO is deployed with these parameters, all auction bids will revert with out of gas errors, when they search for the next available unallocated token.

See the following test case:

```solidity
// in Token.t.sol
function testHundredPercentFunderOwnership() public {
    // setup
    address[] memory wallets = new address[](1);
    uint256[] memory percents = new uint256[](1);
    uint256[] memory vestingEnds = new uint256[](1);

    wallets[0] = founder;
    percents[0] = 100;
    vestingEnds[0] = 4 weeks;

    setFounderParams(wallets, percents, vestingEnds);
    setMockTokenParams();
    setMockAuctionParams();
    setMockGovParams();
    deploy(foundersArr, tokenParams, auctionParams, govParams);

    vm.prank(founder);
    auction.unpause();

    address bidder = makeAddr("bidder");
    vm.prank(bidder);
    auction.createBid{ value: 1 ether }(2);

    // causes an infinite loop, hence eventually an out of gas error
    // [FAIL. Reason: EvmError: Revert] testHundredPercentFunderOwnership() (gas: 8660281895701201590)
}
```

Recommendation:

Change the following condition in [`Token#addFounders`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L87-L88) from:

```solidity
                // Update the total ownership and ensure it's valid
                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
```

to:

```solidity
                // Update the total ownership and ensure it's valid
                if ((totalOwnership += uint8(founderPct)) >= 100) revert INVALID_FOUNDER_OWNERSHIP();
```

# QA

## Use `string.concat`

Solidity v0.8.12 introduced a `string.concat` function that is a little nicer than using `string(abi.encodePacked(data))`.

For example, this function in `MetadataRenderer`:

[`MetadataRenderer#_getItemImage`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L254-L262)

```solidity
    /// @dev Encodes the reference URI of an item
    function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {
        return
            UriEncode.uriEncode(
                string(
                    abi.encodePacked(ipfsData[_item.referenceSlot].baseUri, _propertyName, "/", _item.name, ipfsData[_item.referenceSlot].extension)
                )
            );
    }
```

May be rewritten using `string.concat` as:

```solidity
    /// @dev Encodes the reference URI of an item
    function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {
        return
            UriEncode.uriEncode(
                string.concat(
                    ipfsData[_item.referenceSlot].baseUri, _propertyName, "/", _item.name, ipfsData[_item.referenceSlot].extension
                )
            );
    }
```

There are a number of locations in `MetadataRenderer` where you may wish to replace `string(abi.encodePacked())` with `string.concat`.

## Shadowed variable

The `_owner` state variable inherited from `Ownable` is shadowed inside `Manager#initialize`:

[`Manager#initialize`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L78-L86)

```solidity
    /// @notice Initializes ownership of the manager contract
    /// @param _owner The owner address to set (will be transferred to the Builder DAO once its deployed)
    function initialize(address _owner) external initializer {
        // Ensure an owner is specified
        if (_owner == address(0)) revert ADDRESS_ZERO();

        // Set the contract owner
        __Ownable_init(_owner);
    }
```

Consider renaming this parameter `owner_` to avoid shadowing the inherited variable.


## Omit unused virtual functions

Since the Nouns Builder contracts are meant to be deployed via factory contracts rather than extended as a library, you may wish to omit the unused virtual transfer hook functions in `ERC721`.

[`ERC721.sol`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L231-L251)

```solidity
   /// @dev Hook called before a token transfer
    /// @param _from The sender address
    /// @param _to The recipient address
    /// @param _tokenId The ERC-721 token id
    function _beforeTokenTransfer(
        address _from,
        address _to,
        uint256 _tokenId
    ) internal virtual {}

    /// @dev Hook called after a token transfer
    /// @param _from The sender address
    /// @param _to The recipient address
    /// @param _tokenId The ERC-721 token id
    function _afterTokenTransfer(
        address _from,
        address _to,
        uint256 _tokenId
    ) internal virtual {}
}

```

## Unnecessary low-level assembly

There are two usages of low-level assembly that may be replaced with Solidity equivalents.

[`Auction#_handleOutgoingTransfer`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L347-L355)

```solidity

        // Used to store if the transfer succeeded
        bool success;

        assembly {
            // Transfer ETH to the recipient
            // Limit the call to 50,000 gas
            success := call(50000, _to, _amount, 0, 0, 0, 0)
        }
```

Recommended:

```solidity
        (bool succes,) = payable(_to).call{ value: _amount, gas: 50000 }("");
```

[`Address#isContract`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L29-L34)

```solidity
    /// @dev If an address is a contract
    function isContract(address _account) internal view returns (bool rv) {
        assembly {
            rv := gt(extcodesize(_account), 0)
        }
    }
```

Recommended:

```solidity
    /// @dev If an address is a contract
    function isContract(address _account) internal view returns (bool rv) {
        return _account.code.length > 0;
    }
```

## Omit unused modifier

The `reinitializer` modifier and internal `_disableInitializers` functions in `Initializable` appear to be unused. Consider removing these functions if they are not used in the codebase.

[`Initializable.sol`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L53-L82)

```solidity
   /// @dev Enables initializer versioning
    /// @param _version The version to set
    modifier reinitializer(uint8 _version) {
        if (_initializing || _initialized >= _version) revert ALREADY_INITIALIZED();

        _initialized = _version;

        _initializing = true;

        _;

        _initializing = false;

        emit Initialized(_version);
    }

    ///                                                          ///
    ///                          FUNCTIONS                       ///
    ///                                                          ///

    /// @dev Prevents future initialization
    function _disableInitializers() internal virtual {
        if (_initializing) revert INITIALIZING();

        if (_initialized < type(uint8).max) {
            _initialized = type(uint8).max;

            emit Initialized(type(uint8).max);
        }
    }
```