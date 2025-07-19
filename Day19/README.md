# VSD Hardware Design Program

## Pre-layout timing analysis and importance of good clock tree

### 1.Fix up small DRC errors and verify the design is ready to be inserted into our flow

Conditions to be verified before moving forward with custom designed cell layout:

- Condition 1: The input and output ports of the standard cell should lie on the intersection of the vertical and horizontal tracks.
- Condition 2: **Width** of the standard cell should be **odd multiples** of the **horizontal track pitch**.
- Condition 3: **Height** of the standard cell should be **even multiples** of the **vertical track pitch**.
  
Commands to open the custom inverter layout:

```shell
# Change directory to vsdstdcelldesign
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```
![Alt_Text](Images/1.jpg)

Screenshot of tracks.info of sky130_fd_sc_hd:

![Alt_Text](Images/2.jpg)

Commands for tkcon window to set grid as tracks of locali layer:

```shell
# Get syntax for grid command
help grid

# Set grid values accordingly
grid 0.46um 0.34um 0.23um 0.17um
```

Screenshot of commands run:

![Alt_Text](Images/3.jpg)

**Condition 1 verified:**

![Alt_Text](Images/4.jpg)

**Condition 2 verified:**

Horizontal track pitch = 0.46 µm

![Alt_Text](Images/5.jpg)

Width of standard cell = 1.38 µm = 0.46 × 3

**Condition 3 verified:**

Vertical track pitch = 0.34 µm

![Alt_Text](Images/6.jpg)

Height of standard cell = 2.72 µm = 0.34 × 8

### 2. Save the finalized layout with custom name and open it.

Command for tkcon window to save the layout with custom name:

```shell
# Command to save as
save sky130_vsdinv.mag
```

Command to open the newly saved layout:

```shell
# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_vsdinv.mag &
```

Screenshot of newly saved layout:

![Alt_Text](Images/7.jpg)

### 3. Generate lef from the layout.

Command for tkcon window to write lef

```shell
# lef command
lef write
```
Screenshot of command run:

![Alt_Text](Images/8.jpg)

Screenshot of newly created lef file:

```shell
gvim sky130_vsdinv.lef
```
![Alt_Text](Images/9.jpg)

### 4. Copy the newly generated lef and associated required lib files to 'picorv32a' design 'src' directory.

Commands to copy necessary files to 'picorv32a' design 'src' directory

```shell
# Copy lef file
cp sky130_vsdinv.lef ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# Copy lib files
cp libs/sky130_fd_sc_hd__* ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

```

### 5. Edit 'config.tcl' to change lib file and add the new extra lef into the openlane flow.

Commands to be added to config.tcl to include our custom cell in the openlane flow

```shell
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

Edited config.tcl to include the added lef and change library to ones we added in src directory:

![Alt_Text](Images/10.jpg)

#### 6. Run openlane flow synthesis with newly inserted custom inverter cell.
Commands to invoke the OpenLANE flow include new lef and perform synthesis

```shell
# Change directory to openlane flow directory
cd ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane

export PDK_ROOT=/home/spatha/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/pdks

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```

```shell
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

![Alt_Text](Images/11.jpg)

### 7. Remove/reduce the newly introduced violations with the introduction of custom inverter cell by modifying design parameters.

Noting down current design values generated before modifying parameters to improve timing.

![Alt_Text](Images/12.jpg)

![Alt_Text](Images/13.jpg)

Commands to view and change parameters to improve timing and run synthesis

```shell
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag new -overwrite

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]

add_lefs -src $lefs

# Command to display current value of variable SYNTH_STRATEGY
echo $::env(SYNTH_STRATEGY)

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to display current value of variable SYNTH_BUFFERING to check whether it's enabled
echo $::env(SYNTH_BUFFERING)

# Command to display current value of variable SYNTH_SIZING
echo $::env(SYNTH_SIZING)

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Comparing to previously noted run values area has increased and worst negative slack has become 0

![Alt_Text](Images/14.jpg)

![Alt_Text](Images/15.jpg)

### 8. Once synthesis has accepted our custom inverter we can now run floorplan and placement and verify the cell is accepted in PnR flow.

Now that our custom inverter is properly accepted in synthesis we can now run floorplan using following command

```shell
# Now we can run floorplan
run_floorplan
```
Screenshots of command run:

![Alt_Text](Images/16.jpg)

Since we are facing unexpected un-explainable error while using run_floorplan command, we can instead use the following set of commands available based on information from 

/home/spatha/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl

and also based on Floorplan Commands section in 

/home/spatha/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md

```shell
# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or
```

Screenshots of commands run:

![Alt_Text](Images/17.jpg)

![Alt_Text](Images/18.jpg)

![Alt_Text](Images/19.jpg)

Now that floorplan is done we can do placement using following command

```shell
# Now we are ready to run placement
run_placement
```

Screenshots of commands run:

![Alt_Text](Images/20.jpg)

Commands to load placement def in magic in another terminal

```shell
# Change directory to path containing generated placement def
cd ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/new/results/placement/

