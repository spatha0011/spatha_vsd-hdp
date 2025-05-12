# VSD Hardware Design Program

## Day 1 : Introduction to Verilog RTL design and Synthesis

### Simulator
RTL design is validated by simulating its behavior to ensure it adheres to the specifications.

The simulator used for this course is Icarus Verilog (iverilog).

### Design
The design consists of Verilog code implementing the intended hardware logic.

It should meet functional specifications and can be composed of one or more Verilog modules.

### Testbench
A testbench is used to apply test vectors (stimuli) to the design.

It mimics input signals and observes the response to verify design correctness.

Note: The testbench does not have primary inputs or outputs.

### How the Simulator Works
The simulator continuously checks for changes in input signals.

If there is a change, it evaluates the corresponding output.

If no change occurs in inputs → no change in outputs.

The simulator focuses solely on detecting and reacting to signal changes.

### Iverilog-Based Simulation Flow

The Iverilog-based simulation flow begins with writing the Design (RTL Verilog module) and a corresponding Testbench that applies input vectors to the design and observes the outputs. Both files are compiled using the iverilog tool to generate a simulation executable. When this executable is run, it produces a Value Change Dump (VCD) file that records signal transitions over time. This VCD file is then viewed using GTKWave, a waveform viewer that allows designers to analyze and debug signal behavior visually, ensuring the design meets the expected functionality.


