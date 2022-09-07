# Check for vetoer address to be 0

In the function `initialize` in file **src/governance/Governor.sol**, the `_vetoer` value is not checked for 0 address. If the manager initialises the contract with 0 address vetoer, this could render the "vetoing" functionality useless, until the owner updates a new vetoer. Though this is not a big issue, it could cause some hiccups.

File: src/governance/Governor.sol
LoC: https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L72

Mitigation:
Check for 0 address for vetoer ( Ex: `if (_vetoer == address(0)) revert ADDRESS_ZERO();` )