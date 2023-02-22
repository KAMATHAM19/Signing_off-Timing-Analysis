# Signing-off Timing Analysis

Sign-off timing analysis is a vital step in the electronic design automation process. This analysis validates a design's timing and ensures that it meets the performance specifications. It involves simulating the design at various levels of abstraction in order to test its timing behaviour and identify and correct any potential timing violations. Typically, the analysis is performed at the end of the design flow, just before the design is sent to fabrication. This ensures that the final product meets the desired performance specifications. The analysis of sign-off timing is crucial in ensuring the reliability and functionality of electronic devices and systems.

# Table of Contents
* [DAY- 1](#day-1)
   * [Static timing analysis (STA)](#static-timing-analysis-(sta)) 
   * [STA inputs](#sta-inputs)
   * [Timing paths](#timing-paths)
   * [Components of Timing paths](#components-of-timing-paths)
   * [Setup Check & Hold Check](#setup-check-&-hold-check)
   * [Slack Calculation](#slack-calculation)
   * [SDC commands](#sdc-commands)
   * [Clocks](#clocks) 
   * [Port delays](#port-delays)
   * [Boundary Constraints](#boundary-constraints)    
   * [Lab - 1](#lab-1)
      * [OpenSTA](#opensta)
      * [lab](#lab)
* [DAY - 2](#day-2)
    * [Lab - 2](#lab-2)
       * [Liberity File](#liberity-file)
       * [Lab](#lab)
       * [SPEF](#spef)
       

<a name="day-1"></a>
# DAY - 1 

<a name="static-timing-analysis-(sta)"></a>
## Static timing analysis (STA) 
STA is a technique for improving a design's timing performance.
* Features - static, exhaustive, functionality, conservative
* STA is only used for Synchronous Design

<a name="sta-inputs"></a>
## STA inputs 
- Netlist
- Constraint file or Synopsys Design Constraint (SDC) 
- logic libraries

<a name="timing-paths"></a>
## Timing paths
To perform design analysis, STA divides the logic design into `ports` and `sequential elements`.

![1](https://user-images.githubusercontent.com/64173714/220641759-6bd2f9aa-296b-49c3-aff5-0727fba6b333.jpg)

<a name="components-of-timing-paths"></a>
## Components of Timing paths
* Startpoint 
     * where the data is launched by the clock edge 
     * where the data must be available at a specific time
     * input ports or register clock pin
* Endpoint 
     * where the data is captured by clock edge
     * where the data must be available at a specific time
     * output ports or register data pin
* Combinational Logic 
     * Elements that have no memory element or internal state
     
  ![2](https://user-images.githubusercontent.com/64173714/220641929-590b5bda-b96c-4b8c-9cd1-fad1c14b532f.jpg)

     
<a name="setup-check-&-hold-check"></a>
## Setup Check & Hold Check
               
![3](https://user-images.githubusercontent.com/64173714/220641999-f7bbfebf-0e52-4ff3-80a2-814068537f40.jpg)

SETUP
* The data should be stable at the input of sequential device for sometime before the clock edge that captures the data.
* This sets the maximum data path delay.

HOLD
* The data should be stable at the input of sequential device for sometime before the clock edge that caputers the data.
* This sets the manimum data path delay.
 
** For setup and hold checks of a flop is dependent on the technology node and value is available in logic libraries.

                                 
![4](https://user-images.githubusercontent.com/64173714/220642088-3939b35c-ef4e-4439-b319-3c541cd9c54c.jpg)

To understand the setup and hold checks, DATA (d) is denoted as D1, D2, D3, D4 in the waveform above.

D1 - Data arrives before the setup time window

D2 - Data stays stable for hold for hold time window

D3 - Data arrives close to clock edge violates the setup constraint

D4 - Data arrives close to clock edge violates the hold time constraint


<a name="slack-calculation"></a>
## Slack Calculation

 ![5](https://user-images.githubusercontent.com/64173714/220644424-66778446-7e38-4c90-bbeb-ec21eda3cc62.JPG)


Arrival Time - Time it takes the signal to travel from startpoint to endpoint.

Required Time - The signal should arrive before the specified time.

Slack - The difference between the required arrival time of a signal and the actual arrival time of the signal at the destination
* slack is +ve - when data arrives earlier then required time
* slack is -ve - when data arrives later then the required time

setup slack 
```
    setup slack= Data Required Time- Data Arrival Time
```
hold slack 
```
    hold slack= Data Arrival Time- Data Required Time
```

<a name="sdc-commands"></a>
## SDC commands

1) Constarints for timing - specify parameters affecting operational frequency of the design
   ```
      create_clock
      create_generated_clock
      set_clock_groups
      set_clock_transition
      set_timing_derate
   ```
2) Constraints for area and power - specify restrictions about the area and power
``` 
   set_max_area
   set_max_dynamic_power
```
3) Constaints for design rules - requirements of the target technology
```
    set_max_capacitance
    set_min_capacitance
    set_max_transition
    set_max_fanout
```
4) constarints for interfaces - assumptions on the design boundary
```
    set_driving_cell
    set_input_delay
    set_output_delay
    set_load
```
5) Contarints for specific modes and configurations - assumptions on the values allowed
```
   set_case_analysis
   set_logic_dc
   set_logic_one
   set_logic_zero
```
6) Exceptions to design constraints - relax the requirements set by the other commands or default sta tool analysis
```
   set_false_path
   set_multicycle_path
   set_max_delay
   set_min_delay
   set_disable_timing
```
<a name="clocks"></a>
## Clocks 
clocks are specified in timing constraints using command
```
create_clock -period 10 -waveform {2 4} {C1 Ck} -name

```
Where 
    
    create_clock - It specifies primary source input ports of the design
    -period 10 - period of the clock (10 units)
    -waveform {2, 4} - period when it rises and falls
    {C1 Ck} - attaching  the clocks in the design
    -name - name of the clock
    
<a name="generated-clocks"></a>
## Generated clocks   
clocks which are created inside the design using commands

```
create_generated_clock -divide_by 2 -source C1 -master_clock CLK1 {GC2 GC1}

## Divide by (edges have odd number of elements - rise at 1, fall at 5, rise at 7)
create_generated_clock -edges{1 5 7} -name DIV3A -source SYSCLK {GC}

## To generate pulses
create_geerated_clock -name pulse -source CLK1 -edges{1 1 3} -edge_shift{0 2 0}{pulsegen}
```
<a name="port-delay"></a>
## Port delays
```
 set_input_delay
 set_output_delay
```
<a name="boundary-constraints"></a>
## Boundary constraints
```
set_port_fanout_number - number of pins in fanout
set_fanout_load - load in multiple of standard cell/buffer
set_load - load in terms of capacitance value
set_drive - specifies resistance value
set-driving_cell - specifies cell driving port
set_input_transition - specifiestransition value
```
<a name="lab-1"></a>
# Lab - 1

<a name="OpenSTA"></a>
##  OpenSTA
* OpenSTA is a gate level static timing verifier. It can be used as a standalone executable to verify the timing of a design using standard file formats.
    * Verilog netlist
    * Liberty library
    * SDC timing constraints
    * SDF delay annotation 
    * SPEF parasitics 
* OpenSTA is designed to be easily bolted on to other tools as a timing engine. Using a network adapter, OpenSTA can access the host netlist data structures without duplicating them.
* Query-based incremental update of delays, arrival, and required times
* Simulator for propagating constants from constraints and netlist tie high/low

<a name="lab"></a>
## Lab

```
git clone https://github.com/vikkisachdeva/openSTA_sta_workshop
cd openSTA_sta_workshop/vlsideepdive_openSTA_labs/lab1
ls
leafpad simple.v
```
<img width="960" alt="verilog" src="https://user-images.githubusercontent.com/64173714/219878971-f637e0f4-ffe4-400c-8826-6eb4010a4336.png">

```
leafpad sky130_fd_sc_hd__tt_025C_1v80.lib
leafpad  simple.sdc
leafpad run.tcl
```
To run sta 

```
sta run.tcl -exit | tee run.log
```
<img width="959" alt="d1 -1" src="https://user-images.githubusercontent.com/64173714/220656077-eae71440-bf07-4d97-83b0-51e1f7c9b7da.png">
<img width="960" alt="d1 -2" src="https://user-images.githubusercontent.com/64173714/220656378-779c79f6-5b93-4041-9b42-611ff2ae26ef.png">
<img width="960" alt="d1 -3" src="https://user-images.githubusercontent.com/64173714/220656751-6c88455f-6563-418c-a2d8-675c8ba7c67d.png">

<a name="day-2"></a>
# DAY - 2 


<a name="lab-2"></a>
# Lab - 2

<a name="liberity-file"></a>
Liberity File
The.lib file is an ASCII representation of the timing and power parameters associated with any cell in a specific semiconductor technology.
* The .lib file contains timing models and data to calculate
* I/O delay paths
* Timing check values
* Interconnect delays

Lab
```
cd lab2
leafpad simple_min.lib
leafpad simple_max.lib
```
<img width="960" alt="d2 -1" src="https://user-images.githubusercontent.com/64173714/220660262-d75b2352-7286-4b72-a58a-7f0e881769ee.png">

To find all of the cells in simple max.lib
```
# total no of cells
grep -c " End cell" simple_max.lib 
# cells
grep -c " End cell" simple_max.lib
```
<img width="960" alt="d2 - 2" src="https://user-images.githubusercontent.com/64173714/220673290-1e7c644a-a351-453f-8197-125f67c0d0b9.png">

The pins of the cell NAND2_X1 in simple_max.lib

<img width="635" alt="final nad" src="https://user-images.githubusercontent.com/64173714/220676727-229895fe-355b-43a1-ad67-20ed19dc0e1e.png">

The difference between NAND2_X1 and NAND3_X1

<img width="635" alt="final nad" src="https://user-images.githubusercontent.com/64173714/220679191-bebcdb61-c727-45e1-b591-eddc46e04faf.png"> 
<img width="629" alt="final nad3" src="https://user-images.githubusercontent.com/64173714/220679288-7aed8e8d-09cf-43fa-8f31-613b3f6e446e.png"> 

<a name="spef"></a>
# SPEF

A SPEF (Standard Parasitic Exchange Format) file describes the design's parasitic information.
• Users would never manually create this file.
• It is generated automatically by the tool.
• It is primarily used to transfer parasitic data from one tool to another.

<img width="960" alt="spef" src="https://user-images.githubusercontent.com/64173714/220685189-9ca39ed9-e30e-43cd-bdc5-c8d41a74be63.png">

add  report_timing –num_paths 5 in tcl file
<img width="922" alt="d2 1" src="https://user-images.githubusercontent.com/64173714/220682933-129b1899-9c57-4869-9e08-7013f1e9b823.png">

run sta 
```
sta run.tcl | tee run.log
```
<img width="960" alt="d2 - 3" src="https://user-images.githubusercontent.com/64173714/220684435-31a45d63-7768-44b9-9730-65d6749ea92e.png">


<a name="day-3"></a>
# DAY - 3

<a name="lab-3"></a>
# Lab - 3

```
cd lab3
ls
sta run.tcl | tee out.txt
```
<img width="923" alt="d3 2" src="https://user-images.githubusercontent.com/64173714/220686704-8ed65d6a-f82b-4f25-bbe4-663affc82235.png">


<img width="539" alt="d3 3" src="https://user-images.githubusercontent.com/64173714/220686834-4cb29e4b-7d2f-49c3-b4b3-f6759fe23280.png">

report_checks –from F1/CK -endpoint_count 100

<a name="day-4"></a>
# DAY - 4

lab 4

<img width="960" alt="d4 1" src="https://user-images.githubusercontent.com/64173714/220689445-a0e80c70-afa5-4500-a239-d09e55db6f1e.png">

<img width="960" alt="d4 2" src="https://user-images.githubusercontent.com/64173714/220689509-47b2f21e-5a4d-43f4-9378-7be582146af2.png">
<img width="960" alt="d4 3" src="https://user-images.githubusercontent.com/64173714/220689554-7fc79a10-0ce0-4d96-b90b-93332d13a24f.png">

<img width="960" alt="d4 4" src="https://user-images.githubusercontent.com/64173714/220689598-7935b3f5-8132-430a-82c1-c778c77566a4.png">


<img width="960" alt="d4 5" src="https://user-images.githubusercontent.com/64173714/220689632-049c06d6-68c1-40ac-aeae-268061eaaafb.png">

<img width="960" alt="d4 6" src="https://user-images.githubusercontent.com/64173714/220689652-1a3532c0-94ea-4a73-a95d-6fcdea9b7b9a.png">
<img width="960" alt="d4 asyn 1" src="https://user-images.githubusercontent.com/64173714/220689694-9bbc883e-07d5-4a30-80ed-f20517bf7231.png">

<img width="958" alt="d4 aync 2" src="https://user-images.githubusercontent.com/64173714/220689768-8d178919-0cfb-467a-9a8e-f18fb7b2c569.png">

<a name="day-5"></a>
# DAY - 5


read_verilog simple.v
set_input_delay 0 -min -rise [get_ports inp1] -clock tau2015_clk

## Refernces
1. 
2. https://people.eecs.berkeley.edu/~alanmi/publications/other/liberty07_03.pdf
3. https://www.vlsisystemdesign.com/spef-format-part-1/
