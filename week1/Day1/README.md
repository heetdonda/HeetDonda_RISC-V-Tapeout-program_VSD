# 📘 Day 3 — Combinational and Sequential Logic Optimizations

This document covers **Day 3** topics, highlighting optimization techniques that synthesis tools use to enhance combinational and sequential logic, improving a design’s **Power, Performance, and Area (PPA)**.

---

## 🎯 Introduction to Optimizations

**Logic optimization** refers to the automated process where a synthesis tool modifies a gate-level design to make it smaller, faster, and more power-efficient while preserving its functionality. The main objective is to satisfy design constraints related to **timing (performance)**, **power**, and **chip area**.

---

## ➗ Combinational Logic Optimizations

These optimizations target logic without memory elements, where outputs depend solely on current inputs.

* **Constant Propagation:** Replaces parts of the circuit with known constant values. Example: an AND gate with one input tied to '0' always outputs '0', so it can be replaced by a direct connection to ground.  
* **Boolean Simplification:** Applies Boolean algebra to minimize gate usage. Example: `(A AND B) OR (A AND NOT B)` simplifies to `A`, reducing the number of gates needed.  
* **Common Sub-expression Elimination:** Identical logic used in multiple places is synthesized once and reused, lowering gate count and saving area.


---

## Lab on Combinational optimaization

### Lab on opt_check
1. open yosys
   ```bash
   yosys
   ```
2. synthesis of opt_check
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   read_verilog opt_check.v
   synth -top opt_check
   opt_clean -purge
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
3. output :
<img width="535" height="540" alt="opt_check" src="https://github.com/user-attachments/assets/07975bb9-b735-4e41-af3c-1904c8eefa95" />


### Lab on opt_check2
1. open yosys
   ```bash
   yosys
   ```
2. synthesis of opt_check2
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   read_verilog opt_check2.v
   synth -top opt_check2
   opt_clean -purge
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
3. output :
<img width="536" height="563" alt="opt_check2" src="https://github.com/user-attachments/assets/47135f7b-a90e-41e2-af0c-d9f6f9d061af" />


### Lab on opt_check3
1. open yosys
   ```bash
   yosys
   ```
2. synthesis of opt_check3
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   read_verilog opt_check3.v
   synth -top opt_check3
   opt_clean -purge
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
3. output :
<img width="537" height="536" alt="opt_check3" src="https://github.com/user-attachments/assets/abeea3ce-71f6-45df-bbaf-8a89b1bb5e21" />


### Lab on opt_check4
1. open yosys
   ```bash
   yosys
   ```
2. synthesis of opt_check4
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   read_verilog opt_check4.v
   synth -top opt_check4
   opt_clean -purge
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
3. output :
<img width="537" height="563" alt="opt_check4" src="https://github.com/user-attachments/assets/49d045b5-74cd-4e42-a6e3-bf4c7b5ab41c" />


### Lab on multiple_module_opt
1. open yosys
   ```bash
   yosys
   ```
2. synthesis of multiple_module_opt
   ```bash
   read_verilog multiple_module_opt.v
   synth -top multiple_module_opt
   flatten
   opt_clean -purge
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
3. output :
<img width="538" height="565" alt="multiple_module_opt" src="https://github.com/user-attachments/assets/78905f84-a45f-4835-ba50-c1a25433846a" />


### Lab on multiple_module_opt2
1. open yosys
   ```bash
   yosys
   ```
2. synthesis of multiple_module_opt2
   ```bash
   read_verilog multiple_module_opt2.v
   synth -top multiple_module_opt2
   flatten
   opt_clean -purge
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
3. output :
<img width="538" height="568" alt="multiple_module_opt_2" src="https://github.com/user-attachments/assets/23aaa5d5-ea94-4f0d-98fa-9591c60d1e58" />



## 🔄 Sequential Logic Optimizations

These optimizations target circuits with memory elements (like flip-flops), where outputs depend on both current inputs and previous states.

* **Retiming:** Shifts registers across combinational logic to balance timing paths, reducing the critical path delay and enabling higher clock frequencies without altering functionality.  
* **State Minimization:** In Finite State Machines (FSMs), equivalent states are merged, reducing the number of flip-flops required and saving area.  
* **Clock Gating:** Saves dynamic power by disabling the clock to flip-flops that are not changing state, preventing unnecessary switching.

---
## Lab on sequantial optimaization

### Lab on dff_const1
1. open gtkwave
   ```bash
   iverilog dff_const1.v tb_dff_const1.v
   ./a.out
   gtkwave tb_dff_const1.vcd
   ```
2. output of gtkwave
<img width="1638" height="1044" alt="dff_const1_gtkwave" src="https://github.com/user-attachments/assets/3bc0dcc8-4f9a-49cb-85bf-788c34ea8940" />
   

