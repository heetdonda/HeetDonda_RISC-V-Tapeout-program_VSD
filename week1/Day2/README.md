# 📘 Day 2 — Timing, Synthesis Strategies & Flip-Flop Design

This document presents the **Day 2** content, emphasizing the importance of timing libraries, contrasting various synthesis methodologies, and examining best practices for writing efficient flip-flop RTL code.


---

## 🕒 Introduction to Timing Libraries (`.lib`)

### 📝 Theory
A **timing library (`.lib`)** is an essential file in digital design. Provided by the semiconductor foundry, it describes the performance characteristics of all standard cells in a specific Process Design Kit (PDK).

**Main Features of a `.lib` File:**
- **Timing Data:** Includes gate propagation delays, flip-flop setup and hold times, and other timing arcs, usually in lookup tables based on input transition times and output load.
- **Power Information:** Specifies both leakage and dynamic power consumption for each cell under various conditions.
- **Cell Area:** Physical size of each standard cell.
- **PVT Corners:** Covers different **Process, Voltage, and Temperature (PVT)** scenarios (e.g., fast, slow, typical) to ensure reliable operation across all expected conditions.

Synthesis tools (like Yosys) and Static Timing Analysis tools (like OpenSTA) use `.lib` files to convert RTL into an optimized gate-level netlist and validate its timing performance.


---
## Lab on timeing libraries
1. To open netlist
   ```bash
   cd lib/
   gvim sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

## 🏗️ Hierarchical vs. Flat Synthesis

### 📝 Theory
Synthesis converts high-level RTL into a gate-level netlist. The two main approaches are **flat synthesis** and **hierarchical synthesis**.

#### Flat Synthesis
**Flat synthesis** flattens the entire design hierarchy, treating the whole project as a single large module.  
- **Analogy:** Like dumping all the car’s parts—nuts, bolts, panels—into one pile and assembling it from scratch.  
- **Advantages:** Enables optimizations across the full design, often achieving the best timing and area results (**Quality of Results - QoR**).  
- **Disadvantages:** Very slow and memory-intensive, making it unsuitable for large designs like modern SoCs.

#### Hierarchical Synthesis
**Hierarchical synthesis** preserves the RTL hierarchy and synthesizes the design module by module.  
- **Analogy:** Building a car by assembling the engine, chassis, and interior separately, then combining them into the final car.  
- **Advantages:** Faster, uses less memory, and scales well for large teams working on different blocks concurrently.  
- **Disadvantages:** Module-level optimization may slightly reduce overall performance compared to flat synthesis.

For most complex modern designs, **hierarchical synthesis** is the preferred industrial standard.

---
## Lab on Flat and Hierarchical Synthesis

### 🧪 Lab Steps
In this lab, we use a design with multiple modules. We will perform both **flat synthesis** and **hierarchical synthesis** on the same design to compare results.


### hirachical synthesis
1. open yosys
   ```bash
   yosys
   ```
2. synthesis code for hierarchical
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   read_verilog multiple_modules.v
   synth -top multiple_modules 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show multiple_modules
   ```
3. Output of the hierarchical synthesis
<img width="1649" height="1046" alt="hirachical_synthesis" src="https://github.com/user-attachments/assets/eb4b35f8-c685-4c29-b38c-93391b5badfe" />


### flatten synthesis
1. open yosys
   ```bash
   yosys
   ```
2. synthesis code for flatten
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   read_verilog multiple_modules.v
   synth -top multiple_modules 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   flatten
   show 
   ```
3. Output of the hierarchical synthesis
<img width="1645" height="1047" alt="flatten_synthesis" src="https://github.com/user-attachments/assets/43b41ddd-d784-4307-b8cb-056ec00313d7" />


---
## 💡 Flip-Flop Coding Styles and Optimization

### 📝 Theory

