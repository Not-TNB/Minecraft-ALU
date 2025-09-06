# Minecraft Arithmetic Unit
## Prerequisites
It is assumed that the reader has knowledge of Minecraft redstone components, and that they are familiar with binary numbers and basic logic gates/circuitry. It is also assumed that the reader can read a truth-table.

## Introduction
This file will document the building process of a 8-bit Arithmetic Unit (AU) in Minecraft, which performs the arithmetic operations requested within the processing done in an Arithmetic Logic Unit (ALU) within a CPU with Von-Neumann architecture. An ALU's job within the CPU is to perform arithmetic and bitwise operations on integer binary numbers. The inputs to an ALU are integer operands which undergo a certain operation to be outputted by the ALU. The operation to be carried out is determined via an opcode (operation code) and a status code, which is outputted by the ALU to indicate the status of its previous operation. 

![image](https://github.com/user-attachments/assets/b5c8bc6f-14ba-472d-bd83-e2ed2df0d1c2) <br>
> Source: _en.wikipedia.org_; The inputs and outputs of an ALU. The desired AU will work similarly but will only be able to perform basic operations.

My goal for the AU is to have it be able to perform and output the results of the following operations on two signed 8-bit inputs, the operation done controlled by a 2-bit opcode input:
1. Addition (with carry)
2. Subtraction (with carry/borrow)
3. Incrementing
4. Decrementing

Additionally it should produce the following outputs:
1. Integer result $Y$
2. Carry-out $C_{out}$ (the carry from addition, the borrow from subtraction or the overflow bit from shifting)
3. Zero $Z$ (whether all bits in the output are 0)
4. Negative $N$ (whether the output is negative)
5. Overflow $O_f$ (whether the output exceeds the 8-bit range)
6. Parity $P$ (whether the number of 1 bits in the output is even or odd)

<hr>

## Step 1: The Logic Gates
The following shows my attempt at building the 7 mentioned logic gates:

![2024-07-26_23 53 44](https://github.com/user-attachments/assets/826ed97e-3ac6-4543-a759-2f7e9cc60787)
> From left to right: NOT, AND, OR, XOR, NAND, NOR, XNOR. Blue indicates inputs and Orange indicates outputs.

<img src=https://github.com/user-attachments/assets/3c8ab641-5ad8-4ef8-b2e7-81aff769b76f width=50%> <br>
> Output (2) = back input (12) - side input (10)

The designs for each logic gate is shown below. Note the following:
- Each design is made to be 7 blocks long, 3 blocks wide and all wiring is done 1 block high.
- Target blocks are used as they direct any redstone dust on its side towards them. This is useful as redstone wiring does not automatically direct to the side of other blocks.
- Comparators set to subtract mode (the redstone torch on the tip being toggled on) produces the signal equivalent to what it receives from behind minus what it receives from its side.
- When the block a redstone torch is attached to is powered, the redstone torch is deactivated. This allows for convenient signal inversion. 

<img src=https://github.com/user-attachments/assets/9c75c39d-84b0-4abd-8cff-6132b55ba251 width=50%> <br>

<img src=https://github.com/user-attachments/assets/46dda6fe-ac64-4efd-b68b-0a59cb3f4b8d width=50%> <br>
> Truth tables for the logic gates shown above. Note that "EX-OR" and "EX-NOR" in this chart is equivalent to "XOR" and "XNOR" respectively.

<hr>

## Step 2: Input/Output Board
Let the integer operands be $A$ and $B$, and let the opcode be $O_p$. Then the desired AU is a map $(A,B,O_p) \mapsto (Y,C_{out},Z,N,O_f,P)$. A table showing how the three inputs correspond to $Y$ is shown.

| $O_p$ |  $Y$  |
| :-:   | :---: |
| 000   | $A+B$ |
| 001   | $A+1$ |
| 010   | $A-B$ |
| 011   | $A-1$ |

I created a placeholder input and output board as seen below.

<img src="https://github.com/user-attachments/assets/09d112fd-bde4-4e77-bd99-831fc4547607" width=70%> <br>

<hr>

## Step 3: Addition
The building of a multi-bit adder requires that of a half adder building up to a full adder, of which multiple can be chained together to add larger binary inputs.

### Half Adder
A half adder takes in 2 binary inputs $A$ and $B$ to output their sum $A+B$ in 2 bits; the front, carry bit $C$ and the lower, sum bit $S$. The truth table for the half adder is as follows:

| $A$ | $B$ | $A+B$ | $C$ | $S$ |
| :-: | :-: | :---: | :-: | :-: |
| 0   | 0   | 00    | 0   | 0   |
| 0   | 1   | 01    | 0   | 1   |
| 1   | 0   | 01    | 0   | 1   |
| 1   | 1   | 10    | 1   | 0   |

Notice that $C=A\wedge B$ and $S=A\oplus B$. From this, a half-adder in Minecraft is now within reach:

<img src=https://github.com/user-attachments/assets/48bbfaf8-3931-40d5-a3b9-af5776680baa width=50%> <br>

This design can be heavily compressed as I have done below. A schematic is included:

<img src=https://github.com/user-attachments/assets/6d3623dd-6381-42ac-ac3d-44e89ce2bf0a width=50%> <br>

<img src=https://github.com/user-attachments/assets/1a9f877b-e541-42c2-b730-fbfc24701465 width=50%> <br>

### Full Adder

A full adder adds 3 bits; $A$, $B$ and $C_{in}$ to form a 2-bit output $C_{out}$ and $S$. It is based on chaining 2 half-adders (and an OR gate to form $C_{out}$):

<img src=https://github.com/user-attachments/assets/e141ad7f-ec06-4801-b822-5942a39922e3 width=50%> <br>

A truth table, build picture and build schematic are provided below:

| $A$ | $B$ | $C_{in}$ | $A+B+C_{in}$ | $C_{out}$ | $S$ |
| :-: | :-: | :------: | :----------: | :-------: | :-: |
| 0   | 0   | 0        | 00           | 0         | 0   |
| 0   | 0   | 1        | 01           | 0         | 1   |
| 0   | 1   | 0        | 01           | 0         | 1   |
| 0   | 1   | 1        | 10           | 1         | 0   |
| 1   | 0   | 0        | 01           | 0         | 1   |
| 1   | 0   | 1        | 10           | 1         | 0   |
| 1   | 1   | 0        | 10           | 1         | 0   |
| 1   | 1   | 1        | 11           | 1         | 1   |

<img src=https://github.com/user-attachments/assets/35ef9c65-8eb7-452e-bfa3-0f670b2a41f0 width=50%> <br>

<img src=https://github.com/user-attachments/assets/36fe8e3c-2c5a-420d-acc2-9038acf811b9 width=75%> <br>

### Multi-bit Adder (Ripple Carry)

Consider adding the binary numbers $A=11111$ and $B=10010$:

```
  1 1 1 1 0 0 <-- Carry bits
    0 1 1 1 1 <-- A4,A3,A2,A1,A0
+   1 0 1 1 0 <-- B4,B3,B2,B1,B0
_____________
  1 0 1 1 0 1 <-- Sum bits
```

As illustrated, carry bits are called as such because they account for the carrying done to the next digit, while sum bits are left unchanged to contribute to the output sum. As one may notice, these carry bits may cause a chain of subsequent carries, hence why building an adder out of this method is called "ripple-carry". As shown below, propagation continues in each consecutive digit as long as the sum of the 2 digit bits $A$ and $B$ and the previous carry bit $C_{in}$ is 1 or less.

```
Assume we are adding a large number, and we are on the Nth digit of A and B, A(n) and B(n).
For notation, let the Nth carry bit be C(n). (NOTE: We count from 0 here.)

        1   <-- Carry bit C(n) is 1 as A(n) + B(n) 1+1=10 cannot be stored in 1 bit
    0 1 1 1 ...
+   0 1 0 1 ...
___________ ...
          0 

      1 1   <-- Carry bit C(n+1) is propagated as either A(n+1) or B(n+1) is 1, hence A(n+1) + B(n+1) + C(n) = 10 > 1
    0 1 1 1 ...
+   0 1 0 1 ...
___________ ...
        0 0 ...

    1 1 1   <-- Carry bit is propagated as either A(n+2) or B(n+2) is 1, hence A(n+2) + B(n+2) + C(n+1) = 11 > 1
    0 1 1 1 ...
+   0 1 0 1 ...
___________ ...
      1 0 0 ...

  0 1 1 1   <-- Carry bit is NOT propagated as neither A(n+3) nor B(n+3) is 1, hence A(n+3) + B(n+3) + C(n+2) = 1 <= 1
    0 1 1 1 ...
+   0 1 0 1 ...
___________ ...
    1 1 0 0 ...
```

From this, a schematic for a multi-bit adder is within reach. Note that we do not have a lever for carry bits as they are predetermined from the previous digit. In typical applications the carry bit at the bottom will also be set to 0.

Also, I found that in the full adder design I made there was a chance that the output would periodically toggle on/off instead of staying on, which could pose a problem for reading these outputs. I found that this was due to a redstone clock loop which could be induced within the OR gate, so I introduced a comparator in non-subtractor (normal) mode to fix this.

<img src=https://github.com/user-attachments/assets/515351bf-1087-444d-a115-59af623fc35e width=50%> <br>
> Source: _www.electronicshub.org_; The logic diagram for a 4-bit adder, which is expandable for any number of bits to add.

<img src="https://github.com/user-attachments/assets/7078f53d-1e25-4f5d-b9ef-c8edaaf76688" width=70%> <br>

With this schematic (and some visual modifications to make the build more appealing), the finished 8-bit adder is shown below.

<img src="https://github.com/user-attachments/assets/54548501-6750-4e97-abae-accfa2e2ba58" width=70%> <br>

<hr>

## Step 4: Incrementing & Negation
In tackling subtraction (discussed in Step 5), instead of the approach we took for the adder (that is, building a half subtractor and a full subtractor then modularizing into a multi-bit subtractor), we can take advantage of the 8-bit adder built in the previous section. Observe that we can write $A-B=A+(-B)$, so building a circuit to perform the negation operation $A \mapsto (-A)$ in two's complement would allow us to achieve subtraction with less circuitry and greater time/space efficiency.

### About Negation
In two's complement theory we have $(-A)= 1 + (\neg A)$, where $\neg A$ is the one's complement of $A$ (that is, flipping every bit in $A$, which can be done easily with NOT gates). Adding 1 is precisely the function of the increment operation. Hence it is reasonable to tackle the increment circuit first.

### Deriving Incrementing from Addition
Vacuously, incrementing $A$ is the same as $A+1$, so we can use our multi-bit adder with $B=0$ and $C_0=1$ to increment $A$. To achieve this in Minecraft, we modify the original multi-bit adder by removing all the inputs in one column (equivalent to $B=0$ since no input signal is given) and replacing the $C_0$ input with a redstone block (equivalent to $C_0=1$ as the redstone block always produces a signal). The changes made are shown in the incrementer build below.

<img src="https://github.com/user-attachments/assets/37ba3375-a7fd-4d37-bed6-d65c83c5ce77" width=30%> <br>
> In this specific example, $A=00110101$ and the desired output $A+1=00110110$ is correctly displayed on the side.

### Constructing the Negator
We will build off of the incrementer circuit from above. We first replace the manual input (redstone lamps) in the incrementer and add NOT gates to each one. We can then build another manual input panel in front. As mentioned previously, the added NOT gates effectively performs $A\mapsto (\neg A)$, and followed by the increment operation this completes the negator. A side-view schematic of the modifications performed is shown.

<img src="https://github.com/user-attachments/assets/3c4fa0e2-47f8-4210-856e-3cf078ca659a" width=40%> <br>
> The black blocks are black concrete.

<img src="https://github.com/user-attachments/assets/b062c282-7ceb-425a-be9c-bc4ce891d225" width=70%> <br>
> The negator build. The side-view shows how each individual bit of $A$ is flipped with a NOT gate to produce $\neg A$ before passing through the incrementer to yield $1+(\neg A)=(-A)$
<hr>

## Step 5: Subtraction & Decrementing

### Multi-bit Subtractor
Recall that the idea is to expand $A-B=A+(-B)$. In the Minecraft equivalent, we wire $B$ to the negator circuit to output $-B$, then combine this with $A$ as inputs to the 8-bit adder to produce $A-B$. The wiring for one layer (the ones bit) is shown below.

<img src="https://github.com/user-attachments/assets/acbd7548-81ca-4b73-9c01-dc097a4145d0" width=50%> <br>

<hr>

## Step 6: Opcodes & Operation Selection

<hr>

## Step 7: Output Signals

<hr>

## FINAL RESULT: Arithmetic Unit
