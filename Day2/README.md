# VSD Hardware Design Program

## Day 2 : Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

## Liberty Format

Liberty format is an industry standard format used to describe library cells of a particular technology. A cell could be a standard cell, IO buffer, complex IP, etc. Library cell description contains a lot of information like timing information, power estimation, and several other attributes like area, functionality, and operating conditions. Speaking more technically, Liberty format is a format to represent timing and power properties of black boxes (which we can't descend into). Liberty is an ASCII format, usually represented in a text file with the extension `.lib`.

The information inside the Lib file can be divided into two main parts, in the first part, it contains some information which is common for all the standard cells. To understand it better have a look in the following snapshot of the Lib file.

![Alt Text](Images/1.png)

The common part of Lib file contains
Library name and technology name
Units (of time, power, voltage, current, resistance and capacitance)
Value of operating condition ( process, voltage and temperature) – Max, Min and Typical 

Based on operating conditions there are three different lib files for Max, Min and Typical corners. 

In the second part of Lib file, it contains cell-specific information for each cell. The part of Lib file which contains cell-specific information is shown below.

![Alt Text](Images/2.png)

