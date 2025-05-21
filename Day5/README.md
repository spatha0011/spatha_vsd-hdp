# VSD Hardware Design Program

## Day 5 : VSDBabySoC Design and Modeling

VSDBabySoC is a small SoC including PLL, DAC, and a RISCV-based processor named RVMYTH.

### Introduction to the VSDBabySoC
VSDBabySoC is a small yet powerful RISCV-based SoC. 

The main purpose of designing such a small SoC is to test three open-source IP cores together for the first time and calibrate the analog part of it. 

VSDBabySoC contains one RVMYTH microprocessor, an 8x-PLL to generate a stable clock, and a 10-bit DAC to communicate with other analog devices.

### What is VSDBabySoC?
  VSDBabySoC is a compact SoC featuring:
  - **RVMYTH**: A simple RISC-V-based CPU core.
  - **PLL**: An 8x Phase-Locked Loop for stable clock generation.
  - **DAC**: A 10-bit Digital-to-Analog Converter for interfacing with analog devices.

  Its primary purpose is to integrate and test these IPs collaboratively and calibrate the analog part of the SoC.

  ![Alt Text](Images/Day5/Images/BabySoC_block.png)
