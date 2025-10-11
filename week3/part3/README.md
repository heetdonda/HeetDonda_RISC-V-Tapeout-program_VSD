# 📘 Week 3 Task 3 – Post-Synthesis GLS & STA Fundamentals

## 🎯 Objective
**Static Timing Analysis (STA)** is a key method for verifying the timing performance of digital circuits.  

While **timing simulation** can also check both **functional correctness and timing**, STA provides a faster, data-independent way to analyze timing across the entire design.  

In general, **timing analysis** refers to examining a design to identify potential timing issues, either using STA or timing simulation.  

The term **static** highlights that STA evaluates the design **without relying on specific input values**, making it a purely structural and data-independent method.

---

## Installation of OpenSTA

**Note:** Installation instructions are adapted from the official OpenSTA repository:
🔗 https://github.com/parallaxsw/OpenSTA

#### Step 1: Clone the Repository

```bash
git clone https://github.com/parallaxsw/OpenSTA.git
cd OpenSTA
```

#### Step 2: Build the Docker Image
```bash
docker build --file Dockerfile.ubuntu22.04 --tag opensta .
```
This step creates a Docker image called `opensta` using the provided **Ubuntu 22.04 Dockerfile**.  
All necessary dependencies for running OpenSTA are automatically installed during the build process.

### Step 3: Run the OpenSTA Docker Container

Once the `opensta` image is built, you can launch a container interactively.  
Use the `-v` option to mount local directories containing your design files, and `-i` to run the container in interactive mode:

```bash
docker run -i -v $HOME:/data opensta
```

<img width="1641" height="1045" alt="installation_opensta" src="https://github.com/user-attachments/assets/cd6cc31f-ec60-42be-a413-b2945a555eb5" />


At this point, OpenSTA is fully installed and operational within the Docker container.  

When you see the `%` prompt, it means the **OpenSTA interactive shell** has started successfully and is ready for you to use.

## Performing Timing Analysis with Inline Commands

### 1️⃣ Using the `nangate45_slow.lib.gz` Library
After entering the OpenSTA shell (indicated by the `%` prompt), you can run a simple static timing analysis with these inline commands:

```bash
read_liberty /OpenSTA/examples/nangate45_slow.lib.gz
read_verilog /OpenSTA/examples/example1.v
link_design top
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}
report_checks
```
_This flow is useful for quick testing and debugging without writing a full TCL script._

<img width="1652" height="1046" alt="nangate45_slow_max_path_report" src="https://github.com/user-attachments/assets/d2d18fbe-6428-499c-b4e3-c02310e66af5" />

<img width="1647" height="1035" alt="nangate45_slow_min_path_report" src="https://github.com/user-attachments/assets/62becb7d-be22-48a7-bbe8-833979db0652" />


**Note:** The `report_checks` command is used here because only the **slow library** (`nangate45_slow.lib.gz`) has been loaded.  

This corresponds to the **setup (max delay) timing corner**, so the analysis automatically concentrates on setup paths.  

🤔 **Why does `report_checks` display only maximum (setup) delays?**

By default, `report_checks` shows the **maximum path delays**, which are used for setup timing verification.


OpenSTA interprets report_checks without arguments as:
```bash
report_checks -path_delay max
```
This reports only max path delays, i.e., setup timing checks.

✅ **Checking Hold Paths as Well**

To analyze both **setup and hold timing** (maximum and minimum path delays), use the following command:

```bash
report_checks -path_delay min_max
```
(Or) if you want to see only hold checks (min path delays):
```bash
report_checks -path_delay min
```
🛠️ **Yosys Synthesis Commands for `example1.v`**

To perform synthesis, navigate to the `VSDbabysoc/opensta/examples` directory and run the following commands:

```bash
yosys
read_liberty -lib nangate45_slow.lib
read_verilog example1.v
synth -top top
show
```
<img width="1643" height="1042" alt="yosys_output" src="https://github.com/user-attachments/assets/ee1f8688-ae11-4f63-93d4-0d3f0db33d01" />


### 2️⃣ Using the `nangate45_fast.lib.gz` Library

After entering the OpenSTA shell (shown by the `%` prompt), you can carry out a simple static timing analysis with these inline commands:

