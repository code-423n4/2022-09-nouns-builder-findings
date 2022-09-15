# [L-01] Missing `onlyInitializing` modifier
## Targets:
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L71
## Description
The `_addFounders` function is only called during initialization (the `initialize` function). It's recommended to add
the `onlyInitializing` modifier to the function for consistency with the `__ReentrancyGuard_init` and `__ERC721_init`
functions called in the `initialize` function. It'll also protect from future contract updates accidentally calling this function outside of `initialize`.

# [L-02] Duplicate dependency for the same functionality
## Targets:
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L212
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L294
## Description
There are two dependency contracts that are used for the same functionality:
- `Strings` from "@openzeppelin/contracts/utils/Strings.sol";
- `LibUintToString` from "sol2string/contracts/LibUintToString.sol".

They're both used to convert a `uint` to a string. To ensure that the exact same operation is made in both of the linked cases, it's recommended to use one library,

# [L-03] Missing zero-address check
## Targets:
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L66
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L72
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L188
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L197

# [L-04] Avoid shadowing of state variables
## Targets:
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L82 (`_owner`)

# [N-01] Unused operation result
## Targets:
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118
## Description
The result of the modulus operator is not used.

# [N-02] Dead code
## Targets:
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L55
- https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L74
## Description
It's recommended to remove unused code to reduce the cost of contract deployment.