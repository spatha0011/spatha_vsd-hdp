# VSD Hardware Design Program

## Day 6 : VSDBabySoC Post-Synthesis Simulation

First step in the design flow is to synthesize the generated RTL code and after that we will simulate the result. This way we can find more about our code and its bugs. So in this section we are going to synthesize our code then do a post-synthesis simulation to look for any issues. The post and pre (modeling section) synthesis results should be identical.

```bash
spatha@spatha-VirtualBox:~/VLSI/VSDBabySoC$ cp -r src/include/sp_verilog.vh .

patha@spatha-VirtualBox:~/VLSI/VSDBabySoC$ cp -r src/include/sandpiper.vh .

spatha@spatha-VirtualBox:~/VLSI/VSDBabySoC$ cp -r src/include/sandpiper_gen.vh .

spatha@spatha-VirtualBox:~/VLSI/VSDBabySoC$ yosys
```

 ![Alt Text](Images/1.jpg)

```bash
yosys> read_verilog -I ~/VLSI/VSDBabySoC/src/include/ ~/VLSI/VSDBabySoC/src/module/rvmyth.v
```
 ![Alt Text](Images/2.jpg)

