	Findings: Use assembly when getting a contract’s balance of eth
	
	Code:  src/auction/Auction.sol - line 346
	
	Explanation Non-critical gas optimisation. You can use selfbalance() instead of address(this).balance when getting your contract’s balance of ETH to save gas.
	
	----
	
	Findings: Using private rather than public for constants, saves gas
	
	Code: src/governance/governor/Governor.sol - line 27
	
	Explanation: If needed, the value can be read from the verified contract source code.
	
	----
	
	Findings: It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied.
	
	Code: 
	src/governance/treasury/Treasure.sol - line 162
	src/token/metadata/MetadataRenderer.sol - line 119, line 136, line 193, line 234
	
	
	Explanation: 
	
	  for (uint256 i = 0; i < numTargets; ++i) {
	
	Use instead
	
	  for (uint256 i; i < numTargets; ++i) {
	
	
	----
	
	Findings: Empty blocks should be removed or emit something (external receive & fallback)
	
	Code:  src/governance/treasury/Treasure.sol - line 269
	
	Explanation: The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
	
	Mitigation: Either remove, or implement an emit event.
	
	----
	
	Findings: Multiplication/division by two should use bit shifting
	Code: src/lib/token/ERC721Votes.sol
	
	Explanation: 
	<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas. DIVISION BY TWO SHOULD USE BIT SHIFTING. Given this is an openzeppelin library modification is at the developers discretion.
	
	Mitigation: Use Bit Shifting.
	
	
