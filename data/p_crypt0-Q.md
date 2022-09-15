# Comments
#### Governor.sol: propose()
````
The calldata 0of each call
````
To
````
The calldata of each call
````

# [Informational] Missing return statement in Queue(), Governor.sol

The function queue does not return eta explicitly, in contradiction to the styling of other functions in the contract:

````

/// @notice Queues a proposal
    /// @param _proposalId The proposal id
    function queue(bytes32 _proposalId) external returns (uint256 eta) {
        // Ensure the proposal has succeeded
        if (state(_proposalId) != ProposalState.Succeeded) revert PROPOSAL_UNSUCCESSFUL();
        // Schedule the proposal for execution
        eta = settings.treasury.queue(_proposalId);
        emit ProposalQueued(_proposalId, eta);
    }
````

Add 
````
return eta;
````

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L311

# [informational] Missing GracePeriodUpdated event emission

The function initialise is updating the grace period from zero, to 2 weeks, yet no emission event is being recorded in initialize()

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L58

Add:

````
emit GracePeriodUpdated(0, 2 weeks);
````
