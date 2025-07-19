### Perform detailed routing using TritonRoute and explore the routed layout.

Command to perform routing

```shell
# Check value of 'CURRENT_DEF'
echo $::env(CURRENT_DEF)

# Check value of 'ROUTING_STRATEGY'
echo $::env(ROUTING_STRATEGY)

# Command for detailed route using TritonRoute
run_routing
```

Screenshots of routing run:

![Alt_Text](Images/rout_done.jpg)

Commands to load routed def in magic in another terminal

```shell
# Change directory to path containing routed def
cd ~/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/19-07_00-15/results/routing

# Command to load the routed def in magic tool
magic -T /home/spatha/soc-design-and-planning-nasscom-vsd/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Screenshots of routed def

![Alt_Text](Images/1.jpg)

![Alt_Text](Images/2.jpg)

![Alt_Text](Images/3.jpg)

![Alt_Text](Images/4.jpg)
