# 1. [G-1] Token._addFounders(): Update memory instead of storage variable

From:

    for (uint256 i; i < numFounders; ++i) {
       ...
       // Compute the founder's id
      uint256 founderId = settings.numFounders++;
       ...
    }

Becomes:

    uint256 settingNumFounders = settings.numFounders;
    for (uint256 i; i < numFounders; ++i) {
        ...
         // Compute the founder's id
        uint256 founderId = settingNumFounders++;
        ...
    }
    settings.numFounders = settingNumFounders;


# 2. [G-2] Token._addFounders(): No sign effect in % 100

    (baseTokenId += schedule) % 100;

# 3. [G-3] Token.mint(): Update memory instead of storage variable
From:

    unchecked {
       do {
         // Get the next token to mint
         tokenId = settings.totalSupply++;
         // Lookup whether the token is for a founder, and mint accordingly if so
	} while (_isForFounder(tokenId));
   }

Becomes:

    unchecked {
      uint256 totalSupply = setting.totalSupply;
      do {
        // Get the next token to mint
        tokenId = totalSupply++;
        // Lookup whether the token is for a founder, and mint accordingly if so
      } while (_isForFounder(tokenId));
      setting.totalSupply = totalSupply;
    }

# 4. [G-4] Auction.createBid(): Using memory of 'settings' instead of 'settings' storage variable
Add line:

    Settings memory _settings = settings;
Then changes:

    settings.reservePrice => _settings.reservePrice;
    settings.minBidIncrement => _settings.minBidIncrement;
    settings.timeBuffer => _settings.timeBuffer;

# 5. [G-5] Auction._settleAuction(): Using memory instead of storage variable
From line:

    if (auction.settled) revert AUCTION_SETTLED();

Becomes:

    if (_auction.settled) revert AUCTION_SETTLED();

# 6. [G-6] Auction.unpause(): Using memory instead of storage variable
From:

    if (auction.tokenId == 0) {
       transferOwnership(settings.treasury);
        ...
    }
        // Else if the contract was paused and the previous auction was settled:
        else if (auction.settled) {
        ...
     }
Becomes:

    Auction  memory _auction = auction;
    Settings memory _settings= settings;
    if (_auction.tokenId == 0) {
       transferOwnership(_settings.treasury);
        ...
    }
    // Else if the contract was paused and the previous auction was settled:
    else if (_auction.settled) {
        ...
    }

# 7. [G-7] Treasury.isReady(): Using memory instead of storage variable
From:

    return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];

Becomes:

    uint256 timeStamp = timestamps[_proposalId];
    return timeStamp != 0 && block.timestamp >= timeStamp;

# 8. [G-8] Token._getNextTokenId(): Using memory instead of storage variable

From:

    while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;

Becomes:

    Founder recipient = tokenRecipient[_tokenId];
    while (recipient.wallet != address(0)) ++_tokenId;

# 9. [G-9] Token._isForFounder(): Using memory instead of storage variable

Add line:

    Founder memory recipient = tokenRecipient[baseTokenId];

Update:

    tokenRecipient[baseTokenId].wallet => recipient.wallet;
    tokenRecipient[baseTokenId].vestExpiry => recipient.vestExpiry;

# 10. [G-10] Token.getFounder(): Return memory instead of storage variable

From:

    return founder[_founderId];

Becomes:

    Founder  memory _founder = founder[_founderId];
    return _founder;

# 11. [G-11] Auction.createBid(): Using memory instead of storage variable

Add line:

    Settings memory _settings = settings;

Update:

    settings.reservePrice => _settings.reservePrice;
    settings.minBidIncrement=> _settings.minBidIncrement;
    settings.timeBuffer=> _settings.timeBuffer;

# 12. [G-12] Auction.createBid(): Using local variable for computing value of auction.endTime then using it again

From:

    if (extend) {
       unchecked {
         auction.endTime = uint40(block.timestamp + settings.timeBuffer);
       }
    }
    emit AuctionBid(_tokenId, msg.sender, msg.value, extend, auction.endTime);

Becomes:

    uint40 endTime = auction.endTime;
    if (extend) {
       unchecked {
         endTime = uint40(block.timestamp + settings.timeBuffer);
         auction.endTime = endTime;
       }
    }
    emit AuctionBid(_tokenId, msg.sender, msg.value, extend, endTime);

# 13. [G-13] Treasury.execute(), line 162: Initialize uint256 i; is cheaper than uint256 i= 0;

# 14. [G-14]  <x> += y cost more gas than <x> = <x> + <y>

##### Instances include:

File Governor.sol, line 280, 285, 290
File Token.sol, line 88, 118
File MetadataRenderer.sol, line 140
