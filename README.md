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
 ```bash
sudo dnf groupinstall -y "Development Tools"
sudo dnf install -y epel-release
```
<img width="1366" height="768" alt="Installing Initial Dependencies" src="https://github.com/user-attachments/assets/81bdd6e1-8a05-4fc8-9c95-9129af151536" />
<img width="1366" height="768" alt="Step 1" src="https://github.com/user-attachments/assets/6c23e8a2-0ae2-4642-9c30-ed6aeca85b57" />

```
sudo dnf install -y git vim autoconf automake make gcc gcc-c++ \
mpfr-devel gmp-devel libmpc-devel gawk bison flex texinfo gperf \
libtool patchutils bc zlib-devel expat-devel wget curl device-tree-compiler
```
<img width="1366" height="768" alt="Step 1 done" src="https://github.com/user-attachments/assets/7fac146f-2b77-444a-b052-d563d17b108a" />

Upon running the above code, we will get some error message like the following.
<img width="1366" height="86" alt="DTC Error" src="https://github.com/user-attachments/assets/9db33a13-de76-4182-96f5-7d68700fee02" />

So gave the command
```bash
sudo dnf install -y dtc
```
On running the above command dtc (device tree compiler got installed).
<img width="1366" height="768" alt="DTC Installed" src="https://github.com/user-attachments/assets/2a81064a-3276-4d40-b4b6-607c553cbf94" />

After this, I ran the following command to install the gtkwave
```bash
sudo dnf install -y gtkwave
```
<img width="1366" height="768" alt="GTK Wave Installed" src="https://github.com/user-attachments/assets/40fd8fed-9513-435f-b194-aaf1796d0aeb" />

## 1. Workspace Setup
Then we need to create a workspace setpu where we will download and install all our files and tools. We begin by storing our home path which make installing, cleaning and removing files easy.
```bash
cd
pwd=$PWD
mkdir -p riscv_toolchain
cd riscv_toolchain
```
<img width="877" height="99" alt="Create Workspace Directory and Track Path" src="https://github.com/user-attachments/assets/da3f63a7-af51-4ab0-a511-b9256530ba05" />

### 2. Install RISC-V GCC Toolchain
- Now we Download the prebuilt toolchain and extract it and verify that it is downloaded and extraced correctly. Also add it to our path and verify the path.
# Download the file (complete URL)
```bash
-wget "https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz"
```
## Extract the downloaded file
```bash
-tar -xvzf riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz
```
<img width="1366" height="768" alt="Extracting the Downloaded Packages" src="https://github.com/user-attachments/assets/9f37997a-b1bd-45b6-b2ff-08652729a0d1" />

### 3. Adding the Toolchain to my Path & Creating Permanent Environmrnt Setup
# Creating RISC-V Environment Setup
```bash
-#!/bin/bash
-# Activate RISC-V toolchain paths for the current shell session

-export PATH=/root/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:/root/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin:$PATH
-echo "RISC-V toolchain environment activated."
```
Now we will make it executable using following command:
```bash
-chmod +x ~/set_riscv_env.sh
```
# Creating Native Environment Restore Script
```bash
-#!/bin/bash
-# Restore native system path and environment
-export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/bin
-echo "Native Linux environment restored."
-hash -r  # Clear shell command cache
```
Now to make the above commands execuatble:
```bash
-chmod +x ~/set_native_env.sh
```
Now reloading the bashrc file
```bash
-source ~/.bashrc
```
## Activating the RISC-V Environment
```bash
-source ~/set_riscv_env.sh
```

### 4. Build and Install Spike (RISC-V Simulator)

```bash
-cd $pwd/riscv_toolchain
-git clone https://github.com/riscv/riscv-isa-sim.git
-cd riscv-isa-sim
-mkdir build && cd build
-../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14
-make -j$(nproc)
-sudo make install
```
### 5. Build and Install Proxy Kernel (pk)

```bash
-cd $pwd/riscv_toolchain
-git clone https://github.com/riscv/riscv-pk.git
-cd riscv-pk
-mkdir build && cd build
-../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf
-make -j$(nproc)
-sudo make install
```
Upon running the above code I got an error. Attaching the screenshot below.

Hence I tried setting up the proper environment required & also verified the paths where the toolchain and everything is installed.

```bash
-source ~/set_riscv_env.sh
-which gcc
-which riscv64-unknown-elf-gcc
```

After running the above commands, I cleaned and started rebuilding after setting up the environment.

```bash
-cd /root/riscv_toolchain/riscv-pk/build
-make clean
-../configure --prefix=/root/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf
-make -j$(nproc)
-sudo make install
```
Hence proxy kernel got installed using the above command.

### 6. Installation Check
```bash
-which riscv64-unknown-elf-gcc
-riscv64-unknown-elf-gcc -v
-which gcc   
-which spike                  
-which pk                  
- spike --help
```

### 7. Creating a Unique Test Program
Creating the Unique C Test Program file unique_test.c 
```
-vi unique_test.c
```
```
#include <stdint.h>
#include <stdio.h>

#ifndef MANAV_SHETH
#define MANAV_SHETH "MANAV_SHETH"
#endif
#ifndef PDEU
#define PDEU "PDEU"
#endif

// 64-bit FNV-1a hash function
static uint64_t fnv1a64(const char *s) {
	const uint64_t FNV_OFFSET = 1469598103934665603ULL;
	const uint64_t FNV_PRIME  = 1099511628211ULL;
	uint64_t h = FNV_OFFSET;
	for (const unsigned char *p = (const unsigned char*)s; *p; ++p) {
    	h ^= (uint64_t)(*p);
    	h *= FNV_PRIME;
	}
	return h;
}

int main(void) {
	const char *user = MANAV_SHETH;
	const char *host = PDEU;

	char buf[256];
	int n = snprintf(buf, sizeof(buf), "%s@%s", user, host);
	if (n <= 0) return 1;

	uint64_t uid = fnv1a64(buf);

	printf("RISC-V Uniqueness Check\n");
	printf("User: %s\n", user);
	printf("Host: %s\n", host);
	printf("UniqueID: 0x%016llx\n", (unsigned long long)uid);
#ifdef __VERSION__
    unsigned long long vlen = (unsigned long long)sizeof(__VERSION__) - 1;
    printf("GCC_VLEN: %llu\n", vlen);
#endif

    return 0;
}
```





  

