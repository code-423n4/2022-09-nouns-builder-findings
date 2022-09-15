## Issue 1 Lack of zero checks on founder address
When looping through each founder parameters in `Token._addFounders()`, there is no check that a founder address is `address(0)`, this can cause inconsistencies between `OwnershipPct` and the actual percentage of NFTs which have been minted

Consider adding a check to make sure that a founder address is not 0

## Issue 2 `founderPct` may be larger than 100
Due to inconsistently casting `founderPct` to uint(8), `founderPct` may be truncated and pass all verification checks however when the `baseTokenID`s is assigned, the function through loops through the full value which can cause the founder to receive full ownership of all NFTs even if `OwnershipPct` is set to 0.

Consider adding a check to make sure that `founderPct < type(uint8).max`
