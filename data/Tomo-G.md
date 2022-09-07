# Gas | QA

## ✅ G-1: Don't Initialize Variables with Default Value

### 📝 Description

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas. Not overwriting the default for [stack variables](https://gist.github.com/IllIllI000/e075d189c1b23dce256cd166e28f3397) saves 8 gas. Storage and memory variables have larger savings

### 💡 Recommendation

Delete useless variable declarations to save gas.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162` [for (uint256 i = 0; i < numTargets; ++i) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L162)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119` [for (uint256 i = 0; i < numNewProperties; ++i) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L119)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133` [for (uint256 i = 0; i < numNewItems; ++i) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L133)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189` [for (uint256 i = 0; i < numProperties; ++i) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L189)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229` [for (uint256 i = 0; i < numProperties; ++i) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L229)

`2022-09-nouns-builder/test/Gov.t.sol#L79` [for (uint256 i = 0; i < \_numAgainst; ++i) {](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L79)

`2022-09-nouns-builder/test/Gov.t.sol#L86` [for (uint256 i = 0; i < \_numFor; ++i) {](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L86)

`2022-09-nouns-builder/test/Gov.t.sol#L93` [for (uint256 i = 0; i < \_numAbstain; ++i) {](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L93)

## ✅ G-2: Use != 0 instead of > 0 for Unsigned Integer Comparison

### 📝 Description

Use != 0 when comparing uint variables to zero, which cannot hold values below zero

### 💡 Recommendation

You should change from `> 0` to `!=0`.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L61` [if (\_data.length > 0 || \_forceCall) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L61)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L203` [if (\_from != \_to && \_amount > 0) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L203)

`2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L50` [if (\_returndata.length > 0) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L50)

## ✅ G-3: Use Shift Right/Left instead of Division/Multiplication if possible

### 📝 Description

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.
While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas.
urthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

### 💡 Recommendation

You should change multiplication and division by powers of two to bit shift.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L95` [middle = high - (high - low) / 2;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L95)

## ✅ G-4: Use assembly balance ETH

### 📝 Description

You can save 159 gas per instance if using assembly to check for balance of address

### 💡 Recommendation

Please follow [this link](https://gist.github.com/Tomosuke0930/4547e012ece4575e88e2a6d2baf498fa) to make corrections

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L346` [if (address(this).balance < \_amount) revert INSOLVENT();](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L346)

`2022-09-nouns-builder/test/Auction.t.sol#L69` [uint256 beforeAuctionBalance = address(auction).balance;](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L69)

`2022-09-nouns-builder/test/Auction.t.sol#L79` [uint256 afterBidderBalance = bidder1.balance;](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L79)

`2022-09-nouns-builder/test/Auction.t.sol#L80` [uint256 afterAuctionBalance = address(auction).balance;](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L80)

`2022-09-nouns-builder/test/Auction.t.sol#L108` [uint256 bidder1BeforeBalance = bidder1.balance;](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L108)

`2022-09-nouns-builder/test/Auction.t.sol#L109` [uint256 bidder2BeforeBalance = bidder2.balance;](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L109)

`2022-09-nouns-builder/test/Auction.t.sol#L119` [uint256 bidder1AfterBalance = bidder1.balance;](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L119)

`2022-09-nouns-builder/test/Auction.t.sol#L120` [uint256 bidder2AfterBalance = bidder2.balance;](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L120)

`2022-09-nouns-builder/test/Auction.t.sol#L124` [assertEq(address(auction).balance, 0.5 ether);](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L124)

`2022-09-nouns-builder/test/Auction.t.sol#L191` [assertEq(address(treasury).balance, 1 ether);](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L191)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L40` [return address(this).balance;](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L40)

## ✅ G-5: Use assembly to hash

### 📝 Description

See [this issue](https://github.com/code-423n4/2022-06-putty-findings/issues/59)
You can save about 5000 gas per instance in deploying contracrt.
You can save about 80 gas per instance if using assembly to execute the function.

### 💡 Recommendation

Please follow [this link](https://gist.github.com/Tomosuke0930/72beb469f08c954b232e58b8da03ef28) to make corrections

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27` [bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L104` [return keccak256(abi.encode(\_targets, \_values, \_calldatas, \_descriptionHash));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L104)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L142` [bytes32 descriptionHash = keccak256(bytes(\_description));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L142)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L226` [digest = keccak256(](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L226)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230` [keccak256(abi.encode(VOTE_TYPEHASH, \_voter, \_proposalId, \_support, nonces[\_voter]++, \_deadline))](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230)

`2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L107` [return keccak256(abi.encode(\_targets, \_values, \_calldatas, \_descriptionHash));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L107)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21` [bytes32 internal constant DELEGATION_TYPEHASH = keccak256("Delegation(address from,address to,uint256 nonce,uint256 deadline)");](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L21)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L161` [digest = keccak256(](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L161)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162` [abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, \_from, \_to, nonces[\_from]++, \_deadline)))](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162)

`2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L19` [bytes32 internal constant DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L19)

`2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L49` [HASHED_NAME = keccak256(bytes(\_name));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L49)

`2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L50` [HASHED_VERSION = keccak256(bytes(\_version));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L50)

`2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L69` [return keccak256(abi.encode(DOMAIN_TYPEHASH, HASHED_NAME, HASHED_VERSION, block.chainid, address(this)));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L69)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L68` [metadataHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(\_metadataImpl, "")));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L68)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L69` [auctionHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(\_auctionImpl, "")));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L69)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L70` [treasuryHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(\_treasuryImpl, "")));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L70)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L71` [governorHash = keccak256(abi.encodePacked(type(ERC1967Proxy).creationCode, abi.encode(\_governorImpl, "")));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L71)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L167` [metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L167)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L168` [auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L168)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L169` [treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L169)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L170` [governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L170)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L251` [return uint256(keccak256(abi.encode(\_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L251)

`2022-09-nouns-builder/test/Gov.t.sol#L175` [bytes32 descriptionHash = keccak256(bytes(""));](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L175)

`2022-09-nouns-builder/test/Gov.t.sol#L251` [bytes32 descriptionHash = keccak256(bytes(""));](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L251)

`2022-09-nouns-builder/test/Gov.t.sol#L329` [bytes32 digest = keccak256(](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L329)

`2022-09-nouns-builder/test/Gov.t.sol#L330` [abi.encodePacked("\x19\x01", domainSeparator, keccak256(abi.encode(voteTypeHash, voter1, proposalId, FOR, voterNonce, deadline)))](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L330)

`2022-09-nouns-builder/test/Gov.t.sol#L395` [bytes32 digest = keccak256(](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L395)

`2022-09-nouns-builder/test/Gov.t.sol#L396` [abi.encodePacked("\x19\x01", domainSeparator, keccak256(abi.encode(voteTypeHash, voter1, proposalId, FOR, voterNonce, deadline)))](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L396)

`2022-09-nouns-builder/test/Gov.t.sol#L418` [bytes32 digest = keccak256(](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L418)

`2022-09-nouns-builder/test/Gov.t.sol#L419` [abi.encodePacked("\x19\x01", domainSeparator, keccak256(abi.encode(voteTypeHash, voter1, proposalId, FOR, voterNonce + 1, deadline)))](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L419)

`2022-09-nouns-builder/test/Gov.t.sol#L441` [bytes32 digest = keccak256(](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L441)

`2022-09-nouns-builder/test/Gov.t.sol#L442` [abi.encodePacked("\x19\x01", domainSeparator, keccak256(abi.encode(voteTypeHash, voter1, proposalId, FOR, voterNonce, deadline)))](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L442)

`2022-09-nouns-builder/test/Gov.t.sol#L614` [governor.execute(targets, values, calldatas, keccak256(bytes("")));](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L614)

`2022-09-nouns-builder/test/Gov.t.sol#L668` [governor.execute(targets, values, calldatas, keccak256(bytes("")));](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L668)

`2022-09-nouns-builder/test/Gov.t.sol#L680` [bytes32 descriptionHash = keccak256(bytes("test"));](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L680)

`2022-09-nouns-builder/test/Gov.t.sol#L736` [governor.execute(targets, values, calldatas, keccak256(bytes("")));](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L736)

`2022-09-nouns-builder/test/Gov.t.sol#L771` [governor.execute(targets, values, calldatas, keccak256(bytes("")));](https://github.com/code-423n4/2022-09-nouns-builder/test/Gov.t.sol#L771)

## ✅ G-6: Solidity Version should be the latest

### 📝 Description

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

### 💡 Recommendation

You should consider with your team members whether you should choose the latest version.
The latest version has its advantages, but also it has disadvantages, such as the possibility of unknown bugs.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IEIP712.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IEIP712.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IOwnable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IOwnable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/EIP712.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/Pausable.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Pausable.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/ReentrancyGuard.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/ReentrancyGuard.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#L2` [pragma solidity ^0.8.4;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/SafeCast.sol#L2)

`2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol#L2` [pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol#L2)

## ✅ G-7: Empty blocks should be removed or emit something

### 📝 Description

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 💡 Recommendation

Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269` [receive() external payable {}](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269)


## ✅ G-8: Functions guaranteed to revert when called by normal users can be marked payable

### 📝 Description

See [this issue](https://github.com/code-423n4/2022-06-putty-findings/issues/59)
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
Saves 2400 gas per instance in deploying contracrt.
Saves about 20 gas per instance if using assembly to executing the function.

### 💡 Recommendation

You should add the keyword `payable` to those functions.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L244` [function unpause() external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L244)

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L263` [function pause() external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L263)

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307` [function setDuration(uint256 \_duration) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L307)

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315` [function setReservePrice(uint256 \_reservePrice) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L315)

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323` [function setTimeBuffer(uint256 \_timeBuffer) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L323)

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331` [function setMinimumBidIncrement(uint256 \_percentage) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L331)

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L374` [function \_authorizeUpgrade(address \_newImpl) internal view override onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L374)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L564` [function updateVotingDelay(uint256 \_newVotingDelay) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L564)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L572` [function updateVotingPeriod(uint256 \_newVotingPeriod) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L572)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L580` [function updateProposalThresholdBps(uint256 \_newProposalThresholdBps) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L580)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L588` [function updateQuorumThresholdBps(uint256 \_newQuorumVotesBps) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L588)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L596` [function updateVetoer(address \_newVetoer) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L596)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L605` [function burnVetoer() external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L605)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L618` [function \_authorizeUpgrade(address \_newImpl) internal view override onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L618)

`2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L116` [function queue(bytes32 \_proposalId) external onlyOwner returns (uint256 eta) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L116)

`2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L146` [) external payable onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L146)

`2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L180` [function cancel(bytes32 \_proposalId) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L180)

`2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L47` [function upgradeTo(address \_newImpl) external onlyProxy {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L47)

`2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L55` [function upgradeToAndCall(address \_newImpl, bytes memory \_data) external payable onlyProxy {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L55)


`2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L63` [function transferOwnership(address \_newOwner) public onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L63)

`2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L71` [function safeTransferOwnership(address \_newOwner) public onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L71)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L187` [function registerUpgrade(address \_baseImpl, address \_upgradeImpl) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L187)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L196` [function removeUpgrade(address \_baseImpl, address \_upgradeImpl) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L196)

`2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209` [function \_authorizeUpgrade(address \_newImpl) internal override onlyOwner {}](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L95` [) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L95)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L347` [function updateContractImage(string memory \_newContractImage) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L347)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L355` [function updateRendererBase(string memory \_newRendererBase) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L355)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L363` [function updateDescription(string memory \_newDescription) external onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L363)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L376` [function \_authorizeUpgrade(address \_impl) internal view override onlyOwner {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L376)

## ✅ G-9: Splitting require() statements that use && saves gas

### 📝 Description

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

### 💡 Recommendation

You should change one require which has `&&` to two simple require() statements to save gas

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L363` [if (msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L363)

`2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L89` [return timestamps[\_proposalId] != 0 && block.timestamp >= timestamps[\_proposalId];](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L89)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L105` [if (msg.sender != owner && !operatorApprovals[owner][msg.sender]) revert INVALID_APPROVAL();](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L105)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L134` [if (msg.sender != \_from && !operatorApprovals[\_from][msg.sender] && msg.sender != tokenApprovals[\_tokenId]) revert INVALID_APPROVAL();](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L134)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L165` [Address.isContract(\_to) &&](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L165)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L183` [Address.isContract(\_to) &&](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L183)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L203` [if (\_from != \_to && \_amount > 0) {](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L203)

`2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L36` [if ((!isTopLevelCall || \_initialized != 0) && (Address.isContract(address(this)) || \_initialized != 1)) revert ALREADY_INITIALIZED();](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Initializable.sol#L36)

`2022-09-nouns-builder/test/Auction.t.sol#L63` [vm.assume(\_amount >= auction.reservePrice() && \_amount <= bidder1.balance);](https://github.com/code-423n4/2022-09-nouns-builder/test/Auction.t.sol#L63)

`2022-09-nouns-builder/test/utils/NounsBuilderTest.sol#L108` [require(numFounders == \_percents.length && numFounders == \_vestingEnds.length);](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/NounsBuilderTest.sol#L108)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L60` [if (src != msg.sender && allowance[src][msg.sender] != type(uint128).max) {](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L60)

## ✅ G-10: Use `++i` instead of `i++`

### 📝 Description

You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting
The post-increment operation (i++) boils down to saving the original value of i, incrementing it and saving that to a temporary place in memory, and then returning the original value; only after that value is returned is the value of i actually updated (4 operations).On the other hand, in pre-increment doesn't need to store temporary(2 operations) so, the pre-increment operation uses less opcodes and is thus more gas efficient.

### 💡 Recommendation

You should change from i++ to ++i.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230` [keccak256(abi.encode(VOTE_TYPEHASH, \_voter, \_proposalId, \_support, nonces[\_voter]++, \_deadline))](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L230)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162` [abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, \_from, \_to, nonces[\_from]++, \_deadline)))](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L207` [uint256 nCheckpoints = numCheckpoints[\_from]++;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L207)

`2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L222` [uint256 nCheckpoints = numCheckpoints[\_to]++;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L222)


`2022-09-nouns-builder/blob/main/src/token/Token.sol#L91` [uint256 founderId = settings.numFounders++;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L91)


`2022-09-nouns-builder/blob/main/src/token/Token.sol#L154` [tokenId = settings.totalSupply++;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L154)


## ✅ G-11: Use x=x+y instad of x+=y

### 📝 Description

You can save about 35 gas per instance if you change from `x+=y**`\*\* to `x=x+y`
This works equally well for subtraction, multiplication and division.

### 💡 Recommendation

You should change from `x+=y` to `x=x+y`.

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280` [proposal.againstVotes += uint32(weight);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L280)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285` [proposal.forVotes += uint32(weight);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L285)

`2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290` [proposal.abstainVotes += uint32(weight);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L290)

`2022-09-nouns-builder/blob/main/src/token/Token.sol#L88` [if ((totalOwnership += uint8(founderPct)) > 100) revert INVALID_FOUNDER_OWNERSHIP();](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L88)

`2022-09-nouns-builder/blob/main/src/token/Token.sol#L118` [(baseTokenId += schedule) %!;(MISSING)](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L118)

`2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140` [\_propertyId += numStoredProperties;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L140)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L28` [balanceOf[msg.sender] += msg.value;](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L28)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L34` [balanceOf[msg.sender] -= wad;](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L34)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L62` [allowance[src][msg.sender] -= wad;](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L62)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L65` [balanceOf[src] -= wad;](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L65)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L66` [balanceOf[dst] += wad;](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L66)

## ✅ G-12: Use `uint256` instead of `bool`

### 📝 Description

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### 💡 Recommendation

You should change from bool to uint256 to save gas

### 🔍 Findings:

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L136` [bool extend;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L136)

`2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L349` [bool success;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L349)

`2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L35` [bool settled;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L35)

`2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L52` [bool executed;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L52)

`2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L53` [bool canceled;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L53)

`2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L54` [bool vetoed;](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/types/GovernorTypesV1.sol#L54)

## ✅ G-13: Use `indexed` for uint, bool, and address

### 📝 Description

Using the indexed keyword for value types such as uint, bool, and address saves gas costs, as seen in the example below. However, this is only the case for value types, whereas indexing bytes and strings are more expensive than their unindexed version.
Also indexed keyword has more merits.
It can be useful to have a way to monitor the contract's activity after it was deployed. One way to accomplish this is to look at all transactions of the contract, however that may be insufficient, as message calls between contracts are not recorded in the blockchain. Moreover, it shows only the input parameters, not the actual changes being made to the state. Also events could be used to trigger functions in the user interface.

### 💡 Recommendation

Up to three `indexed` can be used per event and should be added.

### 🔍 Findings:
`2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L22` [event AuctionBid(uint256 tokenId, address bidder, uint256 amount, bool extended, uint256 endTime);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L22)

`2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L28` [event AuctionSettled(uint256 tokenId, address winner, uint256 amount);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L28)

`2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L34` [event AuctionCreated(uint256 tokenId, uint256 startTime, uint256 endTime);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L34)

`2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L38` [event DurationUpdated(uint256 duration);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L38)

`2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L42` [event ReservePriceUpdated(uint256 reservePrice);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L42)

`2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L46` [event MinBidIncrementPercentageUpdated(uint256 minBidIncrementPercentage);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L46)

`2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L50` [event TimeBufferUpdated(uint256 timeBuffer);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L50)


`2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L29` [event ProposalQueued(bytes32 proposalId, uint256 eta);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L29)

`2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L42` [event VoteCast(address voter, bytes32 proposalId, uint256 support, uint256 weight, string reason);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L42)

`2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L45` [event VotingDelayUpdated(uint256 prevVotingDelay, uint256 newVotingDelay);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L45)

`2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L48` [event VotingPeriodUpdated(uint256 prevVotingPeriod, uint256 newVotingPeriod);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L48)

`2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L51` [event ProposalThresholdBpsUpdated(uint256 prevBps, uint256 newBps);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L51)

`2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L54` [event QuorumVotesBpsUpdated(uint256 prevBps, uint256 newBps);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L54)

`2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L57` [event VetoerUpdated(address prevVetoer, address newVetoer);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L57)

`2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L16` [event TransactionScheduled(bytes32 proposalId, uint256 timestamp);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L16)

`2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L25` [event DelayUpdated(uint256 prevDelay, uint256 newDelay);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L25)

`2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L28` [event GracePeriodUpdated(uint256 prevGracePeriod, uint256 newGracePeriod);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L28)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L14` [event Upgraded(address impl);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC1967Upgrade.sol#L14)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L19` [event DelegateVotesChanged(address indexed delegate, uint256 prevTotalVotes, uint256 newTotalVotes);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IERC721Votes.sol#L19)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L13` [event Initialized(uint256 version);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IInitializable.sol#L13)


`2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L14` [event Paused(address user);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L14)

`2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L18` [event Unpaused(address user);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/interfaces/IPausable.sol#L18)

`2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L21` [event DAODeployed(address token, address metadata, address auction, address treasury, address governor);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L21)

`2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L26` [event UpgradeRegistered(address baseImpl, address upgradeImpl);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L26)

`2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L31` [event UpgradeRemoved(address baseImpl, address upgradeImpl);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/IManager.sol#L31)

`2022-09-nouns-builder/blob/main/src/token/IToken.sol#L21` [event MintScheduled(uint256 baseTokenId, uint256 founderId, Founder founder);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/IToken.sol#L21)

`2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L16` [event PropertyAdded(uint256 id, string name);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L16)

`2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L19` [event ItemAdded(uint256 propertyId, uint256 index);](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol#L19)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L13` [event Deposit(address indexed dst, uint256 wad);](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L13)

`2022-09-nouns-builder/test/utils/mocks/WETH.sol#L14` [event Withdrawal(address indexed src, uint256 wad);](https://github.com/code-423n4/2022-09-nouns-builder/test/utils/mocks/WETH.sol#L14)
