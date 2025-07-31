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
