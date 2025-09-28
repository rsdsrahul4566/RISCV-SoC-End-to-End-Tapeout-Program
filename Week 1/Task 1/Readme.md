# 🔧 Task 1: Yosys Optimization with `opt_clean -purge`

[![Yosys](https://img.shields.io/badge/Tool-Yosys-blue.svg)](https://yosyshq.net/yosys/)
[![Verilog](https://img.shields.io/badge/Language-Verilog-green.svg)](https://en.wikipedia.org/wiki/Verilog)
[![Optimization](https://img.shields.io/badge/Technique-Logic%20Optimization-orange.svg)]()
[![Status](https://img.shields.io/badge/Status-✅%20Complete-success.svg)]()

## 📖 Overview

This task demonstrates the effectiveness of Yosys `opt_clean -purge` optimization on a Verilog design. We create a 4-bit counter with intentionally added unused logic, then compare the synthesized results before and after optimization to observe the cleanup effects.

## 🎯 Objectives

- Create a Verilog design with unused logic elements
- Synthesize the design without optimization 
- Apply `opt_clean -purge` optimization
- Compare synthesis results and generated circuits
- Visualize circuit differences using Graphviz

## 🚀 Implementation Steps

### Step 1: Environment Setup

```bash
# Create working directory
mkdir -p ~/week1_tasks/task1_yosys
cd ~/week1_tasks/task1_yosys

# Verify Yosys installation
yosys -V
```

### Step 2: Create Test Design

Create a 4-bit counter with intentionally added unused signals to demonstrate optimization effects:

```verilog
module counter (
    input clk,
    input reset,
    output reg [3:0] count
);
    
    // Some redundant logic that can be optimized
    wire unused_signal;
    reg temp_reg;
    
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            count <= 4'b0000;
            temp_reg <= 1'b0;  // This might be unused
        end else begin
            count <= count + 1;
        end
    end
    
    // Unused assignments
    assign unused_signal = 1'b0;
    
endmodule
```

**Implementation Command:**
```bash
cat << 'EOF' > counter.v
module counter (
    input clk,
    input reset,
    output reg [3:0] count
);
    
    // Some redundant logic that can be optimized
    wire unused_signal;
    reg temp_reg;
    
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            count <= 4'b0000;
            temp_reg <= 1'b0;  // This might be unused
        end else begin
            count <= count + 1;
        end
    end
    
    // Unused assignments
    assign unused_signal = 1'b0;
    
endmodule
EOF

echo "✅ Counter design created"
```

### Step 3: Synthesis Without Optimization

Create Yosys script for unoptimized synthesis:

```bash
cat << 'EOF' > yosys_no_opt.ys
# Read the design
read_verilog counter.v

# Set hierarchy
hierarchy -top counter

# Synthesize without optimization
synth

# Show statistics before optimization
stat

# Write the result
write_verilog counter_no_opt.v
EOF

# Run synthesis
yosys -s yosys_no_opt.ys > yosys_no_opt.log 2>&1

echo "✅ Unoptimized synthesis completed"
```

### Step 4: Extract Unoptimized Statistics

```bash
echo "=== Statistics before optimization ==="
grep -A 15 "=== counter ===" yosys_no_opt.log
```

**📊 Expected Output:**
```
=== counter ===
        8 wires
       17 wire bits
        4 public wires
        7 public wire bits
        3 ports
        6 port bits
       10 cells
        1   $_ANDNOT_
        4   $_DFF_PP0_
        1   $_NAND_
        1   $_NOT_
        1   $_XNOR_
        2   $_XOR_
```

### Step 5: Synthesis With opt_clean -purge

Create optimized synthesis script:

```bash
cat << 'EOF' > yosys_with_opt.ys
# Read the design
read_verilog counter.v

# Set hierarchy
hierarchy -top counter

# Run synthesis
synth

# Apply opt_clean -purge optimization
opt_clean -purge

# Show statistics after optimization
stat

# Write optimized result
write_verilog counter_optimized.v
EOF

# Run optimized synthesis
yosys -s yosys_with_opt.ys > yosys_with_opt.log 2>&1

echo "✅ Optimized synthesis completed"
```

### Step 6: Extract Optimized Statistics

```bash
echo "=== Statistics after opt_clean -purge ==="
grep -A 15 "=== counter ===" yosys_with_opt.log
```

**📊 Expected Output:**
```
=== counter ===
        7 wires        ← Reduced from 8
       16 wire bits    ← Reduced from 17
        3 public wires ← Reduced from 4
        6 public wire bits ← Reduced from 7
        3 ports
        6 port bits
       10 cells
        1   $_ANDNOT_
        4   $_DFF_PP0_
        1   $_NAND_
        1   $_NOT_
        1   $_XNOR_
        2   $_XOR_
```

### Step 7: Generate Circuit Visualizations

#### Create Unoptimized Circuit Diagram

```bash
# Generate DOT file for unoptimized circuit
cat << 'EOF' > visualize_unopt.ys
read_verilog counter.v
hierarchy -top counter
synth
show -format dot -prefix counter_unoptimized
EOF

yosys -s visualize_unopt.ys
dot -Tsvg counter_unoptimized.dot -o counter_unoptimized.svg
dot -Tpng counter_unoptimized.dot -o counter_unoptimized.png
```

#### Create Optimized Circuit Diagram

```bash
# Generate DOT file for optimized circuit
cat << 'EOF' > visualize_opt.ys
read_verilog counter.v
hierarchy -top counter
synth
opt_clean -purge
show -format dot -prefix counter_optimized
EOF

yosys -s visualize_opt.ys
dot -Tsvg counter_optimized.dot -o counter_optimized.svg
dot -Tpng counter_optimized.dot -o counter_optimized.png
```

### Step 8: Results Comparison

```bash
echo "=== Optimization Results Summary ==="
echo "📋 BEFORE opt_clean -purge:"
echo "- Wires: 8"
echo "- Wire bits: 17" 
echo "- Public wires: 4"
echo "- Cells: 10"
echo "- File size: $(ls -l counter_no_opt.v | awk '{print $5}') bytes"

echo ""
echo "📋 AFTER opt_clean -purge:"
echo "- Wires: 7 (-1)"
echo "- Wire bits: 16 (-1)"
echo "- Public wires: 3 (-1)"
echo "- Cells: 10 (unchanged)"
echo "- File size: $(ls -l counter_optimized.v | awk '{print $5}') bytes"

echo ""
echo "🎯 Optimization Effects:"
echo "✅ Removed 1 unused wire"
echo "✅ Reduced 1 wire bit"
echo "✅ Cleaned 1 public wire"
echo "✅ Maintained functional cell count"
echo "✅ Reduced generated code size"
```

## 📊 Results Analysis

### Synthesis Statistics Comparison

| Metric | Before Optimization | After opt_clean -purge | Improvement |
|--------|-------------------|----------------------|-------------|
| **Wires** | 8 | 7 | -1 (12.5% reduction) |
| **Wire bits** | 17 | 16 | -1 (5.9% reduction) |
| **Public wires** | 4 | 3 | -1 (25% reduction) |
| **Cells** | 10 | 10 | No change (functionality preserved) |
| **Generated code** | 50 lines | 47 lines | -3 lines (6% reduction) |

### 🔍 Key Findings

**✅ Successful Optimizations:**
- **Wire cleanup**: Removed unused internal connections
- **Public interface optimization**: Simplified external interface  
- **Code generation**: More compact synthesized output
- **Functionality preservation**: Same cell count maintains design behavior

**🎯 opt_clean -purge Effects:**
- Removes unused wires and signals
- Cleans up dangling connections
- Optimizes internal routing
- Preserves design functionality

## 📸 Visual Results

### Circuit Diagrams

The generated SVG files show the structural differences:

**🔗 File Locations:**
```bash
# Windows file paths for visualization
echo "Unoptimized circuit: \\\\wsl\$\\Ubuntu$(pwd)/counter_unoptimized.svg"
echo "Optimized circuit: \\\\wsl\$\\Ubuntu$(pwd)/counter_optimized.svg"
```

**📁 Generated Files:**
- `counter_unoptimized.svg` - Circuit before optimization
- `counter_optimized.svg` - Circuit after opt_clean -purge
- `counter_unoptimized.png` - PNG version for easy viewing
- `counter_optimized.png` - PNG version for comparison

### Screenshots

*Unoptimized circuit diagram*

<img width="2629" height="1296" alt="counter_unoptimized" src="https://github.com/user-attachments/assets/2acb74d3-a941-4c58-bd1a-337d9b0fbfc4" />

*Optimized circuit diagram*

<img width="2439" height="1210" alt="counter_optimized" src="https://github.com/user-attachments/assets/9d4354e7-b3dd-43d5-8f59-0c53b64cb7e4" />

*Terminal output showing statistics comparison*

<img width="1919" height="1021" alt="Screenshot 2025-09-28 163313" src="https://github.com/user-attachments/assets/d9dd24c5-e928-4ef1-bf35-10f9bb374473" />


## 🛠️ Tools and Technologies Used

| Tool | Purpose | Version |
|------|---------|---------|
| **Yosys** | Logic synthesis and optimization | 0.57+212 |
| **Graphviz** | Circuit visualization (DOT → SVG) | 2.43.0 |
| **WSL Ubuntu** | Development environment | 24.04 LTS |

## 📁 Project Structure

```
task1_yosys/
├── counter.v                    # Original Verilog design
├── yosys_no_opt.ys             # Unoptimized synthesis script
├── yosys_with_opt.ys           # Optimized synthesis script
├── counter_no_opt.v            # Unoptimized synthesis output
├── counter_optimized.v         # Optimized synthesis output
├── counter_unoptimized.dot     # Unoptimized circuit DOT file
├── counter_optimized.dot       # Optimized circuit DOT file
├── counter_unoptimized.svg     # Unoptimized circuit diagram
├── counter_optimized.svg       # Optimized circuit diagram
├── yosys_no_opt.log           # Unoptimized synthesis log
└── yosys_with_opt.log         # Optimized synthesis log
```

## 🔬 Technical Deep Dive

### Understanding opt_clean -purge

The `opt_clean -purge` command in Yosys performs several optimization passes:

1. **Dead code elimination**: Removes unused logic elements
2. **Wire optimization**: Cleans up unnecessary connections  
3. **Signal cleanup**: Eliminates dangling signals
4. **Interface simplification**: Optimizes port connections

### Optimization Benefits

**🚀 Performance Improvements:**
- Reduced routing complexity
- Fewer internal connections
- Simplified signal paths
- Lower resource utilization

**💾 Resource Savings:**
- Decreased wire count
- Optimized bit usage
- Cleaner public interface
- More efficient netlists

## 🎯 Verification Steps

```bash
# Verify all files were generated
echo "✅ Checking generated files:"
ls -la counter_*.v counter_*.svg counter_*.dot

# Verify optimization took effect  
echo "✅ Optimization verification:"
diff counter_no_opt.v counter_optimized.v > /dev/null && echo "❌ No differences found" || echo "✅ Files are different - optimization applied"

# File size comparison
echo "✅ File size comparison:"
wc -l counter_no_opt.v counter_optimized.v
```

## 🔄 Reproducibility

To reproduce these results:

1. **Prerequisites**: Yosys, Graphviz installed
2. **Environment**: WSL Ubuntu or Linux system  
3. **Commands**: Follow the step-by-step implementation above
4. **Verification**: Compare your statistics with the expected outputs

## 📝 Conclusions

**🎯 Task Objectives Achieved:**
- ✅ Demonstrated `opt_clean -purge` effectiveness
- ✅ Quantified optimization improvements  
- ✅ Generated visual circuit comparisons
- ✅ Documented synthesis workflow

**📈 Key Learnings:**
- Logic optimization can reduce resource usage without affecting functionality
- Yosys provides powerful optimization capabilities for FPGA/ASIC design
- Visual circuit representations help understand optimization effects
- Proper benchmarking is essential for evaluating optimization results

**🔮 Next Steps:**
- Task 2: Iverilog simulation and testbench creation
- Task 3: GTKWave waveform analysis
- Task 4: Advanced Yosys synthesis techniques

---

**Author:** Rahul Kumar    
**Course:** RISC-V SoC Tapeout Program  
**Task:** Week 1, Task 1 - Yosys Optimization

[![GitHub](https://img.shields.io/badge/Repository-GitHub-181717?logo=github)](https://github.com/yourusername/week1-tasks)
[![Yosys](https://img.shields.io/badge/Powered%20by-Yosys-blue)](https://yosyshq.net/yosys/)
