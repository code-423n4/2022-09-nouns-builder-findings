# QA Report

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Immutable addresses lack zero address `address(0)` checks | 10 |
| 2      | `transferOwnership` and `safeTransferOwnership` functions missing zero address checks for `_newOwner` | 2|
| 3      | Setters should check the input value and revert if it's the zero address or zero  |  10 |
| 4      | Duplicated checks statements should be refactored to a modifier  |  3 |
| 5      | Replace inline assembly with `address().code.length` |  1 |
| 6      | Related data should be grouped in a struct |  2 |


## Findings

### 1- Missing zero address `address(0)` checks  :

Constructors should check the address written in an immutable address variable is not the zero address

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/auction/Auction.sol

[manager = IManager(_manager);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L40)

[WETH = _weth;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L41)

File: src/governance/governor/Governor.sol

[manager = IManager(_manager);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L42)

File: src/governance/treasury/Treasury.sol

[manager = IManager(_manager);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L33)

File: src/manager/Manager.sol

[tokenImpl = _tokenImpl;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L62)

[metadataImpl = _metadataImpl;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L63)

[auctionImpl = _auctionImpl;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L64)

[treasuryImpl = _treasuryImpl;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L65)

[governorImpl = _governorImpl;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L66)

File: src/token/Token.sol

[manager = IManager(_manager);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L31)

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.

### 2- `transferOwnership` and `safeTransferOwnership` functions missing zero address checks for `_newOwner` :

The `transferOwnership` and `safeTransferOwnership` functions inside the Ownable contract are missing a zero address check for the newOwner address. In the case of `safeTransferOwnership` function this would not create any harm beacause of the two-step ownership transfer, but for the `transferOwnership` function if the `address(0)` is passed by mistake it would transfer the ownership to ZERO address and the contract that uses the Ownable library will be lost. 

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/lib/utils/Ownable.sol

[function transferOwnership(address _newOwner) public onlyOwner](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L63-L67)

[function safeTransferOwnership(address _newOwner) public onlyOwner](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L71-L75)

#### Mitigation
Add non-zero address checks on the _newOwner address in the `transferOwnership` and `safeTransferOwnership` functions as it is implemented in the [Openzeppelin Ownable contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol#L69-L82)

### 3- Setters should check the input value and revert if it's the zero address or zero :

Setters should check the input values and revert if it's the zero address or zero.

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/auction/Auction.sol

[function setDuration(uint256 _duration)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307)

[function setReservePrice(uint256 _reservePrice)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315)

[function setTimeBuffer(uint256 _timeBuffer)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323)

[function setMinimumBidIncrement(uint256 _percentage)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331)

File: src/governance/governor/Governor.sol

[function updateVotingDelay(uint256 _newVotingDelay)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L564)

[function updateVotingPeriod(uint256 _newVotingPeriod)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L572)

[function updateProposalThresholdBps(uint256 _newProposalThresholdBps)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L580)

[function updateQuorumThresholdBps(uint256 _newQuorumVotesBps)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L588)

File: src/governance/treasury/Treasury.sol

[function updateDelay(uint256 _newDelay)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L210)

[function updateGracePeriod(uint256 _newGracePeriod)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L222)

#### Mitigation
Add non-zero checks for uint to the setters aforementioned.

### 4- Duplicated checks statements should be refactored to a modifier :

When a check statement is duplicated into multiple functions inside a contract, it's better to refactore those checks into a modifier for better readability.

#### Impact - NON CRITICAL

#### Proof of Concept

There are 3 instances of this issue:

```
File: src/governance/treasury/Treasury.sol

212     if (msg.sender != address(this)) revert ONLY_TREASURY();
224     if (msg.sender != address(this)) revert ONLY_TREASURY();
280     if (msg.sender != address(this)) revert ONLY_TREASURY();
```

#### Mitigation
Those checks should be replaced by an **onlyTreasury** modifier as follow :

```
modifier onlyTreasury(){
    if (msg.sender != address(this)) {
            revert ONLY_TREASURY();
        }
}
```

### 5- Replace inline assembly with `address().code.length` :

`<address>.code.length` can be used in Solidity `>= 0.8.0` to access an account’s code size and check if it is a contract without inline assembly.

#### Impact - NON CRITICAL

#### Proof of Concept

There is 1 instance of this issue:

File: contracts/lib/utils/Address.sol

[function isContract(address _account)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L30-L34)

#### Mitigation
The function isContract below should be replaced :

```
File: contracts/lib/utils/Address.sol

    function isContract(address _account) internal view returns (bool rv) {
        assembly {
            rv := gt(extcodesize(_account), 0)
        }
    }
```

I propose to replace it with : 

```
    function isContract(address _account) internal view returns (bool rv) {
        rv = _account.code.length != 0;
    }
```


### 6- Related data should be grouped in a struct :

When there are mappings that use the same key value, having separate fields is error prone, for instance in case of deletion or with future new fields.

#### Impact - NON CRITICAL

#### Proof of Concept

Instances include:

[src/lib/token/ERC721Votes.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L29-L37)

```
File: src/lib/token/ERC721Votes.sol

29      mapping(address => address) internal delegation;
33      mapping(address => uint256) internal numCheckpoints;
37      mapping(address => mapping(uint256 => Checkpoint)) internal checkpoints;
```

[src/governance/governor/storage/GovernorStorageV1.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/storage/GovernorStorageV1.sol#L15-L19)

```
File: src/governance/governor/storage/GovernorStorageV1.sol

15      mapping(bytes32 => Proposal) internal proposals;
19      mapping(bytes32 => mapping(address => bool)) internal hasVoted;  
```

#### Mitigation

The first instance could be refactored into the following `struct` and `mapping` for example :

```
struct UserCheckpoints {
        address delegation;
        uint256 numCheckpoints;
        mapping(uint256 => Checkpoint) checkpoints;
    }
    
mapping(address => UserCheckpoints) public userCheckpoints;
```

The second instance could be refactored into the following `struct` and `mapping` for example :

```
struct ProposalInfo {
        Proposal proposals;
        mapping(address => bool) hasVoted;
    }
    
mapping(bytes32 => ProposalInfo) public proposalsInfo;
```