# BabySoC Post-Synthesis GLS & STA: Week 3 Task 1 ✅

Welcome to the BabySoC Gate-Level Simulation (GLS) and Static Timing Analysis (STA) repository for **Week 3 Task 1** of the VSD Hardware Design course! This README provides an end-to-end guide to synthesize BabySoC, run GLS, pre-synthesis functional simulation, and compare results.

---

## 🚀 Project Overview

BabySoC is a small mixed-signal System-on-Chip featuring a RISC-V processor core (RVMYTH), PLL clock generator, and DAC output. The goal of this task is:

- Synthesize the BabySoC design targeting Skywater130 PDK  
- Run *Gate-Level Simulation (GLS)* to validate post-synthesis functionality  
- Run *Pre-Synthesis Functional Simulation*  
- Compare the output waveforms for equivalence verification  
- Generate Static Timing Analysis report introducing STA concepts  

---

## 🧰 Tools & Environment

- Ubuntu 22.04 / WSL Ubuntu  
- **Yosys** for synthesis  
- **Icarus Verilog (iverilog)** for simulation  
- **GTKWave** for waveform visualization  
- **SandPiper SaaS** for TL-Verilog compilation  
- Skywater130 HD standard cell library  

---

## 📂 Directory Structure

<img width="492" height="207" alt="Screenshot 2025-10-12 191131" src="https://github.com/user-attachments/assets/fbcfdfa2-532b-48dd-95b0-fecd6cd1e636" />


---

## ⚡ Steps to Run (Verified & Working)

### 1. Download `sp_verilog.vh` Include File  

*Option 1: Download from VSDMemSoC repo (public and widely used):*

```bash
wget -O src/include/sp_verilog.vh https://github.com/vsdip/VSDMemSoC/raw/main/src/include/sp_verilog.vh

```
*Option 2: Download from VSDBabySoC repo (if available):*

```bash
wget -O src/include/sp_verilog.vh https://github.com/manili/VSDBabySoC/raw/main/src/include/sp_verilog.vh

```

### 2. Generate Verilog From TL-Verilog Source  

BabySoC RTL uses TL-Verilog for RVMYTH processor. Generate the Verilog file:

```bash
sandpiper-saas -i src/module/rvmyth.tlv -o rvmyth.v --bestsv --noline -p verilog
mv rvmyth.v src/module/
mv rvmyth_gen.v src/module/ 2>/dev/null || true
```

### 3. Run Pre-Synthesis Functional Simulation  

Compile and simulate with the `PRE_SYNTH_SIM` macro to include RTL:

```bash
iverilog -g2012 -DPRE_SYNTH_SIM -o output/pre_synth_sim/pre_synth_sim.out -I./src/module -I./src/include src/module/testbench.v
./output/pre_synth_sim/pre_synth_sim.out
```


### 4. Run Post-Synthesis Gate-Level Simulation  

Synthesize with Yosys (use our existing Makefile or command), then run GLS:

*Step 1: Navigate to the Week 2 Directory*

```bash
rsdsrahul@Rahulkumar:~$ cd ~/babysoc-week2/VSDBabySoC
```
*Step 2: Clone the repo:
```bash
git clone https://github.com/manili/VSDBabySoC.git
```

*Step 3: executes a Yosys synthesis script that performs:​
- Reads the RTL Verilog files (vsdbabysoc.v, rvmyth.v, clk_gate.v)
- Reads the Sky130 standard cell library
- Synthesizes the design targeting the Sky130 process
- Generates the gate-level netlist
```bash
# Run synthesis
make synth
```


📷 Screenshots :

<img width="1916" height="1021" alt="Screenshot 2025-10-12 193531" src="https://github.com/user-attachments/assets/7e0b3a70-ecf0-45a0-b84b-802e1345abe7" />

<img width="1919" height="1018" alt="Screenshot 2025-10-12 193607" src="https://github.com/user-attachments/assets/83e1304d-6f6b-4ab3-8c5d-8448980cde17" />

<img width="1919" height="1022" alt="Screenshot 2025-10-12 193702" src="https://github.com/user-attachments/assets/1d8274d4-c21c-46b6-89ab-8ae40759d0c5" />

<img width="1919" height="1021" alt="Screenshot 2025-10-12 193730" src="https://github.com/user-attachments/assets/3c625712-35d7-4f1a-9e59-fb22526318bd" />

