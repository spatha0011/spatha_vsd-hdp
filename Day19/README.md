# VSD Hardware Design Program

## Pre-layout timing analysis and importance of good clock tree

### 1.Fix up small DRC errors and verify the design is ready to be inserted into our flow

Conditions to be verified before moving forward with custom designed cell layout:

- Condition 1: The input and output ports of the standard cell should lie on the intersection of the vertical and horizontal tracks.
- Condition 2: Width of the standard cell should be odd multiples of the horizontal track pitch.
- Condition 3: Height of the standard cell should be even multiples of the vertical track pitch.
  
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

Height of standard cell = 2.72 µm = 0.34 × 8

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
cd Desktop/work/tools/openlane_working_dir/openlane

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
