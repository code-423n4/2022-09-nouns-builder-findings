##### Gas Optimizations

Gas savings are estimated using the gas report of existing `forge test --gas-report` tests (the sum of all deployment costs and the sum of the costs of calling all methods) and may vary depending on the implementation of the fix. I keep my version of the fix for each finding and can provide them if you need them.
In this project, the optimizer is set on 500000 runs, so some of the common optimizations are useless or partially useless. Only cases that save gas with this configuration of optimizer are included in the report.

|       | Issue                                                                                                                              | Instances | Estimated gas(deployments) | Estimated gas(method call) |
| :---: | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: | :------------------------: | :------------------------: |
| **1** | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |     5     |          168 820           |           1 672            |
| **2** | SafeCast is not needed when using Solidity version 0.8+                                                                            |     -     |          121 350           |             52             |
| **3** | State variables can be packed into fewer storage slots                                                                             |     1     |           99 511           |             -              |
| **4** | State variables should be cached in stack variables rather than re-reading them from storage                                       |     5     |           70 505           |           2 634            |
| **5** | Using bools for storage incurs overhead                                                                                            |     5     |           60 668           |           1 191            |
| **6** | Storage variable is used when local exists                                                                                         |     2     |           1 400            |           2 602            |
| **7** | Use named returns where appropriate                                                                                                |     3     |           2 000            |            174             |
| **-** | Overall Gas Saved                                                                                                                  |    21     |          460 774           |           6 547            |

**Total: 21 instances over 7 issues**

---

1. **`storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` (5 instances)**

   Deployment Gas Saved: **168 820**
   Method Call Gas Saved: **1 672**
   `forge snapshot --diff`: **8 746** Gas Saved

   It may not be obvious, but every time you copy a storage `struct`/`array`/`mapping` to a `memory` variable, you are copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.
   Exception: case when you need to read all or many members multiple times. In report included only cases that saved gas

   - src/auction/Auction.sol

   ```diff
   diff --git a/src/auction/Auction.sol b/src/auction/Auction.sol
   index 794da99..92854f6 100644
   --- a/src/auction/Auction.sol
   +++ b/src/auction/Auction.sol
   @@ -89,7 +89,7 @@ contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionS
      89,  89:     /// @param _tokenId The ERC-721 token id
      90,  90:     function createBid(uint256 _tokenId) external payable nonReentrant {
      91,  91:         // Get a copy of the current auction
   -  92     :-        Auction memory _auction = auction;
   +       92:+        Auction storage _auction = auction;
      93,  93:
      94,  94:         // Ensure the bid is for the current token
      95,  95:         if (_auction.tokenId != _tokenId) revert INVALID_TOKEN_ID();
   ```

   - src/governance/governor/Governor.sol

   ```diff
   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index 0d963c5..ca5600a 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -355,7 +355,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
     355, 355:         if (state(_proposalId) == ProposalState.Executed) revert PROPOSAL_ALREADY_EXECUTED();
     356, 356:
     357, 357:         // Get a copy of the proposal
   - 358     :-        Proposal memory proposal = proposals[_proposalId];
   +      358:+        Proposal storage proposal = proposals[_proposalId];
     359, 359:
     360, 360:         // Cannot realistically underflow and `getVotes` would revert
     361, 361:         unchecked {

   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index ca5600a..67db726 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -505,7 +505,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
     505, 505:             uint256
     506, 506:         )
     507, 507:     {
   - 508     :-        Proposal memory proposal = proposals[_proposalId];
   +      508:+        Proposal storage proposal = proposals[_proposalId];
     509, 509:
     510, 510:         return (proposal.againstVotes, proposal.forVotes, proposal.abstainVotes);
     511, 511:     }
   ```

   - src/lib/token/ERC721Votes.sol

   ```diff
   diff --git a/src/lib/token/ERC721Votes.sol b/src/lib/token/ERC721Votes.sol
   index 3e64720..c5759cd 100644
   --- a/src/lib/token/ERC721Votes.sol
   +++ b/src/lib/token/ERC721Votes.sol
   @@ -87,7 +87,7 @@ abstract contract ERC721Votes is IERC721Votes, EIP712, ERC721 {
      87,  87:             uint256 middle;
      88,  88:
      89,  89:             // Used to temporarily hold a checkpoint
   -  90     :-            Checkpoint memory cp;
   +       90:+            Checkpoint storage cp;
      91,  91:
      92,  92:             // While a valid checkpoint is to be found:
      93,  93:             while (high > low) {
   ```

   - src/token/metadata/MetadataRenderer.sol

   ```diff
   diff --git a/src/token/metadata/MetadataRenderer.sol b/src/token/metadata/MetadataRenderer.sol
   index 7a140ec..070da5f 100644
   --- a/src/token/metadata/MetadataRenderer.sol
   +++ b/src/token/metadata/MetadataRenderer.sol
   @@ -231,7 +231,7 @@ contract MetadataRenderer is IPropertyIPFSMetadataRenderer, UUPS, Ownable, Metad
     231, 231:                 bool isLast = i == lastProperty;
     232, 232:
     233, 233:                 // Get a copy of the property
   - 234     :-                Property memory property = properties[i];
   +      234:+                Property storage property = properties[i];
     235, 235:
     236, 236:                 // Get the token's generated attribute
     237, 237:                 uint256 attribute = tokenAttributes[i + 1];
   ```

   **Controversial (Not included in estimation):**

   Saved in deploy: 25433, but lost 100-300 gas per user

   - src/governance/governor/Governor.sol

   ```diff
   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index ca5600a..b47413f 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -412,7 +412,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
     412, 412:     /// @param _proposalId The proposal id
     413, 413:     function state(bytes32 _proposalId) public view returns (ProposalState) {
     414, 414:         // Get a copy of the proposal
   - 415     :-        Proposal memory proposal = proposals[_proposalId];
   +      415:+        Proposal storage proposal = proposals[_proposalId];
     416, 416:
     417, 417:         // Ensure the proposal exists
     418, 418:         if (proposal.voteStart == 0) revert PROPOSAL_DOES_NOT_EXIST();
   ```

2. **SafeCast is not needed when using Solidity version >= 0.8.0**

   Solidity version 0.8+ already implements overflow and underflow checks by default.
   Using the SafeCast library is therefore redundant. Consider using the built-in checks instead of SafeMath and remove SafeMath

   Deployment Gas Saved: **121 350**
   Method Call Gas Saved: **-52**
   `forge snapshot --diff`: **-938**

