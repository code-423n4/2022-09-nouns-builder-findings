# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Multiple mappings related to the same data can be combined into a single mapping using a struct   | 5      |
| 2      | State variables only set in the constructor should be declared `immutable`     | 2 |
| 3      | Variables inside `struct` should be packed to save gas     | 2    |
| 4      | Using `bool` for storage incurs overhead   |  2 |
| 5      | Use `calldata` instead of `memory` for function parameters type  |  3 |
| 6      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  5  |
| 7      | Using `private` rather than `public` for constants, saves gas |  1 |
| 8      | Functions guaranteed to revert when called by normal users can be marked `payable` |  20 |

## Findings

### 1- Multiple mappings related to the same data can be combined into a single mapping using a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 5 instances of this issue :

```
File: src/lib/token/ERC721Votes.sol

29      mapping(address => address) internal delegation;
33      mapping(address => uint256) internal numCheckpoints;
37      mapping(address => mapping(uint256 => Checkpoint)) internal checkpoints;
```

These mappings could be refactored into the following `struct` and `mapping` for example :

```
struct UserCheckpoints {
        address delegation;
        uint256 numCheckpoints;
        mapping(uint256 => Checkpoint) checkpoints;
    }
    
mapping(address => UserCheckpoints) public userCheckpoints;
```

```
File: src/governance/governor/storage/GovernorStorageV1.sol

15      mapping(bytes32 => Proposal) internal proposals;
19      mapping(bytes32 => mapping(address => bool)) internal hasVoted;  
```

These mappings could be refactored into the following `struct` and `mapping` for example :

```
struct ProposalInfo {
        Proposal proposals;
        mapping(address => bool) hasVoted;
    }
    
mapping(bytes32 => ProposalInfo) public proposalsInfo;
```

### 2- State variables only set in the constructor should be declared `immutable` :

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

There are 2 instances of this issue:

```
File: src/lib/token/ERC721.sol

19      string public name;
22      string public symbol;
```

### 3- Variables inside `struct` should be packed to save gas :

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas.

There are 2 instances of this issue:

```
File: src/token/types/TokenTypesV1.sol

16      struct Settings {
            address auction;
            uint96 totalSupply;
            IBaseMetadata metadataRenderer;
            uint8 numFounders;
            uint8 totalOwnership;
        }
```

This should be rearranged as follow to save gas :

```
struct Settings {
        address auction;
        uint96 totalSupply;
        uint8 numFounders;
        uint8 totalOwnership;
        IBaseMetadata metadataRenderer;
    }
```

Thee second instance :

```
File: src/governance/governor/types/GovernorTypesV1.sol

19      struct Settings {
            Token token;
            uint16 proposalThresholdBps;
            uint16 quorumThresholdBps;
            Treasury treasury;
            uint48 votingDelay;
            uint48 votingPeriod;
            address vetoer;
        }
```

This should be rearranged as follow to save gas :

```
struct Settings {
        Token token;
        uint16 proposalThresholdBps;
        uint16 quorumThresholdBps;
        uint48 votingDelay;
        uint48 votingPeriod;
        Treasury treasury;
        address vetoer;
    }
```

### 4- Using `bool` for storage incurs overhead  :
 
Use `uint256(1)` and `uint256(2)` instead of `true/false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from `false` to `true`, after having been `true` in the past

There are 2 instances of this issue:

```
File: src/utils/Initializable.sol

20      bool internal _initializing;

File: src/utils/Pausable.sol

15      bool internal _paused;
```

### 5- Use `calldata` instead of `memory` for function parameters type :

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

There are 3 instances of this issue:

```
File: src/governance/governor/Governor.sol

98      function hashProposal(
            address[] memory _targets,
            uint256[] memory _values,
            bytes[] memory _calldatas,
            bytes32 _descriptionHash
        ) public pure returns (bytes32)
116     function propose(
            address[] memory _targets,
            uint256[] memory _values,
            bytes[] memory _calldatas,
            string memory _description
        ) external returns (bytes32)

File: src/token/metadata/MetadataRenderer.sol

308     function _encodeAsJson(bytes memory _jsonBlob) private pure returns (string memory)
```

### 6- It costs more gas to initialize variables to zero than to let the default of zero be applied :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 5 instances of this issue:

```
File: src/governance/treasury/Treasury.sol

162     for (uint256 i = 0; i < numTargets; ++i)

File: src/token/metadata/MetadataRenderer.sol

119     for (uint256 i = 0; i < numNewProperties; ++i)
133     for (uint256 i = 0; i < numNewItems; ++i)
189     for (uint256 i = 0; i < numProperties; ++i)
229     for (uint256 i = 0; i < numProperties; ++i)
```

### 7- Using private rather than public for constants, saves gas :

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

There is 1 instance of this issue:

```
File: src/governance/governor/Governor.sol

27       bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```

### 8- Functions guaranteed to revert when called by normal users can be marked `payable` :

If a function modifier such as `onlyAdmin` or `onlyRole` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 20 instances of this issue:

```
File: src/auction/Auction.sol

244      function unpause() external onlyOwner
263      function pause() external onlyOwner
307      function setDuration(uint256 _duration) external onlyOwner
315      function setReservePrice(uint256 _reservePrice) external onlyOwner
323      function setTimeBuffer(uint256 _timeBuffer) external onlyOwner
331      function setMinimumBidIncrement(uint256 _percentage) external onlyOwner

File: src/governance/governor/Governor.sol

564      function updateVotingDelay(uint256 _newVotingDelay) external onlyOwner
572      function updateVotingPeriod(uint256 _newVotingPeriod) external onlyOwner
580      function updateProposalThresholdBps(uint256 _newProposalThresholdBps) external onlyOwner
588      function updateQuorumThresholdBps(uint256 _newQuorumVotesBps) external onlyOwner
596      function updateVetoer(address _newVetoer) external onlyOwner
605      function burnVetoer() external onlyOwner

File: src/governance/treasury/Treasury.sol

116      function queue(bytes32 _proposalId) external onlyOwner returns (uint256 eta)
180      function cancel(bytes32 _proposalId) external onlyOwner 

File: src/manager/Manager.sol

187      function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner
196      function removeUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner

File: src/token/metadata/MetadataRenderer.sol

91       function addProperties(
            string[] calldata _names,
            ItemParam[] calldata _items,
            IPFSGroup calldata _ipfsGroup
        ) external onlyOwner
347      function updateContractImage(string memory _newContractImage) external onlyOwner
355      function updateRendererBase(string memory _newRendererBase) external onlyOwner
363      function updateDescription(string memory _newDescription) external onlyOwner
```
