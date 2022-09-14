- [Low](#low)
    - [**1. Outdated compiler**](#1-outdated-compiler)
    - [**2. Method marked as payable unnecessarily**](#2-method-marked-as-payable-unnecessarily)
    - [**3. Integer overflow by unsafe casting**](#3-integer-overflow-by-unsafe-casting)
    - [**4. Lack of checks**](#4-lack-of-checks)
    - [**5. Not fully decentralized**](#5-not-fully-decentralized)
    - [**6. Unsecure state order**](#6-unsecure-state-order)
    - [**7. Timestamp dependence**](#7-timestamp-dependence)
- [Non critical](#non-critical)
    - [**8. Use abstract for base contracts**](#8-use-abstract-for-base-contracts)
    - [**9. Avoid duplicate logic**](#9-avoid-duplicate-logic)
    - [**10. Avoid hardcoded values**](#10-avoid-hardcoded-values)
    - [**11. Unpredictable change of owner**](#11-unpredictable-change-of-owner)
    - [**12. Improve user experience**](#12-improve-user-experience)

# Low

## **1. Outdated compiler**

The pragma version used are:

```
pragma solidity ^0.8.15;
pragma solidity 0.8.15;
pragma solidity ^0.8.4;
pragma solidity ^0.8.0;
```

*Note that mixing pragma are not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.3](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/):

- Optimizer: Fix bug on incorrect caching of Keccak-256 hashes.

[0.8.4](https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/):

- ABI Decoder V2: For two-dimensional arrays and specially crafted data in memory, the result of abi.decode can depend on data elsewhere in memory. Calldata decoding is not affected.

[0.8.9](https://blog.soliditylang.org/2021/09/29/solidity-0.8.9-release-announcement/):

- Immutables: Properly perform sign extension on signed immutables.
- User Defined Value Type: Fix storage layout of user defined value types for underlying types shorter than 32 bytes.

[0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/):
- Code Generator: Correctly encode literals used in `abi.encodeCall` in place of fixed bytes arguments.

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

Apart from these, there are several minor bug fixes and improvements.

## **2. Method marked as `payable` unnecessarily**

The following methods have been marked as `payable`, this may cause both administrators and users calling them to accidentally deposit ether with no recovery possible.

This may be done to save gas, but may result in more loss than benefit in case of one human error.

**Affected source code:**

- [Manager.sol:61](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L61)
- [ERC1967Proxy.sol:21](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Proxy.sol#L21)
- [MetadataRenderer.sol:32](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L32)
- [Token.sol:30](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L30)
- [Auction.sol:39](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L39)
- [Governor.sol:41](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L41)

## **3. Integer overflow by unsafe casting**

Keep in mind that the version of solidity used, despite being greater than `0.8`, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

**Example:**

A dangerous example is with the `quorum` method.

```javascript
        proposal.quorumVotes = uint32(quorum());
```

This method depends the token's `totalSupply`, so it could overflow with certains tokens.

```javascript
function quorum() public view returns (uint256) {
    unchecked {
        return (settings.token.totalSupply() * settings.quorumThresholdBps) / 10_000;
    }
}
```

**Recommendation:**

Use a [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) from Open Zeppelin.

**Affected source code:**

- [ERC721Votes.sol:252](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L252)
- [Token.sol:88](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L88)
- [Token.sol:98](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L98)
- [Token.sol:99](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L99)
- [Token.sol:123](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L123)
- [Token.sol:124](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L124)
- [Auction.sol:149](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L149)
- [Auction.sol:223](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L223)
- [Auction.sol:224](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L224)
- [Governor.sol:168](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L168)
- [Governor.sol:280](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L280)
- [Governor.sol:285](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L285)
- [Governor.sol:290](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L290)

## **4. Lack of checks**

The following methods have a lack of checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

**Affected source code for `address(0)`:**

- [Manager.sol:62-66](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L62-L66)
- [MetadataRenderer.sol:33](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L33)
- [Treasury.sol:33](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L33)
- [Token.sol:31](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L31)
- [Token.sol:65](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L65)
- [Token.sol:66](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L66)
- [Auction.sol:40](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L40)
- [Auction.sol:41](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L41)
- [Auction.sol:79](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L79)
- [Governor.sol:42](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L42)

**It wasn't checked `_delay` max value (E.g. more than 1 month...):**

- [Treasury.sol:43](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L43)

## **5. Not fully decentralized**

The `updateVotingDelay`, `updateVotingPeriod`, `updateProposalThresholdBps`, `updateQuorumThresholdBps`, `updateVetoer`, `burnVetoer` methods in the `Governor` contract have the `onlyOwner` modifier, but to be fully decentralized, these methods must verify that the address is the same as the contract himself to force a proposal to call these methods.

**Affected source code:**

- [Governor.sol:564-609](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L564-L609)

## **6. Unsecure state order**

Negative states must be processed before positive states, otherwise, a proposal that is `Expired` or `Executed`, but is `Active` or `Pending` will be returned as `Active` or `Pending`, this makes it necessary to check that the state is correct from outside this method. I mean, changing outside the variables that alter this state in the correct way.

```diff
    function state(bytes32 _proposalId) public view returns (ProposalState) {
        // Get a copy of the proposal
        Proposal memory proposal = proposals[_proposalId];

        // Ensure the proposal exists
        if (proposal.voteStart == 0) revert PROPOSAL_DOES_NOT_EXIST();

        // If the proposal was executed:
        if (proposal.executed) {
            return ProposalState.Executed;

            // Else if the proposal was canceled:
        } else if (proposal.canceled) {
            return ProposalState.Canceled;

            // Else if the proposal was vetoed:
        } else if (proposal.vetoed) {
            return ProposalState.Vetoed;

            // Else if voting has not started:
        } else if (block.timestamp < proposal.voteStart) {
            return ProposalState.Pending;

+           // Else if the proposal can no longer be executed:
+       } else if (settings.treasury.isExpired(_proposalId)) {
+           return ProposalState.Expired;
-           // Else if voting has not ended:
-       } else if (block.timestamp < proposal.voteEnd) {
-           return ProposalState.Active;

            // Else if the proposal failed (outvoted OR didn't reach quorum):
        } else if (proposal.forVotes < proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {
            return ProposalState.Defeated;

+           // Else if voting has not ended:
+       } else if (block.timestamp < proposal.voteEnd) {
+           return ProposalState.Active;
            // Else if the proposal has not been queued:
        } else if (settings.treasury.timestamp(_proposalId) == 0) {
            return ProposalState.Succeeded;

-           // Else if the proposal can no longer be executed:
-       } else if (settings.treasury.isExpired(_proposalId)) {
-           return ProposalState.Expired;

            // Else the proposal is queued
        } else {
            return ProposalState.Queued;
        }
    }
```

**Affected source code:**

- [Governor.sol:413-456](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L413-L456)

## **7. Timestamp dependence**

There are three main considerations when using a timestamp to execute a critical function in a contract, especially when actions involve fund transfer.

- Timestamp manipulation
  - Be aware that the timestamp of the block can be manipulated by a miner
- The 15-second Rule
  - The [Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf) (Ethereum’s reference specification) does not specify a constraint on how much blocks can drift in time, but [it does specify](https://ethereum.stackexchange.com/a/5926/46821) that each timestamp should be bigger than the timestamp of its parent. Popular Ethereum protocol implementations Geth and Parity both reject blocks with timestamp more than 15 seconds in future. Therefore, a good rule of thumb in evaluating timestamp usage is: if the scale of your time-dependent event can vary by 15 seconds and maintain integrity, it is safe to use a `block.timestamp`.
- Avoid using `block.number` as a timestamp
  - It is possible to estimate a time delta using the `block.number` property and [average block time](https://etherscan.io/chart/blocktime), however this is not future proof as block times may change (such as [fork reorganisations](https://blog.ethereum.org/2015/08/08/chain-reorganisation-depth-expectations) and the [difficulty bomb](https://github.com/ethereum/EIPs/issues/649)). In a sale spanning days, the 15-second rule allows one to achieve a more reliable estimate of time.
  See [SWC-116](https://swcregistry.io/docs/SWC-116)

**Reference:**

- https://consensys.net/blog/developers/solidity-best-practices-for-smart-contract-security/

**Affected source code:**

- [Treasury.sol:89](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L89)

---

# Non critical

## **8. Use `abstract` for base contracts**

`abstract` contracts are contracts that have at least one function without its implementation. **An instance of an abstract cannot be created.**

**Reference:**

- https://docs.soliditylang.org/en/v0.6.2/contracts.html#abstract-contracts

**Affected source code:**

- [TokenStorageV1.sol:9](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/storage/TokenStorageV1.sol#L9)
- [MetadataRendererStorageV1.sol:9](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/storage/MetadataRendererStorageV1.sol#L9)
- [ManagerStorageV1.sol:7](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/storage/ManagerStorageV1.sol#L7)
- [TreasuryTypesV1.sol:7](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/types/TreasuryTypesV1.sol#L7)
- [TreasuryStorageV1.sol:9](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/storage/TreasuryStorageV1.sol#L9)
- [GovernorStorageV1.sol:9](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/storage/GovernorStorageV1.sol#L9)
- [AuctionTypesV1.sol:7](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/types/AuctionTypesV1.sol#L7)
- [AuctionStorageV1.sol:10](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/storage/AuctionStorageV1.sol#L10)

## **9. Avoid duplicate logic**

Use [Address.toBytes32](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L26) instead of replicate the logic.

**Affected source code:**

- [Manager.sol:123](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L123)
- [Manager.sol:165](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L165)

## **10. Avoid hardcoded values**

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these addresses from the source code.

use selectors:

- [ERC721.sol:63-L65](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L63-L65)

Use `immutable` with the formula, instead of the result:

- [ERC1967Upgrade.sol:21](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L21)
- [ERC1967Upgrade.sol:24](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L24)

## **11. Unpredictable change of owner**

The `MetadataRenderer.addProperties` method takes advantage when `ipfsData` is not defined to transfer the owner to the treasury, however this logic should be done in an initialization method and not in a configuration one, since the user does not expect this type of change when calling to this type of methods, it is convenient to adapt the logic or mention this behavior in a comment.

**Affected source code:**

- [MetadataRenderer.sol:102](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L102)

## **12. Improve user experience**

In the `Auction._handleOutgoingTransfer` method, before throwing the `INSOLVENT` error, check if the contract has `WETH`, because maybe it's possible to send `WETH` instead of an throwing an error.

```javascript
    function _handleOutgoingTransfer(address _to, uint256 _amount) private {
        // Ensure the contract has enough ETH to transfer
        if (address(this).balance < _amount) revert INSOLVENT();

        // Used to store if the transfer succeeded
        bool success;

        assembly {
            // Transfer ETH to the recipient
            // Limit the call to 50,000 gas
            success := call(50000, _to, _amount, 0, 0, 0, 0)
        }

        // If the transfer failed:
        if (!success) {
            // Wrap as WETH
            IWETH(WETH).deposit{ value: _amount }();

            // Transfer WETH instead
            IWETH(WETH).transfer(_to, _amount);
        }
    }
```

**Affected source code:**

- [Auction.sol:346](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L346)

