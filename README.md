
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

</details>

<details>
<summary>Day 4 </summary>
 

</details>

<details>
<summary>Day 5 </summary>
 

</details>


