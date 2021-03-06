---
title: "Intro"
date: 2019-09-30T14:08:13-04:00
draft: false
---

# FPGA - (Field Programmable Gate Array)

< Insert image here >

FPGAs, or Field Programmable Gate Arrays, are reconfigurable integrated circuits. They are essentially a sea of gates (In reality, they use "logic blocks" instead of AND, OR, NOT, etc.) which the user can reconfigure. FPGAs are commonly used to prototype ASICs (Application Specific Integrated Circuits), but offer hobbyists a way to design and use their own custom integrated circuits. FPGAs tend to be slower than ASICs, and are often limited to clock frequencies in the hundreds of megahertz, but can be much faster than programmed general-purpose circuits. This is because you can implement your logic in hardware, instead of running software on static hardware. This allows many tasks to run truly asynchronously and atomically, and in some cases are not limited by the clock frequency. 

This speedup is most evident when doing stream processing, since you could design a circuit that processes data asynchronously as it comes in and outputs it in real time. For example, if you wanted to take in a signal and perform a FFT (Fast Fourier Transform; a common signal processing tool), you could build a circuit that performs the FFT, instead of writing a program that takes many clock cycles to perform the same task. In addition to being the fastest (except for ASICs) way to implement a digital function, FPGAs also provide a large amount of flexibility. Say, for example, you have a project that needs 3 processing cores, 6 timers, and needs to handle data from dozens of sensors in real time. You could either try to find a mix of ICs that meet your requirements, or you could design a custom processor with an FPGA. Lets say that later you expand your project and actually need 10 timers. If you used an FPGA, this can be accomplished by flashing a new design, instead of needing to order more hardware and re-design your electronics.

The reason FPGAs are not used everywhere is primarily due to cost and design effort. Many microprocessor ICs cost a few dollars at most, while a decent FPGA will run you from $50 to $10000 USD. This makes it impractical to place an FPGA in every product. From a maker perspective, though, it isn't too outlandish to buy a single FPGA development board, and use it for a variety of projects (remember, it can be configured as nearly any digital circuit). In particular, the FPGA I am using is the 