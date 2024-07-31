# Minecraft ALU (CAS)
## Prerequisites
It is assumed that the reader has knowledge of Minecraft redstone components, and that they are familiar with binary numbers and basic logic gates/circuitry. It is also assumed that the reader can read a truth-table.

## Introduction
This file will document the building process of a 8-bit Arithmetic Logic Unit (ALU) in Minecraft. An ALU's job within a computer's processor is to perform arithmetic and bitwise operations on integer binary numbers. The inputs to an ALU are named "operands", which undergo a certain operation to be outputted by the ALU. The operation to be carried out is determined via an opcode (operation code) and a status code, which is outputted by the ALU to indicate the status of its previous operation. 

![image](https://github.com/user-attachments/assets/b5c8bc6f-14ba-472d-bd83-e2ed2df0d1c2) <br>
> Source: _en.wikipedia.org_; The inputs and outputs of an ALU.

The construction will take place in multiple stages:
1. The logic gates (NOT, AND, NAND, OR, NOR, XOR, XNOR)
2. 8-bit adder
3. 8-bit subtractor with use of two's complement
4. Selector and switch components to control/select input and output channels
5. Arithmetic unit and Logic unit
6. ALU

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

## Step 2: Adder
The building of a multi-bit adder requires that of a half adder building up to a full adder.

### Half Adder
A half adder takes in 2 binary inputs $A$ and $B$ to output their sum $A+B$ in 2 bits; the front, carry bit "C" and the lower, sum bit "S". The truth table for the half adder is as follows:

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

<img src=https://github.com/user-attachments/assets/3d7e9143-dae3-4c2c-8048-fe7326f02637 width=75%> <br>
> Note that the stray redstone wire going off above the $S$ bit will serve the function of lighting output lamps in the multi-bit adder

### Multi-bit Adder

<hr>

## Step 3: Subtractor

<hr>

## Step 4: Selector & Switch

<hr>

## Step 5: Arithmetic Unit & Logic Unit

<hr>

## FINAL RESULT: ALU
