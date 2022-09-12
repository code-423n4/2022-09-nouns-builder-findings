# PREFIX INCREMENTS


IMPACT
    Prefix increments are cheaper than postfix increments.

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L91
    uint256 founderId = settings.numFounders++;


Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L230
    keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))


Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L207
    uint256 nCheckpoints = numCheckpoints[_from]++;

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L222
    uint256 nCheckpoints = numCheckpoints[_to]++;

    
Mitigation:
    replace foo++ to ++foo





# DEFAULT VALUE INITIALIZATION

IMPACT
    If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.


Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L119
    for (uint256 i = 0; i < numNewProperties; ++i) {
    
Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L133
    for (uint256 i = 0; i < numNewItems; ++i) {
    
Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L189
    for (uint256 i = 0; i < numProperties; ++i) {
    
Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L229
    for (uint256 i = 0; i < numProperties; ++i) {

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L162
    for (uint256 i = 0; i < numTargets; ++i) {

Mitigation:
    Remove explicit value initialization.





# COMPARISON OPERATORS

IMPACT
    In the EVM, there is no opcode for >= or <=. When using greater than or equal, two operations are performed: > and =.
    Using strict comparison operators hence saves gas.
    

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L197
    seed >>= 16;

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/auction/Auction.sol#L98
    if (block.timestamp >= _auction.endTime) revert AUCTION_OVER();

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/treasury/Treasury.sol#L89
    return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L61
    if (_timestamp >= block.timestamp) revert INVALID_TIMESTAMP();

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L78
    if (accountCheckpoints[lastCheckpoint].timestamp <= _timestamp) return accountCheckpoints[lastCheckpoint].votes;

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Initializable.sol#L56
    if (_initializing || _initialized >= _version) revert ALREADY_INITIALIZED();


Mitigation:
    Replace <= with <, and >= with >. Do not forget to increment/decrement the compared variable



# COMPARISON WITH ZERO

IMPACT
    >0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes [here](https://twitter.com/gzeon/status/1485428085885640706)


Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/token/ERC721Votes.sol#L203
    if (_from != _to && _amount > 0) {

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/proxy/ERC1967Upgrade.sol#L61
    if (_data.length > 0 || _forceCall) {

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/lib/utils/Address.sol#L50
    if (_returndata.length > 0) {


Mitigation:
    Replace >0 with !0




# Increment/decrement operations

IMPACT
    X = X + Y IS CHEAPER THAN X += Y
    X = X - Y IS CHEAPER THAN X -= Y


Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L88
    if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/Token.sol#L118
    (baseTokenId += schedule) % 100;

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/token/metadata/MetadataRenderer.sol#L140
    _propertyId += numStoredProperties;

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L280
    proposal.againstVotes += uint32(weight);

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L285
    proposal.forVotes += uint32(weight);

Instance:
https://github.com/code-423n4/2022-09-nouns-builder/tree/main/src/governance/governor/Governor.sol#L290
    proposal.abstainVotes += uint32(weight);


Mitigation:
    X += Y replace with X = X + Y
    X -= Y replace with X = X - Y
                