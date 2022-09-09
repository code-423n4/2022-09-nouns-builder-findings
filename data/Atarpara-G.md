Execute function In Governace contract we convert hashProposal to directly compute keccak256 hash.

## Current Implementation 

```
bytes32 proposalId = hashProposal(_targets, _values, _calldatas, _descriptionHash);
```

here hashProposal convert calldata to memory and charge cost of expansion cost.

## Gas Optimize

here we can avoid the memory expansion gas cost.  

```
bytes32 proposalId = keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
```




Links: 
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L331
