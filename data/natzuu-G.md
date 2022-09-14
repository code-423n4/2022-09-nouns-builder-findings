# G001 - Don't Initialize Variables with Default Value

### Description

Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

```solidity
for (uint256 i = 0; i < numTargets; ++i) {
```

[https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162)

```solidity
for (uint256 i = 0; i < numNewProperties; ++i) {
```

[https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L119](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L119)

```solidity
for (uint256 i = 0; i < numNewItems; ++i) {
```

[https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L133](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L133)

```solidity
for (uint256 i = 0; i < numProperties; ++i) {
```

[https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L189](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L189)

```solidity
for (uint256 i = 0; i < numProperties; ++i) {
```

[https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L229](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L229)

### background info

[https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/](https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/)