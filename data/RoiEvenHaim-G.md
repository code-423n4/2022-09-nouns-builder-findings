# gas optimization 

### Don't Initialize Variables with Default Value

### Examples
```
2022-09-nouns-builder\src\governance\treasury\Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {
2022-09-nouns-builder\src\token\metadata\MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {
2022-09-nouns-builder\src\token\metadata\MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {
2022-09-nouns-builder\src\token\metadata\MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {
2022-09-nouns-builder\src\token\metadata\MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```

### Cache Array Length Outside of Loop

### Examples
```
2022-09-nouns-builder\src\governance\governor\Governor.sol::132 => uint256 numTargets = _targets.length;
2022-09-nouns-builder\src\governance\governor\Governor.sol::138 => if (numTargets != _values.length) revert PROPOSAL_LENGTH_MISMATCH();
2022-09-nouns-builder\src\governance\governor\Governor.sol::139 => if (numTargets != _calldatas.length) revert PROPOSAL_LENGTH_MISMATCH();
```

### Use != 0 instead of > 0 for Unsigned Integer Comparison

### Examples
```
2022-09-nouns-builder\src\lib\proxy\ERC1967Upgrade.sol::61 => if (_data.length > 0 || _forceCall) {
2022-09-nouns-builder\src\lib\token\ERC721Votes.sol::203 => if (_from != _to && _amount > 0) {
2022-09-nouns-builder\src\lib\utils\Address.sol::50 => if (_returndata.length > 0) {
```

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

### Examples
```
2022-09-nouns-builder\src\governance\governor\Governor.sol::27 => bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
2022-09-nouns-builder\src\governance\governor\Governor.sol::104 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
2022-09-nouns-builder\src\governance\governor\Governor.sol::142 => bytes32 descriptionHash = keccak256(bytes(_description));
2022-09-nouns-builder\src\governance\governor\Governor.sol::226 => digest = keccak256(
2022-09-nouns-builder\src\governance\governor\Governor.sol::230 => keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
2022-09-nouns-builder\src\governance\treasury\Treasury.sol::107 => return keccak256(abi.encode(_targets, _values, _calldatas, _descriptionHash));
2022-09-nouns-builder\src\lib\proxy\ERC1967Upgrade.sol::20 => /// @dev bytes32(uint256(keccak256('eip1967.proxy.rollback')) - 1)
```

### Use Shift Right/Left instead of Division/Multiplication if possible

### Examples
```
2022-09-nouns-builder\src\lib\token\ERC721Votes.sol::95 => middle = high - (high - low) / 2;
```
