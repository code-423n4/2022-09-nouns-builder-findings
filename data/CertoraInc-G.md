# Gas Optimizations
* The [`_settleAuction` function](https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L172) uses the storage variable `auction` instead of using the cached variable `_auction` 
    ```
    if (auction.settled) revert AUCTION_SETTLED();
    ```
* Use the calldata modifier instead of the memory modifier on struct and array arguments to save gas. For example, the `propose` function receives multiple array arguments, which are passed as memory arguments and can be passed as calldata to save gas.
    ```sol
    function propose(
        address[] memory _targets,
        uint256[] memory _values,
        bytes[] memory _calldatas,
        string memory _description
    ) external returns (bytes32) {
    ```