# Index
1. Post-increment/decrement cost more gas then pre-increment/decrement
2. Call to KECCAK256 should use IMMUTABLE rather than constant
3. Operatos <= or >= cost more gas than operators < or >
4. != 0 is cheaper than > 0
5. Variable1 = Variable1 + (-) Variable2 is cheaper in gas cost than variable1 += (-=) variable2.
6. Using private rather than public for constants, saves gas
7. Using bools for storage incurs overhead
8. ABI.ENCODE() is less efficient than ABI.ENCODEPACKED()
9. Multiplication/division by two should use bit shifting

# Details
## 1. Post-increment/decrement cost more gas then pre-increment/decrement
### Description
++I (--I) cost less gas than I++ (I--) especially in a loop.

### Proof of concept

```solidity
contract TestPost {
	function testPost() public {
		uint256 i;
		i++;
	}
}
contract TestPre {
	function testPre() public {
		uint256 i;
		++i;
	}
}
```

- Transaction cost of testPost is 21333 gas
- Transaction cost of testPre is 21328 gas 
- After the test it's possible to save **5 gas per ocurrence** with this optimization.


### Lines in the code
[Token.sol#L91](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L91)
[Token.sol#L154](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L154)
[Governor.sol#L230](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L230)
[ERC721Votes.sol#L162](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L162)
[ERC721Votes.sol#L207](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L207)
[ERC721Votes.sol#L222](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L222)

## 2. Call to KECCAK256 should use IMMUTABLE rather than constant
### Description
Expressions for constant values such as a call to KECCAK256 should use IMMUTABLE rather than constant

### Lines in the code
[Governor.sol#L27](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L27)
[ERC721Votes.sol#L21](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L21)
[EIP712.sol#L19](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L19)

## 3. Operatos <= or >= cost more gas than operators < or >
### Description
Change all <= / >= operators for < / > and remember to increse / decrese in consecuence to maintain the logic (example, a <= b for a < b + 1)

### Proof of concept

```solidity
contract TestMaxEqual {

	function testMaxEqual() public {
		uint256 i = 1;
		if (i >= 1){
			i++;
		}
	}
}

contract TestMax {

	function TestMax() public {
		uint256 i = 1;
		if (i > 0){
			i++;
		}
	}
}
```

- Transaction cost of TestMaxEqual is 21367 gas
- Transaction cost of TestMax is 21364 gas 
- After the test it's possible to save **3 gas per ocurrence** with this optimization.

### Lines in the code
[Auction.sol#L98](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L98)
[Treasury.sol#L89](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L89)
[ERC721Votes.sol#L61](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L61)
[ERC721Votes.sol#L78](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L78)
[Initializable.sol#L56](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L56)

## 4. != 0 is cheaper than > 0
### Description
Replace all > 0 for != 0

### Lines in the code
[ERC1967Upgrade.sol#L61](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/proxy/ERC1967Upgrade.sol#L61)
[ERC721Votes.sol#L203](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L203)
[Address.sol#L50](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L50)

## 5. Variable1 = Variable1 + (-) Variable2 is cheaper in gas cost than variable1 += (-=) variable2.

### Lines in the code
[Token.sol#L88](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L88)
[Token.sol#L118](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/Token.sol#L118)
[Governor.sol#L280](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L280)
[Governor.sol#L285](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L285)
[Governor.sol#L290](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L290)
[MetadataRenderer.sol#L140](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L140)

## 6. Using private rather than public for constants, saves gas
### Description
If needed, the value can be read from the verified contract source code. 
Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, 
and not adding another entry to the method ID table.

### Lines in the code
[Governor.sol#L27](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L27)

## 7. Using bools for storage incurs overhead
### Description
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) 
when changing from 'false' to 'true', after having been 'true' in the past

### Lines in the code
[Auction.sol#L136](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L136)
[Auction.sol#L349](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L349)
[AuctionTypesV1.sol#L35](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/types/AuctionTypesV1.sol#L35)
[Treasury.sol#L164](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L164)
[Address.sol#L40](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Address.sol#L40)
[Initializable.sol#L20](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L20)
[Initializable.sol#L34](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Initializable.sol#L34)
[Pausable.sol#L15](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/Pausable.sol#L15)
[MetadataRenderer.sol#L231](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L231)
[GovernorTypesV1.sol#L52](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/types/GovernorTypesV1.sol#L52)
[GovernorTypesV1.sol#L53](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/types/GovernorTypesV1.sol#L53)
[GovernorTypesV1.sol#L54](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/types/GovernorTypesV1.sol#L54)
[MetadataRendererTypesV1.sol#L11](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/types/MetadataRendererTypesV1.sol#L11)

## 8. ABI.ENCODE() is less efficient than ABI.ENCODEPACKED()

### Lines in the code

[Governor.sol#L104](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L104)
[Governor.sol#L230](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L230)
[Treasury.sol#L107](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L107)
[EIP712.sol#L69](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/utils/EIP712.sol#L69)
[MetadataRenderer.sol#L251](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L251)
[MetadataRenderer.sol#L309](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/token/metadata/MetadataRenderer.sol#L309)

## 9. Multiplication/division by two should use bit shifting
### Description
<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

### Lines in the code
[ERC721Votes.sol#L95](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/lib/token/ERC721Votes.sol#L95)

