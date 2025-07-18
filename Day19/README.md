# VSD Hardware Design Program

## Pre-layout timing analysis and importance of good clock tree

### `Fix up small DRC errors and verify the design is ready to be inserted into our flow`

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

Condition 1 verified:

![Alt_Text](Images/4.jpg)

Condition 2 verified:

Horizontal track pitch = 0.46 µm

![Alt_Text](Images/5.jpg)

Width of standard cell = 1.38 µm = 0.46 × 3

Condition 3 verified:

Vertical track pitch = 0.34 µm

![Alt_Text](Images/6.jpg)

Height of standard cell = 2.72 µm = 0.34 × 8
