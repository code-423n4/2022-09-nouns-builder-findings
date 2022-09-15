# QA
# Low
## Unused receive() function will lock ether in contract
### Summary
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L269
### Mitigation
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert.


## Missing checks for address(0x0) when assigning values to immutable address variables
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L30-L32
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L32-L34
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L62-L71
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L31-L34
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L41-L43
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L39-L42

Also this values should be checked even if they are not immutable, as they are not reassigned.
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L64-L66
### Mitigation
Check zero address before assigning or using it



## block.timestamp used as time proxy 
### Summary:
Risk of using block.timestamp for time should be considered. 
### Details:
block.timestamp is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L116-L175
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L208-L242
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L353-L377
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L413-L456
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L74-L78
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L82-L84
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L88-L90
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L90-L154
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L167-L201
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L344-L365
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L59-L118
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L144-L174
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L177-L199



### Mitigation
Consider using an oracle for precision
Consider the risk of using block.timestamp as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 

## Front run initializer
### Summary
The initialize function that initializes important contract state can be called by anyone.
### Details
The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.

In the best case for the victim, they notice it and have to redeploy their contract costing gas.

In this case, `deploy` function what calls all `initialize` functions has no way of access control, leading to anybody being able to deploy before it's expected owners

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L97-L102
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L132-L144

### Mitigation
Use the constructor to initialize non-proxied contracts.

For initializing proxy contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.

## abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccack256()
### Summary
If you are dealing with more than one dynamic data type, abi.encodePacked() can lead to collisions when used with a hash function.

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456) , but abi.encode(0x123,0x456) => 0x0...1230...456 ). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to 
bytes() or bytes32() instead.

### Details
abi.encodePacked will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the keccak method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.

https://ethereum.stackexchange.com/questions/119583/when-to-use-abi-encode-abi-encodepacked-or-abi-encodewithsignature-in-solidity

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L68-L71
### Mitigation
Change abi.encodePacked to abi.encode when data collision may happen

## Return value not being checked 
### Details
Return values not being checked may lead into unexpected behaviors with functions. Not events/Error are being emitted if that fails, so functions would be called even of not being working as expect as for in `_upgradeToAndCall`

### Github permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/proxy/ERC1967Upgrade.sol#L62

### Mitigation
Check values and revert/emit events if needed


# Informational
##  Use of magic numbers is confusing and risky
### Summary:
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details:
Values are hardcoded and would be more readable and maintainable if declared as a constant

### Github Permalinks

- 100
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L119
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L88
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L102
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L118
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L179
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L271

- 50000
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L354
            success := call(50000, _to, _amount, 0, 0, 0, 0)

- 10_000
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L468
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L475

- 16
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L179
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L197

### Mitigation
Replace magic hardcoded numbers with declared constants.


## Missing indexed event parameters
### Summary:
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details:
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/IAuction.sol#L22-L50
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/IGovernor.sol#L29-L57
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/ITreasury.sol#L14-L28
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/interfaces/IERC1967Upgrade.sol#L14
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/interfaces/IInitializable.sol#L13
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/interfaces/IPausable.sol#L14-L18
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/IManager.sol#L20-L31
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/IToken.sol#L21
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L15-L28
### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.


## Naming convention of state variable non constant
### Summary
Only constants are suggested to use style CONSTANTS_WITH_UNDERSCORES, other variables are suggested to use camelCase
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L28
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/EIP712.sol#L26-L35
### Mitigation
Rename to camelCase

## Typo in comment
### Summary
 `psuedo`-random seed for a token id
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L249
### Mitigation
Replace to `pseudo`

## Unused code
### Summary
Code that is not used should be removed
### Details:
- Some functions are declared following typical libraries, however, some functions are not used within the code and could be removed
### Github Permalinks
- Functions not used
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/SafeCast.sol#L33-L37
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/SafeCast.sol#L15-L19
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Address.sol#L25-L27
### Mitigation
Remove the code that is not used.

## Variable shadows another variable
###  Summary: 
Name shadowing where two or more variables/functions share the same name could be confusing to developers and/or reviewers
###  Details: 
Use of `_owner` as new value variable shadows Ownable `_owner`
###  Github Permalinks: 
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L78-L86
###  Mitigation
Replace `_owner` variable in the function parameter to `owner_`, `new_owner` or a similar substitution



