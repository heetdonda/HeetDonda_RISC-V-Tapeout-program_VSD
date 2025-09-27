# 📘 Day 4 — GLS, Blocking vs. Non-blocking, and Synthesis-Simulation Mismatch


### Steps for Synthesizing and Running GLS on `bad_mux`
1. open gtk wave
   ```bash
   iverilog bad_mux.v tb_bad_mux.v
   ./a.out
   gtkwave tb_bad_mux.vcd
   ```
2. output of gtk wave
<img width="1646" height="1040" alt="bad_mux_gtkwave" src="https://github.com/user-attachments/assets/8e37fbff-3062-4861-a953-7eac9be00483" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of bad_mux
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog bad_mux.v
   synth -top bad_mux
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   write_verilog -noattr bad_mux_net.v
   show
   ```
5. output of synthesis
<img width="535" height="567" alt="bad_mux_synthesis" src="https://github.com/user-attachments/assets/b4dbcecc-9f81-4899-8e54-9254a088e65b" />

6. gls simulation for bad_mux
   ```bash
   iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
   ./a.out
   gtkwave tb_bad_mux.vcd
   ```
7. output of gls
<img width="1647" height="1045" alt="gls_bad_mux_gtkwave" src="https://github.com/user-attachments/assets/6f643796-8783-4bcc-ad69-b4eb96fdcf6f" />


## 🔬 Labs Overview

## 🔬 Labs on GLS and Synthesis vs Simulation

These exercises walk you through performing a **Gate-Level Simulation (GLS)** on a design after synthesis. The main objective is to check that the GLS waveforms align with the original RTL simulation, ensuring the synthesized circuit behaves correctly and meets the intended functionality.


---
### Steps for Synthesizing and Running GLS on `blocking_caveat`
1. open gtk wave
   ```bash
   iverilog blocking_caveat.v tb_blocking_caveat.v
   ./a.out
   gtkwave tb_blocking_caveat.vcd

   ```
2. output of gtk wave
<img width="1645" height="1048" alt="blocking_caveat_gtkwave" src="https://github.com/user-attachments/assets/c58b1fb7-617e-48d8-8945-5fa92a96af9a" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of blocking_caveat
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog blocking_caveat.v
   synth -top blocking_caveat
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   write_verilog -noattr blocking_cavnet_net.v
   show
   ```
5. output of synthesis
<img width="542" height="578" alt="blocking_caveat_synthesis" src="https://github.com/user-attachments/assets/3914bde0-e556-4d22-afde-cc5b286d38e1" />


6. gls simulation for blocking_caveat
   ```bash
   iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_cavnet_net.v tb_blocking_caveat.v
   ./a.out
   gtkwave tb_blocking_caveat.vcd
   ```
7. output of gls
<img width="1646" height="1044" alt="gls_blocking_caveat_gtkwave" src="https://github.com/user-attachments/assets/ccea653c-2907-4a2b-8039-6520494b8fcb" />



















