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

<strong>Note:</strong> The testbench does not have primary inputs or outputs.

### How the Simulator Works
The simulator continuously checks for changes in input signals.

If there is a change, it evaluates the corresponding output.

If no change occurs in inputs → no change in outputs.

The simulator focuses solely on detecting and reacting to signal changes.

### `Iverilog-Based Simulation Flow`

The Iverilog-based simulation flow begins with writing the Design (RTL Verilog module) and a corresponding Testbench that applies input vectors to the design and observes the outputs. Both files are compiled using the iverilog tool to generate a simulation executable. When this executable is run, it produces a Value Change Dump (VCD) file that records signal transitions over time. This VCD file is then viewed using gtkwave, a waveform viewer that allows designers to analyze and debug signal behavior visually, ensuring the design meets the expected functionality.

![Alt Text](Images/Iverilog_based_simulation_flow.png)

### `Process`

**Step 1**: Passing RTL design and corresponding testbench to Iverilog simulator  
![Step 1](Images/1.png)

**Step 2**: Generating VCD (Value Change Dump) file  
![Step 2](Images/2.png)

**Step 3**: Visualizing the dumped variables using gtkwave
![Step 3](Images/3.png)

**Step 4**: Viewing the simulation waveform  
![Step 4](Images/4.png)


### `File Structure: MUX 2:1 Design and Testbench`

To simulate and verify a 2:1 multiplexer, we use two main Verilog files: the design file and the testbench file.

### Design (`good_mux.v`)
- Implements a 2:1 multiplexer using behavioral Verilog with inputs `i0`, `i1`, `sel` and output `y`.
- Uses an `always @(*)` block with an `if-else` construct to select between `i0` and `i1` based on the value of `sel`.
  
![Alt Text](Images/good_mux.png)

### Testbench (`tb_good_mux.v`)
- Instantiates the `good_mux` module and applies stimulus through internal registers `i0`, `i1`, and `sel`.
- Generates a VCD waveform file using `$dumpfile` and `$dumpvars` for output signal observation via gtkwave.

![Alt Text](Images/tb_good_mux.png)


### Yosys Synthesis Flow for `good_mux`

This flow demonstrates how to perform RTL-to-gate-level synthesis using the [Yosys](https://github.com/YosysHQ/yosys) open synthesis suite and the `sky130_fd_sc_hd` standard cell library.


#### Yosys Commands:
## Yosys Synthesis Steps for `good_mux`

Follow the steps below to perform RTL to gate-level synthesis using Yosys and the Sky130 standard cell library.

**Step 0**: Invoke Yosys
-yosys

**Step 1**: Read Liberty file for Sky130 standard cells
-read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

**Step 2**: Read RTL Verilog
-read_verilog good_mux.v

**Step 3**: Synthesize the top-level module
-synth -top good_mux

**Step 4**: Map synthesized RTL to standard cells
-abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

**Step 5**: View synthesized netlist as a schematic
-show

**Step 6**: Write the synthesized gate-level netlist
-write_verilog -noattr good_mux_netlist.v

![Alt Text](Images/5.png)
![Alt Text](Images/6.png)
![Alt Text](Images/7.png)
![Alt Text](Images/8.png)
![Alt Text](Images/9.png)
![Alt Text](Images/10.png)
![Alt Text](Images/11.png)
![Alt Text](Images/12.png)


