# Unsafe Transfer

The following lines are using  unsafe ways to transfer tokens
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L192
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L363

# External Calls Inside a Loop
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/governance/treasury/Treasury.sol#L164
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation/#calls-inside-a-loop

# Lacks of zero-checks
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/manager/Manager.sol#L62-L66
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#missing-zero-address-validation

# Ignoring the return from .transfer/.deposit
https://github.com/code-423n4/2022-09-nouns-builder/blob/7e9fddbbacdd7d7812e912a369cfd862ee67dc03/src/auction/Auction.sol#L360-L363

