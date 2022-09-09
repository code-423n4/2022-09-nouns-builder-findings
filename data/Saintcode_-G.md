## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

19 Instances:
-Governance.sol lines 99, 100, 101, 102, 117, 118, 119, 120
-ERC1967Proxy.sol line 21
-ERC1967Upgrade.sol lines 35, 56
-UUPS.sol line 55
-Address.sol lines 37, 46
-MetadataRenderer.sol lines 255, 308, 347, 355, 363

## USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another memory array/struct

6 Instances:
-Governor.sol lines 358, 415, 508
-MetadataRenderer.sol 216, 234, 240


## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
3 Instances:
-Governor.sol lines 280, 285, 290 


## IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

5 Instances: 
-Treasury.sol line 162
-MetadataRenderer.sol lines 119, 133, 189, 229


## `>=` COSTS LESS GAS THAN `>`

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

3 Instances:
-Governor.sol line 261
-ERC721Votes.sol line 203
-Address.sol line 50



## `++I/I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
`
   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           
           unchecked { ++i; }   
}  `

8 Instances:
-Treasury.sol line 162
-Token.sol lines 80, 108, 261
-MetadataRenderer.sol lines 119, 133, 189, 229