3. **State variables can be packed into fewer storage slots (1 instances)**

   Deployment Gas Saved: **99 511**
   `forge snapshot --diff`: **1 080**

   - src/auction/Auction.sol

   Storage:

   ```solidity
      /*external inheritance:*/
   address internal _owner;       // 20
   address internal _pendingOwner // 20
   uint256 internal _status;      // 32
   uint8 internal _initialized;   // 1
   bool internal _initializing;   // 1
   bool internal _paused;         // 1
      /* self storage */
   Settings internal settings;
   /*
   address treasury;              // 20
   uint40 duration;               // 5
   uint40 timeBuffer;             // 5
   uint8 minBidIncrement;         // 1
   uint256 reservePrice;          // 32
   */
   Token public token;            // 20
   Auction public auction;
   /*
   uint256 tokenId;               // 32
   uint256 highestBid;            // 32
   address highestBidder;         // 20
   uint40 startTime;              // 5
   uint40 endTime;                // 5
   bool settled;                  // 1
   */
   ```

   Fix:

   ```diff
   diff --git a/src/auction/types/AuctionTypesV1.sol b/src/auction/types/AuctionTypesV1.sol
   index ae90c6c..8fb4241 100644
   --- a/src/auction/types/AuctionTypesV1.sol
   +++ b/src/auction/types/AuctionTypesV1.sol
   @@ -12,10 +12,10 @@ contract AuctionTypesV1 {
      12,  12:     /// @param minBidIncrement The minimum percentage an incoming bid must raise the highest bid
      13,  13:     /// @param reservePrice The reserve price of each auction
      14,  14:     struct Settings {
   -  15     :-        address treasury;
   +       15:+        uint8 minBidIncrement;
      16,  16:         uint40 duration;
      17,  17:         uint40 timeBuffer;
   -  18     :-        uint8 minBidIncrement;
   +       18:+        address treasury;
      19,  19:         uint256 reservePrice;
      20,  20:     }
      21,  21:
   @@ -27,11 +27,11 @@ contract AuctionTypesV1 {
      27,  27:     /// @param endTime The timestamp the auction ends
      28,  28:     /// @param settled If the auction has been settled
      29,  29:     struct Auction {
   -  30     :-        uint256 tokenId;
   -  31     :-        uint256 highestBid;
   -  32     :-        address highestBidder;
      33,  30:         uint40 startTime;
      34,  31:         uint40 endTime;
      35,  32:         bool settled;
   +       33:+        address highestBidder;
   +       34:+        uint256 tokenId;
   +       35:+        uint256 highestBid;
      36,  36:     }
      37,  37: }
   diff --git a/test/Auction.t.sol b/test/Auction.t.sol
   index b664078..bf2c2e5 100644
   --- a/test/Auction.t.sol
   +++ b/test/Auction.t.sol
   @@ -44,7 +44,7 @@ contract AuctionTest is NounsBuilderTest {
      44,  44:         assertEq(token.ownerOf(1), founder2);
      45,  45:         assertEq(token.ownerOf(2), address(auction));
      46,  46:
   -  47     :-        (uint256 tokenId, uint256 highestBid, address highestBidder, uint256 startTime, uint256 endTime, bool settled) = auction.auction();
   +       47:+        (uint256 startTime, uint256 endTime, bool settled, address highestBidder, uint256 tokenId, uint256 highestBid) = auction.auction();
      48,  48:
      49,  49:         assertEq(tokenId, 2);
      50,  50:         assertEq(highestBid, 0);
   @@ -71,7 +71,7 @@ contract AuctionTest is NounsBuilderTest {
      71,  71:         vm.prank(bidder1);
      72,  72:         auction.createBid{ value: _amount }(2);
      73,  73:
   -  74     :-        (, uint256 highestBid, address highestBidder, , , ) = auction.auction();
   +       74:+        (, , , address highestBidder, , uint256 highestBid) = auction.auction();
      75,  75:
      76,  76:         assertEq(highestBid, _amount);
      77,  77:         assertEq(highestBidder, bidder1);
   @@ -123,7 +123,7 @@ contract AuctionTest is NounsBuilderTest {
   123, 123:         assertEq(bidder2BeforeBalance - bidder2AfterBalance, 0.5 ether);
   124, 124:         assertEq(address(auction).balance, 0.5 ether);
   125, 125:
   - 126     :-        (, uint256 highestBid, address highestBidder, , , ) = auction.auction();
   +      126:+       (, , , address highestBidder, , uint256 highestBid) = auction.auction();
   127, 127:
   128, 128:         assertEq(highestBid, 0.5 ether);
   129, 129:         assertEq(highestBidder, bidder2);
   @@ -155,7 +155,7 @@ contract AuctionTest is NounsBuilderTest {
   155, 155:         vm.prank(bidder2);
   156, 156:         auction.createBid{ value: 1 ether }(2);
   157, 157:
   - 158     :-        (, , , , uint256 endTime, ) = auction.auction();
   +      158:+        (, uint256 endTime, , ,, ) = auction.auction();
   159, 159:
   160, 160:         assertEq(endTime, 14 minutes);
   161, 161:     }
   @@ -237,7 +237,7 @@ contract AuctionTest is NounsBuilderTest {
   237, 237:
   238, 238:         auction.settleAuction();
   239, 239:
   - 240     :-        (, , , , , bool settled) = auction.auction();
   +      240:+        (, , bool settled, , , ) = auction.auction();
   241, 241:
   242, 242:         assertEq(settled, true);
   243, 243:     }
   diff --git a/test/utils/NounsBuilderTest.sol b/test/utils/NounsBuilderTest.sol
   index cb17d6b..ccabc62 100644
   --- a/test/utils/NounsBuilderTest.sol
   +++ b/test/utils/NounsBuilderTest.sol
   @@ -240,7 +240,7 @@ contract NounsBuilderTest is Test {
   240, 240:
   241, 241:         unchecked {
   242, 242:             for (uint256 i; i < _numTokens; ++i) {
   - 243     :-                (uint256 tokenId, , , , , ) = auction.auction();
   +      243:+                (, , , , uint256 tokenId, ) = auction.auction();
   244, 244:
   245, 245:                 vm.prank(otherUsers[i]);
   246, 246:                 auction.createBid{ value: reservePrice }(tokenId);
   ```

