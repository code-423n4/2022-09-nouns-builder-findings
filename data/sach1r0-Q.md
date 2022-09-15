# Lack of zero-address check in the constructor

## Details
 Lack of zero-address checks may lead to infunctional protocol especially in the case wherein variable is immutable like the  `tokenImpl` .
 
## Mitigation
Consider adding zero-address checks such as: `require(_tokenImpl != address(0));`

## Line of code
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L55-L72
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L41-L43
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L32-L34
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L39-L42
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L30

___
# Lack of indexed parameters in events

## Details
Some of the events in the codebase are not indexed. Indexing event parameters enable off-chain services to search and filter for specific events.
see reference: Low severity finding from OpenZeppelin Audit of HoldeFi
[L09] Lack of indexed parameters in events
https://blog.openzeppelin.com/holdefi-audit/#low

## Mitigation
Add the `indexed` keyword to the events.

## Line of code
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/IManager.sol#L21

___
# Some functions in the entire codebase does not check for zero-address in the input parameter  

## Details
Accidentaly setting parameters to zero-address to most variables will lose its major functionalities, one example is the `vetoer` role.

## Mitigation
I suggest adding a zero-address input check, the same as the checks done for the other parameters. Ex:
`if (_vetoer == address(0)) revert ADDRESS_ZERO();`

## Line of code
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L57-L87
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L54-L82
___
