# QA Report
# Low Risk Issues
## [L-01] Replace inline assembly with `account.code.length`
`<address>.code.length` can be used in Solidity >= 0.8.0 to access an account’s code size and check if it is a contract without inline assembly.

There is 1 instance of this issue:

[Address#isContract](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L31-L33)
```solidity
File: src/lib/utils/Address.sol     #1

31:        assembly {
32:            rv := gt(extcodesize(_account), 0)
33:        }
```