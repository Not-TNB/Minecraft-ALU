## PLEASE GO TO THE WIKI FOR DOCUMENTATION ON THE BUILD AND DESIGN PROCESS OF THE ARITHMETIC UNIT

The arithmetic unit build is able to perform the following operations with 8-bit binary represented integers and a 2-bit opcode (operation code):
1. Addition
2. Subtraction
3. Incrementing
4. Decrementing

Upon inputting the following outputs are generated
1. The result of the operation
2. A single bit giving any overflow, carry or borrow bit left over from the operation
3. A single bit yielding 1 if the result of the operation is exactly 0, and yielding 0 otherwise
4. A single PARITY bit yielding 1 if the result of the operation has an odd number of 1 bits and yielding 0 otherwise.
