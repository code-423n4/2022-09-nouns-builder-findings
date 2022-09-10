## 1. No check in ``Auction.constructor()`` and ``Auction.initialize()``
### Details
``manager``, ``_token``, ``_founder``, ``_duration``,  ``_reservePrice``,``treasury`` may be 0
### Recomendations
Add required checks

## 2. Auction may be prolonged forever if your buyers have enough money
### Details
Prolongation logic: ``auction.endTime = uint40(block.timestamp + settings.timeBuffer)``
And may be prolonging forever
### Recomendations
Add hard limit

## 3. No limit for proposal operations
### Details
No checks for ``_targets.length``. When executing this proposal, OutOfGas may happen and it proposal will not be executed ever
### Recomendations
Add limit for ``_targets.length``

## 4. No balance checks in ``Governor.propose()``
### Details
No checks that sum of ``_values`` exists on balance
It will fail on executing
### Recomendations
Add balance checks and do not accept proposal if no balance for it's execution

## 5. Saving votes in ``uint32`` may become a problem in future
### Details
Balance of token is ``uint256``. But you stores votes in ``uint32``

## 6. Why ``Governor.execute()`` is payable?
### Details
The function should not be payable. All funds should be stored in treasury

## 7. There can be only 1 vetoer
### Details
It would be better to make at as role, not hardcoded variable
And to be able to give that role to more than 1 address

## 8. You have copy-pasted standard contract (openzeppelin) instead of just importing them
### Details
There may be mistakes and typos on copying

## 9. No check in ``Manager.constructor()``
### Details
``_tokenImpl``, ``_metadataImpl``, ``_auctionImpl``, ``_treasuryImpl``,  ``_governorImpl`` may be 0
### Recomendations
Add required checks


## 10. No check in ``Token.constructor()`` and ``Token.initialize()``
### Details
``manager``, ``metadataRenderer``, ``auction`` may be 0
### Recomendations
Add required checks


## 11. No check if ``_founders[i].wallet == 0`` (i > 0)
### Details
There is a check only for first founder, not for other
### Recomendations
Add required checks


## 12. No check in ``MetadataRenderer.constructor()`` and ``MetadataRenderer.initialize()``
### Details
``_manager``, ``_token``, ``_founder``, ``treasury`` may be 0
### Recomendations
Add required checks


## 13. ``MetadataRenderer.numProperties`` is not limited
### Details
It may lead to OutOfGas in ``MetadataRenderer.onMinted()``
### Recomendations
Add required checks


## 14. ``blockhash(block.number)`` is useless
### Details
blockhash(block.number) is always 0


