### Low Risk Issues List
| Number | Issues Details|
|:--:|:-------|
|[L-01]|Floating pragma|
|[L-02]|```_owner``` declaration shadows an existing declaration|
|[L-03]|Non-essential inheritance list|
|[L-04]|Need Fuzzing test|
|[L-05]|Missing Event for critical parameters change|
|[L-06]|Add to blacklist function|
|[L-07]|No check if OnERC721Received is implemented|
|[L-08]|Missing supportsInterface|
|[L-09]|Use ```SafeTransferOwnership``` instead of ```transferOwnership``` function|
|[L-10]|Insufficient tests|
|[L-11]|Use abi.encode() to avoid collision|
|[L-12]|Return value of transfer not checked|

Total 12 issues

### Non-Critical Issues List
| Number | Issues Details|
|:--:|:-------|
|[N-01]|Inconsistent Solidity Versions|
|[N-02]|Use a more recent version of solidity|
|[N-03]|Unused/Empty ```receive()``` / ```fallback()``` function|
|[N-04]|Function writing that does not comply with the 'Solidity Style Guide’|
|[N-05]|WETH address definition can be use directly|
|[N-06]|```0``` address check|
|[N-07]|Missing NatSpec mapping comment|
|[N-08]|There is no need to cast a variable that is already an address, such as address(x)|
|[N-09]|Add to indexed parameter for countable Events|
|[N-10]|Include return parameters in NatSpec comments|
|[N-11]|Avoid to same local variable names  declarated for code readability|
|[N-12]|Do not import more than one same contract|
|[N-13]|Incorrect information in the document|
|[N-14]|Highest risk must be specified in NatSpec comments and documentation|
|[N-15]|Implement some type of version counter that will be incremented automatically for contract upgrades|
|[N-16]|Add ```castVote```  and ```castVotebySig``` in ERC721Votes|
|[N-17]|Empty blocks should be removed or Emit something|
|[N-18]|Long lines are not suitable for the 'Solidity Style Guide'|

Total 18 issues

## [L-01]  Floating Pragma

**Description:**
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
https://swcregistry.io/docs/SWC-103

Floating Pragma List: 
pragma ^0.8.4.  (src/lib/ all files)
pragma ^0.8.0.   (src/lib/utils/TokenReceiver.sol)

**Recommendation:**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

## [L-02] ```_owner``` declaration shadows an existing declaration