4. **State variables should be cached in stack variables rather than re-reading them from storage (5 instances)**

   Deployment Gas Saved: **70 505**
   Method Call Gas Saved: **2 634**
   `forge snapshot --diff`: **12 481** Gas Saved

   Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs or having local caches of state variable contracts/addresses.

   SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

   - src/auction/Auction.sol

   function: `_settleAuction`: `auction` cached in 169 but readed from storage in 172
   function: `_handleOutgoingTransfer`: `IWETH(WETH)` can be cached

   ```diff
   diff --git a/src/auction/Auction.sol b/src/auction/Auction.sol
   index 794da99..3ef53f5 100644
   --- a/src/auction/Auction.sol
   +++ b/src/auction/Auction.sol
   @@ -356,11 +356,13 @@ contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionS
   356, 356:
   357, 357:         // If the transfer failed:
   358, 358:         if (!success) {
   +      359:+            IWETH iweth = IWETH(WETH);
   +      360:+
   359, 361:             // Wrap as WETH
   - 360     :-            IWETH(WETH).deposit{ value: _amount }();
   +      362:+            iweth.deposit{ value: _amount }();
   361, 363:
   362, 364:             // Transfer WETH instead
   - 363     :-            IWETH(WETH).transfer(_to, _amount);
   +      365:+            iweth.transfer(_to, _amount);
   364, 366:         }
   365, 367:     }
   366, 368:
   ```

   - src/governance/governor/Governor.sol

   function proposalVotes: There is no need to copy `proposals[_proposalId]` to memory, because you reading every field exactly one time

   ```diff
   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index 0d963c5..c8fb215 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -505,9 +505,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   505, 505:             uint256
   506, 506:         )
   507, 507:     {
   - 508     :-        Proposal memory proposal = proposals[_proposalId];
   - 509     :-
   - 510     :-        return (proposal.againstVotes, proposal.forVotes, proposal.abstainVotes);
   +      508:+        return (proposals[_proposalId].againstVotes, proposals[_proposalId].forVotes, proposals[_proposalId].abstainVotes);
   511, 509:     }
   512, 510:
   513, 511:     /// @notice The timestamp valid to execute a proposal
   ```

   - src/governance/treasury/Treasury.sol

   function `isReady`: `timestamps[_proposalId]` can be cached

   ```diff
   diff --git a/src/governance/treasury/Treasury.sol b/src/governance/treasury/Treasury.sol
   index b78bc8c..ce94e3b 100644
   --- a/src/governance/treasury/Treasury.sol
   +++ b/src/governance/treasury/Treasury.sol
   @@ -86,7 +86,8 @@ contract Treasury is ITreasury, UUPS, Ownable, TreasuryStorageV1 {
   86,  86:     /// @notice If a proposal is ready to execute (does not consider expiration)
   87,  87:     /// @param _proposalId The proposal id
   88,  88:     function isReady(bytes32 _proposalId) public view returns (bool) {
   -  89     :-        return timestamps[_proposalId] != 0 && block.timestamp >= timestamps[_proposalId];
   +       89:+        uint256 timestamp = timestamps[_proposalId];
   +       90:+        return timestamp != 0 && block.timestamp >= timestamp;
   90,  91:     }
   91,  92:
   92,  93:     ///                                                          ///
   ```

   - src/token/Token.sol

   function `_isForFounder`: use storage pointer to founder

   ```diff
   diff --git a/src/token/Token.sol b/src/token/Token.sol
   index afad142..ef92fe6 100644
   --- a/src/token/Token.sol
   +++ b/src/token/Token.sol
   @@ -178,14 +178,16 @@ contract Token is IToken, UUPS, ReentrancyGuard, ERC721Votes, TokenStorageV1 {
   178, 178:         // Get the base token id
   179, 179:         uint256 baseTokenId = _tokenId % 100;
   180, 180:
   +      181:+        Founder storage _founder = tokenRecipient[baseTokenId];
   +      182:+
   181, 183:         // If there is no scheduled recipient:
   - 182     :-        if (tokenRecipient[baseTokenId].wallet == address(0)) {
   +      184:+        if (_founder.wallet == address(0)) {
   183, 185:             return false;
   184, 186:
   185, 187:             // Else if the founder is still vesting:
   - 186     :-        } else if (block.timestamp < tokenRecipient[baseTokenId].vestExpiry) {
   +      188:+        } else if (block.timestamp < _founder.vestExpiry) {
   187, 189:             // Mint the token to the founder
   - 188     :-            _mint(tokenRecipient[baseTokenId].wallet, _tokenId);
   +      190:+            _mint(_founder.wallet, _tokenId);
   189, 191:
   190, 192:             return true;
   191, 193:
   ```

   - src/token/metadata/MetadataRenderer.sol

   function `_getItemImage`: complex expression can be cached as storage pointer

   ```diff
   diff --git a/src/token/metadata/MetadataRenderer.sol b/src/token/metadata/MetadataRenderer.sol
   index 7a140ec..6640988 100644
   --- a/src/token/metadata/MetadataRenderer.sol
   +++ b/src/token/metadata/MetadataRenderer.sol
   @@ -253,10 +253,11 @@ contract MetadataRenderer is IPropertyIPFSMetadataRenderer, UUPS, Ownable, Metad
   253, 253:
   254, 254:     /// @dev Encodes the reference URI of an item
   255, 255:     function _getItemImage(Item memory _item, string memory _propertyName) private view returns (string memory) {
   +      256:+        IPFSGroup storage _ipfsData = ipfsData[_item.referenceSlot];
   256, 257:         return
   257, 258:             UriEncode.uriEncode(
   258, 259:                 string(
   - 259     :-                    abi.encodePacked(ipfsData[_item.referenceSlot].baseUri, _propertyName, "/", _item.name, ipfsData[_item.referenceSlot].extension)
   +      260:+                    abi.encodePacked(_ipfsData.baseUri, _propertyName, "/", _item.name, _ipfsData.extension)
   260, 261:                 )
   261, 262:             );
   262, 263:     }
   ```

