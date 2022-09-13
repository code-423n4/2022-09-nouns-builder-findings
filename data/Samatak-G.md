## **`++I` COSTS LESS GAS THAN `I++`**

Saves **6 gas**

*There are 6 instances of this issue:*

```solidity
File: governor/Governor.sol
230:    keccak256(abi.encode(VOTE_TYPEHASH, _voter, _proposalId, _support, nonces[_voter]++, _deadline))
```

```solidity
File: token/Token.sol
91:     uint256 founderId = settings.numFounders++;
154:    tokenId = settings.totalSupply++;       
```

```solidity
File: src/lib/token/ERC721Votes.sol
162:    abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, _from, _to, nonces[_from]++, _deadline)))
207:    uint256 nCheckpoints = numCheckpoints[_from]++;
222:    uint256 nCheckpoints = numCheckpoints[_to]++;
```

---

## **USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS**

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

*There is 1 instance of this issue:*

```solidity
File: governor/Governor.sol
l27:    bytes32 public constant VOTE_TYPEHASH = keccak256("Vote(address voter,uint256 proposalId,uint256 support,uint256 nonce,uint256 deadline)");
```

---

## **`PUBLIC` FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED `EXTERNAL` INSTEAD**

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from `external` to `public`and can save gas by doing so.

*There is 13 instances of this issue:*

```solidity
File: src/lib/token/ERC721.sol
54:     function tokenURI(uint256 _tokenId) public view virtual returns (string memory)
57:     function contractURI() public view virtual returns (string memory)
83:     function balanceOf(address _owner) public view returns (uint256)
91:     function ownerOf(uint256 _tokenId) public view returns (address)
```

```solidity
File: src/lib/token/ERC721Votes.sol
45:     function getVotes(address _account) public view returns (uint256)
59:     function getPastVotes(address _account, uint256 _timestamp) public view returns (uint256)
```

```solidity
File: src/lib/utils/EIP712.sol
63:     function DOMAIN_SEPARATOR() public view returns (bytes32)
```

```solidity
File: src/lib/utils/Ownable.sol
52:    function owner() public view returns (address)
57:    function pendingOwner() public view returns (address)
63:    function transferOwnership(address _newOwner) public onlyOwner
71:    function safeTransferOwnership(address _newOwner) public onlyOwner
78:    function acceptOwnership() public onlyPendingOwner
87:    function cancelOwnershipTransfer() public onlyOwner
```

---

## `OR` CONDITIONS COST LESS THAN THEIR EQUIVALENT `AND` CONDITIONS (“NOT(SOMETHING IS FALSE)” COSTS LESS THAN “EVERYTHING IS TRUE”)

Remember that the equivalent of `(a && b)` is `!(!a || !b)`

Even with the 10k Optimizer enabled: `OR` conditions cost less than their equivalent `AND` conditions.

*There is 6 instances of this issue:*

```solidity
File: src/governance/governor/Governor.sol
363: - if (msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)
     + if (!(msg.sender == proposal.proposer || getVotes(proposal.proposer, block.timestamp - 1) <= proposal.proposalThreshold))
```

```solidity
File: src/lib/token/ERC721.sol
105: - if (msg.sender != owner && !operatorApprovals[owner][msg.sender])
     + if (!(msg.sender == owner || operatorApprovals[owner][msg.sender])

134: - if (msg.sender != _from && !operatorApprovals[_from][msg.sender] && msg.sender != tokenApprovals[_tokenId])
     + if (!(msg.sender == _from || operatorApprovals[_from][msg.sender] || msg.sender == tokenApprovals[_tokenId]))

165: - if (Address.isContract(_to) && ERC721TokenReceiver(_to).onERC721Received(msg.sender, _from, _tokenId, "") != ERC721TokenReceiver.onERC721Received.selector)
     + if (!(!Address.isContract(_to) || ERC721TokenReceiver(_to).onERC721Received(msg.sender, _from, _tokenId, "") == ERC721TokenReceiver.onERC721Received.selector)

183: - if (Address.isContract(_to) && ERC721TokenReceiver(_to).onERC721Received(msg.sender, _from, _tokenId, _data) != ERC721TokenReceiver.onERC721Received.selector)
     + if (!(!Address.isContract(_to) || ERC721TokenReceiver(_to).onERC721Received(msg.sender, _from, _tokenId, _data) == ERC721TokenReceiver.onERC721Received.selector)
```

```solidity
File: src/lib/token/ERC721Votes.sol
203: - if (_from != _to && _amount > 0)
     + if (!(_from == _to || _amount == 0)
```

The negation of `_amount > 0` would normally be `_amount <= 0` but as `_amount` can’t be negative, if changed it to `_amount == 0`