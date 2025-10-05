# Week 2: BabySoC Fundamentals & Functional Modelling

## Objective
To build a solid understanding of SoC fundamentals and practice functional modelling of the VSDBabySoC using simulation tools (Icarus Verilog & GTKWave).

---

## Part 1: Theory - SoC Design Fundamentals

### What is a System-on-Chip (SoC)?

A System-on-Chip (SoC) is an integrated circuit that combines multiple electronic components traditionally found on separate chips into a single chip. An SoC integrates a processor core, memory, input/output ports, and secondary storage on a single substrate, creating a complete electronic system.

### Components of a Typical SoC

1. **CPU (Central Processing Unit)**: The main processor that executes instructions and controls system operations
2. **Memory**: RAM for data storage, cache for fast access, and sometimes ROM for firmware
3. **Peripherals**: I/O interfaces such as UART, SPI, I2C, USB for communication with external devices
4. **Interconnect**: Bus architecture (AHB, AXI, Wishbone) that connects all components and enables data transfer
5. **Analog IP blocks**: PLLs for clock generation, ADCs for analog-to-digital conversion, DACs for digital-to-analog conversion
6. **Clock and Reset Infrastructure**: Distributes timing signals and manages system initialization

### VSDBabySoC Architecture

VSDBabySoC is a simplified RISC-V based SoC designed for learning purposes, containing three main components:

- **RVMYTH**: A RISC-V based microprocessor core implemented as a digital block that executes instructions
- **PLL (Phase-Locked Loop)**: An analog block that generates a stable clock signal from a reference input
- **DAC (Digital-to-Analog Converter)**: An analog block that converts 10-bit digital output from the CPU to analog voltage

This design demonstrates the integration of digital processing (CPU) with analog components (PLL and DAC), representing a complete mixed-signal SoC system.

### Why BabySoC is Effective for Learning

1. **Simplicity**: Contains only essential components, making it easier to understand core SoC concepts
2. **Mixed-signal design**: Demonstrates integration of both digital (RISC-V core) and analog blocks (PLL, DAC)
3. **Open-source**: All IP blocks are accessible for study and modification
4. **Complete flow**: Covers the entire design process from RTL to GDSII physical implementation
5. **Hands-on experience**: Provides practical experience with industry-standard tools and methodologies

### Role of Functional Modelling

Functional modelling is a critical step that occurs before RTL synthesis and physical design stages:

1. **Verification**: Confirms that the design behaves correctly according to specifications before committing to hardware
2. **Early bug detection**: Identifies logic errors and design flaws when fixes are inexpensive and easy to implement
3. **Interface validation**: Ensures that different modules communicate properly and data flows correctly between components
4. **Performance analysis**: Checks that timing requirements are feasible and the design meets performance goals
5. **Design exploration**: Allows testing different architectural choices before physical implementation

By simulating the design functionally, engineers can validate the system's behavior, catch errors early, and ensure proper integration before moving to more time-consuming synthesis and layout stages.

---

## Part 2: Labs - Functional Simulation

### Tools Installation

#### Install Icarus Verilog and GTKWave
```bash
sudo apt update
sudo apt install -y iverilog gtkwave
```

# Verify installations
```bash
iverilog -v
gtkwave --version


# Install SandPiper-SaaS for TL-Verilog Compilation
cd ~
mkdir week2_task2
cd week2_task2

# Create Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install sandpiper-saas
pip install pyyaml click sandpiper-saas

# Verify installation
sandpiper-saas --version
```
# Simulation Workflow
## Step 1: Clone VSDBabySoC Repository
```bash
# Activate virtual environment
source ~/week2_task2/venv/bin/activate

# Create output directory
mkdir -p output/compiled_tlv
```
## Step 2: Compile TL-Verilog to Verilog
```bash
# Activate virtual environment
source ~/week2_task2/venv/bin/activate

# Create output directory
mkdir -p output/compiled_tlv

# Compile TL-Verilog source to Verilog
sandpiper-saas -i src/module/rvmyth.tlv -o rvmyth.v \
    --bestsv --noline -p verilog --outdir output/compiled_tlv
```
## Step 3: Compile and Run Simulation
```bash
# Create pre-synthesis simulation directory
mkdir -p output/pre_synth_sim

# Compile all modules with iverilog
iverilog -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM \
    src/module/testbench.v \
    -I src/include -I src/module -I output/compiled_tlv

# Navigate to simulation directory
cd output/pre_synth_sim

# Run the simulation
./pre_synth_sim.out

# Verify VCD file was generated
ls -l *.vcd
```
## Step 4: View Waveforms in GTKWave
```bash
gtkwave pre_synth_sim.vcd &
```

