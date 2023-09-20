
<details>
<summary>Day 0 </summary>
 
## Tools installation 

### 1. steps to install Openlane
I have used openlane_build_script to install openlane and commands are as follows.

```
git clone https://github.com/nickson-jose/openlane_build_script
cd /work/tools/openlane_working_dir/Openlane
make mount
 
```

and to run RTL2GDS flow used below command

``` ./flow.tcl -design spm ```

![VirtualBox_ubuntu-VLSI_17_09_2023_08_21_07](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/f5eb90f2-57c0-41d2-b962-84cf3ed70854)



</details>

<details>
<summary>Day 1 </summary>
 
 A package (commonly called chip) contains various blocks like IP's, SOC and other things as explained below.

 
 ![Screenshot (108)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/e0348bfd-90d1-4e6f-812e-2f11a6c7307a)

 ![Screenshot (110)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/28550ee0-d907-4a3d-a0eb-47d81356de4f)

__PADS__ - Through which we can send signal from inside to the outside chip and also vice-versa.
__Core__ - All the digital logic sits here.
__Macros__- pure digital logic no need of intellectual property to build this.
__Die__ - Size of entire chip.

### RISC V Instructions set architecture(ISA)

- ISA is nothing but language of computer.
- The flow is as mentioned in digram below

 ![Screenshot (111)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/2efed5ea-9e10-4130-b0ac-41ae0b70b0ea)

 - At first the program --> compiled to assembly language of processor (RISC V in our case) --> converted to machine language --> finally executed inside layout.
 -  RISC V Architecture/specification --> Implemented using HDL --> Layout

 #### Software to Hardware flow

Below block diagram explains about software to hardflow flow more deeply involving various intermediate steps.

![Screenshot (112)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/be0f6b01-691e-4c32-985e-0fbec00a6456)

![Screenshot (113)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/cd8af279-772f-4446-a628-125e18993fd5)

![Screenshot (114)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/754b5258-8687-41b3-bbdb-829b869b8fb6)

### SOC design using OpenLane

__PDK__ : process design kit - These are interface between designers and FABs.
- Files used to model fabrication process includes design rules , device information etc.These are sensitive information and kept secret.
-  Skywater 130nm is the opensource PDK that is available.

  
  ![Screenshot (119)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/e68f74c7-eb85-4413-a359-eb4da79090a1)

### RTL to GSD flow

The simplified RTL to GDS flow is as follows

![Screenshot (120)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/4a37aec3-552a-4fad-a838-8524b7ef4b96)

__synthesis:__  In this step RTL code is converted to Gate level netlist
__Floor and power planning:__ Plan silicon area,partition the chip between different blocks, place I/O ports and create robust power distribution network.
__placement:__ The gate level netlist cells are placed on the above planned floor in rows and columns.
__clock tree synthesis:__ Clock distribution network is created to distribute clock to all sequential elements with minimum possible skew.
__routing:__ also know as signal routing . Interconnect all the cells using availble metal layers.
__signoff:__ We can construct final layout and perform verifications like DRC , LVS , STA.

### Intro to OpenLane 

OpenLane is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, CVC, SPEF-Extractor, KLayout and a number of custom scripts for design exploration and optimization. The flow performs all ASIC implementation steps from RTL all the way down to GDSII.
- Strive is opensource SOC available from OpenLane.
- The OpenLane can be used for both Macros and Chips.
- We have two modes in OpenLane i) Autonomous ii) Interactive
- We also have options to synthesis exploration , design exploration.


![Screenshot (131)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/64c0ded2-2c79-42b4-a242-9460135ca312)

![Screenshot (132)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/6bf9d98e-1937-4048-8831-b415a79930c7)

#### Steps to run OpenLane tool

Commands used are

```
cd /OpenLane
make mount
package require openlane 0.9
./flow.tcl -design spm 
```

The above commands will prepare the design for running.

``` run_synthesis``` - this command will run the synthesis and below is the synthesis report

![VirtualBox_ubuntu-VLSI_17_09_2023_23_42_52](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/f0ea6293-f73a-4497-a28a-97343d2f6d34)

From the report we can calculate __flops ratio__ = 64/301 = 0.212 = 21.2 % 
 

</details>

<details>
<summary>Day 2 </summary>

# Chip floor planning considerations

To identify the dimensions of chip like width,height,die,core we need to first find out dimensions of standard cells and filpflops.Then we calculate the total area occupied by netlist on silicon wafer.

                     
  Utilization factor =  Area occupied by netlist /  total area of core
  