## abi.encodePacked is being used rather than string.concat()
### Summary
Rather than using abi.encodePacked for appending string, since version 0.8.12, string.concat() is enabled to be used instead of abi.encodePacked(<str>,<str>)
### Details
Code is expected to be deployed at 0.8.15 at least
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L208-L213
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L259
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L272-L280
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L290-L303
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L309
### Mitigation
Consider changing to string.concat() for appending strings

## Missing Natspec 
### Summary: 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 
### Details: 
Contracts has partial or full lack of comments
### Github Permalinks 
- NOTE: All the links are to the contracts, but the interfaces don't include neither the @returns natspec value
#### Natspec @param and @return value
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L236-L266
#### Natspec @return value
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L276-L299
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L103
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L121
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L184-L345
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L413-L495
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L515-L556
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L193-L202
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L66-L116
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/proxy/ERC1967Proxy.sol#L30
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/proxy/ERC1967Upgrade.sol#L82-L85
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/proxy/UUPS.sol#L61-L63
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/token/ERC721.sol#L54
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/token/ERC721.sol#L61-L96
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Address.sol#L25-L43
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/EIP712.sol#L55-L70
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Ownable.sol#L51-L59
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Pausable.sol#L43-L46
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L97-L182
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L171-L339
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L76-L85
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L128-L162
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L177-L198
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L219-L296

#### Natspec + comments
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/SafeCast.sol#L9-L49
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/TokenReceiver.sol#L6-L36


### mitigation
- Add @param descriptors
- Add @return descriptors
- Add comments when needed



## So similar name in different functions
### Summary
Names being so similar may lead to unexpected calls and assumptions.
### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/interfaces/IERC721Votes.sol#L53-L59
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L126-L135
### Mitigation
Consider making more unique the names of this variables/functions. For example:
- `delegateVotes()`

## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
### Details
`receive` and `fallback` functions are expected to be at the very end of the contract
### github permalink
- Receive should be located at the end of the file
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L268-L269

### Mitigation
Move the function to the very end


## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.8.4. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/TokenReceiver.sol#L2
`pragma solidity ^0.8.0;`
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/interfaces/IUUPS.sol#L2
`pragma solidity ^0.8.15;`

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/interfaces/IWETH.sol#L2
`pragma solidity ^0.8.15;`

And a lot of instances of:
`pragma solidity ^0.8.4;`


### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^0.8.0 into 0.8.15
Consider converting ^0.8.4 into 0.8.15
Consider converting ^0.8.15 into 0.8.15

## Maximum line length exceeded
### Summary:
 Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details: 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks: 

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L138

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L371

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L376

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/IAuction.sol#L22

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L27

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L128

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L172

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L230

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L239

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L340

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L362

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L363

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L441

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L472

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L615

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L620

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/IGovernor.sol#L42

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/IGovernor.sol#L222

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/types/GovernorTypesV1.sol#L13

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/interfaces/IERC721Votes.sol#L19

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/proxy/ERC1967Upgrade.sol#L21

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/proxy/ERC1967Upgrade.sol#L24

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/proxy/ERC1967Upgrade.sol#L30

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721.sol#L105

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721.sol#L134

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721.sol#L166

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721.sol#L184

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L10

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L21

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L78

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L162

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L170

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L212

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L213

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L216

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L227

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/token/ERC721Votes.sol#L228

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Address.sol#L37

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Address.sol#L46

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/EIP712.sol#L9

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/EIP712.sol#L19

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/EIP712.sol#L64

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/EIP712.sol#L69

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Initializable.sol#L26

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/lib/utils/Initializable.sol#L36

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/IManager.sol#L21

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/IManager.sol#L55

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/IManager.sol#L72

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/IManager.sol#L135

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/IManager.sol#L137

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L68

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L69

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L70

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L71

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L79

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L113

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L134

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L167

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L168

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L169

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L170

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L180

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L19

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L55

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L206

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L243

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L244

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L251

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L255

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L259

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L377

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L63

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/IToken.sol#L86

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L59

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L88

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L221

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L268

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L302

https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L310

### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 

## Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability
### Summary:
Multiples of 10 can be declared as constants with scientific notation so it's easier to read them and less prone to miss/exceed a 0 of the expected value.

### Details
Values 100, 10_000 and 50000 can be used in scientific notation
### Github Permalinks
- 100
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L119
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L88
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L102
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L118
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L179
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/Token.sol#L271

- 50000
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/auction/Auction.sol#L354

- 10_000
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L468
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L475
### Mitigation
Replace hardcoded numbers with constants that represent the scientific corresponding notation


