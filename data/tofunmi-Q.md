### The return data from execution should be used in the revert data, instead of using a custom error
  - [src/Treasury.sol#L164](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L164)
```
       │ File: src/governance/treasury/Treasury.sol#L164
───────┼───────────────────────────────────────------------------------------─
 162   │             for (uint256 i = 0; i < numTargets; ++i) {
 163   │                 // Execute the transaction
 164 + │                 (bool success, bytes _returnData) = _targets[i].call{ value: _values[i] }(_calldatas[i]);
 165   │ 
 166   │                 // Ensure the transaction succeeded
 167  +│                 if (!success) {
      +|                       assembly {
      +|                         revert(add(_returnData, 32), _returnData)
      +|                       }
      +|                   }
 168  +|
 169   │         }
───────┴──────────────
```
### Delete proposal mapping only after all the execution has finished not after to protect the control flow
  - [src/Treasury.sol#L154](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L154)

### ETH can be locked in the treasury in an empty receive block, there should be a function that handles and uses the incoming funds to the treasury
```
receive() external payable {
     _useFundsIncomingFunds();
}
```
- [src/Treasury.sol#L269](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L269)