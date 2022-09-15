## L-01  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

There were 5 instances of this issue.

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2

```
File: src/lib/proxy/ERC1967Proxy.sol    #1

pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2

```
File: src/lib/proxy/ERC1967    #2

pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2

```
File: src/lib/proxy/UUPS.sol    #3

pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2

```
File: src/lib/token/ERC721.sol    #4

pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2

```
File: src/lib/token/ERC721Votes.sol    #5

pragma solidity ^0.8.4;
```

### Recommended Mitigation Steps

Lock the pragma version

---------------

## N-01  USE A MORE RECENT VERSION OF SOLIDITY

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives security fixes. Furthermore, breaking changes as well as new features are introduced regularly.

### Proof of Concept

All contracts in [auction](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction "auction"), [governance](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance "governance"), [manager](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/manager "manager") and [token](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token "token") are using the Solidity version 0.8.15 

### Recommended Mitigation Steps

Update to the latest released version of Solidity

__________
## N-02  NATSPEC IS INCOMPLETE

### Proof of Concept

There are 2 instances of this issue:

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L30-L36

```
File: src/lib/proxy/ERC1967Upgrade.sol    #1

    /// @dev Upgrades to an implementation with security checks for UUPS proxies and an additional function call
    /// @param _newImpl The new implementation address
    /// @param _data The encoded function call
    function _upgradeToAndCallUUPS(
        address _newImpl,
        bytes memory _data,
        bool _forceCall
```

Missing: `@param _forceCall`

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L51-L57

```
File: src/lib/proxy/ERC1967Upgrade.sol    #2

	/// @dev Upgrades to an implementation with an additional function call
    /// @param _newImpl The new implementation address
    /// @param _data The encoded function call
    function _upgradeToAndCall(
        address _newImpl,
        bytes memory _data,
        bool _forceCall
```

Missing: `@param _forceCall`
