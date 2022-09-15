

	Findings: The transferFrom() method is used instead of safeTransferFrom() may freeze.lock the user's NFT. Implement safeTransferFrom instead of transferFrom for erc721

	Code: src/auction/Auction.sol - line 192
	
	Explanation: When using the transferFrom function of an ERC721 contract to send an NFT, if the receiving address is a smart contract and does not support ERC721, the NFT can be frozen in the contract.
	
	Mitigation: Use the ERC721 contract’s safeTransferFrom function to send NFTs, to avoid locked tokens in unsupported smart contracts.

-----

	Findings: _safemint() should be used rather than _mint() wherever possible
	
	Code: 
	src/token/Token.sol - line 188, line 169, line 161, 
	
	Explanation: _mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Reducing the likeliness of "locked" fund in a contract.
	
	Mitigation: Use _safeMint;

-----
	Findings: Use of Block.timestamp
	
	Code: src/auction/Auction.sol - line 178, line 98
	
	Explanation: Block timestamps should not be used to be the deciding factor whether the _auction.end ends. Miners have the ability to adjust timestamps, rearrange tx orders. Which can be dangerous / allows a user to game the system and _settleAuction.
	
	Mitigation: Use of Block.number, whereby miners are unable to easily manuipulate the block number.

-----

	Findings: Constants should be defined rather than using non-contextual/magic numbers
	
	Code: 
	src/auction/Auction.sol - line 119
	
	Explanation: It's recommended/ best practise to use constant variables rather the non-contextual literal values. This helps with code readability and easier to maintain for new/ future devs.
	
	Mitigation: Use constant variables

