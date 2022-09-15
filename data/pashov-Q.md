****Non library/interface files should use fixed compiler version****

All contracts under src/lib/utils are using floating pragma versions like 

```solidity
pragma solidity ^0.8.0;
```

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.