## Low Risk Findings

### Governor function `burnVetoer` can be reversed by `updateVetoer`

`burnVetoer` doesn't permanently "burn" or remove vetoing from a DAO. The following code when executed shows the vetoer can be restored:
```
    function testBurnVetoer() public {
        vm.startPrank(address(treasury));
        address oldVetoer = governor.vetoer();
        console.log("vetor %s", governor.vetoer());
        governor.burnVetoer();
        console.log("vetor %s", governor.vetoer());
        governor.updateVetoer(oldVetoer);
        console.log("vetor %s", governor.vetoer());
    }
```

This might be desired but I think it will cause confusion as users might assume the "burn" is permanent for a couple of reasons:
- in NounsDAO the vetoer is removed forever. see `NounsDAOLogicV2._burnVetoPower()` .
- the term "burn" generally means removed forever. an example is burning a token on an ERC721 contract. it can never be transferred or owned again.

 
Consider either:
1) Make it a permanent burn by a flag that updateVetoer could check. For example `require(vetoerRemoved, "vetoer can not be updated after it has been removed");`

or if the ability to restore a vetoer is desired then:

2) change the terminology to `removeVetoer` or something like this that does not use "burn". This should remove the risk of misunderstandings.

