# 🖥️ Datapath & Control Unit — Study Guide

> 🟡 **HIGH PRIORITY** — Understanding how instructions flow through the datapath is fundamental to everything else.

---

## 1. Core Concepts

- The **datapath** contains the hardware components that perform operations on data: ALU, register file, PC, multiplexers, memory interfaces.
- The **control unit** generates control signals that configure the datapath for each instruction type.
- In a **single-cycle datapath**, every instruction completes in one (long) clock cycle.
- In a **multi-cycle datapath**, instructions take multiple shorter cycles and can reuse hardware.
- **Pipelined datapath** is the most common: overlaps instruction execution.
- The **ALU control** is a sub-unit that selects the ALU operation based on the opcode and function code.
- Control signals determine: what the ALU does, where data comes from (registers or memory), and what gets written where.

---

## 2. Key Datapath Components

| Component | Function |
|-----------|---------|
| **Program Counter (PC)** | Holds address of next instruction |
| **Instruction Memory** | Stores program instructions (read-only during execution) |
| **Register File** | 32 registers; supports 2 reads + 1 write simultaneously |
| **ALU** | Performs arithmetic (add, sub), logical (AND, OR), and comparison operations |
| **Data Memory** | Read/write for LW/SW instructions |
| **Sign Extend** | Extends 16-bit immediate to 32-bit for I-type instructions |
| **Multiplexers (MUX)** | Select between multiple data sources based on control signals |
| **Adder (PC+4)** | Computes address of next sequential instruction |
| **Branch Adder** | Computes branch target address |
| **Shift Left 2** | Multiplies branch offset by 4 (byte addressing) |

---

## 3. Key Control Signals

| Signal | Purpose | R-type | LW | SW | BEQ |
|--------|---------|--------|----|----|-----|
| **RegDst** | Write destination: rd (1) or rt (0) | 1 | 0 | X | X |
| **ALUSrc** | ALU input: register (0) or immediate (1) | 0 | 1 | 1 | 0 |
| **MemToReg** | Write reg from: ALU (0) or memory (1) | 0 | 1 | X | X |
| **RegWrite** | Enable register write | 1 | 1 | 0 | 0 |
| **MemRead** | Enable memory read | 0 | 1 | 0 | 0 |
| **MemWrite** | Enable memory write | 0 | 0 | 1 | 0 |
| **Branch** | Enable branch (if zero) | 0 | 0 | 0 | 1 |
| **ALUOp** | Tell ALU control what operation | 10 | 00 | 00 | 01 |

---

## 4. Visual Diagrams

### Single-Cycle Datapath (Simplified)
```
                          ┌──────────────┐
                    ┌────►│ Instr Memory │────┐
                    │     └──────────────┘    │
PC ─────────────────┤                         │ [25:21]=rs
                    │                         │ [20:16]=rt
                    │                         │ [15:11]=rd
                    │     ┌─────────────┐     │ [15:0]=imm
          ┌─────────┘     │ Register    │     │
          │ PC+4          │    File     │◄────┤
          │               │  (32 regs) │     │
          ▼               └──────┬──────┘     │
     ┌─────────┐                 │ Data1      │ Sign
     │ Branch  │                 │ Data2      │ Extend
     │ target  │                 ▼            ▼
     │ adder   │           ┌─────────┐   ┌──────────┐
     └─────────┘           │   ALU   │◄──┤   MUX    │◄─ ALUSrc
          │                │         │   └──────────┘
          └──────────┐     └────┬────┘
                     │          │ Zero ──► Branch decision
               Branch│          │ Result
               MUX   │          ▼
                     │     ┌──────────┐
                     │     │   Data   │
                     │     │  Memory  │
                     │     └────┬─────┘
                     │          │
                     ▼          ▼
               Next PC    ┌──────────┐
                          │   MUX    │◄── MemToReg
                          └──────────┘
                               │
                               ▼
                         Write Back to
                         Register File
```

### R-Type Instruction Dataflow (ADD rd, rs, rt)
```
1. PC → Instruction Memory → fetch instruction
2. Decode: rs, rt → Register File reads → Data1, Data2
3. Control: RegDst=1, ALUSrc=0, RegWrite=1, MemRead=0, MemWrite=0
4. ALU: Add(Data1, Data2) → Result
5. MUX: MemToReg=0 → select ALU result
6. Register File write: rd ← Result
7. PC ← PC + 4
```

### LW Instruction Dataflow (LW rt, imm(rs))
```
1. PC → Instruction Memory → fetch instruction
2. Decode: rs → Register File reads → Data1; imm → Sign Extend
3. Control: RegDst=0, ALUSrc=1, MemRead=1, MemToReg=1, RegWrite=1
4. ALU: Add(Data1, Sign_Extend(imm)) → Memory Address
5. Data Memory: Read[Address] → Data
6. Register File write: rt ← Data
7. PC ← PC + 4
```

