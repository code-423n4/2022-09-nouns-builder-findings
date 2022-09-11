**Treasury.sol**
- L162 - It is not necessary to initialize a variable with its default value since it generates an extra gas cost without having benefits in the understanding of the code.

**Address**
L50 - It is less expensive to validate "variable != 0" than "variable > 0"
 

**Metadata Renderer**
- L119/133/189/229 - It is not necessary to initialize a variable with its default value since it generates an extra gas cost without having benefits in the understanding of the code.

- L124/151/194/226/237 - It is less expensive to make ++variable or --variable, than variable + 1 for example.


**Token**
- L91/154 - It is less expensive to make ++variable or --variable, than variable + 1 for example.