# Command to load the placement def in magic tool
magic -T ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &

```

Screenshot of placement def in magic:

![Alt_Text](Images/21.jpg)

Screenshot of custom inverter inserted in placement def with proper abutment:

![Alt_Text](Images/22.jpg)

Command for tkcon window to view internal layers of cells

```shell
# Command to view internal connectivity layers
expand
```

![Alt_Text](Images/23.jpg)

![Alt_Text](Images/24.jpg)

### 9. Do Post-Synthesis timing analysis with OpenSTA tool.

Since we are having 0 wns after improved timing run we are going to do timing analysis on initial run of synthesis which has lots of violations and no parameters were added to improve timing

Commands to invoke the OpenLANE flow include new lef and perform synthesis

```shell
# Change directory to openlane flow directory
cd ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane

export PDK_ROOT=/home/spatha/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/pdks

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```shell
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Screenshot of commands run:

![Alt_Text](Images/25.jpg)

Newly created `pre_sta.conf` for STA analysis in `openlane` directory

![Alt_Text](Images/32.jpg)

Newly created `my_base.sdc` for STA analysis in `openlane/designs/picorv32a/src` directory based on the file `openlane/scripts/base.sdc`

![Alt_Text](Images/27.jpg)

Commands to run STA in another terminal

```
# Change directory to openlane
cd /home/spatha/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane

# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```

![Alt_Text](Images/28.jpg)

Since more fanout is causing more delay we can add parameter to reduce fanout and do synthesis again

Commands to include new lef and perform synthesis

```shell
# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a -tag 19-07_00-15 -overwrite

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to set new value for SYNTH_MAX_FANOUT
set ::env(SYNTH_MAX_FANOUT) 4

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Screenshot of commands run:

![Alt_Text](Images/30.jpg)

Commands to run STA in another terminal

```shell
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```

![Alt_Text](Images/31.jpg)

### 10. Make timing ECO fixes to remove all violations.

OR gate of drive strength 2 is driving 4 fanouts

![Alt_Text](Images/33.jpg)

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```shell
report_net -connections _11873_
help replace_cell
replace_cell _14770_ sky130_fd_sc_hd__or3_4
report_checks -fields {net cap slew input_pins} -digits 4
```

![Alt_Text](Images/34.jpg)

Result - slack reduced

![Alt_Text](Images/35.jpg)

OR gate of drive strength 2 driving OA gate has more delay

![Alt_Text](Images/36.jpg)

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```shell
# Reports all the connections to a net
report_net -connections _11844_

# Replacing cell
replace_cell _14741_ sky130_fd_sc_hd__or4_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins fanout} -digits 4
```

![Alt_Text](Images/37.jpg)

Result - slack reduced

![Alt_Text](Images/38.jpg)

OR gate of drive strength 2 driving OA gate has more delay

![Alt_Text](Images/39.jpg)

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```shell
# Reports all the connections to a net
report_net -connections _11869_

# Replacing cell
replace_cell _14766_ sky130_fd_sc_hd__or4_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```

![Alt_Text](Images/40.jpg)

Result - slack reduced

![Alt_Text](Images/41.jpg)

Commands to verify instance _14766_ is replaced with sky130_fd_sc_hd__or4_4

```shell
report_checks -from _29555_ -to _30952_ -through _14766_
```

Screenshot of replaced instance

![Alt_Text](Images/42.jpg)

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```shell
% report_net -connections _12396_
Warning: pre_sta.conf line 1, report_net -connections is deprecated.
Net _12396_
 Pin capacitance: 0.01-0.01
 Wire capacitance: 0.00
 Total capacitance: 0.01-0.01
 Number of drivers: 1
 Number of loads: 3
 Number of pins: 4

Driver pins
 _15474_/X output (sky130_fd_sc_hd__or2_2)

Load pins
 _15475_/B input (sky130_fd_sc_hd__or2_2) 0.00-0.00
 _15505_/A2 input (sky130_fd_sc_hd__a211oi_2) 0.00-0.00
 _15507_/C1 input (sky130_fd_sc_hd__o211a_2) 0.00-0.00

% replace_cell _15474_ sky130_fd_sc_hd__or2_4
1
```

![Alt_Text](Images/43.jpg)

_We started ECO fixes at WNS = -23.8900 ns, and have now improved it to WNS = -22.7650 ns, achieving a reduction of approximately 1.1250 ns in worst negative slack._

### 11. Replace the old netlist with the new netlist generated after timing ECO fix and implement the floorplan, placement and cts.

