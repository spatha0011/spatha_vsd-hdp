# VSD Hardware Design Program

## Day 7 : Timing Graphs using openSTA
OpenSTA(Open Static Timing Analyzer) is a gate level static timing verifier. As a stand-alone executable it can be used to verify the timing of a design using standard file formats.

* Verilog netlist
* Liberty library
* SDC timing constraints
* SDF delay annotation
* SPEF parasitics
* VCD power acitivies
* SAIF power acitivies

OpenSTA uses a TCL command interpreter to read the design, specify timing constraints and print timing reports.

##### Clocks
* Generated
* Latency
* Source latency (insertion delay)
* Uncertainty
* Propagated/Ideal
* Gated clock checks
* Multiple frequency clocks

##### Exception paths
* False path
* Multicycle path
* Min/Max path delay
* Exception points

   -from clock/pin/instance -through pin/net -to clock/pin/instance
*  Edge specific exception points

   -rise_from/-fall_from, -rise_through/-fall_through, -rise_to/-fall_to

##### Delay calculation
* Integrated Dartu/Menezes/Pileggi RC effective capacitance algorithm
* External delay calculator API

##### Analysis
* Report timing checks -from, -through, -to, multiple paths to endpoint
* Report delay calculation
* Check timing setup

## Installation of OpenSTA

#### Step 1: Clone the Repository

```bash
git clone https://github.com/parallaxsw/OpenSTA.git
cd OpenSTA
```

![Alt Text](Images/1.jpg)

#### Step 2: Build the Docker Image
```bash
docker build --file Dockerfile.ubuntu22.04 --tag opensta .
```
This builds a Docker image named opensta using the provided Ubuntu 22.04 Dockerfile. All dependencies are installed during this step.

![Alt Text](Images/2.jpg)

#### Step 3: Run the OpenSTA Container
To run a docker container using the OpenSTA image, use the -v option to docker to mount direcories with data to use and -i to run interactively.
```bash
docker run -i -v $HOME:/data opensta
```
![Alt Text](Images/3.png)

You now have OpenSTA installed and running inside a Docker container. After successful installation, you will see the % prompt—this indicates that the OpenSTA interactive shell is ready for use.

### Timing Ananlysis Using Inline Commands

Once inside the OpenSTA shell (% prompt), you can perform a basic static timing analysis using the following inline commands:
```shell
read_liberty /OpenSTA/examples/nangate45_slow.lib.gz
read_verilog /OpenSTA/examples/example1.v
link_design top
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}
report_checks
```
These commands will:

- Load the standard cell library
- Read the synthesized gate-level Verilog netlist
- Link the design with top module top
- Define a clock with a 10ns period for multiple clock inputs
- Apply input delay constraints
- Generate a timing report showing setup/hold analysis results
  
_This flow is useful for quick testing and debugging without writing a full TCL script._

![Alt Text](Images/4.jpg)

![Alt Text](Images/5.jpg)

### Timing Analysis Using a TCL Script

To automate the timing flow, you can write the commands into a .tcl script and execute it from the OpenSTA shell.

**Sample Script: min_max_delays.tcl**

```shell
# Load liberty files for max and min analysis
read_liberty -max /data/VLSI/VSDBabySoC/OpenSTA/examples/nangate45_slow.lib.gz
read_liberty -min /data/VLSI/VSDBabySoC/OpenSTA/examples/nangate45_fast.lib.gz

# Read the gate-level Verilog netlist
read_verilog /data/VLSI/VSDBabySoC/OpenSTA/examples/example1.v

# Link the top-level design
link_design top

# Define clocks and input delays
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}

# Generate a full min/max timing report
report_checks -path_delay min_max
```

Run the Script Inside OpenSTA

```shell
source /data/VLSI/VSDBabySoC/OpenSTA/examples/min_max_delays.tcl
```
This method ensures repeatability and allows you to maintain reusable timing environments for your design.

![Alt Text](Images/6.jpg)

![Alt Text](Images/7.jpg)


