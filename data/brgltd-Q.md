# [L-01] Add a check to ensure `settings.reservePrice` is not initialized with the value zero

Currently, it's possible to pass the value 0 for the auction reserve price. E.g. by a user-setting mistake.

```
function initialize(
    address _token,
    address _founder,
    address _treasury,
    uint256 _duration,
    uint256 _reservePrice
) external initializer {
    ...
    settings.reservePrice = _reservePrice; 
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L78

If `reservePrice` and the `msg.value` for the first bid are zero, the following check will pass.

```
if (msg.value < settings.reservePrice) revert RESERVE_PRICE_NOT_MET();
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L106

This means that if the first bid has the value zero, and no following bids are executed, the auction will be settled with 0 Eth as a value.

## Recommended Mitigation Steps

Add a validation check on `Auction.sol` `initializer()` to ensure `settings.reserverPrice` cannot receive the value zero.

# [L-02] Replace `transferFrom()` with `safeTransferFrom()` for ERC721 transfers

## Impact

The current implementation will not check if the recipient is capable of receiving ERC721.

## Proof of Concept

Usage of transferFrom in `Auction.sol`

```
192: token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L192

Not checking if the recipient is capable of receive the ERC721 can cause loss of funds.

## Recommended Mitigation Steps

Use `safeTransferFrom()`, which is already implemented in `src/lib/tokens/ERC721.sol` and it will check the recipient.

```
$ git diff --no-index Auction.sol.orig Auction.sol
diff --git a/Auction.sol.orig b/Auction.sol
index 794da99..911afe4 100644
--- a/Auction.sol.orig
+++ b/Auction.sol
@@ -189,7 +189,7 @@ contract Auction is IAuction, UUPS, Ownable, ReentrancyGuard, Pausable, AuctionS
             if (highestBid != 0) _handleOutgoingTransfer(settings.treasury, highestBid);

             // Transfer the token to the highest bidder
