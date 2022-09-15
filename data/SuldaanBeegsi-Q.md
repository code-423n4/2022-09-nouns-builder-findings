Handle 
SuldaanBeegsi

I have found two findings that would be in the interest of the developers 
of this project.
#1
Governer.sol line 34
IManager private immutable manager;
Given that this is a protocol which is supposed to be a template for other
projects. These project are mostly new to solidity or are more experienced
with other coding languages. Where private has an other meaning. Giving that nothing 
is private in the blockchain and all variables/data can be seen by anyone. 
It should either not use private since it gives a falls sense of security 
to the developer. An other solution is to wright a comment above about 
the what the keyword private means in solidity.

#2
Manger.sol line 167-171


metadata = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, metadataHash)))));
        auction = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, auctionHash)))));
        treasury = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, treasuryHash)))));
        governor = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xff), address(this), salt, governorHash)))));
    }

With the use of abi.encodePacked the dev is able to shorten the hash and use less data. But at
the price of risking a hash collision. I see that the developers have taken measure to avoid
know vulnerability, but  given that we have to have the view point
of longevity in coding a smart contract template. The use of abi.enconde will be a
better alternative given that future attackers may find a way to collide the hash in
abi.encodePacked.