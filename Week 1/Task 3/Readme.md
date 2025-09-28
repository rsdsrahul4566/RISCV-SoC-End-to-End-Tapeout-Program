# 🔀 Task 3: Advanced MUX Design using For-Generate with GTKWave Analysis

[![Verilog](https://img.shields.io/badge/Language-Verilog--2001-green.svg)](https://en.wikipedia.org/wiki/Verilog)
[![SystemVerilog](https://img.shields.io/badge/Language-SystemVerilog-blue.svg)](https://en.wikipedia.org/wiki/SystemVerilog)
[![GTKWave](https://img.shields.io/badge/Tool-GTKWave-red.svg)](http://gtkwave.sourceforge.net/)
[![Generate](https://img.shields.io/badge/Technique-For--Generate-orange.svg)]()
[![Status](https://img.shields.io/badge/Status-Verified-success.svg)]()

## 📋 Overview

Task 3 demonstrates advanced Verilog design techniques using **for-generate loops** to create scalable, parametric multiplexer architectures. This implementation showcases both **Verilog-2001 compatible** and **SystemVerilog** approaches, with comprehensive GTKWave verification and synthesis analysis.

## 🚀 Key Features Implemented

### ✨ Design Highlights
- **Parametric MUX**: Configurable data width and input count using parameters
- **For-Generate Loops**: Automatic hardware generation for scalable designs  
- **Multiple Architectures**: Standard MUX vs Priority Encoder implementations
- **Dual Compatibility**: Both Verilog-2001 and SystemVerilog versions
- **Advanced Features**: Enable control, validation signals, debug generation

### 🔧 Technical Specifications
- **Parametric MUX**: 8-bit data, 4 inputs (configurable)
- **Advanced MUX**: 16-bit data, 8 inputs with priority encoding option
- **Generate Constructs**: Input flattening, debug signal creation, conditional architecture
- **Comprehensive Testing**: Full coverage testbench with automated verification

## 📁 Project Structure

```
task3_gtkwave/
├── 📄 Design Files (Verilog-2001 Compatible)
│   ├── parametric_mux_fixed.v       # Main parametric MUX design
│   ├── advanced_mux_fixed.v         # Advanced MUX with features
│   └── mux_testbench_fixed.v        # Comprehensive testbench
├── 📄 Design Files (SystemVerilog)
│   ├── parametric_mux.v             # Original SystemVerilog design
│   ├── advanced_mux.v               # SystemVerilog advanced MUX
│   └── mux_testbench.v              # SystemVerilog testbench
├── 🔄 Simulation & Analysis
│   ├── mux_testbench.vcd            # GTKWave waveform database
│   ├── mux_analysis.gtkw            # GTKWave configuration
│   └── compile_mux.sh               # Compilation script
├── 🔨 Synthesis Files
│   ├── synth_mux.ys                 # Yosys synthesis script
│   ├── mux_parametric.svg           # Circuit diagram
│   └── mux_synth.log                # Synthesis results
└── 📚 Documentation
    ├── README.md                    # This file
    └── IMPLEMENTATION_NOTES.md      # Detailed implementation notes
```

## 🔄 Key Changes from Reference Implementation

### 🛠️ **Problem Solved: SystemVerilog Compatibility Issue**

**Original Issue:**
```bash
parametric_mux.v:5: error: Ports cannot be unpacked arrays. Try enabling SystemVerilog support.
advanced_mux.v:7: error: Ports cannot be unpacked arrays. Try enabling SystemVerilog support.
```

### 📝 **Solution 1: Verilog-2001 Compatible Design**

**Before (SystemVerilog):**
```verilog
module parametric_mux #(
    parameter WIDTH = 8,
    parameter INPUTS = 4
)(
    input [WIDTH-1:0] data_in [0:INPUTS-1],  // ❌ Unpacked array - SystemVerilog only
    input [$clog2(INPUTS)-1:0] select,
    output reg [WIDTH-1:0] data_out
);
```

**After (Verilog-2001 Compatible):**
```verilog
module parametric_mux #(
    parameter WIDTH = 8,
    parameter INPUTS = 4
)(
    input [WIDTH*INPUTS-1:0] data_in_flat,   // ✅ Flattened packed array
    input [$clog2(INPUTS)-1:0] select,
    output reg [WIDTH-1:0] data_out
);

// ✅ Generate block to unflatten inputs
genvar i;
generate
    for (i = 0; i < INPUTS; i = i + 1) begin : gen_unflatten
        assign data_in[i] = data_in_flat[(i+1)*WIDTH-1 : i*WIDTH];
    end
endgenerate
```

### 🔧 **Enhanced Testbench Architecture**

**Added Features:**
- **Flattened Array Management**: Automatic conversion between packed/unpacked arrays
- **Generate-Based Test Data**: Scalable test pattern generation
- **Dual MUX Testing**: Simultaneous verification of both MUX types
- **Enhanced Monitoring**: Comprehensive signal tracking and validation

**Testbench Generate Example:**
```verilog
// ✅ Generate blocks for array flattening in testbench
genvar i;
generate
    for (i = 0; i < INPUTS; i = i + 1) begin : gen_flatten_test
        always @(*) begin
            test_data_flat[(i+1)*WIDTH-1 : i*WIDTH] = test_data[i];
        end
    end
endgenerate
```

### 📊 **Solution 2: SystemVerilog Support**

**Compilation Commands:**
```bash
# ✅ Method 1: Verilog-2001 Compatible (Recommended)
iverilog -o mux_sim parametric_mux_fixed.v advanced_mux_fixed.v mux_testbench_fixed.v

# ✅ Method 2: SystemVerilog Support
iverilog -g2012 -o mux_sim parametric_mux.v advanced_mux.v mux_testbench.v
```

## 🚀 Quick Start Guide

### Step 1: Clone and Setup
```bash
cd ~/week1_tasks
mkdir -p task3_gtkwave
cd task3_gtkwave
```

### Step 2: Create Design Files
```bash
# Download the fixed Verilog-2001 compatible files
# (Files provided in project structure above)
```

### Step 3: Compile and Simulate
```bash
# Method 1: Verilog-2001 Compatible (Recommended)
iverilog -o mux_sim parametric_mux_fixed.v advanced_mux_fixed.v mux_testbench_fixed.v
vvp mux_sim

# Method 2: SystemVerilog Support
iverilog -g2012 -o mux_sim_sv parametric_mux.v advanced_mux.v mux_testbench.v
vvp mux_sim_sv
```

### Step 4: GTKWave Analysis
```bash
# Launch GTKWave with pre-configured signals
gtkwave mux_testbench.vcd mux_analysis.gtkw &

# Or launch manually
gtkwave mux_testbench.vcd &
```

### Step 5: Synthesis (Optional)
```bash
# Synthesize with Yosys
yosys -s synth_mux.ys

# Generate circuit diagram
dot -Tsvg mux_parametric.dot -o mux_parametric.svg
```

## 🔍 GTKWave Analysis Results

### 📈 **Verified Functionality**

**Parametric MUX (8-bit, 4 inputs):**
- ✅ Select transitions: `00 → 01 → 10 → 11`
- ✅ Output values: `0x10 → 0x26 → 0x3C → 0x52`
- ✅ Perfect input-to-output correlation

**Advanced MUX (16-bit, 8 inputs):**
- ✅ Select range: `0 → 7` (3-bit select)
- ✅ Output sequence: `0x1000 → 0x1100 → 0x1200 → ... → 0x1700`
- ✅ Enable functionality working correctly
- ✅ Valid signal properly controlled

### 📊 **Key Waveform Observations**
- **Clean Transitions**: No glitches or undefined states
- **Proper Timing**: Immediate output updates on select change
- **Generate Effectiveness**: Flattened arrays work perfectly
- **Signal Integrity**: All signals maintain expected values

## 🏗️ Design Architecture

### 🔧 **For-Generate Constructs Used**

#### 1. **Input Array Unflattening**
```verilog
generate
    for (i = 0; i < INPUTS; i = i + 1) begin : gen_unflatten
        assign data_in[i] = data_in_flat[(i+1)*WIDTH-1 : i*WIDTH];
    end
endgenerate
```

#### 2. **Conditional Architecture Selection**
```verilog
generate
    if (USE_PRIORITY == 0) begin : gen_normal_mux
        // Standard MUX implementation
    end else begin : gen_priority_mux
        // Priority encoder implementation
    end
endgenerate
```

#### 3. **Debug Signal Generation**
```verilog
generate
    for (g = 0; g < NUM_INPUTS; g = g + 1) begin : gen_debug
        wire selected;
        assign selected = (sel == g) && enable;
    end
endgenerate
```

### ⚙️ **Parametric Design Benefits**

- **Scalability**: Easy to change data width and input count
- **Reusability**: Same code works for different MUX configurations
- **Maintainability**: Single source for multiple MUX sizes
- **Synthesis Optimization**: Generate only required hardware

## 📊 Synthesis Results

### 🔨 **Yosys Synthesis Statistics**
```
=== parametric_mux ===
   Number of cells:         24
   Number of processes:      0
   Number of nets:          35
   Number of variables:      0
```

### 🎯 **Resource Utilization**
- **Logic Cells**: Minimal usage for 4:1 MUX
- **Timing**: Pure combinational logic (no clock dependency)
- **Area**: Optimized gate-level implementation
- **Power**: Low power consumption

## 🧪 Verification Summary

### ✅ **Test Coverage**
- [x] All select combinations tested
- [x] Enable/disable functionality verified
- [x] Edge cases and boundary conditions
- [x] Generate block effectiveness confirmed
- [x] Both MUX architectures validated
- [x] GTKWave waveform analysis completed
- [x] Synthesis verification passed

### 📈 **Performance Metrics**
- **Simulation Time**: ~300ns total test duration
- **Signal Transitions**: Clean and immediate
- **Resource Usage**: Optimal for given functionality
- **Compatibility**: Works with both Verilog-2001 and SystemVerilog tools

## 🔗 Advanced Features

### 🚀 **Priority Encoder Mode**
```verilog
// Enable priority encoder mode
advanced_mux #(
    .DATA_WIDTH(16),
    .NUM_INPUTS(8),
    .USE_PRIORITY(1)  // 🔄 Switch to priority mode
) priority_mux_inst (...);
```

### 🛠️ **Configurable Parameters**
```verilog
// Customize MUX specifications
parametric_mux #(
    .WIDTH(16),     // 🔧 Change data width
    .INPUTS(8)      // 🔧 Change input count
) custom_mux (...);
```

## 📚 Learning Outcomes

### 🎓 **Key Concepts Demonstrated**
1. **For-Generate Loops**: Scalable hardware generation
2. **Parametric Design**: Configurable digital circuits
3. **Array Handling**: Packed vs unpacked array conversion
4. **Tool Compatibility**: Verilog-2001 vs SystemVerilog differences
5. **Verification**: Comprehensive testbench design
6. **Analysis**: GTKWave waveform interpretation

### 💡 **Best Practices Applied**
- Modular design with clear interfaces
- Comprehensive parameter validation
- Extensive commenting and documentation
- Generate block naming conventions
- Proper signal timing considerations

## 🐛 Troubleshooting

### ❗ **Common Issues & Solutions**

**Issue: "Ports cannot be unpacked arrays"**
```bash
# ✅ Solution: Use Verilog-2001 compatible version
iverilog -o mux_sim parametric_mux_fixed.v advanced_mux_fixed.v mux_testbench_fixed.v
```

**Issue: "SystemVerilog features not supported"**
```bash
# ✅ Solution: Enable SystemVerilog support
iverilog -g2012 -o mux_sim parametric_mux.v advanced_mux.v mux_testbench.v
```

**Issue: "GTKWave not displaying signals"**
```bash
# ✅ Solution: Check VCD file generation
$dumpfile("mux_testbench.vcd");
$dumpvars(0, mux_testbench);
```

## Implementation Output 

 <img width="1604" height="1019" alt="Screenshot 2025-09-28 184017" src="https://github.com/user-attachments/assets/d8c3b096-a840-42ab-bfd9-92abfd60cf08" />


## 🎯 Conclusion

**Task 3 successfully demonstrates:**

✅ **Advanced generate constructs** for scalable MUX design  
✅ **Dual compatibility** with Verilog-2001 and SystemVerilog  
✅ **Comprehensive verification** using GTKWave analysis  
✅ **Parametric design** principles for reusable hardware  
✅ **Professional development** practices and documentation  

This implementation showcases how **for-generate loops** enable efficient creation of scalable digital designs while maintaining compatibility across different Verilog standards and tools.

---

### 📞 Support

For questions or issues:
- Check the troubleshooting section above
- Review GTKWave waveforms for signal verification  
- Verify compilation with both methods provided
- Ensure all generate blocks are properly named

**Task 3 Status: ✅ Complete and Verified**