**Context:**
[Treasury.sol#L141-L172](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L141-L172)

**Description:**
Solidity docs:
The function ```initialize``` in the file manager.sol#L82 has a local variable named ```_owner```. However, there is also a state variable in the Ownable.sol file with the same name, from which it inherited. Therefore, there is shadowing here. This shadowing does not pose a security threat because the owner in Ownable.sol is also updated with this function. However, shadowing is undesirable, state variable and local variable names should not be the same, this negatively audit control and code readability.

**Recommendation:**
State variable and local variable should be named differently.

following codes
```js
Ownable.sol
abstract contract Ownable;

    address internal _owner;


Manager.sol
contract Manager is Ownable; 

    function initialize(address _owner) external initializer {
	if (_owner == address(0)) revert ADDRESS_ZERO();
	__Ownable_init(_owner);
}
```

## [L-03] Non-essential inheritance list

**Context:**
[Auction.sol#L22](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L22)

**Description:**
If a contract inherited different contracts, the last inherited contract can be used directly. But there are files in the codes that are not implemented this way.

**Recommendation:**
```IAuction;``` A, B, C contracts are inherited. ```Auction``` can inherit entire hierarchy by simply inheriting ```IAuction```

following codes
```js
lib
proxy─ ERC1967Upgrade - "Contract A"
proxy─ UUPS - "Contract B"
token─ Ownable - "Contract C"
auction
IAuction — "Contract D is A,B,C"
Auction — "Contract E is A,B,C,D"
```

## [L-04] Need Fuzzing test

**Context:**
35 results - 9 files
Project uncheckeds list:

```js
src/auction/Auction.sol:
  116              // Cannot realistically overflow
  117:             unchecked {
  118                  // Compute the minimum bid

  138          // Cannot underflow as `_auction.endTime` is ensured to be greater than the current time above
  139:         unchecked {
  140              // Compute whether the time remaining is less than the buffer

  146              // Cannot realistically overflow
  147:             unchecked {
  148                  // Extend the auction by the time buffer

  216              // Cannot realistically overflow
  217:             unchecked {
  218                  // Compute the auction end time

src/governance/governor/Governor.sol:
  125          // Cannot realistically underflow and `getVotes` would revert
  126:         unchecked {
  127              // Ensure the caller's voting weight is greater than or equal to the threshold

  157          // Cannot realistically overflow
  158:         unchecked {
  159              // Compute the snapshot and deadline

  223          // Cannot realistically overflow
  224:         unchecked {
  225              // Compute the message

  272          // Cannot realistically underflow and `getVotes` would revert
  273:         unchecked {
  274              // Get the voter's weight at the time the proposal was created

  360          // Cannot realistically underflow and `getVotes` would revert
  361:         unchecked {
  362              // Ensure the caller is the proposer or the proposer's voting weight has dropped below the proposal threshold

  466      function proposalThreshold() public view returns (uint256) {
  467:         unchecked {
  468              return (settings.token.totalSupply() * settings.proposalThresholdBps) / 10_000;

  473      function quorum() public view returns (uint256) {
  474:         unchecked {
  475              return (settings.token.totalSupply() * settings.quorumThresholdBps) / 10_000;

src/governance/treasury/Treasury.sol:
   74      function isExpired(bytes32 _proposalId) external view returns (bool) {
   75:         unchecked {
   76              return block.timestamp > (timestamps[_proposalId] + settings.gracePeriod);

  120          // Cannot realistically overflow
  121:         unchecked {
  122              // Compute the timestamp that the proposal will be valid to execute

  159          // Cannot realistically overflow
  160:         unchecked {
  161              // For each target:

src/lib/token/ERC721.sol:
  137  
  138:         unchecked {
  139              --balances[_from];

  197  
  198:         unchecked {
  199              ++balances[_to];

  217  
  218:         unchecked {
  219              --balances[owner];

src/lib/token/ERC721Votes.sol:
   49          // Cannot underflow as `nCheckpoints` is ensured to be greater than 0 if reached
   50:         unchecked {
   51              // Return the number of votes at the latest checkpoint if applicable

   71  
   72:         unchecked {
   73              // Get the latest checkpoint id

  158          // Cannot realistically overflow
  159:         unchecked {
  160              // Compute the hash of the domain seperator with the typed delegation data

  200      ) internal {
  201:         unchecked {
  202              // If voting weight is being transferred:

src/token/Token.sol:
   77  
   78:         unchecked {
   79              // For each founder:

  130      function _getNextTokenId(uint256 _tokenId) internal view returns (uint256) {
  131:         unchecked {
  132              while (tokenRecipient[_tokenId].wallet != address(0)) ++_tokenId;

  150          // Cannot realistically overflow
  151:         unchecked {
  152              do {

  259          // Cannot realistically overflow
  260:         unchecked {
  261              // Add each founder to the array

src/token/metadata/MetadataRenderer.sol:
  116  
  117:         unchecked {
  118              // For each new property:

  186  
  187:         unchecked {
  188              // For each property:

  223  
  224:         unchecked {
  225              // Cache the index of the last property

test/Token.t.sol:
   97  
   98:         unchecked {
   99              for (uint256 i; i < 100; ++i) {

  129  
  130:         unchecked {
  131              for (uint256 i; i < 50; ++i) {

  165  
  166:         unchecked {
  167              for (uint256 i; i < 2; ++i) {

  180  
  181:         unchecked {
  182              for (uint256 i; i < 500; ++i) {

test/utils/NounsBuilderTest.sol:
  109  
  110:         unchecked {
  111              for (uint256 i; i < numFounders; ++i) {

  225  
  226:         unchecked {
  227              for (uint256 i; i < _numUsers; ++i) {

  240  
  241:         unchecked {
  242              for (uint256 i; i < _numTokens; ++i) {
```

**Description:**
In total 9 contracts, 35 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05


## [L-05] Missing Event for Critical Parameters Change

**Context:**
[Auction.sol#L263](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L263)
[Auction.sol#L268](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L268)
[Governor.sol#L208](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L208)
[Treasury.sol#L210](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L210)
[Treasury.sol#L269](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269)

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

**Recommendation:**
Add ```event-emit```


## [L-06] Add to Blacklist function

**Description:**
Cryptocurrency mixing service, Tornado Cash, has been blacklisted in the OFAC.
A lot of blockchain companies, token projects, NFT Projects have ```blacklisted``` all Ethereum addresses owned by Tornado Cash listed in the US Treasury Department's sanction against the protocol.
https://home.treasury.gov/policy-issues/financial-sanctions/recent-actions/20220808
In addition, these platforms even ban accounts that have received ETH on their account with Tornadocash.

Some of these Projects;
* USDC (https://www.circle.com/en/usdc)
* Flashbots (https://www.paradigm.xyz/portfolio/flashbots )
* Aave (https://aave.com/)
* Uniswap
* Balancer
* Infura
* Alchemy 
* Opensea
* dYdX 

Details on the subject;
https://twitter.com/bantg/status/1556712790894706688?s=20&t=HUTDTeLikUr6Dv9JdMF7AA

https://cryptopotato.com/defi-protocol-aave-bans-justin-sun-after-he-randomly-received-0-1-eth-from-tornado-cash/

For this reason, every project in the Ethereum network must have a blacklist function, this is a good method to avoid legal problems in the future, apart from the current need.

Transactions from the project by an account funded by Tonadocash or banned by OFAC can lead to legal problems.Especially American citizens may want to get addresses to the blacklist legally, this is not an obligation

If you think that such legal prohibitions should be made directly by validators, this may not be possible:
https://www.paradigm.xyz/2022/09/base-layer-neutrality

```The ban on Tornado Cash makes little sense, because in the end, no one can prevent people from using other mixer smart contracts, or forking the existing ones. It neither hinders cybercrime, nor privacy.```

Here is the most beautiful and close to the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```js
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```
Recommended Mitigation Steps add to Blacklist function and modifier


## [L-07] No check if OnERC721Received is implemented

**Context:**
[Auction.sol#L206](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L206)

**Description:**
When minting a NFT, the function does not check if a receiving contract implements onERC721Received().
Check if OnERC721Received is implemented.
If this is intended, ```safemint``` should be used instead of ```mint```

```js
 function _createAuction() private {
        // Get the next token available for bidding
        try token.mint() returns (uint256 tokenId) {
            // Store the token id
            auction.tokenId = tokenId;

            // Cache the current timestamp
            uint256 startTime = block.timestamp;

            // Used to store the auction end time
            uint256 endTime;

            // Cannot realistically overflow
            unchecked {
                // Compute the auction end time
                endTime = startTime + settings.duration;
            }

            // Store the auction start and end time
            auction.startTime = uint40(startTime);
            auction.endTime = uint40(endTime);

            // Reset data from the previous auction
            auction.highestBid = 0;
            auction.highestBidder = address(0);
            auction.settled = false;

            emit AuctionCreated(tokenId, startTime, endTime);

            // Pause the contract if token minting failed
        } catch Error(string memory) {
            _pause();
        }
    }
```

**Recommendation:**
Consider using ```_safeMint``` instead of ```_mint```


## [L-08] Missing supportsInterface

**Context:**
[ERC721.sol#L61-L66](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L61-L66)

**Description:**
ERC721TokenReceiver interface ID is missing in supportInterface

```js
 function supportsInterface(bytes4 _interfaceId) external pure returns (bool) {
        return
            _interfaceId == 0x01ffc9a7 || // ERC165 Interface ID
            _interfaceId == 0x80ac58cd || // ERC721 Interface ID
            _interfaceId == 0x5b5e139f; // ERC721Metadata Interface ID
    }
```

**Recommendation:**

```js
 function supportsInterface(bytes4 _interfaceId) external pure returns (bool) {
        return
            _interfaceId == 0x01ffc9a7 || // ERC165 Interface ID
            _interfaceId == 0x80ac58cd || // ERC721 Interface ID
            _interfaceId == 0x150b7a02 || // ERC721TokenReceiver Interface ID
            _interfaceId == 0x5b5e139f; // ERC721Metadata Interface ID
    }
```

## [L-09] Use ```SafeTransferOwnership``` instead of ```transferOwnership``` function

**Context:**
[Ownable.sol#L63-L67](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Ownable.sol#L63-L67)

**Description:**
```transferOwnership``` function is used to change Ownership from ```Ownable.sol```.

```transferOwnership``` is used in Ownable.sol and MetadataRenderer.sol files to transfer ownership of the contract to the DAO.

There is another function, ```SafeTransferOwnership```,  use it is more secure due to 2-stage ownership transfer.

```js
 function transferOwnership(address _newOwner) public onlyOwner {
        emit OwnerUpdated(_owner, _newOwner);

        _owner = _newOwner;
    }
```

**Recommendation:**
```js
Remove to transferOwnership


 function safeTransferOwnership(address _newOwner) public onlyOwner {
        _pendingOwner = _newOwner;

        emit OwnerPending(_owner, _newOwner);
    }
```

## [L-10] Insufficient Tests

**Context:**
[MetadataRenderer.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol)

**Description:**
It is crucial to write tests with possibly 100% coverage for smart contract systems.
Contract of top in Context were found to be not included in the test cases.

**Recommendation:**
It is recommended to write proper test for all possible code flows and specially edge cases.

## [L-11] Use abi.encode() to avoid collision

**Context:**
[MetadataRenderer.sol#L243](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L243)
[MetadataRenderer.sol#L243](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L243)
[MetadataRenderer.sol#L259](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L259)
[MetadataRenderer.sol#L290](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L290)
[MetadataRenderer.sol#L208](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L208)
[MetadataRenderer.sol#L309](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/metadata/MetadataRenderer.sol#L309)

**Description:**
If you use keccak256(abi.encodePacked(a, b)) and both a and b are dynamic types, it is easy to craft collisions in the hash value by moving parts of a into b and vice-versa. More specifically, abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c"). If you use abi.encodePacked for signatures, authentication or data integrity, make sure to always use the same types and check that at most one of them is dynamic. Unless there is a compelling reason, abi.encode should be preferred.

When are these used?

```abi.encode:```
abi.encode encode its parameters using the ABI specs. The ABI was designed to make calls to contracts. Parameters are padded to 32 bytes. If you are making calls to a contract you likely have to use abi.encode. If you are dealing with more than one dynamic data type, you may prefer to use abi.encode as it avoids collision. keccak256(abi.encode(data, ...)) is the safe way to generate a unique ID for a given set of inputs.

```abi.encodePacked:```
abi.encodePacked encode its parameters using the minimal space required by the type. Encoding an uint8 it will use 1 byte. It is used when you want to save some space, and not calling a contract.Takes all types of data and any amount of input.

**Proof of Concept**
```js
contract Contract0 {

    // abi.encode
    // (AAA,BBB) keccak = 0xd6da8b03238087e6936e7b951b58424ff2783accb08af423b2b9a71cb02bd18b
    // (AA,ABBB) keccak = 0x54bc5894818c61a0ab427d51905bc936ae11a8111a8b373e53c8a34720957abb
    function collision(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encode(_text, _anotherText));
    }
}


contract Contract1 {

    // abi.encodePacked
    // (AAA,BBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358
    // (AA,ABBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358

    function hard(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encodePacked(_text, _anotherText));
    }
}
```

## [L-12] Return value of transfer not checked

**Context:**
[Auction.sol#L363](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L363)

**Description:**
When there’s a failure in transfer()/transferFrom() the function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment.

**Recommendation:**
Add return value checks.


## [N-01] Inconsistent solidity versions

**Description:**
Different Solidity compiler versions are used throughout ```src repositories```. The following contracts mix versions:
 
pragma ^0.8.4  (src/lib/ all files)
pragma ^0.8.15
pragma ^0.8.0   (src/lib/utils/TokenReceiver.sol)
pragma  0.8.15

**Recommendation:**
Versions must be consistent with each other

**Full List:**
```js
src/auction/Auction.sol:
pragma solidity 0.8.15;
 
src/auction/IAuction.sol:
pragma solidity 0.8.15;
 
src/auction/storage/AuctionStorageV1.sol:
pragma solidity 0.8.15;
 
src/auction/types/AuctionTypesV1.sol:
pragma solidity 0.8.15;
 
src/governance/governor/Governor.sol:
pragma solidity 0.8.15;
 
src/governance/governor/IGovernor.sol:
pragma solidity 0.8.15;
 
src/governance/governor/storage/GovernorStorageV1.sol:
pragma solidity 0.8.15;
 
src/governance/governor/types/GovernorTypesV1.sol:
pragma solidity 0.8.15;
 
src/governance/treasury/ITreasury.sol:
pragma solidity 0.8.15;
 
src/governance/treasury/Treasury.sol:
pragma solidity 0.8.15;
 
src/governance/treasury/storage/TreasuryStorageV1.sol:
pragma solidity 0.8.15;
 
src/governance/treasury/types/TreasuryTypesV1.sol:
pragma solidity 0.8.15;
 
src/lib/interfaces/IEIP712.sol:
pragma solidity ^0.8.4;
 
src/lib/interfaces/IERC721.sol:
pragma solidity ^0.8.4;
 
src/lib/interfaces/IERC721Votes.sol:
pragma solidity ^0.8.4;
 
src/lib/interfaces/IERC1967Upgrade.sol:
pragma solidity ^0.8.4;
 
src/lib/interfaces/IInitializable.sol:
pragma solidity ^0.8.4;
 
src/lib/interfaces/IOwnable.sol:
pragma solidity ^0.8.4;
 
src/lib/interfaces/IPausable.sol:
pragma solidity ^0.8.4;
 
src/lib/interfaces/IUUPS.sol:
pragma solidity ^0.8.15;
 
src/lib/interfaces/IWETH.sol:
pragma solidity ^0.8.15;
 
src/lib/proxy/ERC1967Proxy.sol:
pragma solidity ^0.8.4;
 
src/lib/proxy/ERC1967Upgrade.sol:
pragma solidity ^0.8.4;
 
src/lib/proxy/UUPS.sol:
pragma solidity ^0.8.4;
 
src/lib/token/ERC721.sol:
pragma solidity ^0.8.4;
 
src/lib/token/ERC721Votes.sol:
pragma solidity ^0.8.4;
 
src/lib/utils/Address.sol:
pragma solidity ^0.8.4;
 
src/lib/utils/EIP712.sol:
pragma solidity ^0.8.4;
 
src/lib/utils/Initializable.sol:
pragma solidity ^0.8.4;
 
src/lib/utils/Ownable.sol:
pragma solidity ^0.8.4;
 
src/lib/utils/Pausable.sol:
pragma solidity ^0.8.4;
 
src/lib/utils/ReentrancyGuard.sol:
pragma solidity ^0.8.4;
 
src/lib/utils/SafeCast.sol:
pragma solidity ^0.8.4;
 
src/lib/utils/TokenReceiver.sol:
pragma solidity ^0.8.0;
 
src/manager/IManager.sol:
pragma solidity 0.8.15;
 
src/manager/Manager.sol:
pragma solidity 0.8.15;
 
src/manager/storage/ManagerStorageV1.sol:
pragma solidity 0.8.15;
 
src/token/IToken.sol:
pragma solidity 0.8.15;
 
src/token/Token.sol:
pragma solidity 0.8.15;
 
src/token/metadata/MetadataRenderer.sol:
pragma solidity 0.8.15;
 
src/token/metadata/interfaces/IBaseMetadata.sol:
pragma solidity 0.8.15;
 
src/token/metadata/interfaces/IPropertyIPFSMetadataRenderer.sol:
pragma solidity 0.8.15;
 
src/token/metadata/storage/MetadataRendererStorageV1.sol:
pragma solidity 0.8.15;
 
src/token/metadata/types/MetadataRendererTypesV1.sol:
pragma solidity 0.8.15;
 
src/token/storage/TokenStorageV1.sol:
pragma solidity 0.8.15;
 
src/token/types/TokenTypesV1.sol:
pragma solidity 0.8.15;
```

## [N-02] Use a more recent version of solidity

**Context:**
[TokenReceiver.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/TokenReceiver.sol)

**Description:**
Old version of Solidity is used (```^0.8.0```), newer version should be used


## [N-03] Unused/Empty ```receive()``` / ```fallback()``` function

**Context:**
[Treasury.sol#L269](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L269)

**Description:**
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth)))


## [N-04] Function writing that does not comply with the 'Solidity Style Guide’

**Context:**
[Treasury.sol](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol)

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.15/style-guide.html

Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last


## [N-05] WETH address definition can be use directly

**Context:**
[Auction.sol#L41](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L41)

**Description:**
WETH is a wrap Ether contract with a specific address in the Etherum network, giving the option to define it may cause false recognition, it is healthier to define it directly.

Advantages of defining a specific contract directly:
- It saves gas 
- Prevents incorrect argument definition 
- Prevents execution on a different chain and re-signature issues

WETH Address : 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2

```js
    /// @param _manager The contract upgrade manager address
    /// @param _weth The address of WETH
    constructor(address _manager, address _weth) payable initializer {
        manager = IManager(_manager);
        WETH = _weth;
    }
```

**Recommendation:**
```js
address private constant WETH = 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2;

    constructor(address _manager) payable initializer {
        manager = IManager(_manager);
}
```

## [N-06] ```0``` address check

**Context:**
[Token.sol#L30-L32](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L30-L32)
[Auction.sol#L39-L42](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L39-L42)
[Governor.sol#L41-L43](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L41-L43)
[Treasury.sol#L33](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L33)

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good natspec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this ;
```if (_treasury == address(0)) revert ADDRESS_ZERO();```

## [N-07] Missing NatSpec mapping comment

**Context:**
[ManagerStorageV1.sol#L9](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/storage/ManagerStorageV1.sol#L9)

**Description:**
Last value in Nested Mapping is missing from comments

**Recommendation:**
ManagerStorageV1.sol#L9 
```js
/// @dev Base impl => Upgrade impl
    mapping(address => mapping(address => bool)) internal isUpgrade;
```
Recomendation code:

```js
/// @dev Base impl => Upgrade impl => Approved
    mapping(address => mapping(address => bool)) internal isUpgrade;
```

## [N-08] There is no need to cast a variable that is already an address, such as address(x)

**Context:**
[Governor.sol#L550](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L550)
[Governor.sol#L555](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L555)

**Description:**
There is no need to cast a variable that is already an address, such as address(auctioneer_), auctioneer also address.

**Recommendation:**
Use directly variable.

```js
contract Contract0 {
    address tokenaddr;

    function token() external returns (address) {
        return address(tokenaddr);
    }
}

//recommendation

contract Contract1 { 
    address tokenaddr;

    function token() public returns(address) {
        tokenaddr;
    }
}
```

## [N-09] Add to indexed parameter for countable Events

**Context:**
[IAuction.sol#L22](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L22)
[IAuction.sol#L28](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/IAuction.sol#L28)
[IGovernor.sol#L42](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L42)
[IGovernor.sol#L20](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/IGovernor.sol#L20)
[ITreasury.sol#L22](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/ITreasury.sol#L22)

**Recommendation:**
Add to indexed parameter for countable Events.


## [N-10] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

## [N-11] Avoid to same local variable names  declarated for code readability

**Context:**
[Token.sol#L179](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L179)
[Token.sol#L105](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L105)

**Description:**
```uint256 baseTokenId``` local variable name is used in two different functions, this causes confusion and negatively affects code readability / controllability.

```js
Token.sol#L179
    function _addFounders(IManager.FounderParams[] calldata _founders) internal {
	//code..
	uint256 baseTokenId;


Token.sol#L105
    function _isForFounder(uint256 _tokenId) private returns (bool) {
	//code..
	uint256 baseTokenId;
```

**Recommendation:**
Avoid to same local variable names  declarated for code readability.


## [N-12] Do not import more than one same contract

**Context:**
[Token.sol#L6-L7](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L6-L7)

**Description:**
In ```Token.sol``` file, ```ERC721Votes``` and ```ERC721``` contracts are imported, but ```ERC721Votes``` has already imported ```ERC721``` contract.
Therefore, there is no need to import ERC721.

```js
Token.sol:
import { ERC721Votes } from "../lib/token/ERC721Votes.sol";
import { ERC721 } from "../lib/token/ERC721.sol";

ERC721Votes.sol:
import { ERC721 } from "../token/ERC721.sol";

ERC721.sol:
import { IERC721 } from "../interfaces/IERC721.sol";
```

**Recommendation:**
```js
Token.sol:
import { ERC721Votes } from "../lib/token/ERC721Votes.sol";

ERC721Votes.sol:
import { ERC721 } from "../token/ERC721.sol";

ERC721.sol:
import { IERC721 } from "../interfaces/IERC721.sol";
```

## [N-13] Incorrect information in the document

**Context:**
[Manager.sol#L120-L129](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L120-L129)

**Description:**
[Protocol document](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/docs/protocol-docs.md) states that contracts are created with ```create2```, but contracts are created with ```new instance```, these are two different architectures, must be updated.

**Recommendation:**
Update to document.

## [N-14] Highest risk must be specified in NatSpec comments and documentation

**Description:**
Although the UUPS model has many advantages and the proposed proxy model is currently the UUPS model, there are some caveats we should be aware of before applying it to a real-world project.

One of the main attention: Since upgrades are done through the implementation contract with the help of the ```upgradeTo``` method, there is a high risk of new implementations such as excluding the ```upgradeTo``` method, which can permanently kill the smart contract's ability to upgrade. Also, this pattern is somewhat complicated to implement compared to other proxy patterns.

**Recommendation:**
Therefore, this highest risk must be found in NatSpec comments and documents.

## [N-15] Implement some type of version counter that will be incremented automatically for contract upgrades

**Description:**
As part of the upgradeability of ```UUPS Proxies```, the contract can be upgraded multiple times, where it is a systematic approach to record the version of each upgrade.

I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

**Recommendation:**
```js
uint256 public authorizeUpgradeCounter;

function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {
        // Ensure the new implementation is registered by the Builder DAO
        if (!manager.isRegisteredUpgrade(_getImplementation(), _newImpl)) revert INVALID_UPGRADE(_newImpl);
	
        authorizeUpgradeCounter+=1;
    }
```

## [N-16] Add ```castVote```  and ```castVotebySig``` in ERC721Votes

**Description:**
There are two methods by which a user can delegate their voting rights or cast votes on proposals: either calling the relevant functions __(delegate, castVote)__ directly; or using by-signature functionality __(delegateBySig, castVotebySig)__.

**Recommendation:**
The ERC721Votes contract is just delegate and delegateBySig has the function. Consider adding ```castVote``` and ```castVotebySig```.

https://medium.com/compound-finance/delegation-and-voting-with-eip-712-signatures-a636c9dfec5e


## [N-17] Empty blocks should be removed or Emit something

**Context:**
[Manager.sol#L209-L210](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L209-L210)

**Description:**
Code contains empty block.

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

_Current code:_
```js
    function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
```

_Suggested code:_
```js
    function _authorizeUpgrade(address _newImpl) internal override onlyOwner {
        emit _authorizeUpgrade(address _newImpl);
}
```

## [N-18] Long lines are not suitable for the ‘Solidity Style Guide’

**Context:**
[Token.sol#L59](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L59)
[Token.sol#L221](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L221)
[Token.sol#L310](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L310)
[Auction.sol#L376](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L376)
[Governor.sol#L27](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L27)
[Governor.sol#L363](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L363)
[Governor.sol#L620](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L620)
[Manager.sol#L167-L170](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/manager/Manager.sol#L167-L170)
[Token.sol#L310](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/token/Token.sol#L310)

**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

(why-is-80-characters-the-standard-limit-for-code-width)[https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width]


**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction

```js
thisFunctionCallIsReallyLong(
    longArgument1,
    longArgument2,
    longArgument3
);
```















