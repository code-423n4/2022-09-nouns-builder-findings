## Proposal vetor can be reset after the vetor is burned

we recommend not to let admin reset the vetor after the vetoer is already burned.

```
    /// @notice Updates the vetoer
    /// @param _newVetoer The new vetoer address
    function updateVetoer(address _newVetoer) external onlyOwner {
        if (_newVetoer == address(0)) revert ADDRESS_ZERO();

        emit VetoerUpdated(settings.vetoer, _newVetoer);

        settings.vetoer = _newVetoer;
    }

    /// @notice Burns the vetoer
    function burnVetoer() external onlyOwner {
        emit VetoerUpdated(settings.vetoer, address(0));

        delete settings.vetoer;
    }

```


## External call return value should be handled

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L192

```
   token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L197

```
   token.burn(_auction.tokenId);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L363

```
     IWETH(WETH).deposit{ value: _amount }();

            // Transfer WETH instead
     IWETH(WETH).transfer(_to, _amount);
```

## Gas should not be hardcoded in low level call.

the gas is hardcoded as 50000 wei.

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L354

```
        assembly {
            // Transfer ETH to the recipient
            // Limit the call to 50,000 gas
            success := call(50000, _to, _amount, 0, 0, 0, 0)
        }
```

we recommand using

```
  (bool sent, bytes memory data) = _to.call{value: msg.value}("");
   require(sent, "Failed to send Ether");
```

## More unit test and integration should be added to increase the test coverage in metadataRenderer.sol

