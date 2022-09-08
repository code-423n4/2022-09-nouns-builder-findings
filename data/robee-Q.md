## Usage of an incorrect version of Ownbale library can potentially malfunction all onlyOwner functions


    The current implementaion is using an non-upgradeable version of the Ownbale library.  instead of the upgradeable version: @openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol.
    A regular, non-upgradeable Ownbale library will make the deployer the default owner in the constructor. Due to a requirement of the proxy-based upgradeability system, no constructors can be used in upgradeable contracts. Therefore, there will be no owner when the contract is deployed as a proxy contract
    Use @openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol and @openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol instead.
    And add     __Ownable_init(); at the beginning of the initializer.
    
    
### Code instances:

        Auction.sol
        Treasury.sol
        Manager.sol
        Governor.sol
        MetadataRenderer.sol





## Missing non reentrancy modifier

The following functions are missing reentrancy modifier although some other pulbic/external functions does use reentrancy modifer.
Even though I did not find a way to exploit it, it seems like those functions should have the nonReentrant modifier as the other functions have it as well..

### Code instance:

        Auction.sol, initialize is missing a reentrancy modifier




## Init frontrun

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: https://github.com/code-423n4/2021-09-sushimiso-findings/issues/64)
The vulnerable initialization functions in the codebase are: 

### Code instances:

        Token.sol, constructor, 30
        Governor.sol, constructor, 41
        MetadataRenderer.sol, initialize, 45
        Manager.sol, initialize, 80
        Governor.sol, initialize, 57
        Treasury.sol, initialize, 43
        Manager.sol, constructor, 55


## Missing commenting


        The following functions are missing commenting as describe below:
        
### Code instances:

        Auction.sol, duration (external), @return is missing
        Auction.sol, minBidIncrement (external), @return is missing
        Auction.sol, treasury (external), @return is missing
        Auction.sol, reservePrice (external), @return is missing
        Ownable.sol, pendingOwner (public), @return is missing
        Manager.sol, isRegisteredUpgrade (external), @return is missing
        Pausable.sol, paused (external), @return is missing
        Auction.sol, timeBuffer (external), @return is missing
        Ownable.sol, owner (public), @return is missing



## Not verified owner


        owner param should be validated to make sure the owner address is not address(0).
        Otherwise if not given the right input all only owner accessible functions will be unaccessible.
        
        
### Code instances:

        Ownable.sol.safeTransferOwnership _newOwner
        Ownable.sol.transferOwnership _newOwner



## Solidity compiler versions mismatch


The project is compiled with different versions of solidity, which is not recommended because it can lead to undefined behaviors.
        


## Treasury may be address(0)


    Make sure the treasury is not address(0).
        
        
### Code instances:

        Auction.sol.initialize _treasury
        MetadataRenderer.sol.initialize _treasury


## Duplicates in array


        You allow in some arrays to have duplicates. Sometimes you assumes there are no duplicates in the array.
        
### Code instance:

MetadataRenderer.addProperties pushed (_ipfsGroup) 


## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instances:

        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L323
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L161
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L315
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L307
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L331
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L268
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L115




## Check transfer receiver is not 0 to avoid burned money


Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.


### Code instances:

        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L136
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L345
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L180
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L216
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L126
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721.sol#L196
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L188
        https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L267



## In the following public update functions no value is returned

In the following functions no value is returned, due to which by default value of return will be 0. 
We assumed that after the update you return the latest new value. 
(similar issue here: https://github.com/code-423n4/2021-10-badgerdao-findings/issues/85). 

### Code instances:

        Governor.sol, updateProposalThresholdBps
        MetadataRenderer.sol, updateDescription
        Governor.sol, updateVotingPeriod
        Treasury.sol, updateGracePeriod
        Governor.sol, updateQuorumThresholdBps


## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.    

### Code instances:

        Manager.sol.registerUpgrade _upgradeImpl
        MetadataRenderer.sol.initialize _founder
        Auction.sol.initialize _token
        Auction.sol.initialize _founder