---

## 5. RISC vs CISC Datapath Design

| Aspect | RISC | CISC |
|--------|------|------|
| Instruction size | Fixed | Variable |
| Number of instruction formats | Few (3 in MIPS) | Many |
| Memory access | Load/Store only | Direct in instructions |
| Datapath design | Simple, regular | Complex |
| Microcode | Rarely used | Common (for complex instrs) |
| Pipelining ease | Easy | Difficult |
| Examples | MIPS, ARM, RISC-V | x86 |

---

## 6. Worked Examples

### Example 1: Tracing a BEQ Instruction
**Instruction:** `BEQ $s0, $s1, LABEL` (branch if $s0 == $s1)

**Datapath trace:**
1. Fetch instruction from PC
2. Decode: rs=$s0, rt=$s1, imm=branch offset
3. Control: Branch=1, ALUSrc=0, ALUOp=01 (subtract), RegWrite=0
4. ALU: Subtract($s0, $s1)
   - If result = 0: **Zero flag = 1** → branch taken
   - If result ≠ 0: **Zero flag = 0** → branch not taken
5. Branch target: PC+4 + (sign_extend(imm) × 4)
6. MUX selects: PC+4 if not taken, branch target if taken

**Key:** BEQ uses subtraction in the ALU to check equality (zero result = equal).

### Example 2: ALU Control Logic
**ALU control inputs:** ALUOp (2-bit from main control) + funct field (6-bit from R-type instruction)

| ALUOp | Instruction Type | ALU Operation |
|-------|-----------------|---------------|
| 00 | LW/SW (add for address) | Add |
| 01 | BEQ (subtract to compare) | Subtract |
| 10 | R-type (use funct field) | Determined by funct |

For R-type (ALUOp=10), funct field determines:
| funct | Operation |
|-------|-----------|
| 100000 | ADD |
| 100010 | SUB |
| 100100 | AND |
| 100101 | OR |
| 101010 | SLT |

---

## 7. Common Exam Questions

### "Trace through the datapath for instruction X"
For each instruction type:
1. State what's read from instruction memory (which fields)
2. State which registers are read
3. State the control signals and their values
4. Trace the data through the ALU
5. State what's written and where

### "What control signals are set for a SW instruction?"
- RegWrite = 0 (not writing a register)
- MemWrite = 1 (writing to memory)
- MemRead = 0 (not reading memory)
- ALUSrc = 1 (use immediate as offset)
- RegDst = X (don't care, not writing register)

### "Why is a MUX needed before the ALU input?"
- R-type instructions: ALU second input = register (rt)
- I-type instructions: ALU second input = immediate value (sign-extended)
- ALUSrc control signal selects which source → only one MUX needed

---

## 8. Tricky Points

### Don't confuse instruction memory and data memory
- **Instruction memory:** Read by IF stage; contains program code
- **Data memory:** Read/written by MEM stage; contains program data (arrays, variables)
In a real system these can share the same physical memory (unified cache), but the datapath separates them logically.

### Sign extension is always for I-type immediates
The 16-bit immediate is sign-extended to 32 bits before entering the ALU. This preserves the signed value of negative offsets (e.g., -4 stays -4, not 65532).

### RegDst selects rd vs rt
- R-type: **rd** is the destination register (bits [15:11])
- I-type (LW, ADDI): **rt** is the destination register (bits [20:16])
RegDst = 1 selects rd (R-type), RegDst = 0 selects rt (I-type)

---

## 9. Quick Self-Test

**Q1:** For an ADD instruction, what are the values of: RegWrite, MemRead, MemWrite, RegDst?

**Q2:** What is the role of the sign-extend unit in the datapath?

**Q3:** How does the datapath determine whether to take a branch (BEQ)?

**Q4:** In a single-cycle datapath, which instruction determines the clock period? Why?

**Q5:** What is the difference between datapath and control unit?

---

### Answers

**A1:** RegWrite=1 (write result to rd), MemRead=0, MemWrite=0, RegDst=1 (destination is rd, bits [15:11])

**A2:** It extends the 16-bit immediate field of I-type instructions to 32 bits by replicating the sign bit. This allows both positive and negative immediate values to work correctly in 32-bit arithmetic.

**A3:** The ALU subtracts the two register values. If the result is zero (Zero output = 1) AND the Branch control signal is 1, then the AND gate enables the branch → PC is set to the branch target address.

**A4:** The **load word (LW)** instruction — it requires all 5 stages in sequence: instruction fetch + decode + ALU (address calc) + memory read + register write. The clock period must be long enough for the slowest (longest) instruction's critical path.

**A5:** 
- **Datapath:** Hardware components that manipulate data (ALU, register file, memory, MUXes)
- **Control unit:** Generates the control signals that configure the datapath for each instruction (tells MUXes which input to select, enables/disables reads and writes)
