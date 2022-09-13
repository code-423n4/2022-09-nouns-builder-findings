## [NAZ-L1] Signature Malleability of EVM's `ecrecover()`
**Severity**: Low
**Context**: [`Governor.sol#L236`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L236), [`ERC721Votes.sol#L167`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L167)

**Description**:
The function calls the Solidity `ecrecover()` function directly to verify the given signatures. However, the `ecrecover()` EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible here since the nonce is increased each time, ensuring the signatures are not malleable is considered a best practice.

**Recommendation**:
Use the ecrecover function from [OpenZeppelin's ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol) for signature verification. (Ensure using a version `> 4.7.3` for there was a critical bug `>= 4.1.0 < 4.7.3`).


## [NAZ-L2] Value Range Validity 
**Severity** Low
**Context**: [`Auction.sol#L77-L78`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L77-L78), [`Auction.sol#L307-L335`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307-L335), [`Governor.sol#L61-L64`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L61-L64), [`Governor.sol#L564-L592`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L564-L592), [`Governor.sol#L211-L231`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L211-L231)

**Description**:
These functions doesn't have any checks to ensure that the variables being set is within some kind of value range.

**Recommendation**:
Each variable input parameter updated should have it's own value range checks to ensure their validity.


## [NAZ-L3] Missing Equivalence Checks In Setters
**Severity**: Low
**Context**: [`MetadataRenderer.sol#L347-L367`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L347-L367), [`Auction.sol#L307-L335`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307-L335), [`Governor.sol#L564-L602`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L564-L602), [`Governor.sol#L211-L231`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L211-L231)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L4] Missing Zero-address Validation
**Severity**: Low
**Context**: [`Manager.sol#L62-L66`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L62-L66), [`Token.sol#L46-L47`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L46-L47), [`MetadataRenderer.sol#L32`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L32), [`MetadataRenderer.sol#L47-L49`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L47-L49), [`Auction.sol#L39`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L39), [`Auction.sol#L55-L57`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L55-L57), [`Governor.sol#L60`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L60), [`Treasury.sol#L33`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L33)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L5] `receive()` Function Should Emit An Event
**Severity**: Low
**Context**: [`Treasury.sol#L269`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269)

**Description**:
Consider emitting an event inside this function with `msg.sender` and `msg.value` as the parameters. This would make it easier to track incoming ether transfers.

**Recommendation**:
Consider adding events to the `receive()` functions. 


## [NAZ-L6] Missing Events In Initialize Functions
**Severity**: Low
**Context**: [`Manager.sol#L80`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L80), [`Token.sol#L43`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L43), [`MetadataRenderer.sol#L45`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L45), [`Auction.sol#L54`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L54), [`Governor.sol#L57`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L57), [`Treasury.sol#L43`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L43)

**Description**:
None of the initialize functions emit init-specific events. They all however have the initializer modifier (from Initializable) so that they can be called only once. Off-chain monitoring of calls to these critical functions is not possible.

**Recommendation**:
It is recommended to emit events in your initialization functions.


## [NAZ-N1] Array length mismatch
**Severity**: Informational
**Context**: [`MetadataRenderer.sol#L92-L93`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L92-L93), [`Governor.sol#L99-L101`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L99-L101), [`Governor.sol#L117-L119`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L117-L119), [`Governor.sol#L325-L327`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L325-L327), [`Treasury.sol#L102-L104`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L102-L104), [`Treasury.sol#L142-L144`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L142-L144)

**Description**:
These fail to perform input validation on arrays to verify the lengths match. A mismatch could lead to an exception or undefined behavior.

**Recommendation**:
Perform input validation on the arrays to verify that the lengths match.


