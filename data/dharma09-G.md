## DEFAULT VALUE INITIALIZATION

### PROBLEM
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### PROOF OF CONCEPT
Instances include:

### MetadataRenderer.sol
```MetadataRenderer.sol#L119
MetadataRenderer.sol#L133
MetadataRenderer.sol#L189
MetadataRenderer.sol#L229
```

### Treasury.sol
```
Treasury.sol#L162
```