- Utilization factor 1 means 100% core is occupied.

                      
  Aspect ratio  = Height / Width
  

- Aspect ratio signifies the shape of the chip.

  ### Preplaced cells

  When we have huge logic gates, we can divide it into sub-modules or IP's which can be reused multiple times.These are placed once in a chip in user-defined locations before automated placement & routing happens. After this remaining cells are placed around them without disturbing these blocks.

  ![Screenshot (139)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/1a24d699-070a-4fd9-bffe-1db7e133cce8)


  In the real world scenario while the circuit is switching i.e, charging or discharging their is voltage drop that we can encounter, which may interfere with output. To overcome this issue we add __decoupling capacitance (Cd)__ across the circuits to reduce this drop get the correct output.

  
   ![Screenshot (144)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/8b8cb656-1a77-4694-b2d3-4b8c25356c84)


  To prevent voltage droop or ground bounce instead of single vdd / vss line we will have multiple such lines called power mesh thus avoiding this conditions.
 
  The pins are placed between die and core area and clock pins are wider since they have to continously drive all the cells. with logical cell placement blockage the automatic placement tool will not place any cells in this area and floor plan is ready for placement and routing step.
  

  ![Screenshot (148)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/457edce0-cd0e-4144-9824-20a336cb934f)


  ### Steps to run floorplan and placement using OpenLane

  __floor plan__:

  use the below command to run floorplan in openalane after completion of sythesis step and setting or modifying the required configs or switches values.

  ``` run_floorplan ```


  ![VirtualBox_ubuntu-VLSI_19_09_2023_15_16_07](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/ef8598f2-a313-458f-9f87-c8e1f104ac97)

  To view the floorplan in magic use below commands
  
  ```
  cd /home/dilli/OpenLane/designs/picorv32a/runs/RUN_2023.09.19_10.43.26/results/floorplan
  magic -T home/dilli/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32.def
  
  ```

  ![VirtualBox_ubuntu-VLSI_19_09_2023_16_21_47](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/e9e70d54-150e-4455-af3d-670dde727a1c)



  __placement__:

  In case of placement we take the physical view of netlist and place them on floorplan with out disturbing the pre-placed cells.we use repeaters if the connections are very long as part of placement optimisation. If the wire connections are overlapping we use different layers.The placement itself is divided into global placement and detailed placement.

  use the below command to run placement(by default openlane will do global placement reducing wire length as objective HPWL) in openalane after completion of floorplan step and setting or modifying the required configs or switches values.

  ``` run_placement ```

  ![VirtualBox_ubuntu-VLSI_19_09_2023_19_13_29](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/1e73fd4a-ab4b-4827-bf43-37118bfe6ddb)

  ![VirtualBox_ubuntu-VLSI_19_09_2023_19_20_20](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/dfaab2e0-906c-4bd4-8170-3b0f003394af)


  we can see from placement image that all the standard cells are placed.

  ### cell design flow

  Below are the three important steps involved in cell design flow

   ![Screenshot (157)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/24fbe401-3561-43f1-ae4f-32bd1ae36727)

  ### Timing Characterization

  Timing threshold definitions

   ![Screenshot (159)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/1da9713a-5ae0-4fc8-9f99-36addbd9ea7d)

  propagation delay definitions

   ![Screenshot (160)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/9427b034-793f-4686-b63d-7bf22fb72d8e)




  

 

</details>

<details>
<summary>Day 3 </summary>

