# Ledger
# Project Structure
The project contains the following components:
libmodbus-2.9.3/: Modbus protocol stack used as the target program for demonstrating control-flow hijacking attacks.

leger_generate.py:The ledger generator script.
It analyzes the target binary, encrypts its control-flow metadata using the PRESENT cipher,
and generates the ledger file and parameter file used by the defense.

Ledger_cfi.py: The main defense script implementing the Ledger-based Control-Flow Integrity (CFI) mechanism.
It verifies return-path authenticity at runtime by comparing instruction tags against pre-generated ledger values.

baseline.py: The baseline script with no defense mechanisms, used to run the target program without protection for comparison.

automated_run.py: Automation script for launching experiments either with or without the Ledger defense.

exp.py: Exploit script used to perform a control-flow hijacking attack against the target server.

README.md: This file, containing the project documentation and instructions.

# Requirements
Operating System: Ubuntu 20.04 (tested)

GDB: GNU Debugger, to run the target program and inject defense logic

Python 3: to run the automation and attack scripts

32-bit GCC toolchain: to build the target program for 32-bit architecture

# Environment Setup
On a fresh Ubuntu 20.04 system, run the following commands in order to set up the environment:

1. Clone the repository: git clone https://github.com/hailin-creat/Ledger.git

cd Ledger

sudo apt update

sudo apt upgrade

--- Install build tools---：

sudo apt install autoconf automake libtool

---Install 32-bit build toolchain and debug libraries---：

sudo apt install gcc-multilib g++-multilib libc6-dev-i386 make libc6-dbg

--- Install 32-bit libc debug symbols---：

sudo apt install libc6-dbg:i386

--- Enable i386 architecture---：

sudo dpkg --add-architecture i386


