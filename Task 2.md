# 🛠️ RISC-V Task 2 - Local Setup Verification

## 📋 Overview

This repository contains the implementation and verification of 4 RISC-V C programs compiled with the local RISC-V toolchain and executed using Spike simulator with proxy kernel (pk). Each program includes a uniqueness mechanism that embeds username, hostname, machine ID, and timestamps to ensure outputs are unique to this PC.

## 🎯 Objectives

- Verify local RISC-V toolchain installation and functionality
- Demonstrate cross-compilation capabilities for RISC-V architecture
- Validate Spike simulator integration with proxy kernel
- Generate unique, machine-specific program outputs for verification

## 🔍 Step-by-Step Implementation
# A.Uniqueness Mechanism Setup
First, set identity variables in the Rocky Linux host shell:
```
export U=$(id -un)
export H=$(hostname -s)
export M=$(cat /etc/machine-id | head -c 16)
export T=$(date -u +%Y-%m-%dT%H:%M:%SZ)
export E=$(date +%s)
```
<img width="594" height="174" alt="Step 1" src="https://github.com/user-attachments/assets/9b6c058b-a614-486d-8d5d-a120a7654724" />

## B. Common Header (unique.h)
```
#ifndef UNIQUE_H
#define UNIQUE_H
#include <stdio.h>
#include <stdint.h>
#include <time.h>
#ifndef MANAV_SHETH
#define MANAV_SHETH "MANAV_SHETH"
#endif
#ifndef PDEU
#define PDEU "PDEU"
#endif
#ifndef MACHINE_ID
#define MACHINE_ID "unknown_machine"
#endif
#ifndef BUILD_UTC
#define BUILD_UTC "unknown_time"
#endif
#ifndef BUILD_EPOCH
#define BUILD_EPOCH 0
#endif
static uint64_t fnv1a64(const char *s) {
const uint64_t OFF = 1469598103934665603ULL, PRIME = 1099511628211ULL;
uint64_t h = OFF;
for (const unsigned char *p=(const unsigned char*)s; *p; ++p) {
h ^= *p; h *= PRIME;
}
return h;
}
static void uniq_print_header(const char *program_name) {
time_t now = time(NULL);
char buf[512];
int n = snprintf(buf, sizeof(buf), "%s|%s|%s|%s|%ld|%s|%s",
MANAV_SHETH, PDEU, MACHINE_ID, BUILD_UTC,
(long)BUILD_EPOCH, __VERSION__, program_name);
(void)n;
uint64_t proof = fnv1a64(buf);
char rbuf[600];
snprintf(rbuf, sizeof(rbuf), "%s|run_epoch=%ld", buf, (long)now);
uint64_t runid = fnv1a64(rbuf);
printf("=== RISC-V Proof Header ===\n");
printf("User : %s\n", MANAV_SHETH);
printf("Host : %s\n", PDEU);
printf("MachineID : %s\n", MACHINE_ID);
printf("BuildUTC : %s\n", BUILD_UTC);
printf("BuildEpoch : %ld\n", (long)BUILD_EPOCH);
printf("GCC : %s\n", __VERSION__);
printf("PointerBits: %d\n", (int)(8*(int)sizeof(void*)));
printf("Program : %s\n", program_name);
printf("ProofID : 0x%016llx\n", (unsigned long long)proof);
printf("RunID : 0x%016llx\n", (unsigned long long)runid);
printf("===========================\n");
}
#endif
```
<img width="1366" height="768" alt="Unique_h " src="https://github.com/user-attachments/assets/ed50b55a-df2e-440a-be35-c350974467eb" />

### C. C. Program Implementation
Now we create files in vi editor
<img width="823" height="176" alt="Files Created" src="https://github.com/user-attachments/assets/5ae7677c-370d-4d04-affa-dc0700de3696" />

Each program must include unique.h and print the header first.
# 1. factorial.c
```
#include "unique.h"
static unsigned long long fact(unsigned n){ return (n<2)?1ULL:n*fact(n-1); }
int main(void){
uniq_print_header("factorial");
unsigned n = 12;
printf("n=%u, n!=%llu\n", n, fact(n));
return 0;
}
```
<img width="762" height="218" alt="factorial_h" src="https://github.com/user-attachments/assets/3f903034-1671-4e91-9fe1-654135e43577" />

## 2. max_array.c
```
#include "unique.h"
int main(void){
uniq_print_header("max_array");
int a[] = {42,-7,19,88,3,88,5,-100,37};
int n = sizeof(a)/sizeof(a[0]), max=a[0];
for(int i=1;i<n;i++) if(a[i]>max) max=a[i];
printf("Array length=%d, Max=%d\n", n, max);
return 0;
}
```
<img width="775" height="226" alt="max_array" src="https://github.com/user-attachments/assets/448de042-e908-4a73-81b5-f1a125732124" />


