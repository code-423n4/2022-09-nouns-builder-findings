## 1.DEFAULT VALUE INITIALIZATION

### PROBLEM
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### PROOF OF CONCEPT
Instances include:

### MetadataRenderer.sol
```MetadataRenderer.sol#L119
MetadataRenderer.sol#L133
MetadataRenderer.sol#L189
MetadataRenderer.sol#L229
```

### Treasury.sol
```
Treasury.sol#L162
```


## 2.OR CONDITIONS COST LESS THAN THEIR EQUIVALENT, AND CONDITIONS (“NOT(SOMETHING IS FALSE)” COSTS LESS THAN “EVERYTHING IS TRUE”)
the equivalent of `(a && b)` is ` !(!a || !b)`

Even with the 10k Optimizer enabled, `OR` conditions cost less than their equivalent `AND` conditions.

### Proof of Concept.
 Compare in Remix this example contract’s 2 diffs (or any test contract of your choice, as experimentation always shows the same results).
```
pragma solidity 0.8.13;
contract Test {
    bool isOpen;
    bool channelPreviouslyOpen;
    
    function boolTest() external view returns (uint) {
-       if (isOpen && !channelPreviouslyOpen) { 
+       if (!(!isOpen || channelPreviouslyOpen)) { 
            return 1;
-       } else if (!isOpen && channelPreviouslyOpen) {
+       } else if (!(isOpen || !channelPreviouslyOpen)) {
            return 2;
        }
    }
    function setBools(bool _isOpen, bool _channelPreviouslyOpen) external {
        isOpen = _isOpen;
        channelPreviouslyOpen= _channelPreviouslyOpen;
    }
}
```
 effectively saving 12 gas.

### Affected Code
It’s possible to save a significant amount of gas by replacing the `&&` conditions with their `||` equivalent in the solution.
- [Governor.sol#L363](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L363)

Use `!(msg.sender == proposal.proposer || !getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)` instead of 

`(msg.sender != proposal.proposer && getVotes(proposal.proposer, block.timestamp - 1) > proposal.proposalThreshold)`

- [ERC721Votes.sol#L203](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L203)

Use `!(_from == _to || !_amount > 0)` instead of `(_from != _to && _amount > 0)`

- [ERC721.sol#L105](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L105)

Use `!(msg.sender == owner || operatorApprovals[owner][msg.sender])` instead of `(msg.sender != owner && !operatorApprovals[owner][msg.sender])`

- [ERC721.sol#L134](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L134)

Use `!(msg.sender == _from || operatorApprovals[_from][msg.sender] || msg.sender == tokenApprovals[_tokenId])` instead of `(msg.sender != _from && !operatorApprovals[_from][msg.sender] && msg.sender != tokenApprovals[_tokenId])`

- [ERC721.sol#L164](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L164)

Use ` !(!Address.isContract(_to) || ERC721TokenReceiver(_to).onERC721Received(msg.sender, _from, _tokenId, "") == ERC721TokenReceiver.onERC721Received.selector )` 
instead of 
` (Address.isContract(_to) &&ERC721TokenReceiver(_to).onERC721Received(msg.sender, _from, _tokenId, "") != ERC721TokenReceiver.onERC721Received.selector )`

-[ERC721.sol#L182](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L182)

Use `!( !Address.isContract(_to) || ERC721TokenReceiver(_to).onERC721Received(msg.sender, _from, _tokenId, _data) ==ERC721TokenReceiver.onERC721Received.selector)` 
instead of 
`(Address.isContract(_to) &&ERC721TokenReceiver(_to).onERC721Received(msg.sender, _from, _tokenId, _data) != ERC721TokenReceiver.onERC721Received.selector )`
