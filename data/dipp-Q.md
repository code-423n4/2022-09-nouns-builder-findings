# [L-01] No limits for auction ```duration```, ```timeBuffer```, ```minBidIncrement``` and ```reservePrice``` in ```Auction.sol```

## Line References

[Auction.sol#L77-L78](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L77-L78)

[Auction.sol#L282](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L282)

[Auction.sol#L287](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L287)

[Auction.sol#L292](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L292)

[Auction.sol#L297](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L297)

## Description

The ```duration``` variable in ```Auction.sol``` is used to set the auction endTime and determines how long an auction is active (unless extended). The duration could mistakenly be set for much longer than intended (100 days instead of 10 days, for example) which, if an auction starts with this duration, could lead massive delays for the DAO.

The ```timeBuffer``` is used to determine whether an auction should be extended or not. If the remaining time in an auction is less than the timeBuffer but more than 0 then the auction is extended by the amount of the timeBuffer. A very long timeBuffer could extend the auction for a very long time as it would be very likely to be extended everytime a bid is made.

The ```minBidIncrement``` should never be 0. If 0 then ```createBid``` could be called using the same bid amount as the current highest bid, so users would be able to bid in an auction without increasing the bid for the token.

Similarly, no limits on the ```reservePrice``` could lead to the same scenario where bids for a token do not increase or it could lead to tokens having bids for 0 ETH if ```reservePrice``` is set to 0.

## Recommended Mitigation Steps

Consider adding upper and lower limits for the ```duration``` in the ```initialize``` and ```setDuration``` functions of ```Auction.sol```.

The ```timeBuffer``` should at least be checked to be less than the ```duration``` in ```initialize``` and ```setTimeBuffer```.

Check that the ```minBidIncrement``` and ```reservePrice``` are more than 0 in the ```initialize```, ```setMinimumBidIncrement``` and ```setReservePrice``` functions.












# [L-02] No limit for delay in ```Treasury.sol```

## Line References

[Treasury.sol#L54](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L54)

[Treasury.sol#L210-L218](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L210-L218)

## Description

The ```delay``` is used to allow users of the DAO to react to changes before they are executed. Since there is no minimum limit to which the delay can be set, changes can be made instantly when queued. If an attacker is able to queue arbitrary proposals and the delay is very short, users interacting with the DAO will have very little time to react to the changes made by the attacker.

## Recommended Mitigation Steps

Consider adding a lower bound for the ```delay``` in the ```initialize``` and ```updateDelay``` functions of ```Treasury.sol```.





# [L-03] If a property has 0 items then tokens cannot be minted temporarily

## Line References

[MetadataRenderer.sol#L188-L198](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L188-L198)

## Description

When a token is minted the ```onMinted``` function in ```MetadataRenderer.sol``` is called. In this function the token's attributes are set to a random item in a property. Due to the way an item is assigned to a token's attribute, a divide by zero error occurs if the number of items for a property is 0.


## Impact

This could prevent new tokens from being minted which could lead to auctions not able to be settled and could cause unnecessary delays for creating new auctions as items would need to be added to the properties with no items. Adding properties would take some time since the treasury is the owner of the ```MetadataRenderer.sol``` contract at this point and a proposal will have to be made and executed to call ```addProperties```.

## Proof of Concept

From the ```onMinted``` function in ```MetadataRenderer.sol```:
```solidity
            // For each property:
            for (uint256 i = 0; i < numProperties; ++i) {
                // Get the number of items to choose from
                uint256 numItems = properties[i].items.length;

                // Use the token's seed to select an item
                tokenAttributes[i + 1] = uint16(seed % numItems);

                // Adjust the randomness
                seed >>= 16;
            }
```

The above code shows how the token's attributes are set. If a property has no items (numItems == 0) then a "divide by zero" error will occur due to ```seed % numItems```.

## Recommended Mitigation Steps

When a new property is added, check that it has at least 1 item at the end of the ```addProperties``` function in ```MetadataRenderer.sol```