# Design library cell using magic and ngspice characterization

 ### CMOS inverter and ngspice simulations 

  CMOS inverter diagram
 
   ![Screenshot (161)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/282c80a5-6e3b-4766-a4a2-ba6c9be57659)

  CMOS inverter with various nodes specified.

   ![Screenshot (162)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/6dbe77a1-3a2a-4e3b-a509-0fb272a209ab)

  Below diagram shows transfer characteristics diagram of inverter in ngspice. we can clearly see that output is transitioning from one to zero when input is high. And the curve shifts left or right   based on the width and length of the pmos and nmos.

   ![Screenshot (163)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/a6575431-f5a3-43c5-9386-e70847f795c0)


 ### steps to git clone vsdstdcelldesign repo

  clone the repo using below command inside openlane folder.

   ``` git clone https://github.com/nickson-jose/vsdstdcelldesign.git ```
   

  after copying the tech file to vsdstdcelldesign path execute below command to view the magic file of inverter.

    ```  magic -T sky130A.tech sky130_inv.mag &   ```


      
   ![VirtualBox_ubuntu-VLSI_20_09_2023_03_09_11](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/19eb4573-6628-4eee-96fe-bede5fc7a043)


  # Inception of layout and CMOS fabrication

   ## 16 mask CMOS process
   
   The 16-mask CMOS (Complementary Metal-Oxide-Semiconductor) fabrication process involves the following steps


   __Substrate Preparation:__ Start with a silicon wafer substrate, typically p-type. Clean it to remove impurities.

   __N-Well Formation (Mask 1):__ Apply a photomask and perform a diffusion process to create n-wells for NMOS transistors.

   __P-Well Formation (Mask 2):__ Apply another photomask and perform a diffusion process to create p-wells for PMOS transistors.

   __Gate Oxide Growth (Mask 3):__  Grow a thin gate oxide layer for both types of transistors.

   __Gate Formation (Mask 4):__ Deposit and pattern polysilicon to form transistor gates.

   __Source/Drain Implantation (Mask 5 and Mask 6):__ Use masks to implant dopants into the substrate to create source and drain regions for NMOS and PMOS transistors separately.

   __Interconnect Layers (Mask 7 and Mask 8):__ Apply masks to deposit and pattern metal layers for interconnections between transistors.

   __Passivation Layer (Mask 9 to Mask 16):__ Apply masks to deposit and pattern passivation layers for protection and insulation.

   These 8 steps, involving 16 masks in total, create the essential components of a CMOS integrated circuit, enabling the fabrication of both NMOS and PMOS transistors and the necessary         interconnections for the desired functionality.

        
     
   ![Screenshot (168)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/bc0dbe9d-323c-4432-b074-0627f8cca180)

   __To extract the spice netlist from magic use below commands__


   ```
   extract all
   ext2spice cthresh 0 rthresh 0
   ext2spice
   ```

  ![VirtualBox_ubuntu-VLSI_20_09_2023_14_56_51](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/7182bb18-3ab3-41ef-92e8-1da38faf6c29)


  The contents of extracted spice file is shown below:

   ![VirtualBox_ubuntu-VLSI_20_09_2023_15_10_01](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/dbf01fda-bc57-473e-8f25-c74bb813f990)

   To plot the graph using ngspice for the above spice file use below command

    ``` 
    ngspice <spice file name>
    plot y vs time a
    ```

    ![VirtualBox_ubuntu-VLSI_20_09_2023_15_12_03](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/0cb2c8db-cec4-49f5-9659-d17e24e163c4)

   #### Four timing parameters for the characterization of an inverter standard cell

   __Rise Transition Time (t_r):__ The time taken for the output signal to transition from 20% to 80% of its maximum value when the input signal changes.
        t_r = (Time at 80% output - Time at 20% output)
   
   __Fall Transition Time (t_f):__ The time taken for the output signal to transition from 80% to 20% of its maximum value when the input signal changes.
        t_f = (Time at 20% output - Time at 80% output)

   __Cell Rise Delay (t_d_rise):__ The difference in time between when the output signal reaches 50% (midpoint) during a rising input transition and when the input signal starts to fall.
       t_d_rise = (Time at 50% output rise - Time at 50% input fall)

   __Cell Fall Delay (t_d_fall):__ The difference in time between when the output signal reaches 50% (midpoint) during a falling input transition and when the input signal starts to rise.
       t_d_fall = (Time at 50% output fall - Time at 50% input rise)
  
   These timing parameters are crucial for understanding the behavior of an inverter cell in digital circuits and are used for characterizing its performance in terms of signal propagation and timing constraints.




 







</details>

<details>
<summary>Day 4 </summary>

# Pre layout timing modelling and clock tree analysis

## steps to convert grid info to track info

execute below command in magic terimal window and we can see the grids apperaring as per specified values. And also we can confirm that the ports are at intersections of these horizontal and veritcal grid lines. And also width of standard cell should be in odd multiples of x & height of standard cell should be multiples of y.

``` grid 0.46um 0.34um 0.23um 0.17um ```

 ![VirtualBox_ubuntu-VLSI_20_09_2023_17_38_45](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/4d5922ae-3f8a-460c-a587-4357d65f1eaf)

## Creating Port Definitions

 Once the layout is prepared, the subsequent step involves generating a LEF (Library Exchange Format) file for the cell. However, it's essential to establish specific properties and descriptions for the cell's pins to assist the placer and router tool. In the context of LEF files, a cell containing ports is defined as a macro cell, where the ports represent the declared PINs of the macro.

