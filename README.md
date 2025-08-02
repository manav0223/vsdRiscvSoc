# 🛠️ Task 1: RISC-V Toolchain Setup and Verification Using WSL
#📋 Table of Contents
#Overview
#Prerequisites
#Installation
#Issues Encountered & Solutions
#Project Structure
#Contributing
#License
#🎯 Overview
This project documents the complete setup process for a RISC-V development toolchain, including:

RISC-V GCC Cross-Compiler (riscv64-unknown-elf-gcc)
Spike RISC-V ISA Simulator
RISC-V Proxy Kernel (pk)
Icarus Verilog for HDL simulation
GTKWave for waveform viewing
The setup includes comprehensive troubleshooting documentation for compatibility issues between different toolchain versions and solutions for cross-compiler interference problems.

#🔧 Prerequisites
sudo dnf install -y epel-release
sudo dnf update -y
sudo dnf install -y texinfo gperf
dnf search texinfo
dnf search gperf
sudo dnf config-manager --set-enabled powertools
sudo dnf config-manager --set-enabled codeready-builder-for-rhel-8-x86_64-rpms
sudo dnf update -y
sudo dnf install -y texinfo gperf