```bash
read_liberty /OpenSTA/examples/nangate45_fast.lib.gz
read_verilog /OpenSTA/examples/example1.v
link_design top
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}
report_checks
report_checks -path_delay max
report_checks -path_delay min_max
report_checks -path_delay min
```
_This flow is useful for quick testing and debugging without writing a full TCL script._

<img width="1650" height="1049" alt="nangate45_fast_max_path_report" src="https://github.com/user-attachments/assets/4e32d8cd-5326-42d5-a05e-b7b038b66ca9" />

<img width="1656" height="997" alt="nangate45_fast_min_path_report" src="https://github.com/user-attachments/assets/4c088730-fe32-435d-a48b-53b87b2e2e98" />



### 3️⃣ Using the `nangate45_typ.lib.gz` Library

Once you are in the OpenSTA shell (indicated by the `%` prompt), you can execute a basic static timing analysis with the following inline commands:

```bash
read_liberty /OpenSTA/examples/nangate45_typ.lib.gz
read_verilog /OpenSTA/examples/example1.v
link_design top
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}
report_checks
report_checks -path_delay max
report_checks -path_delay min_max
report_checks -path_delay min
```
_This flow is useful for quick testing and debugging without writing a full TCL script._

<img width="1654" height="1042" alt="nangate45_typical_max_path_report" src="https://github.com/user-attachments/assets/64df9cad-1999-4ca0-8e52-3cfd7bda0bfb" />

<img width="1647" height="1042" alt="nangate45_typical_min_path_report" src="https://github.com/user-attachments/assets/e29c6b80-2a65-4d4b-95c1-96e1dfea66ad" />


## Timing Analysis Using SPEF

The OpenSTA timing analysis can also incorporate **SPEF-based parasitic data**:  

By including **post-layout RC parasitics**, this approach provides **more accurate delay and slack calculations**, resulting in better timing verification and signoff accuracy.


1️⃣ for ***nangate45_slow.lib.gz*** file
```shell
docker run -i -v $HOME:/data opensta
```
```shell
read_liberty /OpenSTA/examples/nangate45_slow.lib.gz
read_verilog /OpenSTA/examples/example1.v
link_design top
read_spef /OpenSTA/examples/example1.dspef
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}
report_checks
```
<img width="1648" height="1043" alt="SPEF_nangat45_slow_report_check" src="https://github.com/user-attachments/assets/5178dd24-fc60-4c37-b82f-d5b44ef2ce41" />


### Additional Options to Explore

**<ins>Stage-by-Stage Capacitance Report</ins>**

Generates timing path reports with four-digit precision and displays the **net capacitance at each stage**, making it easier to pinpoint nodes with high capacitance that could impact delay.

```bash
report_checks -digits 4 -fields capacitance ## report capacitance per stage
```
<img width="1651" height="1046" alt="SPEF_nangate45_slow_report_capacitance" src="https://github.com/user-attachments/assets/7bb64d40-9ef4-4364-9b1a-69ec09d1312e" />


**<ins>Detailed Timing Report (Capacitance, Slew, Inputs, Fanout)</ins>**

Generates timing reports that include **capacitance, signal slew, input pins, and fanout** at each stage of the path.

```bash
report_checks -digits 4 -fields [list capacitance slew input_pins fanout]       ###Report Timing with Capacitance, Slew, Input Pins, and Fanout
```
<img width="1653" height="1046" alt="SPEF_nangate45_slow_report_slew_fanout" src="https://github.com/user-attachments/assets/8290bf7d-7f7a-45e3-b7f3-9eaee0f0f34e" />


**<ins>Power Analysis: Total and Component Breakdown</ins>**

The `report_power` command performs **static power analysis** using either propagated or annotated pin activity along with Liberty power models.  

It provides detailed reporting of **internal, switching, leakage, and total power**.  

Power consumption is broken down by **combinational logic, sequential elements, macros, and pad cells**, with all values expressed in **watts**.

```bash
report_power ### reort total and component power
```
<img width="841" height="349" alt="SPEF_nangate45_slow_report_power" src="https://github.com/user-attachments/assets/0851f873-e0f5-452a-8d68-798b2cc394f2" />


**<ins>Report Pulse Width Checks</ins>**

The report_pulse_width_checks command reports min pulse width checks for pins in the clock network. 