5. **Using bools for storage incurs overhead (5 instances)**

   Deployment Gas Saved: **60 668**
   Avg. Method Call Gas Saved: **1 191**
   `forge snapshot --diff`: **9 748** Gas Saved

   ```
   // Booleans are more expensive than uint256 or any type that takes up a full
   // word because each write operation emits an extra SLOAD to first read the
   // slot's contents, replace the bits taken up by the boolean, and then write
   // back. This is the compiler's defense against contract upgrades and
   // pointer aliasing, and it cannot be disabled.
   ```

   Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

   NOTE: in some cases, usually in structs, this optimization can cause significant user gas loss, these cases are intentionally excluded from the report

   - src/governance/governor/Governor.sol

   ```diff
   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index 0d963c5..62506ae 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -255,13 +255,13 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
     255, 255:         if (state(_proposalId) != ProposalState.Active) revert VOTING_NOT_STARTED();
     256, 256:
     257, 257:         // Ensure the voter hasn't already voted
   - 258     :-        if (hasVoted[_proposalId][_voter]) revert ALREADY_VOTED();
   +      258:+        if (1==hasVoted[_proposalId][_voter]) revert ALREADY_VOTED();
     259, 259:
     260, 260:         // Ensure the vote is valid
     261, 261:         if (_support > 2) revert INVALID_VOTE();
     262, 262:
     263, 263:         // Record the voter as having voted
   - 264     :-        hasVoted[_proposalId][_voter] = true;
   +      264:+        hasVoted[_proposalId][_voter] = 1;
     265, 265:
     266, 266:         // Get the pointer to the proposal
     267, 267:         Proposal storage proposal = proposals[_proposalId];
   ```

   - src/governance/governor/storage/GovernorStorageV1.sol

   ```diff
   diff --git a/src/governance/governor/storage/GovernorStorageV1.sol b/src/governance/governor/storage/GovernorStorageV1.sol
   index beff22e..25d7ff2 100644
   --- a/src/governance/governor/storage/GovernorStorageV1.sol
   +++ b/src/governance/governor/storage/GovernorStorageV1.sol
   @@ -16,5 +16,5 @@ contract GovernorStorageV1 is GovernorTypesV1 {
      16,  16:
      17,  17:     /// @notice If a user has voted on a proposal
      18,  18:     /// @dev Proposal Id => User => Has Voted
   -  19     :-    mapping(bytes32 => mapping(address => bool)) internal hasVoted;
   +       19:+    mapping(bytes32 => mapping(address => uint256)) internal hasVoted;
      20,  20: }
   ```

   - src/lib/token/ERC721.sol

   ```diff
   diff --git a/src/lib/token/ERC721.sol b/src/lib/token/ERC721.sol
   index 36a4bed..5808613 100644
   --- a/src/lib/token/ERC721.sol
   +++ b/src/lib/token/ERC721.sol
   @@ -35,7 +35,7 @@ abstract contract ERC721 is IERC721, Initializable {
      35,  35:
      36,  36:     /// @notice The balance approvals
      37,  37:     /// @dev Owner => Operator => Approved
   -  38     :-    mapping(address => mapping(address => bool)) internal operatorApprovals;
   +       38:+    mapping(address => mapping(address => uint256)) internal operatorApprovals;
      39,  39:
      40,  40:     ///                                                          ///
      41,  41:     ///                           FUNCTIONS                      ///
   @@ -75,7 +75,7 @@ abstract contract ERC721 is IERC721, Initializable {
      75,  75:     /// @param _owner The owner address
      76,  76:     /// @param _operator The operator address
      77,  77:     function isApprovedForAll(address _owner, address _operator) external view returns (bool) {
   -  78     :-        return operatorApprovals[_owner][_operator];
   +       78:+        return 1==operatorApprovals[_owner][_operator];
      79,  79:     }
      80,  80:
      81,  81:     /// @notice The number of tokens owned
   @@ -102,7 +102,7 @@ abstract contract ERC721 is IERC721, Initializable {
     102, 102:     function approve(address _to, uint256 _tokenId) external {
     103, 103:         address owner = owners[_tokenId];
     104, 104:
   - 105     :-        if (msg.sender != owner && !operatorApprovals[owner][msg.sender]) revert INVALID_APPROVAL();
   +      105:+        if (msg.sender != owner && 0==operatorApprovals[owner][msg.sender]) revert INVALID_APPROVAL();
     106, 106:
     107, 107:         tokenApprovals[_tokenId] = _to;
     108, 108:
   @@ -113,7 +113,7 @@ abstract contract ERC721 is IERC721, Initializable {
     113, 113:     /// @param _operator The account address
     114, 114:     /// @param _approved If permission is being given or removed
     115, 115:     function setApprovalForAll(address _operator, bool _approved) external {
   - 116     :-        operatorApprovals[msg.sender][_operator] = _approved;
   +      116:+        operatorApprovals[msg.sender][_operator] = _approved ? 1 : 0;
     117, 117:
     118, 118:         emit ApprovalForAll(msg.sender, _operator, _approved);
     119, 119:     }
   @@ -131,7 +131,7 @@ abstract contract ERC721 is IERC721, Initializable {
     131, 131:
     132, 132:         if (_to == address(0)) revert ADDRESS_ZERO();
     133, 133:
   - 134     :-        if (msg.sender != _from && !operatorApprovals[_from][msg.sender] && msg.sender != tokenApprovals[_tokenId]) revert INVALID_APPROVAL();
   +      134:+        if (msg.sender != _from && 0==operatorApprovals[_from][msg.sender] && msg.sender != tokenApprovals[_tokenId]) revert INVALID_APPROVAL();
     135, 135:
     136, 136:         _beforeTokenTransfer(_from, _to, _tokenId);
     137, 137:
   ```

   - src/lib/utils/Pausable.sol

   ```diff
   diff --git a/src/lib/utils/Pausable.sol b/src/lib/utils/Pausable.sol
   index 6057d6b..5d37ad7 100644
   --- a/src/lib/utils/Pausable.sol
   +++ b/src/lib/utils/Pausable.sol
   @@ -12,7 +12,7 @@ abstract contract Pausable is IPausable, Initializable {
      12,  12:     ///                                                          ///
      13,  13:
      14,  14:     /// @dev If the contract is paused
   -  15     :-    bool internal _paused;
   +       15:+    uint256 internal _paused;
      16,  16:
      17,  17:     ///                                                          ///
      18,  18:     ///                           MODIFIERS                      ///
   @@ -20,13 +20,13 @@ abstract contract Pausable is IPausable, Initializable {
      20,  20:
      21,  21:     /// @dev Ensures the contract is paused
      22,  22:     modifier whenPaused() {
   -  23     :-        if (!_paused) revert UNPAUSED();
   +       23:+        if (0==_paused) revert UNPAUSED();
      24,  24:         _;
      25,  25:     }
      26,  26:
      27,  27:     /// @dev Ensures the contract isn't paused
      28,  28:     modifier whenNotPaused() {
   -  29     :-        if (_paused) revert PAUSED();
   +       29:+        if (1==_paused) revert PAUSED();
      30,  30:         _;
      31,  31:     }
      32,  32:
   @@ -37,24 +37,24 @@ abstract contract Pausable is IPausable, Initializable {
      37,  37:     /// @dev Sets whether the initial state
      38,  38:     /// @param _initPause If the contract should pause upon initialization
      39,  39:     function __Pausable_init(bool _initPause) internal onlyInitializing {
   -  40     :-        _paused = _initPause;
   +       40:+        _paused = _initPause ? 1 : 0;
      41,  41:     }
      42,  42:
      43,  43:     /// @notice If the contract is paused
      44,  44:     function paused() external view returns (bool) {
   -  45     :-        return _paused;
   +       45:+        return _paused == 1;
      46,  46:     }
      47,  47:
      48,  48:     /// @dev Pauses the contract
      49,  49:     function _pause() internal virtual whenNotPaused {
   -  50     :-        _paused = true;
   +       50:+        _paused = 1;
      51,  51:
      52,  52:         emit Paused(msg.sender);
      53,  53:     }
      54,  54:
      55,  55:     /// @dev Unpauses the contract
      56,  56:     function _unpause() internal virtual whenPaused {
   -  57     :-        _paused = false;
   +       57:+        _paused = 0;
      58,  58:
      59,  59:         emit Unpaused(msg.sender);
      60,  60:     }
   ```

   - src/manager/Manager.sol
   - src/manager/storage/ManagerStorageV1.sol

   ```diff
   diff --git a/src/manager/Manager.sol b/src/manager/Manager.sol
   index d1025ec..5e2a230 100644
   --- a/src/manager/Manager.sol
   +++ b/src/manager/Manager.sol
   @@ -178,14 +178,14 @@ contract Manager is IManager, UUPS, Ownable, ManagerStorageV1 {
     178, 178:     /// @param _baseImpl The base implementation address
     179, 179:     /// @param _upgradeImpl The upgrade implementation address
     180, 180:     function isRegisteredUpgrade(address _baseImpl, address _upgradeImpl) external view returns (bool) {
   - 181     :-        return isUpgrade[_baseImpl][_upgradeImpl];
   +      181:+        return 1==isUpgrade[_baseImpl][_upgradeImpl];
     182, 182:     }
     183, 183:
     184, 184:     /// @notice Called by the Builder DAO to offer implementation upgrades for created DAOs
     185, 185:     /// @param _baseImpl The base implementation address
     186, 186:     /// @param _upgradeImpl The upgrade implementation address
     187, 187:     function registerUpgrade(address _baseImpl, address _upgradeImpl) external onlyOwner {
   - 188     :-        isUpgrade[_baseImpl][_upgradeImpl] = true;
   +      188:+        isUpgrade[_baseImpl][_upgradeImpl] = 1;
     189, 189:
     190, 190:         emit UpgradeRegistered(_baseImpl, _upgradeImpl);
     191, 191:     }
   diff --git a/src/manager/storage/ManagerStorageV1.sol b/src/manager/storage/ManagerStorageV1.sol
   index 5d981ef..e566821 100644
   --- a/src/manager/storage/ManagerStorageV1.sol
   +++ b/src/manager/storage/ManagerStorageV1.sol
   @@ -7,5 +7,5 @@ pragma solidity 0.8.15;
       7,   7: contract ManagerStorageV1 {
       8,   8:     /// @notice If a contract has been registered as an upgrade
       9,   9:     /// @dev Base impl => Upgrade impl
   -  10     :-    mapping(address => mapping(address => bool)) internal isUpgrade;
   +       10:+    mapping(address => mapping(address => uint256)) internal isUpgrade;
      11,  11: }
   ```

   - src/token/metadata/MetadataRenderer.sol
   - src/token/metadata/types/MetadataRendererTypesV1.sol

   ```diff
   diff --git a/src/token/metadata/MetadataRenderer.sol b/src/token/metadata/MetadataRenderer.sol
   index 7a140ec..7976581 100644
   --- a/src/token/metadata/MetadataRenderer.sol
   +++ b/src/token/metadata/MetadataRenderer.sol
   @@ -136,7 +136,7 @@ contract MetadataRenderer is IPropertyIPFSMetadataRenderer, UUPS, Ownable, Metad
     136, 136:
     137, 137:                 // Offset the id if the item is for a new property
     138, 138:                 // Note: Property ids under the hood are offset by 1
   - 139     :-                if (_items[i].isNewProperty) {
   +      139:+                if (_items[i].isNewProperty == 1) {
     140, 140:                     _propertyId += numStoredProperties;
     141, 141:                 }
     142, 142:
   diff --git a/src/token/metadata/types/MetadataRendererTypesV1.sol b/src/token/metadata/types/MetadataRendererTypesV1.sol
   index c0890f6..4a3146b 100644
   --- a/src/token/metadata/types/MetadataRendererTypesV1.sol
   +++ b/src/token/metadata/types/MetadataRendererTypesV1.sol
   @@ -8,7 +8,7 @@ interface MetadataRendererTypesV1 {
       8,   8:     struct ItemParam {
       9,   9:         uint256 propertyId;
      10,  10:         string name;
   -  11     :-        bool isNewProperty;
   +       11:+        uint256 isNewProperty;
      12,  12:     }
      13,  13:
      14,  14:     struct IPFSGroup {
   ```

