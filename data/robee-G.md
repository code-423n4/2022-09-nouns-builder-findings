## Unused state variables

Unused state variables are gas consuming at deployment (since they are located in storage) and are 
a bad code practice. Removing those variables will decrease deployment gas cost and improve code quality. 
This is a full list of all the unused storage variables we found in your code base. 

### Code instances:

        TreasuryStorageV1.sol, timestamps
        TokenStorageV1.sol, tokenRecipient
        GovernorStorageV1.sol, proposals
        GovernorStorageV1.sol, hasVoted
        MetadataRendererStorageV1.sol, ipfsData
        ManagerStorageV1.sol, isUpgrade



## Unused declared local variables

Unused local variables are gas consuming, since the initial value assignment costs gas. And are 
a bad code practice. Removing those variables will decrease the gas cost and improve code quality. 
This is a full list of all the unused storage variables we found in your code base. 

### Code instance:

        Manager.sol, deploy, salt



## Change transferFrom to transfer

'transferFrom(address(this), *, **)' could be replaced by the following more gas efficient 'transfer(*, **)'
               This replacement is more gas efficient and improves the code quality.

### Code instance:

        Auction.sol, 192 : token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);



## Use != 0 instead of > 0


Using != 0 is slightly cheaper than > 0. (see https://github.com/code-423n4/2021-12-maple-findings/issues/75 for similar issue)


### Code instance:

        ERC721Votes.sol, 203: change '_amount > 0' to '_amount != 0'



## Use unchecked to save gas for certain additive calculations that cannot overflow


You can use unchecked in the following calculations since there is no risk to overflow:

### Code instances:

        Auction.sol (L#149) - auction.endTime = uint40(block.timestamp + settings.timeBuffer);
        Treasury.sol (L#76) - return block.timestamp > (timestamps[_proposalId] + settings.gracePeriod);
        Treasury.sol (L#123) - eta = block.timestamp + settings.delay;
        Governor.sol (L#160) - snapshot = block.timestamp + settings.votingDelay;



## Consider inline the following functions to save gas


    You can inline the following functions instead of writing a specific function to save gas.
    (see https://github.com/code-423n4/2021-11-nested-findings/issues/167 for a similar issue.)

    
### Code instances

        ERC1967Proxy.sol, _implementation, { return ERC1967Upgrade._getImplementation(); }
        Address.sol, toBytes32, { return bytes32(uint256(uint160(_account)) << 96); }
        ERC1967Upgrade.sol, _getImplementation, { return StorageSlot.getAddressSlot(_IMPLEMENTATION_SLOT).value; }
        MetadataRenderer.sol, _encodeAsJson, { return string(abi.encodePacked("data:application/json;base64,", Base64.encode(_jsonBlob))); }



## Inline one time use functions


The following functions are used exactly once. Therefore you can inline them and save gas and improve code clearness.
    

### Code instances:

        Address.sol, isContract
        Token.sol, _addFounders
        Address.sol, verifyCallResult
        ERC1967Upgrade.sol, _upgradeTo
        ERC1967Upgrade.sol, _upgradeToAndCall
        MetadataRenderer.sol, _generateSeed
        Token.sol, _isForFounder
        ERC721Votes.sol, _afterTokenTransfer
        Token.sol, _getNextTokenId
        MetadataRenderer.sol, _getItemImage



## Do not cache msg.sender


We recommend not to cache msg.sender since calling it is 2 gas while reading a variable is more.


### Code instances:

        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L120
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L159