If pins is not specified all clock network pins are reported.
```bash
report_pulse_width_checks ### report_pulse_width_check
```
<img width="805" height="313" alt="SPEF_nangate45_slow_report_pulse_width_checks" src="https://github.com/user-attachments/assets/75c4c441-9253-4745-b6a7-d1532ebcf224" />


**<ins>Report Units </ins>**

Report the units used for command arguments and reporting.
```bash
report_units  ### report_units
```
<img width="419" height="250" alt="SPEF_nangate45_slow_report_power_pulsewidth_units" src="https://github.com/user-attachments/assets/361bdc9b-725e-410c-8ac5-a96e1077bb51" />


2️⃣ for ***nangate45_fast.lib.gz*** file
```shell
docker run -i -v $HOME:/data opensta
```
```shell
read_liberty /OpenSTA/examples/nangate45_fast.lib.gz
read_verilog /OpenSTA/examples/example1.v
link_design top
read_spef /OpenSTA/examples/example1.dspef
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}
report_checks
```
<img width="1647" height="1046" alt="SPEF_nangate45_fast_report_check" src="https://github.com/user-attachments/assets/aabd31a5-a6ef-46dc-b514-d72636c0fd8e" />


### more options to explore

```bash
report_checks -digits 4 -fields capacitance ## report capacitance per stage
report_checks -digits 4 -fields [list capacitance slew input_pins fanout]       ###Report Timing with Capacitance, Slew, Input Pins, and Fanout
report_power ### reort total and component power
report_pulse_width_checks ### report_pulse_width_check
report_units  ### report_units
```
<img width="1650" height="1047" alt="SPEF_nangate45_fast_report_capacitance" src="https://github.com/user-attachments/assets/4862ab6e-639a-45a3-a25e-00579a682899" />

<img width="1653" height="1044" alt="SPEF_nangate45_fast_report_slew_fanout" src="https://github.com/user-attachments/assets/ecfe089c-674c-4d82-adad-0d6fd7dc7f02" />

<img width="954" height="360" alt="SPEF_nangate45_fast_report_power" src="https://github.com/user-attachments/assets/93eef2d7-0deb-4126-af0e-10627b1d63de" />

<img width="791" height="317" alt="SPEF_nangate45_fast_report_pulse_width_checks" src="https://github.com/user-attachments/assets/a02dc244-5c3a-4373-8b31-c60a2da7f3c4" />

<img width="572" height="224" alt="SPEF_nangate45_fast_report_units" src="https://github.com/user-attachments/assets/5f2c5a27-a614-49fb-98b8-d04431b46780" />


3️⃣ for ***nangate45_fast.lib.gz*** file
```shell
docker run -i -v $HOME:/data opensta
```
```shell
read_liberty /OpenSTA/examples/nangate45_typ.lib.gz
read_verilog /OpenSTA/examples/example1.v
link_design top
read_spef /OpenSTA/examples/example1.dspef
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}
report_checks
```
<img width="1651" height="1033" alt="SPEF_nangat45_typical_report_check" src="https://github.com/user-attachments/assets/81ec3be6-6b59-42cf-93ee-ca0faf0bb03d" />


### more options to explore

```bash
report_checks -digits 4 -fields capacitance ## report capacitance per stage
report_checks -digits 4 -fields [list capacitance slew input_pins fanout]       ###Report Timing with Capacitance, Slew, Input Pins, and Fanout
report_power ### reort total and component power
report_pulse_width_checks ### report_pulse_width_check
report_units  ### report_units
```
<img width="1654" height="1045" alt="SPEF_nangate45_typical_report_capacitance" src="https://github.com/user-attachments/assets/072c1a04-3e9f-4570-b47a-36be77e739cf" />

<img width="1653" height="1039" alt="SPEF_nangate45_typical_report_slew_fanout" src="https://github.com/user-attachments/assets/d2907e6a-ad56-442f-8fd1-f4598687577e" />

<img width="998" height="351" alt="SPEF_nangate45_typical_report_power" src="https://github.com/user-attachments/assets/3c56aac4-42d4-441f-a93f-445450f2df2d" />

<img width="841" height="318" alt="SPEF_nangate45_typical_report_pilse_width_checks" src="https://github.com/user-attachments/assets/972a8c39-0959-401e-b125-c66999b7d07e" />