3. open yosys
   ```bash
   yosys
   ```
4. synthesis of dff_const1
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog dff_const1.v
   synth -top dff_const1
   dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show 
   ```
5. output of synthesis
<img width="1647" height="1045" alt="synthesis_dff_const1" src="https://github.com/user-attachments/assets/f02465b8-ba28-4787-bccb-57863365eff0" />


### Lab on dff_const2
1. open gtkwave
   ```bash
   iverilog dff_const2.v tb_dff_const2.v
   ./a.out
   gtkwave tb_dff_const2.vcd
   ```
2. output of gtkwave
<img width="1645" height="1042" alt="dff_const2_gtkwave" src="https://github.com/user-attachments/assets/018f4387-bb9b-47a9-813e-0f98d2d25b57" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of dff_const2
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog dff_const2.v
   synth -top dff_const2
   dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show 
   ```
5. output of synthesis
<img width="545" height="574" alt="synthesis_dff_const2" src="https://github.com/user-attachments/assets/ce3330a0-90fb-47b7-b79c-6b56e1b436bd" />


### Lab on dff_const3
1. open gtkwave
   ```bash
   iverilog dff_const3.v tb_dff_const3.v
   ./a.out
   gtkwave tb_dff_const3.vcd
   ```
2. output of gtkwave
   <img width="1646" height="1044" alt="dff_const3_gtkwave" src="https://github.com/user-attachments/assets/55f848ff-5b22-4c58-8c0e-f703316cacbc" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of dff_const3
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog dff_const3.v
   synth -top dff_const3
   dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show 
   ```
5. output of synthesis
<img width="1647" height="1046" alt="synthesis_dff_const3" src="https://github.com/user-attachments/assets/ec037b03-c98a-4aaa-bb8c-500ca9d7dca0" />


### Lab on dff_const4
1. open gtkwave
   ```bash
   iverilog dff_const4.v tb_dff_const3.v
   ./a.out
   gtkwave tb_dff_const3.vcd
   ```
2. output of gtkwave
  <img width="1645" height="1042" alt="dff_const4_gtkwave" src="https://github.com/user-attachments/assets/a85ec592-2cf7-4074-a335-8f72d926d808" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of dff_const4
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog dff_const4.v
   synth -top dff_const4
   dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show 
   ```
5. output of synthesis
<img width="537" height="571" alt="synthesis_dff_const4" src="https://github.com/user-attachments/assets/6c2f4ef9-d959-4a9a-879e-a50a4a417eed" />


### Lab on dff_const5
1. open gtkwave
   ```bash
   iverilog dff_const5.v tb_dff_const5.v
   ./a.out
   gtkwave tb_dff_const5.vcd
   ```
2. output of gtkwave
<img width="1646" height="1044" alt="dff_const5_gtkwave" src="https://github.com/user-attachments/assets/62187ca0-5cde-4ea7-8730-17ab706d2e6b" />

3. open yosys
   ```bash
   yosys
   ```
4. synthesis of dff_const5
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog dff_const5.v
   synth -top dff_const5
   dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show 
   ```
5. output of synthesis
<img width="1646" height="1042" alt="synthesis_dff_const5" src="https://github.com/user-attachments/assets/c5bf07fa-e2e4-473d-90ab-a0665f59a291" />



## 🗑️ Sequential Optimizations for Unused Outputs

This technique helps eliminate unnecessary logic in a design.

* **Logic Trimming (Dead Code Elimination):** If a flip-flop or logic block’s output is not used anywhere (no fan-out), it has no effect. The synthesis tool detects and removes these unused registers and any combinational logic that feeds them, reducing both area and power consumption.

---
## Lab on sequantial logic unused case

### lab on couter_opt
1. open yosys
   ```bash
   yosys
   ```
2. synthesis counter_opt
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   read_verilog counter_opt.v
   synth -top counter_opt
   dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
3. output of synthesis
<img width="1647" height="1044" alt="unused_output_counter_opt" src="https://github.com/user-attachments/assets/952bd598-1262-4a73-8fa0-d8887cb7ae98" />


### copy counter_opt file to counter_opt2 file
1. copy and open the file
   ```bash
   cp counter_opt.v counter_opt2.v
   gvim counter_opt2.v
   ```
### after open gvim counter_opt2.v change the line assign q = count[0]; to assign q = (count[2:0] == 3'b100); and save
### run the counter_opt2 file
1. open yosys
   ```bash
   yosys
   ```
2. synthesis counter_opt2
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
   read_verilog counter_opt2.v
   synth -top counter_opt
   dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
3. output of synthesis
<img width="1649" height="1047" alt="unused_output_counter_opt2" src="https://github.com/user-attachments/assets/047f4c0d-5119-4392-b00d-bd7aae9f47bc" />











   
