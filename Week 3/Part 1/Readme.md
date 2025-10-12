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

Synthesize with Yosys (use your existing Makefile or command), then run GLS:

```bash
make synth # or your synthesis command
make post_synth_sim # or manually, see instructions
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
