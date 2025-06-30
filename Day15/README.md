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


