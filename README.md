# VSD-IAT-Sign-off-Timing-Analysis_Basics_to_Advance
![cover](https://user-images.githubusercontent.com/41202126/220864875-42740d45-fc64-4c6e-872b-e29f386d7b75.png)

**Contents:**

<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=6 orderedList=false`} -->

<!-- code_chunk_output -->

- [Day-1 Summary](#day-1-summary)
	
- [Day-1 Labs](#day-1-labs)
  - [OpenSTA Introduction](#ot_Intro)
  - [Inputs to OpenSTA](#ot_inputs)
  - [Constraints creation](#constraints)
  - [OpenSTA Run script](#ot_run)  
- [Day-2_Summary](#day-2-summary)
  
- [Day-2_Labs](#day-2-labs)
  - [Liberty Files and Understanding Lib Parsing](#liberty_files_and_understanding_lib_parsing)
  - [Understanding SPEF](#understanding_spef_file_and_spef_parsing)
  - [Understanding timing reports](#understanding_timing_reports_and_timing_graphs)
- [Day-3_Summary](#day-3-summary)
 
- [Day-3_Labs](#day-3-labs)
  - [Understanding Slack computation](#slack-computation)
  
- [Day-4_Summary](#day-4-summary)
  
- [Day-4_Labs](#day-4-labs)
  - [Understanding clock gating check](#understanding-clock-gating)
  - [Understanding Async pin checks](#understanding-async-check)
  
- [Day-5_Summary](#day-5-summary)

- [Day-5_Labs](#day-5-labs)
  - [Revisit slack computation](#revisit-slack)
  - [Clock properties](#clockproperties)
  - [CRPR](#crpr)
  - [ECO](#eco)
- [Acknowledgements:](#acknowledgements)
- [Author:](#author)

<!-- /code_chunk_output -->

---

# Day-1 Summary

Static Timing Analysis (STA) is a method of verifying timing performance of a design. Its key feature is that it is exhaustive compared to functional simulation and SPICE simulation. It doesn't require testbenches and is not used for asynchronous design. It doesn't rely on imput vectors and is a mathematical technique going through all the paths. It doesn't verify functionality of the design. Its pessimistic and conservative so as to ensure there is a definite guard band for sign-off. Typical inputs for STA are netlist, SDC or constraints file, and logic libraries.

STA breaks the paths at ports and sequential elements.

The timing analysis is performed on each of these paths.

Broadly speaking, there are three components of the path, which are, startpoint, endpoint and combinational logic.

Startpoint is where the data is launched by clock edge, or where the data must be available at a specific time. It is usually input port or register clock pinhe clock pin is taken into account because CLK -> Q delay also comes into picture.

Endpoints are where data is captured by clock edge, or where the data must be available at a specific time. It is usually output port or register data pin.

Combinational logic blocks are elements which have no memory, or internal state. Ex.: AND gate, OR gate.

Between a set of startpoint and endpoint, combinational logic might have multiple paths.

Setup time of a flop is dependent on the technology node. Value is available in logic libraries. This enforces max delay on the data path. Data should be available at the input of sequential device before clock edge that captures the data.

Similarly hold time enforces min delay on the data path. Here, data should be stable at the input of sequential device for sometime after the clock edge that captures the data.

Setup Slack = Data Required Time - Data Arrival Time

Slack is negative when data arrives later than required time.

Timing is met when slack is positive, otherwise it isn't.

Synopsys Design Constraints (SDC) for timing specify parameters affecting operational frequency of the design. Examples: create_clock, create generated_clock, set_clock groups, set_clock_transition, set_timing_derate, etc.

Similarly, there are constraints for area and power, which specify restrictions about the area and power. Examples: set_max_area, set_max_dynamic_power, etc. and constraints for design rules which are requirements of the target technology. Examples: set_max_capacitance, set_max_transition, set_max_fanout, etc. 

There are exceptions to design constraints which relax the requirements set by other commands. Examples: set_false_path, set_multicycle_path, etc.

# Day-1 Labs

## OpenSTA Introduction

OpenSTA is a gate level static timing verifier. It can be used to verify the timing of a design using standard file formats like verilog netlist, liberty library, SDC timing constraints, SPEF parasitics.It is used to verify the timing of the design. Incremental updating of delays, arrivals, and required times are done using queries.
This analysis provides timing checks -from, -through, -to, multiple paths to endpoint, delay calculation and also checks timing setup

## Inputs to OpenSTA

Simple.v is our design file. The file ‘sky130_fd_sc_hd_tt_025C_1v80.lib’ include library cell information and inside this file, we have cell named ‘sky130_fd_sc_hd__nand2_1’ which is used in our design

![simple](https://user-images.githubusercontent.com/41202126/220868412-22d52ac8-41bd-4532-9d7c-e788f9c949b3.PNG)

![lib](https://user-images.githubusercontent.com/41202126/220869279-3057be37-d91f-4369-9d78-3f62ee6c09f3.PNG)

![a](https://user-images.githubusercontent.com/41202126/220869431-74f45cde-d750-4b25-8597-062f7d225434.PNG)


![b](https://user-images.githubusercontent.com/41202126/220869454-756517d3-3b3c-413f-9e3a-09d4a0e3fb13.PNG)



## OpenSTA Run script

![run1](https://user-images.githubusercontent.com/41202126/220872520-d6ebc4d5-b9c5-4679-9626-d3023076b20c.PNG)


The timing checks aren't met since the slack is negative.

![slack1](https://user-images.githubusercontent.com/41202126/220872597-951dc38c-13dd-478c-b2ff-c7b2d358ecd9.PNG)

![slack2](https://user-images.githubusercontent.com/41202126/220872641-3fc31998-2ac8-43c5-952f-c3da2dc3ed51.PNG)



# Day-2 Summary

Apart from setup and hold checks, STA also has other timing checks in place like clock gating checks, async pin checks and data to data checks. We also make a note of the rise and fall slew transitions. We also have to provide load analysis by specifying min and max capacitances on the IO net, and corresponding fanout load on ports and output pins. The skew between launch and capture clock waveforms also needs to be taken into account, the skew is positive if the capture flop clock leads the launch flop clock. The duty cycle of the clock is limited by various parameters apart from the technology node. Latch based designs allow more flexibility in timing and also aids time borrowing. A typical STA text report contains startpoint, endpoint, 'max' signifies setup time check and the nodes in the design mentioned as paths, whose respective delays are taken into account.   

# Day-2 Labs

## Liberty Files and Understanding Lib Parsing
The .lib file is an ASCII representation of the timing and power
parameters associated with any cell in a particular semiconductor
technology The .lib file contains timing models and data to calculate I/O delay paths, Timing check values and Interconnect delays.

Exercises:

• Find all the cells in simple_max.lib.

![cellcount](https://user-images.githubusercontent.com/41202126/220876334-301b9209-a834-459b-9407-f8ccf2ef05f6.PNG)


![cell](https://user-images.githubusercontent.com/41202126/220876051-5af091ed-b8b0-4b84-b444-0fae880e53f6.PNG)

• Find all the pins of the cell NAND2_X1 in simple_max.lib

![OUT](https://user-images.githubusercontent.com/41202126/220877089-85d6c8fa-a24e-4933-8f6b-e509ec054226.PNG)

The input pins are 

![input_pins_nand2](https://user-images.githubusercontent.com/41202126/220877500-512bf06b-b5e2-4533-83e4-98d1556e7637.PNG)


• What difference you see between NAND2_X1 and NAND3_X1

The max capacitance decreases, and the number of input pins increase for NAND3_X1.

• What is the difference between ‘simple_max.lib’ and ‘simple_min.lib

Fabrication process variations could either increase or decrease the delay of a cell. So we need to set early and late value while setting the derate factor. STA tool would consider early or late timing derate based on the path and type of analysis.

## Understanding SPEF file

SPEF can be generated by place-and-route tool or a parasitic extraction tool, and then this SPEF is used by timing analysis tool for checking the timing, in-circuit simulation or to perform crosstalk analysis.

Parasitics can be represented at many different levels.
A Typical SPEF File has 4 main sections

1. Header
2. Name Map
3. Top Level Ports
4. Parasitic description

![spef](https://user-images.githubusercontent.com/41202126/220878000-7677ab1d-505a-41c6-99eb-ddfa94565228.PNG)


## Understanding timing reports

![run_lab2](https://user-images.githubusercontent.com/41202126/220879508-b89b39e0-bda0-480c-bc14-292000036fe9.PNG)

![lab2_rpt1](https://user-images.githubusercontent.com/41202126/220880312-25a4b0f3-04d7-433a-a509-4822ffc29216.PNG)



![lab2_rpt2](https://user-images.githubusercontent.com/41202126/220880849-f632434c-19f2-4f29-bab3-ba6dbe0846b9.PNG)


# Day-3_Summary

When we have multiple clocks, in STA, a possible common base period is choses, and a restrictive setup and hold check is followed. By default, the checks are restrictive in nature. The inclusion of falling edge clock on capture after positive edge on launching flop may lead to a half cycle period delay. Timing arcs consist of cell and net arcs. Combinational arcs go from input pins to the output pin, whereas sequential arcs consist of CLK->D arc (Setup/hold arc), CLK->OUT (Propagation delay) and CLK->RST (Recovery/Removal). We are also introduced to the concept of unateness and non-unateness. Cell delays are in general fuction of input transition and capacitive load. Clock latency can be from the source and towards the network. Unlike ideal clocks, real clocks have jitter which leads to uncertainty of the position of rising/falling edge.

# Day-3 Labs




## Understanding Slack computation

Following the circuit provided to perform the slack compulation calculation


![ckt](https://user-images.githubusercontent.com/41202126/220880695-29e72d3e-0658-47c8-8113-68c46d2d9beb.PNG)

Timing report for the path F1/CK to F2/D , by default report the path with the maximum violation:

![f1-ck](https://user-images.githubusercontent.com/41202126/220882016-99caab8f-e9e4-4e61-afd7-9ba552121238.PNG)

"report_checks -from F1/CK -endpoint_count 100" used to report timin report for maximum 100 paths but as there are maximum of 8 paths in the design. Thus it will report for those 8 paths shown below :



# Day-4_Summary

Crosstalk may lead to delta delays and glitches. Switching activity affected by coupling of aggresor and victim, leads to delta delay which may cause timing failure. Glitching is caused due to sudden charge transfer on a stable net which may cause functional failure. Variation could be inter-die and intra-die. The former is of global and systematic, and latter is of on-chip and random nature. In clock gating transition on gating pin, shouldn't create unnecessary active edge of the clock in the fanout. Async pin checks are needed to avoid unknown state. Recovery and removal time checks for assertion and deassertions need to be checked therefore.

![1](https://user-images.githubusercontent.com/41202126/220882404-e6e85fc1-952b-4ad7-b633-8ada217b2910.PNG)

![2](https://user-images.githubusercontent.com/41202126/220882447-c67dfb60-7065-4aca-b066-909ba3363e48.PNG)


![3](https://user-images.githubusercontent.com/41202126/220882459-95ac796b-e3be-43ba-8976-a5827a67b01f.PNG)


![4](https://user-images.githubusercontent.com/41202126/220882476-f104dfb1-c2bf-40e5-891d-e2f4a5e730e9.PNG)

![5](https://user-images.githubusercontent.com/41202126/220882506-3134ecde-78b3-4191-851f-68d453c99017.PNG)

![6](https://user-images.githubusercontent.com/41202126/220882537-4b02809b-01ce-4030-8cf2-b6fb6d2995cf.PNG)

![7](https://user-images.githubusercontent.com/41202126/220882558-481fcb85-38db-45ac-a803-6e21eb721f42.PNG)

![8](https://user-images.githubusercontent.com/41202126/220882576-b85dba3f-ba42-4faa-94d8-31b9a907ecad.PNG)


# Day-4_Labs
  ## Understanding clock gating check
  
![clk_gating_1](https://user-images.githubusercontent.com/41202126/220884832-a7326b9e-8671-4d27-a701-16b25338652c.PNG)

![clk_gating_2](https://user-images.githubusercontent.com/41202126/220884857-071c4cab-b0cf-4689-9e7b-c14403f8a214.PNG)

![clk_gating_3](https://user-images.githubusercontent.com/41202126/220885030-6f50e5a3-37f9-45ae-93ac-3e979a5a2381.PNG)


  ## Understanding Async pin checks
  
  ![async_netlist](https://user-images.githubusercontent.com/41202126/220886606-06897e9c-40ae-4547-9c77-1c0dd5ec3c96.PNG)

  ![async_report](https://user-images.githubusercontent.com/41202126/220886631-a61ed516-91ea-4e15-bbfb-723cdbd28e87.PNG)



# Day-5_Summary

In Synchronous clocks, events happen at a fixed phase relation whereas in asynchronous clocks that is untrue. Then there are logically exclusive clocks, which are passed through a mux logic, whereas in physically exclusive clocks, the sources are entirely different. set_clock_groups is used for establishing asynchronous and synchronous pairs. We can provide different properties like latency, uncertainty, transition and sense. Path specification is done by providing -from -to and -through flags. We would need to be careful of false paths and multicycle paths too. There is a provision of providing max and min delay for a path too.

# Day-5_Labs

  ## CPPR
  Slack calculation without CPPR :
  
  ![cppr](https://user-images.githubusercontent.com/41202126/220886984-dee0ef2c-f1d3-4460-800a-e4f39864bba9.PNG)

  Slack calculation with CPPR,

  ‘c2’ is node which requires CPPR, change the run file. Command- set sta_crpr_enabled 1
  
  ![cppr1](https://user-images.githubusercontent.com/41202126/220887126-098b5653-84bc-4a55-a369-9baa671c3d06.PNG)


![cppr12](https://user-images.githubusercontent.com/41202126/220887151-3d286968-7920-417d-b0e0-f3b2952bc036.PNG)

  ## ECO
  In the ECO cycle, we perform various analysis one by one for every check which we need to close but not closed till PnR stage. There are specialized signoff tools that help us to analyze the issue and suggest the changes we need to do in order to close the issue.The suggested change is captured in an eco file.
  
![e1](https://user-images.githubusercontent.com/41202126/220888655-c97162db-94cc-44fe-a450-9f64efe08f06.PNG)

![e2](https://user-images.githubusercontent.com/41202126/220888712-453830bb-fe29-4e10-ae76-9756637e3c5a.PNG)

![e3](https://user-images.githubusercontent.com/41202126/220888729-95e1e7d2-7093-4bc8-a151-894c8be9898b.PNG)

![e4](https://user-images.githubusercontent.com/41202126/220888747-f7702561-e799-4a74-891d-886bf6f5f075.PNG)


## Acknowledgements

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.
- [Vikas Sachdeva](https://vlsideepdive.com/), Advisor, Tech and VLSI Coach, Trainer and Innovator at vlsideepdive.

## Author

[Anmol Saxena], M.Tech IITB (2021-23)










 

 
 










