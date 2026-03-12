# 🔣 Assembly Language — Study Guide

> 🟡 **HIGH PRIORITY** — Assembly tracing, reading/writing code, and understanding instruction formats are tested.

---

## 1. Core Concepts

- Assembly language is a human-readable representation of machine code — each assembly instruction maps directly to one machine instruction.
- **Registers** are the fastest storage in the CPU. Assembly operates primarily on registers.
- **Load/Store architecture (RISC):** Only `LW`/`SW` access memory; arithmetic uses registers only.
- Instructions operate on: registers, immediate values (constants), or memory addresses.
- **MIPS** is the classic architecture used in courses. Key characteristics:
  - 32 registers, each 32 bits wide
  - Fixed 32-bit instruction size
  - Three instruction formats: R-type, I-type, J-type
- **Calling convention:** registers have designated purposes (argument passing, return values, saved registers).

---

## 2. MIPS Register Conventions

| Register | Name | Purpose |
|----------|------|---------|
| $0 | $zero | Always 0 (hardwired) |
| $2-$3 | $v0-$v1 | Return values from functions |
| $4-$7 | $a0-$a3 | Function arguments |
| $8-$15 | $t0-$t7 | Temporary (caller-saved) |
| $16-$23 | $s0-$s7 | Saved registers (callee-saved) |
| $28 | $gp | Global pointer |
| $29 | $sp | Stack pointer |
| $30 | $fp | Frame pointer |
| $31 | $ra | Return address |

---

## 3. Key Instruction Types & Formats

### R-Type (Register operations)
```
Format: op rd, rs, rt
Encoding: | opcode | rs | rt | rd | shamt | funct |
           6 bits   5b  5b   5b    5b      6b
```

| Instruction | Meaning |
|-------------|---------|
| `ADD rd, rs, rt` | rd = rs + rt |
| `SUB rd, rs, rt` | rd = rs - rt |
| `AND rd, rs, rt` | rd = rs & rt |
| `OR rd, rs, rt` | rd = rs \| rt |
| `SLT rd, rs, rt` | rd = (rs < rt) ? 1 : 0 |
| `SLL rd, rt, shamt` | rd = rt << shamt |
| `SRL rd, rt, shamt` | rd = rt >> shamt |
| `JR rs` | PC = rs (jump register) |

### I-Type (Immediate operations)
```
Format: op rt, rs, immediate
Encoding: | opcode | rs | rt | immediate (16 bits) |
```

| Instruction | Meaning |
|-------------|---------|
| `ADDI rt, rs, imm` | rt = rs + imm (sign-extended) |
| `LW rt, imm(rs)` | rt = Mem[rs + imm] |
| `SW rt, imm(rs)` | Mem[rs + imm] = rt |
| `BEQ rs, rt, label` | if (rs == rt) PC = PC+4+4×imm |
| `BNE rs, rt, label` | if (rs != rt) PC = PC+4+4×imm |
| `LUI rt, imm` | rt = imm << 16 |

### J-Type (Jump operations)
```
Format: op target
Encoding: | opcode | target address (26 bits) |
```

| Instruction | Meaning |
|-------------|---------|
| `J target` | PC = (PC[31:28] \| target << 2) |
| `JAL target` | $ra = PC+4; PC = target (function call) |

---

## 4. Visual Diagrams

### MIPS Instruction Formats
```
R-Type:
 31      26 25   21 20   16 15   11 10    6 5      0
 ┌────────┬──────┬──────┬──────┬──────┬──────┐
 │ 000000 │  rs  │  rt  │  rd  │shamt │funct │
 └────────┴──────┴──────┴──────┴──────┴──────┘

I-Type:
 31      26 25   21 20   16 15                    0
 ┌────────┬──────┬──────┬──────────────────────────┐
 │ opcode │  rs  │  rt  │     immediate (16 bits)   │
 └────────┴──────┴──────┴──────────────────────────┘

J-Type:
 31      26 25                                    0
 ┌────────┬──────────────────────────────────────┐
 │ opcode │           target (26 bits)            │
 └────────┴──────────────────────────────────────┘
```

### Stack Frame (Function Call)
```
  High addresses
  ┌──────────────┐
  │  Caller's    │
  │  stack frame │
  ├──────────────┐ ← Old $sp
  │  Return addr │  ($ra saved here)
  │  Saved $fp   │
  │  Saved regs  │  ($s0-$s7 if used)
  │  Local vars  │
  ├──────────────┤ ← New $sp (after ADDIU $sp, $sp, -N)
  Low addresses
```

---

## 5. Worked Examples

