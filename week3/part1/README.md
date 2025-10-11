# 📘 Week 3 Assignment – Gate-Level Simulation & Timing Analysis Basics

## 🎯 Goal
This week’s objective is to build a solid understanding of **Gate-Level Simulation (GLS)** after the synthesis process.  
You’ll verify the functional accuracy of the synthesized design and explore the key principles of **Static Timing Analysis (STA)** using **OpenSTA**.

---
##
## 🧩 Step 1 – Run Synthesis for the BabySoC Design

## 🧠 Yosys Synthesis Workflow for VSDBabySoC

In this step, we perform **module-level synthesis** using **Yosys**.  
Since handling a full **gate-level synthesis** of the complete SoC is not practical, we focus on synthesizing individual modules to simplify the process and ensure accurate functional verification.

### --- Part 1: Load Libraries and Design Files ---

In this stage, we begin by **loading the necessary standard cell libraries** and the **design source files** into Yosys.  
These libraries provide the logic cell definitions required for synthesis, while the design files describe the RTL architecture of the BabySoC.

```bash
read_liberty -lib src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -lib src/lib/avsddac.lib
read_liberty -lib src/lib/avsdpll.lib
read_verilog src/module/vsdbabysoc.v
read_verilog -I src/include src/module/rvmyth.v
read_verilog -I src/include src/module/clk_gate.v
read_verilog src/module/avsddac_stub.v
read_verilog src/module/avsdpll_stub.v
```

### --- Part 2: Visualize the MODULE-LEVEL (RTL) Design Before Synthesis ---

At this point, we inspect the **VSDBabySoC RTL design** before performing synthesis.  
This helps us understand the original **register-transfer level (RTL) structure**, verify module connections, and ensure the logical hierarchy is correctly defined before converting it into a gate-level representation.

```bash
show -format png -prefix vsdbabysoc_rtl vsdbabysoc
```

### --- Part 3: Synthesize the Design (Preserving Module Boundaries) ---

In this phase, we perform **synthesis of the BabySoC design** while ensuring that the **module boundaries remain intact**.  
This allows each submodule to retain its original structure, making the design easier to analyze, debug, and verify in later stages such as Gate-Level Simulation (GLS) and Static Timing Analysis (STA).

```bash
synth -top vsdbabysoc
dfflibmap -liberty src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
opt
abc -liberty src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

### --- Part 4: Generate Gate-Level Visuals for Submodules (Before Flattening) ---

In this step, we create **graphical representations (PNGs)** of each submodule at the **gate level**, before performing the flattening process.  
These visuals help in understanding the synthesized logic structure of individual modules and verifying that the synthesis process correctly reflects the intended RTL functionality.

```bash
show -format png -prefix netlist_clk_gate clk_gate
show -format png -prefix netlist_avsdpll_stub avsdpll_stub
show -format png -prefix netlist_avsddac_stub avsddac_stub
```

### --- Part 5: Flatten the Design and Perform Final Cleanup ---

In this phase, we **flatten the synthesized design**, merging all hierarchical modules into a single unified structure.  
This process simplifies the netlist, making it easier for downstream tools (like place-and-route or STA) to process.  
After flattening, we perform a **final cleanup** to remove redundant logic and ensure the design is fully optimized and ready for gate-level analysis.

```bash
flatten
setundef -zero
clean -purge
rename -enumerate
```

### --- Part 5: Save Final (Flattened) Netlist and Generate Statistics ---

After flattening the design, we **write out the final gate-level netlist**.  
Additionally, we collect **synthesis statistics** such as cell count, timing estimates, and resource utilization.  
These statistics provide insights into the design’s complexity and performance, and

```bash
write_verilog -noattr reports/vsdbabysoc_netlist.v
stat -liberty src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
---

<img width="1580" height="559" alt="vsdbabysoc_rtl" src="https://github.com/user-attachments/assets/d58f94e3-b208-40dd-8810-bed940f7e247" />

<img width="978" height="551" alt="netlist_clk_gate" src="https://github.com/user-attachments/assets/2b1e7f0a-48fd-4b6f-9929-decf70872168" />

<img width="1647" height="1044" alt="output_synthesis" src="https://github.com/user-attachments/assets/b98b654e-37d8-4310-a81b-7f1ddcea149d" />

<img width="1647" height="1046" alt="printing_stestices_1" src="https://github.com/user-attachments/assets/8520894d-4375-49c7-b69e-669bcff36d76" />

<img width="1647" height="1044" alt="printing_stestices_2" src="https://github.com/user-attachments/assets/f861e07c-b03b-43b9-92df-95a3c9c529a7" />

<img width="1647" height="1045" alt="printing_stestices_3" src="https://github.com/user-attachments/assets/2ee407a9-b332-4e45-9310-25c95459ff83" />


