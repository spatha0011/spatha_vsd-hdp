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
