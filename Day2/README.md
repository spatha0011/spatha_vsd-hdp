# VSD Hardware Design Program

## Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

## 📚 Contents

- [Liberty Format](#liberty-format)
- [Standard Cell Example: `a2111o_1`](#standard-cell-example-a2111o_1)
- [Standard Cell Comparison: `and2_1`, `and2_2`, and `and2_4`](#standard-cell-comparison-and2_1-and2_2-and-and2_4)
- [Hierarchical vs Flat Synthesis (Yosys)](#hierarchical-vs-flat-synthesis-yosys)
- [Why Do We Use Flops? (To Avoid Glitches)](#why-do-we-use-flops-to-avoid-glitches)
- [DFF with Asynchronous Reset](#d-flip-flop-with-asynchronous-reset)
- [DFF with Asynchronous Set](#d-flip-flop-with-asynchronous-set)
- [DFF with Synchronous Reset](#d-flip-flop-with-synchronous-reset)
- [DFF with Both Asynchronous and Synchronous Reset](#d-flip-flop-with-both-asynchronous-and-synchronous-reset)
- [Synthesis of DFF with Async Set](#synthesis-of-d-flip-flop-with-asynchronous-set)
- [Synthesis of DFF with Async Reset](#synthesis-of-d-flip-flop-with-asynchronous-reset)
- [Synthesis of DFF with Sync Reset](#synthesis-of-d-flip-flop-with-synchronous-reset)
- [Synthesis of DFF with Both Async and Sync Reset](#synthesis-of-d-flip-flop-with-both-asynchronous-and-synchronous-reset)
- [Interesting Optimisations](#interesting-optimisations)
- [Part 1 - Synthesis of `mul2`](#part1---synthesis-of-mul2)
- [Part 2 - Synthesis of `mult8`](#part2---synthesis-of-mult8)

##  Liberty Format

Liberty format is an industry standard format used to describe library cells of a particular technology. A cell could be a standard cell, IO buffer, complex IP, etc. Library cell description contains a lot of information like timing information, power estimation, and several other attributes like area, functionality, and operating conditions. Speaking more technically, Liberty format is a format to represent timing and power properties of black boxes (which we can't descend into). Liberty is an ASCII format, usually represented in a text file with the extension `.lib`.

The information inside the Lib file can be divided into two main parts, in the first part, it contains some information which is common for all the standard cells. To understand it better have a look in the following snapshot of the Lib file.

![Alt Text](Images/1.png)

The common part of Lib file contains
Library name and technology name
Units (of time, power, voltage, current, resistance and capacitance)
Value of operating condition ( process, voltage and temperature) – Max, Min and Typical 

Based on operating conditions there are three different lib files for Max, Min and Typical corners. 

In the second part of Lib file, it contains cell-specific information for each cell. The part of Lib file which contains cell-specific information is shown below.

![Alt Text](Images/2.png)

## Standard Cell Example: `a2111o_1`

The cell `a2111o_1` from the `sky130_fd_sc_hd__tt_025C_1v80.lib` library implements the following logic:

X = ((A1 & A2) | B1 | C1 | D1)

![Alt Text](Images/3.png)

## Standard Cell Comparison: `and2_1`, `and2_2`, and `and2_4`

This section compares three variants of the 2-input AND gate from the `sky130_fd_sc_hd` library:
- `and2_1`
- `and2_2`
- `and2_4`

![Alt Text](Images/4.png)

## Hierarchical vs Flat Synthesis (Yosys)
A Hierarchical Design contains of multiple sub-modules,instantiated in the 'top' module. 

Hierachical design approach is taken for large designs, to gain the advantage of divide and conquer approach. This leads to better utilization of tool resources for proper optimzation of smaller designs,as a larger design is difficult for a tool to optimize efficiently.

It helps in better utilization of compute resources utilized during design process,and also leads to faster runtime and debugging operations.

A flat design approach is chosen when the design is sufficiently small enough for the tool to optimize efficiently in a reasonable amount of time.

For small and simple ASICs flat approach is preferable while hierarchical approach is preferable for larger,complex ASICs.

### Design : multiple_modules.v

![Alt Text](Images/6.png)

### Hierarchical Flow:
```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
write_verilog -noattr multiple_modules.v
```

It Preserves module hierarchy:

Top: multiple_modules

Submodules: sub_module1, sub_module2

![Alt Text](Images/7.png)

### Flattened Flow:
A flat design is a representation where all hierarchical module structures are collapsed into a single level, eliminating submodules and integrating their logic directly into the top-level module.

No submodules are preserved.

```bash
flatten
write_verilog -noattr multiple_modules_flat.v
```
![Alt Text](Images/flatten.png)

![Alt Text](Images/5.png)


### Why Do We Use Flops? (To Avoid Glitches)
Combinational circuits can produce glitches — short, unwanted changes in output — when the inputs change at slightly different times. This happens because the logic takes a little time to settle when multiple signals arrive with different delays.

These glitches can cause problems, especially if they are captured by other parts of the circuit.

To solve this, we insert flip-flops between combinational blocks. Flip-flops are edge-sensitive, which means they only capture data at a specific moment (like the rising edge of a clock). This helps ensure that only stable, correct data is passed forward, and any glitches that happened before the clock edge are ignored.

![Alt Text](Images/8.png)

## D Flip-Flop with Asynchronous Reset

This module defines a D Flip-Flop with an asynchronous reset. It captures the input d on the rising edge of the clock, unless the asynchronous reset is activated.

![Alt Text](Images/9.png)

In the dff_asyncres module, the asynchronous reset has higher priority than the clock.

async_reset is checked first, so if it is high (1), it immediately resets q to 0.

The clock (clk) only matters when async_reset is low (inactive).

![Alt Text](Images/15.png)

## D Flip-Flop with Asynchronous set

This module defines a D Flip-Flop with an asynchronous set. It captures input d on the rising edge of the clock, unless the asynchronous set is active.

![Alt Text](Images/10.png)

The flip-flop responds to either the positive edge of clk or async_set.

If async_set is high, the output q is immediately set to 1, regardless of the clock.

If async_set is low (inactive), then q takes the value of d on the rising edge of the clock.

![Alt Text](Images/14.png)

## D Flip-Flop with Synchronous Reset

This module defines a D Flip-Flop with a synchronous reset. The flip-flop captures the value of d on the rising edge of the clock, unless the synchronous reset is active.

![Alt Text](Images/11.png)

The always block triggers only on the rising edge of the clock.

If sync_reset is high at the time of the clock edge, the output q is set to 0.

If sync_reset is low, the output q is updated with the input d. 

![Alt Text](Images/16.png)

## D Flip-Flop with Both Asynchronous and Synchronous Reset

This design supports both asynchronous and synchronous reset behavior.

Asynchronous reset has higher priority and takes effect immediately, while synchronous reset is evaluated at the clock edge.

![Alt Text](Images/12.png)

![Alt Text](Images/13.png)

## Synthesis of D Flip-Flop with Asynchronous set

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_async_set.v
synth -top dff_async_set
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

#### Note:
abc -liberty <.lib file path> : This command is used for for technology mapping of yosys’s internal gate library to a target architecture.

synth -top <module_name> : This command runs the yosys synthesis script on the mentioned module name of our design

dfflibmap -liberty <.lib file path> : This command maps internal flipflop cells to the flipflop cells in the technology library specified in the given liberty file.

![Alt Text](Images/18.png)

## Synthesis of D Flip-Flop with Asynchronous Reset

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v 
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Alt Text](Images/17.png)

## Synthesis of D Flip-Flop with Synchronous Reset

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_syncres.v 
synth -top dff_syncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Alt Text](Images/19.png)

## Synthesis of D Flip-Flop with Both Asynchronous and Synchronous Reset

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres_syncres.v
synth -top dff_asyncres_syncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Alt Text](Images/20.png)

## Interesting optimisations

Optimization in synthesis refers to simplifying the RTL logic to make the design more efficient. In the below examples, you can see that the Yosys removes redundant logic, simplifies expressions, and reduces gate count without changing functionality.

## part1 - Synthesis of mul2

![Alt Text](Images/21.png)

![Alt Text](Images/22.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog mult_2.v
synth -top mul2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib # no need to do this step becoz no cells are used #
show
write_verilog -noattr mult2_net.v
```
![Alt Text](Images/23.png)

![Alt Text](Images/24.png)


## part2  - Synthesis of mult8

![Alt Text](Images/28.png)

![Alt Text](Images/25.png)

![Alt Text](Images/26.png)

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog mult_8.v
synth -top mult8
show
write_verilog -noattr mult8_net.v
```

![Alt Text](Images/29.png)

![Alt Text](Images/30.png)
