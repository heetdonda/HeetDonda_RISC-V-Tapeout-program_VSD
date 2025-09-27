# 📘 Day 5 — Advanced Synthesis and Optimization Constructs

This document presents the **Day 5** material, focusing on how various Verilog constructs such as `if`, `case`, and loops are handled by synthesis tools and how they can be leveraged to generate efficient hardware implementations.


---

## ⚖️ `if` and `case` Constructs in Synthesis

`if` and `case` statements both describe conditional logic, but they result in different synthesized hardware.

* **`if-else-if` Chains:** Synthesized as a **priority encoder**, where conditions are checked in order, giving the first condition the highest priority. This can create long logic paths and potentially slower timing.  

* **`case` Statements:** Typically synthesized as a balanced **multiplexer (MUX)**, which can lead to more uniform timing.

### Incomplete Assignments and Latch Inference
A common issue is an "incomplete" `if` or `case` statement where a signal is not assigned in all possible branches.  
* **Effect:** The synthesis tool infers memory to retain the previous value, resulting in an unintended **latch**.  
* **Why Latches Are Problematic:** Latches are transparent (not edge-triggered), prone to glitches, and complicate static timing analysis. To avoid them, ensure every branch assigns a value or include a `default` case.

---

## 🔁 `for loop` vs. `for generate`

Although they appear similar, `for loop` and `for generate` produce fundamentally different hardware.

### `for loop`
A `for loop` is used inside an `always` block to describe sequential behavior. During synthesis, the loop is **unrolled**, creating a large combinational block. It cannot instantiate multiple modules.  

* **Analogy:** A single worker performing repetitive tasks sequentially, resulting in one large outcome.

### `for generate`
A `for generate` is a declarative construct used to create multiple hardware instances. The synthesis tool **elaborates** the loop to produce parallel, duplicated structures such as modules, registers, or logic blocks. Ideal for repetitive hardware like register files or multi-core interconnects.  

* **Analogy:** Multiple workers performing the same task in parallel, producing multiple identical hardware elements.

---

## 🔬 Labs Overview

* **Lab on Incomplete `if`/`case`:** In this exercise, you will create Verilog code with incomplete conditional assignments, perform synthesis, and examine the resulting unintended latches in the netlist. This lab demonstrates how to write conditional logic that avoids latch inference.


## Lab on incomplete if
### steps of incomplete_if
1. open gtkwave
   ```bash
   iverilog incomp_if.v tb_incomp_if.v 
   ./a.out
   gtkwave tb_incomp_if.vcd
   ```
2. output of gtkwave
<img width="1647" height="1042" alt="incomp_if_gtkwave" src="https://github.com/user-attachments/assets/64268c93-70d0-4287-b08d-5185cf18cc27" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of incomplete_if
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog incomp_if.v
   synth -top incomp_if
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
5. output of synthesis
<img width="536" height="570" alt="incomp_if_synthesis" src="https://github.com/user-attachments/assets/27a733b3-4bf1-4119-bb1b-6fe1434d6fd6" />

### steps of incomplete_if2
1. open gtkwave
   ```bash
   iverilog incomp_if2.v tb_incomp_if2.v 
   ./a.out
   gtkwave tb_incomp_if2.vcd
   ```
2. output of gtkwave
<img width="1645" height="1039" alt="incomp_if2_gtkwave" src="https://github.com/user-attachments/assets/c97e646b-237c-47ee-ab96-a83eed5e8963" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of incomplete_if2
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog incomp_if2.v
   synth -top incomp_if2
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
5. output of synthesis
<img width="1648" height="1046" alt="incomp_if2_synthesis" src="https://github.com/user-attachments/assets/d8a9d7d7-8be8-447d-a1dc-0f1cd863d30b" />

* **Lab on Incomplete or Overlapping `case`:** In this exercise, you will examine how overlapping or partially specified `case` statements are synthesized. The resulting hardware often behaves like a priority encoder, similar to `if-else` chains.


## Lab on incomplete case
### Steps of incomp_case

1. open gtk wave
   ```bash
   iverilog incomp_case.v tb_incomp_case.v 
   ./a.out
   gtkwave tb_incomp_case.vcd
   ```
