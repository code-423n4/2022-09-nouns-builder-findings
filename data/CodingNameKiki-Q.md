1. Bool success can be better applied in the execute() function.

It is better choice for the function to use return statement (bool success), instead of adding the bool variable out of nowhere:
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L164

Recommend: Applying return statement on the fuction execute() and replacing the "bool success" in the fuction only with "success".
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L141-L146

(Before) 141: function execute( address[] calldata _targets, uint256[] calldata _values, bytes[] calldata _calldatas, bytes32 _descriptionHash) external payable onlyOwner {
(After) 141: function execute( address[] calldata _targets, uint256[] calldata _values, bytes[] calldata _calldatas, bytes32 _descriptionHash) external payable onlyOwner returns (bool success){

(Before) 164: (bool success, ) = _targets[i].call{ value: _values[i] }(_calldatas[i]);
(After) 164: (success, ) = _targets[i].call{ value: _values[i] }(_calldatas[i]);

https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/ITreasury.sol#L107-L112
(Before) 107: function execute( address[] calldata targets, uint256[] calldata values, bytes[] calldata calldatas, bytes32 descriptionHash) external payable;
(After) 107: function execute( address[] calldata targets, uint256[] calldata values, bytes[] calldata calldatas, bytes32 descriptionHash) external payable returns (bool success);