## 🧩 Part 2 – Post-Synthesis Gate-Level Simulation (GLS)

### 🔍 Overview
**Gate-Level Simulation (GLS)** allows us to validate the **functional correctness** of the synthesized netlist.  
This step ensures that the design’s behavior **remains consistent with the original RTL** and operates correctly when timing and gate-level details are introduced.


### **Step 1: Compile the Testbench**

Use `iverilog` to compile the testbench along with the synthesized netlist.  
This prepares the simulation environment so that the GLS can execute and verify the design’s functionality.  

```bash
iverilog -o output/post_synth_sim/post_synth_sim.out   -DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1   -I src/include -I src/module src/module/testbench.v
```
---
### **Step 2: Move to the Post-Synthesis Simulation Output Directory**

Change your working directory to where the **post-synthesis simulation files** are located.  
This ensures that all simulation outputs, such as waveform files and logs, are generated in a dedicated folder for easy access and analysis.

```bash
cd output/post_synth_sim/
```
---
### **Step 3: Execute the Gate-Level Simulation**

Run the compiled testbench to perform the **Gate-Level Simulation (GLS)**.  
This step verifies that the synthesized netlist behaves as expected under real gate-level timing and logic conditions.  


```bash
vvp post_synth_sim.out
```
### **Step 4: Inspect the Waveforms Using GTKWave**

After running the simulation, open the generated **VCD waveform file** in **GTKWave**.  
This allows you to visually examine signal transitions, verify the timing of operations, and confirm that the synthesized design behaves correctly.  

```bash
gtkwave post_synth_sim.vcd
```
---

<img width="1618" height="1041" alt="post_synth_uut_gtkwave" src="https://github.com/user-attachments/assets/52754364-048f-4245-875a-7923e6c52b31" />

<img width="1641" height="1041" alt="post_synth_dac_gtkwave" src="https://github.com/user-attachments/assets/cd9aa485-a4c4-4b27-b2e5-1870f7ad5d95" />

<img width="1644" height="1039" alt="post_synth_pll_gtkwave" src="https://github.com/user-attachments/assets/8bd4d100-e99c-4306-b80b-50c3ad13432f" />


# Functional vs. Gate-Level Simulation in Digital Design

## Overview

This project highlights the relationship between **Functional Simulation** and **Gate-Level Simulation (GLS)**, showing that both should produce **identical outputs** for the same digital circuit.  
While both methods verify design correctness, they operate at different levels of abstraction:

- **Functional Simulation** checks the **RTL design**, focusing on high-level logic and data flow without considering gate delays.  
- **Gate-Level Simulation (GLS)** validates the **synthesized netlist**, including actual gates, connections, and timing details introduced during synthesis.  

Matching results from both simulations confirm that the RTL has been accurately translated into the gate-level implementation.

---

## Key Concepts in Digital Design Verification

Digital designs are verified at multiple levels. Understanding these levels helps ensure your design works correctly from RTL to gates.

---

### **1. High-Level Functional Simulation**
- Purpose: Verify **logic correctness** before hardware implementation.  
- Focus: Simulates **RTL behavior** without worrying about gate delays.  
- Speed: Very fast, since it avoids low-level details.  
- Role: Serves as the **golden reference** for later verification steps.

---

### **2. Logic Synthesis**
- Purpose: Convert RTL code into a **gate-level netlist** suitable for hardware.  
- What Happens: Tools like **Yosys** or Synopsys Design Compiler map abstract RTL into **real gates and flip-flops**.  
- Result: A netlist ready for simulation, timing analysis, and eventually hardware implementation.  
- Significance: Bridges the gap between **conceptual design** and **physical implementation**.

---

### **3. Gate-Level Simulation (GLS)**
- Purpose: Check the **synthesized design** for functional correctness.  
- Focus: Simulates the **actual gates** and optionally considers **timing delays**.  
- Role: Confirms that synthesis did **not alter the intended behavior** of the design.  
- Benefits: Detects issues like incorrect logic mapping or timing violations early, before hardware fabrication.


---

## Why Functional and Gate-Level Outputs Should Align

Both **Functional Simulation** and **Gate-Level Simulation (GLS)** are checking the same design from different perspectives:

- **Functional Simulation**: Confirms that the **designer’s intent** is correctly captured in the RTL.  
- **Gate-Level Simulation**: Verifies that the **synthesized implementation** faithfully reflects the RTL.

### Expected Outcome
- If synthesis is performed correctly, **both simulations should produce the same results**.  

### Possible Reasons for Mismatches
- Errors introduced during the **synthesis process**.  
- Incorrect **synthesis constraints** or tool configurations.  
- RTL bugs that only appear **after the design is mapped to gates**.

---

