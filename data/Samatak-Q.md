## `_SAFEMINT()` SHOULD BE USED RATHER THAN `_MINT()` WHEREVER POSSIBLE

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function :

*There is 1 instance of this issue:*

```solidity
File: token/Token.sol
169:           super._mint(_to, _tokenId);
```

### Mitigation

Use _safeMint() instead of _mint().

---

## IMMUTABLE ADDRESSES LACK ZERO-ADDRESS CHECK

Constructors should check the address written in an immutable address variable is not the zero address

*There is 6 instances of this issue:*

```solidity
File: auction/Auction.sol
41:           WETH = _weth;
```

```solidity
File: manager/Manager.sol
62:        tokenImpl = _tokenImpl;
63:        metadataImpl = _metadataImpl;
64:        auctionImpl = _auctionImpl;
65:        treasuryImpl = _treasuryImpl;
66:        governorImpl = _governorImpl;
        
```

### Mitigation

Add a zero address check for each variables.

---

## Missing msg.value validation

It is a good idea to add a validation for msg.value to avoid any overpay or null value.

*There is 1 instance of this issue:*

```solidity
File: governor/Governor.sol
340:           settings.treasury.execute{ value: msg.value }(_targets, _values, _calldatas, _descriptionHash);
```

### Mitigation

Add a msg.value validation :

```solidity
if(msg.value != expectedAmount) revert NOT_EXPECTED_AMOUNT(msg.value);
```

---

## Emit an event before doing the change

The risk is that the change could be revert and may not happen. The event will still have been emitted, which could lead to a mishandling of the information.

This could especially happen when the change is using `SafeCast` library as functions `toUint128` `toUint64` `toUint48` `toUint40` `toUint32` `toUint16` `toUint8` may revert.

*There is 6 instances of this issue:*

```solidity
File: treasury/Treasury.sol
208:           /// @notice Updates the transaction delay
						   /// @param _newDelay The new time delay
					     function updateDelay(uint256 _newDelay) external {
					        // Ensure the caller is the treasury itself
					        if (msg.sender != address(this)) revert ONLY_TREASURY();
					
					        emit DelayUpdated(settings.delay, _newDelay);
					
					        // Update the delay
					        settings.delay = SafeCast.toUint128(_newDelay);
					     }
```

```solidity
File: treasury/Treasury.sol
220:          /// @notice Updates the execution grace period
					    /// @param _newGracePeriod The new grace period
					    function updateGracePeriod(uint256 _newGracePeriod) external {
					        // Ensure the caller is the treasury itself
					        if (msg.sender != address(this)) revert ONLY_TREASURY();
					
					        emit GracePeriodUpdated(settings.gracePeriod, _newGracePeriod);
					
					        // Update the grace period
					        settings.gracePeriod = SafeCast.toUint128(_newGracePeriod);
					    }
```

```solidity
File: governor/Governor.sol
220:        /// @notice Updates the voting delay
				    /// @param _newVotingDelay The new voting delay
				    function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner {
				        emit VotingDelayUpdated(settings.votingDelay, _newVotingDelay);
				
				        settings.votingDelay = SafeCast.toUint48(_newVotingDelay);
				    }
```

```solidity
File: governor/Governor.sol
220:        /// @notice Updates the voting period
				    /// @param _newVotingPeriod The new voting period
				    function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner {
				        emit VotingPeriodUpdated(settings.votingPeriod, _newVotingPeriod);
				
				        settings.votingPeriod = SafeCast.toUint48(_newVotingPeriod);
				    }
```

```solidity
File: governor/Governor.sol
220:        /// @notice Updates the minimum proposal threshold
				    /// @param _newProposalThresholdBps The new proposal threshold basis points
				    function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner {
				        emit ProposalThresholdBpsUpdated(settings.proposalThresholdBps, _newProposalThresholdBps);
				
				        settings.proposalThresholdBps = SafeCast.toUint16(_newProposalThresholdBps);
				    }
```

```solidity
File: governor/Governor.sol
220:        /// @notice Updates the minimum quorum threshold
				    /// @param _newQuorumVotesBps The new quorum votes basis points
				    function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner {
				        emit QuorumVotesBpsUpdated(settings.quorumThresholdBps, _newQuorumVotesBps);
				
				        settings.quorumThresholdBps = SafeCast.toUint16(_newQuorumVotesBps);
				    }
```

---

## **PRAGMA VERSION**

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

```solidity
src/lib/token/ERC721.sol
src/lib/token/ERC721Votes.sol
src/lib/proxy/ERC1967Proxy.sol
src/lib/proxy/ERC1967Upgrade.sol
src/lib/proxy/UUPS.sol
```

### Recommended Mitigation Steps

Lock the pragma version and use version 0.8.15 (as it is the version that is used for every other contracts)