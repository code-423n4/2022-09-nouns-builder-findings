## possible dos with uints and  uint32 and block.timestamp

1. since `snapshot` is uint256 and is unchecked the value where block.timestamp is large and voting delay is large and so the uint32 is overflowed but since its uint256 its ok but  then when its  casting it overflows which makes it zero  which can cause loss of funds because the   `VoteStart` and `voteEnd` would be zero  and which which shouldnt happen

```js
        uint256 snapshot;
        uint256 deadline;

        // Cannot realistically overflow
        unchecked {
            // Compute the snapshot and deadline
            snapshot = block.timestamp + settings.votingDelay;
            deadline = snapshot + settings.votingPeriod;
        }
        // Store the proposal data
        proposal.voteStart = uint32(snapshot);
        proposal.voteEnd = uint32(deadline);
        proposal.proposalThreshold = uint32(currentProposalThreshold);
        proposal.quorumVotes = uint32(quorum());
        proposal.proposer = msg.sender;
        proposal.timeCreated = uint32(block.timestamp);
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/governor/Governor.sol#L149-L166
### reccomend 
to make sure that deadline and snapshot < uint32(max)
or make it bigger types like uint96 to make it more future proof.
## make sure that you can change args for  `manager.sol->deploy()` and if not have comments to show what you should do if you cant change variables
 if the deployer  makes bad init then attacker can take advantage for it or cause the depolyer a loss of gas.
 https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/manager/Manager.sol#L97
 ## there is no need to make your contstuctor payable and initilzer  because if the depolyer sends eth with the constuctor then  in some contracts the funds will be lost
ex:manager.sol where there is no way to geting the eth out.
```
./src/auction/Auction.sol:39:    constructor(address _manager, address _weth) payable initializer {
./src/governance/governor/Governor.sol:41:    constructor(address _manager) payable initializer {
./src/governance/treasury/Treasury.sol:32:    constructor(address _manager) payable initializer {
./src/lib/proxy/ERC1967Proxy.sol:21:    constructor(address _logic, bytes memory _data) payable {
./src/manager/Manager.sol:55:    constructor(
./src/token/metadata/MetadataRenderer.sol:32:    constructor(address _manager) payable initializer {
./src/token/Token.sol:30:    constructor(address _manager) payable initializer {
```
## There is  centralization risk  on functions that the action contract which is used at deployment can be any contract or address and then burn what ever token the action address wants 

ex: Token.sol->burn : 207
```
./src/token/Token.sol:207:    function burn(uint256 _tokenId) external {
```
## An attacker can guess the random seed beause of how the blockchain works 
when we compute the random hash we use  this:
everything in this function is guess able by the miner so  the attacker/miner can get what every seed they want and possiblity get the most expensive seed.
```js
 return uint256(keccak256(abi.encode(_tokenId, blockhash(block.number), block.coinbase, block.timestamp)));
```
you can have a a miner guess the tokenid an blockhash because its only 256 blocks of hashs before and block.coinbase and block.timestamp is all guessable so a miner can figure out what seed someone is getting and they can frontrun it and get the higher seed then someone else
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L251

## owner can dos someone else  token when they minted by dosing the for loop in `onMinted()`
the attacker/owner `addProperties` and pushes alot of stuff on to the properites and makes it so high that when a user mints it will dos and cost alot of gas 
```js
    for (uint256 i = 0; i < numProperties; ++i) {
                // Get the number of items to choose from
                uint256 numItems = properties[i].items.length;
```
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L171

## make max and  min for delay becaue you can end up making the functions dossed becaue there is to much of delay like uint128 is more 6x more then block.timestamp meaning you can put a delay that will cause the system to never execute  

```
  function updateDelay(uint256 _newDelay) external {
        // Ensure the caller is the treasury itself
        if (msg.sender != address(this)) revert ONLY_TREASURY();

        emit DelayUpdated(settings.delay, _newDelay);

        // Update the delay
        settings.delay = SafeCast.toUint128(_newDelay);
    }

```
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L210
same thing with setting.gracePeriod 
 there is a possiblity of settings.gracePeriod getting set to high the queue will never expire
Treasury.sol:67

## there is also a change  that a  high number for  timestamp which then will  overflow and then it will never expire because of `unchecked`

```js

     unchecked {
            return block.timestamp > (timestamps[_proposalId] + settings.gracePeriod);
        }

```

because `timestamps[_proposalId]=uint256` + graceperiod then it will never reach block.timestamp and that is one issue where it will always expire 
but if it is a big enough number then it will oveflow and be zero and always beable to execute.
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/governance/treasury/Treasury.sol#L74-L77
## there is no way for updateDelay to get called

because since `updateDelay` is not called inside the contract and the check you have to pass is `msg.sender != address(this)` and since it will always pass,this address can't call it.
either use this function for owner or manager/ take out the function.
Treasury.sol:211,223
## in frontend  it says qurom is 10% but its in base points which is alot lower just make sure the calacution is true

Governor.sol
## there is no input validation on `initilaize` which if protocols are not tought on best practices for Goverance.sol they can be rekt

## when project gets bigger

  only  goes up to 65,535 and if the project gets bigger then this will be  problem and you will be easly be able to pass a proposal
        if a user makes 65,000 and then the value never changes and there is alot of tokens then the system might get dosed  easily with proposals coming in every block

``` js
        settings.proposalThresholdBps = SafeCast.toUint16(_proposalThresholdBps);
        settings.quorumThresholdBps = SafeCast.toUint16(_quorumThresholdBps);
```

## if no verfiaction for some sort on eip-721 then there can be a pishing attack

Governor.sol:95

```js
   __EIP712_init(string.concat(settings.token.symbol(), " GOV"), "1");


```
## block.timestamp will overflow in 2106 make it unchecked to account for that

like uniswap they use unchecked block for block.timesmtap in smart contract solidity verision ^0.8.0
Governor.sol:144

```js
 if (getVotes(msg.sender, block.timestamp - 1) < proposalThreshold()) 

```
## there is possiblity that `_description` is more then 32 bytes

Which can cause a the `proposal` description to be cut off and can lead to issues.
Governor.sol:144
```js
       bytes32 descriptionHash = keccak256(bytes(_description));
```
## if a user makes proposal.voteStart zero because of overflow 
then it can dos the governace and cause fund loss which 
then an attacker can make `proposal.proposer=attacker`
because of how snaphsot is uint256 trying to fix into a uint32 and it can be onwers fault that votingDelay or votingPeriod is to high and that can cause the overflow 
so just be carefull of what the depolyer makes `settings.votingDelay and setting.VotingPeriod`
```js


        unchecked {
            // Compute the snapshot and deadline
            snapshot = block.timestamp + settings.votingDelay;
            deadline = snapshot + settings.votingPeriod;
        }
        // Store the proposal data
        proposal.voteStart = uint32(snapshot);
        proposal.voteEnd = uint32(deadline);
```
## you should make block.timestmap into block.number its just more ressetient 
Govovner.sol:

## if  a voter has alot of votes then their  vote wont count and they will not be able to vote and they will loss out on their vote

because `getVotes->getpastVotes` for an account and timestamp which it wont get its current votes and it will be off so it is possible after an amount of time that you still have votes becaues of using `pastVotes`
steps:
attacker sells of his votes (tokens).
but since its using `getPastVotes` the attacker will still have votes when in the current block and timestmap they shouldnt,causing a break of logic
Govoner.sol:296
```js
 function getVotes(address _account, uint256 _timestamp) public view returns (uint256) {
        return settings.token.getPastVotes(_account, _timestamp);
    }

```
## the excute function will revert

because of `execute->execute` will revert because in treasury.sol  its only called by owner otherwise it will revert.
##  if sent eth into this function it will be lost in governace conract  and there is no check if proposal has enough votes 

because it only sends eth from the treasury not the goverance contract 
and there is no check in execute that there is enough votes to pass 
```js
 if (state(proposalId) != ProposalState.Queued) revert PROPOSAL_NOT_QUEUED(proposalId);

        // Mark the proposal as executed
        proposals[proposalId].executed = true;

        // Execute the proposal
        settings.treasury.execute{ value: msg.value }(_targets, _values, _calldatas, _descriptionHash);




```
Governance.sol:352
## there is a chance  that an attacker can cause any proposal to get canceled

```js
    unchecked {
            // Ensure the caller is the proposer or the proposer's voting weight has dropped below the proposal threshold
            if (msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)
                revert INVALID_CANCEL();
        }
```

because of unchecked when block.timestamp is overflowed you can cancel any  proposal
because if getVotes(proposer,uint32.max) if that the case and there is a possiblity that it can pass and if proposer sells of and dosnt hit the Threshold which can be anytime and a attacker can cancel any proposal.
Governance.sol:377

## centralization issues with onlyOwner and can change any variable

```js
   function burnVetoer() external onlyOwner {
        emit VetoerUpdated(settings.vetoer, address(0));

        delete settings.vetoer;
    }

```

Governance.sol:591,607,583

## `minBid` is to strict and can cause users fund lost

// minBid = 1 ether + (1e18* 100) / 100=2e18
 and minBid is wrong calculation its to strict of calculations
 it shouldn't be a whole 1 eth more and there is no docs of why not just make it 0.5 eth more

```js

  unchecked {
                // Compute the minimum bid
                minBid = highestBid + ((highestBid * settings.minBidIncrement) / 100);
            }
```

Governance.sol:116
## attacker can make highestBidder from a smart contract but then when they make a call to it and they would make it revert so no body else can bid on it and they can steal funds

```js
       _handleOutgoingTransfer(highestBidder, highestBid);
```

and they can make that call into the fallback and make it revert so they get the auction item for a very little funds 
funds can get lost and its brakes the whole auction idea
```js
       assembly {
            // Transfer ETH to the recipient
            // Limit the call to 50,000 gas
            success := call(50000, _to, _amount, 0, 0, 0, 0)
        }

```



## there is possibility for an auction to not stop

and this can be an issue
also there is no way onlyOwner to change it

```js
                auction.endTime = uint40(block.timestamp + settings.timeBuffer);
            }
```
## an attacker can on purpose  cause the auction contract to pause 
 if an attacker  can make the minting revert then pause the functionality which is flawed and should instead not trust that an attacker just made it happen because he tried reentering and since of the lock it goes to the `catch` statement
https://github.com/code-423n4/2022-09-nouns-builder/blob/debe9b792cc70510eadf9b3728cde5b0f2ec9a1f/src/token/metadata/MetadataRenderer.sol#L171
## `endTime`can be a hugh number and then the `action.endTime` will overflow and be zero 
and it can cause loss of funds.
```js
  // Cache the current timestamp
            uint256 startTime = block.timestamp;

            // Used to store the auction end time
            uint256 endTime;

            // Cannot realistically overflow
            unchecked {
                // Compute the auction end time
                // uint 40+uint32 at most = uint72 in uint256
                endTime = startTime + settings.duration;
            }

            // Store the auction start and end time
            auction.startTime = uint40(startTime);
            auction.endTime = uint40(endTime);
```
## an eoa account can be true and fail sielnetly in a call and then the bidder wont get there funds.
if eoa call is true and it failed and then you try to get weth but since `seccess=true` you wont get your funds.
```js


  assembly {
            // Transfer ETH to the recipient
            // Limit the call to 50,000 gas
            success := call(50000, _to, _amount, 0, 0, 0, 0)
        }

        // If the transfer failed:
        if (!success) {
            // Wrap as WETH
            IWETH(WETH).deposit{ value: _amount }();

            // Transfer WETH instead
            IWETH(WETH).transfer(_to, _amount);
        }
    }


```
governance.sol:351
## in `UUPS.sol`  `_self` and `address(this)` and since this is true the onlyProxy modifer will always revert.
```js
    modifier onlyProxy() {
        if (address(this) == __self) revert ONLY_DELEGATECALL();
        if (_getImplementation() != __self) revert ONLY_PROXY();
        _;
    }

```
## issue with initializer function ,its high risk but out of scope
You can initialize again even after its deployed and the attacker can steal funds with changing  initilizion function
 an attacker can call  initialize   even if your not owner and since  its works like this ex:
lets say isTopLevelCall=true || true && true || false = false so the whole statement  is false because of && and `  _initialized != 1` makes this vulnerable to issues 
lock it like re-entrancy  this whole funcioninlllity like this is wrong 
steps:
deployer initializes in auction.sol:initialize 
which makes `isTopLevelCall=true` and `_initializing=true`
then when the attacker calls it 
`isTopLevelCall=false`
and it will be true || true && true  || false which will be true && false =false and it wont revert causing an attacker to inilitiaze again.
`        if ((!isTopLevelCall || _initialized != 0) && (Address.isContract(address(this)) || _initialized != 1)) revert ALREADY_INITIALIZED();`
right now it not exploitable beacuse of manager check but if in the future you dont do this it wll be vunlernable 

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v4.7/contracts/proxy/utils/Initializable.sol
they dont do `_initilize!=1` 

*/
```js
    modifier initializer() {
        bool isTopLevelCall = !_initializing;

        if ((!isTopLevelCall || _initialized != 0) && (Address.isContract(address(this)) || _initialized != 1)) revert ALREADY_INITIALIZED();

        _initialized = 1;

        if (isTopLevelCall) {
            _initializing = true;
        }

        _;

        if (isTopLevelCall) {
            _initializing = false;

            emit Initialized(1);
        }
    }
```
<https://github.com/code-423n4/2022-09-nouns-builder/blob/c682e196982ca6953fe9fcb85df02748795394fd/src/lib/utils/Initializable.sol#L48>
