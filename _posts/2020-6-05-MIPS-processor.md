---
layout: post
title: "MIPS Processor (Verilog)"
author: "Dan Peluso"
categories: programming
tags: [programming, verilog, architecture, mips, vivado]
image: arch/a7.png
---

The code below is the top module for a CPU which uses the MIPS instruction set.
This processor is capable of utilizing the following instructions:

![instructions](\assets\img\arch\a7InsSet.PNG)

For this particular model, the instruction set is given as an array of instructions,
and given the 3-cycle delay should produce the correct values in the destination register.
The memory system was not modeled in this implementation, but to simulate it I made
a tristate buffer to use the memory 'Databus' as both an input and output.

![top](\assets\img\arch\a7Top.PNG)

For easier reading, I put all the submodules directly below the top module.


<script src="https://gist.github.com/pelusodan/fe271c2e1687b804232369b64b6c49e2.js"></script>


# Skills
- Verilog
- MIPS
- Vivado
- Digital Hardware Design
