  #1 ABI.ENCODEPACKED() SHOULD NOT BE USED WITH DYNAMIC TYPES WHEN PASSING THE RESULT TO A HASH FUNCTION SUCH AS KECCAK256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L226
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L162
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L68

#2 USE OF BLOCK.TIMESTAMP ,  A miner can manipulate the block timestamp which can be used to their advantage to attack a smart contract via Block Timestamp Manipulation

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L76 
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L153
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L253
Blocks have a timestamp field in the block header which is set by the miner and can be changed to whatever they want (with some restriction). In order for a miner to set a block timestamp they need to win the next block and abide by the following time constrains:

The next timestamp is after the last block timestamp
The timestamp can not be too far into the future
If the miner wins a block they can slightly change the block timestamp to their advantage.

## Impact
Dishonest  Miners can influence the value of block.timestamp to perform Maximal Extractable Value (MEV) attacks.
The use of now creates a risk that time manipulation can be performed to manipulate price oracles. Miners can modify the timestamp by up to 900 seconds , Usually to an extent of few seconds on Ethereum, or generally few percent of the block time on any EVM-compatible PoW network.

## Recommended Mitigation Steps
Use block.number instead of  block.timestamp or now to reduce the risk of
MEV attacks
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

### here some reference :
https://www.bookstack.cn/read/ethereumbook-en/spilt.14.c2a6b48ca6e1e33c.md
https://ethereum.stackexchange.com/questions/108033/what-do-i-need-to-be-careful-about-when-using-block-timestamp
#3  _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L161
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L188
#4 USE SAFETRANSFER/SAFETRANSFERFROM CONSISTENTLY INSTEAD OF TRANSFER/TRANSFERFROM
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L192 
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L363
Recommended Mitigation Steps
Consider using safeTransfer/safeTransferFrom or require() consistently.
#5 RETURN VALUES OF TRANSFER()/TRANSFERFROM() NOT CHECKED
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L192 
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L363