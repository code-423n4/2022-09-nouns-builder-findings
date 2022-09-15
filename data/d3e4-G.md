**Unnecessary function `burnVetoer`**
The function [`burnVetoer`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L605-L609) sets the vetoer to the zero-address. `burnVetoer` may be removed and its function performed by calling [`updateVetoer`](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L596-L602) with the zero-address as the parameter, if the check to revert on zero-address was removed from `updateVetoer`.

**Put boolean calculation directly in if-condition**
The declaration and calculation of `extend` can be removed and the boolean statement in line 141 can be put directly in the if-condition in line 145. The entire if-block should then be in an unchecked bracket.
[Auction.sol#L135-L151](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L135-L151)

**Redundant else-if**
[Governor.sol#L288](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L288) can simply be an `else` as `_support` cannot be but `2` after having passed the previous check at line [261](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/governor/Governor.sol#L261)

**`<` is cheaper than `<=` or `>=`**
Instances:
Auction.sol: [98](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L98)