<img width="1918" height="1021" alt="Screenshot 2025-10-12 193752" src="https://github.com/user-attachments/assets/e7d9ee1e-05c8-47a4-be86-e6bd003cd895" />


*Step 4: Download Sky130 Primitives & Verify primitives exist:

```bash
git clone --depth=1 https://github.com/google/skywater-pdk-libs-sky130_fd_sc_hd.git
```

```bash
ls -la src/gls_model/
```

```bash
Expected files:
- `primitives.v`
- `sky130_fd_sc_hd.v`
```
*If your working directory doesn't have these files, copy them:*

```bash
cp ~/babysoc-week2/VSDBabySoC_complete/src/gls_model/* ~/babysoc-week2/VSDBabySoC/src/gls_model/
```
*Step 5: Compile with iverilog and run the simulation using GTK Viewer:*
```bash
iverilog -DFUNCTIONAL -DUNIT_DELAY=#1 \
  -o ./output/post_synth_sim.out \
  ./src/gls_model/primitives.v \
  ./src/gls_model/sky130_fd_sc_hd.v \
  ./output/synth/vsdbabysoc.synth.v \
  ./src/module/avsdpll.v \
  ./src/module/avsddac.v \
  ./src/module/testbench.v

# Run simulation
cd output
./post_synth_sim.out

# View waveforms
gtkwave dump.vcd &

```

---

## 📝 Troubleshooting

- **Missing `sp_verilog.vh`:** Ensure it's downloaded in `src/include/`  
- **SandPiper permissions:** Use `chmod +x` on the sandpiper binary if issues arise  
- **VCD file not found:** Run `ls -lt *.vcd output/pre_synth_sim/*.vcd` to locate  
- **GTKWave display issues:** Confirm DISPLAY env var set properly for WSL  
- **Unknown module errors:** Regenerate `rvmyth.v` after fixing includes

---

## 📊 Comparison of Results

| Signal        | Pre-Synthesis Simulation                                    | Post-Synthesis GLS                                          | Match     |
|---------------|-------------------------------------------------------------|-------------------------------------------------------------|-----------|
| **CLK**       | Continuous 11ns period toggling (from PLL)                  | Continuous same toggling with gate delays                    | ✅ Yes    |
| **reset**     | Asserted initially, deasserted after a few cycles           | Same assertion/deassertion preserved                         | ✅ Yes    |
| **RV_TO_DAC[9:0]** | Incrementing register r17 values cycle-by-cycle           | Identical values transition                                  | ✅ Yes    |
| **OUT (DAC)** | Analog output follows RV_TO_DAC values                         | Same DAC analog output behavior                              | ✅ Yes    |

---

## 🖼️ Add Your Screenshots Below

### 📷 Pre-Synthesis Functional Simulation Waveform

<img width="1916" height="1079" alt="Screenshot 2025-10-12 190607" src="https://github.com/user-attachments/assets/a669d999-96d3-45a2-9cd5-61eb912c9420" />


---

### 📷 Post-Synthesis GLS Waveform

<img width="1919" height="1079" alt="Screenshot 2025-10-12 184021" src="https://github.com/user-attachments/assets/9cce33d4-df15-4cfb-bcd9-fbd97da939cf" />


---

## ✔️ Confirmation Note (Sample)

> The post-synthesis Gate-Level Simulation output matches the pre-synthesis functional simulation output. All key signals (CLK, reset, RV_TO_DAC[9:0], OUT) exhibit identical behavior, confirming that the synthesis preserved functionality. The RVMYTH processor correctly executes with the Sky130 standard cell mapping verified by GLS. The design is functionally correct and meets the objectives of Week 3 Task 1.

---

## 📜 How to Use This Repo

1. Clone repository  
2. Follow steps under **Steps to Run**  
3. Use GTKWave to check waveforms  
4. Update screenshots and confirmation note here in README.md  
5. Submit deliverables to course portal

---



## 🔗 Useful Links

- [Yosys Synthesis](http://yosyshq.readthedocs.io/)  
- [Icarus Verilog](http://iverilog.icarus.com/)  
- [GTKWave Viewer](http://gtkwave.sourceforge.net/)  
- [SandPiper SaaS](https://makerchip.com/)  
- [Skywater130 PDK](https://github.com/google/skywater-pdk)  

---

🚀 **Happy Hardware Designing!**  
