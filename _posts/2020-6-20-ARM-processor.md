---
layout: post
title: "ARM Processor (Verilog)"
author: "Dan Peluso"
categories: programming
tags: [programming, verilog, architecture, ARM, vivado]
image: arch/computerArchFinalProject.png
---

The code below is the top module for a CPU which uses the ARMv8 instruction set.
This processor is capable of utilizing the following instructions:

![instructions](\assets\img\arch\finalInsSet.png)

For this particular model, the instruction set is given as an array of instructions,
and given the 4-cycle delay should produce the correct values in the destination register.
The memory system was not modeled in this implementation, but to simulate it I made
a tristate buffer to use the memory 'Databus' as both an input and output.

![top](\assets\img\arch\archTopModuleFinalProj.png)

I calculated the IPC to be ~.67, which I explain below:

![ipc](\assets\img\arch\ipc_png.png)


For easier reading, I put all the submodules directly below the top module.


<script src="https://gist.github.com/pelusodan/8a8b4065b3b6375dc87e8c430be6f030.js"></script>

# Skills
- Verilog
- ARMv8
- Vivado
- Digital Hardware Design
