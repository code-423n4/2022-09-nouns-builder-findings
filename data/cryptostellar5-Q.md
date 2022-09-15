## L-01 REPLACE INLINE ASSEMBLY WITH ACCOUNT.CODE.LENGTH

`<address>.code.length` can be used in Solidity >= 0.8.0 to access an account’s code size and check if it is a contract without inline assembly.

[/src/lib/utils/Address.sol#L31-L33](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/utils/Address.sol#L31-L33)

```
assembly {

rv := gt(extcodesize(_account), 0)

}
```


## L-02  NO EVENTS ARE RAISED

All important functions wherever some change is happening need to raise events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

Missing events and timelocks do not promote transparency and if such changes immediately affect users’ perception of fairness or trustworthiness, they could exit the protocol causing a reduction in liquidity which could negatively impact protocol TVL and reputation.


[Auction.sol#L374-L376](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L374-L376), [Governor.sol#L618-L621](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L618-L621), [Treasury.sol#L278-L283](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/treasury/Treasury.sol#L278-L283) 

```
function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {

        // Ensure the new implementation is registered by the Builder DAO //@audit-info event not

        if (!manager.isRegisteredUpgrade(_getImplementation(), _newImpl)) revert INVALID_UPGRADE(_newImpl);

    }
```



## L-03 FLOATING PRAGMA VERSION SHOULD NOT BE USED

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

[ERC1967Proxy.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Proxy.sol#L2) , [UUPS.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/UUPS.sol#L2), [ERC1967Upgrade.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/proxy/ERC1967Upgrade.sol#L2), [ERC721.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721.sol#L2), [ERC721Votes.sol#L2](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/lib/token/ERC721Votes.sol#L2)

```
pragma solidity ^0.8.4;
```


### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)



## N-01 USE LATEST SOLIDITY VERSION
When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17
This applies for all the smart contracts in scope


## N-02 REDUNDANT FUNCTIONS

Several contracts define exactly the same function

[Auction.sol#L374-L376](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/auction/Auction.sol#L374-L376), [Governor.sol#L618-L621](https://github.com/code-423n4/2022-09-nouns-builder/blob/main/src/governance/governor/Governor.sol#L618-L621) 