2. output of gtk wave
<img width="1645" height="1043" alt="incomp_case_gtkwave" src="https://github.com/user-attachments/assets/73618f5c-b2ba-429a-b00f-eb0550a7bc83" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of incomp_case
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog incomp_case.v
   synth -top incomp_case
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
5. output of synthesis
<img width="1648" height="1044" alt="incomp_case_synthesis" src="https://github.com/user-attachments/assets/f4b81320-90a8-4a3b-b9e8-6a60f963c90f" />

### Steps of comp_case

1. open gtk wave
   ```bash
   iverilog comp_case.v tb_comp_case.v 
   ./a.out
   gtkwave tb_comp_case.vcd
   ```
2. output of gtk wave
<img width="1643" height="1043" alt="comp_case_gtkwave" src="https://github.com/user-attachments/assets/9fa3f541-b102-4223-b07c-e44aca491e43" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of comp_case
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog comp_case.v
   synth -top comp_case
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
5. output of synthesis
<img width="1643" height="1042" alt="comp_case_synthesis" src="https://github.com/user-attachments/assets/0a7955e0-9fb8-4e3f-ae8b-a2e15227ecc9" />

### Steps of partial_case_assign

1. open gtk wave
   ```bash
   iverilog partial_case_assign.v tb_partial_case_assign.v 
   ./a.out
   gtkwave tb_partial_case_assign.vcd
   ```
2. output of gtk wave


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of partial_case_assign
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog partial_case_assign.v
   synth -top partial_case_assign
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   show
   ```
5. output of synthesis
<img width="1644" height="1042" alt="partial_case_assign_synthesis" src="https://github.com/user-attachments/assets/96ef6983-5888-4ae6-9120-6b7cb1be9321" />

### Steps of bad_case

1. open gtk wave
   ```bash
   iverilog bad_case.v tb_bad_case.v
   ./a.out
   gtkwave tb_bad_case.vcd
   ```
2. output of gtk wave

<img width="1643" height="1044" alt="bad_case_gtkwave" src="https://github.com/user-attachments/assets/6b020b33-6af3-4b86-97e0-fb051d48c5ea" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of bad_case
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog bad_case.v
   synth -top bad_case
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   write_verilog -noattr blocking_cavnet_net.v
   show
   ```
5. output of synthesis
<img width="1646" height="1044" alt="bad_case_synthesis" src="https://github.com/user-attachments/assets/01d9d6a6-adb4-4e6e-9442-75f9ee978bec" />
6. gls for bad_case
   ```bash
   iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_case_net.v tb_bad_case.v
   ./a.out
   gtkwave tb_bad_case.vcd
   ```

7. output of gls
<img width="1647" height="1043" alt="gls_bad_case_gtkwave" src="https://github.com/user-attachments/assets/6ed64145-5482-498c-adb7-98473ba028b7" />


* **Labs on "`for loop`" and "`for generate`":** You will implement a function (like a multi-bit shifter or adder) using both constructs. By comparing the synthesized schematics, you will see the clear structural difference between a single unrolled combinational block (`for loop`) and multiple parallel instances (`for generate`).

## Labs on for and for genrater
### Steps of mux_generate

1. open gtk wave
   ```bash
   iverilog mux_generate.v tb_mux_generate.v
   ./a.out
   gtkwave tb_mux_generate.vcd
   ```
