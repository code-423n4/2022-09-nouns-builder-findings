# Token._getNextTokenId is ineffficient

In file Token.sol the function `_getNextTokenId(_tokenId)` is very inefficient.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L130

The function is called an already nested loop so this function makes the loop a 3 level nested loop.

The function call can be seen at line 110:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L110

## Recommendation

Since he tokenIds seem incremental the contract should save the next available tokenId in a storage variable. If any of the Ids realease under the "next available token Id" it should be stored in a storage array. The contract can later choose to fill those gaps up first or to just choose the "next available tokenId"

## Pseudo code for recommendation

```solidity
    function _getNextTokenId() internal view returns (uint256) {
            return nextAvailableTokenId++;
    }
```

With storing the gaps for lower, released Ids:

```solidity
mapping(uint256 => uint256) private availableIds; //
uint256 private  nextUnusedAvailableIndex; // contains the next Id that has useful data
uint256 private  nextAvailableIndex; // contains the size of _availableIds
uint256 private nextAvailableTokenId; // contains the new next available token

function _getNextTokenId() internal view returns (uint256) {
            if (nextAvailableIndex > nextUnusedAvailableIndex) {
                        return availableIds[nextUnusedAvailableIndex];
            }
            return nextAvailableTokenId++;
}

// deletes a tokenId that is below the nextAvailableTokenId
function _deleteTokenId(uint256 _tokenId) internal {
            require(_tokenId < nextAvailableTokenId);
            availableIds[nextAvailableIndex++] = _tokenId;
}
```

Please note that `nextUnusedAvailableIndex` should be incremented after reusing the index.

For easier readibility I did not cache storage variables in the example implementation of these functions but in the production ready implementation they should be cached.

# Non cached variable was used in Auction.

`auction` was cached to local memory variable `_auction` but in line 172 the global, storage variable was used using unnecessary gas.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L172