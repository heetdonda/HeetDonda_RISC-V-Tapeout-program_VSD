# 📘 Week 2 Task – BabySoC Fundamentals & Functional Modelling

## Objective
To build a solid understanding of SoC fundamentals and practice functional modelling of the BabySoC using simulation tools such as **Icarus Verilog** and **GTKWave**.

## PART 2: Hands-On Functional Modelling

This lab focuses on practical experimentation with the BabySoC design to understand how its components interact and behave. You will:

1. **Clone the BabySoC Project Repository**  
   Download the source files to your local machine for simulation and testing.

2. **Compile Verilog Modules Using Icarus Verilog**  
   Convert the Verilog HDL code into an executable simulation file.

3. **Simulate the BabySoC**  
   Run the compiled simulation to generate `.vcd` waveform files that capture signal behavior.

4. **Analyze Waveforms in GTKWave**  
   - Observe reset sequences and clock operations.  
   - Trace data flow between the CPU, memory, and peripherals.  
   - Capture screenshots of key signals for documentation.

5. **Document Observations**  
   Record your analysis of the BabySoC behavior, noting the operation of each component and signal interaction.


## 📂 Project Structure

```txt
VSDBabySoC/
├── src/
│   ├── include/      # Header files (*.vh)
│   ├── module/       # Verilog + TLV modules
│   │   ├── vsdbabysoc.v   # Top-level module
│   │   ├── rvmyth.v       # CPU
│   │   ├── avsdpll.v      # PLL
│   │   ├── avsddac.v      # DAC
│   │   └── testbench.v    # Testbench
└── output/           # Simulation outputs
```

---

## 🛠️ Setup

### 📥 Cloning the Project

```bash
cd ~/VLSI
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoC/
```
---

## 🔧 TLV → Verilog Conversion

The **RVMYTH CPU** is originally implemented in **TL-Verilog (.tlv)**.  
Before running simulations, the TLV code must be **translated into standard Verilog**, which is compatible with tools like Icarus Verilog.


```bash
# Install tools
sudo apt update
sudo apt install python3-venv python3-pip

# Create virtual env
python3 -m venv sp_env
source sp_env/bin/activate

# Install SandPiper-SaaS
pip install pyyaml click sandpiper-saas

# Convert TLV → Verilog
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```

✅ After conversion, the file `rvmyth.v` will appear alongside the other Verilog modules in your project directory.  
You can now include it in your simulation and compilation steps with Icarus Verilog.

---

📂 **Add Extra Files for Synthesis**

For the simulation and synthesis to work correctly, include the following two additional Verilog stub files in your project directory:  

- `avsddac_stub.v`  
- `avsdpll_stub.v`  

These files are provided in the **Week 2 Part-2** folder (Testbench and Design Files).  
Make sure they are placed alongside your other Verilog modules before compiling and simulating.

---

## 🧪 Simulation Steps

### pre-synthesis simulation

```bash
mkdir -p output/pre_synth_sim

iverilog -o output/pre_synth_sim/pre_synth_sim.out \
  -DPRE_SYNTH_SIM \
  -I src/include -I src/module \
  src/module/testbench.v

cd output/pre_synth_sim
./pre_synth_sim.out
```
### for see the gtk wave

```bash
gtkwave pre_synth_sim.vcd
```
### output GTKWaves
<img width="1644" height="1041" alt="pre_synthesis_uut_gtkwave" src="https://github.com/user-attachments/assets/a56d24c0-c7c2-4798-b557-e426b05eeda5" />



## Waveform Analysis

### 1. CPU Core and Memory Interface

## Analysis of `L1_CPU_dmem` Module

The `L1_CPU_dmem` module represents the **Level 1 Data Memory interface** for the CPU core.

- **Hierarchy:** `vsdbabysoc_tb` → `u_dut` → `core` → `L1_CPU_dmem`  
- **Timestamp:** 37,900 ns  
- **Observation:** The CPU is performing a **memory read** operation, as indicated by `CPU_dmem_rd_en_a5` being high (`1`).  
- **Key Result:** The top-level output signal `OUT[9:0]` shows the hexadecimal value **`0x134`**, confirming that the CPU correctly places data onto the output bus to interact with a peripheral.

**Conclusion:** This demonstrates successful data transfer from the CPU to memory and onward to a peripheral, validating correct CPU-memory interfacing in BabySoC.

---

<img width="1644" height="1041" alt="pre_synthesis_dac_gtkwave" src="https://github.com/user-attachments/assets/42a72d6b-ad55-4a55-b8aa-d48d28ad9954" />


## Waveform Analysis

### 2. Digital-to-Analog Converter (DAC) Verification

The `dac` module is observed to ensure it correctly receives data from the CPU.

- **Hierarchy:** `vsdbabysoc_tb` → `u_dut` → `dac`  
- **Timestamp:** 22,470 ns  
- **Observation:** The DAC input `D[9:0]` is connected to the CPU's `OUT[9:0]` bus and shows the value **`0x134`**.  
- **Result:** The DAC’s analog-style output (`OUT`) responds correctly to the digital input.  

**Conclusion:** This confirms that the CPU-to-DAC data path works as intended, with the DAC receiving the proper digital value and producing the corresponding analog output.

---

<img width="1645" height="1043" alt="pre_synthesis_pll_gtkwave" src="https://github.com/user-attachments/assets/63b9af54-922e-46e3-9d45-c41c460728f3" />


## Waveform Analysis

