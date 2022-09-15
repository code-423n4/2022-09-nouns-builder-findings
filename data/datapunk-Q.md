# 1
## Impact
struct/contract Auction name shadowing
Local variables, state variables, functions, modifiers, or events with names that shadow (i.e. override) builtin Solidity symbols e.g. now or other declarations from the current scope are misleading and may lead to unexpected usages and behavior. 

## Proof of Concept
https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/types/AuctionTypesV1.sol#L29
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L22

## Tools Used

## Recommended Mitigation Steps
be more explicit/creative with naming

# 2
## Impact
unnecessary uint40 casting, while _duration variable could have been passed in as uint40 to start with

## Proof of Concept
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L58
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L77


## Tools Used

## Recommended Mitigation Steps
function initialize(..., uint40 _duration, ...)

# 3
## Impact
unnecessary uint16 casting, while both quorumThresholdBps and _newQuorumVotesBps are defined as uint256

## Proof of Concept
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L588
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/IManager.sol#L79

## Tools Used

## Recommended Mitigation Steps
function updateQuorumThresholdBps(uint16 _newQuorumVotesBps) external onlyOwner {...}

# 4 Differences between ERC721 in this project and OpenZepplin
## Impact
one can approve or set him/herself as _to and _operator

## Proof of Concept
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L102
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721.sol#L115

## Tools Used

## Recommended Mitigation Steps
function setApprovalForAll(address _operator, bool _approved) external {
  **require(owner != operator, "ERC721: approve to caller");**
  ...
}
function approve(address _to, uint256 _tokenId) external {
  **require(to != owner, "ERC721: approval to current owner");**
  ...
}

# 5
## Impact
no need to delegate if new delegatee is the same as previous delegatee

## Proof of Concept
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L181

## Tools Used

## Recommended Mitigation Steps
add `if (_to==prevDelegate) revert REPEATED_DELEGATE`;

# 6
## Impact
condense elements in struct FounderParams into one slot
Especially all three elements are accessibled together

## Proof of Concept
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/IManager.sol#L48
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L82

## Tools Used

## Recommended Mitigation Steps
    struct FounderParams {
        address wallet;
        **uint8** ownershipPct;
        **uint32** vestExpiry; // @audit why uint256? can use one slot instead
    }

# 7
## Impact
increase state variable for incrementation. Use a memory variable to save gas

## Proof of Concept
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L91

## Tools Used

## Recommended Mitigation Steps
uint256 curNumFounder = settings.numFounders;
unchecked {
    ...
    uint256 founderId = curNumFounders++; 
    ...
}
settings.numFounders = curNumFounder;


# 8
## Impact
by design, there should not be more than 16 attributes. but when adding properties, there are no constraints

## Proof of Concept
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/storage/MetadataRendererStorageV1.sol#L20
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L91

## Tools Used

## Recommended Mitigation Steps
do something with this effect:
function addProperties() {
    ...
    if (_names.length + properties.length) > 16) revert TOO_MANY_PROPERTIES;
    ...
}