Our goal is to extract a LEF file from a given layout, such as a simple CMOS inverter, adhering to the standard format. The initial step involves defining the ports and correctly assigning class and use attributes to each port. The most straightforward method for defining a port is through the Magic Layout window. The following steps outline this process:

1) In the Magic Layout window, load the .mag file for the design (in this case, the inverter).

2) Navigate to 'Edit' >> 'Text,' which opens a dialogue box.

3) Continue with the same number of lines.

 ![VirtualBox_ubuntu-VLSI_20_09_2023_18_20_19](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/5acbd791-5bb7-4412-82b4-0f5809a9b99a)

 To create a lef file use below command and this will create lef file the contents of which can be seen below

 ``` lef write ```

  ![VirtualBox_ubuntu-VLSI_20_09_2023_18_26_31](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/eae22c31-219a-49b9-b6b9-37af50fd5c06)


## Steps to Incorporate a Custom Cell into ASIC Design

In the previous steps, we've created a custom standard cell for an inverter. Now, to include this custom cell in the ASIC design, follow these steps:

1) Copy the LEF file and the library files, namely sky130_fd_sc_hd_typical.lib, sky130_fd_sc_hd_slow.lib, and sky130_fd_sc_hd_fast.lib, from the 'libs' folder in 'vsdstdcelldesign' to the 'src' folder of 'picorv32a.'

2) Modify the 'config.tcl' file as shown below:

```
   # Design
set ::env(DESIGN_NAME) "picorv32a"
set ::env(VERILOG_FILES) "$::env(DESIGN_DIR)/src/picorv32a.v"
set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_NET) $::env(CLOCK_PORT)
set ::env(GLB_RESIZER_TIMING_OPTIMIZATIONS) {1}
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set filename $::env(DESIGN_DIR)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
    source $filename
}

```

execute below commands

```
prep -design picorv32a -tag RUN_2023.09.09_20.37.18 -overwrite 
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis

```

####  from below we can see that our inverter has been incorporated to openlane flow



 
 ![Screenshot (178)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/7d9db414-7922-481e-9f1e-877f7638d3db)

 




## Delay tables

__Parameter Influence__

In VLSI design, the parameter "Delay" holds significant sway over cell behavior, dictating critical timing considerations. A "delay model table" or "timing table" is crafted when working with cells of varying sizes and threshold voltages. This table acts as a key instrument, capturing the intricate interplay between a cell's delay, input transitions, and output loads.

__Wire Length Dynamics__