<img width="469" height="255" alt="SPEF_nangate45_typical_report_units" src="https://github.com/user-attachments/assets/cff664a4-bfce-41a6-b4bb-87d9fc721ce3" />


### Timing Analysis Using a TCL Script

To automate the timing flow, you can write the commands into a .tcl script and execute it from the OpenSTA shell.

**<ins>min_max_delays.tcl</ins>**
```bash
# Load liberty files for max and min analysis
read_liberty -max /data/riscv_soc/VSDBabySoC/OpenSTA/examples/nangate45_slow.lib.gz
read_liberty -min /data/riscv_soc/VSDBabySoC/OpenSTA/examples/nangate45_fast.lib.gz

# Read the gate-level Verilog netlist
read_verilog /data/riscv_soc/VSDBabySoC/OpenSTA/examples/example1.v

# Link the top-level design
link_design top

# Define clocks and input delays
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}

# Generate a full min/max timing report
report_checks -path_delay min_max
```
| **Command**                                         | **Purpose**                  | **Description**                                                                                     |
| --------------------------------------------------- | ---------------------------- | ------------------------------------------------------------------------------------------------- |
| `read_liberty -max nangate45_slow.lib.gz`          | Load slow corner library     | Loads the **slow (max delay) Liberty file** for **setup timing** analysis.                        |
| `read_liberty -min nangate45_fast.lib.gz`          | Load fast corner library     | Loads the **fast (min delay) Liberty file** for **hold timing** analysis.                         |
| `read_verilog example1.v`                           | Import synthesized netlist   | Reads the synthesized **Verilog netlist** for analysis.                                           |
| `link_design top`                                   | Connect design modules       | Links all modules with `top` as the **top-level module**, associating them with Liberty cells.     |
| `create_clock -name clk -period 10 {clk1 clk2 clk3}`| Define clock                 | Creates a **clock named `clk`** with a 10 ns period on pins `clk1`, `clk2`, and `clk3`.          |
| `set_input_delay -clock clk 0 {in1 in2}`           | Specify input timing         | Sets **0 ns input delay** for `in1` and `in2` relative to the `clk` signal.                       |
| `report_checks -path_delay min_max`                | Perform complete STA         | Generates timing reports for both **setup (max)** and **hold (min)** paths and validates them.    |


### Run the Script Using Docker

To run this script non-interactively using Docker:

```shell
docker run -it -v $HOME:/data opensta /data/riscv_soc/VSDBabySoC/OpenSTA/examples/min_max_delays.tcl
```

🤔 **Why Use the Absolute Path?**

Within the Docker container, your host system’s `$HOME` directory is mounted at `/data`.  

_For example, a file at `$HOME/VLSI/VSDBabySoC/OpenSTA/examples/min_max_delays.tcl` on your computer is accessible as `/data/VLSI/VSDBabySoC/OpenSTA/examples/min_max_delays.tcl` inside the container._  

Using the full path guarantees that OpenSTA can **find and run the script correctly** in the container environment.  

This approach also improves **reproducibility** and makes it easier to manage and reuse timing analysis workflows for multiple designs.


### VSDBabySoC basic timing analysis

#### Prepare Required Files

To begin static timing analysis on the VSDBabySoC design, you must organize and prepare the required files in specific directories.

```bash
# Create a directory to store Liberty timing libraries
heet@heet:~/riscv_soc/VSDBabySoC/OpenSTA$ mkdir -p examples/timing_libs/
heet@heet:~/riscv_soc/VSDBabySoC/OpenSTA/examples$ ls timing_libs/
avsddac.lib  avsdpll.lib  sky130_fd_sc_hd__tt_025C_1v80.lib
# Create a directory to store synthesized netlist and constraint files
heet@heet:~/riscv_soc/VSDBabySoC/OpenSTA$ mkdir -p examples/BabySoC
heet@heet:~/riscv_soc/VSDBabySoC/OpenSTA/examples$ ls BabySoC/
gcd_sky130hd.sdc vsdbabysoc_synthesis.sdc  vsdbabysoc.synth.v
```
These files include:

- Standard cell library: sky130_fd_sc_hd__tt_025C_1v80.lib

- IP-specific Liberty libraries: avsdpll.lib, avsddac.lib