2. output of gtk wave
<img width="1645" height="1043" alt="mux_genrate_gtkwave" src="https://github.com/user-attachments/assets/a053a3ef-f656-4346-8702-ec9230f07072" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of mux_generate
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog mux_generate.v
   synth -top mux_generate
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   write_verilog -noattr mux_generate_net.v
   show
   ```
5. output of synthesis
<img width="1643" height="1040" alt="mux_generate_synthesis" src="https://github.com/user-attachments/assets/5b9d1153-c80c-497a-ac89-c5dafe2982a4" />

6. gls for mux_generate
   ```bash
   iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v mux_generate_net.v tb_mux_generate.v
   ./a.out
   gtkwave tb_mux_generate.vcd
   ```

7. output of gls
<img width="1649" height="1035" alt="gls_mux_generate_gtkwave" src="https://github.com/user-attachments/assets/6965c7cc-b26a-4b8f-a468-6973729550d1" />


### Steps of demux_case

1. open gtk wave
   ```bash
   iverilog demux_case.v tb_demux_case.v
   ./a.out
   gtkwave tb_demux_case.vcd
   ```
2. output of gtk wave
<img width="1644" height="1043" alt="demux_case_gtkwave" src="https://github.com/user-attachments/assets/bbe26d6a-4498-4305-a960-e7ea7a34940e" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of demux_case
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog demux_case.v
   synth -top demux_case
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   write_verilog -noattr demux_case.v
   show
   ```
5. output of synthesis
<img width="1642" height="1038" alt="demux_case_synthesis" src="https://github.com/user-attachments/assets/ccd847bd-a64c-464d-bdd7-1e0a3e2041e3" />


6. gls for demux_case
   ```bash
   iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v demux_case_net.v tb_demux_net.v
   ./a.out
   gtkwave tb_demux_case.vcd
   ```### Steps of demux_generate

1. open gtk wave
   ```bash
   iverilog demux_generate.v tb_demux_generate.v
   ./a.out
   gtkwave tb_demux_generate.vcd
   ```
2. output of gtk wave
<img width="1645" height="1045" alt="demux_generate_gtkwave" src="https://github.com/user-attachments/assets/01b5af51-6a5a-4f73-9b14-afa674799de6" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of demux_generate
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog demux_generate.v
   synth -top demux_generate
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   write_verilog -noattr demux_generate_net.v
   show
   ```
5. output of synthesis
<img width="1646" height="1046" alt="demux_genrate_synthesis" src="https://github.com/user-attachments/assets/327578a6-5de6-42e9-874f-648a49d63a84" />


6. gls for demux_generate
   ```bash
   iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v demux_generate_net.v tb_demux_generate.v
   ./a.out
   gtkwave tb_demux_generate.vcd
   ```

7. output of gls
<img width="1644" height="1043" alt="gls_demux_generate_gtkwave" src="https://github.com/user-attachments/assets/54344ee2-3e06-4f70-9c58-4d05205dc6f3" />

## Steps of ripple_carry_adder

1. open gtk wave
   ```bash
   iverilog fa.v rca.v tb_rca.v
   ./a.out
   gtkwave tb_rca.vcd
   ```
2. output of gtk wave
<img width="1646" height="1044" alt="rca_gtkwave" src="https://github.com/user-attachments/assets/b162a08b-d570-409d-9ec3-124cd563c758" />


3. open yosys
   ```bash
   yosys
   ```
4. synthesis of ripple_carry_adder
   ```bash
   read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   read_verilog fa.v rca.v
   synth -top fa rca
   abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
   write_verilog -noattr rca_net.v
   select top rca
   show
   ```
5. output of synthesis
<img width="1638" height="1036" alt="rca_synthesis" src="https://github.com/user-attachments/assets/c5a304ab-848e-485c-8a43-e0fd5e70f5bd" />


6. gls for ripple_carry_adder
   ```bash
   iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v rca_net.v tb_rca.v
   ./a.out
   gtkwave tb_rca.vcd
   ```

7. output of gls
<img width="1644" height="1042" alt="gls_rca_gtkwave" src="https://github.com/user-attachments/assets/1037677d-6e66-455c-9cb6-15b07b32cbb9" />


# 🗝️ Key Takeaways from Day 5

A summary of the important synthesis concepts covered:

* **`if-else-if` vs. `case`:** `if-else-if` chains generate **priority logic**, which can slow down timing, whereas `case` statements typically synthesize into parallel **multiplexers (MUXes)** for faster operation.

* **Inferred Latches:** Not assigning a value in every branch of an `if` or `case` can lead to unintended **latches** being inferred by the synthesis tool.

* **`for loop`:** Synthesized by **unrolling** into one large combinational logic block.

* **`for generate`:** Used to instantiate multiple, parallel **hardware structures** (modules, registers, or logic) efficiently.