# Simulation Results and Analysis
## Screenshot 1: Reset Operation


<img width="1919" height="1079" alt="Screenshot 2025-10-05 161049" src="https://github.com/user-attachments/assets/87312787-e850-45ca-bf82-59da526700b2" />




### **Description:**
This waveform demonstrates the reset operation of the VSDBabySoC system. The reset signal (visible at reset=0) controls the initialization of all system modules.
Key Observations:

**The reset signal starts at 0, holding all modules in their initial state**
**During reset, all registers and sequential elements are initialized to known values**
**When reset is deasserted, the system transitions to normal operation**
**The PLL begins generating the stable clock signal**
**The RISC-V core starts fetching and executing instructions from memory**

The reset operation ensures deterministic startup behavior and proper system initialization before normal operation begins.


## Screenshot 2: Clocking and PLL Operation

<img width="1919" height="1079" alt="Screenshot 2025-10-05 162346" src="https://github.com/user-attachments/assets/50d800e2-970a-4860-9c90-c9532cf4a921" />

### **Description:**
This waveform shows the PLL (Phase-Locked Loop) generating a stable system clock from the reference input.
Key Observations:

**CLK**: The system clock output from the PLL, shown as a regular square wave
**REF**: The reference clock input signal to the PLL
**VCO_IN**: Voltage-controlled oscillator input
**ENb_CP, ENb_VCO**: Active-low enable signals for charge pump and VCO (=1 disabled, =0 enabled)
**lastedge**: Internal PLL signal tracking the last edge detection
**period**: The calculated period of the PLL output (approximately 35.41625 time units)
**refpd**: Reference phase detector signal

The waveforms demonstrate that the PLL successfully locks to the reference frequency and maintains a stable output clock throughout the simulation. The period remains consistent, showing proper phase-locking behavior.

### Screenshot 3: Dataflow Between Modules (CPU to DAC)


<img width="1919" height="1079" alt="Screenshot 2025-10-05 162242" src="https://github.com/user-attachments/assets/4d5e7395-e990-4300-85cf-1a0ba1e6d4ca" />

  ### **Description:**

This waveform demonstrates the complete dataflow path from the RISC-V CPU core to the DAC (Digital-to-Analog Converter), showing successful inter-module communication in the VSDBabySoC.

**Key Signals Visible:**

- **D[9:0] = 04E (hexadecimal)**: The 10-bit digital input to the DAC module, representing data from the RISC-V core
- **Dext[10:0] = 04E**: Extended 11-bit representation of the digital data being transferred
- **EN = 1**: DAC enable signal (active high), indicating the DAC is operational
- **NaN = nan**: Not-a-Number indicator for invalid/uninitialized analog values
- **OUT = 0.076**: DAC analog output voltage in volts, converting the digital input (04E hex = 78 decimal) to its corresponding analog level

**Upper Waveforms (Data Bus):**
The top two rows show the digital data values changing over time:
- Values transition: 011 → 000 → 001 → 003 → 006 → 00A → 00F → 015 → 01C → 024 → 02D → 037 → 042 → 04E → 05B → 069 → 078 → 088 → 099 → 0AB → 0BE → 0D2...
- This sequence represents the RISC-V core's computational output being continuously sent to the DAC

**Lower Waveform (Analog Output):**
The green trace shows the DAC's analog output responding to each digital input:
- Voltage levels: 0.0166V → 0.0009V → 0.0029V → 0.0058V → 0.0097V → 0.0146V → 0.0205V → 0.0273V → 0.0351V → 0.0439V → 0.0537V → 0.0645V → 0.0762V → 0.0889V → 0.1026V...
- Each voltage step corresponds to the digital input value, demonstrating correct DAC operation

**Dataflow Analysis:**