### 3. Phase-Locked Loop (PLL) Verification

The `pll` module is observed to ensure proper system clock generation.

- **Hierarchy:** `vsdbabysoc_tb` → `u_dut` → `pll`  
- **Timestamp:** 18,420 ns  
- **Observation:** The PLL waveforms indicate correct operation:  
  - `REF` serves as the reference clock.  
  - `CLK` output is a stable oscillating signal derived from `REF`.  
  - `VCO_IN` (Voltage-Controlled Oscillator input) shows the control voltage adjusting the output frequency.  

**Conclusion:** The PLL generates a stable system clock, confirming that the SoC’s timing unit functions reliably and provides synchronized signals for all modules.

---

## 🧠 Yosys Synthesis Flow for VSDBabySoC

In this step, we perform **module-level synthesis** using Yosys.  
Gate-level synthesis is not feasible due to the complexity and educational focus of BabySoC, so we synthesize at the module level to verify that each component can be correctly interpreted and prepared for further implementation.

**Key Points:**  
- Synthesizes individual modules rather than the entire chip at the gate level.  
- Ensures that Verilog RTL code is valid and can be translated into a netlist.  
- Provides insight into resource usage and module connectivity without full physical synthesis.

### --- Part 1: Read Libraries and Design ---

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

### --- Part 2: Show the MODULE-LEVEL (RTL) design BEFORE synthesis for vsdbabysoc ---

```bash
show -format png -prefix vsdbabysoc_rtl vsdbabysoc
```

###  --- Part 3: Synthesize the Design (while preserving module boundaries) ---

```bash
synth -top vsdbabysoc
dfflibmap -liberty src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
opt
abc -liberty src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
### --- Part 4: Generate Gate-Level PNGs for Sub-modules (BEFORE flattening) ---

```bash
show -format png -prefix netlist_clk_gate clk_gate
show -format png -prefix netlist_avsdpll_stub avsdpll_stub
show -format png -prefix netlist_avsddac_stub avsddac_stub
```

### --- Part 5: Flatten the Design and Final Cleanup ---

```bash
flatten
setundef -zero
clean -purge
rename -enumerate
```
### --- Part 5: Write Final (Flattened) Netlist and Statistics ---

```bash
write_verilog -noattr reports/vsdbabysoc_netlist.v
stat -liberty src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
---

## Output photo

<img width="1580" height="559" alt="vsdbabysoc_rtl" src="https://github.com/user-attachments/assets/d35ee738-3403-4634-acc0-650bc1bc5feb" />

<img width="978" height="551" alt="netlist_clk_gate" src="https://github.com/user-attachments/assets/ff5e7d40-a1b5-489b-b556-c604d86087ac" />

<img width="1647" height="1044" alt="output_synthesis" src="https://github.com/user-attachments/assets/b43540c4-2a6a-44e6-8dbb-240a8dd8ef87" />

<img width="1647" height="1046" alt="printing_stestices_1" src="https://github.com/user-attachments/assets/02f3184f-f029-493f-8cc6-3220bfde8710" />

<img width="1647" height="1044" alt="printing_stestices_2" src="https://github.com/user-attachments/assets/b39c6c5b-a499-4bb9-85bd-40c202cde4b0" />

<img width="1647" height="1045" alt="printing_stestices_3" src="https://github.com/user-attachments/assets/c3631159-7943-465e-b66e-c9421a76c172" />


## Post-Synthesis Verification (GLS)

After synthesis, it is important to verify that the logic of the design remains unchanged.  
This is done using **Gate-Level Simulation (GLS)**, where the netlist generated by Yosys is simulated with the **same testbench** used for RTL verification.

### Key Points
- Confirms that synthesis did not introduce functional errors.  
- Uses the generated netlist, which represents the design in terms of standard cells.  
- Requires Verilog models of the **Sky130 standard cells** to accurately simulate gate-level behavior.

### Command
The `iverilog` command for GLS is more involved than RTL simulation, as it must include all standard cell models along with the design and testbench files.

```bash
iverilog -DFUNCTIONAL -DUNIT_DELAY=#1 -o simulation/post_synth_sim.out src/gls_model/primitives.v src/gls_model/sky130_fd_sc_hd.v reports/vsdbabysoc_netlist.v src/module/avsdpll.v src/module/avsddac.v src/module/testbench.v
 ```

### **Execution**:

 ```bash
 cd simulation
 ./post_synth_sim.out
 gtkwave dump.vcd
 ```

### Output GTKWave

<img width="1646" height="1036" alt="post_synthesis_uut_gtkwave" src="https://github.com/user-attachments/assets/160e65f4-6549-4a7c-ae04-fe06656d2711" />


<img width="1644" height="1042" alt="post_synthesis_vsdbabysoc_gtkwave" src="https://github.com/user-attachments/assets/d400fa4b-3fe5-49bc-a5f9-273065f600c8" />


## 🔑 Key Takeaways

- **RTL to Netlist Conversion:** Yosys translates the BabySoC RTL into a gate-level netlist using standard cell libraries.  
- **Module Visualization:** Pre-flatten views allow inspection of each module’s internal connections and structure.  
- **Hierarchy Flattening:** Merges all modules into a single design hierarchy, preparing the design for physical implementation.  
- **Optimization & Mapping:** Ensures efficient resource usage, timing, and overall design performance.  
- **Smooth Integration:** This flow connects RTL design to final SoC integration, bridging simulation and synthesis effectively.