6. **Storage variable is used when local exists (2 instances)**

   Deployment Gas Saved: **1 400**
   Method Call Gas Saved: **2 602**
   `forge snapshot --diff`: **41 326** Gas Saved

   - src/auction/Auction.sol

   function: `_settleAuction`: `auction` cached in 169 but readed from storage in 172

   ```diff
   diff --git a/src/auction/Auction.sol b/src/auction/Auction.sol
   index 794da99..3754f3b 100644
   --- a/src/auction/Auction.sol
   +++ b/src/auction/Auction.sol
   @@ -169,7 +169,7 @@ contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionS
   169, 169:         Auction memory _auction = auction;
   170, 170:
   171, 171:         // Ensure the auction wasn't already settled
   - 172     :-        if (auction.settled) revert AUCTION_SETTLED();
   +      172:+        if (_auction.settled) revert AUCTION_SETTLED();
   173, 173:
   174, 174:         // Ensure the auction had started
   175, 175:         if (_auction.startTime == 0) revert AUCTION_NOT_STARTED();
   ```

   - src/governance/governor/Governor.sol

     function `propose`: `proposalThreshold()` cached in 123, but called again in 128

   ```diff
   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index 0d963c5..ce1ad1a 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -125,7 +125,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   125, 125:         // Cannot realistically underflow and `getVotes` would revert
   126, 126:         unchecked {
   127, 127:             // Ensure the caller's voting weight is greater than or equal to the threshold
   - 128     :-            if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) revert BELOW_PROPOSAL_THRESHOLD();
   +      128:+            if (getVotes(msg.sender, block.timestamp - 1) < currentProposalThreshold) revert BELOW_PROPOSAL_THRESHOLD();
   129, 129:         }
   130, 130:
   131, 131:         // Cache the number of targets
   ```

7. **Use named returns where appropriate (3 instances)**

   Deployment Gas Saved: **2 000**
   Method Call Gas Saved: **174**
   `forge snapshot --diff`: **621** Gas Saved

   - src/governance/governor/Governor.sol

   ```diff
   diff --git a/src/governance/governor/Governor.sol b/src/governance/governor/Governor.sol
   index 0d963c5..200a72a 100644
   --- a/src/governance/governor/Governor.sol
   +++ b/src/governance/governor/Governor.sol
   @@ -118,7 +118,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   118, 118:         uint256[] memory _values,
   119, 119:         bytes[] memory _calldatas,
   120, 120:         string memory _description
   - 121     :-    ) external returns (bytes32) {
   +      121:+    ) external returns (bytes32 proposalId) {
   122, 122:         // Get the current proposal threshold
   123, 123:         uint256 currentProposalThreshold = proposalThreshold();
   124, 124:
   @@ -142,7 +142,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   142, 142:         bytes32 descriptionHash = keccak256(bytes(_description));
   143, 143:
   144, 144:         // Compute the proposal id
   - 145     :-        bytes32 proposalId = hashProposal(_targets, _values, _calldatas, descriptionHash);
   +      145:+        proposalId = hashProposal(_targets, _values, _calldatas, descriptionHash);
   146, 146:
   147, 147:         // Get the pointer to store the proposal
   148, 148:         Proposal storage proposal = proposals[proposalId];
   @@ -170,8 +170,6 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   170, 170:         proposal.timeCreated = uint32(block.timestamp);
   171, 171:
   172, 172:         emit ProposalCreated(proposalId, _targets, _values, _calldatas, _description, descriptionHash, proposal);
   - 173     :-
   - 174     :-        return proposalId;
   175, 173:     }
   176, 174:
   177, 175:     ///                                                          ///
   @@ -250,7 +248,7 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   250, 248:         address _voter,
   251, 249:         uint256 _support,
   252, 250:         string memory _reason
   - 253     :-    ) internal returns (uint256) {
   +      251:+    ) internal returns (uint256 weight) {
   254, 252:         // Ensure voting is active
   255, 253:         if (state(_proposalId) != ProposalState.Active) revert VOTING_NOT_STARTED();
   256, 254:
   @@ -266,9 +264,6 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   266, 264:         // Get the pointer to the proposal
   267, 265:         Proposal storage proposal = proposals[_proposalId];
   268, 266:
   - 269     :-        // Used to store the voter's weight
   - 270     :-        uint256 weight;
   - 271     :-
   272, 267:         // Cannot realistically underflow and `getVotes` would revert
   273, 268:         unchecked {
   274, 269:             // Get the voter's weight at the time the proposal was created
   @@ -292,8 +287,6 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   292, 287:         }
   293, 288:
   294, 289:         emit VoteCast(_voter, _proposalId, _support, weight, _reason);
   - 295     :-
   - 296     :-        return weight;
   297, 290:     }
   298, 291:
   299, 292:     ///                                                          ///
   @@ -326,9 +319,9 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   326, 319:         uint256[] calldata _values,
   327, 320:         bytes[] calldata _calldatas,
   328, 321:         bytes32 _descriptionHash
   - 329     :-    ) external payable returns (bytes32) {
   +      322:+    ) external payable returns (bytes32 proposalId) {
   330, 323:         // Get the proposal id
   - 331     :-        bytes32 proposalId = hashProposal(_targets, _values, _calldatas, _descriptionHash);
   +      324:+        proposalId = hashProposal(_targets, _values, _calldatas, _descriptionHash);
   332, 325:
   333, 326:         // Ensure the proposal is queued
   334, 327:         if (state(proposalId) != ProposalState.Queued) revert PROPOSAL_NOT_QUEUED(proposalId);
   @@ -340,8 +333,6 @@ contract Governor is IGovernor, UUPS, Ownable, EIP712, GovernorStorageV1 {
   340, 333:         settings.treasury.execute{ value: msg.value }(_targets, _values, _calldatas, _descriptionHash);
   341, 334:
   342, 335:         emit ProposalExecuted(proposalId);
   - 343     :-
   - 344     :-        return proposalId;
   345, 336:     }
   346, 337:
   347, 338:     ///                                                          ///
   ```

