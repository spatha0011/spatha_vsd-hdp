# VSD Hardware Design Program

## Floorplan and Placement of VSDBabySoC

### 📚 Contents

###  `RTL2GDS Flow for VSDBabySoC: Initial Steps`

1. **Create Directories:**
   - Inside `OpenROAD-flow-scripts/flow/designs/sky130hd/`, create a folder named `vsdbabysoc`.
   - Create another folder named `vsdbabysoc` in `OpenROAD-flow-scripts/flow/designs/src/` and place all Verilog files here.

2. **Copy Folders:**
   - From your `VSDBabySoC` folder, copy the following folders into `sky130hd/vsdbabysoc`:
     - **gds:** Contains `avsddac.gds`, `avsdpll.gds`.
     - **include:** Contains `sandpiper.vh`, `sandpiper_gen.vh`, `sp_default.vh`, `sp_verilog.vh`.
     - **lef:** Contains `avsddac.lef`, `avsdpll.lef`.
     - **lib:** Contains `avsddac.lib`, `avsdpll.lib`.

3. **Copy Constraint and Configuration Files:**
   - Copy `vsdbabysoc_synthesis.sdc` into `sky130hd/vsdbabysoc`.
   - Copy `macro.cfg` and `pin_order.cfg` into `sky130hd/vsdbabysoc`.

4. **Create Config File:**
   - Create a `config.mk` file in `sky130hd/vsdbabysoc` with the required configuration details. 

 ##### The file details are given below:
 ```shell
  # Design and Platform Configuration
   export DESIGN_NICKNAME = vsdbabysoc
   export DESIGN_NAME = vsdbabysoc
   export PLATFORM    = sky130hd

  # Design Paths
  export vsdbabysoc_DIR = /home/spatha/OpenROAD-flow-scripts/flow/designs/sky130hd/$(DESIGN_NICKNAME)

  # Explicitly list Verilog files for synthesis
   export VERILOG_FILES = /home/spatha/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/vsdbabysoc.v \
                         /home/spatha/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/rvmyth.v \
                         /home/spatha/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/clk_gate.v


  # Include Directory for Verilog Header Files
   export VERILOG_INCLUDE_DIRS = $(vsdbabysoc_DIR)/include

  # Constraints File
    export SDC_FILE = $(vsdbabysoc_DIR)/vsdbabysoc_synthesis.sdc

  # Additional GDS Files
    export ADDITIONAL_GDS = $(vsdbabysoc_DIR)/gds/avsddac.gds \
                            $(vsdbabysoc_DIR)/gds/avsdpll.gds

  # Additional LEF Files
   export ADDITIONAL_LEFS = $(vsdbabysoc_DIR)/lef/avsddac.lef \
                            $(vsdbabysoc_DIR)/lef/avsdpll.lef

  # Additional LIB Files
   export ADDITIONAL_LIBS = $(vsdbabysoc_DIR)/lib/avsddac.lib \
                            $(vsdbabysoc_DIR)/lib/avsdpll.lib

 # Pin Order and Macro Placement Configurations
   export FP_PIN_ORDER_CFG = $(vsdbabysoc_DIR)/pin_order.cfg
   export MACRO_PLACEMENT_CFG = $(vsdbabysoc_DIR)/macro.cfg

 # Clock Configuration
   export CLOCK_PORT = CLK
   export CLOCK_NET  = $(CLOCK_PORT)
   export CLOCK_PERIOD = 20.0

# Floorplanning Configuration
  export DIE_AREA   = 0 0 1600 1600
  export CORE_AREA  = 20 20 1590 1590

# Placement Configuration
  export PLACE_PINS_ARGS = -exclude left:0-600 -exclude left:1000-1600 -exclude right:* -exclude top:* -exclude bottom:*

# Tuning for Timing and Buffers
  export TNS_END_PERCENT     = 100
  export REMOVE_ABC_BUFFERS  = 1
  export CTS_BUF_DISTANCE    = 600
  export SKIP_GATE_CLONING   = 1

 # Magic Tool Configuration
   export MAGIC_ZEROIZE_ORIGIN = 0
   export MAGIC_EXT_USE_GDS    = 1
```
 

**Below is a sample view of the file structure after setup:**

```shell
spatha@spatha-VirtualBox:~/OpenROAD-flow-scripts/flow$ ls -ltrh designs/src/vsdbabysoc/
total 3.1M
-rw-rw-r-- 1 spatha spatha 1.1K Jun 29 15:50 avsddac.v
-rw-rw-r-- 1 spatha spatha  947 Jun 29 15:50 avsdpll.v
lrwxrwxrwx 1 spatha spatha   87 Jun 29 15:50 primitives.v -> /home/spatha/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/primitives.v
-rw-rw-r-- 1 spatha spatha 1.7K Jun 29 15:50 clk_gate.v
-rw-rw-r-- 1 spatha spatha  17K Jun 29 15:50 rvmyth.v
-rw-rw-r-- 1 spatha spatha  19K Jun 29 15:50 rvmyth_gen.v
-rw-rw-r-- 1 spatha spatha 2.3M Jun 29 15:50 sky130_fd_sc_hd.v
-rwxrwxr-x 1 spatha spatha 1.3K Jun 29 15:50 testbench.v
-rw-rw-r-- 1 spatha spatha  603 Jun 29 15:50 testbench.rvmyth.post-routing.v
-rw-rw-r-- 1 spatha spatha  590 Jun 29 15:50 vsdbabysoc.v
-rw-rw-r-- 1 spatha spatha 743K Jun 29 15:50 vsdbabysoc.synth.v
```

```shell
spatha@spatha-VirtualBox:~/OpenROAD-flow-scripts/flow$ ls -ltrh designs/sky130hd/vsdbabysoc/
total 32K
drwxrwxr-x 2 spatha spatha 4.0K Jun 29 15:52 gds
drwxrwxr-x 2 spatha spatha 4.0K Jun 29 15:52 include
drwxrwxr-x 2 spatha spatha 4.0K Jun 29 15:53 lef
-rw-rw-r-- 1 spatha spatha   73 Jun 29 15:54 vsdbabysoc_synthesis.sdc
lrwxrwxrwx 1 spatha spatha   65 Jun 29 15:55 macro.cfg -> /home/spatha/VLSI/VSDBabySoC/src/layout_conf/vsdbabysoc/macro.cfg
lrwxrwxrwx 1 spatha spatha   69 Jun 29 15:55 pin_order.cfg -> /home/spatha/VLSI/VSDBabySoC/src/layout_conf/vsdbabysoc/pin_order.cfg
-rw-rw-r-- 1 spatha spatha 2.1K Jun 29 15:59 config.mk
drwxrwxr-x 2 spatha spatha 4.0K Jun 29 16:06 lib
```


