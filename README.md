# Minecraft ALU (CAS)
## Prerequisites
It is assumed that the reader has knowledge of Minecraft redstone components, and that they are familiar with binary numbers and basic logic gates/circuitry.

## Introduction
This file will document the building process of an 8-bit Arithmetic Logic Unit (ALU) in Minecraft. An ALU's job within a computer's processor is to perform arithmetic and bitwise operations on integer binary numbers. The inputs to an ALU are named "operands", which undergo a certain operation to be outputted by the ALU. The operation to be carried out is determined via an opcode (operation code) and a status code, which is outputted by the ALU to indicate the status of its previous operation. 

![image](https://github.com/user-attachments/assets/b5c8bc6f-14ba-472d-bd83-e2ed2df0d1c2) <br>
> Source: _en.wikipedia.org_; The inputs and outputs of an ALU.

The construction will take place in multiple stages:
1. The logic gates (NOT, AND, NAND, OR, NOR, XOR, XNOR)
2. 8-bit adder
3. 8-bit subtractor with use of two's complement
4. Selector and switch components to control/select input and output channels
5. Arithmetic unit and Logic unit
6. ALU

**Note that a binary "1" is represented by an "on" redstone lamp, and a binary "0" by an "off" redstone lamp. Levers are attached to input lamps to allow player input**

<img src=https://github.com/user-attachments/assets/65b08425-57b9-4dc4-89f0-1e0fef1aa1ce width="50%"> <br>
> When the lever is flicked down, the redstone lamp turns on, equivalent to a "1" (right). Otherwise, it is a "0" (left).

<hr>

## Step 1: The Logic Gates
The following shows my attempt at building the 7 mentioned logic gates:

![2024-07-26_23 53 44](https://github.com/user-attachments/assets/826ed97e-3ac6-4543-a759-2f7e9cc60787)
> From left to right: NOT, AND, OR, XOR, NAND, NOR, XNOR. Blue indicates inputs and Orange indicates outputs.

<img src=https://github.com/user-attachments/assets/3c8ab641-5ad8-4ef8-b2e7-81aff769b76f width="50%"> <br>
> Output (2) = back input (12) - side input (10)

The designs for each logic gate is shown below. Note the following:
- Each design is made to be 7 blocks long, 3 blocks wide and all wiring is done 1 block high.
- Target blocks are used as they direct any redstone dust on its side towards them. This is useful as redstone wiring does not automatically direct to the side of other blocks.
- Comparators set to subtract mode (the redstone torch on the tip being toggled on) produces the signal equivalent to what it receives from behind minus what it receives from its side.
- When the block a redstone torch is attached to is powered, the redstone torch is deactivated. This allows for convenient signal inversion. 

