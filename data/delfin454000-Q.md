### Typos

[IGovernor.sol: L290](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/IGovernor.sol#L290)
```solidity
    /// @param newVetoer The new vetoer addresss
```
Change `addresss` to `address`
___
[ERC721Votes.sol: L160](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L160)
```solidity
            // Compute the hash of the domain seperator with the typed delegation data
```
Change `seperator` to `separator`
___
[ERC721Votes.sol: L221](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L221)
```solidity
                    // Get the recipients's number of checkpoints
```
Change `recipients's` to `recipient's`
___
[Manager.sol: L113](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L113)
```solidity
        // This founder is responsible for adding token artwork and launching the first auction -- they're also free to transfer this responsiblity
```
Change `responsiblity` to `responsibility`
___
[Token.sol: L104](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L104)
```solidity
                // Used to store the base token id the founder will recieve
```
Change `recieve` to `receive`
___
___

### Long single line comments 
In theory, comments over 79 characters should wrap using multi-line comment syntax. Even if somewhat longer comments are acceptable, there are cases where very long comments interfere with readability. Below are five instances of extra-long comments whose readability could be improved by wrapping, as shown:

___
[Manager.sol: L113](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L113)
```solidity
        // This founder is responsible for adding token artwork and launching the first auction -- they're also free to transfer this responsiblity
```
Suggestion:
```solidity
        // This founder is responsible for adding token artwork and launching the first auction — 
        //   they're also free to transfer this responsibility.
```
___
The following comment occurs twice:

[IToken.sol: L86](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/IToken.sol#L86)

[Token.sol: L268](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L268)
```solidity
    /// NOTE: If a founder is returned, there's no guarantee they'll receive the token as vesting expiration is not considered
```
Suggestion:
```solidity
    /// NOTE: If a founder is returned, there's no guarantee they'll receive the token
    ///   as vesting expiration is not considered.
```
___
[IManager.sol: L55](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/IManager.sol#L55)
```solidity
    /// @param initStrings The encoded token name, symbol, collection description, collection image uri, renderer base uri
```
Suggestion:
```solidity
    /// @param initStrings The encoded token name, symbol, collection description, 
    ///   collection image uri, renderer base uri.
```
___
[Governor.sol: L362](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L362)
```solidity
            // Ensure the caller is the proposer or the proposer's voting weight has dropped below the proposal threshold
```
Suggestion:
```solidity
            // Ensure the caller is the proposer or the proposer's voting weight
            //   has dropped below the proposal threshold.
```
___
[Initializable.sol: L26](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L26)
```solidity
    /// @dev Ensures an initialization function is only called within an `initializer` or `reinitializer` function
```
Suggestion:
```solidity
    /// @dev Ensures an initialization function is only called
    ///   within an `initializer` or `reinitializer` function.
```
___
___

### Missing `@param` statements

`@param` statements are missing for `_forceCall` in both functions referenced below: 

[ERC1967Upgrade.sol: L30-37](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L30-L37)

[ERC1967Upgrade.sol: L51-58](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L51-L58)

*Example ([ERC1967Upgrade.sol: L51-58](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L51-L58)):*
```solidity
    /// @dev Upgrades to an implementation with an additional function call
    /// @param _newImpl The new implementation address
    /// @param _data The encoded function call
    function _upgradeToAndCall(
        address _newImpl,
        bytes memory _data,
        bool _forceCall
    ) internal {
```
___
`@param` statement is missing for `_data` below: 

[ERC721.sol: L170-179](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L170-L179)
```solidity
    /// @notice Safe transfers a token from sender to recipient with additional data
    /// @param _from The sender address
    /// @param _to The recipient address
    /// @param _tokenId The ERC-721 token id
    function safeTransferFrom(
        address _from,
        address _to,
        uint256 _tokenId,
        bytes calldata _data
    ) external {
```
___
___

### Inconsistent initialization of counters in `for` loops 
Some `for` loop counters are initiated to zero (e.g., `uint256 i = 0;`) in `Nouns Builder` whereas others are not (`uint256 i;`). It is not necessary to initialize `for` loop counters to zero since this is their default value. For consistency, it makes sense to omit counter initializations in the `for` loops below: 

[Treasury.sol: L162](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L162)

[MetadataRenderer.sol: L119](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L119)

[MetadataRenderer.sol: L133](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L133)

[MetadataRenderer.sol: L189](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L189)

[MetadataRenderer.sol: L229](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L229)
___
___