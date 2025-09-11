# Minecraft Arithmetic Unit
## WORLD FILE
Please find the world file attached in this repository.

## Prerequisites
It is assumed that the reader has knowledge of Minecraft redstone components, and that they are familiar with binary numbers and basic logic gates/circuitry. It is also assumed that the reader can read a truth-table.

## Introduction
This file will document the building process of a 8-bit Arithmetic Unit (AU) in Minecraft, which performs the arithmetic operations requested within the processing done in an Arithmetic Logic Unit (ALU) within a CPU with Von-Neumann architecture. An ALU's job within the CPU is to perform arithmetic and bitwise operations on integer binary numbers. The inputs to an ALU are integer operands which undergo a certain operation to be outputted by the ALU. The operation to be carried out is determined via an opcode (operation code) and a status code, which is outputted by the ALU to indicate the status of its previous operation. 

![image](https://github.com/user-attachments/assets/b5c8bc6f-14ba-472d-bd83-e2ed2df0d1c2) <br>
> Source: _en.wikipedia.org_; The inputs and outputs of an ALU. The desired AU will work similarly but will only be able to perform basic operations.

My goal for the AU is to have it be able to perform and output the results of the following operations on two signed 8-bit inputs (in two's complement form), the operation done controlled by a 2-bit opcode input:
1. Addition (with carry)
2. Subtraction (with carry/borrow)
3. Incrementing
4. Decrementing

Additionally it should produce the following outputs:
1. Integer result $Y$
2. Carry-out $C_{out}$ (the carry from addition or the borrow/overflow from subtraction)
3. Zero $Z$ (whether all bits in the output are 0)
4. Parity $P$ (whether the number of 1 bits in the output is even or odd)

<hr>

## Step 1: The Logic Gates
The following shows my attempt at building the 7 major logic gates:

![2024-07-26_23 53 44](https://github.com/user-attachments/assets/826ed97e-3ac6-4543-a759-2f7e9cc60787)
> From left to right: NOT, AND, OR, XOR, NAND, NOR, XNOR. Blue indicates inputs and Orange indicates outputs.

<img src=https://github.com/user-attachments/assets/3c8ab641-5ad8-4ef8-b2e7-81aff769b76f width=50%> <br>
> Output (2) = back input (12) - side input (10)

The designs for each logic gate is shown below. Note the following:
- Each design is made to be 7 blocks long, 3 blocks wide and all wiring is done 1 block high.
- Target blocks are used as they direct any redstone dust on its side towards them. This is useful as redstone wiring does not automatically direct to the side of other blocks.
- Comparators set to subtract mode (the redstone torch on the tip being toggled on) produces the signal equivalent to what it receives from behind minus what it receives from its side.
- When the block a redstone torch is attached to is powered, the redstone torch is deactivated. This allows for convenient signal inversion. 

<img src="https://github.com/user-attachments/assets/f2a5b200-3f4d-4033-a6a7-034e166d8b7e" width=50%> <br>

<img src=https://github.com/user-attachments/assets/46dda6fe-ac64-4efd-b68b-0a59cb3f4b8d width=50%> <br>

> Truth tables for the logic gates shown above. Note that "EX-OR" and "EX-NOR" in this chart is equivalent to "XOR" and "XNOR" respectively.

<hr>

## Step 2: Input/Output Board
Let the integer operands be $A$ and $B$, and let the opcode be $O_p$. Then the desired AU is a map $(A,B,O_p) \mapsto (Y,C_{out},Z,P)$. A table showing how the three inputs correspond to $Y$ is shown.

| $O_p$ |  $Y$  |
| :-:   | :---: |
| 00   | $A+B$ |
| 01   | $A+1$ |
| 10   | $A-B$ |
| 11   | $A-1$ |

I created a placeholder input and output board as seen below.

<img src="https://github.com/user-attachments/assets/505b8cd6-f416-455b-ab82-56e83b3bf2a0" width=50%> <br>

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

<img src="https://github.com/user-attachments/assets/7f6cb9e6-49ef-49d3-9565-06b43c4a8f9a" width=50%> <br>

When wiring is completed, the subtractor is achieved.

<img src="https://github.com/user-attachments/assets/60f2b195-41e1-48a6-885b-20c087975417" width=80%> <br>
> In this example we have $A=10100001$ and $B=10111101$, which in two's complement form are equivalent to $A=-95 \wedge B=-67$ yielding $A-B=-28=11100100_2$. The benefit of allowing two's complement form is that all the adder and subtracter curcuits do not require additional logic for arithmetic with negatives, simplifying the build process compared to sign-bit representation.

### Decrementer
Vacuously decrementing $A$ is the same as $A-1$. However instead of directly using our subtractor we can note the following:
```math
 A-1 = A+(-1) = A+[11111111]_2 \qquad (\text{-1 in 8-bit two's complement form is 00000001})
```
So we can use a single adder module (setting $B=11111111$ and $C_0=0$) to emulate decrementing. This is done in the build below by modifying the adder to remove the $C_0$ input and replacing the $B$ input signals with redstone blocks.

<img src="https://github.com/user-attachments/assets/3365c39e-ec81-452f-95ee-67774bdf4bbd" width=30%> <br>

<hr>

## Step 6: Operation Selection Module & Operation Circuit Wiring

At this stage all four crucial modules have been built; the adder, the subtractor, the incrementer and the decrementer. Before we hook up these four circuits to our input board, we must first design a system which ensures the inputs $A$ and $B$ flow only to the correct module depending on the opcode input $O_p$. My idea was to have the inputs branch out into four lines, each line going to one of the four operation circuits. For each of the four lines, the signal will only pass through to the operation circuit if this happens to be the operation selected by $O_p$.

### Interpreting Opcodes
I first built a circuit which, given the two-bit input $O_p$, will output a 1 bit in exactly one of four output lines, each output line corresponding to a different operation which the opcode can specify. The desired truth table is given below:

| $O_p[1]$ (Twos bit) | $O_p[0]$ (Ones bit) | Add | Sub | Inc | Dec |
| :------------: | :------------: | :-: | :-: | :-: | :-: |
| 0              | 0              | 1   | 0   | 0   | 0   |
| 0              | 1              | 0   | 1   | 0   | 0   |
| 1              | 0              | 0   | 0   | 1   | 0   |
| 1              | 1              | 0   | 0   | 0   | 1   |

From the truth table I deduced the following boolean logic expressions for $Add$, $Sub$, $Inc$ and $Dec$:

```math
\begin{aligned}
Add &= \neg \left( O_p[1] \vee O_p[0] \right) &\qquad \text{( [1] NOR [0] )} \\
Sub &= \neg O_p[1] \wedge O_p[0]                &\qquad \text{( NOT [1] AND [0] )} \\
Inc &= O_p[1] \wedge \neg O_p[0]                &\qquad \text{( [1] AND NOT [0] )} \\
Dec &= O_p[1] \wedge O_p[0]                     &\qquad \text{( [1] AND [0] )}
\end{aligned}
```

To implement this in Minecraft I decided to build the selector in 4 layers, each layer corresponding to one of the output signals. I also included a logic diagram to illustrate this

<img src="https://github.com/user-attachments/assets/44610fa8-05f8-45f7-93fa-950852127186" width=50%> <br>

<img src="https://github.com/user-attachments/assets/da391b7d-09c2-4af0-87d2-15f57106dd6b" width=90%> <br>

I included some pictures of the build interconnected with the input board (STEP 2):

<img src="https://github.com/user-attachments/assets/2b609e1e-c5d5-49ad-8eb8-506c9c9726e3" width=60%> <br>

### Side-Note: The Redstone Torch Tower
Notice how in the 3rd layer I seem to have built $\neg O_p[1] \wedge O_p[0]$ (similar to the second layer) rather than the expected $O_p[1] \wedge \neg O_p[0]$. This is due to the method I used in this build to transport redstone signals vertically, known as a redstone torch tower. This method works because redstone torches placed atop a block are able to influence the block above it, so chaining this mechanic leads to a column of redstone torches and solid blocks. 

Despite being extremely compact, one caveat of this method for vertical redstone trabsmission is that the activation of redstone torches alternate (since when a redstone torch receives an indirect redstone signal it toggles off), apparent in the third layer. 

Hence, the inputs to the third layer are actually $X=\neg O_p[0]$ ad $Y=\neg O_p[1]$, so to obtain $O_p[1] \wedge \neg O_p[0]$ we observe that
```math
\begin{aligned}
O_p[1] \wedge \neg O_p[0] &= \left( \neg \neg O_p[1] \right) \wedge \left(\neg O_p[0]\right) &\qquad \text{Justification: } P=\neg\neg P \\
&= \neg \left( \neg O_p[1] \right) \wedge \left( \neg O_p[0] \right) \\
&= \neg X \wedge Y
\end{aligned}
```
So we can repeat the circuit in layer 2 to for the building of layer 3.

<img src="https://github.com/user-attachments/assets/1074ba15-6559-4539-b3fe-45043b358001" width=35%> <br>
> Two examples of so-called "redstone torch towers", often used for the vertical transmission of redstone. Notice how if a redstone torch powers the block above it, the following torch is deactivated, leading to an alternating pattern (on, off, on, ...)

### Input Operands
I first built redstone lines going out of each of the 16 input bits. The following image shows how I connected the red (ones bit) lines to the adding circuit.

<img src="https://github.com/user-attachments/assets/7d33f1a2-a37d-4d38-a075-d0d6f15a3b76" width=60%> <br>

Repeating a similar process into the other three operational circuits and with all other bits, the AU so far looks as follows:

<img src="https://github.com/user-attachments/assets/0a6a73d5-c102-48d4-bbeb-2d549914ad76" width=70%> <br>

<hr>

## Step 7: Output Signals

At this stage, inputting $A$ and $B$ will yield outputs in all four operational modules. The last step is hence to choose exactly one of these to output (with some additional output signals $C_{out}$, $Z$ and $P$) using the operation selector built previously.

First I wired up the outputs of each of the operational modules to the lower right where I can deal with them.

<img src="https://github.com/user-attachments/assets/03ed6a2b-64e0-4928-bc8b-4e757e496502" width=70%> <br>
> NOTE that I actually swapped the positions of the incrementer and decrementer circuits since the previous screenshot. This is so the ordering of the outputs from left to right are exactly the order of outputs from the operation selector; this is an organizational/visual fix and does/will not affect functionality.

To allow the opcode to control the line which is outputted, I built AND gates between each of the output lines and their corresponding selector line fcrom the operation selector. An example of how this is done is shown below, and I did a similar approach for the other bits and lines:

<img src="https://github.com/user-attachments/assets/4a24c341-6e17-4ed4-bb18-41219f2a8149" width=70%> <br>

After wiring up AND gates to all 8 bits for each respective operation circuit output lines, the following is obtained

<img src="https://github.com/user-attachments/assets/8af80e94-8a99-465f-ab9d-f416e652fb7c" width=70%> <br>
<img src="https://github.com/user-attachments/assets/d712057a-a034-4bd3-8687-6ca9f5e20f49" width=50%> <br>
> The gray half-blocks are upper-slabs; stacking them this way and placing redstone atop each one is another method of vertical redstone transmission.

I then connected the four output lines together to yield the output $Y$ as well as $C_out$ at the top.

<img src="https://github.com/user-attachments/assets/27d98261-c48e-42d6-97cd-c1510f58c26c" width=60%>

### Interpreting $Y$ to Compute $C_{out}$, $Z$ and $P$
$Y$ and $C_{out}$ are already apparent and can be brought to the output board for display. It is left to compute $Z$ (which is 1 iff. $Y=0$) and $P$ (which is 1 iff. the number of 1 bits in $Y$ is odd). First we calculate $Z$:
```math
\begin{aligned}
Z &= \bigwedge_{i=0}^7(\neg Y[i]) \\
  &= \neg\left(\bigvee_{i=0}^7Y[i]\right) \qquad \text{By De-Morgan's law}
\end{aligned}
```
Which can be implemented as follows

<img src="https://github.com/user-attachments/assets/b03ad955-8770-40f5-818e-bd19426403e7" width=50%> <br>
> The upper-slab technique for vertical redstone transmission connects all 8 bits of $Y$ creating an OR gate. A NOT gate with a redstone torch inverter is built at the top to produce $Z$.

Now we calculate $P$. To do this, notice how $A\oplus B=1$ iff. the number of 1 bits among $A$ and $B$ is odd. Hence we can chain XOR gates along the bits of $Y$:
```math
\begin{aligned}
P &= \bigoplus_{i=0}^7Y[i] \\
&= \left[ \left( Y[0] \oplus Y[1] \right) \oplus \left( Y[2] \oplus Y[3] \right) \right] \bigoplus \left[ \left( Y[4] \oplus Y[5] \right) \oplus \left( Y[6] \oplus Y[7] \right) \right]  \qquad \text{XOR is associative}
\end{aligned}
``` 

When this is implemented the output interpreter build is completed:

<img src="https://github.com/user-attachments/assets/6913a644-d338-43d0-b897-9705d412735b" width=70%> <br>

Lastly I just needed to hook up this component to the output $Y$, and build the proper connections to the output board. The build is finished.

<img src="https://github.com/user-attachments/assets/ab5cd213-b63f-4843-9d2e-464072c8e58d" width=80%> <br>

<hr>

## FINAL RESULT: Arithmetic Unit
The arithmetic unit is completed. Find additional images of the final build under [images](https://github.com/Not-TNB/Minecraft-Arithmetic-Unit/tree/main/images%20).

<img src="https://github.com/user-attachments/assets/65c33c0b-3458-40b3-8d8c-e38107c72f1d" width=100%><br>
> The input board is within the blue glass on the left, and the output board is within the orange glass on the right. I used glass here instead of solid blocks to showcase the wiring behind the boards.

<img src="https://github.com/user-attachments/assets/1106eceb-1c13-4cee-968e-1512d268c023" width=100%> <br>
> A component/circuit breakdown of the finished AU.

Overall, this project, although simple in nature (a subset of the ALU with limited function), challenged me to apply my knowledge of fundamental boolean logic and logic circuits in a unique, unconventional and restrictive environment (Minecraft redstone), allowing me to implement basic concepts like the full adder in interesting and original ways. This is further exacerbated by the fact that I strived to use no tutorials or guides within this niche field of redstone computing during the project.
