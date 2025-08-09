# 🛠️ Task 1: RISC-V Toolchain Setup and Verification Using WSL

[![RISC-V](https://img.shields.io/badge/Architecture-RISC--V-blue.svg)](https://riscv.org/)
[![Toolchain](https://img.shields.io/badge/Toolchain-RISC--V%20GCC-blueviolet.svg)]()
[![Rocky Linux](https://img.shields.io/badge/Platform-Rocky%20Linux-339966.svg)](https://rockylinux.org/)
[![Status](https://img.shields.io/badge/Status-✅%20Complete-success.svg)]()


A comprehensive guide for setting up a complete RISC-V development toolchain with detailed troubleshooting and solutions for common issues.
## 📋 Table of Contents

- [Overview](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-overview)
- [Prerequisites](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-prerequisites)
- [Installation](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-installation)
- [Issues Encountered & Solutions](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-issues-encountered--solutions)
- [Project Structure](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-project-structure)
- [Contributing](#contributing)
- [License](#license)
-
- ## 🎯 Overview

This project documents the complete setup process for a RISC-V development toolchain, including:

- **RISC-V GCC Cross-Compiler** (riscv64-unknown-elf-gcc)
- **Spike RISC-V ISA Simulator** 
- **RISC-V Proxy Kernel (pk)**
- **GTKWave** for waveform viewing
- The setup includes comprehensive troubleshooting documentation for compatibility issues between different toolchain versions and solutions for cross-compiler interference problems.
## 🔧 Prerequisites

### System Requirements
- **OS**: Rocky Linux 8.0 (Native or RISC-V Environment)
- **RAM**: 4-8 GB recommended
- **Storage**: 30 GB free space
- **CPU**: 2+ cores recommended

- ## 🚀 Installation
- Once we have completed the installation and verification of Rocky Linux 8.0, Open the terminal and run the base dependencies.
- ```bash
sudo dnf groupinstall -y "Development Tools"
sudo dnf install -y epel-release
sudo dnf install -y git vim autoconf automake make gcc gcc-c++ \
mpfr-devel gmp-devel libmpc-devel gawk bison flex texinfo gperf \
libtool patchutils bc zlib-devel expat-devel wget curl device-tree-compiler
```bash
Upon running the above code, we will get some error message like the following.

So gave the command
    
  
  

