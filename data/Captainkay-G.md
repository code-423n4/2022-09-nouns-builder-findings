Under src/token/metadata/MetadataRenderer.sol

```
            for (uint256 i = 0; i < numProperties; ++i) {
                // Get the number of items to choose from
                uint256 numItems = properties[i].items.length;

                // Use the token's seed to select an item
                tokenAttributes[i + 1] = uint16(seed % numItems);

                // Adjust the randomness
                seed >>= 16;
            }
```
The properties datatype can be cached so that while doing the operation uint256 numItems = properties[i].items.length; we doing need to read from the chain