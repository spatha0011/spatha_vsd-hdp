# VSD Hardware Design Program

## Day 3 : Combinational and sequential optmizations

# 📚 Table of Contents

1. [Combinational Logic Optimisation](#Combinational-Logic-Optimisation)
2. [Sequential Logic Optimisation](#Sequential-Logic-Optimisation)
3. [Combinational Logic Optimisation-labs](#Combinational-Logic-Optimisation-examples)
4.  [sequential-Logic-Optimisation-labs](#sequential-Logic-Optimisation-examples)

### Combinational Logic Optimisation

#### Objective:
Squeeze and simplify logic to get the most optimized design in terms of **area** and **power**.

#### Techniques:

- **Constant Propagation**
  - Replaces logic blocks with constants when input values are fixed.
  - **Example:**
    ```
    Y = ((A·B) + C)' 
    If A = 0 → Y = (0 + C)' = C'
    ```
  - **Result:** Complex gate logic (6 MOS transistors) simplifies to a single inverter (2 MOS transistors).

- **Boolean Logic Optimisation**
  - Simplify Boolean expressions using:
    - Karnaugh Map (K-Map)
    - Quine McCluskey Method
  - **Example Verilog:**
    ```verilog
    assign y = a ? (b ? c : (c ? a : 0)) : (!c);
    ```
    - Optimized expression:  
      ```
      y = a ⊕ c
      ```
**Note:**  
  - The command to perform logic optimization in Yosys is `opt_clean`.  
  - Additionally, for a hierarchical design involving multiple sub-modules, the design must be flattened by running the `flatten` command before executing the `opt_clean` command.

```shell
USAGE:
After the synth -top <module_name> command is executed, do:
    opt_clean -purge

This command identifies wires and cells that are unused and removes them.
The additional switch, purge also removes the internal nets if they have a public name.
```
---

### Sequential Logic Optimisation

#### Basic:
- **Sequential Constant Propagation**  
  - Propagates known constant values through flip-flops during synthesis.

#### Advanced :
- **State Optimization**
  - Reduces the number of states in FSMs to optimize area and transitions.
- **Retiming**
  - Moves registers across logic boundaries to balance delay and improve timing.
- **Sequential Logic Cloning**
  - Duplicates logic in floorplan-aware synthesis to meet timing and congestion goals.
    
## Combinational Logic Optimisation examples
## lab1

![Alt Text](Images/1.png)
![Alt Text](Images/2.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v 
synth -top opt_check
opt_clean -purge # Removes unused or redundant logic #
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Alt Text](Images/5.png)

## lab2

![Alt Text](Images/3.png)
![Alt Text](Images/4.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check2.v 
synth -top opt_check2
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Alt Text](Images/6.png)

## lab3

![Alt Text](Images/7.png)
![Alt Text](Images/8.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check3.v 
synth -top opt_check3
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Alt Text](Images/9.png)

## lab4

![Alt Text](Images/10.png)
![Alt Text](Images/12.jpeg)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check4.v 
synth -top opt_check4
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Alt Text](Images/11.png)

## lab5 - multiple_module_opt1

![Alt Text](Images/13.png)
![Alt Text](Images/14.png)


```bash
# --------Phase 1: Flatten the hierarchical RTL design---------------

# Invoke yosys
yosys

# Load standard cell library (Liberty format)
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read hierarchical RTL design
read_verilog multiple_module_opt.v

# Synthesize top module
synth -top multiple_module_opt

# Map to standard cells using ABC
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Flatten design hierarchy 
# 🔸Essential before performing optimization on multi-module RTLs
flatten

# Write out the flattened netlist
write_verilog -noattr multiple_module_opt_flat.v
```
```bash

# ----------Phase 2: Optimize the flattened netlist-------------

# Invoke yosys
yosys

# Load standard cell library (Liberty format)
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read the flattened netlist for further optimization
read_verilog multiple_module_opt_flat.v

# Synthesize top module
synth -top multiple_module_opt

# Remove unused logic and clean netlist
opt_clean -purge   # Cleans up redundant gates and wires after flattening

# Map to standard cells using ABC
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Visualize optimized gate-level netlist
show
```

![Alt Text](Images/15.png)

>One AND2 gate (a & 1)
>One A21O gate ((a & b) | c)

## lab6 - multiple_module_opt2

![Alt Text](Images/16.png)
![Alt Text](Images/19.png)

```bash
# --------Phase 1: Flatten the hierarchical RTL design---------------

# Invoke yosys
yosys

# Load standard cell library (Liberty format)
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read hierarchical RTL design
read_verilog multiple_module_opt2.v

# Synthesize top module
synth -top multiple_module_opt2

# Map to standard cells using ABC
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Flatten design hierarchy 
# 🔸Essential before performing optimization on multi-module RTLs
flatten

# Write out the flattened netlist
write_verilog -noattr multiple_module_opt2_flat.v
```
```bash

# ----------Phase 2: Optimize the flattened netlist-------------

# Invoke yosys
yosys

# Load standard cell library (Liberty format)
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read the flattened netlist for further optimization
read_verilog multiple_module_opt2_flat.v

# Synthesize top module
synth -top multiple_module_opt2

# Remove unused logic and clean netlist
opt_clean -purge   # Cleans up redundant gates and wires after flattening

# Map to standard cells using ABC
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Visualize optimized gate-level netlist
show
```
![Alt Text](Images/18.png)

## sequential Logic Optimisation examples

## lab1
In this example, no optimization is possible as the flop output, q changes.
![Alt Text](Images/22.png)

![Alt Text](Images/20_a.png)

![Alt Text](Images/20_wave.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Alt Text](Images/20.png)

## lab2
Here, the the flip-flop output, q remains constant at 1 irrespective of the other signals in the sensitivity list.

Thus the realization does not need any cells and q is connected to 1'b1.
![Alt Text](Images/21_design.png)

![Alt Text](Images/21_wave.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Alt Text](Images/21.png)

## lab3
Here, both q1 & q have transitions and thus cannot be optimized further.
So the design will have two DFFs.

![Alt Text](Images/23_a.png)

![Alt Text](Images/23_b.png)

![Alt Text](Images/23_c.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Alt Text](Images/23_d.png)

## lab4
In this example, both q1 & q remain constant at 1'b1.

Thus, the design can be optimized to have only wires. Further, q1 being an internal net can also be removed.
![Alt Text](Images/24_a.png)

![Alt Text](Images/24_b.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const4.v
synth -top dff_const4
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Alt Text](Images/24_c.png)

## lab5

![Alt Text](Images/25_a.png)

![Alt Text](Images/25_b.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const5.v
synth -top dff_const5
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![Alt Text](Images/25_c.png)

## lab6 - unused outputs

![Alt Text](Images/counter_3.png)
![Alt Text](Images/counter_4.png)
This design serves as an example for Sequential logic optimization with designs having unused outputs.

Although we have a 3-bit up counter in the RTL design, only the LSB, count[0:0] is used for generating the output signal, q.

Since count[0:0] toggles every clock cycle, there really is a need for only one flip-flop in the circuit.

In other words, the synthesis output does not have a 3-bit up counter and its associated count incrementing logic.

#### Synthesis Result w/o opt_clean switch

![Alt Text](Images/counter_1.png)

#### Synthesis Result with opt_clean switch

![Alt Text](Images/counter_2.png)
