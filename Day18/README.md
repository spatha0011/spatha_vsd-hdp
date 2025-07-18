# VSD Hardware Design Program

## Magic Layout and ngspice characterization

### 📚 Table of Contents

- [Cell Design and characterization flows](#cell-design-and-characterization-flows)
  - [Inputs](#inputs)
  - [Standard Cell Design Flow](#standard-cell-design-flow)
  - [Outputs](#outputs)
  - [Standard Cell Characterization Flow](#standard-cell-characterization-flow)
- [Timing Characterization](#timing-characterization)
  - [Timing Threshold Definitions](#timing-threshold-definitions)
  - [Propagation Delay & Transition Time](#propagation-delay--transition-time)
    - [Propagation Delay](#propagation-delay)
    - [Transition Time](#transition-time)
- [Design Library Cell using magic layout and ngspice charcterization](#design-library-cell-using-magic-layout-and-ngspice-charcterization)
  - [16-Mask CMOS N-Well Process – Summary](#16-mask-cmos-n-well-process--summary)
  - [Spice extraction of inverter in magic](#spice-extraction-of-inverter-in-magic)
  - [Editing the spice model file for analysis through simulation](#editing-the-spice-model-file-for-analysis-through-simulation)
  - [Post-layout ngspice simulations](#post-layout-ngspice-simulations)
- [Rise Transition Time Calculation](#rise-transition-time-calculation)
- [Fall Transition Time Calculation](#fall-transition-time-calculation)
- [Rise Cell Delay Calculation](#rise-cell-delay-calculation)
- [Fall Cell Delay Calculation](#fall-cell-delay-calculation)
- [Find problem in the DRC section of the old magic tech file for the skywater process and fix them](#find-problem-in-the-drc-section-of-the-old-magic-tech-file-for-the-skywater-process-and-fix-them)
  - [DRC Rule Debug Summary](#summary)
  
## `Cell Design and characterization flows`

A **library** is a collection of standard cells, each defined by its size, functionality, threshold voltage, and other electrical/physical properties. These libraries are fundamental to the ASIC flow for synthesis, placement, and timing analysis.

#### <ins>Inputs</ins>

- **PDKs (Process Design Kits):**
  - DRC & LVS decks
  - SPICE models
- **Library & User-Defined Specifications**

#### <ins>Standard Cell Design Flow</ins>

1. **Circuit Design**
2. **Layout Design**
   - Involves techniques like Euler paths and stick diagrams
3. **Parasitic Extraction**
4. **Characterization**
   - Timing
   - Power
   - Noise

#### <ins>Outputs<ins>

- `CDL` – Circuit Description Language file
- `LEF` – Library Exchange Format
- `GDSII` – Final layout database
- Extracted SPICE netlist (`.cir`)
- Characterized `.lib` files:
  - Timing
  - Power
  - Noise

#### <ins>Standard Cell Characterization Flow</ins>

A typical standard cell characterization process includes:

1. Read in SPICE models and technology files
2. Load the extracted SPICE netlist
3. Recognize cell behavior
4. Identify subcircuits
5. Attach power sources
6. Apply stimulus to the setup
7. Set output capacitance loads
8. Provide necessary simulation commands

All these steps are described in a configuration file and passed to a characterization tool such as **GUNA**. The tool simulates the cells and generates:

- Timing Models  
- Power Models  
- Noise Models

These are exported in `.lib` format and used in synthesis and static timing analysis flows.

![Alt Text](Images/1.jpg)

### <ins>Timing Characterization</ins>

In standard cell characterization, one of the key components is **Timing Characterization**, which defines how a cell behaves with respect to input signal changes over time.

#### Timing Threshold Definitions

| **Timing Definition**      | **Value**       |
|---------------------------|-----------------|
| `slew_low_rise_thr`       | 20% of signal   |
| `slew_high_rise_thr`      | 80% of signal   |
| `slew_low_fall_thr`       | 20% of signal   |
| `slew_high_fall_thr`      | 80% of signal   |
| `in_rise_thr`             | 50% of signal   |
| `in_fall_thr`             | 50% of signal   |
| `out_rise_thr`            | 50% of signal   |
| `out_fall_thr`            | 50% of signal   |


#### <ins>Propagation Delay & Transition Time</ins>

#### <ins>Propagation Delay</ins>

**Definition**:  
The time difference between the input signal reaching 50% of its final value and the output reaching 50% of its final value.

```text
Propagation Delay = time(out_thr) - time(in_thr)
```

**in_thr (input threshold time):**

The time at which the input signal crosses its defined threshold voltage during a transition.

For delay measurement, this is typically the 50% point of the input voltage swing.

**out_thr (output threshold time):**

The time at which the output signal crosses its threshold voltage during the response to the input transition.

Also typically measured at the 50% point for consistency with in_thr.

![Alt Text](Images/3.jpg)

Poor choice of threshold values lead to negative delay values. Even thought you have taken good threshold values, sometimes depending upon how good or bad the slew, the dealy might be still +ve or -ve.

![Alt Text](Images/4.jpg)

#### <ins>Transition Time</ins>

**Definition**:  
The time it takes for a signal to transition between logic states, typically measured between 10–90% or 20–80% of the voltage levels.

```text
Rise Transition Time = time(slew_high_rise_thr) - time(slew_low_rise_thr)
Fall Transition Time = time(slew_high_fall_thr) - time(slew_low_fall_thr)
```
**slew_low_rise_thr:**
The time when the rising input or output crosses the lower threshold, usually 20% of the voltage swing.

**slew_high_rise_thr:**
The time when the rising input or output crosses the upper threshold, usually 80% of the voltage swing.

**slew_high_fall_thr:**
The time when the falling input or output crosses the upper threshold, typically 80%.

**slew_low_fall_thr:**
The time when the falling input or output crosses the lower threshold, typically 20%.

![Alt Text](Images/2.jpg)

## `Design Library Cell using magic layout and ngspice charcterization`

First, clone the required mag files and spicemodels of inverter,pmos and nmos sky130.

```shell
cd ~cd ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane/

git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```

To view the layout of the inverter in magic :
```shell
magic -T sky130A.tech sky130_inv.mag &
```

Ampersand at the end makes the next prompt line free, otherwise magic keeps the prompt line busy. Once we run the magic command we get the layout of the inverter in the magic window

Upon running that command we would be able to see the `CMOS Inverter` in magic.

![Alt Text](Images/5.jpg)

PMOS source connectivity to VDD (here VPWR) verified

![Alt Text](Images/m3.jpg)

NMOS source connectivity to VSS (here VGND) verified

![Alt Text](Images/m4.jpg)

#### 	<ins>16-Mask CMOS N-Well Process – Summary</ins>

The 16-mask CMOS fabrication process is a standard method used in the semiconductor industry to manufacture integrated circuits (ICs). This process involves a series of photolithography, doping, deposition, and etching steps that define the active and passive components of a CMOS circuit. Each mask step is critical in shaping specific layers or features of the chip, ensuring proper device functionality and integration.

**1. Substrate Preparation**  
The process begins with a high-quality silicon wafer. This wafer acts as the base substrate on which all devices are built. It is thoroughly cleaned to remove any impurities that could affect device performance.

**2. N-Well Formation**  
N-well regions are formed by introducing n-type dopants such as phosphorus into specific areas of the substrate using ion implantation or diffusion. These regions serve as the body for PMOS transistors.

**3. P-Well Formation**  
P-well regions are created using ion implantation or diffusion of p-type dopants such as boron. These regions form the body for NMOS transistors. In a twin-well process, both N-well and P-well regions are used to independently optimize NMOS and PMOS performance.

**4. Gate Oxide Deposition**  
A thin insulating layer of silicon dioxide is thermally grown or deposited over the surface of the wafer. This layer electrically isolates the gate electrode from the underlying silicon channel.

**5. Polysilicon Deposition**  
A layer of polysilicon is deposited over the entire wafer surface. This will later be patterned to form the gate electrodes of the transistors.

**6. Polysilicon Masking and Etching**  
A photoresist mask is applied to define the gate regions. The exposed polysilicon is etched away, leaving the gate structures in place over the gate oxide.

**7. N-Well Masking and Implantation**  
A mask is used to expose only the regions where additional N-well doping is needed. Phosphorus or arsenic is implanted to adjust the doping concentration and improve PMOS characteristics.

**8. P-Well Masking and Implantation**  
Similar to N-well masking, a mask is used to define regions for P-well adjustment. Boron is implanted into the exposed regions to enhance NMOS performance.

**9. Source/Drain Implantation**  
Using another photolithography step, openings are created to define the source and drain regions for both NMOS and PMOS transistors. Appropriate dopants (e.g., arsenic or phosphorus for NMOS, boron or BF₂ for PMOS) are implanted.

**10. Gate Formation Finalization**  
The gate electrode pattern is refined, and any alignment steps are performed to ensure that the gate overlaps properly with the channel region between source and drain.

**11. Source/Drain Masking and Etching**  
A photoresist mask is applied again to define the contact regions. Etching is performed to remove the insulating oxide over the source and drain terminals.

**12. Contact/Via Formation**  
Contact holes or vias are etched through the insulating oxide to expose source, drain, and gate terminals. These vias will later be filled with metal to establish electrical connections.

**13. Metal Deposition**  
A metal layer, typically aluminum or copper, is deposited across the wafer. This layer forms the interconnects that connect transistors and other circuit components.

**14. Metal Masking and Etching**  
Photolithography is used to define the desired interconnect patterns. Unwanted metal is etched away, leaving only the functional routing and connections.

**15. Passivation Layer Deposition**  
A passivation layer of silicon dioxide or silicon nitride is deposited over the entire wafer to protect the circuit from mechanical damage, moisture, and contamination.

**16. Final Testing and Packaging**  
The completed wafer is tested to identify functional and defective chips. The functional chips are then diced, packaged into individual components, and prepared for use in electronic systems.

<p align="center">
  <img src="Images/7.jpg" alt="Left Image" width="45%" style="display:inline-block; margin-right: 10px;" />
  <img src="Images/6.jpg" alt="Right Image" width="45%" style="display:inline-block;" />
</p>

### <ins>Spice extraction of inverter in magic</ins>

Commands for spice extraction of the custom inverter layout to be used in tkcon window of magic

```shell
# Check current directory
pwd

# Extraction command to extract to .ext format
extract all

# Before converting ext to spice this command enable the parasitic extraction also
ext2spice cthresh 0 rthresh 0

# Converting to ext to spice
ext2spice
```

Screenshot of tkcon window after running above commands:

![Alt Text](Images/m5.jpg)

Screenshot of created spice file:

![Alt Text](Images/m6.jpg)

#### <ins>Editing the spice model file for analysis through simulation</ins>

Measuring unit distance in layout grid:

![Alt Text](Images/m7.jpg)

Final edited spice file ready for ngspice simulation:

![Alt Text](Images/m8.jpg)

#### <ins>Post-layout ngspice simulations</ins>

Commands for ngspice simulation

```shell
# Command to directly load spice file for simulation to ngspice
ngspice sky130_inv.spice

# Now that we have entered ngspice with the simulation spice file loaded we just have to load the plot
plot y vs time a
```

Screenshots of ngspice run:

![Alt Text](Images/m9.jpg)

Screenshot of generated plot:

![Alt Text](Images/m10.jpg)

### `Rise Transition Time Calculation`

#### <ins>Formula</ins>

```
Rise transition time = Time taken for output to rise to 80% − Time taken for output to rise to 20%
```

**Rise Time = T<sub>80%</sub> − T<sub>20%</sub>**


#### <ins>Reference Values (for VDD = 3.3V)</ins>

- **20% of output voltage:**  
  `0.20 × 3.3V = 660 mV`

- **80% of output voltage:**  
  `0.80 × 3.3V = 2.64 V`

The **rise transition time** is the **difference in time between the output reaching 2.64 V and 660 mV** during the rising edge of the signal.

output rising to 20%:

![Alt Text](Images/g1_20.jpg)

output rising to 80%:

![Alt Text](Images/g1_80.jpg)

terminal values:

![Alt Text](Images/g1_t.jpg)

#### <ins>Rise Transition Time Calculation from ngspice</ins>

> - Time at **20% of V<sub>out</sub>** (0.66 V): `t_20 = 2.1800 ns`  
> - Time at **80% of V<sub>out</sub>** (2.64 V): `t_80 = 2.2463 ns`

> **Formula**:  
> `Rise Time = t_80 − t_20`  
> `= 2.2463 ns − 2.1800 ns`  
> `= 0.0663 ns`  
> `= 66.3 ps`

> ✅ **Rise Transition Time = 66.3 ps**

### `Fall Transition Time Calculation`

```
Fall transition time = Time taken for output to fall to 20% − Time taken for output to fall to 80%
```

**Fall Time = T<sub>20%</sub> − T<sub>80%</sub>**


#### <ins>Reference Values (for VDD = 3.3V)<ins>

- **20% of output voltage:**  
  `0.20 × 3.3V = 660 mV`

- **80% of output voltage:**  
  `0.80 × 3.3V = 2.64 V`

The **fall transition time** is the **difference in time between the output falling from 2.64 V to 660 mV** during the falling edge of the signal.

output falling to 20%:

![Alt Text](Images/g2_t20.jpg)

output falling to 80%:

![Alt Text](Images/g2_t80.jpg)

terminal values:

![Alt Text](Images/g2_t.jpg)


#### <ins>Fall Transition Time Calculation from ngspice</ins>

> - Time at **80% of V<sub>out</sub>** (2.64 V): `t_80 = 4.0518 ns`  
> - Time at **20% of V<sub>out</sub>** (0.66 V): `t_20 = 4.0953 ns`

> **Formula**:  
> `Fall Time = t_20 − t_80`  
> `= 4.0953 ns − 4.0518 ns`  
> `= 0.0435 ns`  
> `= 43.5 ps`

> ✅ **Fall Transition Time = 43.5 ps**

### `Rise Cell Delay Calculation`

**Rise Cell Delay** is the time it takes for the output to reach 50% of V<sub>DD</sub> after the input begins transitioning.

**Formula**:  
`Rise Cell Delay = Time(output rises to 50%) − Time(input falls to 50%)`

For V<sub>DD</sub> = 3.3V,  

50% of V<sub>DD</sub> = **1.65V**

output rising to 50% and input falling to 50%:

![Alt Text](Images/g3_50.jpg)

terminal values:

![Alt Text](Images/g3_t.jpg)

> `Rise Cell Delay = Time(output @ 50%) − Time(input @ 50%)`  
> `= 2.21079e-09 − 2.15e-09`  
> `= 0.06079e-09 s`  
> `= 60.79 ps`

> ✅ **Rise Cell Delay = 60.79 ps**

### `Fall Cell Delay Calculation`

**Fall Cell Delay** is the time it takes for the output to fall to 50% of V<sub>DD</sub> after the input begins transitioning.

**Formula**:  
`Fall Cell Delay = Time(output falls to 50%) − Time(input rises to 50%)`

For V<sub>DD</sub> = 3.3V,  

50% of V<sub>DD</sub> = **1.65V**

output falling to 50% and input rising to 50%:

![Alt Text](Images/g4_50.jpg)

terminal values:

![Alt Text](Images/g4_t.jpg)

> `Fall Cell Delay = Time(output @ 50%) − Time(input @ 50%)`  
> `= 4.07745e-09 − 4.05e-09`  
> `= 0.02745e-09 s`  
> `= 27.45 ps`

> ✅ **Fall Cell Delay = 27.45 ps**

### `Find problem in the DRC section of the old magic tech file for the skywater process and fix them`

Link to Sky130 Periphery rules: https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html

Commands to download and view the corrupted skywater process magic tech file and associated files to perform drc corrections

```shell
# Change to home directory
cd ~

# Command to download the lab files
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

# Since lab file is compressed command to extract it
tar xfz drc_tests.tgz

# Change directory into the lab folder
cd drc_tests

# List all files and directories present in the current directory
ls -al

# Command to view .magicrc file
gvim .magicrc

# Command to open magic tool in better graphics
magic -d XR &
```
Screenshot of .magicrc file:

![Alt Text](Images/magicrc.jpg)

First load the poly file by using the cmd 'load poly' on tkcon window:

![Alt Text](Images/s3.jpg)

Incorrectly implemented poly.9 rule no drc violation even though spacing < 0.48u:

![Alt Text](Images/s4.jpg)

Screenshot of poly rules from the website:

![Alt Text](Images/s5.jpg)

New commands inserted in `sky130A.tech` file to update drc:

![Alt Text](Images/add1.jpg)

![Alt Text](Images/add2.jpg)

Commands to run in tkcon window:

```shell
# Loading updated tech file
tech load sky130A.tech

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented:

![Alt Text](Images/add3.jpg)

#### <ins>Summary:</ins>

To enforce the poly.9 design rule correctly—which specifies that poly resistors must maintain a minimum spacing of 0.48 µm (480 nm) from diffusion (alldiff) and non-resistor poly (allpolynonres) layers—new DRC rules were added to the sky130A.tech file. The original implementation did not flag violations even when spacing was below the required 0.48 µm, indicating the rule was either missing or incorrectly defined. To correct this, two new spacing rules were inserted: one between npres and alldiff, and another between npres and allpolynonres, both with touching_illegal condition and corresponding rule IDs referencing poly.9. After updating the .tech file, the following commands were executed in the Magic tool’s TkCon window: tech load sky130A.tech to reload the updated tech file, drc check to rerun the DRC engine, and drc why to inspect specific violations. As seen in the final Magic screenshot, the DRC now correctly flags violations where spacing is less than 0.48 µm, confirming that the poly.9 rule is implemented and functional.

