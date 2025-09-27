# 📘 Day 1 — Verilog RTL Design, Simulation & Synthesis

This document covers the **Day 1** content of the RTL Design & Synthesis workshop, featuring Verilog coding, simulation with Icarus Verilog and GTKWave, and synthesis using Yosys with the Sky130 PDKs.


---

## Introduction to Verilog RTL Design and Synthesis

### 📝 Theory
Verilog is a Hardware Description Language (HDL) used to model digital circuits. RTL (Register-Transfer Level) describes how data moves between registers and through combinational logic.

**Highlights:**
- RTL captures both synchronous elements (flip-flops, registers) and combinational logic.
- Simulation is used to verify the design's correctness before creating hardware.
- Synthesis transforms RTL code into a gate-level netlist.
- Common tools: **Icarus Verilog** (for simulation), **GTKWave** (for waveform viewing), **Yosys** (for synthesis), **Sky130 PDK** (library of standard cells).

---

## Introduction to the Open-Source Simulator: Icarus Verilog

### 📝 Theory
**Icarus Verilog (iverilog)** is a free, open-source Verilog simulator.  
It compiles Verilog code into a runnable simulation, and when used with **GTKWave**, allows visualization of signal waveforms.

**Typical Workflow:**
1. Develop the RTL design and corresponding testbench.  
2. Compile the code using `iverilog`.  
3. Execute the compiled simulation.  
4. Open the generated `.vcd` file in GTKWave to inspect waveforms.

This process verifies the RTL functionality prior to synthesis.

---

## Labs Using Icarus Verilog and GTKWave

### 🧪 Lab Steps
 Navigate to the `Riscv_soc` folder.  
 Clone the repository containing the Verilog files.
 Open the `verilog_files` directory to access and simulate the designs using Icarus Verilog and GTKWave.

1. Clone and navigate:
   ```bash
   git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
   cd sky130RTLDesignAndSynthesisWorkshop/verilog_files/
   ```
2. Compile and run:
   ```bash
   iverilog good_mux.v tb_good_mux.v
   ./a.out 
    gtkwave tb_good_mux.vcd
   ```
3. Output:
<img width="1182" height="849" alt="good_mux_gtkwave" src="https://github.com/user-attachments/assets/ab40c468-b0e4-4c72-b511-0dce3cfbdd44" />

---

## Introduction to Yosys and Logic Synthesis

### 📝 Theory

**Yosys** is an open-source tool for logic synthesis.  
It converts RTL designs into gate-level netlists and can target a specific PDK's standard cell library.

**Typical Workflow:**
- Load RTL: `read_verilog your_design.v`  
- Perform logic synthesis: `synth`  
- Map to standard cells: `abc -liberty your_lib.lib`  
- Inspect the design visually (optional): `show`  
- Export the synthesized netlist: `write_verilog output.v`

---
## Lab using yosys

### 🧪 Lab Steps for synthesis
1. open yosys
   ```bash
   yosys
   ```
2. for synthesis run below comment in yosys
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    read_verilog good_mux.v
    synth -top good_mux
    abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    show
   ```
3.Output:
<img width="1088" height="770" alt="good_mux_synthesis" src="https://github.com/user-attachments/assets/a120a495-a1ff-4b53-b356-a9f3ab043831" />


4. For netlist:
   ```bash
   write_verilog -noattr good_mux_netlist.v
   !gvim good_mux_netlist.v
   ```
5. Output of Netlist:
<img width="1093" height="794" alt="good_mux_netlist" src="https://github.com/user-attachments/assets/ba2ecf8e-1c85-482f-93bf-f3b813879fae" />

---

## 🗝️ Key Takeaways from Day 1

- Grasped the fundamentals of **RTL Design** using Verilog.  
- Simulated designs with **Icarus Verilog (iverilog)**.  
- Examined signal waveforms using **GTKWave**.  
- Got introduced to **Yosys** for logic synthesis.  
- Conducted synthesis targeting **Sky130 PDK standard cells**.  
- Saw the transformation of RTL code into a **gate-level netlist**.

   

   