8. **Overall Gas Saved**

   This is the result of merging all the fixes:

   Deployment Gas Saved: **460 774**
   Method Call Gas Saved: **6 547**
   `forge snapshot --diff`: **71 792** Gas Saved

   **`forge test --gas-report`:**

   ```diff
   diff --git a/original.txt b/foundry.txt
   index 66f1919..aa828af 100644
   --- a/original.txt
   +++ b/foundry.txt
   @@ -3,46 +3,46 @@
   ╞═══════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
   │ Deployment Cost                                       ┆ Deployment Size ┆        ┆        ┆        ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ 2278467                                               ┆ 11788           ┆        ┆        ┆        ┆         │
   +│ 2095247                                               ┆ 10866           ┆        ┆        ┆        ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ Function Name                                         ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ auction()(address)                                    ┆ 791             ┆ 791    ┆ 791    ┆ 791    ┆ 2       │
   +│ auction()(address)                                    ┆ 785             ┆ 785    ┆ 785    ┆ 785    ┆ 11      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ auction()(uint256,uint256,address,uint40,uint40,bool) ┆ 791             ┆ 791    ┆ 791    ┆ 791    ┆ 12      │
   +│ auction()(uint40,uint40,bool,address,uint256,uint256) ┆ 785             ┆ 785    ┆ 785    ┆ 785    ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ createBid                                             ┆ 4117            ┆ 19771  ┆ 28022  ┆ 28022  ┆ 50      │
   +│ createBid                                             ┆ 4004            ┆ 19695  ┆ 27947  ┆ 27947  ┆ 50      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ duration                                              ┆ 334             ┆ 334    ┆ 334    ┆ 334    ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ initialize                                            ┆ 3058            ┆ 124851 ┆ 117699 ┆ 137599 ┆ 73      │
   +│ initialize                                            ┆ 3058            ┆ 124783 ┆ 117630 ┆ 137530 ┆ 73      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ minBidIncrement                                       ┆ 389             ┆ 389    ┆ 389    ┆ 389    ┆ 2       │
   +│ minBidIncrement                                       ┆ 378             ┆ 378    ┆ 378    ┆ 378    ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ owner                                                 ┆ 363             ┆ 1029   ┆ 363    ┆ 2363   ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ pause                                                 ┆ 1450            ┆ 1450   ┆ 1450   ┆ 1450   ┆ 4       │
   +│ pause                                                 ┆ 1356            ┆ 1356   ┆ 1356   ┆ 1356   ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ paused                                                ┆ 382             ┆ 382    ┆ 382    ┆ 382    ┆ 2       │
   +│ paused                                                ┆ 379             ┆ 379    ┆ 379    ┆ 379    ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ reservePrice                                          ┆ 326             ┆ 1659   ┆ 2326   ┆ 2326   ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ settleAuction                                         ┆ 3398            ┆ 49224  ┆ 49224  ┆ 95050  ┆ 2       │
   +│ settleAuction                                         ┆ 3398            ┆ 49226  ┆ 49226  ┆ 95055  ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ settleCurrentAndCreateNewAuction                      ┆ 3986            ┆ 156417 ┆ 162440 ┆ 163090 ┆ 36      │
   +│ settleCurrentAndCreateNewAuction                      ┆ 3974            ┆ 156410 ┆ 162437 ┆ 163075 ┆ 36      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ timeBuffer                                            ┆ 387             ┆ 387    ┆ 387    ┆ 387    ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ treasury                                              ┆ 341             ┆ 1341   ┆ 1341   ┆ 2341   ┆ 2       │
   +│ treasury                                              ┆ 352             ┆ 1352   ┆ 1352   ┆ 2352   ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ unpause                                               ┆ 2408            ┆ 373450 ┆ 389513 ┆ 389513 ┆ 42      │
   +│ unpause                                               ┆ 2408            ┆ 373107 ┆ 389161 ┆ 389161 ┆ 42      │
   ╰───────────────────────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
   ╭────────────────────────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
   │ src/governance/governor/Governor.sol:Governor contract ┆                 ┆        ┆        ┆        ┆         │
   ╞════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
   │ Deployment Cost                                        ┆ Deployment Size ┆        ┆        ┆        ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ 4045061                                                ┆ 20520           ┆        ┆        ┆        ┆         │
   +│ 3874434                                                ┆ 19668           ┆        ┆        ┆        ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ Function Name                                          ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -50,21 +50,21 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ VOTE_TYPEHASH                                          ┆ 296             ┆ 296    ┆ 296    ┆ 296    ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ cancel                                                 ┆ 1214            ┆ 7608   ┆ 8414   ┆ 13799  ┆ 5       │
   +│ cancel                                                 ┆ 1214            ┆ 7267   ┆ 7964   ┆ 13237  ┆ 5       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ castVote                                               ┆ 1382            ┆ 24589  ┆ 29300  ┆ 30820  ┆ 17      │
   +│ castVote                                               ┆ 1382            ┆ 24482  ┆ 29171  ┆ 30691  ┆ 17      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ castVoteBySig                                          ┆ 594             ┆ 26061  ┆ 24899  ┆ 53855  ┆ 4       │
   +│ castVoteBySig                                          ┆ 594             ┆ 26029  ┆ 24899  ┆ 53726  ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ execute(address[],uint256[],bytes[],bytes32)           ┆ 17537           ┆ 17537  ┆ 17537  ┆ 17537  ┆ 2       │
   +│ execute(address[],uint256[],bytes[],bytes32)           ┆ 17310           ┆ 17310  ┆ 17310  ┆ 17310  ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ execute(address[],uint256[],bytes[],bytes32)(bytes32)  ┆ 17537           ┆ 17537  ┆ 17537  ┆ 17537  ┆ 1       │
   +│ execute(address[],uint256[],bytes[],bytes32)(bytes32)  ┆ 17310           ┆ 17310  ┆ 17310  ┆ 17310  ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ getProposal                                            ┆ 1924            ┆ 1924   ┆ 1924   ┆ 1924   ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ hashProposal                                           ┆ 3733            ┆ 3733   ┆ 3733   ┆ 3733   ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ initialize                                             ┆ 3106            ┆ 183841 ┆ 186352 ┆ 186352 ┆ 73      │
   +│ initialize                                             ┆ 3106            ┆ 184073 ┆ 186587 ┆ 186587 ┆ 73      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ nonce                                                  ┆ 2613            ┆ 2613   ┆ 2613   ┆ 2613   ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -80,9 +80,9 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ proposalThresholdBps                                   ┆ 410             ┆ 410    ┆ 410    ┆ 410    ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ proposalVotes                                          ┆ 1149            ┆ 1149   ┆ 1149   ┆ 1149   ┆ 3       │
   +│ proposalVotes                                          ┆ 737             ┆ 737    ┆ 737    ┆ 737    ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ propose                                                ┆ 8813            ┆ 60443  ┆ 69284  ┆ 79791  ┆ 29      │
   +│ propose                                                ┆ 7477            ┆ 59104  ┆ 67943  ┆ 78450  ┆ 29      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ queue                                                  ┆ 1334            ┆ 21893  ┆ 32192  ┆ 40192  ┆ 8       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -94,9 +94,9 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ treasury                                               ┆ 2408            ┆ 2408   ┆ 2408   ┆ 2408   ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ updateProposalThresholdBps                             ┆ 9044            ┆ 9044   ┆ 9044   ┆ 9044   ┆ 3       │
   +│ updateProposalThresholdBps                             ┆ 8978            ┆ 8978   ┆ 8978   ┆ 8978   ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ updateQuorumThresholdBps                               ┆ 9022            ┆ 9022   ┆ 9022   ┆ 9022   ┆ 1       │
   +│ updateQuorumThresholdBps                               ┆ 8956            ┆ 8956   ┆ 8956   ┆ 8956   ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ veto                                                   ┆ 2472            ┆ 6989   ┆ 3341   ┆ 15155  ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -111,7 +111,7 @@
   ╞════════════════════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
   │ Deployment Cost                                        ┆ Deployment Size ┆       ┆        ┆       ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ 1883434                                                ┆ 9725            ┆       ┆        ┆       ┆         │
   +│ 1856398                                                ┆ 9590            ┆       ┆        ┆       ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ Function Name                                          ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -119,15 +119,15 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ delay                                                  ┆ 355             ┆ 755   ┆ 355    ┆ 2355  ┆ 5       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ execute(address[],uint256[],bytes[],bytes32)           ┆ 8885            ┆ 8885  ┆ 8885   ┆ 8885  ┆ 2       │
   +│ execute(address[],uint256[],bytes[],bytes32)           ┆ 8662            ┆ 8662  ┆ 8662   ┆ 8662  ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ execute(address[],uint256[],bytes[],bytes32)(bytes32)  ┆ 8885            ┆ 8885  ┆ 8885   ┆ 8885  ┆ 1       │
   +│ execute(address[],uint256[],bytes[],bytes32)(bytes32)  ┆ 8662            ┆ 8662  ┆ 8662   ┆ 8662  ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ fallback                                               ┆ 55              ┆ 55    ┆ 55     ┆ 55    ┆ 25      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ hashProposal                                           ┆ 2411            ┆ 2411  ┆ 2411   ┆ 2411  ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ initialize                                             ┆ 2816            ┆ 49203 ┆ 49848  ┆ 49848 ┆ 73      │
   +│ initialize                                             ┆ 2816            ┆ 49122 ┆ 49766  ┆ 49766 ┆ 73      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ isExpired                                              ┆ 627             ┆ 627   ┆ 627    ┆ 627   ┆ 5       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -154,31 +154,31 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ VOTE_TYPEHASH                                                       ┆ 679             ┆ 679     ┆ 679     ┆ 679     ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ auction()(address)                                                  ┆ 758             ┆ 1051    ┆ 1198    ┆ 1198    ┆ 3       │
   +│ auction()(address)                                                  ┆ 758             ┆ 1155    ┆ 1192    ┆ 1192    ┆ 12      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ auction()(uint256,uint256,address,uint40,uint40,bool)               ┆ 758             ┆ 1164    ┆ 1198    ┆ 1198    ┆ 13      │
   +│ auction()(uint40,uint40,bool,address,uint256,uint256)               ┆ 758             ┆ 1083    ┆ 1192    ┆ 1192    ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ balanceOf                                                           ┆ 1001            ┆ 1001    ┆ 1001    ┆ 1001    ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ burn                                                                ┆ 883             ┆ 12715   ┆ 12715   ┆ 24548   ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ cancel                                                              ┆ 1601            ┆ 6948    ┆ 6668    ┆ 14182   ┆ 6       │
   +│ cancel                                                              ┆ 1601            ┆ 6663    ┆ 6269    ┆ 13620   ┆ 6       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ castVote                                                            ┆ 1772            ┆ 24978   ┆ 29689   ┆ 31209   ┆ 17      │
   +│ castVote                                                            ┆ 1772            ┆ 24871   ┆ 29560   ┆ 31080   ┆ 17      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ castVoteBySig                                                       ┆ 1014            ┆ 26481   ┆ 25319   ┆ 54274   ┆ 4       │
   +│ castVoteBySig                                                       ┆ 1014            ┆ 26449   ┆ 25319   ┆ 54145   ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ createBid                                                           ┆ 4504            ┆ 20137   ┆ 28405   ┆ 28405   ┆ 50      │
   +│ createBid                                                           ┆ 4391            ┆ 20061   ┆ 28330   ┆ 28330   ┆ 50      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ delay                                                               ┆ 738             ┆ 1138    ┆ 738     ┆ 2738    ┆ 5       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ deploy                                                              ┆ 6147            ┆ 1919053 ┆ 1629729 ┆ 7538521 ┆ 73      │
   +│ deploy                                                              ┆ 6147            ┆ 1919116 ┆ 1629793 ┆ 7538585 ┆ 73      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ duration                                                            ┆ 717             ┆ 717     ┆ 717     ┆ 717     ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ execute(address[],uint256[],bytes[],bytes32)                        ┆ 9242            ┆ 13569   ┆ 13569   ┆ 17896   ┆ 4       │
   +│ execute(address[],uint256[],bytes[],bytes32)                        ┆ 9019            ┆ 13344   ┆ 13344   ┆ 17669   ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ execute(address[],uint256[],bytes[],bytes32)(bytes32)               ┆ 9242            ┆ 13569   ┆ 13569   ┆ 17896   ┆ 2       │
   +│ execute(address[],uint256[],bytes[],bytes32)(bytes32)               ┆ 9019            ┆ 13344   ┆ 13344   ┆ 17669   ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ fallback                                                            ┆ 4942            ┆ 4942    ┆ 4942    ┆ 4942    ┆ 25      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -198,13 +198,13 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ initialize((address,uint256,uint256)[],bytes,address,address)       ┆ 237618          ┆ 540410  ┆ 237618  ┆ 6086313 ┆ 72      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ initialize(address,address,address,uint256,uint256)                 ┆ 7966            ┆ 125317  ┆ 118103  ┆ 138003  ┆ 73      │
   +│ initialize(address,address,address,uint256,uint256)                 ┆ 7966            ┆ 125249  ┆ 118034  ┆ 137934  ┆ 73      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ initialize(address,address,address,uint256,uint256,uint256,uint256) ┆ 8026            ┆ 184319  ┆ 186768  ┆ 186768  ┆ 73      │
   +│ initialize(address,address,address,uint256,uint256,uint256,uint256) ┆ 8026            ┆ 184551  ┆ 187003  ┆ 187003  ┆ 73      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ initialize(address,uint256)                                         ┆ 7706            ┆ 49651   ┆ 50234   ┆ 50234   ┆ 73      │
   +│ initialize(address,uint256)                                         ┆ 7706            ┆ 49570   ┆ 50152   ┆ 50152   ┆ 73      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ initialize(bytes,address,address,address)                           ┆ 207975          ┆ 207975  ┆ 207975  ┆ 207975  ┆ 72      │
   +│ initialize(bytes,address,address,address)                           ┆ 207955          ┆ 207955  ┆ 207955  ┆ 207955  ┆ 72      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ isExpired                                                           ┆ 1013            ┆ 1013    ┆ 1013    ┆ 1013    ┆ 5       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -214,9 +214,9 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ metadataRenderer                                                    ┆ 736             ┆ 736     ┆ 736     ┆ 736     ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ minBidIncrement                                                     ┆ 772             ┆ 772     ┆ 772     ┆ 772     ┆ 2       │
   +│ minBidIncrement                                                     ┆ 761             ┆ 761     ┆ 761     ┆ 761     ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ mint                                                                ┆ 995             ┆ 206704  ┆ 299139  ┆ 325439  ┆ 77      │
   +│ mint                                                                ┆ 995             ┆ 206580  ┆ 298899  ┆ 325199  ┆ 77      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ name                                                                ┆ 1666            ┆ 1666    ┆ 1666    ┆ 1666    ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -232,9 +232,9 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ ownerOf                                                             ┆ 946             ┆ 981     ┆ 986     ┆ 986     ┆ 9       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ pause                                                               ┆ 1754            ┆ 1754    ┆ 1754    ┆ 1754    ┆ 4       │
   +│ pause                                                               ┆ 1660            ┆ 1660    ┆ 1660    ┆ 1660    ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ paused                                                              ┆ 765             ┆ 765     ┆ 765     ┆ 765     ┆ 2       │
   +│ paused                                                              ┆ 762             ┆ 762     ┆ 762     ┆ 762     ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ proposalDeadline                                                    ┆ 927             ┆ 927     ┆ 927     ┆ 927     ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -244,23 +244,23 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ proposalThresholdBps                                                ┆ 793             ┆ 793     ┆ 793     ┆ 793     ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ proposalVotes                                                       ┆ 1541            ┆ 1541    ┆ 1541    ┆ 1541    ┆ 3       │
   +│ proposalVotes                                                       ┆ 1129            ┆ 1129    ┆ 1129    ┆ 1129    ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ propose                                                             ┆ 9272            ┆ 64312   ┆ 74239   ┆ 84746   ┆ 29      │
   +│ propose                                                             ┆ 7936            ┆ 62972   ┆ 72898   ┆ 83405   ┆ 29      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ queue                                                               ┆ 1721            ┆ 23937   ┆ 26590   ┆ 40578   ┆ 13      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ quorumThresholdBps                                                  ┆ 750             ┆ 750     ┆ 750     ┆ 750     ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ registerUpgrade                                                     ┆ 7490            ┆ 18878   ┆ 24573   ┆ 24573   ┆ 3       │
   +│ registerUpgrade                                                     ┆ 7490            ┆ 18870   ┆ 24561   ┆ 24561   ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ removeUpgrade                                                       ┆ 2159            ┆ 4834    ┆ 4834    ┆ 7510    ┆ 2       │
   +│ removeUpgrade                                                       ┆ 2074            ┆ 4792    ┆ 4792    ┆ 7510    ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ reservePrice                                                        ┆ 709             ┆ 2042    ┆ 2709    ┆ 2709    ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ settleAuction                                                       ┆ 3782            ┆ 49606   ┆ 49606   ┆ 95430   ┆ 2       │
   +│ settleAuction                                                       ┆ 3782            ┆ 49608   ┆ 49608   ┆ 95435   ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ settleCurrentAndCreateNewAuction                                    ┆ 4370            ┆ 156744  ┆ 162744  ┆ 163470  ┆ 36      │
   +│ settleCurrentAndCreateNewAuction                                    ┆ 4358            ┆ 156737  ┆ 162741  ┆ 163455  ┆ 36      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ state                                                               ┆ 1632            ┆ 2510    ┆ 1712    ┆ 5169    ┆ 8       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -276,19 +276,19 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ totalFounders                                                       ┆ 804             ┆ 804     ┆ 804     ┆ 804     ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ totalSupply                                                         ┆ 783             ┆ 1078    ┆ 783     ┆ 7283    ┆ 88      │
   +│ totalSupply                                                         ┆ 783             ┆ 1223    ┆ 783     ┆ 7283    ┆ 59      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ transferFrom(address,address,uint256)                               ┆ 77784           ┆ 79324   ┆ 79384   ┆ 79384   ┆ 27      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ transferFrom(address,address,uint256)(bool)                         ┆ 79384           ┆ 79384   ┆ 79384   ┆ 79384   ┆ 9       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ treasury                                                            ┆ 724             ┆ 2079    ┆ 2724    ┆ 2791    ┆ 3       │
   +│ treasury                                                            ┆ 735             ┆ 2087    ┆ 2735    ┆ 2791    ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ unpause                                                             ┆ 7292            ┆ 377795  ┆ 394393  ┆ 394393  ┆ 42      │
   +│ unpause                                                             ┆ 7292            ┆ 377451  ┆ 394041  ┆ 394041  ┆ 42      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ updateProposalThresholdBps                                          ┆ 13927           ┆ 13927   ┆ 13927   ┆ 13927   ┆ 3       │
   +│ updateProposalThresholdBps                                          ┆ 13861           ┆ 13861   ┆ 13861   ┆ 13861   ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ updateQuorumThresholdBps                                            ┆ 13905           ┆ 13905   ┆ 13905   ┆ 13905   ┆ 1       │
   +│ updateQuorumThresholdBps                                            ┆ 13839           ┆ 13839   ┆ 13839   ┆ 13839   ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ upgradeTo                                                           ┆ 2396            ┆ 5482    ┆ 5523    ┆ 5523    ┆ 78      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -307,11 +307,11 @@
   ╞══════════════════════════════════════════╪═════════════════╪═════════╪═════════╪═════════╪═════════╡
   │ Deployment Cost                          ┆ Deployment Size ┆         ┆         ┆         ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ 2084414                                  ┆ 13004           ┆         ┆         ┆         ┆         │
   +│ 2070008                                  ┆ 12932           ┆         ┆         ┆         ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ Function Name                            ┆ min             ┆ avg     ┆ median  ┆ max     ┆ # calls │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ deploy                                   ┆ 1081            ┆ 1917371 ┆ 1629134 ┆ 7531453 ┆ 73      │
   +│ deploy                                   ┆ 1081            ┆ 1917434 ┆ 1629198 ┆ 7531517 ┆ 73      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ getAddresses                             ┆ 1361            ┆ 1361    ┆ 1361    ┆ 1361    ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -323,9 +323,9 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ proxiableUUID                            ┆ 307             ┆ 307     ┆ 307     ┆ 307     ┆ 77      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ registerUpgrade                          ┆ 2600            ┆ 16991   ┆ 24187   ┆ 24187   ┆ 3       │
   +│ registerUpgrade                          ┆ 2600            ┆ 16983   ┆ 24175   ┆ 24175   ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ removeUpgrade                            ┆ 1850            ┆ 2235    ┆ 2235    ┆ 2620    ┆ 2       │
   +│ removeUpgrade                            ┆ 1765            ┆ 2192    ┆ 2192    ┆ 2620    ┆ 2       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ upgradeTo                                ┆ 5140            ┆ 5140    ┆ 5140    ┆ 5140    ┆ 77      │
   ╰──────────────────────────────────────────┴─────────────────┴─────────┴─────────┴─────────┴─────────╯
   @@ -334,13 +334,13 @@
   ╞═══════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪═════════╪═════════╡
   │ Deployment Cost                                       ┆ Deployment Size ┆        ┆        ┆         ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ 3254451                                               ┆ 16572           ┆        ┆        ┆         ┆         │
   +│ 3231219                                               ┆ 16456           ┆        ┆        ┆         ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ Function Name                                         ┆ min             ┆ avg    ┆ median ┆ max     ┆ # calls │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ auction()(address)                                    ┆ 375             ┆ 375    ┆ 375    ┆ 375     ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ auction()(uint256,uint256,address,uint40,uint40,bool) ┆ 375             ┆ 375    ┆ 375    ┆ 375     ┆ 1       │
   +│ auction()(uint40,uint40,bool,address,uint256,uint256) ┆ 375             ┆ 375    ┆ 375    ┆ 375     ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ balanceOf                                             ┆ 615             ┆ 615    ┆ 615    ┆ 615     ┆ 3       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -358,7 +358,7 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ metadataRenderer                                      ┆ 353             ┆ 353    ┆ 353    ┆ 353     ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ mint                                                  ┆ 611             ┆ 204217 ┆ 298756 ┆ 320556  ┆ 77      │
   +│ mint                                                  ┆ 611             ┆ 204093 ┆ 298516 ┆ 320316  ┆ 77      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ name                                                  ┆ 1277            ┆ 1277   ┆ 1277   ┆ 1277    ┆ 1       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -372,7 +372,7 @@
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ totalFounders                                         ┆ 421             ┆ 421    ┆ 421    ┆ 421     ┆ 4       │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ totalSupply                                           ┆ 400             ┆ 490    ┆ 400    ┆ 2400    ┆ 88      │
   +│ totalSupply                                           ┆ 400             ┆ 535    ┆ 400    ┆ 2400    ┆ 59      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ transferFrom(address,address,uint256)                 ┆ 77470           ┆ 79010  ┆ 79070  ┆ 79070   ┆ 27      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -387,11 +387,11 @@
   ╞═══════════════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
   │ Deployment Cost                                                   ┆ Deployment Size ┆        ┆        ┆        ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ 2980911                                                           ┆ 15206           ┆        ┆        ┆        ┆         │
   +│ 2942865                                                           ┆ 15016           ┆        ┆        ┆        ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ Function Name                                                     ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ initialize                                                        ┆ 207475          ┆ 207475 ┆ 207475 ┆ 207475 ┆ 72      │
   +│ initialize                                                        ┆ 207455          ┆ 207455 ┆ 207455 ┆ 207455 ┆ 72      │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ onMinted                                                          ┆ 3186            ┆ 4160   ┆ 3186   ┆ 7186   ┆ 158     │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   @@ -415,7 +415,7 @@
   ╞═════════════════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
   │ Deployment Cost                                     ┆ Deployment Size ┆       ┆        ┆       ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   -│ 898725                                              ┆ 4927            ┆       ┆        ┆       ┆         │
   +│ 894518                                              ┆ 4906            ┆       ┆        ┆       ┆         │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   │ Function Name                                       ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
   ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
   ```