-            token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
+            token.safeTransferFrom(address(this), _auction.highestBidder, _auction.tokenId);

             // Else no bid was placed:
         } else {
```

# [L-03] Replace _mint with _safeMint

## Impact

The current ERC721 implementation is inspired on OpenZeppelin.
OpenZeppelin [discorages](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC721/ERC721Upgradeable.sol#L275) the usage of `_mint` is favor of `_safeMint()`.

## Proof of Concept

The current implementation will not check if the receiver accepts ERC721 token transfers.

```
function mint() external nonReentrant returns (uint256 tokenId) {
    // Cache the auction address
    address minter = settings.auction;

    // Ensure the caller is the auction
    if (msg.sender != minter) revert ONLY_AUCTION();

    // Cannot realistically overflow
    unchecked {
        do {
            // Get the next token to mint
            tokenId = settings.totalSupply++;

            // Lookup whether the token is for a founder, and mint accordingly if so
        } while (_isForFounder(tokenId));
    }

    // Mint the next available token to the auction house for bidding
    _mint(minter, tokenId);
}
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L143-L162

```
function _mint(address _to, uint256 _tokenId) internal virtual {
    if (_to == address(0)) revert ADDRESS_ZERO();

    if (owners[_tokenId] != address(0)) revert ALREADY_MINTED();

    _beforeTokenTransfer(address(0), _to, _tokenId);

    unchecked {
        ++balances[_to];
    }

    owners[_tokenId] = _to;

    emit Transfer(address(0), _to, _tokenId);

    _afterTokenTransfer(address(0), _to, _tokenId);
}
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L191-L207

## Recommended Mitigation Steps

Implement the `_safeMint()` variant of [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC721/ERC721Upgradeable.sol#L400-L422), to ensure the proper checks on the receiver are implemented.

# [L-04] Use SafeCast consistently in `Governor.sol`

## Impact

`Governor.sol` is using 8 SafeCast operations and 8 unsafe cast operations (casting without SafeCast).

The 8 instances with unsafe cast are highlighted on the following snippet:

```
165: proposal.voteStart = uint32(snapshot);
166: proposal.voteEnd = uint32(deadline);
167: proposal.proposalThreshold = uint32(currentProposalThreshold);
168: proposal.quorumVotes = uint32(quorum());
170: proposal.timeCreated = uint32(block.timestamp);
280: proposal.againstVotes += uint32(weight);
285: proposal.forVotes += uint32(weight);
290: proposal.abstainVotes += uint32(weight);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol

## Proof of concept

Taking the following snippet as an example:

```
154: uint256 snapshot;
160: snapshot = block.timestamp + settings.votingDelay;
165: proposal.voteStart = uint32(snapshot);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol

The variable `settings.votingDelay` can have a value up to uint48. If the valus passed is bigger than uint32 (e.g. due to a user setting mistake during contract initialization), the operation `proposal.voteStart = uint32(snapshot);` will silently overflow.

## Recommended Mitigation Steps

Use SafeCast consistently for all casting oprations implemented in `Governor.sol`.

# [L-05] Ensure _token and _treasury are not address zero

Similarly to what the implementation of `initialize()` on `Governor.sol`, the contracts `MetadataRenderer.sol` and `Auction.sol` should check that `_token` and `_treasury` are not receiving address zero during initialization.

## Recommened Mitigation Steps

Add the following checks on `MetadataRenderer.sol` and `Auction.sol` `initialize()` functions.

```
// Ensure non-zero addresses are provided
if (_treasury == address(0)) revert ADDRESS_ZERO();
if (_token == address(0)) revert ADDRESS_ZERO();
```

# [L-06] Empty receive function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert.
Also, the Ethereum Engineering Group recommends not using an empty receive function. [Video context](https://youtu.be/QfFjUMPtsM0?t=2565).


# [L-07] Contract inheriting upgradable contracts should have a storage variable `__gap` to allow for new storage variables in later versions

`ERC721Votes.sol` is inheriting `ERC721.sol`, and `ERC721.sol` is implemented using `ERC721Upgradable.sol` from OpenZeppelin as a reference.

```
/// @notice Modified from OpenZeppelin Contracts v4.7.3 (token/ERC721/ERC721Upgradeable.sol)
/// - Uses custom errors declared in IERC721
abstract contract ERC721 is IERC721, Initializable {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L11-L13
 
OpenZeppelin [recommends](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) the usage of a storage gap to allows to be able to add new state variables in the future. Please check [here](https://github.com/code-423n4/2022-08-foundation/blob/792e00df429b0df9ee5d909a0a5a6e72bd07cf79/contracts/mixins/shared/Gap10000.sol) for a implementation example.

# [L-08] Not following checks-effects-interactions

Inside the `_settleAuction()` function, an auction will be considered settled before the NFT is transferred to the highest bidder and the ETH is transferred to the treasury. 

```
function _settleAuction() private {
    // Get a copy of the current auction
    Auction memory _auction = auction;

    // Ensure the auction wasn't already settled
    if (auction.settled) revert AUCTION_SETTLED();

    // Ensure the auction had started
    if (_auction.startTime == 0) revert AUCTION_NOT_STARTED();

    // Ensure the auction is over
    if (block.timestamp < _auction.endTime) revert AUCTION_ACTIVE();

    // Mark the auction as settled
    auction.settled = true;

    // If a bid was placed:
    if (_auction.highestBidder != address(0)) {
        // Cache the amount of the highest bid
        uint256 highestBid = _auction.highestBid;

        // If the highest bid included ETH: Transfer it to the DAO treasury
        if (highestBid != 0) _handleOutgoingTransfer(settings.treasury, highestBid); 

        // Transfer the token to the highest bidder
        token.transferFrom(address(this), _auction.highestBidder, _auction.tokenId);
```

Reentrancy is not possible due to the parent function `settleCurrentAndCreateNewAuction()` `nonReentrant` modifier.

## Recommended mitigation steps

To follow the checks-effects-interactions, consider updating the state variable `auction.settled = true` after the NFT and ETH external transfer calls.

# [NC-01] Public functions not called by the contract should be declared external

```
221: function tokenURI(uint256 _tokenId) public view override(IToken, ERC721) returns (string memory) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L221

```
226: function contractURI() public view override(IToken, ERC721) returns (string memory) {
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L226

# [NC-02] Declare a constant instead of using magic numbers

The value `10_000` could be replace with a constant variable in `proposalThreshold()` and `quorum()`.

```
function proposalThreshold() public view returns (uint256) {
    unchecked {
        return (settings.token.totalSupply() * settings.proposalThresholdBps) / 10_000;
    }
}
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L466-L470

```
function quorum() public view returns (uint256) {
    unchecked {
        return (settings.token.totalSupply() * settings.quorumThresholdBps) / 10_000;
    }
}
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L473-L477

# [NC-03] Cache msg.value and msg.sender in `createAuction()`

The `createAuction()` function in `Auction.sol` is using `msg.value` 4 times and `msg.sender` 2 times.

These values could be cached and reused inside the function.

```
106: if (msg.value < settings.reservePrice) revert RESERVE_PRICE_NOT_MET();
123: if (msg.value < minBid) revert MINIMUM_BID_NOT_MET();
130: auction.highestBid = msg.value;
153: emit AuctionBid(_tokenId, msg.sender, msg.value, extend, auction.endTime);
```

```
133: auction.highestBidder = msg.sender;
153: emit AuctionBid(_tokenId, msg.sender, msg.value, extend, auction.endTime);
```

https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L90-L154
