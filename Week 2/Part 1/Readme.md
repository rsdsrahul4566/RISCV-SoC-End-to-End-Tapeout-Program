# 📚 Week 2 Part 1: SoC Design Fundamentals - Theory


[![SoC](https://img.shields.io/badge/Topic-System%20on%20Chip-blue.svg)]()
[![RISC-V](https://img.shields.io/badge/Architecture-RISC--V-red.svg)](https://riscv.org/)
[![Theory](https://img.shields.io/badge/Type-Conceptual%20Study-purple.svg)]()
[![VSD](https://img.shields.io/badge/Course-SFAL--VSD-green.svg)](https://www.vlsisystemdesign.com/)
[![Mixed-Signal](https://img.shields.io/badge/Design-Mixed%20Signal-orange.svg)]()
[![Status](https://img.shields.io/badge/Status-✅%20Complete-success.svg)]()

## 🎯 Objective

To develop a comprehensive understanding of System-on-Chip (SoC) design fundamentals and understand how VSDBabySoC serves as an educational platform for learning SoC concepts.

---

## 📖 Table of Contents

- [What is a System-on-Chip (SoC)?](#what-is-a-system-on-chip-soc)
- [Components of a Typical SoC](#components-of-a-typical-soc)
- [VSDBabySoC Architecture](#vsdbabysoc-architecture)
- [Why BabySoC for Learning](#why-babysoc-for-learning)
- [Role of Functional Modelling](#role-of-functional-modelling)
- [Detailed Simulation Flow](#Detailed-Simulation-Flow)
- [Conclusion](#conclusion)
- [References](#references)

---

## 🔷 What is a System-on-Chip (SoC)?

A **System-on-Chip (SoC)** is an integrated circuit that consolidates all necessary electronic components of a computer or electronic system onto a single chip. Unlike traditional systems where components like the processor, memory, and peripherals reside on separate chips connected via a motherboard, an SoC integrates these elements onto one substrate.

### Key Characteristics:

- **Integration**: Combines CPU, memory, I/O ports, and secondary storage on a single die
- **Efficiency**: Reduces power consumption, physical space, and manufacturing costs
- **Performance**: Minimizes signal delay between components due to proximity
- **Complexity**: Requires sophisticated design methodologies to manage millions of transistors

### Common Applications:

- Mobile devices (smartphones, tablets)
- Embedded systems (IoT devices, automotive electronics)
- Wearable technology (smartwatches, fitness trackers)
- Consumer electronics (smart TVs, gaming consoles)

---

## 🧩 Components of a Typical SoC

A modern SoC consists of several interconnected subsystems, each serving specific functions:

### 1. **Central Processing Unit (CPU)**

- **Function**: Executes instructions and performs computations
- **Types**: Single-core or multi-core processors
- **Architecture**: ARM, RISC-V, x86, MIPS, etc.
- **Role in SoC**: Acts as the "brain" controlling all system operations

### 2. **Memory Subsystem**

- **Cache Memory**: Fast, small-capacity memory for frequently accessed data
  - L1 Cache: Closest to CPU cores
  - L2/L3 Cache: Shared between cores
- **RAM Interface**: Controller for external DRAM (DDR4, DDR5, LPDDR)
- **ROM/Flash**: Non-volatile storage for firmware and boot code

### 3. **Peripherals and I/O Interfaces**

- **Communication Protocols**:
  - UART (Universal Asynchronous Receiver-Transmitter)
  - SPI (Serial Peripheral Interface)
  - I2C (Inter-Integrated Circuit)
  - USB (Universal Serial Bus)
  - PCIe (Peripheral Component Interconnect Express)
- **GPIO**: General Purpose Input/Output pins for custom interfacing
- **Timers and Counters**: For timing-critical operations

### 4. **Interconnect/Bus Architecture**

- **Purpose**: Connects all SoC components and enables data transfer
- **Common Standards**:
  - **AHB** (Advanced High-performance Bus): High-speed data transfer
  - **APB** (Advanced Peripheral Bus): Low-power peripheral connections
  - **AXI** (Advanced eXtensible Interface): High-performance protocol
  - **Wishbone**: Open-source alternative
- **Features**: Arbitration, address decoding, data routing

### 5. **Analog and Mixed-Signal IP Blocks**

- **PLL (Phase-Locked Loop)**: Generates stable clock frequencies
- **ADC (Analog-to-Digital Converter)**: Converts analog signals to digital
- **DAC (Digital-to-Analog Converter)**: Converts digital data to analog signals
- **Power Management Units**: Voltage regulators, power gating

### 6. **Clock and Reset Infrastructure**

- **Clock Distribution Network**: Delivers synchronized clock signals
- **Clock Gating**: Reduces dynamic power consumption
- **Reset Controllers**: Manages system initialization and recovery

### 7. **Specialized Accelerators** (in advanced SoCs)

- GPU (Graphics Processing Unit)
- DSP (Digital Signal Processor)
- Neural Processing Unit (NPU) for AI/ML
- Video/Audio codecs

---

## 🔬 VSDBabySoC Architecture

The **VSDBabySoC** is a simplified educational SoC designed to demonstrate fundamental SoC concepts without the overwhelming complexity of commercial designs.

### Architecture Overview:


```markdown
## 🔬 VSDBabySoC Architecture

### Architecture Overview Diagram


┌─────────────────────────────────────────────────────────────────┐
│                         VSDBabySoC                              │
│                                                                 │
│    ┌────────────┐          ┌─────────────┐                      │
│    │   AVSDPLL  │          │   RVMYTH    │                      │
│    │   (Analog  │   CLK    │  (RISC-V    │                      │
│    │    Block)  ├─────────►│    Core)    │                      │
│    │            │          │  (Digital)  │                      │
│    └─────▲──────┘          └──────┬──────┘                      │
│          │                        │                             │
│          │ REF                    │ RV_TO_DAC[9:0]              │
│          │                        │                             │
│    ┌─────┴──────┐          ┌──────▼──────┐                      │
│    │  Reference │          │   AVSDDAC   │                      │
│    │   Clock    │          │  (10-bit    │──OUT──► Analog       │
│    │   Input    │          │    DAC)     │        Output        │
│    │            │          │  (Analog)   │                      │
│    └────────────┘          └─────────────┘                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

Legend:
  ───►  : Signal Flow
  CLK   : Clock Signal from PLL to CPU
  REF   : Reference Clock Input to PLL
  RV_TO_DAC[9:0] : 10-bit Data Bus from CPU to DAC
  OUT   : Analog Output from DAC
```



### Component Details:

#### 1. **RVMYTH - RISC-V Microprocessor Core**

- **Type**: Digital block
- **ISA**: RISC-V (RV32I base instruction set)
- **Implementation**: Written in TL-Verilog (Transaction-Level Verilog)
- **Function**: Executes instructions and generates 10-bit output data
- **Features**:
  - 32-bit architecture
  - Simple pipeline implementation
  - Educational focus for understanding CPU design

#### 2. **AVSDPLL - Phase-Locked Loop**

- **Type**: Analog block
- **Function**: Generates stable system clock from reference input
- **Inputs**: 
  - REF: Reference clock
  - ENb_VCO, ENb_CP: Enable signals
- **Output**: CLK (stable system clock)
- **Purpose**: Demonstrates clock generation in mixed-signal systems

#### 3. **AVSDDAC - Digital-to-Analog Converter**

- **Type**: Analog block
- **Resolution**: 10-bit
- **Input**: RV_TO_DAC[9:0] from RISC-V core
- **Output**: Analog voltage (OUT)
- **Function**: Converts digital computational results to analog signals
- **Application**: Interfacing with analog world (sensors, actuators)

### Key Integration Points:

1. **Clock Domain**: PLL provides synchronized clock to RISC-V core
2. **Data Path**: 10-bit bus carries data from CPU to DAC
3. **Mixed-Signal Interface**: Demonstrates digital-analog integration

---

### **Component Interconnection Details:**
```markdown


┌──────────────┐
│   External   │
│  Reference   │
│    Clock     │
└──────┬───────┘
       │ REF
       │
       ▼
┌──────────────────────────────────────────────────────────┐
│  AVSDPLL (Phase-Locked Loop)                             │
│  ┌────────────────────────────────────────────────────┐  │
│  │ Inputs:  REF, ENb_VCO, ENb_CP, VCO_IN              │  │
│  │ Output:  CLK (Stable System Clock)                 │  │
│  │ Function: Generate stable clock from reference     │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────┬───────────────────────────────┘
                           │ CLK
                           │
                           ▼
┌──────────────────────────────────────────────────────────┐
│  RVMYTH (RISC-V Microprocessor)                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │ Input:   CLK (from PLL)                            │  │
│  │ Output:  RV_TO_DAC[9:0] (10-bit data)              │  │
│  │ Function: Execute instructions, generate output    │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────┬───────────────────────────────┘
                           │ RV_TO_DAC[9:0]
                           │
                           ▼
┌──────────────────────────────────────────────────────────┐
│  AVSDDAC (Digital-to-Analog Converter)                   │
│  ┌────────────────────────────────────────────────────┐  │
│  │ Inputs:  D[9:0], VREFH (reference voltage)         │  │
│  │ Output:  OUT (analog voltage)                      │  │
│  │ Function: Convert digital to analog signal         │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────┬───────────────────────────────┘
                           │ OUT
                           │
                           ▼
                    ┌──────────────┐
                    │   Analog     │
                    │   Output     │
                    │  (External)  │
                    └──────────────┘



```

---
## 🎓 Why BabySoC for Learning?

VSDBabySoC serves as an excellent educational platform for several reasons:

### 1. **Simplicity and Focus**

- **Minimal Components**: Only three core blocks (CPU, PLL, DAC)
- **Clear Functionality**: Each component has well-defined purpose
- **Reduced Complexity**: Easier to understand interactions without distraction
- **Manageable Scope**: Students can comprehend the entire system

### 2. **Mixed-Signal Integration**

- **Digital Domain**: RISC-V core demonstrates digital design
- **Analog Domain**: PLL and DAC show analog circuit integration
- **Real-World Relevance**: Most modern SoCs are mixed-signal systems
- **Interface Learning**: Understanding digital-analog boundaries

### 3. **Open-Source Philosophy**

- **Complete Accessibility**: All design files are available for study
- **Modification Freedom**: Students can experiment and enhance
- **Community Support**: Active community for learning and collaboration
- **No Licensing Barriers**: Free to use and learn from

### 4. **Industry-Standard Tools**

- **Verilog/SystemVerilog**: Industry-standard HDL
- **TL-Verilog**: Modern high-level design approach
- **Standard Simulation Flow**: Icarus Verilog, GTKWave
- **Practical Experience**: Tools used in real semiconductor industry

### 5. **Complete Design Flow**

VSDBabySoC covers the entire SoC design process:

```bash


Specification → RTL Design → Functional Simulation → Synthesis →
Physical Design → Timing Analysis → GDSII Generation


```
- **Holistic Learning**: Understanding end-to-end flow
- **Design Methodology**: Industry-standard practices
- **Verification Techniques**: Functional and timing verification
- **Practical Skills**: Hands-on experience with each stage

### 6. **Scalable Complexity**

- **Foundation**: Start with basic functional understanding
- **Expansion**: Can be extended with more peripherals
- **Customization**: Students can add UART, SPI, memory controllers
- **Progressive Learning**: Build complexity incrementally

---

## ⚙️ Role of Functional Modelling

Functional modelling is a critical phase in the SoC design flow that occurs **before RTL synthesis and physical design**. It serves multiple essential purposes:

### 1. **Design Verification**

**Purpose**: Validate that the design meets specifications

- **Behavioral Validation**: Confirm correct functionality without timing details
- **Specification Compliance**: Ensure design matches requirements
- **Early Detection**: Identify architectural flaws before hardware commitment
- **Cost Savings**: Fixing bugs in functional simulation is far cheaper than post-silicon

**Example in BabySoC**:
- Verify RISC-V core executes instructions correctly
- Confirm PLL generates stable clock frequency
- Validate DAC converts digital values accurately

### 2. **Interface Validation**

**Purpose**: Ensure modules communicate correctly

- **Protocol Verification**: Check handshaking, timing relationships
- **Data Integrity**: Verify data passes correctly between modules
- **Bus Transactions**: Validate interconnect behavior
- **Signal Compatibility**: Ensure voltage levels, timing margins are adequate

**Example in BabySoC**:
- RV_TO_DAC bus transfers correct 10-bit values
- Clock from PLL properly synchronizes CPU operations
- DAC receives and processes CPU output correctly

### 3. **Performance Analysis**

**Purpose**: Assess if design meets performance requirements

- **Throughput Measurement**: Data processing rate
- **Latency Analysis**: Delay between input and output
- **Resource Utilization**: Memory usage, computation cycles
- **Bottleneck Identification**: Find performance-limiting factors

**Example in BabySoC**:
- CPU instruction execution rate
- DAC conversion time
- System response to input stimuli

### 4. **Early Bug Detection**

**Purpose**: Find and fix errors when changes are easy

**Cost of Bug Fixes** (relative):
- Functional Simulation: **1x** (baseline cost)
- RTL Synthesis: **10x** 
- Physical Design: **100x**
- Post-Silicon: **10,000x**

- **Logic Errors**: Incorrect algorithms or state machines
- **Integration Errors**: Module interconnection mistakes
- **Specification Misunderstanding**: Design doesn't match intent
- **Corner Cases**: Rare conditions that cause failures

### 5. **Design Exploration**

**Purpose**: Evaluate different architectural options

- **Trade-off Analysis**: Performance vs. power vs. area
- **What-If Scenarios**: Test alternative implementations
- **Optimization**: Refine design before commitment
- **Risk Mitigation**: Identify potential issues early

**Example in BabySoC**:
- Different PLL configurations for clock stability
- DAC resolution trade-offs (8-bit vs. 10-bit vs. 12-bit)
- CPU pipeline depth optimization

### 6. **Documentation and Communication**

**Purpose**: Create reference for design team

- **Specification Validation**: Simulation results prove concept
- **Team Alignment**: Common understanding of functionality
- **Test Plan Development**: Basis for synthesis verification
- **Customer Demonstration**: Show working prototype

---

## ⚙️ Functional Modelling Workflow

```markdown



┌────────────────────────────────────────────────────────────┐
│  1. SPECIFICATION PHASE                                    │
│     • Define system requirements                           │
│     • Identify interfaces and protocols                    │
│     • Establish performance targets                        │
└──────────────────────┬─────────────────────────────────────┘
                       │
                       ▼
┌────────────────────────────────────────────────────────────┐
│  2. RTL DESIGN PHASE                                       │
│     • Write Verilog/TL-Verilog code                        │
│     • Implement modules (CPU, PLL, DAC)                    │
│     • Define module interfaces                             │
└──────────────────────┬─────────────────────────────────────┘
                       │
                       ▼
┌────────────────────────────────────────────────────────────┐
│  3. TESTBENCH DEVELOPMENT                                  │
│     • Create test scenarios                                │
│     • Generate input stimuli                               │
│     • Define expected outputs                              │
└──────────────────────┬─────────────────────────────────────┘
                       │
                       ▼
┌────────────────────────────────────────────────────────────┐
│  4. COMPILATION                                            │
│     • TL-Verilog → Verilog (SandPiper-SaaS)                │
│     • Compile with iverilog                                │
│     • Generate simulation executable                       │
└──────────────────────┬─────────────────────────────────────┘
                       │
                       ▼
┌────────────────────────────────────────────────────────────┐
│  5. FUNCTIONAL SIMULATION                                  │
│     • Run simulation executable                            │
│     • Generate VCD waveform file                           │
│     • Capture signal transitions                           │
└──────────────────────┬─────────────────────────────────────┘
                       │
                       ▼
┌────────────────────────────────────────────────────────────┐
│  6. WAVEFORM ANALYSIS                                      │
│     • Open VCD in GTKWave                                  │
│     • Verify signal behavior                               │
│     • Check timing relationships                           │
│     • Validate reset, clock, dataflow                      │
└──────────────────────┬─────────────────────────────────────┘
                       │
                       ▼
                   ┌───┴───┐
                   │ Pass? │
                   └───┬───┘
                       │
            ┌──────────┴──────────┐
            │                     │
           NO                    YES
            │                     │
            ▼                     ▼
┌────────────────────┐  ┌────────────────────┐
│  7. DEBUG & FIX    │  │  8. VERIFICATION   │
│     • Identify     │  │     SIGN-OFF       │
│       issues       │  │     • Document     │
│     • Modify RTL   │  │       results      │
│     • Re-simulate  │  │     • Archive      │
│     • Iterate      │  │       waveforms    │
└────────┬───────────┘  └─────────┬──────────┘
         │                        │
         └────────┬───────────────┘
                  │
                  ▼
        ┌──────────────────┐
        │   Proceed to     │
        │   Synthesis      │
        └──────────────────┘


```




--- 

# Proceed to Synthesis

### Tools for Functional Modelling:

- **Simulators**: Icarus Verilog, ModelSim, VCS, Xcelium
- **Waveform Viewers**: GTKWave, Verdi, DVE
- **Verification**: SystemVerilog UVM, Formal verification
- **Code Coverage**: Track tested vs. untested scenarios


### Detailed Simulation Flow

```markdown
┌─────────────────────────────────────────────────────────┐
│  INPUT: RTL Design Files                                │
│  • rvmyth.tlv (TL-Verilog)                             │
│  • vsdbabysoc.v, avsdpll.v, avsddac.v                  │
│  • testbench.v                                          │
└─────────────┬───────────────────────────────────────────┘
              │
              ▼
      ┌───────────────┐
      │ SandPiper-SaaS│
      │  Compilation  │
      └───────┬───────┘
              │
              ▼
┌─────────────────────────────────────────────────────────┐
│  COMPILED VERILOG                                       │
│  • rvmyth.v (converted from TL-Verilog)                │
│  • rvmyth_gen.v (generated auxiliary file)             │
└─────────────┬───────────────────────────────────────────┘
              │
              ▼
      ┌───────────────┐
      │   iverilog    │
      │  Compilation  │
      └───────┬───────┘
              │
              ▼
┌─────────────────────────────────────────────────────────┐
│  SIMULATION EXECUTABLE                                  │
│  • pre_synth_sim.out                                   │
└─────────────┬───────────────────────────────────────────┘
              │
              ▼
      ┌───────────────┐
      │   Execute     │
      │  Simulation   │
      └───────┬───────┘
              │
              ▼
┌─────────────────────────────────────────────────────────┐
│  VCD WAVEFORM FILE                                      │
│  • pre_synth_sim.vcd                                   │
│  • Contains all signal transitions                      │
└─────────────┬───────────────────────────────────────────┘
              │
              ▼
      ┌───────────────┐
      │   GTKWave     │
      │   Viewer      │
      └───────┬───────┘
              │
              ▼
┌─────────────────────────────────────────────────────────┐
│  ANALYSIS & VERIFICATION                                │
│  • Visual waveform inspection                           │
│  • Timing verification                                  │
│  • Functional correctness check                         │
└─────────────────────────────────────────────────────────┘
```


---

## 🎯 Conclusion

The VSDBabySoC project provides an excellent foundation for understanding System-on-Chip design fundamentals. By studying this simplified yet realistic SoC, learners gain insights into:

- **SoC Architecture**: How different components integrate into a cohesive system
- **Mixed-Signal Design**: Interaction between digital and analog domains
- **Design Methodology**: Industry-standard practices and workflows
- **Verification Techniques**: Importance of functional modelling and simulation
- **Practical Skills**: Hands-on experience with real design tools

---

### Key Takeaways:

1. **SoCs integrate multiple subsystems** (CPU, memory, peripherals, analog blocks) onto a single chip for efficiency
2. **BabySoC demonstrates core concepts** with minimal complexity, making it ideal for education
3. **Functional modelling is essential** for validating designs before expensive synthesis and fabrication
4. **Mixed-signal integration** is increasingly important in modern electronics
5. **Open-source tools and designs** enable accessible learning for SoC development

This theoretical foundation, combined with hands-on simulation practice, prepares students for more advanced topics in SoC design, including synthesis, physical design, timing analysis, and tape-out procedures.

---

## 📚 References

### Primary Resources

- [SFAL-VSD SoC Journey - Fundamentals of SoC Design](https://github.com/hemanthkumardm/SFAL-VSD-SoC-Journey/tree/main/11.%20Fundamentals%20of%20SoC%20Design)
- [VSDBabySoC GitHub Repository](https://github.com/manili/VSDBabySoC)

### Technical Documentation

- [RISC-V ISA Specifications](https://riscv.org/technical/specifications/)
- [ARM AMBA Specification](https://www.arm.com/architecture/system-architectures/amba)
- [IEEE 1364 Verilog Standard](https://ieeexplore.ieee.org/document/954909)

### Textbooks

- Harris, D. M., & Harris, S. L. (2021). *Digital Design and Computer Architecture: RISC-V Edition*
- Rabaey, J. M., Chandrakasan, A., & Nikolic, B. (2003). *Digital Integrated Circuits: A Design Perspective*
- Weste, N. H., & Harris, D. (2010). *CMOS VLSI Design: A Circuits and Systems Perspective*

### Online Learning

- [VSD - VLSI System Design](https://www.vlsisystemdesign.com/)
- [Redwood EDA - TL-Verilog](https://www.redwoodeda.com/tl-verilog)
- [Chipyard Framework](https://chipyard.readthedocs.io/)

---

- **Author**: Rahul Kumar  
- **Task**: Week 2 Part 1 - SoC Design Fundamentals (Theory)  
- **Date**: October 2025

---




