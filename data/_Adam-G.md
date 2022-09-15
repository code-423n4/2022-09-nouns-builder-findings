### [G01] x = x + y is Cheaper than x += y

Based on test in remix you can save ~1,007 gas on deployment and ~15 gas on execution cost if you use x = x + y over x += y (Is only true for Storage Variables).

```
contract Test {
	uint256 x = 1;
	function test() external {
		x += 3; 
		(Deployment Cost: 153,124, Execution Cost: 30,369)
		vs
		x = x + 1;
		(Deployment Cost: 152,117, Execution Cost: 30,354)
	}

}
```

Instances where x = x + y/ x = x - y can be used:
[Governor.sol#L280](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L280) 
[Governor.sol#L285](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L285) 
[Governor.sol#L290](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L290) 

### [G02] Packing Variables in Structs

Both ownershipPct & vestExpiry are cast down to uints < 256 when they are used throughout the contracts, so we can safely change both to uint128 and condense into 1 storage slot.

[IManager.sol#L50-L51](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/IManager.sol#L50-L51)