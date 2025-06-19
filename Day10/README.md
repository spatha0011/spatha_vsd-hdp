# VSD Hardware Design Program

## CMOS Switching threshold and and dynamic simulations

### 📚 Contents

- [Voltage Transfer Characteristics and Spice simulations](#voltage-transfer-characteristics-and-spice-simulations)
- [Labs Sky130 SPICE simulation for CMOS](#labs-sky130-spice-simulation-for-cmos)
  - [Comparison of Voltage Transfer Characteristics (.dc) vs. Transient Response (.tran) Simulations](#comparison-vtc-vs-tran)

### `Voltage Transfer Characteristics and Spice simulations`

#### CMOS Inverter SPICE Deck

This image illustrates how to construct a SPICE deck for a CMOS inverter.

- **Component Connectivity**: Define how PMOS (M1), NMOS (M2), power supply (Vdd), ground (Vss), input (Vin), and output (Vout) are connected.
- **Component Values**: Specify transistor dimensions (W/L), supply voltages (e.g., 2.5V), and load capacitance (Cload = 10fF).
- **Identify Nodes**: Recognize each node in the circuit — such as `in`, `out`, `vdd`, `vss`, and transistor terminals.
- **Name Nodes**: Assign clear names to all nodes for ease of reference during simulation and result interpretation.

This forms the foundation of writing an accurate and functional SPICE netlist for CMOS circuit simulations.

![Alt Text](Images/1.png)

#### CMOS Inverter SPICE Deck Example

This image shows a complete SPICE deck and corresponding CMOS inverter circuit.

![Alt Text](Images/2.png)

**Key Sections of the SPICE Deck:**

1. **Model & Netlist Description:**
    - `M1 out in vdd vdd pmos W=0.375u L=0.25u`: PMOS transistor M1 with width 0.375µm, length 0.25µm. Connected between VDD and output node (`out`), gate connected to input (`in`), bulk tied to VDD.
    - `M2 out in 0 0 nmos W=0.375u L=0.25u`: NMOS transistor M2 with same dimensions, connected between ground and output, gate connected to input, bulk tied to ground.
    - `cload out 0 10f`: Load capacitor (10 fF) connected between output node and ground.

2. **Voltage Sources:**
    - `Vdd vdd 0 2.5`: DC supply voltage of 2.5V between VDD and ground.
    - `Vin in 0 2.5`: DC input voltage, swept during the simulation.

3. **Simulation Commands:**
    - `.op`: Perform operating point analysis (DC bias point).
    - `.dc Vin 0 2.5 0.05`: Perform a DC sweep of `Vin` from 0V to 2.5V in 0.05V steps to generate voltage transfer characteristics (VTC).

4. **Model Inclusion:**
    - `.include tsmc_025um_model.mod`: Include technology model file with parameters for the transistors.
    - `.LIB "tsmc_025um_model.mod" CMOS_MODELS`: Another way to reference the technology model.

5. **End Statement:**
    - `.end`: End of SPICE deck.

### `Labs Sky130 SPICE simulation for CMOS`

<details> <summary><strong>day3_inv_vtc_Wp084_Wn036.spice </strong></summary>

```
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end
```
</details>

📈**plot the waveforms in ngspice**

```shell
ngspice day3_inv_vtc_Wp084_Wn036.spice
plot out vs in
```

Below image shows the Voltage Transfer Characteristics (VTC) of a CMOS Inverter:

![Alt Text](Images/3.png)

<details> <summary><strong>day3_inv_tran_Wp084_Wn036.spice </strong></summary>

```
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

*simulation commands

.tran 1n 10n

.control
run
.endc

.end
```
</details>

📈**plot the waveforms in ngspice**

```shell
ngspice day3_inv_tran_Wp084_Wn036.spice
plot out vs time in
```

Below image shows the output waveform of transient analysis of a CMOS inverter, illustrating rise time delay and fall time delay:

**How to calculate rise time and fall time from the transient analysis:**

- Output **rise time delay** = Time at 50% of rising edge − Time at 50% of falling edge  
- Output **fall time delay** = Time at 50% of falling edge − Time at 50% of rising edge  

![Alt Text](Images/4.png)

<a name="comparison-vtc-vs-tran"></a>

### 📊 Comparison of Voltage Transfer Characteristics (.dc) vs. Transient Response (.tran) Simulations

| Aspect                    | Voltage Transfer Characteristics (DC Sweep)          | Transient Response (Dynamic Simulation)                                  |
|---------------------------|-----------------------------------------------------|-------------------------------------------------------------------------|
| Simulation Type           | DC Sweep (Voltage Transfer Characteristics - VTC)    | Transient (Dynamic behavior over time)                                   |
| Input Signal (`Vin`)      | Fixed DC sweep: 0V to 1.8V in 0.01V steps             | Pulse waveform: 0V to 1.8V, 2ns width, 4ns period                        |
| Simulation Command        | `.dc Vin 0 1.8 0.01`                                 | `.tran 1n 10n`                                                           |
| Purpose                   | Plot static VTC curve of CMOS inverter               | Analyze rise/fall times and delays under dynamic switching                |
| Typical Output            | Vout vs Vin graph                                    | Vout vs Time, Vin vs Time waveforms                                       |
| Analysis Goal             | Understand switching threshold & noise margins       | Measure timing parameters (propagation delay, rise time, fall time)       |