Imagine two scenarios: one with a cell (let's call it X1) at the end of a long wire, and another with the same cell positioned at the termination of a shorter wire. In the former, the extended wire introduces resistance and capacitance, leading to substantial delay and slower transitions. Conversely, the latter scenario, with a shorter wire, results in lower delay, showcasing the wire's profound impact on signal transitions. Despite using the same cell, input transitions distinctly alter the delay, highlighting wire length's significance.

__Delay Tables and Algorithmic Precision__

VLSI engineers have established stringent buffer insertion rules to maintain signal integrity. Buffer sizes remain consistent, but individual delays can vary based on the load they drive, leading to the concept of "delay tables." These two-dimensional arrays contain precise values for input slew and load capacitance, linked to various buffer sizes, serving as comprehensive timing models.

 ![Screenshot (169)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/a5c84d49-e222-43a4-8853-fd9a71c56973)


 # STATIC TIMING ANALYSIS

__Timing Constraints__

In the domain of VLSI design, the parameters of "Setup Time" and "Hold Time" wield significant influence. "Setup Time" signifies the duration preceding the active clock edge during which data input must remain stable to ensure accurate latching. Disregarding this requirement can lead to setup violations, risking data integrity. Conversely, "Hold Time" sets the minimum interval after the clock's active edge during which data input must remain unchanged. Violating this leads to hold violations, potentially causing erroneous data capture. Both are measured concerning the clock's active edge and form the foundation of reliable digital circuit operation.


  ![Screenshot (172)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/48fff999-7a07-40e4-8afa-6fab84c4ae79)


__Clock Jitter Causes__

Clock jitter, defined as the deviation of a clock edge from its ideal position, results from various factors such as noise, component variations, temperature fluctuations, phase-locked loops (PLLs), and clock distribution issues. Noise from power supply fluctuations and electromagnetic interference, as well as variations in electronic component characteristics, can introduce jitter. Temperature changes affect component performance, while PLLs may introduce jitter if not configured correctly. Clock signals traveling through traces or cables can also experience delays and reflections, contributing to jitter.

__Clock Tree Synthesis (CTS) Importance__

Clock Tree Synthesis is vital for efficient clock signal distribution in digital ICs, minimizing clock skew, reducing the need for clock repeaters, and meeting timing and power specifications. CTS ensures reasonable clock skew, controlled clock latency, transition times, pulse width, duty cycle adherence, and power limits. Clock skew refers to differences in clock arrival times between registers. Proper synchronization through CTS prevents setup and hold time violations, crucial for data processing in complex ICs with millions or billions of transistors. Crosstalk, an interference issue, can be mitigated with spacing, wire sizing, shielding, buffering, and clock gating techniques.


  ![Screenshot (171)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/fc5169c6-bdd0-45a3-8448-582ec8b3473f)







 

</details>

<details>
<summary>Day 5 </summary>

# Final steps for RTL2GSD flow

### Maze routing and Lee's algorithm

One approach to routing, known as the Maze Routing algorithm, employs techniques like the Lee algorithm. This method uses a grid, similar to the one used in cell customization, for routing purposes. The Lee algorithm initiates with source and target points, utilizing the routing grid to discover the shortest or optimal route between them.

The algorithm labels neighboring grid cells around the source, incrementing from 1 until reaching the target (e.g., from 1 to 7). During this process, various paths emerge, including L-shaped and zigzag routes. The Lee algorithm prioritizes selecting the best path, typically favoring L-shaped routes over zigzags. When no L-shaped paths are available, it may resort to zigzag routes. This approach is particularly useful for global routing tasks.

__Algorithm Considerations__

Despite its utility, the Lee algorithm has limitations, particularly when dealing with a large number of pins. Constructing a maze and numbering cells from source to target can be time-consuming when dealing with millions of pins. Alternative algorithms exist to address similar routing challenges efficiently.


  ![Screenshot (177)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/fcd2a47f-d36b-4ea6-a634-9c1397d1da75)



### Design rule checks

__Spacing, Width, and Alignment Rules:__ These checks confirm adequate spacing between features, minimum width for conductive traces, and proper alignment between different layers to prevent short circuits, defects, and misalignment.

__Enclosure and Notch Rules:__ DRC tools also validate that components are suitably enclosed within specified regions and that features requiring notches or cutouts adhere to the design requirements.

__Via Rules:__ Verification of via size and placement, which facilitate connections between different layers within the IC, is an integral part of DRC checks.

__Antenna Rules:__ DRC tools examine the buildup and discharge of charge during fabrication to ensure they remain within acceptable thresholds. This ensures the manufacturing process remains reliable and within expected limits.
 
 
 ![Screenshot (176)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/63b8f149-2a53-4f1a-9dda-fac0eab1900a)



 # Power Distribution Network generation


 In contrast to the typical ASIC flow, OpenLANE does not include Power Distribution Network (PDN) generation during the initial floorplan run. The PDN generation process occurs after Clock Tree Synthesis (CTS) and post-CTS Static Timing Analysis (STA).

You can determine if the PDN has been generated by inspecting the current def environment variable with the command: echo $::env(CURRENT_DEF).

To initiate the PDN generation process, execute the following commands after preparing the design and assigning a tag:

```
prep -design picorv32a -tag Run 12.07.10.11
gen_pdn
```


## Routing

OpenLANE uses the TritonRoute tool for routing. There are 2 stages of routing:

Global routing: Routing region is divided into rectangle grids which are represented as course 3D routes (Fastroute tool)
Detailed routing: Finer grids and routing guides used to implement physical wiring (TritonRoute tool)


### Features of TritonRoute:

Honouring pre-processed route guides
Assumes that each net satisfies inter guide connectivity
Uses MILP based panel routing scheme
Intra-layer parallel and inter-layer sequential routing framework
Running routing step in TritonRoute as part of openLANE flow:

``` run_routing ```



  ![Screenshot (179)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/954f9fac-e82b-4b38-b0f8-885556ffb499)




 # custom made sky130_vsdinv


 ![Screenshot (178)](https://github.com/dillibabuporlapothula/PHYSICAL-DESIGN/assets/141803312/04a095ea-7768-4bfb-b591-fa84ebaac54a)


 

</details>


# References

- https://github.com/The-OpenROAD-Project/OpenLane 
- https://github.com/nickson-jose/vsdstdcelldesign
- https://github.com/kunalg123