- Synthesized gate-level netlist: vsdbabysoc.synth.v

- Timing constraints: vsdbabysoc_synthesis.sdc

Below is the TCL script to run complete min/max timing checks on the SoC:
```bash
# Load liberty files for max and min analysis
read_liberty -max /data/riscv_soc/VSDBabySoC/OpenSTA/examples/nangate45_slow.lib.gz
read_liberty -min /data/riscv_soc/VSDBabySoC/OpenSTA/examples/nangate45_fast.lib.gz

# Read the gate-level Verilog netlist
read_verilog /data/riscv_soc/VSDBabySoC/OpenSTA/examples/example1.v

# Link the top-level design
link_design top

# Define clocks and input delays
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}

# Generate a full min/max timing report
report_checks -path_delay min_max
```
| **Line of Code**                                       | **Purpose**                | **Explanation**                                                                     |
| ------------------------------------------------------ | -------------------------- | ------------------------------------------------------------------------------------|
| `read_liberty -min ...sky130...` & `-max ...sky130...` | Load standard cell library | Loads the **typical PVT corner** for both min and max timing analysis.              |
| `read_liberty -min/-max avsdpll.lib`                   | Load PLL IP Liberty        | Includes Liberty timing views of the **PLL IP** used in the design.                 |
| `read_liberty -min/-max avsddac.lib`                   | Load DAC IP Liberty        | Includes Liberty timing views of the **DAC IP** used in the design.                 |
| `read_verilog vsdbabysoc.synth.v`                      | Load synthesized netlist   | Loads the gate-level Verilog netlist of the **VSDBabySoC** design.                  |
| `link_design vsdbabysoc`                               | Link top-level module      | Links the hierarchy using `vsdbabysoc` as the **top module** for timing analysis.   |
| `read_sdc vsdbabysoc_synthesis.sdc`                    | Load constraints           | Loads SDC file specifying **clock definitions, input/output delays, false path**    |
| `report_checks`                                        | Run timing analysis        | Generates a default **setup timing report**. Add `-path_delay min_max`              |

execute it inside the Docker container:

```shell
docker run -it -v $HOME:/data opensta /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/vsdbabysoc_min_max_delays.tcl
```
⚠️ **Possible Error Alert**

You may encounter the following error when running the script:

```bash
Warning: /data/riscv_soc/VSDBabySoC/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 23, default_fanout_load is 0.0.
Warning: /data/riscv_soc/VSDBabySoC/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 1, library sky130_fd_sc_hd__tt_025C_1v80 already exists.
Warning: /data/riscv_soc/VSDBabySoC/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 23, default_fanout_load is 0.0.
Error: /data/riscv_soc/VSDBabySoC/OpenSTA/examples/timing_libs/avsdpll.lib line 54, syntax error
```

✅ **Fix:**

This error occurs because Liberty syntax does not support // for single-line comments, and more importantly, the { character appearing after // confuses the Liberty parser. Specifically, check around _line 54 of avsdpll.lib_ and correct any syntax issues such as:

```shell
//pin (GND#2) {
//  direction : input;
//  max_transition : 2.5;
//  capacitance : 0.001;
//}
```
✔️ **Replace with:**
```shell
/*
pin (GND#2) {
  direction : input;
  max_transition : 2.5;
  capacitance : 0.001;
}
*/
```
This adjustment enables OpenSTA to **read the Liberty file correctly** without generating syntax errors.  

Once the Liberty file comments are corrected as described, you can rerun the script to carry out a **full timing analysis** for VSDBabySoC:

### VSDBabySoC PVT Corner Timing Analysis (Post-Synthesis)

Static Timing Analysis (STA) is conducted across multiple **PVT (Process, Voltage, Temperature)** corners to verify that the design satisfies timing constraints under varying conditions.

### Key Timing Corners

**Setup-Critical Paths (Worst Max Delay) Corners:**
- `ss_LowTemp_LowVolt`
- `ss_HighTemp_LowVolt`  
_These correspond to the **slowest timing conditions**._

**Hold-Critical Paths (Worst Min Delay) Corners:**
- `ff_LowTemp_HighVolt`
- `ff_HighTemp_HighVolt`  
_These correspond to the **fastest timing conditions**._

