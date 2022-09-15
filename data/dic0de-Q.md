### Overview
`registerUpgrade ()` and `removeUpgrade ()` functions does not check if the implementation is registered before registering or removing upgrade. This is visible here https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L187-L200.

### Impact
1. It allows for re-registration of upgrades where already registered upgrades are again registered.
2. It also allows for removal of upgrades which are not even registered. 

### Recommendation
1. Consider checking if the upgrades are registered before registering or removing upgrades. 