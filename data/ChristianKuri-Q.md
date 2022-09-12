## [L-01] Front-runnable initializer

If the initializer is not executed in the same transaction as the constructor, a malicious user can front-run the `initialize()` call. In the best case, the owner is forced to waste gas and re-deploy. In the worst case, the owner does not notice that his/her call reverts, and everyone starts using a contact that the attacker would have control of (the manager) since he will be the owner.

If the manager contract is initialized before upgrading to v2, the manager proxy would also have incorrect values, and all the contract for the logic of the token, auction, governance, treasury and metadata would have an incorrect manager address. Making the deployer deploy everything again.

[manager/Manager.sol#L80-L86](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L80-L86)

```solidity
File: manager/Manager.sol   #1

80    function initialize(address _owner) external initializer {
81        // Ensure an owner is specified
82        if (_owner == address(0)) revert ADDRESS_ZERO();
83
84        // Set the contract owner
85        __Ownable_init(_owner);
86    }
```


## [L-02] Add address(0) check to all the addresses that are passed in all the initializers

The Treasury `initialize()` function have a check for `address(0)` in the governor param.
[treasury/Treasury.sol#L43-L60](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L43-L60)
```solidity
File: treasury/Treasury.sol#L47-L48

47    // Ensure a governor address was provided
48    if (_governor == address(0)) revert ADDRESS_ZERO();
```

The Governor `initialize()` function have a check for `address(0)` in the treasury and token params.
[governor/Governor.sol#L57-L87](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L57-L87)
```solidity
File: governor/Governor.sol#L69-L71

69    // Ensure non-zero addresses are provided
70    if (_treasury == address(0)) revert ADDRESS_ZERO();
71    if (_token == address(0)) revert ADDRESS_ZERO();
```

To keep consistency, all the initializers should have `address(0)` check as well.

[token/Token.sol#L43](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L43)
[metadata/MetadataRenderer.sol#L45](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L45)
[auction/Auction.sol#L54](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L54)


## [L-03] A Paused Auction that already started can still be bid on and settled

The auction can be paused and then started again. If the auction is paused it can be bid on and settled. I consider that if an auction is paused it should not allow anyone to bid on it or settle it. When the auction is unpaused it should increment the auction duration by the duration of the pause, this way the auction will be correctly paused.

[auction/Auction.sol#L90-L127](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L90-L127)
[auction/Auction.sol#L268-L270](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L268-L270)

## [L-04] Auction duration can be a really low number

The auction duration can be set to a really low number, this can cause the auction to end before anyone can interact with it. The auction should have a minimum duration. When the auction finishes and settles it burns the token if no one bid, but keeps increasing the totalSupply of the token.

## [N-01] Return value could confuse developers

When calling a `uint++` the value that is returned is the previous one.

[token/Token.sol#L154](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L154)

```solidity
File: token/Token.sol   #1

/// Some developers could thing that founderId is the new value from settings.numFounders (1)
/// But instead in the first iteration founderId will be 0
uint256 founderId = settings.numFounders++;
```

My recommendation is do it this way.
  
```solidity
uint256 founderId = settings.numFounders;
settings.numFounders++;
```

## [N-02] Modulus after the set is not needed

The % after the assignment is not necessary, it does not do anything. Also the Schedule cannot be greater than 100.

[token/Token.sol#L118](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118)

```solidity
(baseTokenId += schedule) % 100;
```