# Gas Optimization Report

## Issues found

### [GO01] Don't Initialize Variables with Default Value

#### Impact
Uninitialized variables are assigned with the types default value.
Explicitly initializing a variable with it's default value costs unnecessary gas.

#### Findings:
```sol
governance/treasury/Treasury.sol::162 => for (uint256 i = 0; i < numTargets; ++i) {
token/metadata/MetadataRenderer.sol::119 => for (uint256 i = 0; i < numNewProperties; ++i) {
token/metadata/MetadataRenderer.sol::133 => for (uint256 i = 0; i < numNewItems; ++i) {
token/metadata/MetadataRenderer.sol::189 => for (uint256 i = 0; i < numProperties; ++i) {
token/metadata/MetadataRenderer.sol::229 => for (uint256 i = 0; i < numProperties; ++i) {
```


### [GO02] Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

#### Findings:
```sol
lib/proxy/ERC1967Upgrade.sol::61 => if (_data.length > 0 || _forceCall) {
lib/token/ERC721Votes.sol::203 => if (_from != _to && _amount > 0) {
lib/utils/Address.sol::50 => if (_returndata.length > 0) {
```

