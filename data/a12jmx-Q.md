It is best practice and also unnecessary to initialize for variables in:

Contract: Treasury.sol

	line 162

Recommendation:

	for (uint256 i; i < numTargets; ++i) {
	
Contract: MetadataRenderer.sol

	line 119
	line 133
	line 189
	line 229
	
Recommendation:

	for (uint256 i; i < numNewProperties; ++i) {
	for (uint256 i; i < numNewItems; ++i) {
	for (uint256 i; i < numProperties; ++i) {
	for (uint256 i; i < numProperties; ++i) {
	
This will also bring consistency throughout all contracts, especially given that contract Token.sol already has this implemented in lines 80, 108, and 261.