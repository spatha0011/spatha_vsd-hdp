# VSD Hardware Design Program

## Day 3 : Combinational and sequential optmizations


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
 
### opt1

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

### opt2

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

### opt3

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

### opt4

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


yosys

### opt5

![Alt Text](Images/13.png)

```bash
# Load standard cell library (Liberty format)
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read hierarchical RTL design
read_verilog multiple_module_opt.v

# Synthesize top module
synth -top multiple_module_opt

# Map to standard cells using ABC
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Flatten design hierarchy 
# 🔸 Essential before performing optimization on multi-module RTLs
flatten

# Write out the flattened netlist
write_verilog -noattr multiple_module_opt_flat.v

# Read the flattened netlist for further optimization
read_verilog multiple_module_opt_flat.v

# Remove unused logic and clean netlist
opt_clean -purge   # 🔍 Cleans up redundant gates and wires after flattening

# Visualize optimized gate-level netlist
show
```
![Alt Text](Images/14.png)