## 3. bitops.c
```
#include "unique.h"
int main(void){
uniq_print_header("bitops");
unsigned x=0xA5A5A5A5u, y=0x0F0F1234u;
printf("x&y=0x%08X\n", x&y);
printf("x|y=0x%08X\n", x|y);
printf("x^y=0x%08X\n", x^y);
printf("x<<3=0x%08X\n", x<<3);
printf("y>>2=0x%08X\n", y>>2);
return 0;
}
```
<img width="778" height="261" alt="bitops" src="https://github.com/user-attachments/assets/75092ac3-bd49-419e-bdcb-690cea13bd69" />

## 4. bubble_sort.c
```
#include "unique.h"
void bubble(int *a,int n){ for(int i=0;i<n-1;i++) for(int j=0;j<n-1-i;j++) if(a[j]>a[j
+1]){int t=a[j];a[j]=a[j+1];a[j+1]=t;} }
int main(void){
uniq_print_header("bubble_sort");
int a[]={9,4,1,7,3,8,2,6,5}, n=sizeof(a)/sizeof(a[0]);
bubble(a,n);
printf("Sorted:"); for(int i=0;i<n;i++) printf(" %d",a[i]); puts("");
return 0;
}
```
<img width="798" height="252" alt="bubble_sort" src="https://github.com/user-attachments/assets/370a92b4-6845-4717-8ff8-33d68cb03f37" />

### D. Build, Run, and Capture Evidence
# Program 1: Factorial
# Compile
```
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    factorial.c -o factorial
```
# Run
```
spike pk ./factorial
```
<img width="611" height="317" alt="Spike Factorial" src="https://github.com/user-attachments/assets/df2bd0b0-beba-4585-ba51-066e336c74f4" />


# Program 2: max_array
# Compile
```
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    max_array.c -o max_array
```
# Run
```
spike pk ./max_array
```
<img width="1366" height="768" alt="Spike max_array" src="https://github.com/user-attachments/assets/96439b72-857a-4eea-959e-83c620e87168" />

# Program 3: bitops
# Compile
```
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    bitops.c -o bitops
```
# Run
```
spike pk ./bitops
```
<img width="510" height="434" alt="Spike Bitops" src="https://github.com/user-attachments/assets/11038d4d-7503-43dc-b667-d157ecb7c9f6" />

# Program 4: bubble_sort
# Compile
```
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    bubble_sort.c -o bubble_sort
```
# Run
```
spike pk ./bubble_sort
```
<img width="816" height="332" alt="Spike Bubble_Sort" src="https://github.com/user-attachments/assets/5b0aec63-a666-4bf4-9aff-aa6f2b1eca7a" />

### E. Produce Assembly and Disassembly

## factorial
# Generate Assembly:
```
riscv64-unknown-elf-gcc -O0 -S -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    factorial.c -o factorial.s
```

# Generate Disassembly:
```
riscv64-unknown-elf-objdump -d ./factorial | sed -n '/<main>:/,/^$/p' > factorial_main_objdump.txt
```
<img width="1366" height="768" alt="Diassemble_1" src="https://github.com/user-attachments/assets/42ff8d79-d5b1-445f-8863-f5cbd1320b7b" />

## max_array
# Generate Assembly:
```
riscv64-unknown-elf-gcc -O0 -S -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    max_array.c -o max_array.s
```

# Generate Disassembly:
```
riscv64-unknown-elf-objdump -d ./max_array | sed -n '/<main>:/,/^$/p' > max_array_main_objdump.txt
```
<img width="1366" height="768" alt="Disassemble_max_array" src="https://github.com/user-attachments/assets/99f19da9-3954-4f32-baf9-81f2e41d71bd" />

## bitops
# Generate Assembly:
```
riscv64-unknown-elf-gcc -O0 -S -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    bitops.c -o bitops.s
```

# Generate Disassembly:
```
riscv64-unknown-elf-objdump -d ./bitops | sed -n '/<main>:/,/^$/p' > bitops_main_objdump.txt
```
<img width="1366" height="330" alt="Spike   Disassemble Bitops" src="https://github.com/user-attachments/assets/e948053f-d344-42f0-9398-e2bb7e5a39bd" />

## bubble_sort
# Generate Assembly:
```
riscv64-unknown-elf-gcc -O0 -S -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    bubble_sort.c -o bubble_sort.s
```

# Generate Disassembly:
```
riscv64-unknown-elf-objdump -d ./bubble_sort | sed -n '/<main>:/,/^$/p' > bubble_sort_main_objdump.txt
```
<img width="1366" height="405" alt="Spike   Disassemble Bubble_Sort" src="https://github.com/user-attachments/assets/a68e9d15-2e19-46e8-90db-57deedddfbdd" />

