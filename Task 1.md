# 🛠️ Task 1: RISC-V Toolchain Setup and Verification Using WSL

[![RISC-V](https://img.shields.io/badge/Architecture-RISC--V-blue.svg)](https://riscv.org/)
[![Toolchain](https://img.shields.io/badge/Toolchain-RISC--V%20GCC-blueviolet.svg)]()
[![Rocky Linux](https://img.shields.io/badge/Platform-Rocky%20Linux-339966.svg)](https://rockylinux.org/)
[![Status](https://img.shields.io/badge/Status-✅%20Complete-success.svg)]()


A comprehensive guide for setting up a complete RISC-V development toolchain with detailed troubleshooting and solutions for common issues.
## 📋 Table of Contents

- [Overview](https://github.com/manav0223/vsdRiscvSoc/blob/main/README.md#-overview)
- [Prerequisites](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-prerequisites)
- [Installation](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-installation)
- [Installation Issues Faced](https://github.com/manav0223/vsdRiscvSoc/edit/main/README.md#-installation-issues-faced)
- [Key Learning Points](https://github.com/manav0223/vsdRiscvSoc/edit/main/README.md#-key-learning-points)
- [Quick Troubleshooting Reference](https://github.com/manav0223/vsdRiscvSoc/edit/main/README.md#-quick-troubleshooting-reference)

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
wget "https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz"
```
## Extract the downloaded file
```bash
tar -xvzf riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz
```
<img width="1366" height="768" alt="Extracting the Downloaded Packages" src="https://github.com/user-attachments/assets/9f37997a-b1bd-45b6-b2ff-08652729a0d1" />

### 3. Adding the Toolchain to my Path & Creating Permanent Environmrnt Setup
# Creating RISC-V Environment Setup
```bash
#!/bin/bash
# Activate RISC-V toolchain paths for the current shell session

export PATH=/root/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:/root/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin:$PATH
echo "RISC-V toolchain environment activated."
```
Now we will make it executable using following command:
```bash
chmod +x ~/set_riscv_env.sh
```
# Creating Native Environment Restore Script
```bash
#!/bin/bash
# Restore native system path and environment
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/bin
echo "Native Linux environment restored."
hash -r  # Clear shell command cache
```
Now to make the above commands execuatble:
```bash
chmod +x ~/set_native_env.sh
```
Now reloading the bashrc file
```bash
source ~/.bashrc
```
<img width="1366" height="768" alt="Set the correct PATH after redownloading the tool chain" src="https://github.com/user-attachments/assets/754ef4fe-c3e9-4ef6-a5c5-3af423ed1265" />
<img width="1366" height="768" alt="Make the PATH permanent" src="https://github.com/user-attachments/assets/9cb1d4b6-a0d6-4c31-8d32-788e4a9b3543" />

## Activating the RISC-V Environment
```bash
source ~/set_riscv_env.sh
```
<img width="1366" height="350" alt="Starting of Spike" src="https://github.com/user-attachments/assets/3731d42a-3b9c-41ea-ae8e-e5036fca27ff" />

### 4. Build and Install Spike (RISC-V Simulator)

```bash
cd $pwd/riscv_toolchain
git clone https://github.com/riscv/riscv-isa-sim.git
cd riscv-isa-sim
mkdir build && cd build
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14
make -j$(nproc)
sudo make install
```
<img width="1366" height="768" alt="Spike new commands running_" src="https://github.com/user-attachments/assets/45a52e36-ec6b-4e4f-abdb-3375a15642f6" />
<img width="1366" height="768" alt="Make Install" src="https://github.com/user-attachments/assets/7748b462-735e-4f2e-adb9-3437ecaaf8cf" />
<img width="1366" height="768" alt="Spike Confirmation" src="https://github.com/user-attachments/assets/a6c86ee1-df4b-435e-9459-494809891280" />

### 5. Build and Install Proxy Kernel (pk)

```bash
cd $pwd/riscv_toolchain
git clone https://github.com/riscv/riscv-pk.git
cd riscv-pk
mkdir build && cd build
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf
make -j$(nproc)
sudo make install
```
Upon running the above code I got an error. Attaching the screenshot below.
<img width="1366" height="768" alt="Error_gcc while installing Proxy kernel" src="https://github.com/user-attachments/assets/eb78d059-526d-43c0-9060-5f05ed48db00" />

Hence I tried setting up the proper environment required & also verified the paths where the toolchain and everything is installed.

```bash
source ~/set_riscv_env.sh
which gcc
which riscv64-unknown-elf-gcc
```

After running the above commands, I cleaned and started rebuilding after setting up the environment.

```bash
cd /root/riscv_toolchain/riscv-pk/build
make clean
../configure --prefix=/root/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf
make -j$(nproc)
sudo make install
```
Hence proxy kernel got installed using the above command.
<img width="1366" height="768" alt="Proxy Kernel" src="https://github.com/user-attachments/assets/17897521-7591-4d86-b2a0-88d0c84c9342" />


### 6. Installation Check
```bash
which riscv64-unknown-elf-gcc
riscv64-unknown-elf-gcc -v
which gcc   
which spike                  
which pk                  
 spike --help
```
<img width="1366" height="461" alt="Quick Sanity Check" src="https://github.com/user-attachments/assets/8b302ab1-21b6-4b2e-b6c1-935b702f0897" />

### 7. Creating a Unique Test Program
Creating the Unique C Test Program file unique_test.c 
```
vi unique_test.c
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
### 8. Compiling the Unique Test Program for RISC-V
After making the file, we will compile the Unique Test Program for RISC-V
```
riscv64-unknown-elf-gcc -O2 -Wall -march=rv64imac -mabi=lp64 \
  -DUSERNAME="$(id -un)" -DHOSTNAME="$(hostname -s)" \
  unique_test.c -o unique_test
```
### 9. Running the Test Program on Spike Simulator & Generation of Unique ID
Now Run the Test Program on Spike Simulator & a unique id will be generated.
```
spike pk ./unique_test
```
<img width="1366" height="204" alt="Unique ID confirmation" src="https://github.com/user-attachments/assets/402254e3-ee38-499c-91d6-40b47f86cf34" />

## 🔧 Installation Issues faced
### 1. **Cross-Compiler PATH Interference**
- **Problem**: RISC-V cross-compiler tools interfere with native x86-64 compilation
- **Impact**: Native builds fail because wrong assembler is used for host architecture
- **Solution**: Temporary PATH isolation during native tool compilation

### 2. **Shell Command Expansion Issues**
- **Problem**: Shell command substitution creates unquoted tokens for C preprocessor
- **Impact**: Preprocessor sees separate identifiers instead of single string constants  
- **Solution**: Use explicit string literals to bypass problematic shell expansion

### 3. **Installation Path Configuration**
- **Problem**: Tools install to non-standard locations not included in default PATH
- **Impact**: Successfully installed tools appear missing and unusable
- **Solution**: Permanent PATH updates to include all installation directories

## 🎯 Key Learning Points

### **🔍 Debugging Methodology**
- **Multi-stage investigation**: Complex toolchain issues require systematic debugging across multiple layers and components
- **Root cause analysis**: Surface-level errors often mask deeper compatibility or configuration problems that need isolation
- **Component isolation testing**: Test individual tools and dependencies separately to identify conflicting or interfering sources

### **⚙️ Toolchain Management**
- **Version synchronization**: Ensure compiler, binutils, libraries, and target architecture support align across the entire toolchain
- **Environment isolation**: Separate cross-compilation and native build environments to prevent PATH pollution and tool conflicts
- **Dependency verification**: Validate that all required libraries, headers, and runtime components are present and compatible

### **📋 Configuration Best Practices**
- **Persistent environment setup**: Configure PATH, environment variables, and aliases in shell profiles (`.bashrc`, `.zshrc`) for consistency
- **Incremental validation**: Test and verify each installation and configuration step before proceeding to prevent cascading failures
- **Configuration versioning**: Document and backup working configurations, environment setups, and successful build procedures for reproducibility

## ⚡ Quick Troubleshooting Reference

### **💥 Build Breaks? Start Here:**
```bash
# Verify active tools
which gcc && which as && which ld

# Check library links
ldd your_binary 2>/dev/null || echo "Static binary or missing libs"

# Get detailed error context
make VERBOSE=1 2>&1 | tee build.log
```

### **🔧 PATH Hell? Debug Fast:**
```bash
# See what's in your PATH
echo $PATH | sed 's/:/\n/g' | nl

# Test with clean environment
env -i PATH="/usr/bin:/bin" your_command

# Verify tool locations
ls -la $(which gcc) && readlink -f $(which gcc)
```

### **🎯 Version Conflicts? Quick Fixes:**
- **Check compatibility**: `gcc --version` + `as --version` + `ld --version`
- **Find alternatives**: `apt list --installed | grep gcc` or `ls /usr/bin/*gcc*`
- **Container escape**: `docker run --rm -v $PWD:/work -w /work gcc:9 make`
- **Version lock**: Pin specific versions in `requirements.txt` or `Dockerfile`
