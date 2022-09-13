## Variables don't need to be declared with default value

### Target codebase

[Treasury.sol#L162](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162)

[MetadataRenderer.sol#L119](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119)

[MetadataRenderer.sol#L133](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133)

[MetadataRenderer.sol#L189](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189)

[MetadataRenderer.sol#L229](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229)

## **Vulnerability details**

Variables declared with default value aren't gas optimized, they shouldn't be declared with default value

## **Recommended Mitigation Steps**

Follow the changes to optimize the code:

```diff
- uint256 i = 0;
+ uint256 i;
```

---

## Delete `auction` before assign

### Target codebase

[Auction.sol#L222-L229](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L222-L229)

## **Vulnerability details**

Deleting the `auction` variable before assign `startTime` and `endTime` save gas.

## **Recommended Mitigation Steps**

Follow the changes to optimize the code:

```diff
+ delete auction;

// Store the auction start and end time
auction.startTime = uint40(startTime);
auction.endTime = uint40(endTime);

- // Reset data from the previous auction
- auction.highestBid = 0;
- auction.highestBidder = address(0);
- auction.settled = false;
```