# F. RISC-V Integer Instruction Decoding

## Instructions from Provided Objdump Files

| Instruction | Opcode | rd | rs1 | rs2/imm | funct3 | funct7 | Binary | Description |
|-------------|--------|----|----|---------|--------|--------|--------|-------------|
| `addi sp,sp,-32` | 0010011 | x2 | x2 | -32 | 000 | - | `111111100000 00010 000 00010 0010011` | sp = sp + (-32) |
| `lw a5,-20(s0)` | 0000011 | x15 | x8 | -20 | 010 | - | `111111101100 01000 010 01111 0000011` | a5 = mem[s0 + (-20)] |
| `and a5,a5,a4` | 0110011 | x15 | x15 | x14 | 111 | 0000000 | `0000000 01110 01111 111 01111 0110011` | a5 = a5 & a4 |
| `slli a5,a5,0x2` | 0010011 | x15 | x15 | 2 | 001 | - | `000000000010 01111 001 01111 0010011` | a5 = a5 << 2 |
| `blt a4,a5,10374` | 1100011 | - | x14 | x15 | 100 | - | `1111111 01111 01110 100 01100 1100011` | if (a4 < a5) pc = 10374 |

## Detailed Breakdown

### 1. ADDI (I-type) - Stack Pointer Adjustment
```
From: bitops_main_objdump.txt, line 10328
Instruction: addi sp,sp,-32 (1101 compressed form)
Full form:   addi x2,x2,-32
Binary:      111111100000 00010 000 00010 0010011
             immediate    rs1   f3  rd    opcode
```
- **Type**: I-type (Immediate arithmetic)
- **Operation**: Add immediate -32 to stack pointer
- **Immediate**: -32 (decimal) = 111111100000 (12-bit two's complement)
- **Registers**: rd=x2(sp), rs1=x2(sp)

### 2. LW (I-type) - Load Word
```
From: bitops_main_objdump.txt, line 1039a
Instruction: lw a5,-20(s0)
Full form:   lw x15,-20(x8)
Binary:      111111101100 01000 010 01111 0000011
             immediate    rs1   f3  rd    opcode
```
- **Type**: I-type (Load)
- **Operation**: Load word from memory at address s0-20
- **Immediate**: -20 (decimal) = 111111101100 (12-bit two's complement)
- **Registers**: rd=x15(a5), rs1=x8(s0)

### 3. AND (R-type) - Bitwise AND
```
From: bitops_main_objdump.txt, line 1035a
Instruction: and a5,a5,a4
Full form:   and x15,x15,x14
Binary:      0000000 01110 01111 111 01111 0110011
             funct7  rs2   rs1   f3  rd    opcode
```
- **Type**: R-type (Register-Register)
- **Operation**: Bitwise AND of a5 and a4
- **Registers**: rd=x15(a5), rs1=x15(a5), rs2=x14(a4)
- **funct3**: 111 (AND operation)

### 4. SLLI (I-type) - Shift Left Logical Immediate
```
From: max_array_main_objdump.txt, line 10378
Instruction: slli a5,a5,0x2
Full form:   slli x15,x15,2
Binary:      000000000010 01111 001 01111 0010011
             shamt|funct6 rs1   f3  rd    opcode
```
- **Type**: I-type (Shift immediate)
- **Operation**: Shift a5 left by 2 positions
- **Shift amount**: 2 (decimal) = 000010 (6-bit)
- **Registers**: rd=x15(a5), rs1=x15(a5)

### 5. BLT (B-type) - Branch Less Than
```
From: max_array_main_objdump.txt, line 103b8
Instruction: blt a4,a5,10374
Full form:   blt x14,x15,10374
Binary:      1111111 01111 01110 100 01100 1100011
             imm[12|10:5] rs2 rs1 f3 imm[4:1|11] opcode
```
- **Type**: B-type (Branch)
- **Operation**: Branch if a4 < a5 (signed comparison)
- **Registers**: rs1=x14(a4), rs2=x15(a5)
- **Target**: PC-relative offset to address 10374
- **funct3**: 100 (BLT operation)

## Register Encoding Reference
| Register | ABI Name | Binary | Decimal |
|----------|----------|--------|---------|
| x2  | sp  | 00010 | 2  |
| x8  | s0  | 01000 | 8  |
| x14 | a4  | 01110 | 14 |
| x15 | a5  | 01111 | 15 |

## Instruction Type Summary
- **R-type (0110011)**: Register-register operations (AND)
- **I-type (0010011)**: Immediate arithmetic/shift operations (ADDI, SLLI)
- **I-type (0000011)**: Load operations (LW)
- **B-type (1100011)**: Branch operations (BLT)
