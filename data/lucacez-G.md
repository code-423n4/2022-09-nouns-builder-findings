### Don't Initialize Variables with Default Value
Uninitialized variables are assigned with the types default value.
Explicitly initializing a variable with it's default value costs unnecesary gas.

There are 5 instances of this issue:

```
File: src/governance/treasury/Treasury.sol

167:            for (uint256 i = 0; i < numTargets; ++i) {
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162

```
File: src/token/metadata/MetadataRenderer.sol

119:            for (uint256 i = 0; i < numNewProperties; ++i) {

133:            for (uint256 i = 0; i < numNewItems; ++i) {

189:            for (uint256 i = 0; i < numProperties; ++i) {

229:            for (uint256 i = 0; i < numProperties; ++i) {
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol

### COMPARISONS: != IS MORE EFFICIENT THAN > IN REQUIRE (6 GAS LESS)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here:

File: ERC1967Upgrade.sol [Line 61](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L61)
```
        if (_data.length > 0 || _forceCall) {
```
File: ERC721Votes.sol [Line 203](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L203)
```
            if (_from != _to && _amount > 0) {
```
### ++X IS MORE EFFICIENT THAN X++(SAVES ~6 GAS)
There are 6 instances of this issue:

File: Governor.sol [Line 230](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L230)
```
          nonces[_voter]++
```
File: ERC721Votes.sol [Line 162](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L162)
```
          nonces[_from]++
```
File: ERC721Votes.sol [Line 207](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L207)
```
          uint256 nCheckpoints = numCheckpoints[_from]++;
```
File: ERC721Votes.sol [Line 222](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L222)
```
          uint256 nCheckpoints = numCheckpoints[_to]++;
```
File: Token.sol [Line 91](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L91)
```
          uint256 founderId = settings.numFounders++;
```
File: Token.sol [Line 154](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L154)
```
          tokenId = settings.totalSupply++;
```
### X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES
File: Governor.sol [Line 280](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L280)
```
                proposal.againstVotes += uint32(weight);
```
File: Governor.sol [Line 285](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L285)
```
                proposal.forVotes += uint32(weight);
```
File: Governor.sol [Line 290](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L290)
```
                proposal.abstainVotes += uint32(weight);
```
File: Token.sol [Line 88](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L88)
```
                if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
```
File: Token.sol [Line 118](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L118)
```
                    (baseTokenId += schedule) % 100;
```
File: MetadataRender.sol [Line 140](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L140)
```
                    _propertyId += numStoredProperties;
```
### EMITTING STORAGE VALUES INSTEAD OF THE MEMORY ONE
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:
File: Auction.sol [Line 172](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L172)
```
        Auction memory _auction = auction;


        // Ensure the auction wasn't already settled
        if (auction.settled) revert AUCTION_SETTLED();
```
It must be read from the variable created in memory and not from storage

### CACHE STORAGE VALUES IN MEMORY TO MINIMIZE SLOADS
The code can be optimized by minimising the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs(3gas)
Storage value should get cached in memory

File: Auction.sol [Lines 244-260](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L244-L260) 

Create a variable in memory to read `auction`, and not read it twice from storage: 
```
    function unpause() external onlyOwner {
        _unpause();

        Auction memory _auction = auction;

        // If this is the first auction:
        if (_auction.tokenId == 0) {
            // Transfer ownership of the contract to the DAO
            transferOwnership(settings.treasury);


            // Start the first auction
            _createAuction();
        }
        // Else if the contract was paused and the previous auction was settled:
        else if (_auction.settled) {
            // Start the next auction
            _createAuction();
        }
    }
```

File: Token.sol [Lines 177-199](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L177-L199)
```
    function _isForFounder(uint256 _tokenId) private returns (bool) {
        // Get the base token id
        uint256 baseTokenId = _tokenId % 100;
        tokenRecipient memory _toketokenRecipient = tokenRecipient[baseTokenId];

        // If there is no scheduled recipient:
        if (_tokenRecipient.wallet == address(0)) {
            return false;

            // Else if the founder is still vesting:
        } else if (block.timestamp < _tokenRecipient.vestExpiry) {
            // Mint the token to the founder
            _mint(_tokenRecipient.wallet, _tokenId);

            return true;

            // Else the founder has finished vesting:
        } else {
            // Remove them from future lookups
            delete tokenRecipient[baseTokenId];

            return false;
        }
    }
```