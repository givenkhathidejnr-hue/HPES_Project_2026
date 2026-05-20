# Dual-Mode Secure-Boot StarCore (SBSC) Processor

An extension of the baseline 16-bit **StarCore-1 architecture** developed for EEE4120F (High-Performance Embedded Systems). The Dual-Mode Secure-Boot StarCore (SBSC) integrates real-time, hardware-enforced bitwise XOR-based instruction decryption directly into the instruction fetch path. This enables the runtime environment to execute encrypted binaries securely, ensuring software confidentiality and protecting the embedded system against unauthorized software propagation and malicious code tampering.

The decryption process and secure mode selection was integrated in the InstructionMemory.v,. The MMIO operations were integrated and handled by DataMemory.v. 

## 🚀 Key Architectural Enhancements
* **Dual-Mode Secure Execution:** Controlled via a memory-mapped I/O (MMIO) register configuration. When `secure_mode = 0`, legacy unencrypted programs execute directly. When `secure_mode = 1`, an inline combinational hardware array unmasks ciphertext programs on-the-fly.
* **Zero-Cycle Decryption Latency:** Decryption happens concurrently within the instruction fetch stage prior to opcode parsing, maintaining a high-performance throughput of **1 Instruction Per Cycle (IPC)**.
* **Strict Fault Isolation:** Any code execution mismatch (e.g., executing ciphertext in legacy mode or plaintext in secure mode) corrupts the instruction stream predictably, triggering automated testbench assertion blocks and rendering the system safely inoperable without leaking register state.

---

## 📊 Experimental Test Matrix
The processor architecture was validated using a 2x2 verification framework crossing the processor state against the target machine binary type:

| Configuration | Secure Mode Register (`secure_mode`) | Software Binary Type | Expected System Behavior | Testbench Status |
| :--- | :---: | :--- | :--- | :---: |
| **Scenario 1: Normal** | `0` | Plaintext | Direct routing of unencrypted instructions; normal program flow. | **PASS** |
| **Scenario 2: Tamper** | `0` | Encrypted | Ciphertext interpreted as raw opcodes; causes illegal operations. | **FAIL (Safe)** |
| **Scenario 3: Mismatch**| `1` | Plaintext | Plaintext incorrectly XOR-scrambled; creates invalid instructions. | **FAIL (Safe)** |
| **Scenario 4: Secure** | `1` | Encrypted | Real-time hardware decryption path active; flawless execution. | **PASS** |

---

## 📁 Repository Layout
```text
├── src/
│   ├── ALU.v           
│   ├── ALU_Control.v
│   ├── GPR.v           
│   ├── InstructionMemory.v                
│   ├── ControlUnit.v
│   ├── Parameter.v            
│   ├── Datapath.v
│   ├── DataMemory.v           
│   └── StarCore1.v    
├── tb/
│   ├── ALU_tb.v           
│   ├── ALU_Control_tb.v
│   ├── GPR_tb.v           
│   ├── InstructionMemory_tb.v                
│   ├── ControlUnit_tb.v          
│   ├── DataMemory_tb.v           
│   └── StarCore1_tb.v    
└── README.md                 # This repository profile manual