This screenshot clearly demonstrates:
1. **Digital-to-Analog Conversion**: The DAC successfully converts discrete digital values (D[9:0]) into continuous analog voltage levels (OUT)
2. **Inter-Module Communication**: Data flows seamlessly from the RISC-V core through the Dext bus to the DAC input
3. **Real-time Operation**: The waveform shows continuous data updates throughout the simulation timeline (0 to 84999 ns)
4. **Correct Functionality**: The analog output voltage increases proportionally with the digital input value, confirming proper DAC operation

**Technical Insight:**

The relationship between digital input and analog output follows the DAC transfer function:
- Digital value 04E (hex) = 78 (decimal)
- With a 10-bit DAC and reference voltage VREFH = 1V (typical)
- Expected output ≈ (78/1024) × 1V ≈ 0.076V ✓ (matches observed value)

This validates that the VSDBabySoC's mixed-signal integration is functioning correctly, with the digital RISC-V core successfully communicating with the analog DAC module.


## Screenshot 4: Dataflow Between Modules (CPU to DAC)

<img width="1919" height="1079" alt="Screenshot 2025-10-05 162917" src="https://github.com/user-attachments/assets/4174f3b5-23be-4d96-983a-dea898ec1214" />




### **Description:**
This waveform captures the critical dataflow from the RISC-V core to the DAC, demonstrating inter-module communication.
Key Observations:

**D[9:0]** / **Dext[10:0]**: 10-bit digital data bus carrying values from the RISC-V core to the DAC

**Values transition**: 011 → 000 → 001 → 003 → 005 → 00A → 00F → 015 → 01C → 024... (hexadecimal)
These values represent computational results from the CPU being sent to the DAC


**OUT**: DAC analog output voltage

Responds to digital input changes with corresponding analog voltage levels
**Values**: 0.0166V, 0.0009V, 0.0277V, 0.0351V, etc.
The DAC successfully converts digital values to proportional analog voltages


**w_CPU_dmem_rd_data_a4[31:0]**: Shows CPU memory read operations, demonstrating active instruction execution

**Dataflow Path**: RISC-V Core → Dext[10:0] bus → DAC Input → Analog OUT
This demonstrates the complete integration of digital and analog domains in the VSDBabySoC, with the CPU generating computational results that are successfully converted to analog signals by the DAC.


## Simulation Logs Summary
```bash
Compilation Status: Successful
Simulation Runtime: 84999 ns (84.999 μs)
VCD File Generated: pre_synth_sim.vcd
Waveform Signals Captured: 50+ signals across all modules
Simulation Tool: Icarus Verilog v12.0
Waveform Viewer: GTKWave v3.3.116
```

---

### Deliverables Checklist

 **Installed Icarus Verilog and GTKWave on WSL Ubuntu**
 **Installed SandPiper-SaaS for TL-Verilog compilation**
 **Cloned VSDBabySoC repository successfully**
 **Compiled TL-Verilog source to standard Verilog**
 **Simulated complete BabySoC design with all modules**
 **Generated VCD waveform file (pre_synth_sim.vcd)**
 **Analyzed and documented reset operation**
 **Analyzed and documented clocking mechanism (PLL)**
 **Analyzed and documented dataflow between modules (CPU→DAC)**
 **Captured GTKWave screenshots with explanations**
 **Completed Part 1 theory write-up on SoC fundamentals**
 **Created comprehensive documentation**
 
---

 ### Learning Outcomes
Through this Week 2 task, the following competencies were demonstrated:

**SoC Design Understanding**: Comprehensive knowledge of SoC architecture, components, and design methodology
**Tool Proficiency**: Successfully set up and operated Icarus Verilog, GTKWave, and SandPiper-SaaS
**Mixed-Signal Design**: Understanding of digital-analog integration in the VSDBabySoC
**Simulation Skills**: Ability to compile, simulate, and verify complex digital designs
**Waveform Analysis**: Capability to interpret simulation results and identify correct system behavior
**TL-Verilog Workflow**: Experience with Transaction-Level Verilog compilation process
**Module Integration**: Understanding of inter-module communication and dataflow in SoC designs
**Verification Methodology**: Knowledge of functional verification as a critical pre-synthesis step


--- 

-Author: **Rahul Kumar**
-Date: **October 2025**
-Task: Week 2 - **BabySoC Fundamentals & Functional Modelling**

 **WEEK 2 Task 2 Status: ✅ Complete and Verified**