### Example 1: Reading Assembly Code
```assembly
# What does this code compute?
ADDI $t0, $zero, 0    # t0 = 0
ADDI $t1, $zero, 10   # t1 = 10
loop:
  ADD  $t0, $t0, $a0  # t0 = t0 + a0
  ADDI $t1, $t1, -1   # t1 = t1 - 1
  BNE  $t1, $zero, loop  # if t1 != 0, goto loop
# Result: t0 = 10 * a0 (multiply a0 by 10 using addition)
```

### Example 2: Writing Assembly Code
**C code:** `if (a > b) { c = a - b; } else { c = b - a; }`
(Assume: a=$s0, b=$s1, c=$s2)

```assembly
  SLT  $t0, $s1, $s0    # t0 = (b < a) → i.e., a > b
  BEQ  $t0, $zero, else # if NOT (a > b), goto else
  SUB  $s2, $s0, $s1    # c = a - b
  J    done
else:
  SUB  $s2, $s1, $s0    # c = b - a
done:
```

### Example 3: Array Access
**C code:** `x = A[i]` (A is an int array, base address in $s0, i in $s1)

```assembly
  SLL  $t0, $s1, 2     # t0 = i * 4 (byte offset for int array)
  ADD  $t0, $t0, $s0   # t0 = base + offset (effective address)
  LW   $t1, 0($t0)     # t1 = A[i]
```

### Example 4: Function Call and Return
```assembly
# Caller:
  ADDI $a0, $zero, 5   # argument = 5
  JAL  myFunc          # call myFunc, save return addr in $ra
  # ... $v0 now holds return value

# Callee (myFunc):
  ADDI $sp, $sp, -4    # make room on stack
  SW   $ra, 0($sp)     # save return address
  # ... function body ...
  ADD  $v0, $a0, $zero # return value = argument
  LW   $ra, 0($sp)     # restore return address
  ADDI $sp, $sp, 4     # restore stack pointer
  JR   $ra             # return to caller
```

---

## 6. Common Exam Questions

### "Translate this C code to MIPS assembly"
**Strategy:**
1. Map variables to registers ($s0, $s1, etc.)
2. Translate expressions to R-type or I-type instructions
3. Translate if/else to BEQ/BNE with labels
4. Translate loops to branch-based loops
5. Handle array access: multiply index by element size, add base

### "Trace through this assembly and give the final register values"
**Strategy:**
1. Start with given initial values
2. Execute each instruction, updating register values
3. Handle branches: evaluate condition, follow if true
4. Track all register changes

### "Encode this instruction in binary/hex"
1. Identify instruction type (R/I/J)
2. Look up opcode/funct fields
3. Encode each field into the correct bit positions
4. Combine into 32-bit binary, convert to hex if needed

---

## 7. Tricky Points

### Sign extension of immediates
I-type instructions sign-extend the 16-bit immediate to 32 bits. So `ADDI $t0, $zero, -1` stores 0xFFFFFFFF (all ones), not just 0x0000FFFF.

### Byte offset for arrays
- `char` array: offset = index × 1 (no shift needed)
- `short` array: offset = index × 2 (SLL by 1)
- `int` array: offset = index × 4 (SLL by 2)
- `double` array: offset = index × 8 (SLL by 3)

### Branch target calculation
`BEQ` target = (PC + 4) + (sign_extend(imm) × 4)
The immediate field counts words (instructions), not bytes.

### `$zero` is read-only
Writing to `$zero` has no effect. It always reads as 0.

### Caller-saved vs callee-saved registers
- **Caller-saved ($t0-$t9):** Caller must save if needed after function call
- **Callee-saved ($s0-$s7):** Callee must restore before returning

---

## 8. Quick Self-Test

**Q1:** What is the byte offset to access the 5th element (index 4) of an integer array?

**Q2:** Translate to MIPS: `if (x == 0) y = 1;` (x=$s0, y=$s1)

**Q3:** What instruction saves the return address when calling a function?

**Q4:** In MIPS R-type format, what are the 6 fields? (left to right)

**Q5:** What does `SLT $t0, $s0, $s1` do?

---

### Answers

**A1:** 4 × 4 = **16 bytes** (index 4, 4 bytes per int)

**A2:**
```assembly
BNE $s0, $zero, done   # if x != 0, skip
ADDI $s1, $zero, 1     # y = 1
done:
```

**A3:** `JAL` (Jump and Link) — saves PC+4 into $ra.

**A4:** opcode (6b), rs (5b), rt (5b), rd (5b), shamt (5b), funct (6b)

**A5:** If $s0 < $s1, set $t0 = 1; otherwise $t0 = 0. ("Set Less Than")
