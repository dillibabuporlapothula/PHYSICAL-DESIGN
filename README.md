
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



</details>

<details>
<summary>Day 2 </summary>
 

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