The **required timing libraries** for this analysis can be downloaded from:  
🔗 [Skywater PDK - sky130_fd_sc_hd Timing Libraries](https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/tree/master/timing)

Below is an example script to perform **STA across the PVT corners** using the Sky130 Liberty files.

```bash
# List of standard cell liberty files
set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

# Read custom libs first
read_liberty /data/riscv_soc/VSDBabySoC/OpenSTA/examples/timing_libs/avsdpll.lib
read_liberty /data/riscv_soc/VSDBabySoC/OpenSTA/examples/timing_libs/avsddac.lib

# make STA_OUTPUT directory
exec mkdir -p /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT

# Loop over standard cell libraries
for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {

    read_liberty /data/riscv_soc/VSDBabySoC/OpenSTA/examples/timing_libs/$list_of_lib_files($i)
    read_verilog /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/vsdbabysoc.synth.v
    link_design vsdbabysoc
    current_design
    read_sdc /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/vsdbabysoc_synthesis.sdc

    check_setup -verbose

    report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} \
        > /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/min_max_$list_of_lib_files($i).txt

    exec echo "$list_of_lib_files($i)" >> /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_max_slack.txt
    report_worst_slack -max -digits {4} >> /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_max_slack.txt

    exec echo "$list_of_lib_files($i)" >> /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_min_slack.txt
    report_worst_slack -min -digits {4} >> /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_min_slack.txt

    exec echo "$list_of_lib_files($i)" >> /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_tns.txt
    report_tns -digits {4} >> /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_tns.txt

    exec echo "$list_of_lib_files($i)" >> /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_wns.txt
    report_wns -digits {4} >> /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_wns.txt
}
```
execute it inside the Docker container:

```bash
docker run -it -v $HOME:/data opensta /data/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/sta_across_pvt.tcl
```

After executing the above script, you can find the generated timing reports in the STA_OUTPUT directory:

```bash
heet@heet:~/riscv_soc/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT$ ls
min_max_sky130_fd_sc_hd__ff_100C_1v65.lib.txt  min_max_sky130_fd_sc_hd__ss_100C_1v40.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v44.lib.txt  sta_worst_max_slack.txt
min_max_sky130_fd_sc_hd__ff_100C_1v95.lib.txt  min_max_sky130_fd_sc_hd__ss_100C_1v60.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v76.lib.txt  sta_worst_min_slack.txt
min_max_sky130_fd_sc_hd__ff_n40C_1v56.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v28.lib.txt  min_max_sky130_fd_sc_hd__tt_025C_1v80.lib.txt
min_max_sky130_fd_sc_hd__ff_n40C_1v65.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v35.lib.txt  sta_tns.txt
min_max_sky130_fd_sc_hd__ff_n40C_1v76.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v40.lib.txt  sta_wns.txt
```

| **File**                     | **Purpose**                                                                 |
| ---------------------------- | --------------------------------------------------------------------------- |
| `min_max_<lib>.txt`          | Comprehensive timing report showing **setup and hold paths** for each PVT corner |
| `sta_worst_max_slack.txt`    | Reports the **worst setup slack** across all timing corners                 |
| `sta_worst_min_slack.txt`    | Reports the **worst hold slack** across all timing corners                  |
| `sta_tns.txt`                | Shows the **total negative slack (TNS)** accumulated across all corners     |
| `sta_wns.txt`                | Shows the **worst negative slack (WNS)** across all timing corners          |


### 📈Timing Plots Across PVT Corners

![plot_minimum_slack_pvt_corner](output_week3_part3/plot_minimum_slack_pvt_corner.png)
<img width="1191" height="490" alt="plot_tns_pvt_corner" src="https://github.com/user-attachments/assets/602ac2c3-2b08-4119-b946-ed8961819c04" />

<img width="1186" height="486" alt="plot_wns_pvt_corner" src="https://github.com/user-attachments/assets/3b0aec85-86b5-4d55-b910-0ebaa7bb201d" />

<img width="1191" height="493" alt="plot_worst_slack_max_pvt_corner" src="https://github.com/user-attachments/assets/6c77aec7-19aa-499f-9182-c868dcf1a465" />

<img width="1188" height="488" alt="plot_worst_slack_min_pvt_corner" src="https://github.com/user-attachments/assets/cc0c8943-0d38-4cb2-ab15-cbd972325d3c" />









