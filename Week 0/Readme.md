#  Week 0 - EDA Tools Installation - Using WSL

[![VLSI](https://img.shields.io/badge/VLSI-Design-blue)](https://vlsisystemdesign.com/)
[![Tools](https://img.shields.io/badge/EDA-Tools-green)](#tools-installed)
[![WSL](https://img.shields.io/badge/WSL-Ubuntu-orange)](#prerequisites)
[![Status](https://img.shields.io/badge/Status-Complete-brightgreen)](#verification-results)

## Overview

This repository documents the complete installation and verification of Electronic Design Automation (EDA) tools required for the SFAL VSD Course Week 0. All installations are performed in Windows Subsystem for Linux (WSL) environment, culminating in a successful OpenLANE RTL-to-GDSII implementation flow.



## Tools Installed

- **Yosys** - Open-source synthesis framework
- **Iverilog** - Verilog simulation and synthesis tool  
- **GTKWave** - Waveform viewer
- **NGSpice** - SPICE circuit simulator
- **Magic** - VLSI layout tool
- **Docker** - Container platform
- **OpenLANE** - Complete RTL-to-GDSII flow
- **sky130A PDK** - SkyWater 130nm Process Design Kit

## Prerequisites

### System Requirements
```
OS: Ubuntu 20.04+ (WSL2 recommended)
RAM: 6GB minimum
Storage: 50GB available space
CPU: 4vCPU cores
```

### Initial Environment Setup
```bash
# Update system packages
sudo apt-get update && sudo apt-get upgrade -y

# Install essential build tools
sudo apt-get install build-essential git wget curl -y
```

## Installation Instructions

### 1. Yosys Installation

**Fix ABC Submodule Issue (Critical Step)**

```bash
# Clone repository
git clone https://github.com/YosysHQ/yosys.git
cd yosys

# IMPORTANT: Initialize ABC submodule to prevent compilation errors
git submodule update --init

# Install dependencies
sudo apt-get install make clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev -y

# Clean and build
make clean
make config-gcc
make -j$(nproc)
sudo make install
cd ..
```

**Verification:**
```bash
yosys -V
yosys -p "help; exit"
```

**📸 Implementation Output:** Yosys version information and help menu display

<img width="1167" height="871" alt="Screenshot 2025-09-25 171854" src="https://github.com/user-attachments/assets/db6270e3-5685-4ed6-873c-b0cf7153abb8" />


### 2. Iverilog Installation

```bash
sudo apt-get update
sudo apt-get install iverilog -y
```

**Verification:**
```bash
iverilog -V

# Test with sample Verilog code
echo 'module test(input a, b, output c); assign c = a & b; endmodule' > test.v
iverilog -o test test.v
```

**📸 Implementation Output:** Icarus Verilog version and successful compilation

<img width="1183" height="975" alt="Screenshot 2025-09-25 173137" src="https://github.com/user-attachments/assets/8fdc8715-4561-404b-86ac-ea872d7c331a" />


### 3. GTKWave Installation

```bash
sudo apt-get update
sudo apt-get install gtkwave -y
```

**Verification:**
```bash
gtkwave --version
```

**📸 Implementation Output:** GTKWave version information

<img width="1845" height="721" alt="Screenshot 2025-09-25 173309" src="https://github.com/user-attachments/assets/0e61d784-bbba-4af5-9de9-607deb4b773b" />


### 4. NGSpice Installation (Complete Process)

**Install X11 Dependencies:**
```bash
sudo apt-get install libxaw7-dev libx11-dev libxt-dev \
    libxmu-dev libxext-dev libreadline-dev -y
```

**Download and Build:**
```bash
cd ~
wget https://sourceforge.net/projects/ngspice/files/ngspice-37.tar.gz/download -O ngspice-37.tar.gz
tar -zxvf ngspice-37.tar.gz
cd ngspice-37
mkdir release && cd release

# Configure with GUI support
../configure --with-x --with-readline=yes --disable-debug

# Compile (takes 10-30 minutes)
make -j$(nproc)
sudo make install
cd ../..
```

**Verification:**
```bash
ngspice --version
```

**📸 Implementation Output:** NGSpice version and configuration details

<img width="1018" height="500" alt="Screenshot 2025-09-25 162837" src="https://github.com/user-attachments/assets/b105e3e6-b556-4c33-95e9-cf30ef554927" />


### 5. Magic Installation

**Install Dependencies:**
```bash
sudo apt-get install m4 tcsh csh libx11-dev tcl-dev tk-dev \
    libcairo2-dev mesa-common-dev libglu1-mesa-dev libncurses-dev -y
```

**Build from Source:**
```bash
cd ~
git clone https://github.com/RTimothyEdwards/magic
cd magic
./configure --prefix=/usr/local
make -j$(nproc)
sudo make install
cd ..
```

**Verification:**
```bash
magic -noconsole -dnull <<< "puts [magic::version]; quit -noprompt" 2>/dev/null
```

**Note:** Magic Tcl interface may need additional configuration for full functionality.

<img width="1867" height="883" alt="Screenshot 2025-09-25 173833" src="https://github.com/user-attachments/assets/a7bd69f6-3b89-4530-b84f-f1b3b6de8062" />


### 6. Docker Installation

**Install Docker:**
```bash
# Remove old Docker versions
sudo apt-get remove docker docker-engine docker.io containerd runc

# Install prerequisites
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl \
    gnupg lsb-release software-properties-common -y

# Add Docker GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
    sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
    https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io -y

# Add user to docker group
sudo groupadd docker 2>/dev/null || true
sudo usermod -aG docker $USER

# Test Docker (may require WSL restart)
sudo docker run hello-world
```

**Fix Docker Permissions (Important):**
```bash
# Exit WSL completely and restart, then test:
docker run hello-world
```

**📸 Implementation Output:** Docker hello-world message without sudo

<img width="1319" height="786" alt="Screenshot 2025-09-25 174217" src="https://github.com/user-attachments/assets/11721dcd-9f06-405d-98c6-2be07a5e8599" />


### 7. OpenLANE Installation (Complete Flow)

**Install OpenLANE:**
```bash
cd $HOME
git clone https://github.com/The-OpenROAD-Project/OpenLane
cd OpenLane

# Build OpenLANE (downloads containers and tools)
make
```

**Install Sky130 PDK:**
```bash
# Fix permissions if needed
sudo chown -R $USER:$USER ~/.ciel/

# List available PDK versions
./venv/bin/ciel ls-remote --pdk-family sky130

# Install compatible PDK version for OpenLANE
./venv/bin/ciel enable --pdk-family sky130 0fe599b2afb6708d281543108caf8310912f54af
```

**Test OpenLANE:**
```bash
make test
```

**📸 Implementation Output:** Successful SPM test design completion with "Basic test passed"

<img width="1919" height="1023" alt="Screenshot 2025-09-26 181802" src="https://github.com/user-attachments/assets/88b7efa3-1244-4daa-af01-14b1fd23cbd1" />

<img width="1703" height="1003" alt="Screenshot 2025-09-26 181745" src="https://github.com/user-attachments/assets/5417e107-d4b2-4413-96d1-159caa88a6fa" />




### Installation Status

- [x] **Yosys** - Synthesis framework working with ABC submodule
- [x] **Iverilog** - Verilog simulator verified  
- [x] **GTKWave** - Waveform viewer installed
- [x] **NGSpice** - Circuit simulator with GUI support
- [x] **Magic** - Layout tool installed (Tcl interface needs configuration)
- [x] **Docker** - Container platform working without sudo
- [x] **OpenLANE** - RTL-to-GDSII flow complete
- [x] **sky130A PDK** - Process design kit installed and verified

![Screenshot - Replace with your verification script output]()

## OpenLANE Success Proof

The ultimate verification of a complete EDA toolchain installation is a successful OpenLANE test run. The following output confirms all tools working together:

```
[INFO]: Running Magic DRC (log: designs/spm/runs/openlane_test/logs/signoff/40-drc.log)...
[INFO]: Converting Magic DRC database to various tool-readable formats...
[INFO]: No DRC violations after GDS streaming out.
[STEP 41] Running OpenROAD Antenna Rule Checker...
[STEP 42] Running Circuit Validity Checker ERC...
[INFO]: Saving current set of views in 'designs/spm/runs/openlane_test/results/final'...
[SUCCESS]: Flow complete.
Basic test passed
```

This proves:
- Complete RTL-to-GDSII implementation working
- All synthesis, place & route, and verification tools integrated
- PDK properly installed and configured
- Final GDSII layout ready for manufacturing

![Screenshot - Replace with your OpenLANE success screenshot]()

## Troubleshooting

### Common Issues and Solutions

| Issue | Cause | Solution |
|-------|--------|----------|
| **Yosys: "multiple definition of '_start'"** | ABC submodule not initialized | Run `git submodule update --init` |
| **NGSpice: "Couldn't find Xaw library"** | Missing X11 libraries | Install `libxaw7-dev libx11-dev` packages |
| **Docker: "permission denied"** | User not in docker group | Restart WSL after `usermod -aG docker $USER` |
| **OpenLANE: "No container engine found"** | Docker permissions not applied | Exit and restart WSL completely |
| **PDK: "Permission denied"** | .ciel directory owned by root | Run `sudo chown -R $USER:$USER ~/.ciel/` |

### Recovery Commands

**Reset Docker permissions:**
```bash
sudo usermod -aG docker $USER
# Then restart WSL completely
```

**Fix PDK permissions:**
```bash
sudo chown -R $USER:$USER ~/.ciel/
```

**Clean build Yosys:**
```bash
cd yosys
git submodule update --init
make clean
make config-gcc
make
sudo make install
```

## System Information

| Component | Details |
|-----------|---------|
| **OS** | Ubuntu 20.04.6 LTS (WSL2) |
| **Installation Environment** | Windows Subsystem for Linux |
| **Total Installation Time** | ~4-6 hours (including OpenLANE build) |
| **Disk Space Used** | ~20-25 GB |
| **Installation Date** | [Add your date] |

## Key Learning Outcomes

### Technical Skills Gained
- **EDA Tool Installation** - Complete open-source VLSI toolchain setup
- **Linux System Administration** - Package management and dependency resolution
- **Docker Containerization** - Container-based tool deployment
- **Build System Management** - Compilation from source with proper configuration
- **Process Design Kit Integration** - PDK installation and OpenLANE integration

### Problem-Solving Experience
- **Dependency Management** - Resolving library and package conflicts
- **Permission Issues** - Docker and file system permission configuration
- **Build Failures** - Debugging compilation errors and missing dependencies
- **Container Integration** - OpenLANE Docker-based workflow setup

### VLSI Design Flow Understanding
- **RTL-to-GDSII Flow** - Complete understanding through successful OpenLANE test
- **Tool Integration** - How synthesis, P&R, and verification tools work together
- **PDK Usage** - Process design kit integration for real fabrication process


## Next Steps

With the complete EDA toolchain installed:

1. **Digital Circuit Design** - Start with basic RTL designs in Verilog
2. **Analog Circuit Simulation** - Use NGSpice for circuit analysis
3. **Layout Design** - Learn Magic for custom cell design
4. **Complete Design Flow** - Run personal designs through OpenLANE
5. **Advanced Verification** - Explore formal verification and timing analysis

## Conclusion

This Week 0 task successfully demonstrates the installation of a complete, production-ready EDA toolchain in WSL. The successful OpenLANE test run proves that all tools are properly integrated and capable of handling real VLSI design projects from RTL to GDSII layout.

The installation provides a solid foundation for advanced VLSI design coursework and professional IC development work.

---

**Author:** Rahul Kumar
**Week:** 0 - Tools Installation  
**Status:** Complete ✅

[![GitHub](https://img.shields.io/badge/GitHub-Repository-181717?logo=github)](https://github.com/yourusername/sfal-vsd-week0)
[![OpenLANE](https://img.shields.io/badge/OpenLANE-Verified-green?logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mNk+M9QDwADhgGAWjR9awAAAABJRU5ErkJggg==)]()
[![PDK](https://img.shields.io/badge/Sky130A-PDK%20Ready-blue)]()
