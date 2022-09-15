1)It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {  
    
File: 2022-09-nouns-builder\src\governance\treasury\Treasury.sol
  162,26:             for (uint256 i = 0; i < numTargets; ++i) {
  
File: 2022-09-nouns-builder\src\token\metadata\MetadataRenderer.sol
  119,26:             for (uint256 i = 0; i < numNewProperties; ++i) {
  133,26:             for (uint256 i = 0; i < numNewItems; ++i) {
  189,26:             for (uint256 i = 0; i < numProperties; ++i) {
  229,26:             for (uint256 i = 0; i < numProperties; ++i) {  
  
2) ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops  
  
File: 2022-09-nouns-builder\src\governance\treasury\Treasury.sol
  162,26:             for (uint256 i = 0; i < numTargets; ++i) {
  
File: 2022-09-nouns-builder\src\token\metadata\MetadataRenderer.sol
  119,26:             for (uint256 i = 0; i < numNewProperties; ++i) {
  133,26:             for (uint256 i = 0; i < numNewItems; ++i) {
  189,26:             for (uint256 i = 0; i < numProperties; ++i) {
  229,26:             for (uint256 i = 0; i < numProperties; ++i) {  

File: 2022-09-nouns-builder\src\token\Token.sol
  80,13:             for (uint256 i; i < numFounders; ++i) {
  108,17:                 for (uint256 j; j < founderPct; ++j) {
  261,13:             for (uint256 i; i < numFounders; ++i) founders[i] = founder[i];    
  


3)STATE VARIABLES CAN BE PACKED INTO FEWER STORAGE SLOTS
If variables occupying the same slot are both written the same function or by the constructor,
avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper
  
File: 2022-09-nouns-builder\src\governance\governor\Governor.sol
  213,9:         uint8 _v,  
  
209         address _voter,
2010        bytes32 _proposalId,
2011        uint256 _support,
2012        uint256 _deadline,
2013        uint8 _v,
2014        bytes32 _r,
2015        bytes32 _s

Variable ordering with 6 slots instead of the current 7:
		uint8(1):_v,address(20):_voter,uint256(32):_support,uint256(32):_deadline, 
		bytes32(32):_r,bytes32(32):_s
		
		
		
177		   address voter,
178        bytes32 proposalId,
179        uint256 support,
180        uint256 deadline,
181        uint8 v,
182        bytes32 r,
183        bytes32 s

Variable ordering with 6 slots instead of the current 7:
		uint8(1):v,address(20):voter,uint256(32):support,uint256(32):deadline, 
		bytes32(32):r,bytes32(32):s
		
4)X = X + Y IS CHEAPER THAN X += Y 

File: 2022-09-nouns-builder\src\governance\governor\Governor.sol
  280,39:                 proposal.againstVotes += uint32(weight);
  285,35:                 proposal.forVotes += uint32(weight);
  290,39:                 proposal.abstainVotes += uint32(weight);

File: 2022-09-nouns-builder\src\token\Token.sol
  88,37:                 if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();
  118,34:                     (baseTokenId += schedule) % 100;  
  
File: 2022-09-nouns-builder\src\token\metadata\MetadataRenderer.sol
  140,33:                     _propertyId += numStoredProperties; 

5)Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code.
Savings are due to the compiler not having to create non-payable getter
functions for deployment calldata, and not adding another entry to the method ID table   

File: 2022-09-nouns-builder\src\governance\governor\Governor.sol
  27,13:     bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)"); 
  
  
6)++x is more efficient than x++
  
File: 2022-09-nouns-builder\src\lib\token\ERC721Votes.sol
  162,133:                 abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
  207,65:                     uint256 nCheckpoints = numCheckpoints[_from]++;
  222,63:                     uint256 nCheckpoints = numCheckpoints[_to]++;
  
File: 2022-09-nouns-builder\src\token\Token.sol
 
  91,57:                 uint256 founderId = settings.numFounders++;
 
  154,47:                 tokenId = settings.totalSupply++;
