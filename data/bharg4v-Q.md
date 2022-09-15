# QA REPORT 
## Number of Issues: 2
## Issues
### 1. Calls inside a loop
### 2. Missing zero address validation
### 3. Reentrancies leading to out-of-order events.
### 4. Poor usage of Block timestamp
### 5. Low-level calls
### 6. Conformance to Solidity naming conventions
### 7. Public function that could be declared external


# 1. Calls inside a loop

## Impact
Calls inside a loop might lead to a denial-of-service attack.

## Proof of Concept
```
Treasury.execute(address[],uint256[],bytes[],bytes32) (src/governance/treasury/Treasury.sol#141-172) has external calls inside a loop: (success) = _targets[i].call{value: _values[i]}(_calldatas[i]) (src/governance/treasury/Treasury.sol#164)                                                                                                                                                                                                                                          Token._mint(address,uint256) (src/token/Token.sol#167-173) has external calls inside a loop: ! settings.metadataRenderer.onMinted(_tokenId) (src/token/Token.sol#172)  
```


# 2. Missing zero address validation


## Proof of Concept

```
Manager.constructor(address,address,address,address,address)._auctionImpl (src/manager/Manager.sol#58) lacks a zero-check on :
                - auctionImpl = _auctionImpl (src/manager/Manager.sol#64)
Manager.constructor(address,address,address,address,address)._treasuryImpl (src/manager/Manager.sol#59) lacks a zero-check on :
                - treasuryImpl = _treasuryImpl (src/manager/Manager.sol#65)
Manager.constructor(address,address,address,address,address)._governorImpl (src/manager/Manager.sol#60) lacks a zero-check on :
                - governorImpl = _governorImpl (src/manager/Manager.sol#66)
```


# 3. Reentrancies leading to out-of-order events.

## Proof of Concept
```
Reentrancy in Manager.deploy(IManager.FounderParams[],IManager.TokenParams,IManager.AuctionParams,IManager.GovParams) (src/manager/Manager.sol#97-147):                                                                                              External calls:                                                                                                                                                                                                                              - token = address(new ERC1967Proxy(tokenImpl,)) (src/manager/Manager.sol#120)                                                                                                                                                                - metadata = address(new ERC1967Proxy(metadataImpl,)) (src/manager/Manager.sol#126)                                                                                                                                                          - auction = address(new ERC1967Proxy(auctionImpl,)) (src/manager/Manager.sol#127)                                                                                                                                                            - treasury = address(new ERC1967Proxy(treasuryImpl,)) (src/manager/Manager.sol#128)                                                                                                                                                          - governor = address(new ERC1967Proxy(governorImpl,)) (src/manager/Manager.sol#129)                                                                                                                                                          - IToken(token).initialize(_founderParams,_tokenParams.initStrings,metadata,auction) (src/manager/Manager.sol#132)                                                                                                                           - IBaseMetadata(metadata).initialize(_tokenParams.initStrings,token,founder,treasury) (src/manager/Manager.sol#133)                                                                                                                          - IAuction(auction).initialize(token,founder,treasury,_auctionParams.duration,_auctionParams.reservePrice) (src/manager/Manager.sol#134)                                                                                                     - ITreasury(treasury).initialize(governor,_govParams.timelockDelay) (src/manager/Manager.sol#135)                                                                                                                                            - IGovernor(governor).initialize(treasury,token,founder,_govParams.votingDelay,_govParams.votingPeriod,_govParams.proposalThresholdBps,_govParams.quorumThresholdBps) (src/manager/Manager.sol#136-144)                                      Event emitted after the call(s):                                                                                                                                                                                                             - DAODeployed(token,metadata,auction,treasury,governor) (src/manager/Manager.sol#146)  
```

# 4. Poor usage of Block timestamp
## Impact
Dangerous usage of block.timestamp. block.timestamp can be manipulated by miners.
## Proof of Concept
```
Treasury.isExpired(bytes32) (src/governance/treasury/Treasury.sol#74-78) uses timestamp for comparisons                                                                                                                                              Dangerous comparisons:                                                                                                                                                                                                                       - block.timestamp > (timestamps[_proposalId] + settings.gracePeriod) (src/governance/treasury/Treasury.sol#76)                                                                                                                       Treasury.isQueued(bytes32) (src/governance/treasury/Treasury.sol#82-84) uses timestamp for comparisons                                                                                                                                               Dangerous comparisons:                                                                                                                                                                                                                       - timestamps[_proposalId] != 0 (src/governance/treasury/Treasury.sol#83)
```

# 5. Low-level calls
## Proof of Concept
```
(success) = _targets[i].call{value: _values[i]}(_calldatas[i]) (src/governance/treasury/Treasury.sol#164)  
```

# 6. Conformance to Solidity naming conventions
## Proof of Concept
```
Parameter Token.tokenURI(uint256)._tokenId (src/token/Token.sol#221) is not in mixedCase                                                                                                                                                     Parameter Token.getFounder(uint256)._founderId (src/token/Token.sol#246) is not in mixedCase                                                                                                                                                 Parameter Token.getScheduledRecipient(uint256)._tokenId (src/token/Token.sol#270) is not in mixedCase 
```

# 7. Public function that could be declared external
## Proof of Concept
```
onERC721Received(address,address,uint256,bytes) should be declared external:                                                                                                                                                                         - Treasury.onERC721Received(address,address,uint256,bytes) (src/governance/treasury/Treasury.sol#237-244)                                                                                                                            onERC1155Received(address,address,uint256,uint256,bytes) should be declared external:                                                                                                                                                                - Treasury.onERC1155Received(address,address,uint256,uint256,bytes) (src/governance/treasury/Treasury.sol#247-255)                                                                                                                   onERC1155BatchReceived(address,address,uint256[],uint256[],bytes) should be declared external:
```

