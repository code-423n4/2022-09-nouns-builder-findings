- Use a more recent version of Solidity.
	- Use latest 0.8.17 for security. [release note](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)
	- Instances: files within the entire scope.


- `++x` is more efficient than `x++` (saves \~6 gas).
	- Instances:
		1. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230
		2. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L154

- `abi.encode()` is less efficient than `abi.encodePacked()`.
	- Instances:
		1. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230
		2. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L104
		3. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L251

- It costs more gas to initialize NON-CONSTANT/NON-IMMUTABLE variables to zero than to let the default of zero be applied.
	- Instances:
		1. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162
		2. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119
		3. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133
		4. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189
		5. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229

- `x += y` cost more gas than `x = x + y` for state variables.
	- Instances:
		1. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280
		2. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285
		3. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290
		4. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L88
		5. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118
		6. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140

- Using PRIVATE rather than PUBLIC for CONSTANTS, saves gas.
	- Instance:
		1. https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27

