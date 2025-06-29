# VSD Hardware Design Program

## OpenROAD installation guide

**OpenROAD** is an open-source, fully automated RTL-to-GDSII flow for digital integrated circuit (IC) design. It supports synthesis, floorplanning, placement, clock tree synthesis, routing, and final layout generation. OpenROAD enables rapid design iterations, making it ideal for academic research and industry prototyping.

### `Steps to Install OpenROAD and Run GUI`

### 1. Clone the OpenROAD Repository

```bash
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
cd OpenROAD-flow-scripts
```

### 2. Run the Setup Script

```bash
sudo ./setup.sh
```
[<img src="Images/installation1.jpg" alt="Setup" style="width:100%;">](Images/installation1.jpg)


### 3. Build OpenROAD

```bash
./build_openroad.sh --local
```

![Alt Text](Images/installation2.jpg)


### 4. Verify Installation

```bash
source ./env.sh
yosys -help  
openroad -help
```
![Alt Text](Images/installation3.jpg)

![Alt Text](Images/installation4.jpg)

### 5. Run the OpenROAD Flow

```bash
cd flow
make
```

![Alt Text](Images/installation5.jpg)

### 6. Launch the graphical user interface (GUI) to visualize the final layout

```bash
 make gui_final
```

![Alt Text](Images/installation6.jpg)

✅ Installation Complete! You can now explore the full RTL-to-GDSII flow using OpenROAD.

### ORFS Directory Structure and File formats

OpenROAD-flow-scripts/

```plaintext
├── OpenROAD-flow-scripts             
│   ├── docker           -> It has Docker based installation, run scripts and all saved here
│   ├── docs             -> Documentation for OpenROAD or its flow scripts.  
│   ├── flow             -> Files related to run RTL to GDS flow  
|   ├── jenkins          -> It contains the regression test designed for each build update
│   ├── tools            -> It contains all the required tools to run RTL to GDS flow
│   ├── etc              -> Has the dependency installer script and other things
│   ├── setup_env.sh     -> Its the source file to source all our OpenROAD rules to run the RTL to GDS flow
```
Inside the `flow/` Directory

```plaintext
├── flow           
│   ├── design           -> It has built-in examples from RTL to GDS flow across different technology nodes
│   ├── makefile         -> The automated flow runs through makefile setup
│   ├── platform         -> It has different technology note libraries, lef files, GDS etc 
|   ├── tutorials        
│   ├── util            
│   ├── scripts                 
```

![Alt Text](Images/installation7.jpg)