## [NAZ-N2] Unindexed Event Parameters
**Severity** Informational
**Context**: [`IManager.sol#L21`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L21), [`IManager.sol#L26`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L26), [`IManager.sol#L31`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L31), [`IToken.sol#L21`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol#L21), [`IPropertyIPFSMetadataRenderer.sol#L16-L28`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L16-L28), [`IAuction.sol#L22-L50`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L22-L50), [`IGovernor.sol#L29-L57`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L29-L57), [`ITreasury.sol#L16-L28`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L16-L28)

**Description**:
Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

**Recommendation**:
Consider adding the indexed keyword to event parameters that should include it.


## [NAZ-N3] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`Auction.sol#L354`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L354)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N4] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`Manager.sol#L180`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L180), [`Token.sol#L143`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L143), [`MetadataRenderer.sol#L91`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L91), [`Auction.sol#L244`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L244), [`Governor.sol#L116`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L116), [`Treasury.sol#L116`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L116), [`ERC1967Upgrade.sol#L83`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L83), [`UUPS.sol#L47`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L47), [`ERC721.sol#L54`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L54), [`ERC721Votes.sol#L126`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L126)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N5] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`Manager.sol#L209`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209), [`Treasury.sol#L269`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269), [`ERC721.sol#L57`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L57), [`ERC721.sol#L239`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L239), [`ERC721.sol#L249`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L249)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N6] Line Length
**Severity**: Informational
**Context**: [`Manager.sol#L113`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L113), [`Manager.sol#L167-L170`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L167-L170), [`IManager.sol#L55`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L55), [`Token.sol#L268`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L268), [`IToken.sol#L86`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol#L86), [`MetadataRenderer.sol#L55`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L55), [`MetadataRenderer.sol#L206`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L206), [`MetadataRenderer.sol#L243`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L243), [`MetadataRenderer.sol#L259`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L259), [`IPropertyIPFSMetadataRenderer.sol#L63`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L693), [`Governor.sol#L27`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27), [`Governor.sol#L362-L363`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L362-L363), [`TreasuryStorageV1.sol#L11`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/storage/TreasuryStorageV1.sol#L11), [`TreasuryStorageV1.sol#L15`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/storage/TreasuryStorageV1.sol#L15), [`ERC721.sol#L134`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L134), [`ERC721.sol#L166`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L166), [`ERC721.sol#L184`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L184), [`ERC721Votes.sol#L10`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L10), [`ERC721Votes.sol#L21`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21), [`ERC721Votes.sol#L162`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162)

**Description**:
Max line length must be no more than 120 but many lines are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N7] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`Manager.sol#L40`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L40), [`Manager.sol#L43`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L43), [`Manager.sol#L49`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L49), [`ManagerStorageV1.sol#L10`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/storage/ManagerStorageV1.sol#L10), [`TokenStorageV1.sol#L11`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/storage/TokenStorageV1.sol#L11), [`TokenStorageV1.sol#L15`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/storage/TokenStorageV1.sol#L15), [`TokenStorageV1.sol#L19`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/storage/TokenStorageV1.sol#L19), [`MetadataRenderer.sol#L25`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L25), [`MetadataRendererStorageV1.sol#L11`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#L11), [`MetadataRendererStorageV1.sol#L14`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#L14), [`MetadataRendererStorageV1.sol#L17`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#L17), [`MetadataRendererStorageV1.sol#L20`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/storage/MetadataRendererStorageV1.sol#L20), [`Auction.sol#L28`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L28), [`Auction.sol#L31`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L31), [`AuctionStorageV1.sol#L12`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/storage/AuctionStorageV1.sol#L12), [`Governor.sol#L34`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L34), [`GovernorStorageV1.sol#L11`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/storage/GovernorStorageV1.sol#L11), [`GovernorStorageV1.sol#L15`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/storage/GovernorStorageV1.sol#L15), [`GovernorStorageV1.sol#L19`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/storage/GovernorStorageV1.sol#L19), [`Treasury.sol#L25`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L25), [`ERC721.sol#L26`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L26), [`ERC721.sol#L30`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L30), [`ERC721.sol#L38`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L38), [`ERC721.sol#L47`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L47), [`ERC721Votes.sol#L29`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L29), [`ERC721Votes.sol#L33`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L33), [`ERC721Votes.sol#L37`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L37), [`ERC721Votes.sol#L78`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L78)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html).


## [NAZ-N8] Spelling Errors
**Severity**: Informational
**Context**: [`Manager.sol#L113 (responsiblity=> responsibility)`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L113), [`Token.sol#L104 (receive => receive)`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L104), [`TokenTypesV1.sol#L13 (metadatarenderer => metadataRenderer)`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/types/TokenTypesV1.sol#L13), [`IGovernor.sol#L74 (calldatas => calldata)`](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L74)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected.


## [NAZ-N9] Multiple Solidity Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src)

**Description**:
It is better to use one Solidity compiler version across all contracts instead of different versions with different bugs and security checks. 

**Recommendation**: 
Consider ensuring all pragma versions are the same one.


## [NAZ-N10] Floating Pragma
**Severity**: Informational
**Context**: [`./proxy`](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy)

**Description**:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

**Recommendation**: 
Consider locking the pragma version.


## [NAZ-N11] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.