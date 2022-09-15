### Inline a function/modifier that is only used once
It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.

Since notDelegated() is only used once in this contract (in function proxiableUUID()), it should get inlined to save gas

[UUPS.sol: L31-35](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L31-L35)
```solidity
    /// @dev Ensures that execution is via direct call
    modifier notDelegated() {
        if (address(this) != __self) revert ONLY_CALL();
        _;
    }
```
`notDelegated()` is used only once:

[UUPS.sol: L60-63](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/UUPS.sol#L60-L63)
```solidity
    /// @notice The storage slot of the implementation address
    function proxiableUUID() external view notDelegated returns (bytes32) {
        return _IMPLEMENTATION_SLOT;
    }
```
___
___