---
layout: archive-wide
title: "MIPS Pipeline in VHDL"
excerpt: "Constructing a MIPS Pipeline with R, I and J-type Instructions <br/> <img src='/images/MIPS-datapath-vivado.png' width=750>"
collection: portfolio
author_profile: false
share: false
classes: wide
order: 2
---

This project was the culmination of the lab portion of my **Computer Organization class (ECE 445 at GMU)**. This project was created in VHDL using **Vivado** as our software of choice. The lab course initially started by creating smaller components of the pipeline such as an Arithmetic Logic Unit (ALU) and easier parts of the pipeline before the entirety of this project was assembled (R-type was created first, then implementation of I-type was added, and finally J-type was added for the final project).

The MIPS pipeline is an assembly of parts designed to take an input which contains an instruction which is, in our case, encoded in hexadecimal, and outputs a different value according to what was instructed (addition, multiplication, etc). This is used as a way to simulate MIPS assembly code instructions.

<p align="center">
<img src='/images/MIPS-datapath.png' width=700><br><br><img src='/images/MIPS-datapath-vivado.png' width=1000>
<br>An official MIPS datapath that can be found online (top) compared with my recreation in Vivado (bottom)
</p>

<hr>
## Overview of Project Functions
The process of development of this project involves multiple steps to ensure every component works and the system can function as a whole. Because we were using Vivado as a way to create Hardware Description Language code, the process is more complicated than simply writing code and expecting a certain result.

### Coding components
The MIPS pipeline is an assembly of multiple different parts with different purposes, but each one is necessary in order to allow implementation of every instruction of the pipeline. Each unique component requires its own VHDL code to define to the part what it should do with its input(s) in order to achieve proper output. A list of a few of the significant components are as follows:
- <u>Program Counter</u>: Stores and increments memory address of instruction to be executed.
- <u>Instruction Memory</u>: Provides the 32-bit instruction based on which address is input.
- <u>Control</u>: Sends bits out to multiple components, stating which mode each component should be set to.
- <u>Register File</u>: Storage containers containing data, including the current numbers which are to be used in arithmetic
- <u>Arithmetic Logic Unit</u>: Performs arithmetic operation based on given inputs. This also accounts for zero, overflow and carryout bits.
- <u>Data Memory</u>: Stores certain variables and static data.

There are multiple other components involved, but these are the main notable ones used in the pipeline.


### Assembling Components (Block Diagram)
Assembling the components consists of placing each component into a workspace and using slicers to choose which bits of certain outputs should be sent to different locations. This assembly step allowed us to create an auto-created wrapper which is necessary to allow for code synthesis and implementation to an FPGA board.

### Testing Assembly
Vivado has a strong testbench system, allowing users to examine inputs/outputs of every component for every clock cycle. This allows for highly in-depth testing which is exactly what was done for our system. This can also be done for individual components if so desired.

To test the functionality of our design, we were given a select few MIPS instructions and used their corresponsing hex codes as inputs for our simulation. If the end result was the same as the expected result after the correct number of clock cycles, we would know the system functions correctly.

<p align="center">
<img src='/images/MIPS-sim-instructions.png' width=400> <img src='/images/MIPS-simulation-values.png' width=330><br><br><img src='/images/MIPS-simulation.png' width=600>
<br>The instructions used for our test simulation (First Image) and the results that were expected from the simulation (Second Image) as well as the actual simulation results (Third Image)
</p>

Going through each instruction, we were able to calculate the resulting values in each memory block and use such values to test if our pipeline was properly coded and assembled. Referencing the images above, you can see how our simulated memory blocks match the calculated values after the code finishes running.

### Hardware verification
This assignment also had a hardware testing component in which we mapped buttons to certain functions on an FPGA board. The idea was to map a button to execute one instruction every time it was pressed, and the resulting would be displayed on a Seven-Segment LED.<br>
The instructions for the project were to start with a number and if it's odd, multiply by 2, and if it's even add the previous value. This should all be done with R-, I- and J-type instructions, and the assembly code for such can be seen below.

<p align="center">
<img src='/images/MIPS-instructions.png' width=700>
<br>Address of instruction (Left Column), the instruction written in assembly (Middle Column) and the instruction's equivalent in hexadecimal (Right Column)
</p>



Unfortunately footage of this functionality has been lost. However the instructions and code still exist on my PC, so if I ever decide to re-run the project, footage of functionality may be added.


<hr>
## Challenges and End Thoughts
Having not gone very deep into VHDL in previous classes or personal experience, the whole process of this project was quite a challenge, especially because of the scope of what needed to be implemented. This project, however, was great at reinforcing my knowledge of the MIPS pipeline and having a better understanding of how assembly code is read and executed.<br>