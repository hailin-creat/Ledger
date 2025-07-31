# Ledger_ROP
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

Clone the repository: git clone https://github.com/hailin-creat/Ledger_ROP.git

cd Ledger_ROP

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

# Building the Target Program
In the project directory, navigate to libmodbus-2.9.3/ and build the server:(Some error messages in the compilation will not affect the experiment.)

cd libmodbus-2.9.3

./autogen.sh

./configure   --host=i686-linux-gnu   --disable-dependency-tracking   CFLAGS="-m32 -fno-stack-protector -no-pie -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=0 -g -O0"   LDFLAGS="-m32 -no-pie -z execstack -z norelro"

make

make install

# Generating the Ledger Files
Before running the defense, generate the ledger and parameter files from the target binary:

python3 leger_generate.py ./libmodbus-2.9.3/tests/.libs/unit-test-server

This will produce:

Ledger.txt – Encrypted ledger entries used for runtime verification

parameter.txt – Encryption key and counter seed used by Ledger_cfi.py

These files are required for the defense to work and must match the exact binary you are testing.

# Running the Experiment
1. Enable or disable Ledger defense
Edit :automated_run.py,
ENABLE_LEDGER = True   # Enable Ledger CFI Defense
ENABLE_LEDGER = False  # Run without defense (baseline)

2. Run the target under GDB
From the project root directory:

python3 automated_run.py

This will:
Start GDB with or without Ledger defense
Launch the Modbus server (unit-test-server)
Wait for the attack payload (exp.py) to be executed
If the attack is successful (control flow is hijacked), the experiment executes the shellcode

3. Launch the attack

To successfully reproduce the attack (and execute the shellcode), you need to set the correct return address in exp.py that points to the shellcode in memory.

In a separate terminal window, while the server is running under GDB (launched by automated_run.py), execute the attack script: 

python3 exp.py

With Ledger defense enabled:
The attack should be detected; program terminates immediately when control-flow mismatch is found.

Without defense (baseline mode):
If the payload is correct, the attack may execute the injected shellcode.

-----------------------------------------Notes--------------------------------------------------------------

If you do not set the correct return address in exp.py to point to the shellcode, the attack can still cause a segmentation fault (segfault) on the server side when the return address is overwritten with an invalid value.

A segmentation fault also indicates that the attack successfully hijacked the control flow — the return address was tampered with — but the shellcode was not executed

# Debugging to Find the Return Address
If you want to reproduce the attack and set the correct return address in exp.py, you can use GDB to precisely locate the return address on the stack and compute the memory address of the injected shellcode.

1.Start GDB and set a breakpoint in modbus_reply

2.When the breakpoint hits, examine the stack frame and locate the saved return address for modbus_reply.

3.Next, identify where your shellcode is placed relative to the return address.
For example, if in exp.py you see that the shellcode is appended after a certain number of padding bytes after the return address, you can calculate its position in memory from the stack snapshot in GDB.

4.Once you determine the memory address where your shellcode resides, update the ret variable in exp.py to point to this address: ret = p32(0xYOUR_SHELLCODE_ADDRESS)

5.Run the attack again: python3 exp.py

# What Happens
Without Defense:
Return address overwritten → Shellcode executed → /bin/sh spawned.

With Ledger Defense:
Ledger verification fails → Program immediately terminates → No malicious code runs.

# License
This project is licensed under the MIT License - see the LICENSE file for details.





