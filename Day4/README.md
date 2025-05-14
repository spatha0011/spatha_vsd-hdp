# VSD Hardware Design Program

## Day 4 : GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

![Alt Text](Images/1.png)
![Alt Text](Images/2.png)
![Alt Text](Images/3.png)
![Alt Text](Images/4.png)
![Alt Text](Images/5.png)
![Alt Text](Images/7.png)

## Synthesis of Ternary Operator MUX using Yosys and Sky130

This module implements a 2:1 multiplexer using a ternary (conditional) operator.

![Alt Text](Images/8_a.png)

```bash
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
![Alt Text](Images/8_b.png)

```bash
# Launch Yosys
yosys

# Step 1: Read Sky130 Liberty File
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Step 2: Read Verilog RTL Design
read_verilog ternary_operator_mux.v

# Step 3: Synthesize the design with the given top module
synth -top ternary_operator_mux

# Step 4: Technology Mapping using ABC
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Step 5: View the schematic
show

# Step 6: Export the gate-level netlist
write_verilog -noattr ternary_operator_mux_net.v
```

![Alt Text](Images/8_c.png)

## Gate-Level Simulation (GLS) of Ternary Operator MUX

Gate-Level Simulation is performed using the synthesized netlist (ternary_operator_mux_net.v) instead of RTL. This helps verify the functional correctness of the design after synthesis, using the actual standard cells and any delays (if modeled)

```bash
# Compile the gate-level netlist and testbench
iverilog ternary_operator_mux_net.v tb_ternary_operator_mux.v -o gls_out

# Run the simulation binary
./a.out

# View the waveform
gtkwave tb_ternary_operator_mux.vcd
```
![Alt Text](Images/9_a.png)
